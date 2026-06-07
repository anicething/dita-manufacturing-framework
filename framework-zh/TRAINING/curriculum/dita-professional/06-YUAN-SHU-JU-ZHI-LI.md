---
marp: true
theme: dita-training
class: invert
paginate: true
---

# 元数据治理

## DITA Professional — 模块 6

**M1-M7 规则体系与自动化验证**

---

## 学习目标

完成本模块后，您将能够：

- 理解元数据治理在制造业文档中的关键作用
- 掌握框架 M1-M7 七条元数据规则的详细定义
- 编写 Schematron 规则实现自动化元数据验证
- 计算并追踪元数据质量指标 (MQ-Score)
- 配置元数据质量报表看板
- 实施治理执行机制（Pre-commit Hook + CI 门禁）

---

## 为什么制造业需要元数据治理

| 治理缺口 | 表现 | 后果 |
|----------|------|------|
| 未标记受众 | 技师文档和操作员文档混在一起 | 错误操作，安全风险 |
| 产品名拼写不一致 | "MC5000" / "DR 5000" / "MC-5000" | 搜索失败，内容孤立 |
| 无状态标记 | 草稿和已发布文档无法区分 | 车间可能使用过时文档 |
| 缺少技能等级 | 新手拿到专家级维护指导 | 操作超纲，设备损坏 |

> 元数据治理确保「对的人在对的时机看到对的内容」

---

## M1-M7 规则体系总览

```
                                ┌─────────────────────┐
                                │  M1-M7 规则体系      │
                                │  元数据质量 =        │
                                │  强制性 + 结构性     │
                                └──────────┬──────────┘
           ┌────────────────┬────────┬─────┴─────┬────────┬────────────────┐
           ▼                ▼        ▼           ▼        ▼                ▼
    ┌──────────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
    │ M1: 最低     │ │ M2: 产品 │ │ M3: 受众 │ │ M4: 文档 │ │ M5: 内容 │ │ M6: 技能 │
    │ 必要元数据   │ │ 隶属关系  │ │ 角色定位  │ │ 生命周期  │ │ 类型分类  │ │ 等级分级  │
    └──────────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘
                                                                         │
                                                                         ▼
                                                                 ┌──────────────┐
                                                                 │ M7: 专业级  │
                                                                 │ 复合规则     │
                                                                 └──────────────┘
```

---

## M1: 最低必要元数据 (Mandatory Minimum)

**规则**：每个 DITA 主题必须声明以下 5 个核心属性：

| 属性 | 说明 | 示例值 | 严重级别 |
|------|------|--------|----------|
| `@id` | 主题唯一标识 | `task-replace-filter` | **阻断** |
| `@xml:lang` | 语言代码 | `zh-CN` | **阻断** |
| `@product` | 所属产品线 | `dr5000` | **阻断** |
| `@audience` | 目标受众 | `technician` | **阻断** |
| `@document-status` | 文档状态 | `approved` | **阻断** |

> 阻断 (Blocking) = 缺少该属性时 CI 构建失败，不允许合并

---

## M1 验证示例

**错误示例**（缺少 M1 属性）：
```xml
<!-- 缺失: @product, @audience, @document-status -->
<task id="replace-coolant-filter" xml:lang="zh-CN">
  <title>更换冷却液过滤器</title>
  ...
</task>
```
```
Schematron 错误:
  ✗ M1-FAIL: @product 属性缺失 (task: replace-coolant-filter)
  ✗ M1-FAIL: @audience 属性缺失 (task: replace-coolant-filter)
  ✗ M1-FAIL: @document-status 属性缺失 (task: replace-coolant-filter)
```

**正确示例**：
```xml
<task id="replace-coolant-filter" xml:lang="zh-CN"
      product="dr5000" audience="technician"
      document-status="approved">
```

---

## M2: 产品隶属关系 (Product Affiliation)

**规则**：`@product` 值必须在 Subject Scheme 的 `product-values` 枚举中。

```xml
<!-- Subject Scheme 中定义的产品合法值 -->
<subjectdef keys="product-values" navtitle="产品线">
  <subjectdef keys="prod-dr5000" navtitle="MC-5000 CNC"/>
  <subjectdef keys="prod-spindlext300" navtitle="SpindleXT 300"/>
  <subjectdef keys="prod-hydramax200" navtitle="HydraMax 200"/>
  <subjectdef keys="prod-controlplus" navtitle="ControlPlus"/>
</subjectdef>
```

