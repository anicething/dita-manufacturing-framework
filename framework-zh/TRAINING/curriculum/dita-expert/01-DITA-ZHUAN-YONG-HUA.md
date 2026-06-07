---
marp: true
theme: dita-training
class: invert
paginate: true
---

# DITA 专用化详解

## 制造业领域的自定义设计

**DITA 专家级培训 · 模块 1**

---

## 学习目标

完成本模块后，您将能够：

- 清晰解释 DITA 专用化的概念和必要性
- 区分领域专用化和主题类型专用化
- 理解 `@class` 属性作为专用化核心机制
- 设计和实现制造业特定领域
- 使用 DTD shell 集成自定义模块
- 排查常见专用化错误

---

## 什么是 DITA 专用化

DITA 专用化（Specialization）是**扩展基础主题类型以创建领域特定信息模型**的正式机制。

```
基础层            topic (通用主题)
                    |
中间层    concept   task   reference
          |         |         |
专用层  故障排除   机器操作  物料清单
       troubleshooting machine-op BOM
```

专用化不是"扩展"（extension），而是**约束与细化**——新元素必须是已有元素的语义子集。

---

## 为什么需要专用化

| 目标 | 痛点 | 专用化方案 |
|------|------|-----------|
| **语义标记** | 通用 `<ph>` 无法区分电压单位与温度单位 | 创建 `<electricalValue>`、`<thermalReading>` |
| **自动验证** | 机器操作步骤缺少必需的安全警告 | 强制 `<safetyNote>` 在每个 `<taskbody>` 中 |
| **自定义处理** | PDF 中所有警告一样，无法区分严重级别 | `<danger>` 红色框，`<caution>` 黄色框 |
| **行业标准** | 符合 ISO 12100（机械安全）需要特定标记 | 实现 ISO 标准元素到 DITA 的映射 |
| **向导式创作** | 写作者不确定哪些字段必填 | 专用化强制必填字段，引导式创作 |

---

## 专用化层级：从基础到制造业

```
           topic
        /    |     \
   concept  task  reference
     |        |        |  
     |   machine-task  material-ref
     |        |
 troubleshooting
      |
machine-troubleshooting
```

**关键规则**：每个专用化类型必须能在其基础类型中"安全降级"。

---

## 制造业专用化示例：机器维护任务

<!-- _class: exercise -->

从 `task` 专用化的 `machine-maintenance`:

```xml
<!-- base DTD: task -->
<!ELEMENT machine-maintenance    ((%title;), (%titlealts;)?, 
                                   (%shortdesc;)?, (%prolog;)?,
                                   (%machine-maintenance-body;),
                                   (%related-links;)?,
                                   (%task-info-types;)* )>
<!ATTLIST machine-maintenance       id ID #REQUIRED
                                    %conref-atts;
                                    %select-atts;
                                    %localization-atts;
                                    outputclass CDATA #IMPLIED
>
```

---

## 领域专用化 vs 主题类型专用化

| 特性 | 主题类型专用化 | 领域专用化 |
|------|---------------|-----------|
| **作用范围** | 定义新主题结构 | 添加可复用元素 |
| **使用方式** | 独立主题文档 | 嵌入任何主题类型 |
| **示例** | `<machine-task>` | `<torqueSpec>`（可出现在 task、reference 中） |
| **声明位置** | `.dtd` shell | `.ent` + `.mod` 文件 |
| **继承基础** | 基础主题类型 | 不需要完整主题结构 |
| **集成复杂度** | 高 | 中 |

---

## 领域专用化：制造业维护领域示例

创建 `<machineMaintenance>` 领域，包含以下新元素：

| 元素 | 用途 | 继承自 |
|------|------|--------|
| `<maintenanceInterval>` | 维护周期 | `<data>` |
| `<lubricationPoint>` | 润滑点描述 | `<sectiondiv>` |
| `<torqueSpec>` | 扭矩规格 | `<data>` |
| `<wearIndicator>` | 磨损指标 | `<ph>` |
| `<sparePart>` | 备件信息 | `<data>` |
| `<safetyInterlock>` | 安全联锁装置 | `<note>` |

---

## 领域模块文件结构（.mod）

`machine-maintenance-domain.mod`:

```xml
<!-- ============================================================= -->
<!-- MODULE:    Machine Maintenance Domain                          -->
<!-- VERSION:   1.0                                                 -->
<!-- DATE:      2025-01                                             -->
<!-- ============================================================= -->

<!-- ================== Domain Element Declarations ============== -->

<!ENTITY % maintenanceInterval
  "maintenanceInterval"
>
<!ENTITY % lubricationPoint
  "lubricationPoint"
>
<!ENTITY % torqueSpec
  "torqueSpec"
>
```

---

