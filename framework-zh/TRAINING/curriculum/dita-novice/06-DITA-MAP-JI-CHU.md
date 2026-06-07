---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

## 模块 06：DITA Map 基础
### 用导航图组织你的主题世界

**DITA 课程 | 制造业文档**

---

## 学习目标

完成本模块后，你将能够：

- 解释 DITA Map (.ditamap) 是什么及其核心作用
- 理解 `<map>` 和 `<topicref>` 的层级结构
- 区分导航型、集合型、关系型三种 Map 类型
- 使用 Keys 和 keyref 机制实现间接引用
- 应用 DITAVAL 进行条件处理和内容过滤
- 为 MC-5000 CNC 加工中心创建完整的 DITA Map
- 描述主题引用与内容复用的关系

---

<!-- _class: divider -->

## 第一章：什么是 DITA Map

---

## DITA Map 的定义

**DITA Map** (`.ditamap` 文件) 是 DITA 中用来组织和管理主题的导航文件。

> DITA Map 就像一本书的"目录 + 组装说明书"——它定义了哪些主题按什么顺序、什么层级关系组合成一份完整的文档。

| 类比 | 说明 |
|------|------|
| 乐高说明书 | 告诉你有哪些积木块、怎么拼在一起 |
| 图书目录 | 定义章节的层次结构和顺序 |
| 装配清单 | 列出最终产品包含的所有组件 |

---

## DITA Map vs 书

```
传统书籍思维:
  第一章 → 第二章 → 第三章 (线性固定)

DITA Map 思维:
  主题A ─┐
  主题B ─┼─→ Map (导航图) ─→ 交付物 (.pdf / .html)
  主题C ─┘
           │
           └─→ 另一个 Map ─→ 另一个交付物
```

同一个主题可以被多个 Map 引用——这就是内容的"一次编写，多次组装"。

---

## DITA Map 文件扩展名

| 文件类型 | 扩展名 | 用途 |
|----------|--------|------|
| 标准 Map | `.ditamap` | 通用主题组织 |
| Bookmap | `.bookmap` | 书籍样式出版（含封面、版权页等） |
| 分类 Map | `.ditamap` | 主题分类和分组 |

对于制造业文档，最常用的是标准 `.ditamap` 文件。

---

## 为什么制造业需要 DITA Map

| 场景 | Map 的作用 |
|------|-----------|
| 产品文档 | 将安全、操作、维护主题组成一份手册 |
| 多型号管理 | 同型号不同配置用不同 Map 组合 |
| 培训教材 | 从主题库中选取相关主题组成课程 |
| 合规交付 | 按要求提取必要主题交给审计方 |
| 本地化管理 | 为不同语言版本创建不同的 Map |

> 示例制造的 MC-5000 CNC 加工中心包含 80+ 个主题，通过 8 个 Map 组合为操作手册、维护手册、培训教材等不同交付物。

---

<!-- _class: divider -->

## 第二章：Map 的核心结构

---

## Map 的基本骨架

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000_user_manual" xml:lang="zh-CN">
  <title>MC-5000 CNC 加工中心用户手册</title>

  <topicref href="topics/safety.dita" type="concept"/>
  <topicref href="topics/operation.dita" type="task"/>
  <topicref href="topics/specifications.dita" type="reference"/>

</map>
```

三个核心部分：
1. **XML 声明和 DOCTYPE**：声明文档类型
2. **`<map>` 根元素**：所有内容的容器
3. **`<topicref>` 元素**：指向具体主题的引用

---

## topicref 的核心属性

```xml
<topicref
  href="topics/dr5000_safety_overview.dita"
  type="concept"
  format="dita"
  scope="local"
  linking="normal"
  toc="yes"
  navtitle="安全概述"
  locktitle="yes"