**违规示例**：
- `product="dr-5000"` -- 使用了连字符 (应为 dr5000)
- `product="coolpro5000"` -- 已停产产品 (应从 Subject Scheme 移除)
- `product="all"` -- 未定义的模糊值

---

## M3: 受众角色定位 (Audience Targeting)

**规则**：`@audience` 必须从 Subject Scheme 定义的 6 类受众中选择。

```
受众层级模型：
audience-values
  ├── operator (操作员)
  │   ├── operator-novice     ← 新手操作员
  │   └── operator-senior     ← 高级操作员
  ├── technician (维修技师)
  ├── engineer (工程师)
  ├── manager (管理人员)
  ├── trainer (培训师)
  └── inspector (质检员)
```

**适用原则**：
- 安全警告类主题 → `@audience` 含全部 6 类（每个人都必须看到）
- 电气图纸专题 → `@audience="engineer"` (仅工程师)
- 日常点检表 → `@audience="operator"` (仅操作员)

---

## M3 多受众标记

当一个主题面向多个受众时：

```xml
<!-- 安全警告：面向所有受众 -->
<hazardstatement id="hazard-flying-chips"
    product="dr5000"
    audience="operator technician engineer manager trainer inspector"
    document-status="approved">
```

```xml
<!-- 冷却系统维护：面向技师和工程师 -->
<task id="maintain-coolant-system"
    product="dr5000"
    audience="technician engineer"
    document-status="approved">
```

> 注意：`@audience` 支持空格分隔的多值，需在 Subject Scheme 中启用多选模式。

---

## M4: 文档生命周期 (Document Lifecycle)

**规则**：`@document-status` 必须为 5 个合法状态之一，且变更必须遵循状态机。

```
         ┌─────────────┐
    ────▶│   draft     │────▶ 创作中
         └──────┬──────┘
                │
                ▼
         ┌─────────────┐
         │   review    │────▶ 同行评审
         └──────┬──────┘
                │
                ▼
         ┌─────────────┐
         │  approved   │────▶ 批准发布
         └──────┬──────┘
                │
      ┌─────────┴─────────┐
      ▼                   ▼
┌───────────┐     ┌──────────────┐
│ published │     │  deprecated  │
│  已发布    │     │   已废弃      │
└───────────┘     └──────────────┘
```

---

## M4 状态转换规则

| 当前状态 | 允许转换至 | 禁止转换至 |
|----------|------------|------------|
| `draft` | `review` | `published`, `approved` (跳级) |
| `review` | `draft` (退回), `approved` | `published` (跳过审批) |
| `approved` | `published`, `deprecated`, `draft` (重新起草) | `review` (批准后不应评审) |
| `published` | `deprecated` | `draft` (已发布不可重起草) |
| `deprecated` | 无 (终态) | 任何状态（不可逆） |

---

## M5: 内容类型分类 (Content Type Classification)

**规则**：`@content-type` 必须声明，且与主题 DOCTYPE 一致。

| DITA 主题类型 | @content-type 值 | 验证方式 |
|---------------|------------------|----------|
| `<concept>` | `concept` | DOCTYPE 匹配 |
| `<task>` / `<machinery-task>` | `task` | DOCTYPE 匹配 |
| `<reference>` | `reference` | DOCTYPE 匹配 |
| `<troubleshooting>` | `troubleshooting` | DOCTYPE 匹配 |
| `<glossentry>` | `glossary` | DOCTYPE 匹配 |
| `<hazardstatement>` | `hazard` | DOCTYPE 匹配 |
| `<learningObject>` | `learning` | DOCTYPE 匹配 |
| `<taskrequirements>` | `requirements` | DOCTYPE 匹配 |

---

## M6: 技能等级分级 (Skill Level Assignment)

**规则**：所有 Task 和 Troubleshooting 主题必须标注 `@skill-level`。

| 等级 | 代号 | 适用人员 | 操作复杂度 |
|------|------|----------|------------|
| **Level 0** | `level-0` | 无需培训 | 目视检查、日常清洁 |
| **Level 1** | `level-1` | 操作员（基础培训） | 标准启停、简单更换 |
| **Level 2** | `level-2` | 操作员（高级培训） | 参数调整、多步骤操作 |
| **Level 3** | `level-3` | 技师 | 组件更换、故障诊断 |
| **Level 4** | `level-4` | 工程师 | 系统校准、程序修改 |

