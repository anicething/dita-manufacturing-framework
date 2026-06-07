# 扩展主题类型 — 超越概念 / 任务 / 参考

**Document ID:** ARCH-004  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved

---

## 1. 目的

DITA 1.3 定义了超过 25 种主题类型。尽管概念/任务/参考覆盖了大部分内容需求，但制造型企业需要更多专门类型来处理安全、故障排查、术语、培训和结构化需求等领域。

本文档编目了本框架采纳的**所有补充类型**、它们的用例以及实施指南。

---

## 2. 快速参考

| # | 类型 | DOCTYPE | 使用时机 | 优先级 |
|---|------|---------|-------------|----------|
| 1 | **故障排查** | `troubleshooting` | 诊断和解决问题 — v1.0 中的头号缺口 | P0 |
| 2 | **术语条目** | `glossentry` | 以可复用、可翻译的方式定义术语 | P0 |
| 3 | **危害声明** | `hazard`（域） | 用于 ISO 合规的结构化安全警告 | P1 |
| 4 | **Subject Scheme** | `subjectScheme` | 元数据治理的受控值映射 | P1 |
| 5 | **Bookmap** | `bookmap` | 正式出版结构（章节、前附页/后附页） | P2 |
| 6 | **任务需求** | `taskreq` | 结构化前置条件（技能、工具、条件） | P2 |
| 7 | **学习对象** | `learningObject` | 在线学习内容包 | P3 |
| 8 | **学习概览** | `learningOverview` | 学习模块介绍 | P3 |
| 9 | **学习内容** | `learningContent` | 核心教学内容主体 | P3 |
| 10 | **学习总结** | `learningSummary` | 模块回顾和评估 | P3 |
| 11 | **术语分组** | `glossgroup` | 相关术语条目集合 | P1 |
| 12 | **API 参考** | `reference`（带 API 域） | 编程接口文档 | P3 |
| 13 | **任务**（扩展） | `task`（带制造领域） | 包含零件、工具、流体的增强流程 | P1 |

---

## 3. 主题类型决策树

使用此流程图选择正确的类型：

```
这是什么类型的内容？
│
├─ 理解/解释？
│   └─► 概念
│
├─ 分步操作流程？
│   └─► 任务（如有复杂前置条件，则使用任务需求）
│
├─ 查阅数据（规格、参数）？
│   └─► 参考
│
├─ 问题诊断？
│   └─► 故障排查
│
├─ 安全警告？
│   └─► 危害声明（Machine Industry Domain）
│
├─ 术语定义？
│   └─► 术语条目
│
├─ 培训/在线学习？
│   └─► 学习对象 → 概览 → 内容 → 总结
│
└─ 出版结构？
    └─► Ditamap（Web）或 Bookmap（打印/PDF）
```

---

## 4. 逐类型规格

### 4.1 故障排查（`troubleshooting`）

**目的：** 根据症状诊断问题。每个主题涵盖一个报警代码或可观察症状，并列出所有可能的原因及相应的解决措施。

**弥补的缺口：** 框架 v1.0 没有结构化方式来编写维修指南。作者使用任务主题编写维修，但任务假设单一固定路径 — 故障排查主题反映了同一症状可能有多种原因的现实。

**关键结构：**

```xml
<troubleshooting id="alarm-700016">
  <title>Coolant Pressure Low (Alarm 700016)</title>
  <troublebody>
    <condition>
      <p>Alarm 700016 displayed. Coolant pressure below 0.3 MPa.</p>
    </condition>
    <cause>
      <p>Return filter clogged (80% of cases)</p>
      <remedy>Clean or replace coolant return filter.</remedy>
    </cause>
    <cause>
      <p>Pump impeller damaged (15% of cases)</p>
      <remedy>
        <cmd>Replace coolant pump impeller.</cmd>
        <info>See Pump Replacement Task topic for steps.</info>
      </remedy>
    </cause>
  </troublebody>
</troubleshooting>
```

**模板：** `06-TEMPLATES/06-TROUBLESHOOTING-TEMPLATE.dita`

---

### 4.2 术语条目（`glossentry`）

**目的：** 将术语定义为独立、可复用、可翻译的主题。对于多语言环境（本框架 9 种语言）至关重要。

**弥补的缺口：** v1.0 使用 Markdown 术语表（`02-GLOSSARY.md`） — 一个无法通过 TMS 翻译或通过 conref 复用的纯表格。基于 DITA 的逐术语主题解决了这两个问题。

**关键结构：**

```xml
<glossentry id="gloss-lockout-tagout">
  <glossterm>Lockout/Tagout (LOTO)</glossterm>
  <glossdef>
    <p>Safety procedure that isolates energy sources and locks
       the disconnect device before maintenance.</p>
  </glossdef>
  <glossAlternative>
    <glossSurfaceForm>LOTO</glossSurfaceForm>
    <glossAcronym>LOTO</glossAcronym>
  </glossAlternative>
</glossentry>
```

**模板：** `06-TEMPLATES/07-GLOSSARY-ENTRY-TEMPLATE.dita`

**术语分组（`glossgroup`）：** 将相关条目收集到一个主题下 — 例如，所有安全术语归入一组。

---

### 4.3 危害声明（Machine Industry Domain）

**目的：** 编写结构化、机器可读的安全警告，可提取并审计以符合 ISO 12100 / GB/T 15706 合规要求。

**弥补的缺口：** v1.0 在概念主题内使用散文式警告。这些警告对合规审计不可见，且在不同产品间不一致。Machine Industry Domain 提供了专用的危害元素。

**关键结构：**