/>
```

| 属性 | 说明 | 常用值 |
|------|------|--------|
| `href` | 主题文件路径 | 相对/绝对路径 |
| `type` | 主题类型 | concept / task / reference |
| `format` | 文件格式 | dita / html / pdf |
| `scope` | 引用范围 | local / peer / external |
| `toc` | 是否出现在目录中 | yes / no |
| `navtitle` | 导航标题 | 自定义显示名称 |

---

## 层级结构：嵌套 topicref

```xml
<map id="dr5000_manual">
  <title>MC-5000 用户手册</title>

  <topicref href="topics/safety_overview.dita" type="concept">
    <!-- 子主题：安全概述下的具体安全程序 -->
    <topicref href="topics/emergency_stop.dita" type="task"/>
    <topicref href="topics/electrical_safety.dita" type="concept"/>
    <topicref href="topics/coolant_safety.dita" type="task"/>
  </topicref>

  <topicref href="topics/daily_operation.dita" type="concept">
    <topicref href="topics/startup_sequence.dita" type="task"/>
    <topicref href="topics/tool_change.dita" type="task"/>
    <topicref href="topics/shutdown_sequence.dita" type="task"/>
  </topicref>

</map>
```

每个 `<topicref>` 内部可以继续嵌套更多的 `<topicref>`，形成多层级树状结构。

---

## MC-5000 文档层级示意图

```
MC-5000 用户手册 (Map)
├── 第1章：安全信息
│   ├── 1.1 安全概述 (concept)
│   ├── 1.2 紧急停机程序 (task)
│   ├── 1.3 电气安全规程 (concept)
│   └── 1.4 冷却液安全操作 (task)
├── 第2章：日常操作
│   ├── 2.1 开机流程 (task)
│   ├── 2.2 换刀操作 (task)
│   └── 2.3 关机流程 (task)
├── 第3章：维护保养
│   ├── 3.1 日常点检 (task)
│   ├── 3.2 润滑系统维护 (task)
│   └── 3.3 主轴维护 (task)
└── 附录：技术规格
    ├── A.1 机械参数 (reference)
    ├── A.2 电气参数 (reference)
    └── A.3 精度指标 (reference)
```

---

## topicmeta：为 topicref 添加元数据

```xml
<topicref href="topics/dr5000_safety.dita" type="concept">
  <topicmeta>
    <navtitle>MC-5000 安全操作指南</navtitle>
    <shortdesc>包含所有操作人员必须遵循的安全规程和注意事项。</shortdesc>
    <audience type="user" job="cnc-operator"/>
    <author type="creator">张安全</author>
    <prodinfo>
      <prodname>MC-5000</prodname>
    </prodinfo>
  </topicmeta>
</topicref>
```

`<topicmeta>` 在 Map 层面为引用添加元数据，不影响主题本身的 prolog。

---

## topichead：纯导航节点

```xml
<topichead navtitle="安全与合规">
  <topicmeta>
    <shortdesc>所有安全相关文档</shortdesc>
  </topicmeta>
  <topicref href="safety/safety_overview.dita" type="concept"/>
  <topicref href="safety/emergency_stop.dita" type="task"/>
</topichead>
```

`<topichead>` 是没有对应主题文件的导航容器——仅用于组织层级结构，不对应实际文件。

---

## topicgroup：无目录条目的分组

```xml
<topicref href="chapters/chapter1.dita">
  <topicgroup>
    <topicref href="topics/related_standard.dita" toc="no"/>
    <topicref href="topics/appendix_note.dita" toc="no"/>
  </topicgroup>
</topicref>
```

`<topicgroup>` 用于将一组 `<topicref>` 捆绑在一起，但**不在目录中产生额外的层级条目**。

---

<!-- _class: divider -->

## 第三章：三种 Map 类型

---

## Map @type 属性的三种取值

```xml
<!-- 导航型 Map -->
<map type="navigation">
  <!-- 定义阅读顺序和层级结构 -->
</map>

<!-- 集合型 Map -->
<map type="collection">
  <!-- 主题仓库，不定义阅读顺序 -->
</map>

<!-- 关系型 Map -->
<map type="relationship">
  <!-- 仅定义主题之间的关联关系 -->
</map>
```

---

## 导航型 Map (navigation)

**最常见的使用场景**——定义文档的阅读顺序和层级结构。

```xml
<map id="dr5000_user_guide" type="navigation">
  <title>MC-5000 CNC 加工中心操作指南</title>

  <topicref href="safety_overview.dita">
    <topicref href="emergency_stop.dita"/>
    <topicref href="ppe_requirements.dita"/>
  </topicref>

  <topicref href="basic_operation.dita">
    <topicref href="startup.dita"/>
    <topicref href="shutdown.dita"/>
  </topicref>
