---
marp: true
theme: uncover
class:
  - lead
  - invert
paginate: true
header: "DITA 制造框架 — 培训课程"
footer: "DITA 治理框架 v1.1 — 2026 年 6 月"
---

<!-- _class: lead invert -->
# DITA 制造框架
## 培训第二模块：扩展类型编写、工作流与发布

**培训对象：** 技术文档工程师、编辑
**时长：** 8 小时（含实操练习）

制造企业 — 文档治理委员会

---

## 第二模块目标

完成本培训后，您将能够：

- 从模板**编写**概念、任务和参考主题
- **编写**故障排查、术语条目和危害声明主题
- 在复杂流程中**使用**任务前置条件
- **配置** Subject Scheme 用于元数据治理
- **使用** Bookmap 构建印刷/PDF 输出结构
- **执行** 10 步审批工作流
- **遵循** Git 分支策略
- **触发** CI/CD 发布流水线

---

## 第二模块路线图

```
┌──────────────────────────────────────────────────┐
│  第一部分：核心编写（2 小时）                     │
│  ├── DITA 主题结构                               │
│  ├── 模板 T1–T5（概念/任务/参考）               │
│  └── 模板 T6–T8：故障排查、术语条目、危害声明   │
├──────────────────────────────────────────────────┤
│  第二部分：高级主题（2 小时）                     │
│  ├── 模板 T9：任务前置条件                       │
│  ├── 模板 T12：Subject Scheme                    │
│  ├── 交叉引用与 conref                           │
│  └── 条件处理                                    │
├──────────────────────────────────────────────────┤
│  第三部分：工作流（2 小时）                       │
│  ├── 10 步审批流程                               │
│  ├── Git 分支策略                                │
│  └── 评审标准                                    │
├──────────────────────────────────────────────────┤
│  第四部分：发布（2 小时）                         │
│  ├── 模板 T11：Bookmap 用于 PDF                  │
│  ├── DITA-OT 转换                                │
│  ├── GitHub Actions CI/CD                        │
│  └── 发布 SLA 与指标                             │
└──────────────────────────────────────────────────┘
```

---

## 模板一览 — T1 到 T12

v1.1 新增 7 个模板。全部 12 个模板位于 `06-TEMPLATES/`：

```
T1  01-CONCEPT-TOPIC-TEMPLATE.dita
T2  02-TASK-TOPIC-TEMPLATE.dita
T3  03-REFERENCE-TOPIC-TEMPLATE.dita
T4  04-METADATA-TEMPLATE.xml
T5  05-DITAMAP-TEMPLATE.ditamap
─── v1.1 新增以下 ─────────
T6  06-TROUBLESHOOTING-TEMPLATE.dita     ← 新增
T7  07-GLOSSARY-ENTRY-TEMPLATE.dita      ← 新增
T8  08-HAZARD-STATEMENT-TEMPLATE.dita    ← 新增
T9  09-TASK-REQUIREMENTS-TEMPLATE.dita   ← 新增
T10 10-LEARNING-OBJECT-TEMPLATE.dita     ← 新增
T11 11-BOOKMAP-TEMPLATE.ditamap          ← 新增
T12 12-SUBJECT-SCHEME-TEMPLATE.ditamap   ← 新增
```

> 今天重点学习 T6、T7、T8、T9、T11、T12。T10 在高级培训中覆盖。

---

## 编写故障排查主题

**模板 T6** — 按症状诊断问题。

```
<troubleshooting id="alarm-700016">
  <title>故障排查：冷却液压力低（报警 700016）</title>
  <shortdesc>诊断报警 700016 — 冷却液压力低于 0.3 MPa。</shortdesc>
  <troublebody>
    <condition>
      <p>操作员看到的症状。量化阈值。</p>
    </condition>
    <cause id="cause-80pct">
      <p><b>可能原因（80%）：</b>回液过滤器堵塞。</p>
      <remedy>
        <cmd>清洁或更换过滤器滤芯。</cmd>
        <stepresult>压力恢复至 0.4–0.6 MPa。</stepresult>
      </remedy>
    </cause>
    <cause id="cause-15pct">
      <p><b>可能原因（15%）：</b>泵叶轮损坏。</p>
      <remedy>
        <cmd>更换泵组件。</cmd>
      </remedy>
    </cause>
  </troublebody>
</troubleshooting>
```

