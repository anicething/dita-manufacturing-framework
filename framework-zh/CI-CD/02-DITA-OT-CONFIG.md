# DITA-OT 配置指南

**文档编号：** CIC-002  
**版本：** 1.0  
**最后更新：** 2026-06-07  
**状态：** 已批准  

---

## 概述

本文档涵盖 DITA-OT 的安装选项、插件管理和构建参数配置，适用于制造企业文档化环境。

---

## 1. 安装

### 1.1 手动安装

```bash
# 下载 DITA-OT 4.x
wget https://github.com/dita-ot/dita-ot/releases/download/4.0/dita-ot-4.0.zip
unzip dita-ot-4.0.zip -d /opt/
export PATH=/opt/dita-ot-4.0/bin:$PATH

# 验证安装
dita --version
```

### 1.2 Docker 安装

```bash
# 拉取 DITA-OT Docker 镜像
docker pull ditaot/dita-ot:4.0

# 通过 Docker 运行构建
docker run --rm \
  -v $(pwd)/src:/workspace/src \
  -v $(pwd)/output:/workspace/output \
  ditaot/dita-ot:4.0 \
  dita --input=/workspace/src/product/maps/master.ditamap \
       --format=html5 \
       --output=/workspace/output/html5
```

建议在 CI/CD 环境中使用 Docker 以确保构建可重现。

---

## 2. 插件管理

### 2.1 安装插件

```bash
# 列出已安装插件
dita --plugins

# 从目录安装插件
dita --install /path/to/plugin

# 从 DITA-OT 注册表安装
dita --install com.elovirta.pdf
```

### 2.2 推荐插件

| 插件 | 用途 | 安装方式 |
|------|------|---------|
| `org.dita.pdf2` | 标准 PDF 输出 | DITA-OT 已内置 |
| `com.elovirta.pdf` | 基于 CSS 的 PDF（Chemistry） | `dita --install com.elovirta.pdf` |
| `org.dita.html5` | HTML5 输出 | DITA-OT 已内置 |
| `org.dita.epub` | EPUB 输出 | `dita --install org.dita.epub` |

### 2.3 自定义插件结构

```
framework/plugins/
└── com.example.manufacturing/
    ├── plugin.xml              # 插件描述文件
    ├── integrator.xml          # 集成钩子
    ├── build.xml               # Ant 构建目标
    ├── cfg/
    │   ├── fo/
    │   │   ├── attrs/
    │   │   │   └── custom.xsl  # FO 属性覆盖
    │   │   └── xsl/
    │   │       └── custom.xsl  # 自定义 XSL 转换
    │   └── html/
    │       └── css/
    │           └── custom.css  # HTML5 自定义 CSS
    └── i18n/
        ├── messages_en.xml     # 英文消息
        └── messages_zh.xml     # 中文消息
```

---

## 3. 构建参数

### 3.1 常用参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--input` | 输入 DITA Map 路径 | `src/product/maps/master.ditamap` |
| `--format` | 输出格式 | `html5`、`pdf`、`epub` |
| `--output` | 输出目录 | `output/html5` |
| `--property` | DITA-OT 属性 | `--property=args.css=style.css` |
| `--validate` | 启用验证 | `--validate` |

### 3.2 制造文档的关键属性

```bash
# CSS 定制
--property=args.css=framework/styles/shared/base.css

# 目录深度
--property=toc.section.depth=4

# 草稿模式（显示批注）
--property=args.draft=false

# PDF 格式化器选择
--property=pdf.formatter=chemistry

# 图片分辨率
--property=args.image.resolution=300

# 复制 CSS 到输出目录
--property=args.copy.css=true
```

---

## 4. 故障排除

| 问题 | 原因 | 解决 |
|------|------|------|
| `DOTA001F` | 缺少 DTD/XSD | 确认 catalog.xml 在类路径中 |
| PDF 生成失败 | 缺少 PDF 插件 | 安装 `com.elovirta.pdf` |
| HTML5 缺少 CSS | args.css 路径错误 | 使用绝对路径或正确的相对路径 |
| 构建超时 | 内存耗尽 | 增加 JVM 堆：`-Djava.awt.headless=true -Xmx2g` |
| 中文字符乱码 | 缺少字体 | 在 PDF 插件配置中添加中文字体 |

---

## 相关文档

| 文档 | 位置 |
|------|------|
| CI/CD 流水线 | [01-CICD-PIPELINE.md](01-CICD-PIPELINE.md) |
| 发布 SLA | [03-PUBLISHING-SLA.md](03-PUBLISHING-SLA.md) |
| 跨格式一致性 | [PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md](../PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md) |

---

**文档编号：** CIC-002  
**下次审核日期：** 2026 年第三季度
