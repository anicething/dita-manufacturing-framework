---
marp: true
theme: dita-training
class: invert
paginate: true
---

# 内容复用策略

## DITA Professional — 模块 2

**Conref、Keyref 与条件处理**

---

## 学习目标

完成本模块后，您将能够：

- 理解内容复用对制造业文档的价值
- 使用 Conref 实现直接内容引用
- 使用 Keyref 实现基于键的间接引用
- 应用三种复用模式组织框架内容
- 使用 DITAVAL 文件进行条件化发布
- 创建共享内容片段并跨主题引用

---

## 内容复用的价值

| 指标 | 无复用 | 目标复用率 | 节约 |
|------|--------|------------|------|
| 文档量（万字） | 120 | 78 | **35%** |
| 更新工时（小时） | 480 | 300 | **37.5%** |
| 翻译成本（万元） | 96 | 62.4 | **35%** |
| 一致性错误率 | 12% | 3% | **75%** |
| 新文档交付周期（天） | 45 | 28 | **38%** |

> 框架目标复用率：**35-40%**，减少重复劳动，确保全系列产品文档一致性

---

## DITA 三种引用机制对比

| 特性 | Conref | Keyref | Conkeyref |
|------|--------|--------|-----------|
| 引用方式 | 直接路径 | 间接键 | 键+元素ID |
| 灵活性 | 低 | 高 | 高 |
| 可维护性 | 中 | 高 | 最高 |
| 适用场景 | 固定共享内容 | 可变内容 | 组合引用 |
| 变更影响 | 需更新引用路径 | 仅更新 map 定义 | 仅更新 map 定义 |

---

## Conref — 内容引用

**语法**：`conref="filename.dita#topicid/elementid"`

**源文件**（共享内容库）：
```xml
<!-- shared/warnings.dita -->
<concept id="shared-warnings">
  <title>共享警告</title>
  <conbody>
    <note id="warn-high-temp" type="warning">
      <p>警告：在操作前确保组件已冷却至 40°C 以下。
         接触高温表面可能导致严重烫伤。</p>
    </note>
  </conbody>
</concept>
```

---

## Conref — 引用共享内容

**目标文件**（引用方）：
```xml
<!-- tasks/replace-heater.dita -->
<task id="replace-heater">
  <title>更换加热元件</title>
  <taskbody>
    <prereq>
      <note conref="shared/warnings.dita#shared-warnings/warn-high-temp"/>
    </prereq>
    <steps>
      <step><cmd>断开电源并执行 LOTO 程序</cmd></step>
      <step><cmd>等待温度降至 40°C 以下</cmd></step>
    </steps>
  </taskbody>
</task>
```

---

## Conrefend — 可复用内容范围

**用途**：引用一段连续的范围

```xml
<!-- 源文件 shared/steps.dita -->
<steps id="common-loto-steps">
  <step id="step1"><cmd>关闭主电源开关</cmd></step>
  <step id="step2"><cmd>安装个人锁具</cmd></step>
  <step id="step3"><cmd>验证能量隔离</cmd></step>
</steps>
```

```xml
<!-- 引用方 -->
<steps>
  <step conref="shared/steps.dita#common-loto-steps/step1"
        conrefend="shared/steps.dita#common-loto-steps/step3"/>
</steps>
```

> `conrefend` 将 step1 到 step3 的全部内容一次性拉入

---

## Keyref — 基于键的间接引用

**步骤 1**：在 Ditamap 中定义键：

```xml
<map>
  <title>冷却系统手册</title>
  <keydef keys="safety-warning"
          href="shared/warnings.dita#shared-warnings/warn-high-temp"/>
  <keydef keys="loto-procedure"
          href="procedures/loto-standard.dita"/>
</map>
```

**步骤 2**：在主题中使用键引用：

```xml
<note keyref="safety-warning"/>

<p>请先完成 <xref keyref="loto-procedure">LOTO 程序</xref>。</p>
```

---

## Keyref 的优势

```
不使用 Keyref：
  100+ 个主题中硬编码
  href="shared/warnings.dita#shared-warnings/warn-high-temp"
  文件名变更 → 修改 100+ 个文件

使用 Keyref：
  100+ 个主题中写
  keyref="safety-warning"
  文件名变更 → 仅修改 1 个 ditamap 定义
```

| 场景 | Keyref 优势 |
|------|------------|
| 内容位置变更 | 只需更新 map 中的 `href` |
| 多产品变体 | 不同 map 定义不同键值 |
| 翻译管理 | 键保持不变，href 指向翻译版 |
| 条件发布 | 键根据条件指向不同内容 |

---

## 框架复用模式一：共享主题（30%）

**公司级别、跨产品共享**：