</map>
```

输出时严格按照 topicref 的顺序和层级生成文档目录。

---

## 集合型 Map (collection)

主题仓库——不定义阅读顺序，仅将相关主题归类。

```xml
<map id="dr5000_topic_library" type="collection">
  <title>MC-5000 主题库</title>

  <topichead navtitle="安全主题">
    <topicref href="safety_overview.dita"/>
    <topicref href="emergency_stop.dita"/>
    <topicref href="electrical_safety.dita"/>
    <topicref href="coolant_safety.dita"/>
  </topichead>

  <topichead navtitle="操作主题">
    <topicref href="startup.dita"/>
    <topicref href="tool_change.dita"/>
    <topicref href="part_setup.dita"/>
  </topichead>

</map>
```

用作内容管理系统中的主题目录，方便查找和复用。

---

## 关系型 Map (relationship)

定义主题之间的关联关系，但不生成层级文档。

```xml
<map id="dr5000_relationships" type="relationship">
  <title>MC-5000 主题关联</title>

  <reltable>
    <relheader>
      <relcolspec type="concept"/>
      <relcolspec type="task"/>
      <relcolspec type="reference"/>
    </relheader>
    <relrow>
      <relcell>
        <topicref href="spindle_overview.dita"/>
      </relcell>
      <relcell>
        <topicref href="spindle_maintenance.dita"/>
      </relcell>
      <relcell>
        <topicref href="spindle_specs.dita"/>
      </relcell>
    </relrow>
  </reltable>

</map>
```

关系表 (reltable) 建立了"概念-任务-参考"三类主题之间的横向关联链接。

---

## 三种 Map 类型对比

| 特性 | navigation | collection | relationship |
|------|-----------|------------|--------------|
| 定义阅读顺序 | 是 | 否 | 否 |
| 生成文档目录 | 是 | 是(按字母) | 否 |
| 主题仓库 | 否 | 是 | 否 |
| 关系表支持 | 是 | 是 | 专门用途 |
| 制造业常用度 | 最高 | 中 | 中 |

---

## 制造业三类型 Map 使用场景

| Map 类型 | MC-5000 场景 |
|----------|-------------|
| **navigation** | 为操作工生成"MC-5000 操作手册"PDF |
| **collection** | 维护所有 MC-5000 主题的中央主题库 |
| **relationship** | 关联安全概念、安全任务和安全参考主题，生成"参见"链接 |

---

<!-- _class: divider -->

## 第四章：Keys 和 Keyref 机制

---

## 什么是 Key？

**Key** 是 DITA 中间接引用的核心机制——为主题定义一个"代号"，通过代号引用，而非直接使用文件路径。

```xml
<map>
  <!-- 定义 Key -->
  <keydef keys="safety_overview"
          href="topics/dr5000_safety_overview.dita"
          type="concept"/>

  <keydef keys="emergency_stop"
          href="topics/dr5000_emergency_stop.dita"
          type="task"/>

  <!-- 通过 Key 引用 -->
  <topicref keyref="safety_overview"/>
  <topicref keyref="emergency_stop"/>
</map>
```

---

## Key 的优势

| 优势 | 说明 | 制造业场景 |
|------|------|-----------|
| **间接引用** | 修改 keydef 即可切换引用目标 | 换用不同版本的维修程序 |
| **路径无关** | 引用方不知道文件路径 | 重组目录结构无需修改 topicref |
| **变量替换** | Key 可携带文本值用于替换 | 产品名称、版本号等动态内容 |
| **条件化** | 不同 Map 定义同名 Key 指向不同主题 | 不同客户同一 Key 指向不同定制内容 |

---

## keydef 详解

```xml
<keydef keys="dr5000_safety_procedures"
        href="topics/procedures/safety_procedures.dita"
        type="concept"
        format="dita"
        scope="local"
        processing-role="resource-only"/>

