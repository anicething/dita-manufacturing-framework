---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

## 模块 08：初级实践项目
### 为 MC-5000 CNC 加工中心创建小型 DITA 文档集
#### 示例制造 · DITA 制造框架

---

## 学习目标

完成本模块后，你将能够：

- 从零规划一个小型 DITA 文档集
- 独立编写概念、任务、参考三类核心主题
- 创建结构良好的 DITA Map 组织主题
- 为制造业文档添加标准化元数据
- 执行同行评审并提供建设性反馈
- 使用检查清单验证交付物完整性
- 融会贯通前 7 个模块的全部知识点

---

## 议程

| 章节 | 内容 | 预计时间 |
|------|------|----------|
| 第一部分 | 项目概述与场景设定 | 15 分钟 |
| 第二部分 | 主题规划 | 20 分钟 |
| 第三部分 | 编写概念主题（安全概述） | 30 分钟 |
| 第四部分 | 编写任务主题（日常操作） | 30 分钟 |
| 第五部分 | 编写参考主题（规格表） | 20 分钟 |
| 第六部分 | 创建 DITA Map | 20 分钟 |
| 第七部分 | 添加元数据 | 15 分钟 |
| 第八部分 | 同行评审 | 20 分钟 |
| 第九部分 | 最终检查清单与评分 | 15 分钟 |

---

<!-- _class: divider -->

## 第一部分
### 项目概述与场景设定

---

## 项目背景

**示例制造**最新推出的 **MC-5000 型 CNC 加工中心**，需要为其编制第一套结构化技术文档。

| 项目属性 | 说明 |
|----------|------|
| 产品 | MC-5000 CNC 立式加工中心 |
| 目标用户 | 机床操作员、维护技师 |
| 交付格式 | PDF（印刷）+ HTML5（在线帮助） |
| 源格式 | DITA 1.3 XML |
| 工具链 | oXygen XML Editor + DITA-OT 4.x |
| 版本控制 | Git |

---

## MC-5000 产品简介

```
                    ┌─────────────────────────────┐
                    │     MC-5000 CNC 加工中心      │
                    ├─────────────────────────────┤
                    │  主轴转速：12,000 RPM         │
                    │  刀库容量：24 把刀具           │
                    │  行程(X/Y/Z)：800/500/500 mm  │
                    │  控制系统：FANUC 0i-MF Plus    │
                    │  定位精度：±0.005 mm          │
                    │  最大工件重量：500 kg          │
                    └─────────────────────────────┘
```

**本次文档集范围**：安全概述 + 日常操作 + 快速规格表（入门级，非完整手册）

---

## 你的角色与交付物

**角色**：初级技术文档工程师

**需要交付的文件**：

```
practice-project/
├── concepts/
│   └── dr5000-safety-overview.dita        ← 概念主题
├── tasks/
│   └── dr5000-daily-operation.dita        ← 任务主题
├── reference/
│   └── dr5000-quick-specs.dita            ← 参考主题
├── maps/
│   └── dr5000-quick-start.ditamap         ← DITA Map
└── README.txt                             ← 项目说明
```

---

## 项目约束与要求

| 维度 | 要求 |
|------|------|
| **主题数量** | 最少 3 个（概念 1 + 任务 1 + 参考 1） |
| **必须包含** | 短描述（shortdesc）在每个主题中 |
| **必须包含** | 至少一个有序列表（ol） |
| **必须包含** | 至少一个无序列表（ul） |
| **必须包含** | 至少一个表格（table） |
| **必须包含** | 至少一个注意事项（note） |
| **元数据** | 作者、日期、产品名称、版本 |
| **验证** | 通过 DITA-OT 验证无错误 |

---

<!-- _class: divider -->

## 第二部分
### 主题规划

---

## 主题规划方法论

在开始编写之前，先回答三个问题：

```
问题 1：用户需要知道什么？（背景知识）
  → 概念主题：安全机制、机器布局、基本术语

问题 2：用户需要做什么？（操作步骤）
  → 任务主题：开机检查、日常启动、关机流程

问题 3：用户需要查阅什么？（参数数据）
  → 参考主题：技术规格、刀具参数、环境要求
```

---

## 信息映射表