## 领域元素声明（续）

```xml
<!-- ================== Element Definitions ===================== -->

<!ENTITY % maintenanceInterval.content
  "(#PCDATA |
    %data.elements.incl; |
    %foreign.unknown.incl;)*"
>
<!ENTITY % maintenanceInterval.attributes
  "%univ-atts;
   name     CDATA #REQUIRED
   value    CDATA #REQUIRED
   interval-unit (hours|days|weeks|months|cycles) #REQUIRED
   outputclass CDATA #IMPLIED"
>
<!ELEMENT maintenanceInterval %maintenanceInterval.content;>
<!ATTLIST maintenanceInterval %maintenanceInterval.attributes;>
```

---

## @class 属性：专用化的核心

`@class` 属性是 DITA 专用化的**基石机制**。它记录了每个元素的完整继承链。

```xml
<!-- 通用段落 -->
<p class="- topic/p ">这是一个普通段落</p>

<!-- 专用化的注意事项 -->
<note class="- topic/note ">这是通用注意事项</note>

<!-- 制造业专用化的润滑点 -->
<lubricationPoint class="+ topic/sectiondiv mining-d/lubricationPoint ">
   <ul>
     <li>轴承座A：每500小时加注锂基润滑脂</li>
     <li>齿轮箱B：每1000小时检查油位</li>
   </ul>
</lubricationPoint>
```

---

## @class 属性语法规则

```
@class = "-" 或 "+" [空格分隔的继承链]

"- topic/p "                    → 直接是 topic 的段落
"- topic/note "                 → 直接是 topic 的注意事项  
"+ topic/note mydomain/safety-note "  → 从 note 专用化
```

| 前缀 | 含义 | 处理行为 |
|------|------|---------|
| `-` | 从指定模块直接使用（起始） | 泛化时从该点开始 |
| `+` | 从父级专用化 | 泛化时回退到父级 |

**关键规则**：专用化必须保持 class 层级结构不变，只能追加，不能删除或重排。

---

## class 属性在制造业中的传递

```xml
<!-- 基础层级 -->
<data class="- topic/data ">通用数据</data>

<!-- 一级专用化：维护周期 -->
<maintenanceInterval class="+ topic/data mfg-d/maintenanceInterval ">
  每800小时
</maintenanceInterval>

<!-- 二级专用化：润滑周期（进一步专用化） -->
<lubricationInterval class="+ topic/data mfg-d/maintenanceInterval 
                              mfg-d/lubricationInterval ">
  每200小时（高速轴承）
</lubricationInterval>
```

处理工具通过 `@class` 知道：
- `lubricationInterval` 是一个 `maintenanceInterval`
- `maintenanceInterval` 是一个 `data`
- 因此可以按 `data` 或 `maintenanceInterval` 统一处理

---

## 完整的制造业领域定义

`machine-maintenance-domain.ent`（实体声明文件）：

```xml
<!-- ============================================================= -->
<!-- ENTITY:   Machine Maintenance Domain Entities                  -->
<!-- ============================================================= -->

<!-- Domain entity: signals domain availability -->
<!ENTITY % machine-maintenance-d-dec
  PUBLIC "-//ACME//ENTITIES DITA Maintenance Domain//EN"
         "machine-maintenance-domain.ent"
>
%machine-maintenance-d-dec;

<!-- Parameter entity for domain integration -->
<!ENTITY % machine-maintenance-d-def
  "../../domains/machine-maintenance-domain.mod"
>
```

---

## DTD Shell 集成

`machine-task.dtd`（DTD Shell）：

```xml
<!-- ============================================================= -->
<!-- DTD:      Machine Task Shell                                   -->
<!-- ============================================================= -->

<!-- ============ Base Type Integration =============== -->
<!ENTITY % task-typemod
  PUBLIC "-//OASIS//ELEMENTS DITA 1.3 Task//EN"
         "../../base/dtd/task.mod"
>
%task-typemod;

<!-- ============ Domain Integration ================== -->
<!ENTITY % hi-d-def
  PUBLIC "-//OASIS//ELEMENTS DITA 1.3 Highlight Domain//EN"
         "../../base/dtd/highlightDomain.mod"
>
%hi-d-def;

<!ENTITY % machine-maintenance-d-def
  PUBLIC "-//ACME//ELEMENTS DITA Maintenance Domain//EN"
         "../../domains/machine-maintenance-domain.mod"
>
%machine-maintenance-d-def;
```

---

## DTD Shell 集成（续）：域属性扩展