```xml
<p hazards="">
  <hazardstatement type="caution">
    <messagepanel>
      <typeofhazard>Flying chips</typeofhazard>
      <consequence>Severe eye injury</consequence>
      <howtoavoid>Wear safety glasses with side shields</howtoavoid>
    </messagepanel>
    <hazardsymbol href="safety-goggles.svg"/>
  </hazardstatement>
</p>
```

**模板：** `06-TEMPLATES/08-HAZARD-STATEMENT-TEMPLATE.dita`

---

### 4.4 Subject Scheme（`subjectScheme`）

**目的：** 为元数据字段定义受控值，使作者从列表中选择而非输入自由文本。支持验证、过滤和一致的报告。

**弥补的缺口：** v1.0 元数据中有类似 `audience="admin|user|developer|executive"` 的列表，但没有强制执行。Subject Scheme 使这些值可被机器检查。

**关键结构：**

```xml
<subjectScheme>
  <schemehandler>
    <topicref href="http://docs.oasis-open.org/dita/v1.3/...
                 base-type/scheme.shell.ditamap"/>
  </schemehandler>
  <hasInstance>
    <subjectdef keys="audience">
      <subjectdef keys="operator"/>
      <subjectdef keys="maintenance"/>
      <subjectdef keys="engineer"/>
    </subjectdef>
  </hasInstance>
  <enumerationdef>
    <attributedef name="audience"/>
    <subjectdef keyref="audience"/>
  </enumerationdef>
</subjectScheme>
```

**参考仓库：** `02-ARCHITECTURE/05-SUBJECT-SCHEME-GUIDE.md`  
**模板：** `06-TEMPLATES/12-SUBJECT-SCHEME-TEMPLATE.ditamap`

---

### 4.5 Bookmap（`bookmap`）

**目的：** 为打印/PDF 出版物结构化内容，包含正式的前附页（目录、列表）、章节、附录和索引。

**弥补的缺口：** v1.0 仅有 Ditamap，适用于 Web 导航但缺少书籍特有功能：零件编号、版本信息、章节编号、专用后附页、索引生成。

**关键结构：**

```xml
<bookmap id="dr5000-user-guide">
  <booktitle>
    <mainbooktitle>MC-5000 User Guide</mainbooktitle>
    <booktitlealt>Version 2.1.A</booktitlealt>
  </booktitle>
  <frontmatter>
    <booklists>
      <toc/>
      <figurelist/>
      <tablelist/>
    </booklists>
  </frontmatter>
  <chapter href="safety-overview.dita"/>
  <chapter href="operations.dita"/>
  <backmatter>
    <appendix href="specifications.dita"/>
    <amendments/>
  </backmatter>
</bookmap>
```

**模板：** `06-TEMPLATES/11-BOOKMAP-TEMPLATE.ditamap`

---

### 4.6 任务需求（`taskreq`）

**目的：** 为任务定义结构化前置条件：所需技能等级、认证、工具、零件、环境条件和安全预防措施。

**弥补的缺口：** v1.0 任务主题仅有纯文本 `<prereq>` 段落。复杂的维护任务（如主轴对中）需要机器可读的需求，这些需求可根据技术员资质和库存进行核对。

**关键结构：**

```xml
<taskrequirements id="spindle-alignment-reqs">
  <reqcond>
    <personnel>
      <skill level="3">Level 3 Technician</skill>
      <certification>Spindle Alignment Certified</certification>
    </personnel>
    <equipment>
      <item>Laser alignment kit (HZ-5000-KIT)</item>
      <item>Torque wrench (5-50 Nm, calibrated)</item>
    </equipment>
    <parts>
      <item>O-ring for taper seal (P/N: OR-1024)</item>
    </parts>
    <environment>
      <temp min="10" max="40">10-40°C ambient</temp>
      <cleanliness class="ISO 8">Clean room optional</cleanliness>
    </environment>
    <safety>
      <hazardref href="gloss-lockout-tagout.dita">LOTO required</hazardref>
    </safety>
  </reqcond>
</taskrequirements>
```

**模板：** `06-TEMPLATES/09-TASK-REQUIREMENTS-TEMPLATE.dita`

---

### 4.7 学习与培训类型

**目的：** 创建结构化在线学习内容，可渲染为自定进度的 HTML 课程、LMS 的 SCORM 包，或打印的讲师指南。

**四种相关类型：**
- `learningObject` — 容器：将概览 + 内容 + 总结组合在一起
- `learningOverview` — 模块介绍：目标、前置条件、时长
- `learningContent` — 核心教学内容主体（可包含概念/任务/参考内容）
- `learningSummary` — 回顾、知识检查、下一步

**模板：** `06-TEMPLATES/10-LEARNING-OBJECT-TEMPLATE.dita`

---

## 5. 从 v1.0 的迁移路径

| v1.0 做法 | v1.1 目标 | 工作量 |
|---------------|-------------|--------|
| 概念主题中的散文式安全警告 | 专用的危害声明主题 | 中 |
| Markdown 术语表格 | 逐术语 DITA 术语条目主题 | 高（一次性转换） |
| 作为任务主题的维修流程 | 故障排查 + 任务需求 | 中 |
| 仅 Ditamap 发布 | Bookmap 用于 PDF 输出 | 低 |
| 自由文本元数据值 | Subject Scheme 受控值 | 中 |

---

## 6. 实施顺序

**阶段 1（P0，立即）：**
- 故障排查模板 + 示例
- 术语条目模板 + 现有术语表的转换

**阶段 2（P1，下个季度）：**
- 危害声明集成到安全内容中
- 全部元数据字段的 Subject Scheme
- 制造领域任务增强（工具、零件、流体）

**阶段 3（P2-P3，持续进行）：**
- Bookmap 用于正式出版物
- 复杂流程的任务需求
- 学习对象类型用于培训内容

---

**Document ID:** ARCH-004  
**下次评审日期：** 2026 年 Q4
