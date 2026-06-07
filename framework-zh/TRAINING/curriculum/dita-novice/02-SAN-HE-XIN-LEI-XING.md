---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->
# 模块 02：三大核心主题类型
## Concept | Task | Reference
### DITA 信息分类的核心支柱

---

# 学习目标

完成本模块后，你将能够：

- 区分 DITA 的三种核心主题类型及其适用场景
- 描述每种主题类型的结构和组成元素
- 为制造业文档选择合适的主题类型
- 掌握 Concept / Task / Reference 的黄金配比
- 阅读并理解每种类型的完整 XML 示例

---

# DITA 信息分类法

## 三大支柱主题类型

| 类型 | 回答的问题 | 制造业场景 | 占比建议 |
|------|-----------|-----------|----------|
| **Concept** | 这是什么？为什么？ | 功能原理、安全理念、系统架构 | **40%** |
| **Task** | 怎么做？ | 操作步骤、维护流程、故障排除 | **40%** |
| **Reference** | 具体参数是什么？ | 技术规格表、错误代码、零件清单 | **20%** |

---

# 三种类型的关系

```
┌──────────────────────────────────────┐
│             CONCEPT                  │
│   提供背景知识和"为什么"               │
│   ↓                                  │
│             TASK                     │
│   提供操作步骤和"怎么做"               │
│   ↓                                  │
│           REFERENCE                  │
│   提供细节参数和"具体值"               │
└──────────────────────────────────────┘
```

> **信息设计原则**：用户先理解概念（Concept）→ 再执行任务（Task）→ 需要时查阅参数（Reference）

---

# Concept（概念）主题

## 回答"是什么"和"为什么"

**适用场景**：
- 介绍产品功能原理
- 解释安全机制背景
- 描述系统架构
- 提供操作前的背景知识
- 行业标准和法规说明

**结构元素**：
- `<title>` — 主题标题
- `<shortdesc>` — 简短描述（摘要）
- `<conbody>` — 概念正文（包含段落、章节、图片）

---

# Concept 结构示意

```
<concept id="concept_xxx">
  ├── <title>            ← 主题标题（说明这是什么）
  ├── <shortdesc>        ← 1-2 句简短描述
  ├── <prolog>           ← 元数据区域
  └── <conbody>          ← 概念正文
       ├── <section>     ← 分节：工作原理
       ├── <section>     ← 分节：核心特性
       ├── <section>     ← 分节：应用场景
       └── <example>     ← 示例说明
```

---

# Concept 完整 XML 示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">
<concept id="cnc_spindle_overview">
  <title>CNC 主轴系统概述</title>
  <shortdesc>CNC 主轴是机床核心动力部件，负责驱动刀具旋转完成切削加工。</shortdesc>
  <conbody>
    <section>
      <title>工作原理</title>
      <p>主轴电机通过传动系统将电能转化为旋转动能。伺服控制系统
         实时调节转速和转矩，确保加工精度。</p>
    </section>
    <section>
      <title>核心特性</title>
      <ul>
        <li>最高转速：24000 RPM</li>
        <li>冷却方式：油冷循环</li>
        <li>控制精度：±0.001 mm</li>
      </ul>
    </section>
  </conbody>
</concept>
```

---

# Task（任务）主题

## 回答"怎么做"

**适用场景**：
- 操作步骤（开机、对刀、编程）
- 维护流程（日检、周检、年检）
- 故障排除（报警代码处理）
- 更换部件（刀具、滤芯、润滑油）
- 校准和参数设置

**结构元素**：
- `<prereq>` — 前置条件（需要什么？准备什么？）
- `<steps>` — 操作步骤（编号顺序）
- `<result>` — 预期结果
- `<postreq>` — 后续操作

---

# Task 写作规则

<!-- _class: tip -->

1. **每个步骤一个动作** — 不要在一个 `<step>` 中塞入多个操作
2. **使用命令式语气** — "按下急停按钮"，而非"你需要按下急停按钮"
3. **明确前置条件** — 注明工具、权限、安全装备要求
4. **步骤不超过 10 步** — 超过则拆分为子任务
5. **步骤后附预期结果** — 让用户确认操作是否正确

---

# Task 结构示意

```
<task id="task_xxx">
  ├── <title>            ← 任务标题（动宾结构：如何更换刀具）
  ├── <shortdesc>        ← 简短描述任务目标
  ├── <prolog>           ← 元数据区域
  ├── <taskbody>
  │    ├── <prereq>      ← 前置条件
  │    ├── <context>     ← 背景信息
  │    ├── <steps>       ← 操作步骤（核心）
  │    │    ├── <step><cmd>步骤1</cmd></step>
  │    │    ├── <step><cmd>步骤2</cmd></step>
  │    │    └── ...
  │    ├── <result>      ← 预期结果
  │    └── <postreq>     ← 后续操作
