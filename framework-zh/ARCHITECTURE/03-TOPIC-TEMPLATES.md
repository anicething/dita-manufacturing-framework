# 主题模板 — 完整参考手册

**Document ID:** ARCH-003  
**Version:** 1.1  
**Last Updated:** 2026-06-07  
**Status:** Approved

> **v1.1 更新：** 为全部 8 种专门主题类型新增模板。
> 模板位于 `templates/`。

---

## 1. 模板索引

| # | 模板文件 | DTD | 用途 |
|---|--------------|-----|-------------|
| T1 | `01-CONCEPT-TOPIC-TEMPLATE.dita` | `concept.dtd` | 解释「是什么」和「为什么」 |
| T2 | `02-TASK-TOPIC-TEMPLATE.dita` | `task.dtd` | 分步操作流程 |
| T3 | `03-REFERENCE-TOPIC-TEMPLATE.dita` | `reference.dtd` | 查阅数据和规格 |
| T4 | `04-METADATA-TEMPLATE.xml` | — | 样板元数据块 |
| T5 | `05-DITAMAP-TEMPLATE.ditamap` | `map.dtd` | Web 导航结构 |
| **T6** | **`06-TROUBLESHOOTING-TEMPLATE.dita`** | **`troubleshooting.dtd`** | **根据症状诊断问题** |
| **T7** | **`07-GLOSSARY-ENTRY-TEMPLATE.dita`** | **`glossentry.dtd`** | **可复用的术语定义** |
| **T8** | **`08-HAZARD-STATEMENT-TEMPLATE.dita`** | **`reference.dtd` + hi-d** | **结构化安全警告** |
| **T9** | **`09-TASK-REQUIREMENTS-TEMPLATE.dita`** | **`task.dtd` + pr-d** | **结构化前置条件** |
| **T10** | **`10-LEARNING-OBJECT-TEMPLATE.dita`** | **`learningObject.dtd`** | **在线学习模块** |
| **T11** | **`11-BOOKMAP-TEMPLATE.ditamap`** | **`bookmap.dtd`** | **打印/PDF 出版物** |
| **T12** | **`12-SUBJECT-SCHEME-TEMPLATE.ditamap`** | **`subjectScheme.dtd`** | **元数据治理** |

> 模板 T6–T12 在 v1.1 中新增。设计原理参见 `04-EXTENDED-TOPIC-TYPES.md`。

---

## 2. 何时使用每个模板

```
┌──────────────────────────────────────────────────────────────────┐
│                          决策树                                  │
├──────────────────────────────────────────────────────────────────┤
│ 你正在创建什么类型的内容？                                        │
│                                                                  │
│ 解释概念或系统？                          → 模板 T1（概念）       │
│ 编写分步操作说明？                        → 模板 T2（任务）       │
│    └─ 复杂前置条件（技能、                 → 模板 T9（+TaskReq）  │
│        工具、条件）？                                             │
│ 列出数据或规格？                          → 模板 T3（参考）       │
│ 诊断问题？                                → 模板 T6（故障排查）   │
│ 编写安全警告？                            → 模板 T8（危害声明）   │
│ 定义术语？                                → 模板 T7（术语条目）   │
│ 创建在线学习内容？                        → 模板 T10（学习对象）  │
│ 组织打印出版物？                          → 模板 T11（Bookmap）   │
│ 定义元数据允许值？                        → 模板 T12（Scheme）    │
└──────────────────────────────────────────────────────────────────┘
```

---

## 3. 模板快速参考卡片

### T6 — 故障排查

```xml
<troubleshooting id="unique-id">
  <title>Troubleshooting: [Problem or Alarm]</title>
  <shortdesc>One-sentence summary of the symptom.</shortdesc>
  <troublebody>
    <condition>
      <p>What the operator sees. Quantitative thresholds.</p>
    </condition>
    <cause>
      <p>Probable cause (XX%): description</p>
      <remedy>
        <cmd>Step to fix this cause.</cmd>
        <stepresult>Verification step.</stepresult>
      </remedy>
    </cause>
    <!-- 多个 <cause> 元素，按可能性排序 -->
  </troublebody>
</troubleshooting>
```

**关键规则：**
- 一个故障排查主题 = 一个症状/报警代码
- 按最可能到最不可能的次序列出原因，并标注估计频率
- 每个 `<cause>` 包含一个或多个 `<remedy>` 元素
- 交叉引用相关任务主题以获取详细的维修步骤

---

### T7 — 术语条目

```xml
<glossentry id="gloss-term-name">
  <glossterm>Term Name</glossterm>
  <glossdef>
    <p>Clear, concise definition.</p>
  </glossdef>
  <glossAlternative>
    <glossAcronym>ACRONYM</glossAcronym>
  </glossAlternative>
  <glossRelated>
    <glossxref href="related-term.dita"/>
  </glossRelated>
</glossentry>
```

**关键规则：**
- 每个术语一个文件 — 实现精细化的翻译和复用
- 使用 `<glossAlternative>` 表示缩略词、缩写和表面变体
- 使用 `<glossRelated>` 构建术语网络
- 将相关术语组合到 `<glossgroup>` 主题中以实现组织化输出

---

### T8 — 危害声明