| 用户问题 | 信息类型 | DITA 主题 | 文件名 |
|----------|----------|-----------|--------|
| 机器有哪些安全装置？ | 概念 | Concept | `dr5000-safety-overview.dita` |
| 如何使用紧急停止？ | 概念/任务 | (含在上述主题) | -- |
| 如何执行日常开机？ | 任务 | Task | `dr5000-daily-operation.dita` |
| 如何执行日常关机？ | 任务 | (含在上述主题) | -- |
| 主轴参数是多少？ | 参考 | Reference | `dr5000-quick-specs.dita` |
| 刀库能放多少刀？ | 参考 | (含在上述主题) | -- |

---

## 主题粒度原则

<!-- _class: keypoint -->

## 一个主题 = 回答一个具体问题

| 太粗（不推荐） | 合适 | 太细（不推荐） |
|---------------|------|---------------|
| MC-5000 全部操作 | 日常开机操作 | 按下绿色按钮 |
| MC-5000 安全与操作 | 紧急停止程序 | 手指接触按钮的动作 |
| MC-5000 所有参数 | 主轴与刀库规格 | 主轴最大转速值 |

> **粒度测试**：如果你写的主题标题包含"和"或"与"字，可能需要拆分。

---

## 规划你的主题大纲

在开始编写之前，先用纯文本写出每个主题的大纲：

**概念主题 `dr5000-safety-overview.dita` 大纲**：

```
标题：MC-5000 安全系统概述
短描述：了解 MC-5000 CNC 加工中心关键安全系统
正文：
  1. 安全防护罩与联锁装置
  2. 紧急停止系统（E-Stop）
  3. 安全警示标识
  4. 操作前安全检查清单
```

建议在实际编写前，先在纸上或文本编辑器中完成大纲。

---

<!-- _class: divider -->

## 第三部分
### 编写概念主题：安全概述

---

## 概念主题结构

```xml
<concept id="dr5000-safety-overview">
  <title>MC-5000 安全系统概述</title>
  <shortdesc>了解 MC-5000 CNC 加工中心的关键安全系统，
    确保操作人员安全作业。</shortdesc>
  <conbody>
    <p>主体内容段落...</p>
    <section>...</section>
    <section>...</section>
  </conbody>
</concept>
```

**要点回顾**（模块 2）：
- `<concept>` 根元素，必须有 `@id`
- `<title>` 每个主题必须有且仅有一个标题
- `<shortdesc>` 必须紧接在标题之后
- `<conbody>` 包含所有正文内容

---

## 安全概述 -- 完整示例（第一部分）

```xml
<concept id="dr5000-safety-overview">
  <title>MC-5000 安全系统概述</title>
  <shortdesc>了解 MC-5000 CNC 加工中心的关键安全系统，
    确保操作人员安全作业。</shortdesc>
  <conbody>
    <p>MC-5000 型 CNC 加工中心配备了多层级安全防护系统，
      符合 ISO 13849-1 机械安全标准。操作人员必须理解以下
      安全系统的工作原理，方可在安全前提下高效作业。</p>

    <section>
      <title>安全防护罩</title>
      <p>加工区域由<b>全封闭式安全防护罩</b>保护，采用
        聚碳酸酯视窗，允许操作人员清晰观察加工状态。
        防护罩配备<u>电磁联锁开关</u>——罩门开启时，
        主轴和冷却系统立即停止。</p>
    </section>
```

---

## 安全概述 -- 完整示例（第二部分）

```xml
    <section>
      <title>紧急停止系统</title>
      <p>操作面板、可移动手轮、以及机床后侧共设有
         <b>3 个醒目红色紧急停止按钮</b>。</p>
      <ul>
        <li>按下 E-Stop → 全部轴运动立即停止</li>
        <li>主轴惯性运转 ≤ 2 秒内刹车</li>
        <li>恢复操作需要<b>手动复位</b>并确认安全条件</li>
      </ul>
      <note type="warning">每周应测试一次紧急停止功能，
        确保响应正常。测试时确保加工区域内无人。</note>
    </section>

```

---

## 安全概述 -- 完整示例（第三部分）