<keydef keys="product_name">
  <topicmeta>
    <keywords>
      <keyword>MC-5000 CNC 加工中心</keyword>
    </keywords>
  </topicmeta>
</keydef>
```

| keydef 用法 | 说明 |
|------------|------|
| 指向主题 | href 指向 .dita 文件，用作主题引用 |
| 定义文本 | 不设 href，通过 topicmeta/keywords 定义可替换文本 |
| resource-only | processing-role 标记该 key 仅用于引用，不直接出现在目录中 |

---

## keyref 使用示例

```xml
<!-- 在 Map 中使用 keyref -->
<topicref keyref="safety_overview">
  <topicref keyref="emergency_stop"/>
  <topicref keyref="electrical_safety"/>
</topicref>
```

```xml
<!-- 在主题中使用 keyref 引用可替换文本 -->
<p>本文件适用于 <keyword keyref="product_name"/> 的所有型号。</p>
<!-- 渲染为: 本文件适用于 MC-5000 CNC 加工中心 的所有型号。 -->
```

---

## 制造业 Key 定义示例：MC-5000

```xml
<map id="dr5000_keys">
  <title>MC-5000 全局 Key 定义</title>

  <!-- 主题 Keys -->
  <keydef keys="dr5000_safety"    href="safety/safety_overview.dita"/>
  <keydef keys="dr5000_startup"   href="operation/startup.dita"/>
  <keydef keys="dr5000_specs"     href="reference/tech_specs.dita"/>
  <keydef keys="dr5000_maintain"  href="maintenance/daily_check.dita"/>

  <!-- 文本 Keys -->
  <keydef keys="product_name">
    <topicmeta><keywords>
      <keyword>示例 MC-5000 CNC 加工中心</keyword>
    </keywords></topicmeta>
  </keydef>

  <keydef keys="manufacturer">
    <topicmeta><keywords>
      <keyword>示例制造有限公司</keyword>
    </keywords></topicmeta>
  </keydef>

  <keydef keys="doc_version">
    <topicmeta><keywords>
      <keyword>v2.1</keyword>
    </keywords></topicmeta>
  </keydef>

</map>
```

---

## Key 作用域 (Scoped Keys)

```xml
<map>
  <keydef keys="product_name">
    <topicmeta><keywords><keyword>MC-5000</keyword></keywords></topicmeta>
  </keydef>

  <!-- 在子 Map 中，Key 可被重新定义 -->
  <topicref href="submaps/dr5000_variant_hs.ditamap" format="ditamap"
            keyscope="highspeed">
    <!-- 在这个子 Map 内部，product_name 可以被重定义为 "MC-5000-HS" -->
  </topicref>
</map>
```

`keyscope` 属性创建独立 Key 命名空间，允许不同子 Map 定义同名 Key 指向不同值——这对管理同一产品的不同变体非常有用。

---

<!-- _class: divider -->

## 第五章：DITAVAL 条件处理

---

## 什么是 DITAVAL

**DITAVAL** (DITA Conditional Processing Profile) 是一个独立的 XML 文件，定义内容过滤规则。

> 想想一个过滤器——按照你设定的条件，把不需要的内容"隐藏"起来，只留下目标受众相关的内容。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<val>
  <!-- 定义过滤动作：exclude 排除 / include 包含 / flag 标记 -->
  <prop action="exclude" att="audience" val="maintainer"/>
  <prop action="include" att="audience" val="operator"/>
  <prop action="flag"    att="product"  val="MC-5000-HS" color="red"/>
</val>
```

---

## DITAVAL 三种动作

| 动作 | 含义 | 示例 |
|------|------|------|
| `include` | 包含匹配的内容 | 只保留操作工相关内容 |
| `exclude` | 排除匹配的内容 | 移除维护人员专用章节 |
| `flag` | 标记匹配的内容 | 用红色边框标记高速型号特有内容 |

---

## DITAVAL 在构建时应用

```bash
# 使用 DITAVAL 过滤输出
dita --input=dr5000_manual.ditamap \
     --format=pdf \
     --filter=ditaval/operator_only.ditaval \
     --output=./output/operator_manual
```