**典型映射**：
```xml
<task id="daily-check" skill-level="level-0">  <!-- 日常点检 -->
<task id="startup-sequence" skill-level="level-1">  <!-- 开机流程 -->
<task id="replace-spindle" skill-level="level-3">  <!-- 更换主轴 -->
```

---

<!-- _class: keypoint -->

## M7: 专业级复合规则 (Composite Rules)

**规则**：M1-M6 的组合约束，只有全部满足才算通过。

```
M7 = M1 (5个必填) + M2 (值合法) + M3 (受众合法)
   + M4 (状态有效) + M5 (类型匹配) + M6 (Task/TS 需技能等级)

专业级判定：
  全部 M1-M6 通过 → M7 通过 → 允许合并至 main 分支
  任一规则失败     → M7 失败 → CI 构建阻断
```

**M7 额外约束**：
- Safety 类主题 (`@content-type="hazard"`) 的 `@audience` 必须包含全部 6 种受众
- `@document-status="published"` 的主题不允许有未解析的 `<xref>`
- 同级目录中不得存在 `@id` 重复的主题文件

---

## Schematron 规则简介

**Schematron** 是一种基于 XML 的规则验证语言，天然适合验证 DITA 元数据。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://purl.oclc.org/dsdl/schematron"
        queryBinding="xslt2">
  <pattern id="M1-Mandatory-Minimum">
    <title>M1: 最低必要元数据检查</title>

    <!-- 检查 @product 属性存在 -->
    <rule context="*[@id][@xml:lang]">
      <assert test="@product"
        id="M1-PRODUCT-MISSING"
        role="error">
        M1-FAIL: <value-of select="@id"/> — @product 属性缺失
      </assert>
    </rule>
  </pattern>
</schema>
```

---

## Schematron 完整验证规则 — M1

```xml
<schema xmlns="http://purl.oclc.org/dsdl/schematron"
        queryBinding="xslt2">

  <pattern id="M1-Mandatory-Minimum">
    <rule context="*[contains(@class, ' topic/topic ')]">
      <!-- @id -->
      <assert test="@id" id="M1-ID" role="fatal">
        M1: 主题缺少 @id 属性 [文件: <value-of select="document-uri(/)"/>]
      </assert>
      <!-- @product -->
      <assert test="@product" id="M1-PRODUCT" role="error">
        M1: <value-of select="@id"/> — 缺少 @product 属性
      </assert>
      <!-- @audience -->
      <assert test="@audience" id="M1-AUDIENCE" role="error">
        M1: <value-of select="@id"/> — 缺少 @audience 属性
      </assert>
      <!-- @document-status -->
      <assert test="@document-status" id="M1-STATUS" role="error">
        M1: <value-of select="@id"/> — 缺少 @document-status 属性
      </assert>
    </rule>
  </pattern>
</schema>
```

---

## Schematron 规则 — M2 和 M3

```xml
  <!-- M2: 产品值合法性 -->
  <pattern id="M2-Product-Affiliation">
    <rule context="*[@product]">
      <assert test="@product = ('dr5000','spindlext300',
                'hydramax200','controlplus')"
        id="M2-PRODUCT-INVALID" role="error">
        M2: <value-of select="@id"/> — @product 值
        '<value-of select="@product"/>' 不在合法值域中
      </assert>
    </rule>
  </pattern>

  <!-- M3: 受众值合法性 -->
  <pattern id="M3-Audience-Targeting">
    <rule context="*[@audience]">
      <let name="aud-vals" value="tokenize(@audience, '\s+')"/>
      <assert test="every $a in $aud-vals satisfies
        $a = ('operator','technician','engineer',
              'manager','trainer','inspector')"
        id="M3-AUDIENCE-INVALID" role="error">
        M3: <value-of select="@id"/> — @audience 含非法值
        '<value-of select="@audience"/>'
      </assert>
    </rule>
  </pattern>