```xml
    <section>
      <title>安全警示标识</title>
      <table>
        <tgroup cols="2">
          <colspec colname="c1" colwidth="30%"/>
          <colspec colname="c2" colwidth="70%"/>
          <thead>
            <row>
              <entry>标识颜色</entry>
              <entry>含义</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry><b>红色</b></entry>
              <entry>禁止 / 危险 / 消防设备</entry>
            </row>
            <row>
              <entry><b>黄色</b></entry>
              <entry>警告 / 注意潜在危险</entry>
            </row>
            <row>
              <entry><b>蓝色</b></entry>
              <entry>强制性指令（如必须佩戴护目镜）</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
  </conbody>
</concept>
```

---

## 编写技巧回顾

<!-- _class: tip -->

| 技巧 | 说明 |
|------|------|
| **短描述先行** | `<shortdesc>` 应能独立回答"这个主题讲什么" |
| **一段一意** | 每个 `<p>` 聚焦一个知识点 |
| **恰当使用强调** | `<b>` 用于关键词，`<u>` 用于引入术语 |
| **安全警告用 note** | `type="warning"` 或 `type="danger"` |
| **列表表达并列关系** | 同层级安全装置用 `<ul>`，有顺序的用 `<ol>` |
| **表格表达对照关系** | 颜色-含义对照、参数-数值对照 |

---

<!-- _class: exercise -->

## 练习 1：编写你的概念主题

**任务**：在 `practice-project/concepts/` 目录下创建 `dr5000-safety-overview.dita`。

**要求**：
1. 包含完整的 `<title>` 和 `<shortdesc>`
2. 至少 2 个 `<section>`（每节有 `<title>`）
3. 至少 1 个 `<ul>` 或 `<ol>`
4. 至少 1 个 `<note>`（类型自选）
5. 至少 1 个 `<table>`

**参考内容来源**：MC-5000 安全手册、ISO 13849-1

**时间**：30 分钟

---

<!-- _class: divider -->

## 第四部分
### 编写任务主题：日常操作

---

## 任务主题结构

```xml
<task id="dr5000-daily-operation">
  <title>MC-5000 日常开机与关机</title>
  <shortdesc>每日操作标准程序，包括开机检查、启动和关机。</shortdesc>
  <taskbody>
    <prereq>...</prereq>        ← 前提条件
    <context>...</context>      ← 操作背景
    <steps>...</steps>          ← 操作步骤
    <result>...</result>        ← 预期结果
    <example>...</example>      ← 示例
    <postreq>...</postreq>      ← 后续操作
  </taskbody>
</task>
```

**要点**（模块 2）：
- `<steps>` 中只能使用 `<step><cmd>` 来写步骤
- 如果有子步骤，使用 `<substeps>`
- 步骤中的补充信息用 `<info>` 或 `<stepresult>`

---

## 日常操作 -- 完整示例（第一部分）

```xml
<task id="dr5000-daily-operation">
  <title>MC-5000 日常开机操作</title>
  <shortdesc>执行以下步骤，安全启动 MC-5000 CNC 加工中心。</shortdesc>
  <taskbody>

    <prereq>
      <p>操作人员必须<b>完成三级安全培训</b>并持有有效上岗证。
         所需工具：机床钥匙、清洁布、润滑油注油枪。</p>
    </prereq>

    <context>
      <p>本程序适用于每天首次开机操作。如果机器已在运行中，
        请参阅"连续加工操作"主题。</p>
    </context>
```

---

## 日常操作 -- 完整示例（第二部分）

```xml
    <steps>
      <step>
        <cmd>执行开机前目视检查</cmd>
        <info>
          <ul>
            <li>确认安全防护罩无破损</li>
            <li>确认紧急停止按钮<b>未被按下</b></li>
            <li>确认工作台无异物</li>
            <li>检查气源压力表读数（0.5-0.7 MPa）</li>
          </ul>
        </info>
      </step>
```

---

## 日常操作 -- 完整示例（第三部分）

```xml
      <step>
        <cmd>接通主电源</cmd>
        <info>将主电源开关旋转至<b> ON </b>位置。</info>
        <stepresult>控制面板屏幕亮起，FANUC 系统启动画面显示，
          约 30 秒后进入主操作界面。</stepresult>
      </step>

      <step>
        <cmd>执行原点复归</cmd>
        <substeps>
          <substep><cmd>将模式旋钮旋转至<b> ZERO RETURN </b>位置</cmd></substep>
          <substep><cmd>按下<b> +Z </b>方向键，Z 轴回归原点</cmd></substep>
          <substep><cmd>依次将 X 轴、Y 轴回归原点</cmd></substep>
          <substep><cmd>确认控制面板<b>原点指示灯</b>全部亮起</cmd></substep>
        </substeps>
        <stepresult>三轴原点指示灯亮起，机械坐标系归零。</stepresult>
      </step>
```