```bash
# 不同受众使用不同的 DITAVAL
dita --input=dr5000_manual.ditamap \
     --format=pdf \
     --filter=ditaval/maintainer_only.ditaval \
     --output=./output/maintainer_manual
```

一个 Map、一套主题，通过不同的 DITAVAL 生成针对不同受众的定制化文档。

---

## 制造业 DITAVAL 示例

**操作工版 (operator_only.ditaval)**：

```xml
<val>
  <prop action="exclude" att="audience" val="maintainer"/>
  <prop action="exclude" att="audience" val="programmer"/>
  <prop action="include" att="audience" val="operator"/>
  <prop action="flag"    att="product"  val="MC-5000-HS" color="blue"/>
</val>
```

**维护人员版 (maintainer_only.ditaval)**：

```xml
<val>
  <prop action="exclude" att="audience" val="operator"/>
  <prop action="include" att="audience" val="maintainer"/>
  <prop action="include" att="audience" val="programmer"/>
  <prop action="flag"    att="otherprops" val="high-voltage"
        color="red" backcolor="yellow"/>
</val>
```

---

## DITAVAL 与 DITA Map 的配合

```xml
<map id="dr5000_manual">
  <title>MC-5000 用户手册</title>

  <!-- 所有受众都看的安全信息 -->
  <topicref href="safety_overview.dita"/>

  <!-- 仅操作工 -->
  <topicref href="daily_operation.dita"
            audience="operator"/>

  <!-- 仅维护人员 -->
  <topicref href="spindle_maintenance.dita"
            audience="maintainer"/>

  <!-- 仅 CNC 编程员 -->
  <topicref href="gcode_reference.dita"
            audience="programmer"/>

</map>
```

---

<!-- _class: divider -->

## 第六章：MC-5000 完整产品 Map 示例

---

## MC-5000 文档体系

示例制造为 MC-5000 CNC 加工中心维护以下 Map 体系：

| Map 文件 | 类型 | 用途 | 目标受众 |
|----------|------|------|---------|
| `dr5000_operator_manual.ditamap` | navigation | 操作手册 | 操作工 |
| `dr5000_maintain_manual.ditamap` | navigation | 维保手册 | 维修技师 |
| `dr5000_programming_guide.ditamap` | navigation | 编程指南 | CNC 编程员 |
| `dr5000_training.ditamap` | navigation | 培训教材 | 新员工 |
| `dr5000_topic_library.ditamap` | collection | 主题仓库 | 文档团队 |
| `dr5000_keys.ditamap` | — | Key 定义 | 全局引用 |

---

## MC-5000 操作手册 Map

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000_operator_manual" xml:lang="zh-CN">
  <title>MC-5000 CNC 加工中心操作手册</title>

  <topicref href="topics/dr5000_safety_overview.dita" type="concept">
    <topicref href="topics/dr5000_emergency_stop.dita" type="task"/>
    <topicref href="topics/dr5000_ppe_requirements.dita" type="concept"/>
  </topicref>

  <topicref href="topics/dr5000_daily_operation.dita" type="concept">
    <topicref href="topics/dr5000_startup.dita" type="task"/>
    <topicref href="topics/dr5000_tool_change.dita" type="task"/>
    <topicref href="topics/dr5000_part_setup.dita" type="task"/>
    <topicref href="topics/dr5000_shutdown.dita" type="task"/>
  </topicref>

  <topicref href="topics/dr5000_quality_check.dita" type="concept">
    <topicref href="topics/dr5000_first_article.dita" type="task"/>
    <topicref href="topics/dr5000_in_process.dita" type="task"/>
  </topicref>

  <topicref href="topics/dr5000_tech_specs.dita" type="reference"/>
  <topicref href="topics/dr5000_troubleshooting.dita" type="task"/>