```xml
<!-- ========== Domain Extension for taskbody ========== -->

<!ENTITY % taskbody.content
  "((%prereq;)?,
    (%context;)?,
    (%section; |
     %steps; |
     %steps-unordered; |
     %steps-informal; |
     %lubricationPoint; |
     %maintenanceInterval; |
     %torqueSpec; |
     %wearIndicator;)*,
    (%result;)?,
    (%example;)?,
    (%postreq;)?)"
>

<!ENTITY % taskbody.attributes
  "%id-atts;
   %localization-atts;
   base        CDATA #IMPLIED
   %base-attribute-extensions;
   outputclass CDATA #IMPLIED"
>
```

---

## Catalog.xml：PUBLIC/SYSTEM ID 映射

```xml
<?xml version="1.0" encoding="UTF-8"?>
<catalog prefer="public"
         xmlns="urn:oasis:names:tc:entity:xmlns:xml:catalog">

  <!-- DITA OT base catalog -->
  <nextCatalog catalog="../../plugins/org.oasis-open.dita.v1_3/catalog.xml"/>

  <!-- Machine Maintenance Domain -->
  <public publicId="-//ACME//ENTITIES DITA Maintenance Domain//EN"
          uri="domains/machine-maintenance-domain.ent"/>
  <public publicId="-//ACME//ELEMENTS DITA Maintenance Domain//EN"
          uri="domains/machine-maintenance-domain.mod"/>

  <!-- Machine Task Shell -->
  <public publicId="-//ACME//DTD DITA Machine Task//EN"
          uri="dtd/machine-task.dtd"/>

</catalog>
```

---

## 制造业完整文档类型映射

```
文档类型              DTD Shell              基础类型      包含领域
--------------------------------------------------------------------
machine-task          machine-task.dtd         task          maintenance, hazard
machine-concept       machine-concept.dtd      concept       maintenance
machine-reference     machine-reference.dtd    reference     maintenance, equipment
BOM-topic             bom-topic.dtd            topic         materials, compliance
troubleshooting-guide trouble-guide.dtd        concept       maintenance, diagnostics
safety-procedure      safety-procedure.dtd     task          hazard-statement, ppe
```

---

## 专用化识别：domainContribution

```xml
<!-- 在主题 prolog 中声明使用的领域，用于处理工具自动识别 -->
<prolog>
  <metadata>
    <othermeta name="domains" 
               content="(topic task) 
                        (topic hi-d) 
                        (topic maintenance-d)"/>
  </metadata>
</prolog>
```

DITA-OT 通过 `@domains` 属性识别适用的领域模块并相应地应用处理。

---

## 常见陷阱 1：错误的 class 属性

```xml
<!-- 错误：缺少起始的 "-" 前缀 -->
<torqueSpec class="topic/data mfg-d/torqueSpec ">

<!-- 错误：颠倒了层级顺序 -->
<lubricationInterval class="+ topic/data mfg-d/lubricationInterval 
                                 mfg-d/maintenanceInterval ">

<!-- 正确写法 -->
<lubricationInterval class="+ topic/data mfg-d/maintenanceInterval 
                                 mfg-d/lubricationInterval ">
```

**检查清单**：
- [ ] 第一个模块以 `- ` 开头
- [ ] 后续模块以 `+ ` 开头
- [ ] 层级从通用到专用
- [ ] 元素名在 DTD 中声明

---

## 常见陷阱 2：缺少模块引用

```xml
<!-- 文档引用 machine-task.dtd -->
<!DOCTYPE machine-task PUBLIC "-//ACME//DTD DITA Machine Task//EN"
  "machine-task.dtd">

<!-- machine-task.dtd 中忘记包含维护领域 -->

<!-- 错误现象： -->
<!-- DTD 解析错误：Element 'lubricationPoint' is not declared -->

<!-- 修复：在 machine-task.dtd 中添加 -->
<!ENTITY % machine-maintenance-d-def
  PUBLIC "-//ACME//ELEMENTS DITA Maintenance Domain//EN"
         "../../domains/machine-maintenance-domain.mod"
>
%machine-maintenance-d-def;
```

---

## 常见陷阱 3：内容模型冲突

```xml
<!-- 专用化不允许扩展内容模型 -->
<!-- 基础 topic 的 <body> 不能新增子元素类型 -->

<!-- 错误示例：直接修改 body 内容模型添加新元素 -->
<!ENTITY % body.content
  "((%bodydiv; | %section; | %p; | %machineWarning;)*)"
>
<!-- 这在 DTD 规范层面不冲突，但破坏了 DITA 专用化规则 -->

<!-- 正确做法：在 taskbody 或 sectiondiv 层面扩展 -->
<!ENTITY % taskbody.content
  "((%prereq;)?, (%context;)?, ..., (%machineWarning;)*, ...)"
>
```

---

## 工具支持