---

## 日常操作 -- 完整示例（第四部分）

```xml
      <step>
        <cmd>启动冷却系统和润滑泵</cmd>
        <info>按下操作面板上的<b>冷却泵启动</b>按钮，
          确认冷却液从喷嘴流出。检查导轨润滑泵是否自动启动。</info>
        <stepresult>冷却液流出正常，导轨自动润滑指示灯闪烁。</stepresult>
      </step>

      <step>
        <cmd>执行主轴预热程序（建议）</cmd>
        <info>如果机器闲置超过 4 小时，建议执行预热程序。
          将主轴转速设定为 3000 RPM，运转<b>5 分钟</b>。</info>
      </step>
    </steps>
```

---

## 日常操作 -- 完整示例（第五部分）

```xml
    <result>
      <p>控制面板无报警信息，三轴原点指示灯亮起，机器处于
        <b>就绪</b>状态，可投入加工生产。</p>
    </result>

    <postreq>
      <p>下一步可进行：</p>
      <ul>
        <li><xref href="dr5000-tool-setup.dita">刀具安装与对刀</xref></li>
        <li><xref href="dr5000-program-load.dita">加工程序载入</xref></li>
        <li><xref href="dr5000-first-part.dita">首件试切</xref></li>
      </ul>
    </postreq>

  </taskbody>
</task>
```

---

## 任务主题编写要点

| 元素 | 是否必需 | 说明 |
|------|----------|------|
| `<prereq>` | 可选 | 用户开始前需要什么（工具、权限、前序操作） |
| `<context>` | 可选 | 什么情况下执行此任务 |
| `<steps>` | **必需** | 核心操作步骤 |
| `<result>` | 可选 | 完成后的预期状态 |
| `<example>` | 可选 | 示例 |
| `<postreq>` | 可选 | 之后可以做什么 |

<!-- _class: note -->
> **最佳实践**：每个 `<cmd>` 写一个动作。用 `<info>` 补充说明，用 `<stepresult>` 描述该步的结果。

---

<!-- _class: exercise -->

## 练习 2：编写你的任务主题

**任务**：在 `practice-project/tasks/` 目录下创建 `dr5000-daily-operation.dita`。

**要求**：
1. 包含完整的 `<title>` 和 `<shortdesc>`
2. 至少 4 个 `<step>`（每个有 `<cmd>` 和 `<info>` 或 `<stepresult>`）
3. 至少 1 个 `<substeps>` 组
4. 包含 `<prereq>`、`<context>`、`<result>`、`<postreq>`
5. 至少 1 个 `<note>`（安全类型）

**时间**：30 分钟

---

<!-- _class: divider -->

## 第五部分
### 编写参考主题：快速规格表

---

## 参考主题结构

```xml
<reference id="dr5000-quick-specs">
  <title>MC-5000 快速规格表</title>
  <shortdesc>MC-5000 型 CNC 加工中心的主要技术参数一览。</shortdesc>
  <refbody>
    <section>...</section>
    <table>...</table>
    <section>...</section>
  </refbody>
</reference>
```

**适合参考主题的制造业内容**：
- 技术参数表、尺寸数据
- 刀具规格、材料兼容性
- 环境要求（温度/湿度/电源）
- 维护周期表、消耗品清单

---

## 快速规格表 -- 完整示例（第一部分）

```xml
<reference id="dr5000-quick-specs">
  <title>MC-5000 快速规格表</title>
  <shortdesc>MC-5000 CNC 立式加工中心的主要技术参数。</shortdesc>
  <refbody>

    <section>
      <title>加工能力</title>
      <table>
        <tgroup cols="2">
          <colspec colname="c1" colwidth="40%"/>
          <colspec colname="c2" colwidth="60%"/>
          <thead>
            <row><entry>参数</entry><entry>数值</entry></row>
          </thead>
          <tbody>
```

---