**关键规则：**
- 一个主题 = 一个症状或报警代码
- 按可能性排序原因（最可能在前）
- 每个 `<cause>` 必须至少包含一个 `<remedy>`

---

## 故障排查 — 最佳实践

**应做：**
- ✅ 包含频率估计（80%、15%、5%）— 帮助技师确定优先级
- ✅ 在 `<condition>` 中使用量化阈值（"压力 < 0.3 MPa"）
- ✅ 通过 `<xref>` 交叉引用到详细维修步骤的任务主题
- ✅ 在元数据中添加 `alarm-code` 和 `severity`

**不应做：**
- ❌ 不要将多个症状合并到一个主题中
- ❌ 如果只有一个原因，不要使用故障排查类型（使用任务即可）
- ❌ 不要忘记 `stepresult` — 告诉技师修复是否成功

> **示例：** `examples/topics/zh-CN/new-types/ALARM-700016-TROUBLESHOOTING.dita`

---

## 编写术语条目

**模板 T7** — 将术语定义为可复用、可翻译的主题。

```xml
<glossentry id="gloss-lockout-tagout-zh">
  <glossterm>锁定/挂牌（LOTO）</glossterm>
  <glossdef>
    <p>在维护操作前隔离能源的安全程序。</p>
  </glossdef>
  <glossAlternative>
    <glossAcronym>LOTO</glossAcronym>
  </glossAlternative>
  <glossRelated>
    <glossxref href="GLOSS-INTERLOCK.dita"/>
  </glossRelated>
</glossentry>
```

**关键规则：**
- **每个文件一个术语** — 支持细粒度翻译
- 如果术语有缩略词，务必包含 `<glossAlternative>`
- 使用 `<glossRelated>` 建立术语关系网络
- 在元数据中设置 `translatable="yes"`

> **示例：** `examples/topics/zh-CN/new-types/GLOSS-LOTO.dita`

---

## 编写危害声明

**模板 T8** — 使用机械行业域的结构化安全警告。

```xml
<reference id="hazard-flying-chips-zh">
  <refbody>
    <section id="warning">
      <p hazard="">
        <hazardstatement type="caution">
          <messagepanel>
            <typeofhazard>飞溅的切屑</typeofhazard>
            <consequence>严重眼部伤害</consequence>
            <howtoavoid>佩戴带侧护板的安全护目镜</howtoavoid>
          </messagepanel>
        </hazardstatement>
      </p>
    </section>
    <section id="risk-assessment">
      <!-- GB/T 15706 风险表 -->
    </section>
  </refbody>
</reference>
```

**严重性级别：** Danger > Warning > Caution > Notice

| 类型 | 含义 | 示例 |
|------|------|------|
| `danger` | 必然导致死亡 | 电击 |
| `warning` | 可能导致死亡 | 运动部件 |
| `caution` | 轻伤 | 飞溅切屑 |
| `notice` | 财产损失 | 冷却液泄漏 |

> **示例：** `examples/topics/zh-CN/new-types/HAZARD-FLYING-CHIPS.dita`

---

## 编写任务前置条件

**模板 T9** — 复杂流程的结构化前置条件。

