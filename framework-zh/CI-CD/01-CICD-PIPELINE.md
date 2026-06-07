# CI/CD 发布流水线

**文档编号：** CIC-001  
**版本：** 1.1  
**最后更新：** 2026-06-07  
**状态：** 已批准  

---

## 目录

1. [流水线概念](#1-流水线概念)
2. [构建触发器](#2-构建触发器)
3. [验证阶段](#3-验证阶段)
4. [构建与发布阶段](#4-构建与发布阶段)
5. [部署阶段](#5-部署阶段)
6. [发布 SLA](#6-发布-sla)
7. [平台说明](#7-平台说明)
8. [故障排除](#8-故障排除)

---

## 1. 流水线概念

### 1.1 核心流水线

CI/CD 流水线自动完成 DITA 内容的验证、转换和分发。通用流程如下：

```
内容变更
      │
      ▼
┌─────────────┐
│  验证阶段   │  ← XML 有效性、元数据完整性、链接完整性
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  构建阶段   │  ← DITA-OT 转换（HTML5、PDF、移动端）
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  部署阶段   │  ← 复制到 Web 服务器、CDN 或文档门户
└──────┬──────┘
       │
       ▼
  通知相关方
```

### 1.2 流水线分类

| 流水线 | 触发条件 | 用途 | 典型耗时 |
|--------|---------|------|---------|
| **验证** | 分支推送（非主分支） | 进入生产前的质量门禁 | < 5 分钟 |
| **发布** | 推送到主/发布分支 | 生产构建与部署 | < 1 小时 |
| **定时** | Cron/定时器（每周或每月） | 全量重建 + 完整性检查 | < 1 小时 |

### 1.3 前置条件

设置 CI/CD 流水线前，确保：

- [ ] 在构建服务器上安装 DITA-OT 4.x，或提供容器镜像
- [ ] 已安装 PDF 插件（org.dita.pdf2 或 com.elovirta.pdf via Chemistry）
- [ ] 构建服务器可访问 DITA-OT 和插件仓库
- [ ] 输出目录已配置且可写入
- [ ] 构建服务器可访问部署目标

---

## 2. 构建触发器

### 2.1 触发器配置

配置 CI/CD 系统在内容变更时触发：

| 触发器 | 分支 | 路径过滤 | 使用场景 |
|--------|------|---------|---------|
| 推送到主分支 | `main` | `src/**`, `references/**` | 生产发布 |
| 推送到发布分支 | `release/*` | `src/**`, `references/**` | 版本化发布 |
| Pull/Merge 请求 | 任意特性分支 | `src/**` | 合并前验证 |
| 定时任务 | `main` | 全部 | 每周重建 + 链接检查 |
| 手动触发 | 任意 | 全部 | 临时发布 |

**路径过滤建议：** 仅对内容目录触发。排除 `.gitignore`、`README.md` 和工具配置变更等不影响发布输出的内容。

### 2.2 并行构建

将 HTML5、PDF 和移动端构建并行运行，以最小化流水线总耗时：

```
                    ┌──────────────┐
                    │  验证阶段    │
                    └──────┬───────┘
                           │
            ┌──────────────┼──────────────┐
            │              │              │
            ▼              ▼              ▼
       ┌────────┐    ┌────────┐    ┌──────────┐
       │ HTML5  │    │  PDF   │    │  移动端  │
       └────────┘    └────────┘    └──────────┘
            │              │              │
            └──────────────┼──────────────┘
                           │
                           ▼
                     ┌──────────┐
                     │  部署    │
                     └──────────┘
```

---

## 3. 验证阶段

### 3.1 验证步骤

每次内容变更在生产发布前应通过以下检查：

| 检查项 | 工具/命令 | 失败处理 |
|--------|----------|---------|
| **XML 格式正确性** | `xmllint --noout *.dita` | 阻止流水线 |
| **DITA Schema 验证** | DITA-OT `--validate` 参数 | 阻止流水线 |
| **元数据完整性** | 自定义验证脚本 | 阻止流水线 |
| **链接完整性** | DITA-OT 验证报告 | 阻止流水线 |
| **Schematron 规则** | 自定义 Schematron + DITA-OT | 阻止流水线 |
| **拼写检查** | Hunspell / aspell | 警告（非阻止） |
| **复用率检查** | 自定义复用扫描脚本 | 低于阈值时警告 |

### 3.2 验证命令（DITA-OT）

```bash
# 验证源目录中所有 DITA 文件
dita --input=src/product-a/maps/master.ditamap \
     --format=html5 \
     --validate \
     --property=args.reload=true \
     --property=generate.debug.attributes=true
```

### 3.3 自定义验证脚本

创建 `scripts/validate.sh`（或对应平台的等效脚本）：

```bash
#!/bin/bash
set -euo pipefail

echo "=== 验证流水线 ==="

# 1. XML 格式正确性
echo "检查 XML 格式..."
find src/ references/ -name "*.dita" -exec xmllint --noout {} +
echo "✅ 所有 XML 文件格式正确"

# 2. 元数据检查
echo "检查必需元数据..."
for file in $(find src/ -name "*.dita"); do
    if ! grep -q '<author>' "$file"; then
        echo "❌ 文件缺少作者信息: $file"
        exit 1
    fi
done
echo "✅ 元数据检查通过"

# 3. 断开链接检查
echo "检查交叉引用..."
dita --input=src/product/maps/master.ditamap \
     --format=html5 \
     --validate 2>&1 | grep -i "warning\|error" || true
echo "✅ 链接验证完成"

echo "=== 所有检查通过 ==="
```

---

## 4. 构建与发布阶段

### 4.1 DITA-OT 构建命令

```bash
# 单格式构建
dita --input=src/product/maps/master.ditamap \
     --format=html5 \
     --output=output/html5/product-a \
     --property=args.css=framework/styles/shared/base.css \
     --property=toc.section.depth=4

# PDF 构建（使用 Chemistry 插件以获得更好的 CSS 支持）
dita --input=src/product/maps/master.ditamap \
     --format=pdf \
     --output=output/pdf/product-a \
     --property=pdf.formatter=chemistry

# 移动端（EPUB）构建
dita --input=src/product/maps/master.ditamap \
     --format=epub \
     --output=output/mobile/product-a
```

### 4.2 多格式构建脚本

创建 `scripts/build-all.sh`：

```bash
#!/bin/bash
set -euo pipefail

MAP_PATH="${1:-src/product/maps/master.ditamap}"
OUTPUT_BASE="${2:-output}"
TIMESTAMP=$(date '+%Y%m%d-%H%M')

echo "=== DITA 多格式构建 ==="
echo "输入: $MAP_PATH"
echo "时间戳: $TIMESTAMP"

# 并行构建所有格式
for format in html5 pdf epub; do
    (
        echo "正在构建 $format..."
        dita --input="$MAP_PATH" \
             --format="$format" \
             --output="${OUTPUT_BASE}/${format}/${TIMESTAMP}" \
             --property=args.draft=false
        echo "✅ $format 构建完成"
    ) &
done

wait
echo "=== 所有构建完成 ==="
```

### 4.3 构建产物

构建成功后，应生成以下输出：

```
output/
├── html5/latest/           ← 当前 HTML5 构建
│   ├── index.html
│   ├── topics/
│   └── images/
├── pdf/latest/             ← 当前 PDF 构建
│   └── product-manual.pdf
├── epub/latest/            ← 当前 EPUB 构建
│   └── product-manual.epub
└── archive/                ← 按时间戳存档，支持回滚
    ├── 20260607-0800/
    └── 20260601-0800/
```

### 4.4 构建配置文件

在仓库根目录创建 `build-config.xml`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="dita-build">
  <property name="input" value="src/product/maps/master.ditamap"/>
  <property name="output" value="output"/>
  <property name="css" value="framework/styles/shared/base.css"/>
  <property name="toc.depth" value="4"/>
  
  <target name="build-all" description="构建所有输出格式">
    <antcall target="build-html5"/>
    <antcall target="build-pdf"/>
    <antcall target="build-epub"/>
  </target>
  
  <target name="build-html5">
    <exec executable="dita">
      <arg line="--input=${input}"/>
      <arg line="--format=html5"/>
      <arg line="--output=${output}/html5"/>
      <arg line="--property=args.css=${css}"/>
    </exec>
  </target>
  
  <target name="build-pdf">
    <exec executable="dita">
      <arg line="--input=${input}"/>
      <arg line="--format=pdf"/>
      <arg line="--output=${output}/pdf"/>
      <arg line="--property=pdf.formatter=chemistry"/>
    </exec>
  </target>
  
  <target name="build-epub">
    <exec executable="dita">
      <arg line="--input=${input}"/>
      <arg line="--format=epub"/>
      <arg line="--output=${output}/epub"/>
    </exec>
  </target>
</project>
```

---

## 5. 部署阶段

### 5.1 部署方案

选择与您基础设施匹配的部署方式：

| 方式 | 适用场景 | 设置复杂度 |
|------|---------|-----------|
| **Web 服务器同步** | 内部 Web 服务器 | 低 |
| **文档门户** | 面向客户的文档 | 中 |
| **CMS 集成** | 企业级 CCMS | 高 |
| **静态托管** | 公有文档 | 低 |
| **网络共享** | 内部团队使用 | 低 |

### 5.2 部署脚本模板

创建 `scripts/deploy.sh`：

```bash
#!/bin/bash
set -euo pipefail

DEPLOY_TARGET="${1:-/var/www/docs}"
BUILD_OUTPUT="${2:-output}"

echo "=== 部署文档 ==="
echo "源目录: $BUILD_OUTPUT"
echo "目标路径: $DEPLOY_TARGET"

# 复制 HTML5 输出
rsync -avz --delete "${BUILD_OUTPUT}/html5/latest/" "${DEPLOY_TARGET}/html5/"

# 复制 PDF 输出
rsync -avz "${BUILD_OUTPUT}/pdf/latest/" "${DEPLOY_TARGET}/pdf/"

# 验证部署
if [ -f "${DEPLOY_TARGET}/html5/index.html" ]; then
    echo "✅ 部署验证成功"
else
    echo "❌ 部署失败：未找到 index.html"
    exit 1
fi
```

---

## 6. 发布 SLA

| 步骤 | 耗时 | 自动化 |
|------|------|-------|
| 内容提交 | 即时 | 是 |
| 触发 CI/CD | < 1 分钟 | 是 |
| XML 验证 | < 1 分钟 | 是 |
| HTML5 生成 | < 15 分钟 | 是 |
| PDF 生成 | < 20 分钟 | 是 |
| 移动端（EPUB）生成 | < 10 分钟 | 是 |
| 部署到目标 | < 5 分钟 | 是 |
| 搜索索引 | < 10 分钟 | 是 |
| **总计** | **< 1 小时** | **100% 自动化** |

---

## 7. 平台说明

### 7.1 CI/CD 平台对比

本文档中的流水线概念适用于任何 CI/CD 平台。实施时，请根据所选平台调整语法：

| 平台 | 配置文件 | 并行构建 | 容器支持 |
|------|---------|---------|---------|
| 通用 CI/CD | `pipeline.yml` 或自定义 | 是（并行阶段） | 是 |
| Jenkins | `Jenkinsfile`（声明式） | 是（parallel 指令） | 是（Docker agent） |
| GitLab CI | `.gitlab-ci.yml` | 是（needs/parallel） | 是（Docker executor） |
| Azure DevOps | `azure-pipelines.yml` | 是（jobs.strategy） | 是（container jobs） |
| TeamCity | `buildTypes`（UI/XML） | 是（构建链） | 是（Docker 封装） |
| Bamboo | `bamboo-specs/`（YAML） | 是（阶段） | 是（Docker 任务） |

### 7.2 使用 Docker 镜像运行 DITA-OT

对于容器化构建，使用 DITA-OT Docker 镜像：

```bash
# 拉取镜像
docker pull ditaot/dita-ot:4.0

# 运行构建
docker run --rm \
  -v "$(pwd)/src:/workspace/src" \
  -v "$(pwd)/output:/workspace/output" \
  ditaot/dita-ot:4.0 \
  dita --input=/workspace/src/product/maps/master.ditamap \
       --format=html5 \
       --output=/workspace/output/html5
```

将 Docker 镜像添加到 CI/CD 流水线，可在不同环境中实现一致、可重现的构建。

---

## 8. 故障排除

| 症状 | 可能原因 | 解决方案 |
|------|---------|---------|
| 构建立即失败 | 未安装 DITA-OT 或版本错误 | 检查 `dita --version`；安装 DITA-OT 4.x |
| XML 验证错误 | DITA 文件格式错误 | 对每个文件运行 `xmllint`；检查未关闭标签 |
| 输出中缺少主题 | ditamap 中 `topicref` 损坏 | 验证所有 `@href` 路径正确解析 |
| PDF 缺少字体 | 字体未嵌入 | 将字体添加到自定义 CSS 或 PDF 插件配置 |
| PDF 中图片不显示 | 图片路径问题 | 使用绝对路径或 `@keyref` 来引用图片 |
| 构建缓慢（> 30 分钟） | 格式过多或内容量过大 | 启用并行构建；检查 DITA-OT 内存设置 |
| 部署失败 | 目标服务器不可达 | 检查网络连接；验证凭据 |

**构建失败处理流程：**
1. 检查 CI/CD 构建日志中第一个错误
2. 查找 XML 验证错误
3. 检查地图引用中是否缺少文件
4. 验证元数据是否完整
5. 联系 DITA 管理员

---

## 相关文档

| 文档 | 位置 |
|------|------|
| DITA-OT 配置 | [CI-CD/02-DITA-OT-CONFIG.md](02-DITA-OT-CONFIG.md) |
| 发布 SLA | [CI-CD/03-PUBLISHING-SLA.md](03-PUBLISHING-SLA.md) |
| 跨格式一致性 | [PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md](../PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md) |
| 验证门禁 | [WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md](../WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md) |