## 快速规格表 -- 完整示例（第二部分）

```xml
            <row>
              <entry>主轴最大转速</entry>
              <entry>12,000 RPM（内置式主轴电机）</entry>
            </row>
            <row>
              <entry>主轴锥度</entry>
              <entry>BT40</entry>
            </row>
            <row>
              <entry>X/Y/Z 轴行程</entry>
              <entry>800 / 500 / 500 mm</entry>
            </row>
            <row>
              <entry>快速移动速度（X/Y/Z）</entry>
              <entry>36 / 36 / 30 m/min</entry>
            </row>
            <row>
              <entry>最大工件重量</entry>
              <entry>500 kg</entry>
            </row>
            <row>
              <entry>定位精度</entry>
              <entry>±0.005 mm（全行程）</entry>
            </row>
            <row>
              <entry>重复定位精度</entry>
              <entry>±0.003 mm</entry>
            </row>
```

---

## 快速规格表 -- 完整示例（第三部分）

```xml
          </tbody>
        </tgroup>
      </table>
    </section>

    <section>
      <title>刀库参数</title>
      <table>
        <tgroup cols="2">
          <colspec colname="c1" colwidth="40%"/>
          <colspec colname="c2" colwidth="60%"/>
          <thead>
            <row><entry>参数</entry><entry>数值</entry></row>
          </thead>
          <tbody>
            <row><entry>刀库容量</entry><entry>24 把（刀臂式自动换刀）</entry></row>
            <row><entry>最大刀具直径</entry><entry>80 mm（满刀）/ 150 mm（邻空）</entry></row>
            <row><entry>最大刀具长度</entry><entry>300 mm</entry></row>
            <row><entry>最大刀具重量</entry><entry>8 kg</entry></row>
            <row><entry>换刀时间（T-T）</entry><entry>2.5 秒</entry></row>
          </tbody>
        </tgroup>
      </table>
    </section>
```

---

## 快速规格表 -- 完整示例（第四部分）

```xml
    <section>
      <title>工作环境要求</title>
      <table>
        <tgroup cols="2">
          <colspec colname="c1" colwidth="40%"/>
          <colspec colname="c2" colwidth="60%"/>
          <thead>
            <row><entry>参数</entry><entry>数值</entry></row>
          </thead>
          <tbody>
            <row><entry>环境温度</entry><entry>+5°C 至 +40°C</entry></row>
            <row><entry>相对湿度</entry><entry>≤ 80%（无冷凝）</entry></row>
            <row><entry>电源要求</entry><entry>3 相 AC 380V ±10%, 50Hz</entry></row>
            <row><entry>额定功率</entry><entry>18 kW</entry></row>
            <row><entry>气源要求</entry><entry>0.5 MPa, 200 L/min</entry></row>
          </tbody>
        </tgroup>
      </table>
    </section>

  </refbody>
</reference>
```

---

## 参考主题编写技巧

<!-- _class: tip -->

| 原则 | 说明 |
|------|------|
| **数据优先** | 参考主题的核心是数据，不是叙述 |
| **表格对齐** | 参数-数值是典型的键值对结构，适合用 2 列表格 |
| **分组清晰** | 用 `<section>` 对参数分组（加工能力/刀库/环境） |
| **单位规范** | 全部数值带上单位（mm, RPM, kg, kW） |
| **避免解释** | 参考主题只给数据，需要解释的内容放到概念主题 |
| **可检索性** | 标题和 shortdesc 要包含用户搜索时会使用的关键词 |

---

<!-- _class: exercise -->

## 练习 3：编写你的参考主题

**任务**：在 `practice-project/reference/` 目录下创建 `dr5000-quick-specs.dita`。

**要求**：
1. 包含完整的 `<title>` 和 `<shortdesc>`
2. 至少 3 个 `<section>`（每节有 `<title>`）
3. 每节包含至少 1 个 `<table>`（2 列：参数-数值）
4. 至少引用 10 项技术参数
5. 参照 MC-5000 真实或合理推测的规格

**时间**：20 分钟

---

<!-- _class: divider -->

## 第六部分
### 创建 DITA Map

---

## DITA Map 的作用

```
                     dr5000-quick-start.ditamap
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
  safety-overview      daily-operation      quick-specs
    （概念）              （任务）             （参考）
          │                   │                   │
     第二章（安全）      第三章（操作）      附录（规格）
```