| 工具 | 功能 | 适用场景 |
|------|------|---------|
| **oXygen XML Editor** | 专用化编辑器、DTD 可视化 | 专用化开发与调试 |
| **DITA-OT** | 专用化插件支持 | 构建和发布 |
| **XMetal** | 专用化向导 | 快速原型 |
| **DITA For Small Teams** | 轻量级专用化工具 | 小型团队 |
| **Schematron** | 业务规则验证 | 专用化规则强制 |

---

## DITA-OT 专用化插件结构

```
com.acme.machine-maintenance/
    plugin.xml                  ← 插件描述
    catalog.xml                 ← 目录映射
    dtd/
        machine-task.dtd        ← DTD shell
    domains/
        machine-maintenance-domain.mod     ← 元素定义
        machine-maintenance-domain.ent     ← 实体声明
    xsl/
        dita2htmlshell.xsl      ← XSL 覆盖
    css/
        maintenance.css         ← 自定义样式
```

---

## plugin.xml 配置

```xml
<plugin id="com.acme.machine-maintenance">
  <require plugin="org.dita.base"/>
  <feature extension="dita.specialization.catalog.relative" 
           file="catalog.xml"/>
  <feature extension="dita.xsl.html5" 
           file="xsl/dita2htmlshell.xsl"/>
  <feature extension="dita.conductor.transtype.check" 
           value="machine-task"/>
</plugin>
```

---

## 练习：创建制造业维护领域专用化

<!-- _class: exercise -->

**任务**：为 CNC 加工中心创建一个简单的维护领域专用化

1. 定义新元素 `<spindleCheck>`（主轴检查），继承自 `<data>`
2. 添加属性 `rpm`(CDATA)、`temperature-max`(CDATA)、`vibration-limit`(CDATA)
3. 创建 `.mod` 和 `.ent` 文件
4. 在测试主题中使用该元素

---

## 练习答案：spindleCheck 元素定义

```xml
<!-- spindleCheck 内容模型 -->
<!ENTITY % spindleCheck.content
  "(#PCDATA |
    %data.elements.incl;)*"
>

<!ENTITY % spindleCheck.attributes
  "%univ-atts;
   rpm           CDATA #IMPLIED
   temperature-max CDATA #IMPLIED
   vibration-limit CDATA #IMPLIED
   outputclass   CDATA #IMPLIED"
>

<!ELEMENT spindleCheck %spindleCheck.content;>
<!ATTLIST spindleCheck %spindleCheck.attributes;>

<!ATTLIST spindleCheck %global-atts;
  class CDATA "+ topic/data mfg-cnc-d/spindleCheck ">
```

---

## 练习答案：在主题中使用

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE task PUBLIC "-//ACME//DTD DITA CNC Machine Task//EN"
  "cnc-machine-task.dtd">
<task id="cnc-maintenance-daily">
  <title>CNC 加工中心日常维护</title>
  <taskbody>
    <context>
      <p>每日开机前执行以下检查：</p>
    </context>
    <steps>
      <step>
        <cmd>检查主轴状态</cmd>
        <info>
          <spindleCheck rpm="12000"
                        temperature-max="65"
                        vibration-limit="2.5">
            主轴空载运转，温度不应超过65°C，
            振动幅度不超过2.5 mm/s
          </spindleCheck>
        </info>
      </step>
    </steps>
  </taskbody>
</task>
```

---

## 泛化（Generalization）：反向过程

专用化内容的泛化允许在只支持基础 DITA 的工具中处理内容。

```
<lubricationPoint class="+ topic/sectiondiv mfg-d/lubricationPoint ">
  → 泛化 →
<sectiondiv class="- topic/sectiondiv ">
```

**泛化用途**：
- 与不支持专用化的工具交换
- 将内容迁移到新的 DITA 版本
- 在内容管理系统中统一索引

---

## 专用化设计原则总结

1. **语义一致性**：专用化元素必须是其基元素的语义子集
2. **结构约束**：不能扩展基元素的内容模型
3. **class 完整**：class 属性必须包含完整继承链
4. **可泛化**：任何专用化内容必须能安全泛化回基础类型
5. **命名空间清晰**：领域名称应唯一标识（如 `mfg-maintenance-d`）
6. **文档化**：每个专用化领域必须有完整的元素说明和使用指南

---

## 关键要点

<!-- _class: key-takeaways -->

- DITA 专用化是**约束与细化**，不是随意扩展
- `@class` 属性是专用化核心，记录了完整的元素继承链
- 领域专用化为任何主题类型添加可复用元素；主题类型专用化创建新的完整文档类型
- 制造业场景优先考虑**领域专用化**（如维护、安全、计量领域）
- DTD Shell 中将所有模块组装成一个可用的文档类型
- Catalog.xml 确保 PUBLIC/SYSTEM ID 正确映射
- 每个专用化必须保证**可泛化性**——能回退到基础类型