```
shared/
  ├── company-info.dita       # 公司介绍、联系方式
  ├── legal-notice.dita       # 法律声明、免责条款
  ├── safety-common.dita      # 通用安全规则
  └── glossary-common.dita    # 通用术语定义
```

| 主题 | 复用范围 | 更新频率 |
|------|----------|----------|
| 公司信息 | 全部文档 | 年/次 |
| 法律声明 | 全部文档 | 年/次 |
| 通用安全 | 全部操作手册 | 季/次 |
| 标准术语 | 全部技术文档 | 月/次 |

---

## 框架复用模式二：内容片段（20%）

**可复用的小粒度内容单元**：

```
fragments/
  ├── warnings.dita        # 警告片段集合
  ├── steps-common.dita    # 通用步骤集合
  ├── notes.dita           # 通用注释片段
  └── examples.dita        # 示例内容片段
```

```xml
<!-- fragments/notes.dita -->
<concept id="common-notes">
  <note id="torque-standard" type="important">
    <p>所有螺栓需使用扭矩扳手按规定力矩拧紧。
       参考《紧固件扭矩标准表》TB-STD-001。</p>
  </note>
</concept>
```

---

## 框架复用模式三：产品特有（50%）

**各产品独立维护的独有内容**：

```
products/
  ├── coolpro5000/
  │   ├── specs.dita
  │   └── maintenance.dita
  ├── spindlext300/
  │   ├── specs.dita
  │   └── setup.dita
  └── hydramax200/
      └── operation.dita
```

> 产品特有内容不强制复用，但鼓励提取通用部分下沉到共享层

---

## 条件处理 — 属性机制

使用 DITA 条件属性实现单一源文件多输出：

| 属性 | 用途 | 典型值 |
|------|------|--------|
| `@product` | 产品线过滤 | coolpro5000, spindlext300 |
| `@audience` | 受众过滤 | operator, technician, engineer |
| `@platform` | 平台过滤 | win, linux, embedded |
| `@props` | 通用条件 | 自定义属性值 |
| `@rev` | 版本标记 | v1.0, v1.1, v2.0 |

---

## 条件处理示例

```xml
<step>
  <cmd>启动冷却系统</cmd>
  <info>
    <p product="coolpro5000">按下控制面板上的绿色启动按钮</p>
    <p product="hydramax200">在触摸屏上点击"系统启动"图标</p>
    <p platform="embedded">通过 Modbus 发送 0x01 命令</p>
  </info>
</step>
```

> 同一主题，根据发布目标选择不同内容

---

## DITAVAL 文件 — 条件过滤规则

```xml
<val>
  <!-- 包含 coolpro5000 产品内容 -->
  <prop att="product" val="coolpro5000" action="include"/>

  <!-- 排除 spindlext300 产品内容 -->
  <prop att="product" val="spindlext300" action="exclude"/>

  <!-- technician 受众看到的内容 -->
  <prop att="audience" val="technician" action="include"/>

  <!-- operator 受众看不到的内容 -->
  <prop att="audience" val="operator" action="exclude"/>
</val>
```

---

## DITAVAL 多输出场景

```
ditaval/
  ├── product-coolpro5000.ditaval
  ├── product-spindlext300.ditaval
  ├── audience-technician.ditaval
  ├── audience-operator.ditaval
  └── full.ditaval  (全部包含，用于审查)
```

```bash
# 使用 DITAVAL 生成不同输出
dita -i manual.ditamap \
     -f html5 \
     --filter=ditaval/product-coolpro5000.ditaval \
     -o output/coolpro5000/
```

---

<!-- _class: exercise -->

## 练习：创建共享警告片段并在 2 个主题中引用

**任务**：

1. 在 `shared/warnings.dita` 中创建一个 id 为 `warn-pinch-point` 的 `<note>` 警告：关于夹点伤害风险
2. 在 `task-change-tool.dita` 中使用 `conref` 引用该警告
3. 在 `task-clean-conveyor.dita` 中使用 `conref` 引用同一警告
4. 在 ditamap 中用 `keydef` 为该警告定义一个键
5. 将其中一处的 `conref` 改为 `keyref` 实现间接引用

**预期结果**：两个主题共享同一警告内容；警告更新时只需修改源文件一处

---

## 关键要点

- **Conref** 适合固定内容的直接引用，源变更自动传播
- **Keyref** 提供间接层，适合多产品、多受众的场景
- 框架复用分三层：**共享主题 30% / 内容片段 20% / 产品特有 50%**
- **DITAVAL** 文件实现单一源 → 多输出的条件发布
- 好的复用策略能降低 **35-40%** 的维护成本和翻译成本
- `conrefend` 用于引用元素范围，适合批量步骤引用

---

## 下一步

模块 3：**Subject Scheme — 受控词汇治理**

学习使用 Subject Scheme 定义和管理属性值域，确保元数据一致性