DITA Map 定义了：
1. 主题的**集合**（哪些主题属于本文档）
2. 主题的**层级和顺序**（导航结构）
3. 主题间**关联关系**

---

## DITA Map 结构

```xml
<map id="dr5000-quick-start">
  <title>MC-5000 快速入门指南</title>

  <topichead>
    <topicheadmeta>
      <navtitle>第一章：安全须知</navtitle>
    </topicheadmeta>
    <topicref href="../concepts/dr5000-safety-overview.dita"/>
  </topichead>

  <topichead>
    <topicheadmeta>
      <navtitle>第二章：操作程序</navtitle>
    </topicheadmeta>
    <topicref href="../tasks/dr5000-daily-operation.dita"/>
  </topichead>

</map>
```

---

## DITA Map 完整示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-quick-start">
  <title>MC-5000 CNC 加工中心快速入门指南</title>

  <topichead>
    <topicheadmeta>
      <navtitle>第一章：安全须知</navtitle>
    </topicheadmeta>
    <topicref href="../concepts/dr5000-safety-overview.dita"
              type="concept">
      <topicmeta>
        <navtitle>安全系统概述</navtitle>
      </topicmeta>
    </topicref>
  </topichead>
```

---

## DITA Map 完整示例（续）

```xml
  <topichead>
    <topicheadmeta>
      <navtitle>第二章：基本操作</navtitle>
    </topicheadmeta>
    <topicref href="../tasks/dr5000-daily-operation.dita"
              type="task">
      <topicmeta>
        <navtitle>日常开机与关机</navtitle>
      </topicmeta>
    </topicref>
  </topichead>

  <appendix>
    <topicref href="../reference/dr5000-quick-specs.dita"
              type="reference">
      <topicmeta>
        <navtitle>技术规格参考</navtitle>
      </topicmeta>
    </topicref>
  </appendix>

</map>
```

---

<!-- _class: note -->

## Map 元素速查

| 元素 | 用途 |
|------|------|
| `<map>` | 根元素，DITA Map 的容器 |
| `<topicref>` | 引用一个主题文件 |
| `<topichead>` | 分组标题（不含具体主题，仅用于导航层次） |
| `<topicgroup>` | 分组（不出现在导航输出中） |
| `<topicmeta>` | 为引用的主题提供元数据覆盖 |
| `<navtitle>` | 导航标题，可与主题内标题不同 |
| `<reltable>` | 建立主题间关联关系表 |
| `<appendix>` | 附录部分 |

---

<!-- _class: exercise -->

## 练习 4：创建你的 DITA Map

**任务**：在 `practice-project/maps/` 目录下创建 `dr5000-quick-start.ditamap`。

**要求**：
1. 正确的 XML 声明和 DOCTYPE 声明
2. 使用 `<topichead>` 分组（至少 2 个章节）
3. 每个 `<topicref>` 正确指向你创建的 3 个主题
4. 每个引用包含 `<topicmeta>` 和 `<navtitle>`
5. 至少使用 1 个 `<appendix>` 元素

**时间**：20 分钟

---

<!-- _class: divider -->

## 第七部分
### 添加元数据

---

## 为什么需要元数据？

在制造业文档中，元数据用于：

| 元数据 | 作用 |
|--------|------|
| **作者/创建者** | 责任追溯 |
| **创建/修改日期** | 时效性管理 |
| **产品名称/型号** | 适用范围定义 |
| **文档版本** | 变更追踪 |
| **安全等级** | 访问权限控制 |
| **适用用户角色** | 定向发布 |
| **语言/翻译状态** | 本地化管理 |

---

## 在 DITA Map 中添加元数据

```xml
<map id="dr5000-quick-start">
  <title>MC-5000 CNC 加工中心快速入门指南</title>

  <topicmeta>
    <author>张三</author>
    <source>示例制造技术文档部</source>
    <critdates>
      <created date="2026-06-07"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <data name="product" value="MC-5000"/>
    <data name="version" value="1.0"/>
    <data name="safety-level" value="general"/>
    <data name="audience" value="operator"/>
    <data name="language" value="zh-CN"/>
  </topicmeta>

  <!-- 主题引用... -->