```

---

# Task 完整 XML 示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE task PUBLIC "-//OASIS//DTD DITA Task//EN" "task.dtd">
<task id="replace_cutting_tool">
  <title>更换切削刀具</title>
  <shortdesc>本任务描述在 CNC 加工中心上更换磨损刀具的标准流程。</shortdesc>
  <taskbody>
    <prereq>
      <p>确保已执行紧急停机操作，主轴已完全停止转动。
         准备新刀具、内六角扳手和防护手套。</p>
    </prereq>
    <steps>
      <step>
        <cmd>按下操作面板上的 <uicontrol>紧急停机</uicontrol> 按钮。</cmd>
      </step>
      <step>
        <cmd>使用内六角扳手松开刀柄锁紧螺母。</cmd>
        <info>逆时针旋转约 3-4 圈即可松开。</info>
      </step>
      <step>
        <cmd>取下磨损刀具，将新刀具装入刀柄。</cmd>
        <stepresult>刀具应完全插入刀柄底部，无间隙。</stepresult>
      </step>
    </steps>
    <result>
      <p>新刀具安装到位，机床准备就绪，可恢复加工。</p>
    </result>
  </taskbody>
</task>
```

---

# Task 步骤元素详解

| 步骤子元素 | 用途 | 是否必需 |
|-----------|------|----------|
| `<cmd>` | 操作指令（用户需执行的动作） | **必需** |
| `<info>` | 补充信息或说明 | 可选 |
| `<substeps>` | 子步骤列表 | 可选 |
| `<choices>` | 多种可选操作方式 | 可选 |
| `<stepresult>` | 该步骤完成后应出现的结果 | 可选 |
| `<stepxmp>` | 步骤示例 | 可选 |

> 最佳实践：在关键步骤中使用 `<stepresult>` 帮助用户确认操作正确性。

---

# Reference（参考）主题

## 回答"具体参数是什么"

**适用场景**：
- 技术规格参数表
- 错误代码列表
- 零件和材料清单
- 命令速查表
- 维护周期表
- 接线图和接口定义

**特征**：
- 以表格和列表为主要组织形式
- 信息密度高，适合快速查阅
- 不包含"如何做"的步骤

---

# Reference 结构示意

```
<reference id="ref_xxx">
  ├── <title>            ← 参考主题标题
  ├── <shortdesc>        ← 简短描述
  ├── <prolog>           ← 元数据区域
  └── <refbody>
       ├── <section>     ← 分节
       ├── <table>       ← 数据表格（核心）
       ├── <simpletable> ← 简易表格
       └── <properties>  ← 属性列表
```

---

# Reference 完整 XML 示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE reference PUBLIC "-//OASIS//DTD DITA Reference//EN" "reference.dtd">
<reference id="alarm_code_list">
  <title>CNC 报警代码速查表</title>
  <shortdesc>本主题列出常见 CNC 报警代码及其含义和处置建议。</shortdesc>
  <refbody>
    <section>
      <title>伺服系统报警</title>
      <table>
        <tgroup cols="3">
          <colspec colname="c1" colwidth="1*"/>
          <colspec colname="c2" colwidth="2*"/>
          <colspec colname="c3" colwidth="3*"/>
          <thead>
            <row>
              <entry>报警代码</entry>
              <entry>含义</entry>
              <entry>处置建议</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry>SV0001</entry>
              <entry>伺服驱动器过热</entry>
              <entry>检查冷却风扇，降低负载</entry>
            </row>
            <row>
              <entry>SV0002</entry>
              <entry>编码器通信故障</entry>
              <entry>检查编码器线缆连接</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
  </refbody>
</reference>
```

---

# 三种类型对比速查

| 维度 | Concept | Task | Reference |
|------|---------|------|-----------|
| 回答的问题 | 是什么？为什么？ | 怎么做？ | 具体参数？ |
| 根元素 | `<concept>` | `<task>` | `<reference>` |
| 正文容器 | `<conbody>` | `<taskbody>` | `<refbody>` |
| 典型结构 | 章节 + 段落 | 编号步骤 | 表格 + 列表 |
| 阅读方式 | 顺序阅读 | 边看边做 | 快速查阅 |
| DOCTYPE | concept.dtd | task.dtd | reference.dtd |

---

# 选择正确的主题类型

<!-- _class: tip -->

| 如果内容是... | 使用... |
|-------------|---------|
| 解释某个功能/概念/原理 | **Concept** |
| 描述执行某任务的操作流程 | **Task** |
| 列出参数、代码、规格数据 | **Reference** |
| 描述故障现象 + 排除步骤 | **Task** (troubleshooting) |
| 介绍一组相关功能的概述 | **Concept** |
| 提供维护周期和检查项目表 | **Reference** |

---

# 常见错误：类型误用

| 错误做法 | 正确做法 |
|----------|----------|
| 在 Concept 中嵌入操作步骤 | 将步骤提取为独立 Task 主题 |
| 在 Task 中大段解释背景 | 用 `<context>` 简述，或链接 Concept 主题 |
| 在 Reference 中加入操作指导 | 操作指导应移至 Task 主题 |
| 将所有内容塞入一个主题 | 按信息类型拆分为多个独立主题 |

> 一个主题 = 一种信息类型。不要混合！

---

# 关键要点

| # | 要点 |
|---|------|
| 1 | DITA 三大支柱：**Concept**（是什么/为什么）、**Task**（怎么做）、**Reference**（具体参数） |
| 2 | 推荐配比：**40% Concept + 40% Task + 20% Reference** |
| 3 | Concept 用 `<conbody>` 承载章节和段落 |
| 4 | Task 用 `<steps>` 承载编号步骤，每步一个动作 |
| 5 | Reference 以 **表格和列表** 为主，适合快速查阅 |
| 6 | **一个主题 = 一种信息类型**，不要混合使用 |

> **下一步**：学习 DITA XML 的基础语法和常用元素