</map>
```

---

## MC-5000 主题库 Map (collection)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000_topic_library" xml:lang="zh-CN" type="collection">
  <title>MC-5000 主题仓库</title>

  <topichead navtitle="01-安全 (5 主题)">
    <topicref href="safety/safety_overview.dita"/>
    <topicref href="safety/emergency_stop.dita"/>
    <topicref href="safety/electrical_safety.dita"/>
    <topicref href="safety/coolant_safety.dita"/>
    <topicref href="safety/ppe_requirements.dita"/>
  </topichead>

  <topichead navtitle="02-操作 (8 主题)">
    <topicref href="operation/startup.dita"/>
    <topicref href="operation/shutdown.dita"/>
    <topicref href="operation/tool_change.dita"/>
    <topicref href="operation/part_setup.dita"/>
    <topicref href="operation/probe_calibration.dita"/>
    <topicref href="operation/program_load.dita"/>
    <topicref href="operation/offset_setting.dita"/>
    <topicref href="operation/first_article.dita"/>
  </topichead>

  <topichead navtitle="03-维护 (6 主题)">
    <topicref href="maintenance/daily_check.dita"/>
    <topicref href="maintenance/weekly_check.dita"/>
    <topicref href="maintenance/lubrication.dita"/>
    <topicref href="maintenance/spindle_maintenance.dita"/>
    <topicref href="maintenance/coolant_system.dita"/>
    <topicref href="maintenance/chip_management.dita"/>
  </topichead>

</map>
```

---

## 带 Key 和 DITAVAL 的完整示例

```xml
<map id="dr5000_manual_production">
  <title>MC-5000 操作手册（生产版）</title>

  <!-- 引入全局 Key 定义 -->
  <topicref href="config/dr5000_keys.ditamap"
            format="ditamap"
            processing-role="resource-only"/>

  <!-- 使用 Key 引用主题 -->
  <topicref keyref="dr5000_safety">
    <topicref keyref="dr5000_emergency_stop"/>
    <topicref keyref="dr5000_ppe"/>
  </topicref>

  <topicref keyref="dr5000_operation">
    <topicref keyref="dr5000_startup"   audience="operator"/>
    <topicref keyref="dr5000_tool_change" audience="operator"/>
    <topicref keyref="dr5000_shutdown"  audience="operator"/>
  </topicref>

  <topicref keyref="dr5000_specs"/>

</map>
```

构建命令：
```bash
dita --input=dr5000_manual_production.ditamap \
     --format=pdf \
     --filter=ditaval/operator_only.ditaval \
     --output=./output/MC5000_Operator_Manual.pdf
```

---

<!-- _class: divider -->

## 第七章：Map 组织最佳实践

---

## Map 组织原则

| 原则 | 说明 |
|------|------|
| **扁平优于深嵌套** | 通常 3-4 层即可，过深影响可读性 |
| **按受众分组** | 操作工内容、维护人员内容分区域 |
| **主题独立** | 每个主题可脱离 Map 独立理解 |
| **Key 集中管理** | 所有 Key 定义在一个独立的 Map 中 |
| **Map 分层** | 根 Map 引用子 Map，模块化管理 |

---

## Map 层级化：根 Map + 子 Map

```xml
<!-- 根 Map: dr5000_master.ditamap -->
<map id="dr5000_master">
  <title>MC-5000 完整文档集</title>

  <topicref href="submaps/dr5000_safety.ditamap"
            format="ditamap"/>

  <topicref href="submaps/dr5000_operation.ditamap"
            format="ditamap"/>

  <topicref href="submaps/dr5000_maintenance.ditamap"
            format="ditamap"/>

  <topicref href="submaps/dr5000_reference.ditamap"
            format="ditamap"/>

</map>
```

每个子 Map 独立维护，互不干扰。根 Map 将它们组装为完整交付物。

---

## Map 文件目录结构建议

```
dr5000_docs/
├── master_maps/
│   ├── dr5000_operator_manual.ditamap
│   ├── dr5000_maintain_manual.ditamap
│   └── dr5000_training.ditamap
├── submaps/
│   ├── dr5000_safety.ditamap
│   ├── dr5000_operation.ditamap
│   ├── dr5000_maintenance.ditamap
│   └── dr5000_reference.ditamap
├── topics/
│   ├── safety/
│   ├── operation/
│   ├── maintenance/
│   └── reference/
├── config/
│   └── dr5000_keys.ditamap
├── ditaval/
│   ├── operator_only.ditaval
│   └── maintainer_only.ditaval
└── output/
    ├── operator_manual.pdf
    └── maintain_manual.pdf
```