</map>
```

---

## 在主题中添加个体元数据

```xml
<task id="dr5000-daily-operation">
  <title>MC-5000 日常开机操作</title>
  <shortdesc>...</shortdesc>

  <prolog>
    <author>张三</author>
    <critdates>
      <created date="2026-06-07"/>
    </critdates>
    <resourceid appname="ERP" id="MC5000-DOC-OP-001"/>
    <data name="review-status" value="draft"/>
  </prolog>

  <taskbody>...</taskbody>
</task>
```

`<prolog>` 放在 `<taskbody>`、`<conbody>` 或 `<refbody>` 之前。

---

<!-- _class: exercise -->

## 练习 5：添加元数据

**任务**：为你创建的 DITA Map 和 3 个主题添加元数据。

**要求**：
1. Map 中：作者、创建日期、产品、版本、语言
2. 每个主题的 `<prolog>` 中：作者、创建日期、审核状态
3. 至少 1 个 `<resourceid>`（模拟与 ERP 系统的关联 ID）

**时间**：15 分钟

---

<!-- _class: divider -->

## 第八部分
### 同行评审

---

## 为什么需要同行评审？

```
          编写                  评审                修正
    ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │  作者编写    │ ──► │  同行评审    │ ──► │  作者修正    │
    │  初始草案    │     │  发现问题    │     │  改进质量    │
    └─────────────┘     └─────────────┘     └─────────────┘
                              │
                    ┌─────────┴─────────┐
                    │  技术准确性检查    │
                    │  结构完整性检查    │
                    │  语言规范性检查    │
                    │  DITA 合规性检查   │
                    └───────────────────┘
```

评审不是找茬，而是确保交付物质量的**关键环节**。

---

## DITA 同行评审检查清单

| 类别 | 检查项 |
|------|--------|
| **结构** | 每个主题有正确根元素和 @id |
| **结构** | `<title>` 和 `<shortdesc>` 存在且有意义 |
| **内容** | 信息准确、完整、无歧义 |
| **内容** | 安全警告正确标注（note type） |
| **格式** | XML 格式良好（标签正确嵌套和关闭） |
| **格式** | 缩进一致，推荐 2 空格 |
| **元数据** | prolog 中有作者和日期 |
| **链接** | 交叉引用（xref）路径正确 |

---

## 如何给出建设性反馈

<!-- _class: tip -->

| 不要这样写 | 应该这样写 |
|-----------|-----------|
| "这写得不好" | "标题建议加上产品型号，用户搜索时更易定位" |
| "步骤不对" | "第 3 步缺少冷却泵启动确认，建议补充 stepresult" |
| "格式乱" | "缩进不一致，建议使用 2 空格统一缩进，参见项目规范" |
| "缺东西" | "这个主题没有 shortdesc，预览时看不到概要" |

> **好反馈公式**：具体位置 + 存在问题 + 改进建议 + 参考规范

---

<!-- _class: exercise -->

## 练习 6：交换评审

**任务**：与同桌/团队成员交换项目，进行同行评审。

**要求**：
1. 使用上述检查清单逐项检查
2. 至少给出 3 条建设性反馈（书面，标注行号）
3. 至少发现 1 个并给出正面评价
4. 收到反馈后修正你的交付物

**时间**：20 分钟

---

<!-- _class: divider -->

## 第九部分
### 最终检查清单与评分

---

## 交付物完整性检查

在提交前，逐项确认：

```
□ concepts/dr5000-safety-overview.dita  存在且 DITA-OT 验证通过
□ tasks/dr5000-daily-operation.dita     存在且 DITA-OT 验证通过
□ reference/dr5000-quick-specs.dita     存在且 DITA-OT 验证通过
□ maps/dr5000-quick-start.ditamap       存在且 DITA-OT 验证通过
□ 所有主题含 <title> 和 <shortdesc>      已确认
□ 所有主题含 <prolog> 元数据             已确认
□ Map 含 <topicmeta> 元数据              已确认
□ 同行评审已完成，反馈已归入修正         已确认
□ README.txt 说明文件存在                已确认
```

---

## DITA-OT 验证命令

```bash
# 进入项目目录
cd practice-project/

# 对每个主题单独验证
dita --input maps/dr5000-quick-start.ditamap \
     --format=dita \
     --validate