```

---

## Schematron 规则 — M4 和 M5

```xml
  <!-- M4: 文档状态合法性 -->
  <pattern id="M4-Document-Lifecycle">
    <rule context="*[@document-status]">
      <assert test="@document-status = ('draft','review',
                'approved','published','deprecated')"
        id="M4-STATUS-INVALID" role="error">
        M4: <value-of select="@id"/> — @document-status 值非法
      </assert>
    </rule>
  </pattern>

  <!-- M5: 内容类型与 DOCTYPE 匹配 -->
  <pattern id="M5-Content-Type">
    <rule context="*[contains(@class, ' topic/topic ')]">
      <assert test="
        (self::concept and @content-type='concept') or
        (self::task and @content-type='task') or
        (self::reference and @content-type='reference') or
        (self::troubleshooting and @content-type='troubleshooting')
        " id="M5-TYPE-MISMATCH" role="error">
        M5: <value-of select="@id"/> — @content-type 与 DITA 类型不匹配
      </assert>
    </rule>
  </pattern>
```

---

## Schematron 规则 — M6 和 M7

```xml
  <!-- M6: 技能等级 (仅 Task/Troubleshooting) -->
  <pattern id="M6-Skill-Level">
    <rule context="*[@content-type=('task','troubleshooting')]">
      <assert test="@skill-level = ('level-0','level-1',
                'level-2','level-3','level-4')"
        id="M6-SKILL-LEVEL-MISSING" role="error">
        M6: <value-of select="@id"/> — Task/Troubleshooting 必须声明
        @skill-level (level-0 至 level-4)
      </assert>
    </rule>
  </pattern>

  <!-- M7: 安全主题必须覆盖全部受众 -->
  <pattern id="M7-Safety-All-Audiences">
    <rule context="*[@content-type='hazard']">
      <assert test="count(tokenize(@audience, '\s+')) = 6"
        id="M7-HAZARD-AUDIENCE" role="error">
        M7: <value-of select="@id"/> — Hazard 主题必须面向全部 6 种受众
      </assert>
    </rule>
  </pattern>
```

---

## Schematron 执行方式

**方式一：oXygen 内置引擎**（交互式验证）

```
oXygen 自动加载 Schematron 文件 → 实时红色下划线提示
文件 → 验证 → 配置验证场景 → 添加 Schematron 规则文件
```

**方式二：命令行批量验证**（CI 集成）

```bash
# 使用 oXygen 的命令行 Schematron 引擎
schematron-validate.sh \
  --schema=schemas/metadata-governance.sch \
  --phase=M7-Professional \
  --input-dir=language/zh-CN/topics/ \
  --report=reports/metadata-validation.xml

# 或使用开源 Schematron 处理器
saxonb-xslt -s:language/zh-CN/topics/ \
  -xsl:schematron/iso_svrl_for_xslt2.xsl \
  -o:reports/svrl-output.xml \
  schematron=schemas/metadata-governance.sch
```

---

## 元数据质量指标 (MQ-Score)

**MQ-Score** 量化每个主题的元数据完整度。

```
MQ-Score 计算公式：

  MQ-Score = (M1 得分 × 0.30) + (M2 得分 × 0.10)
           + (M3 得分 × 0.15) + (M4 得分 × 0.10)
           + (M5 得分 × 0.10) + (M6 得分 × 0.15)
           + (M7 得分 × 0.10)

每条规则得分：Pass = 1.0, Fail = 0.0
满分：100
最低通过线：85 分
```

---

## MQ-Score 计算示例

```
主题: task-replace-coolant-filter.dita

  M1: ✓ (5/5 必填属性)        = 1.0 × 0.30 = 0.30
  M2: ✓ (product="dr5000")   = 1.0 × 0.10 = 0.10
  M3: ✓ (audience="tech")    = 1.0 × 0.15 = 0.15
  M4: ✓ (status="approved")  = 1.0 × 0.10 = 0.10
  M5: ✓ (content-type="task")= 1.0 × 0.10 = 0.10
  M6: ✓ (skill-level="level-3")=1.0×0.15 = 0.15
  M7: ✓ (复合规则通过)         = 1.0 × 0.10 = 0.10
  ─────────────────────────────────────────────
  MQ-Score = 100  ✓ 优秀
```

```
主题: reference-coolant-specs.dita (缺少 @document-status, @skill-level)

  M1: ✗ (4/5)                = 0.0 × 0.30 = 0.00
  ...
  MQ-Score = 55  ✗ 不合格