---

<!-- _class: divider -->

## 第八章：动手练习

---

<!-- _class: exercise -->

## 练习 1：创建 MC-5000 安全文档 Map

**任务**：为 MC-5000 CNC 加工中心的安全文档创建一个 DITA Map。

要求：
- 使用 navigation 类型
- 包含以下主题结构（至少 3 层）：
  - 安全概述 (concept)
    - 紧急停机程序 (task)
    - 个人防护装备要求 (concept)
  - 电气安全 (concept)
    - 上锁/挂牌程序 (task)
    - 高压区域识别 (reference)
  - 冷却液安全 (task)
- 使用 `<topicmeta>` 为每个 topicref 添加简短描述

---

<!-- _class: exercise -->

## 练习 2：定义 Key 和 Keyref

**任务**：为 MC-5000 安全文档创建一个独立的 Key 定义 Map。

要求：
- 为练习 1 中的每个主题定义对应的 Key
- 定义以下文本 Key：
  - `product_name` → "示例 MC-5000 CNC 加工中心"
  - `manufacturer` → "示例制造有限公司"
  - `safety_dept` → "安全工程部"
- 使用 `processing-role="resource-only"` 标记 Key 定义 Map

---

<!-- _class: exercise -->

## 练习 3：创建 DITAVAL 条件文件

**任务**：为 MC-5000 安全文档创建两个不同的 DITAVAL。

**操作工版** (`operator_safety.ditaval`)：
- 包含 audience="operator" 的内容
- 排除 audience="maintainer" 的内容
- 用蓝色标记安全等级为 "critical" 的内容

**维护人员版** (`maintainer_safety.ditaval`)：
- 包含 audience="maintainer" 的内容
- 排除 audience="operator" 的内容
- 用红色标记涉及高压 (high-voltage) 的内容

---

<!-- _class: exercise -->

## 练习 4：Map 层级化设计

**任务**：将 MC-5000 完整文档拆分为根 Map + 子 Map 结构。

要求：
- 创建根 Map `dr5000_master.ditamap`
- 创建 4 个子 Map（safety, operation, maintenance, reference）
- 在根 Map 中通过 `<topicref format="ditamap">` 引用子 Map
- 设计合理的目录结构

---

## 练习 1 参考答案

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000_safety_docs" xml:lang="zh-CN" type="navigation">
  <title>MC-5000 CNC 加工中心安全文档</title>

  <topicref href="topics/safety/safety_overview.dita" type="concept">
    <topicmeta>
      <shortdesc>MC-5000 安全操作的核心原则和整体框架</shortdesc>
    </topicmeta>
    <topicref href="topics/safety/emergency_stop.dita" type="task">
      <topicmeta>
        <shortdesc>在所有紧急情况下的停机操作步骤</shortdesc>
      </topicmeta>
    </topicref>
    <topicref href="topics/safety/ppe_requirements.dita" type="concept">
      <topicmeta>
        <shortdesc>操作 MC-5000 必须佩戴的个人防护装备清单</shortdesc>
      </topicmeta>
    </topicref>
  </topicref>

  <topicref href="topics/safety/electrical_safety.dita" type="concept">
    <topicmeta>
      <shortdesc>MC-5000 电气系统的安全操作原则</shortdesc>
    </topicmeta>
    <topicref href="topics/safety/loto_procedure.dita" type="task">
      <topicmeta>
        <shortdesc>上锁/挂牌的标准操作流程</shortdesc>
      </topicmeta>
    </topicref>
    <topicref href="topics/safety/high_voltage_zones.dita" type="reference">
      <topicmeta>
        <shortdesc>配电柜和高压组件的识别与位置图</shortdesc>
      </topicmeta>
    </topicref>
  </topicref>

  <topicref href="topics/safety/coolant_safety.dita" type="task">
    <topicmeta>
      <shortdesc>冷却液的加注、更换和泄漏处理安全程序</shortdesc>
    </topicmeta>
  </topicref>