```xml
<taskbody>
  <taskrequirements id="spindle-reqs">
    <reqcond>
      <personnel>
        <skill level="3">3 级技师</skill>
        <certification>主轴校准认证</certification>
      </personnel>
      <equipment>
        <item>激光校准套件（HZ-5000-KIT）</item>
        <item>扭矩扳手（5-50 Nm）</item>
      </equipment>
      <parts>
        <item>锥孔密封圈（料号：OR-1024）</item>
      </parts>
      <environment>
        <temp min="10" max="40">10-40°C</temp>
      </environment>
      <safety>
        <hazardref href="HAZARD-FLYING-CHIPS.dita"/>
      </safety>
    </reqcond>
  </taskrequirements>
  <prereq>散文式前置条件</prereq>
  <steps>...</steps>
</taskbody>
```

> 仅在**非平凡**前置条件下使用 `<taskrequirements>`。简单任务可以使用普通的 `<prereq>`。

---

## 练习 1a：编写扩展类型主题（30 分钟）

选择**一个**场景，用正确的类型编写简短的 DITA 主题：

1. **故障排查：** MC-5000 报警 700045 — "门锁打开"。
   门未完全关闭时机床停止。3 种可能原因：门未关好（60%）、
   互锁传感器偏移（30%）、接线故障（10%）。

2. **术语条目：** 定义"主轴预热循环" — 一种编程例行程序，
   在切削开始前以递增速度运行主轴以分配润滑。缩略词：SWC。

3. **危害声明：** 换刀过程中高压冷却液喷射。级别：caution。
   后果：化学性眼部刺激。避免方法：佩戴防溅护目镜，
   ATC 循环期间保持门关闭。

> 使用相应的模板。您有 30 分钟。

---

## 第二部分：Subject Scheme 配置

**模板 T12** — 定义元数据的受控值。

```xml
<subjectScheme id="dr5000-scheme">
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

**配置流程：**
1. 根据您的产品线编辑 `12-SUBJECT-SCHEME-TEMPLATE.ditamap`
2. 在 oXygen 中注册：首选项 → DITA → Subject Scheme
3. 验证合规性：DITA → 验证 → 使用 Subject Scheme 验证
4. 添加 pre-commit git hook 用于 CI/CD 验证

> **所有值必须来自 Subject Scheme。** 详见 `02-ARCHITECTURE/05-SUBJECT-SCHEME-GUIDE.md`。

---

## 练习 1b：使用 Subject Scheme 的元数据（15 分钟）

使用模板中定义的 Subject Scheme 值，纠正以下元数据条目：

**修改前（有什么问题？）：**
```xml
<audience type="operators"/>
<othermeta name="content-type" content="Troubleshooting"/>
<othermeta name="document-status" content="waiting for review"/>
<othermeta name="product" content="MC5000 加工中心"/>
```

> **提示：** Scheme 定义的确切值为：
> - audience: `operator`, `maintenance`, `engineer`, `field-service`, `trainer`, `all`
> - content-type: `concept`, `task`, `reference`, `troubleshooting`, `glossary-entry`, `hazard-statement`, `learning-object`
> - document-status: `draft`, `review`, `approved`, `published`, `archived`
> - product: `MC-5000`, `MC-8000`, `DR-2000`, `common`

---

## 练习 1b — 参考答案

**修改后：**
```xml
<audience type="operator"/>
<othermeta name="content-type" content="troubleshooting"/>
<othermeta name="document-status" content="review"/>
<othermeta name="product" content="MC-5000"/>
```

**问题所在：**
- `operators` → `operator`（Subject Scheme 值，不允许复数）
- `Troubleshooting` → `troubleshooting`（小写 kebab-case）
- `waiting for review` → `review`（不在受控列表中）
- `MC5000 加工中心` → `MC-5000`（必须匹配产品代码）

> Subject Scheme 验证会自动捕获所有这些错误。

---

## 新类型的交叉引用

所有新类型以相同方式支持交叉引用：

```xml
<!-- 从任务主题链接到故障排查主题 -->
<xref href="ALARM-700016-TROUBLESHOOTING.dita">冷却液压力故障排查</xref>

<!-- 链接到术语条目以嵌入定义 -->
<xref href="GLOSS-LOTO.dita" format="dita">LOTO</xref>

