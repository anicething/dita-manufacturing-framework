---
marp: true
theme: dita-training
class: invert
paginate: true
---

# 扩展主题类型

## DITA Professional — 模块 1

**13 种 DITA 主题类型全面解析**

---

## 学习目标

完成本模块后，您将能够：

- 列举框架所采用的 13 种 DITA 主题类型
- 理解三层模型的设计理念与分布比例
- 为特定制造业场景选择合适的主题类型
- 掌握六种专用主题类型的结构与用法
- 了解 v1.0 到 v1.1 的主题类型迁移路径

---

## DITA 主题类型全景

| 序号 | 主题类型 | 层级 | 用途 |
|------|----------|------|------|
| 1 | Concept（概念） | 核心 | 解释背景原理 |
| 2 | Task（任务） | 核心 | 操作步骤指导 |
| 3 | Reference（参考） | 核心 | 规格参数表 |
| 4 | Troubleshooting（故障排查） | 专用 | 诊断与修复 |
| 5 | Glossary Entry（术语条目） | 专用 | 术语定义 |
| 6 | Hazard Statement（危险声明） | 专用 | 安全警告 |

---

## DITA 主题类型全景（续）

| 序号 | 主题类型 | 层级 | 用途 |
|------|----------|------|------|
| 7 | Task Requirements（任务前提条件） | 专用 | 执行前要求 |
| 8 | Learning Object（学习对象） | 专用 | 培训材料 |
| 9 | Bookmap（书图） | 高级 | 出版组织 |
| 10 | Subject Scheme（主题方案） | 高级 | 受控词汇 |
| 11 | Ditamap（图） | 核心 | 主题组织 |
| 12 | Composite（复合） | 核心 | 组合主题 |
| 13 | Machinery Task（机械任务） | 专用 | 机械操作 |

---

## 三层模型 (Three-Tier Model)

```
                    ┌─────────────┐
                    │   高级 5%    │  Bookmap, Subject Scheme
                    │  Advanced   │
                    ├─────────────┤
                    │   专用 15%   │  Troubleshooting, Glossary,
                    │ Specialized │  Hazard, TaskRequirements,
                    │             │  LearningObject, MachineryTask
                    ├─────────────┤
                    │             │
                    │   核心 80%   │  Concept, Task, Reference,
                    │    Core     │  Ditamap, Composite
                    │             │
                    └─────────────┘
```

---

## 核心层详解 — Concept（概念）

**用途**：解释背景知识、原理和工作机制

```xml
<concept id="coolant-system-overview">
  <title>冷却系统工作原理</title>
  <conbody>
    <p>冷却系统通过循环冷却液将加工热量从主轴和刀具带走，
       维持机床在最佳温度范围内运行。</p>
    <section>
      <title>主要组件</title>
      <ul>
        <li><b>冷却泵</b>：提供循环动力，额定流量 40 L/min</li>
        <li><b>热交换器</b>：将热量传递至外部冷却水源</li>
        <li><b>过滤器</b>：去除切屑和杂质，精度 50 μm</li>
      </ul>
    </section>
  </conbody>
</concept>
```

---

## 核心层详解 — Task（任务）

**用途**：逐步操作指导

```xml
<task id="replace-coolant-filter">
  <title>更换冷却液过滤器</title>
  <taskbody>
    <steps>
      <step><cmd>关闭冷却泵电源</cmd></step>
      <step><cmd>关闭过滤器进出口阀门</cmd></step>
      <step><cmd>用扳手松开过滤器壳体</cmd></step>
      <step><cmd>取出旧滤芯，检查密封圈</cmd></step>
      <step><cmd>安装新滤芯，拧紧壳体</cmd></step>
    </steps>
  </taskbody>
</task>
```

---

## 核心层详解 — Reference（参考）

**用途**：结构化规格数据，适合表格呈现