```

---

## 元数据质量报表看板

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
           元数据治理看板 — 2025-06-07
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

 整体 MQ-Score 均值:    92.3  ✓ (目标 ≥85)
 合格主题数:            847 / 912 (92.9%)
 不合格主题数:           65 / 912 (7.1%)

 按规则分布的通过率:
 M1 (最低必要):  ████████████████████ 98.4%
 M2 (产品隶属):  ██████████████████░░ 90.1%
 M3 (受众角色):  █████████████████░░░ 86.5%
 M4 (生命周期):  ██████████████████░░ 91.2%
 M5 (内容类型):  ████████████████████ 97.8%
 M6 (技能等级):  ████████████████░░░░ 82.3% ← 注意
 M7 (复合规则):  █████████████████░░░ 88.7%
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 按产品/受众维度的 MQ-Score 分布

```
按产品线：
┌──────────────┬───────┬───────┬────────┐
│ 产品          │ 主题数 │ 均分   │ 合格率  │
├──────────────┼───────┼───────┼────────┤
│ MC-5000      │ 342   │ 94.1  │ 96.2%  │
│ SpindleXT300 │ 278   │ 91.8  │ 91.4%  │
│ HydraMax200  │ 195   │ 89.7  │ 88.1%  │
│ ControlPlus  │ 97    │ 91.2  │ 90.5%  │
└──────────────┴───────┴───────┴────────┘

按受众角色：
┌────────────────┬───────┬───────┐
│ 目标受众        │ 主题数 │ 均分   │
├────────────────┼───────┼───────┤
│ operator       │ 312   │ 91.0  │
│ technician     │ 387   │ 93.5  │
│ engineer       │ 213   │ 92.1  │
└────────────────┴───────┴───────┘
```

---

## 治理执行机制一：Pre-commit Hook

```bash
#!/bin/bash
# .git/hooks/pre-commit — 元数据治理门禁

SCHEMA_FILE="schemas/metadata-governance.sch"
MIN_MQ_SCORE=85
FAILED=0

STAGED_DITA=$(git diff --cached --name-only --diff-filter=ACM \
  | grep '\.dita$')

for file in $STAGED_DITA; do
  RESULT=$(schematron-validate --schema="$SCHEMA_FILE" \
    --phase="M7-Professional" --file="$file" --format=json)

  MQ_SCORE=$(echo "$RESULT" | jq '.score')

  if [ "$(echo "$MQ_SCORE < $MIN_MQ_SCORE" | bc)" -eq 1 ]; then
    echo "ERROR: $file — MQ-Score = $MQ_SCORE (要求 ≥ $MIN_MQ_SCORE)"
    FAILED=1
  fi
done

if [ $FAILED -eq 1 ]; then
  echo ""
  echo "提交被阻止。请修复元数据问题后重试。"
  exit 1
fi

echo "元数据验证通过 ✓"
```

---

## 治理执行机制二：CI 门禁

```yaml
# .github/workflows/metadata-governance.yml
name: Metadata Governance Gate

on:
  pull_request:
    paths:
      - 'language/zh-CN/topics/**/*.dita'

jobs:
  metadata-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Schematron Validation
        run: |
          schematron-validate \
            --schema=schemas/metadata-governance.sch \
            --phase=M7-Professional \
            --input-dir=language/zh-CN/topics/ \
            --report=reports/metadata-report.json

      - name: Quality Gate
        run: |
          AVG_SCORE=$(jq '.average_score' reports/metadata-report.json)
          if [ "$(echo "$AVG_SCORE < 85" | bc)" -eq 1 ]; then
            echo "MQ-Score 均值 $AVG_SCORE < 85，构建失败"
            exit 1
          fi
```

---

## 治理执行机制三：质量退化检测

```yaml
      - name: Quality Regression Check
        run: |
          # 对比 PR 分支与 main 分支的 MQ-Score
          git checkout main
          schematron-validate --input-dir=... \
            --report=reports/main-report.json
          git checkout ${{ github.head_ref }}

          MAIN_SCORE=$(jq '.average_score' reports/main-report.json)
          PR_SCORE=$(jq '.average_score' reports/metadata-report.json)

          if [ "$(echo "$PR_SCORE < $MAIN_SCORE" | bc)" -eq 1 ]; then
            echo "WARNING: PR 导致元数据质量下降"
            echo "  main 分支 MQ-Score: $MAIN_SCORE"
            echo "  PR 分支 MQ-Score:   $PR_SCORE"
            # 降级 > 3 分则阻断
            if [ "$(echo "$MAIN_SCORE - $PR_SCORE > 3" | bc)" -eq 1 ]; then
              exit 1
            fi
          fi