</map>
```

---

## 练习 2 参考答案

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000_safety_keys" xml:lang="zh-CN"
     processing-role="resource-only">
  <title>MC-5000 安全文档 Key 定义</title>

  <keydef keys="safety_overview"
          href="topics/safety/safety_overview.dita" type="concept"/>
  <keydef keys="emergency_stop"
          href="topics/safety/emergency_stop.dita" type="task"/>
  <keydef keys="ppe_requirements"
          href="topics/safety/ppe_requirements.dita" type="concept"/>
  <keydef keys="electrical_safety"
          href="topics/safety/electrical_safety.dita" type="concept"/>
  <keydef keys="loto_procedure"
          href="topics/safety/loto_procedure.dita" type="task"/>
  <keydef keys="high_voltage_zones"
          href="topics/safety/high_voltage_zones.dita" type="reference"/>
  <keydef keys="coolant_safety"
          href="topics/safety/coolant_safety.dita" type="task"/>

  <keydef keys="product_name">
    <topicmeta><keywords>
      <keyword>示例 MC-5000 CNC 加工中心</keyword>
    </keywords></topicmeta>
  </keydef>
  <keydef keys="manufacturer">
    <topicmeta><keywords>
      <keyword>示例制造有限公司</keyword>
    </keywords></topicmeta>
  </keydef>
  <keydef keys="safety_dept">
    <topicmeta><keywords>
      <keyword>安全工程部</keyword>
    </keywords></topicmeta>
  </keydef>

</map>
```

---

## 练习 3 参考答案

**operator_safety.ditaval**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<val>
  <prop action="include" att="audience" val="operator"/>
  <prop action="exclude" att="audience" val="maintainer"/>
  <prop action="exclude" att="audience" val="programmer"/>
  <prop action="flag" att="otherprops" val="critical"
        color="blue" backcolor="#E8F0FE"/>
</val>
```

**maintainer_safety.ditaval**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<val>
  <prop action="include" att="audience" val="maintainer"/>
  <prop action="exclude" att="audience" val="operator"/>
  <prop action="flag" att="otherprops" val="high-voltage"
        color="red" backcolor="#FFEBEE"/>
</val>
```

---

<!-- _class: divider -->

## 第九章：总结与测验

---

<!-- _class: keypoint -->

## 本章要点

1. DITA Map (.ditamap) 是主题的导航和组装工具
2. `<map>` 根元素 + `<topicref>` 层级 = 完整的文档结构
3. 三种 Map 类型：navigation（导航）、collection（集合）、relationship（关系）
4. Key/keyref 实现间接引用 — 路径变更不影响引用方
5. DITAVAL 实现条件过滤 — 一套源内容，多版本交付
6. Map 层级化（根 Map + 子 Map）管理大型文档集
7. MC-5000 通过 6 个 Map + DITAVAL 覆盖 3 种受众

---

## 自我测验

1. DITA Map 的文件扩展名是什么？
2. `<topicref>` 的 `type` 属性可以取哪些值？
3. navigation 和 collection 两种 Map 类型的核心区别是什么？
4. Key 比直接 href 引用有什么优势？（至少列出 2 条）
5. DITAVAL 的三种动作 (action) 分别是什么？
6. `<topichead>` 和 `<topicref>` 的区别是什么？
7. 如何在一个根 Map 中引用另一个 Map？

---

## 测验答案

1. `.ditamap`
2. `concept`、`task`、`reference`（以及其他专用化类型）
3. navigation 定义阅读顺序和层级，collection 仅归类不定义顺序
4. 间接引用（路径解耦）、变量替换、条件化、易于维护
5. `include`（包含）、`exclude`（排除）、`flag`（标记）
6. `<topichead>` 无对应主题文件，仅作导航容器；`<topicref>` 指向实际文件
7. `<topicref href="submap.ditamap" format="ditamap"/>`

---

## 下节预告

下一课我们将学习框架的审批工作流程——如何通过 10 步审批流、Git 分支策略和 Pull Request 评审确保文档质量。

---

<!-- _class: title -->

## 谢谢
### 下一课：框架审批流程

**DITA 课程 | 制造业文档**