```xml
<reference id="coolant-specs">
  <title>冷却液规格参数</title>
  <refbody>
    <table>
      <tgroup cols="3">
        <colspec colname="param"/>
        <colspec colname="value"/>
        <colspec colname="unit"/>
        <thead>
          <row><entry>参数</entry><entry>数值</entry><entry>单位</entry></row>
        </thead>
        <tbody>
          <row><entry>工作压力</entry><entry>0.5-2.0</entry><entry>MPa</entry></row>
          <row><entry>流量</entry><entry>40</entry><entry>L/min</entry></row>
          <row><entry>过滤精度</entry><entry>50</entry><entry>μm</entry></row>
        </tbody>
      </tgroup>
    </table>
  </refbody>
</reference>
```

---

## 专用层：Troubleshooting（故障排查）

**结构模型**：`condition → cause → remedy`

```xml
<troubleshooting id="trouble-alarm-700016">
  <title>故障排查：报警代码 700016 — 冷却液压力低</title>
  <shortdesc>诊断并解决冷却液压力低于阈值的故障</shortdesc>
  <troublebody>
    <condition>控制面板显示报警 700016，冷却液压力低于 0.3 MPa</condition>
    <troubleSolution>
      <cause>冷却液液位过低（发生概率 80%）</cause>
      <remedy>
        <steps>
          <step><cmd>检查冷却液箱液位计</cmd></step>
          <step><cmd>若低于 MIN 标记，添加冷却液至 MAX 线</cmd></step>
        </steps>
      </remedy>
    </troubleSolution>
  </troublebody>
</troubleshooting>
```

---

## Troubleshooting 多原因示例（续）

```xml
    <troubleSolution>
      <cause>冷却泵过滤器堵塞（发生概率 15%）</cause>
      <remedy>
        <steps>
          <step><cmd>关闭泵电源和阀门</cmd></step>
          <step><cmd>拆卸过滤器壳体，检查滤芯</cmd></step>
          <step><cmd>若堵塞严重，更换新滤芯（零件号 FL-050-01）</cmd></step>
        </steps>
      </remedy>
    </troubleSolution>
    <troubleSolution>
      <cause>压力传感器故障（发生概率 5%）</cause>
      <remedy>
        <steps>
          <step><cmd>用万用表测量传感器输出信号</cmd></step>
          <step><cmd>若偏差超过 10%，更换传感器（零件号 PS-4C20）</cmd></step>
        </steps>
      </remedy>
    </troubleSolution>
```

---

## Troubleshooting 设计原则

| 原则 | 说明 | 制造业应用 |
|------|------|------------|
| **一个报警一个主题** | 每个报警代码独立为单个主题 | 便于检索和维护 |
| **按概率排序** | 最常见原因排最前（80/15/5） | 减少平均修复时间 |
| **原因可验证** | 每个原因有客观判断标准 | 避免猜测性维修 |
| **补救可执行** | 步骤精确到工具和零件号 | 一线技工可直接操作 |
| **关联链接** | 链接到相关参考和任务主题 | 形成知识网络 |

---

## Glossary Entry（术语条目）

**结构**：`glossterm → glossdef → glossAlternative → glossRelated`

```xml
<glossentry id="gloss-loto">
  <glossterm>锁定/挂牌 (LOTO)</glossterm>
  <glossdef>一种安全程序，用于确保在设备维护期间危险能源
           已被隔离并锁定，防止意外启动造成人员伤害。</glossdef>
  <glossAlt>
    <glossAlternateForm>Lockout/Tagout</glossAlternateForm>
  </glossAlt>
  <glossRelated>
    <glossAbbreviation>LOTO</glossAbbreviation>
    <glossSeeAlso>能源隔离程序</glossSeeAlso>
    <glossSeeAlso>OSHA 1910.147</glossSeeAlso>
  </glossRelated>
</glossentry>
```

---

## Hazard Statement（危险声明）

**领域**：`hi-d`（Hazard Information Domain）

```xml
<hazardstatement id="hazard-flying-chips">
  <title>危险：飞溅切屑</title>
  <messagepanel>
    <typeofhazard>机械伤害 — 飞溅切屑</typeofhazard>
    <consequence>高速飞出的金属切屑可导致严重眼部损伤、
              皮肤割伤和穿刺伤</consequence>
    <howtoavoid>
      <ul>
        <li>始终佩戴符合 ANSI Z87.1 的防护眼镜</li>
        <li>确保机床防护门在加工期间关闭</li>
        <li>使用切屑防护挡板</li>
      </ul>
    </howtoavoid>
  </messagepanel>
</hazardstatement>
```