```

---

## 元数据治理成熟度模型

```
Level 0: 无治理 — 主题无元数据要求，质量不可知
   │
   ▼
Level 1: 约定治理 — 团队文档约定元数据，无自动化
   │                                     ← 大多数组织在此
   ▼
Level 2: 静态检查 — Schematron + 手动执行
   │                                     ← 框架当前在此
   ▼
Level 3: 自动门禁 — Pre-commit + CI 阻断
   │                                     ← 目标状态
   ▼
Level 4: 预测治理 — ML 预测元数据缺失，自动建议
   │
   ▼
Level 5: 自适应治理 — 根据使用数据动态调整规则权重
```

---

## 元数据治理角色与职责

| 角色 | 职责 | 权限 |
|------|------|------|
| **内容作者** | 创建时填写 M1-M6 属性 | 编辑主题元数据 |
| **信息架构师** | 维护 Subject Scheme，定义新属性值 | 修改 .subjectscheme 和 .sch 文件 |
| **审查者** | 审核 M4 状态转换合理性 | 批准状态变更 PR |
| **CI 管理员** | 维护 Schematron 规则和 CI 管道 | 修改 CI 配置和 Hook |
| **质量经理** | 监控 MQ-Score 趋势，发起改进 | 查看质量报表 |

---

## 批量元数据修复策略

当发现大量主题缺少元数据时：

```bash
#!/bin/bash
# scripts/batch-metadata-fix.sh

# 为所有缺少 @product 的主题添加默认值
find language/zh-CN/topics/ -name "*.dita" | while read f; do
  # 检查是否缺 @product
  if ! grep -q 'product=' "$f"; then
    TEMP_DIR=$(dirname "$f")
    # 从目录路径推断产品
    if echo "$TEMP_DIR" | grep -q "dr5000"; then
      sed -i 's/<task id=/<task product="dr5000" id=/' "$f"
    elif echo "$TEMP_DIR" | grep -q "spindlext"; then
      sed -i 's/<task id=/<task product="spindlext300" id=/' "$f"
    fi
    echo "Fixed: $f (added @product)"
  fi
done
```

> 注意：批量修复后必须通过 Schematron 验证，确保不引入新错误。

---

<!-- _class: exercise -->

## 练习：编写 Schematron 规则并运行验证

**任务**：

1. 创建一个新文件 `schemas/my-metadata-check.sch`，包含以下规则：
   - M1: 检查 `@id` 和 `@xml:lang` 属性存在
   - M2: `@product` 值必须是 `dr5000`
   - M6: 如果 `@content-type="task"`，则必须有 `@skill-level`
2. 故意创建两个测试主题文件：
   - 一个缺少 `@product`（预期 M2 失败）
   - 一个 `@content-type="task"` 但无 `@skill-level`（预期 M6 失败）
3. 运行 Schematron 验证并检查输出报告
4. 修复两个主题文件，使 MQ-Score 达到 100

**验证**：修复后的两个主题 MQ-Score = 100

---

## 关键要点

- **M1-M7** 规则体系覆盖元数据的**存在性**(M1)、**合法性**(M2/M3)、**状态**(M4)、**类型一致性**(M5)、**分级**(M6) 和**复合约束**(M7)
- **Schematron** 是 DITA 元数据验证的标准工具，支持 `fatal/error/warning` 三级严重性
- **MQ-Score** 量化元数据质量，满分 100，通过线 **85 分**
- **Pre-commit Hook + CI 门禁 + 退化检测** 三套机制确保治理执行
- 治理成熟度分 5 级，大多数组织在 Level 1-2，框架目标 Level 3
- 批量修复时务必通过 Schematron 验证防止引入新错误

---

## 下一步

模块 7：**多语言发布**

学习多语言构建策略，掌握 DITA-OT CJK 字体配置，实现跨语言自动发布管道
