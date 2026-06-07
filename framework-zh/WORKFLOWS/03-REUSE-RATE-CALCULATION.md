# 内容复用率计算与优化

**文档 ID：** WF-003
**版本：** 1.0
**最后更新：** 2026-06-07
**状态：** 已批准

---

## 目录

1. [为什么衡量复用率](#1-为什么衡量复用率)
2. [复用率定义与公式](#2-复用率定义与公式)
3. [按类型衡量复用](#3-按类型衡量复用)
4. [自动化复用率计算脚本](#4-自动化复用率计算脚本)
5. [CI/CD 复用门禁集成](#5-cicd-复用门禁集成)
6. [复用机会识别](#6-复用机会识别)
7. [复用优化工作流](#7-复用优化工作流)
8. [报告与仪表盘](#8-报告与仪表盘)
9. [目标与基准](#9-目标与基准)

---

## 1. 为什么衡量复用率

DITA 的核心价值主张依赖于内容复用。没有量化评估，团队将无法：

- 向管理层证明 DITA 投资合理性
- 识别表现不佳的内容领域
- 追踪随时间的改进趋势
- 为作者设定有意义的复用目标
- 检测内容重复时发现退化

**行业基准（Munne 2024，ACM SIGDOC 2024）：**

| 成熟度阶段 | 复用率 | 典型时间线 |
|-----------|--------|-----------|
| 初始采用 | 5-15% | 第 1-6 个月 |
| 建立规范 | 15-30% | 第 7-18 个月 |
| 优化阶段 | 30-40% | 第 19-36 个月 |
| 行业领先 | 40%+ | 36 个月以上 |

**本框架目标：第 12 个月达到 35-40%**

---

## 2. 复用率定义与公式

### 2.1 核心公式

```
复用率 = (复用主题数 + 引用实例数) / (产出主题总数) × 100%
```

| 术语 | 定义 |
|------|------|
| **复用主题数** | 被 2+ 个地图引用或通过 `conref`/`conkeyref` 包含的主题 |
| **引用实例数** | 复用主题在所有地图中的每次出现 |
| **产出主题总数** | 仓库中的唯一主题（每个只计一次） |

### 2.2 加权复用率（推荐）

简单主题计数偏向小型参考主题。加权公式考虑实际被复用的内容量：

```
加权复用率 = Σ(复用主题字数) / Σ(所有主题字数) × 100%
```

这能更准确地反映作者实际避免了多少重写工作。

### 2.3 细分复用类型

| 复用机制 | 衡量方法 | 权值 |
|---------|---------|------|
| **Conref**（`<conref>`） | 统计主题正文中的 `conref` 属性数 | 1.0（行内） |
| **Conkeyref**（`<conkeyref>`） | 统计 `conkeyref` 属性数 | 1.0（行内） |
| **Keyref**（`<keyword keyref>`） | 统计 `keyref` 属性使用次数 | 0.3（单值） |
| **主题级地图复用** | 出现在 2+ 个 ditamap 中的主题 | 等于主题字数 |
| **Ditamap 复用** | 被 2+ 个 bookmap 引用的 ditamap | 每次包含计 1.0 |

### 2.4 实际示例

仓库快照：

```
产品 A 手册：     150 个主题（其中 50 个来自共享库）
产品 B 手册：     120 个主题（其中 40 个来自共享库）
共享库：           80 个主题（均为共享）
```
```
唯一主题总数：        150 + 120 + 80 - 40(已计共享) = 310
复用主题实例数：       50 + 40 = 90
复用率：              90 / 310 = 29.0%
```

---

## 3. 按类型衡量复用

### 3.1 Conref 分析

Conref 复用发生在主题引用另一主题的片段时：

```xml
<p conref="shared/common-precautions.dita#precautions/warning-electrical"/>
```

**衡量方法：** 统计主题正文中所有 `<* conref="..."` 属性。

### 3.2 Keyref 分析

Keyref 复用通过变量键进行：

```xml
<p>操作时务必佩戴 <keyword keyref="ppe-safety-glasses"/>。</p>
```

**衡量方法：** 统计所有 `<keyword keyref="..."` 和 `<ph keyref="..."` 元素。

### 3.3 地图级复用

地图级复用发生在同一主题被多个 ditamap 引用时：

```
product-a-manual.ditamap         product-b-manual.ditamap
  ├── safety-intro.dita            ├── safety-intro.dita        ← 已复用
  ├── product-a-setup.dita         ├── product-b-setup.dita
  └── common-maintenance.dita      └── common-maintenance.dita  ← 已复用
```

**衡量方法：** 跨所有 ditamap 交叉比较 `topicref/@href` 值。

---

## 4. 自动化复用率计算脚本

### 4.1 复用扫描脚本

保存为 `scripts/reuse-scanner.sh`：

```bash
#!/bin/bash
# DITA 复用扫描器 v1.0

set -euo pipefail

echo "=== DITA 内容复用报告 ==="
echo "日期：$(date '+%Y-%m-%d %H:%M')"
echo ""

# 1. 统计主题总数
total_topics=$(find . -name "*.dita" -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "DITA 主题总数：$total_topics"

# 2. 统计 conref 使用次数
conref_count=$(grep -rh 'conref=' --include="*.dita" . \
    -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Conref 实例：$conref_count"

# 3. 统计 conkeyref 使用次数
conkeyref_count=$(grep -rh 'conkeyref=' --include="*.dita" . \
    -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Conkeyref 实例：$conkeyref_count"

# 4. 统计 keyref 使用次数
keyref_count=$(grep -rh 'keyref=' --include="*.dita" . \
    -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Keyref 实例：$keyref_count"

# 5. 计算复用率
total_unique=$total_topics
reuse_instances=$((conref_count + conkeyref_count))
reuse_rate=$(echo "scale=2; ($reuse_instances * 100) / $total_unique" | bc 2>/dev/null || echo "N/A")

echo ""
echo "=== 复用率结果 ==="
echo "唯一主题总数：$total_unique"
echo "复用实例数（conref + conkeyref）：$reuse_instances"
echo "复用率：${reuse_rate}%"
```

### 4.2 Windows PowerShell 版本

保存为 `scripts/reuse-scanner.ps1`：

```powershell
# DITA 复用扫描器 v1.0（PowerShell）
Write-Host "=== DITA 内容复用报告 ===" -ForegroundColor Cyan
Write-Host "日期：$(Get-Date -Format 'yyyy-MM-dd HH:mm')"
Write-Host ""

$ditaFiles = Get-ChildItem -Recurse -Filter "*.dita" | Where-Object { $_.FullName -notmatch 'output|templates' }
$totalTopics = $ditaFiles.Count
Write-Host "DITA 主题总数：$totalTopics"

$conrefCount = Select-String -Pattern 'conref=' -Include "*.dita" -Recurse | Where-Object { $_.Path -notmatch 'output|templates' } | Measure-Object | Select-Object -ExpandProperty Count
Write-Host "Conref 实例数：$conrefCount"

$keyrefCount = Select-String -Pattern 'keyref=' -Include "*.dita" -Recurse | Where-Object { $_.Path -notmatch 'output|templates' } | Measure-Object | Select-Object -ExpandProperty Count
Write-Host "Keyref 实例数：$keyrefCount"

$reuseRate = [math]::Round(($conrefCount * 100) / $totalTopics, 2)
Write-Host "复用率：$reuseRate%" -ForegroundColor Green
```

### 4.3 定期执行计划

| 频率 | 范围 | 触发方式 |
|------|------|---------|
| **每次提交** | 仅变更文件 | Pre-commit 钩子 |
| **每次 PR** | 特性分支所有主题 | CI/CD PR 检查 |
| **每周** | 全仓库 | Cron 任务（周一 08:00） |
| **每月** | 全仓库 + 趋势报告 | 定时流水线 |
| **每季度** | 全面审计 + 优化计划 | 治理评审周期 |

---

## 5. CI/CD 复用门禁集成

### 5.1 CI/CD 复用门禁脚本

添加到 `scripts/reuse-gate.sh`：

```bash
#!/bin/bash
# 复用门禁脚本 — 验证复用率是否满足最低阈值
# 用法：./reuse-gate.sh [阈值]
# 示例：./reuse-gate.sh 15

set -euo pipefail

THRESHOLD="${1:-15}"

# 执行复用率扫描
chmod +x scripts/reuse-scanner.sh
./scripts/reuse-scanner.sh > reuse-report.txt

# 提取复用率
RATE=$(grep '复用率：' reuse-report.txt | grep -oP '\d+\.?\d*')

if [ -z "$RATE" ]; then
    echo "错误：无法从报告中提取复用率"
    exit 1
fi

echo "当前复用率：${RATE}%"

# 门禁检查
if (( $(echo "$RATE < $THRESHOLD" | bc -l) )); then
    echo "失败：复用率 ${RATE}% 低于 ${THRESHOLD}% 阈值"
    exit 1
else
    echo "通过：复用率 ${RATE}% 满足最低阈值 ${THRESHOLD}%"
fi
```

### 5.2 各阶段门禁阈值

| 阶段 | 最低阈值 | 警告阈值 | 处理方式 |
|------|---------|---------|---------|
| **第 1-3 月** | 不设门禁 | < 5% | 标记退化 |
| **第 4-6 月** | 10% | < 8% | 阻止 PR |
| **第 7-9 月** | 20% | < 15% | 阻止 PR |
| **第 10-12 月** | 30% | < 25% | 阻止 PR |
| **生产阶段** | 35% | < 30% | 阻止 PR，升级上报 |

---

## 6. 复用机会识别

### 6.1 重复内容检测

定期运行以查找相似主题：

```bash
#!/bin/bash
echo "=== 潜在重复内容候选 ==="
# 方法：按字数相近度查找
find . -name "*.dita" -not -path "*/output/*" | while read f1; do
    w1=$(wc -w < "$f1")
    find . -name "*.dita" -not -path "*/output/*" -not -name "$(basename "$f1")" | while read f2; do
        w2=$(wc -w < "$f2")
        diff=$((w1 > w2 ? w1 - w2 : w2 - w1))
        if [ "$diff" -lt 5 ] && [ "$w1" -gt 0 ]; then
            ratio=$(echo "scale=2; $diff / $w1 * 100" | bc)
            if [ "$(echo "$ratio < 10" | bc)" -eq 1 ]; then
                echo "  检查：$(basename "$f1") ~ $(basename "$f2")"
            fi
        fi
    done
done
```

### 6.2 复用机会检查清单

**编写新内容前：**
- [ ] 已在共享库中搜索该主题的现有内容
- [ ] 已按元数据关键词搜索（产品、组件、读者对象）
- [ ] 已检查现有内容是否可通过 `conref` 扩展
- [ ] 已确认其他产品中不存在类似流程

**主题评审期间：**
- [ ] 识别了跨主题重复的步骤
- [ ] 识别了可集中管理的警告和注意事项
- [ ] 识别了可移至 keyref 变量的规格参数
- [ ] 识别了可做 conref 的样板文本

**地图评审期间：**
- [ ] 检查该主题是否已被其他地图引用
- [ ] 考虑使用 `topicgroup` 实现共享导航结构
- [ ] 确认键定义覆盖了所有复用值

### 6.3 高价值复用候选

| 内容类型 | 复用潜力 | 优先级 | 典型 ROI |
|---------|---------|-------|---------|
| 安全警告/危险声明 | 极高 | P0 | 15-20% 复用增益 |
| 标准流程（开机、关机） | 高 | P0 | 10-15% |
| 规格参数表 | 高 | P1 | 8-12% |
| 法律免责声明 | 极高 | P0 | 5-8% |
| 简介/概述文本 | 中 | P1 | 3-5% |
| 故障排除步骤 | 中 | P2 | 2-5% |
| 术语表定义 | 极高 | P0 | 5-10% |
| 维护计划 | 低 | P2 | 1-3% |

---

## 7. 复用优化工作流

### 第 1 阶段：审计（每季度第 1 周）
1. 对全仓库运行复用扫描器
2. 识别复用贡献最低的 10 个主题
3. 标记重复内容候选
4. 生成复用机会报告

### 第 2 阶段：计划（第 2 周）
1. 在月度 DGC 会议上审查报告
2. 按投入产出比排列优先级
3. 将优化任务分配给主题负责人
4. 设定季度复用率目标

### 第 3 阶段：执行（第 3-8 周）
1. 作者将重复内容合并到共享库
2. 将硬编码值转换为 keyref 变量
3. 为可复用片段创建 conref 目标
4. 更新地图以引用共享主题

### 第 4 阶段：验证（第 9 周）
1. 运行复用扫描器衡量改进效果
2. 验证无损坏引用（运行完整构建）
3. 检查所有地图仍能正确输出
4. 记录经验教训

### 第 5 阶段：报告（第 10 周）
1. 生成季度复用报告
2. 向 DGC 提交趋势数据
3. 更新下季度目标
4. 表彰贡献突出的团队成员

---

## 8. 报告与仪表盘

### 8.1 季度复用报告模板

```markdown
# 季度复用报告 — 2026 年第 X 季度

## 概要
| 指标 | 本季度 | 上季度 | 变化 | 目标 |
|------|-------|-------|------|------|
| 简单复用率 | XX% | XX% | +/-X% | XX% |
| 加权复用率 | XX% | XX% | +/-X% | XX% |
| Conref 实例数 | XXX | XXX | +/-X | — |
| Keyref 实例数 | XXX | XXX | +/-X | — |
| 复用主题数 | XX | XX | +/-X | — |
| 主题总数 | XXX | XXX | +/-X | — |

## 本季度最高复用内容
1. [主题标题] — 被 X 个地图引用
2. [主题标题] — 被 X 个地图引用
3. [主题标题] — 被 X 个地图引用

## 已完成优化操作
- [x] 合并了 X 个重复主题
- [x] 将 X 个值转换为 keyref 变量
- [x] 创建了 X 个新的共享库条目
- [ ] 下季度末实现 XX% 的复用率目标

## 建议
1.
2.
3.
```

### 8.2 趋势追踪

追踪以下指标以展示改进成效：

| 季度 | 日期 | 复用率 | 加权率 | Conref 数 | Keyref 数 | 主题总数 |
|------|------|-------|--------|----------|----------|---------|
| 2026 Q1 | 2026-03-31 | | | | | |
| 2026 Q2 | 2026-06-30 | | | | | |
| 2026 Q3 | 2026-09-30 | | | | | |
| 2026 Q4 | 2026-12-31 | | | | | |

---

## 9. 目标与基准

### 9.1 年度目标

| 年度 | 最低 | 目标 | 挑战 |
|------|------|------|------|
| 第 1 年 | 15% | 25% | 35% |
| 第 2 年 | 25% | 35% | 45% |
| 第 3 年 | 30% | 40% | 50% |

### 9.2 团队级目标

| 团队 | 角色 | 复用贡献目标 |
|------|------|------------|
| 技术写作人员 | 创建可复用内容 | 30% 撰写的主题被复用 |
| 内容架构师 | 设计共享库 | 80% 的共享库主题被复用 |
| 产品经理 | 识别重复内容 | 每季度参与审计 |
| 本地化团队 | 报告复用缺口 | 每季度识别 5+ 个机会 |

---

## 相关文档

| 文档 | 位置 |
|------|------|
| 内容架构 | [ARCHITECTURE/01-CONTENT-ARCHITECTURE.md](../ARCHITECTURE/01-CONTENT-ARCHITECTURE.md) |
| 变量管理 | [ARCHITECTURE/06-VARIABLE-MANAGEMENT.md](../ARCHITECTURE/06-VARIABLE-MANAGEMENT.md) |
| 共享库治理 | [WORKFLOWS/04-SHARED-LIBRARY-GOVERNANCE.md](04-SHARED-LIBRARY-GOVERNANCE.md) |
| 质量检查清单 | [STYLE-GUIDE/03-QUALITY-CHECKLIST.md](../STYLE-GUIDE/03-QUALITY-CHECKLIST.md) |
| CI/CD 流水线 | [CI-CD/01-CICD-PIPELINE.md](../CI-CD/01-CICD-PIPELINE.md) |