---

## Hazard Statement — 严重性等级

| 信号词 | 英文 | 严重程度 | 后果定义 |
|--------|------|----------|----------|
| **危险** | Danger | 最高 | 将导致死亡或严重伤害 |
| **警告** | Warning | 高 | 可能导致死亡或严重伤害 |
| **注意** | Caution | 中 | 可能导致中度伤害 |
| **通知** | Notice | 低 | 可能导致财产损失 |

---

## Hazard Statement — ISO 风险评估表

| 评估维度 | 内容 |
|----------|------|
| 危险源 | 高速旋转刀具产生的飞溅切屑 |
| 风险等级（评估前） | R2（中高风险） |
| 控制措施 | 防护门联锁 + 防护眼镜 + 防护挡板 |
| 风险等级（评估后） | R4（可接受风险） |
| 相关标准 | ISO 13857, ANSI Z87.1 |
| 评估日期 | 2025-03-15 |

---

## Task Requirements（任务前提条件）

**领域**：`pr-d`（Pre-requisite Domain）

```xml
<taskrequirements id="req-coolant-filter-replace">
  <title>更换冷却液过滤器的前提条件</title>
  <reqcond>
    <personnel>操作人员需持有 LOTO 授权证书</personnel>
    <equipment>
      <ul>
        <li>活动扳手 24mm</li>
        <li>新滤芯 FL-050-01</li>
        <li>密封圈 O-RNBR-050</li>
        <li>个人防护装备（PPE）全套</li>
      </ul>
    </equipment>
    <parts>
      <ul>
        <li>冷却泵电源断开并锁定</li>
        <li>过滤器进出口阀门处于关闭位置</li>
        <li>接油盘就位</li>
      </ul>
    </parts>
    <environment>工作区域通风良好，温度 5-40°C</environment>
    <safety>执行 LOTO 程序，确认能量隔离完成</safety>
  </reqcond>
</taskrequirements>
```

---

## Task Requirements — 元素说明

| 元素 | 英文 | 说明 |
|------|------|------|
| `personnel` | 人员 | 所需技能、资质、人数 |
| `equipment` | 设备 | 所需工具、仪器、量具 |
| `parts` | 备件 | 所需零件、材料、耗材 |
| `environment` | 环境 | 温度、湿度、照明等要求 |
| `safety` | 安全 | 安全措施、PPE 要求 |
| `duration` | 时间 | 预计完成时间（可选） |

---

## Learning Object（学习对象）

**三部分结构**：`overview → content → summary`

```xml
<learningObject id="lo-coolant-system-maintenance">
  <title>冷却系统维护 — 学习对象</title>
  <learningOverview>
    <duration>45 分钟</duration>
    <audience>CNC 维护技术人员（技能等级 3 级以上）</audience>
    <prerequisites>
      <ul>
        <li>已完成基础安全培训</li>
        <li>拥有 LOTO 授权</li>
      </ul>
    </prerequisites>
  </learningOverview>
```

---

## Learning Object（续）

```xml
  <learningContent>
    <learningContentRef keyref="concept-coolant-system"/>
    <learningContentRef keyref="task-coolant-filter"/>
    <learningContentRef keyref="trouble-alarm-700016"/>
    <learningPlan>
      <title>学习路径</title>
      <ol>
        <li>阅读冷却系统概念主题（10 分钟）</li>
        <li>观看更换过滤器视频演示（10 分钟）</li>
        <li>实际操作练习（20 分钟）</li>
        <li>完成知识测验（5 分钟）</li>
      </ol>
    </learningPlan>
  </learningContent>
  <learningSummary>
    <assessment>
      <p>通过标准：实际操作评分 >= 80 分，知识测验 >= 85 分</p>
    </assessment>
  </learningSummary>
</learningObject>
```

---

## Bookmap（书图）

**用途**：组织多主题出版物，生成 PDF/EPUB