<!-- 从安全前置条件链接到危害声明 -->
<xref href="HAZARD-FLYING-CHIPS.dita">飞溅切屑危害详情</xref>

<!-- 任务前置条件可以引用危害 -->
<hazardref href="HAZARD-FLYING-CHIPS.dita"/>
```

> **提示：** 故障排查 → 任务（详细维修）和任务 → 故障排查（诊断）双向交叉引用，为技师创建导航闭环。

---

## 第三部分：审批工作流（10 步）

审批工作流适用于**所有主题类型**，包括新增类型：

| 步骤 | 阶段 | 负责人 | SLA | 验证内容 |
|------|------|--------|-----|----------|
| 1–2 | 编写 | 技术文档工程师 | — | — |
| 3 | 自检 | 技术文档工程师 | 1 天 | 模板符合性 |
| 4 | 同行评审 | 其他文档工程师 | 1 天 | 类型正确性 |
| 5 | SME 评审 | 领域专家 | 2 天 | 技术准确性 |
| 6 | 合规评审 | 合规专员 | 2 天 | ISO 要求 |
| 7 | 编辑评审 | 编辑 | 1 天 | 风格指南 |
| 8 | 最终审批 | 内容所有者 | 1 天 | — |
| 9–10 | 暂存→发布 | CI/CD | 自动 | Scheme 验证 |

> **新增：** 步骤 6（合规评审）现在专门根据 GB/T 15706 验证危害声明。

---

## 第四部分：使用 Bookmap 进行印刷/PDF 发布

**模板 T11** — 为正式出版物组织内容。

```
bookmap
├── booktitle: "MC-5000 用户手册 v2.1"
├── frontmatter
│   ├── 目录、图列表、表列表
│   └── 前言
├── chapter: 安全
│   ├── 安全系统概述（概念）
│   ├── 危害声明（危害）
│   └── LOTO 流程（任务）
├── chapter: 维护
│   ├── 主轴校准（任务 + 前置条件）
│   ├── 冷却液过滤器更换（任务）
│   └── 报警 700016（故障排查）
├── chapter: 规格
│   └── 技术规格（参考）
├── backmatter
│   ├── 附录：术语表（术语条目主题）
│   └── 索引
```

> **规则：** PDF 出版使用 Bookmap → HTML/Web 使用 Ditamap。相同主题同时服务于两者。

---

## 练习 2：创建 Bookmap（30 分钟）

为"MC-5000 维护手册"创建一个 bookmap，包含：

1. **三个章节：**
   - 安全（1 个概念 + 1 个危害主题）
   - 维护（2 个任务主题 + 1 个故障排查主题）
   - 参考（1 个参考主题）

2. **前页：** 目录、图列表

3. **后页：** 1 个术语表附录 + 索引

**要求：**
- 使用带 `@navtitle` 的 `<chapter>` 元素
- 包含带作者和版权的 `<bookmeta>`
- 为尚未编写的主题添加占位符（使用 `processing-role="resource-only"`）

> 参考：`06-TEMPLATES/11-BOOKMAP-TEMPLATE.ditamap`

---

## 发布流水线 — v1.1 新增

扩展类型的新验证步骤：

```
文档工程师推送 feature 分支 → PR 到 develop →
GitHub Actions 触发：
├── 1. 验证 XML（所有 12 种 DTD）
├── 2. Subject Scheme 验证（元数据值）
├── 3. 故障排查检查：每个 <cause> 都有 <remedy>？
├── 4. 危害检查：严重性元数据与 @type 匹配？
├── 5. 术语检查：<glossdef> 存在且非空？
├── 6. 构建 HTML5 + PDF（通过 Bookmap 编排书内容）
├── 7. 部署到暂存服务器
└── 8. 通知评审人
```

> **新增：** Subject Scheme 验证现在是必需步骤——任何主题包含无效元数据值都将导致构建失败。

---

## 第二模块 — 知识测验

1. **故障排查**主题使用哪个 DTD？
2. 在**术语条目**中，哪个元素存放缩略词？
3. **危害声明**的四个严重性级别是什么？
4. `<taskrequirements>` 在任务主题的 `<taskbody>` 中位于什么位置？
5. **Bookmap** 和 **Ditamap** 有什么区别？
6. 哪个模板定义元数据的受控值？

> 写下您的答案。10 分钟后一起核对。

---

<!-- _class: lead invert -->
## 参考答案

1. `<!DOCTYPE troubleshooting PUBLIC "-//OASIS//DTD DITA Troubleshooting//EN" "troubleshooting.dtd">`
2. `<glossAcronym>` 位于 `<glossAlternative>` 内部
3. **Danger** > **Warning** > **Caution** > **Notice**
4. 在 `<prereq>` 和 `<steps>` 之间，在 `<taskbody>` 内
5. **Bookmap** = 正式出版物（前页、章、后页、索引），用于 PDF/印刷。
   **Ditamap** = 网页导航结构。
6. **模板 T12** — `12-SUBJECT-SCHEME-TEMPLATE.ditamap`

---

## 第二模块 — 签署清单

| 技能 | 模板 | 已演示 |
|------|------|--------|
| 编写概念主题 | T1 | ☐ |
| 编写任务主题 | T2 | ☐ |
| 编写参考主题 | T3 | ☐ |
| 编写故障排查主题 | T6 | ☐ |
| 编写术语条目 | T7 | ☐ |
| 编写危害声明 | T8 | ☐ |
| 使用任务前置条件 | T9 | ☐ |
| 配置 Subject Scheme | T12 | ☐ |
| 构建 Bookmap 结构 | T11 | ☐ |
| 使用 xref 交叉引用 | — | ☐ |
| 遵循 10 步工作流 | — | ☐ |
| 理解 CI/CD 流水线 | — | ☐ |

> **下一节：** 第三模块 — 工具配置与高级编写

---

<!-- _class: lead invert -->
# 第二模块完成

**下一节：** 第三模块 — 工具配置与高级编写

DITA 制造框架 v1.1
制造企业 — 文档治理委员会

---

## 速查：扩展类型元素

| 元素 | 用途 | 用于 |
|------|------|------|
| `<troublebody>` | 故障排查内容的容器 | T6 故障排查 |
| `<condition>` | 描述可观察的症状 | T6 故障排查 |
| `<cause>` | 可能的根本原因（按可能性排序） | T6 故障排查 |
| `<remedy>` | 解决特定原因的步骤 | T6 故障排查 |
| `<glossentry>` | 术语条目的根元素 | T7 术语 |
| `<glossterm>` | 被定义的术语 | T7 术语 |
| `<glossdef>` | 定义内容 | T7 术语 |
| `<glossAlternative>` | 缩略词、缩写、表面形式 | T7 术语 |
| `<hazardstatement>` | 结构化安全警告 | T8 危害 |
| `<typeofhazard>` | 危害类型（"飞溅切屑"） | T8 危害 |
| `<consequence>` | 未避免的后果 | T8 危害 |
| `<howtoavoid>` | 预防措施 | T8 危害 |
| `<taskrequirements>` | 结构化前置条件块 | T9 任务 |
| `<reqcond>` | 所有前置条件的容器 | T9 任务 |
| `<personnel>` / `<equipment>` / `<parts>` | 资源需求 | T9 任务 |
| `<bookmap>` | 正式出版物根元素 | T11 Bookmap |
| `<chapter>` | 书籍章节（映射到 Ditamap 或主题） | T11 Bookmap |
| `<subjectScheme>` | 元数据治理根元素 | T12 Scheme |
| `<subjectdef>` | 定义受控值 | T12 Scheme |
| `<enumerationdef>` | 将值绑定到属性 | T12 Scheme |