# 尝试生成 HTML5 输出（完整验证）
dita --input maps/dr5000-quick-start.ditamap \
     --format=html5 \
     --output=output/html5/
```

验证通过的标准：
- 无 ERROR 级别信息
- WARNING 最多 2 个（且知道原因）
- HTML5 输出正确生成，可浏览器打开查看

---

## 评分标准

| 评分项 | 权重 | 满分 |
|--------|------|------|
| 主题结构完整性（三个主题齐全） | 20% | 20 |
| XML 规范（标签嵌套、缩进、@id） | 15% | 15 |
| 内容质量（准确、完整、可用） | 20% | 20 |
| shortdesc 质量（每个主题） | 10% | 10 |
| 元数据（prolog + topicmeta） | 10% | 10 |
| DITA Map 结构合理 | 10% | 10 |
| 同行评审参与度 | 10% | 10 |
| DITA-OT 验证通过 | 5% | 5 |

**总分**：100 分 | **及格线**：70 分 | **优秀**：90 分以上

---

## 8 模块总结回顾

| 模块 | 主题 | 核心技能 |
|------|------|----------|
| 01 | DITA 基础认知 | 理解 DITA 概念、历史、价值 |
| 02 | 三大核心类型 | Concept / Task / Reference |
| 03 | DITA XML 基础 | XML 语法、元素、属性 |
| 04 | 使用模板 | 框架模板、自定义内容 |
| 05 | 元数据基础 | prolog、topicmeta、audience |
| 06 | DITA Map 基础 | 导航结构、关系表 |
| 07 | 审批工作流程 | 状态管理、审核 |
| **08** | **实践项目** | **端到端文档集创建** |

---

## 从新手到熟练：你的成长路径

```
        初级（本模块）          中级             高级
   ┌─────────────────┐ ┌──────────────┐ ┌──────────────┐
   │ 3 主题 + 1 Map   │ │ 完整文档集    │ │ 定制化框架    │
   │ 基本结构         │ │ 复用策略      │ │ CI/CD 流水线  │
   │ 元数据入门        │ │ 多语言发布    │ │ 团队管理      │
   │ 同行评审         │ │ SubjectScheme │ │ 持续改进      │
   └─────────────────┘ └──────────────┘ └──────────────┘
              │                  │                  │
        DITA Novice       DITA Professional     DITA Expert
```

恭喜完成 DITA Novice 全部 8 个模块！继续加油向 Professional 进发。

---

## 知识测验

**1. 一个概念主题的根元素是什么？**

A. `<reference>` | B. `<concept>` | C. `<task>` | D. `<topic>`

**2. DITA Map 中哪个元素用于分组主题但不包含具体文件？**

A. `<topicref>` | B. `<topicgroup>` | C. `<topichead>` | D. `<reltable>`

**3. 哪个元素用于在任务主题中描述操作前提条件？**

A. `<context>` | B. `<prereq>` | C. `<postreq>` | D. `<result>`

**4. shortdesc 应该放在主题的哪个位置？**

A. 主题末尾 | B. prolog 之后 | C. title 之后、body 之前 | D. body 中间

**5. 验证 DITA 文件的命令选项是什么？**

A. `--check` | B. `--test` | C. `--validate` | D. `--verify`

---

## 测验答案

| 题号 | 正确答案 | 说明 |
|------|----------|------|
| 1 | B | 概念主题根元素是 `<concept>` |
| 2 | C | `<topichead>` 仅用于导航分组，不含文件引用 |
| 3 | B | `<prereq>` 定义任务前提条件 |
| 4 | C | `<shortdesc>` 紧接 `<title>` 之后、`<conbody>`/`<taskbody>` 之前 |
| 5 | C | DITA-OT 验证使用 `--validate` 参数 |

---

<!-- _class: divider -->

## 课程结束

### 模块 08：初级实践项目

**关键收获**：
- 从规划到交付的完整 DITA 文档集创建流程
- 概念、任务、参考三类主题的实战编写经验
- DITA Map 组织与元数据标准
- 同行评审与质量保证机制

**下一步**：进入 DITA Professional 系列，学习主题类型扩展、内容复用和本地化。

**项目文件归档**：将 `practice-project/` 目录提交至团队 Git 仓库。