```xml
<bookmap id="cooling-system-manual">
  <booktitle>
    <mainbooktitle>CoolPro 5000 冷却系统手册</mainbooktitle>
    <booktitlealt>维护与故障排除指南</booktitlealt>
  </booktitle>
  <bookmeta>
    <author>张三</author>
    <publisher>Advanced Manufacturing Corp.</publisher>
    <bookid>DOC-2025-CP5K-001</bookid>
    <bookchangehistory>
      <revision>v1.1, 2025-06-01, 张三, 新增故障排除章节</revision>
    </bookchangehistory>
  </bookmeta>
  <frontmatter>
    <toc/><!-- 目录 -->
    <preface/>
    <abbrevlist/>
  </frontmatter>
  <chapter href="concept-cooling-overview.dita"/>
  <chapter href="task-replace-filter.dita"/>
  <chapter href="trouble-alarm-700016.dita"/>
  <appendix href="reference-coolant-specs.dita"/>
</bookmap>
```

---

## Subject Scheme（主题方案）

**用途**：定义和管理受控词汇

```xml
<subjectScheme>
  <subjectdef keys="hazard-severity">
    <subjectdef keys="hazard-danger"/>
    <subjectdef keys="hazard-warning"/>
    <subjectdef keys="hazard-caution"/>
    <subjectdef keys="hazard-notice"/>
  </subjectdef>
  <enumerationdef>
    <attributedef name="hazard-severity"/>
    <subjectdef keyref="hazard-danger"/>
    <subjectdef keyref="hazard-warning"/>
    <subjectdef keyref="hazard-caution"/>
    <subjectdef keyref="hazard-notice"/>
  </enumerationdef>
</subjectScheme>
```

---

## 类型选择决策树

```
需要描述背景或原理？ ──是──▶ Concept

需要逐步操作指导？ ────是──▶ Task

需要结构化规格数据？ ──是──▶ Reference

需要诊断报警/故障？ ───是──▶ Troubleshooting

需要定义术语？ ────────是──▶ Glossary Entry

需要安全警告和风险评估？─是──▶ Hazard Statement

需要列出执行前提条件？──是──▶ Task Requirements

需要培训材料？ ────────是──▶ Learning Object

需要组织出版物？ ──────是──▶ Bookmap

需要定义受控词汇？ ────是──▶ Subject Scheme
```

---

## v1.0 → v1.1 主题类型迁移路径

| 变更项 | v1.0 | v1.1 |
|--------|------|------|
| 主题类型数量 | 9 种 | 13 种 |
| Machinery Task | 使用通用 Task | 专用 Machinery Task 类型 |
| Hazard Statement | 使用 `note` 元素 | 独立 hi-d 领域主题 |
| Task Requirements | 嵌入 taskbody/prereq | 独立 pr-d 领域主题 |
| Learning Object | 不在框架中 | 新增 lc-d 领域主题 |
| Subject Scheme | 手动管理 | 集成 oXygen + Git Hook |

---

## 迁移清单

- [ ] 识别现有通用 Task 中需要转换为 Machinery Task 的主题
- [ ] 提取嵌入在 `note` 中的安全信息为独立 Hazard Statement
- [ ] 提取 `prereq` 元素为独立 Task Requirements
- [ ] 评估培训材料是否需要转换为 Learning Object
- [ ] 创建 Subject Scheme 文件并绑定属性
- [ ] 更新所有主题的 DOCTYPE 声明
- [ ] 在 CI/CD 中增加新类型验证规则
- [ ] 更新编写指南文档

---

## 关键要点

- 三层模型中**核心层占 80%**，涵盖最常见的文档场景
- **Troubleshooting** 按 `condition→cause→remedy` 结构，原因按概率排序
- **Hazard Statement** 使用四级严重性（Danger > Warning > Caution > Notice）
- **Task Requirements** 独立为主题，细化 personnel/equipment/parts/environment/safety
- 选择主题类型时使用**决策树**，避免滥用通用类型
- v1.1 新增 Machinery Task、Hazard Statement、Task Requirements、Learning Object 四种专用类型

---

## 下一步

模块 2：**内容复用策略**

学习如何使用 Conref、Keyref 和条件处理实现 35-40% 的内容复用率