```xml
<reference id="hazard-name">
  <title>Hazard: [Name]</title>
  <refbody>
    <section>
      <p hazard="">
        <hazardstatement type="caution|warning|danger|notice">
          <messagepanel>
            <typeofhazard>Description</typeofhazard>
            <consequence>Injury or damage</consequence>
            <howtoavoid>Preventive measures</howtoavoid>
          </messagepanel>
        </hazardstatement>
      </p>
    </section>
  </refbody>
</reference>
```

**关键规则：**
- 每个危害 = 一个主题（允许从任务主题进行交叉引用）
- 严重性由 `<hazardstatement>` 上的 `@type` 定义：danger > warning > caution > notice
- 在同一主题中包含 ISO 风险评估表
- 危害符号引用 SVG 文件以实现无障碍渲染

---

### T9 — 任务需求（嵌入在任务中）

```xml
<task id="task-id">
  <taskbody>
    <taskrequirements id="reqs">
      <reqcond>
        <personnel>
          <skill level="3">Skill description</skill>
          <certification>Certification name</certification>
        </personnel>
        <equipment>
          <item>Tool name (P/N)</item>
        </equipment>
        <parts>
          <item>Part name (P/N)</item>
        </parts>
        <environment>
          <temp min="10" max="40">Temperature range</temp>
        </environment>
        <safety>
          <hazardref href="hazard-topic.dita">Reference</hazardref>
        </safety>
      </reqcond>
    </taskrequirements>
    <prereq>text prose</prereq>
    <steps>...</steps>
  </taskbody>
</task>
```

**关键规则：**
- `<taskrequirements>` 位于 `<taskbody>` 中 `<prereq>` 和 `<steps>` 之间
- 仅用于具有非平凡前置条件（工具、技能、环境）的流程
- 仅需少量前置条件的简单任务可使用纯 `<prereq>` 替代

---

### T10 — 学习对象

```xml
<learningObject id="module-id">
  <learningObjectbody>
    <learningOverview>
      <!-- 目标、前置条件、时长 -->
    </learningOverview>
    <learningContent>
      <!-- 核心教学内容主体 -->
      <learningPractice>
        <!-- 实践练习 -->
      </learningPractice>
    </learningContent>
    <learningSummary>
      <!-- 回顾、评估、下一步 -->
      <learningAssessment>
        <lcQuestion>...</lcQuestion>
      </learningAssessment>
    </learningSummary>
  </learningObjectbody>
</learningObject>
```

**关键规则：**
- 一个 learningObject 始终包含各一个：概览、内容、总结
- learningContent 可以按需包含概念、任务和参考内容
- learningSummary 必须包含评估（推荐多项选择）
- 时长、受众级别和前置条件在概览中声明

---

### T11 — Bookmap

```xml
<bookmap id="guide-id">
  <booktitle>
    <mainbooktitle>Guide Title</mainbooktitle>
  </booktitle>
  <frontmatter>
    <booklists><toc/><figurelist/><tablelist/></booklists>
  </frontmatter>
  <chapter href="chapter-file.dita"/>
  <backmatter>
    <appendix href="appendix-file.dita"/>
    <indexlist/>
  </backmatter>
</bookmap>
```

**关键规则：**
- 当输出目标为 **打印/PDF** 时使用 Bookmap；Web 使用 Ditamap
- Bookmap 增加了：书名、版本信息、前附页（目录）、后附页（附录、索引）
- 章节可以引用 Ditamap（嵌套结构）
- PDF 输出时始终在后附页中包含 `<indexlist/>`

---

### T12 — Subject Scheme

```xml
<subjectScheme id="scheme-id">
  <hasInstance>
    <subjectdef keys="attribute-name">
      <subjectdef keys="allowed-value-1"/>
      <subjectdef keys="allowed-value-2"/>
    </subjectdef>
  </hasInstance>
  <enumerationdef>
    <attributedef name="attribute-name"/>
    <subjectdef keyref="attribute-name"/>
  </enumerationdef>
</subjectScheme>
```

**关键规则：**
- 整个企业使用一个 Scheme 文件（而非每个产品一个）
- 每个 `<subjectdef>` 映射到一个受控值
- 每个 `<enumerationdef>` 将一个列表绑定到一个元数据属性
- 完整的设置指南参见 `05-SUBJECT-SCHEME-GUIDE.md`

---

## 4. 文件命名约定

| 类型 | 模式 | 示例 |
|------|---------|---------|
| 概念 | `[SUBJECT]-CONCEPT.dita` | `SAFETY-SYSTEM-CONCEPT.dita` |
| 任务 | `[SUBJECT]-TASK.dita` | `SPINDLE-ALIGNMENT-TASK.dita` |
| 参考 | `[SUBJECT]-REFERENCE.dita` | `MC5000-SPECS-REFERENCE.dita` |
| 故障排查 | `ALARM-[CODE]-TROUBLESHOOTING.dita` | `ALARM-700016-TROUBLESHOOTING.dita` |
| 术语条目 | `GLOSS-[TERM].dita` | `GLOSS-LOTO.dita` |
| 危害声明 | `HAZARD-[TYPE].dita` | `HAZARD-FLYING-CHIPS.dita` |
| 学习对象 | `TRAIN-[MODULE]-MODULE.dita` | `TRAIN-SAFETY-MODULE.dita` |

---

**Document ID:** ARCH-003  
**模板位置：** `templates/`  
**下次评审日期：** 2026 年 Q4
