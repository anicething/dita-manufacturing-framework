# 变量管理 —— 基于 Key 的内容复用策略

## 1. 目的

本文定义了一套策略，用于管理跨所有 DITA 文档的**变量内容**（公司名称、产品名称、法律声明、版本号）。目标是确保一次变更即可传播到所有主题，无需手动搜索替换。

---

## 2. 问题

硬编码变量内容会造成严重的维护灾难：

```xml
<p>欢迎阅读示例制造公司的 MC-5000 文档。</p>
```

如果公司名称变更，每个包含它的主题都必须编辑。在一个拥有 10,000 个主题的制造业文档集中，这是一个灾难。

---

## 3. 解决方案：三层变量策略

DITA 提供了三种互补的变量管理机制，按优先级顺序使用：

| 优先级 | 机制 | 适用场景 | 变更影响面 |
|--------|------|---------|-----------|
| **1** | Keys + keyref | 主题正文内容、prolog 元数据 | 修改 ditamap 中 1 处 |
| **2** | Conkeyref（内容引用） | 较长的可重用文本块（法律、版权） | 修改源主题中 1 处 |
| **3** | XML 实体 (`.ent`) | DTD 级宏、属性默认值 | 修改 .ent 文件中 1 处 |

---

## 4. 机制 1：Keys + keyref（首选方案）

### 4.1 在框架级 Map 中定义 Key

创建集中式 key 定义 map：

```xml
<!-- references/framework-keys.ditamap -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="framework-keys">
  <title>框架全局 Key 定义</title>

  <!-- === 公司信息 === -->
  <keydef keys="company-name">
    <topicmeta>
      <keywords><keyword>示例制造有限公司</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="company-abbr">
    <topicmeta>
      <keywords><keyword>ECM</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="company-short">
    <topicmeta>
      <keywords><keyword>示例制造</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- === 产品线 === -->
  <keydef keys="product-line">
    <topicmeta>
      <keywords><keyword>MC 系列加工中心</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="product-flagship">
    <topicmeta>
      <keywords><keyword>MC-5000 五轴加工中心</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- === 标准 === -->
  <keydef keys="standard-iso12100">
    <topicmeta>
      <keywords><keyword>ISO 12100:2010</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="standard-iso9001">
    <topicmeta>
      <keywords><keyword>ISO 9001:2015</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- === 版本信息 === -->
  <keydef keys="copyright-year">
    <topicmeta>
      <keywords><keyword>2026</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="doc-version">
    <topicmeta>
      <keywords><keyword>v1.1</keyword></keywords>
    </topicmeta>
  </keydef>
</map>
```

### 4.2 在主题内容中引用 Key

在主题正文中：

```xml
<p>欢迎阅读 <keyword keyref="company-name"/> 的
<keyword keyref="product-flagship"/> 文档。</p>
```

输出：
> 欢迎阅读示例制造有限公司的 MC-5000 五轴加工中心文档。

### 4.3 在元数据中引用 Key

```xml
<prolog>
  <metadata>
    <publisher>
      <keyword keyref="company-name"/>
    </publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>
        <keyword keyref="company-name"/>
      </copyrholder>
    </copyright>
  </metadata>
</prolog>
```

### 4.4 Key 作用域与覆盖

Key 遵循 DITA 的作用域规则。产品级 map 可以覆盖框架 key：

```xml
<!-- products/mc5000/mc5000-map.ditamap -->
<map id="mc5000-map">
  <!-- 引入框架 key -->
  <topicref href="../references/framework-keys.ditamap"
            processing-role="resource-only"/>

  <!-- 覆盖产品专用 key -->
  <keydef keys="product-flagship">
    <topicmeta>
      <keywords><keyword>MC-5000 五轴加工中心</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- 主题引用 -->
  <topicref href="topics/overview.dita"/>
</map>
```

---

## 5. 机制 2：Conkeyref（内容引用）

当可重用内容比关键词更长时（段落、多行法律文本、样板章节），使用 conkeyref。

### 5.1 定义可重用内容

```xml
<!-- references/legal-statements.dita -->
<reference id="legal-statements">
  <title>法律声明</title>
  <refbody>
    <section id="copyright">
      <title>版权声明</title>
      <p id="copyright-zh">版权所有 © 2026 示例制造有限公司。保留所有权利。
未经事先书面许可，不得复制本文档的任何部分。</p>
    </section>
    <section id="disclaimer">
      <title>免责声明</title>
      <p id="disclaimer-zh">本文档中的信息如有变更，恕不另行通知。
示例制造不对本文档信息的准确性作任何形式的保证。</p>
    </section>
  </refbody>
</reference>
```

### 5.2 在 Map 中绑定

```xml
<keydef keys="legal" href="../references/legal-statements.dita"/>
```

### 5.3 从主题中引用

```xml
<p conkeyref="legal/copyright-zh"/>
```

---

## 6. 机制 3：XML 实体（传统 / DTD 级）

### 6.1 实体定义文件

```xml
<!-- references/variables.ent -->
<!ENTITY company-name    "示例制造有限公司">
<!ENTITY company-abbr    "ECM">
<!ENTITY product-flagship "MC-5000 五轴加工中心">
```

### 6.2 在主题中使用实体

```xml
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd"
  [<!ENTITY % vars SYSTEM "../references/variables.ent">
   %vars;
  ]>
<concept id="overview">
  <title>&product-flagship; 概述</title>
</concept>
```

> **限制：** 实体不支持 DITA 的 key 作用域覆盖。推荐优先使用 keyref。

---

## 7. 变量变更流程

当变量发生变化时，遵循以下流程：

| 场景 | 需修改的位置 | 工作量 |
|------|------------|--------|
| 公司名称变更 | 更新 `framework-keys.ditamap` 中的 key 定义 | **5 分钟** |
| 产品品牌变更 | 更新 key 定义；或者在产品级覆盖 | **15 分钟** |
| 版权年份更新 | 更新 `copyright-year` key 值 | **1 分钟** |
| 法律声明更新 | 编辑 `legal-statements.dita` 源文件 | **30 分钟** |
| ISO 标准引用更新 | 更新 key 值或 conref 源 | **10 分钟** |
| 版本号提升 | 更新 `doc-version` key 值 | **1 分钟** |

---

## 8. 变量目录结构

```
references/
├── framework-keys.ditamap        ← 集中式 Key 定义（所有产品共用）
├── legal-statements.dita         ← 版权、免责声明、商标
├── variables.ent                 ← 传统实体定义
├── en/
│   ├── company-variables.dita    ← 公司信息主题（英文）
│   └── standard-refs.dita        ← ISO/标准引用（英文）
└── zh-CN/
    ├── company-variables.dita    ← 公司信息主题（中文）
    └── standard-refs.dita        ← ISO/标准引用（中文）
```

---

## 9. 与模板的集成

每个 DITA 模板应使用基于 key 的引用，而非硬编码占位符：

| 模板元素 | 使用 Key/引用 | 替代方式 |
|---------|--------------|---------|
| 发布者名称 | `<keyword keyref="company-name"/>` | 硬编码公司名 |
| 产品名称 | `<keyword keyref="product-flagship"/>` | 硬编码产品名 |
| 版权声明 | `<p conkeyref="legal/copyright-zh"/>` | 硬编码文本 |
| ISO 标准 | `<keyword keyref="standard-iso12100"/>` | 硬编码版本号 |
| 文档版本 | `<keyword keyref="doc-version"/>` | 硬编码版本号 |

---

## 10. 治理规则

| 规则 | 说明 | 执行方式 |
|------|------|---------|
| M-V1 | 主题正文和标题中不得硬编码公司名称 | Schematron 检查 |
| M-V2 | 所有产品名称必须使用 keyref | 代码评审 |
| M-V3 | 法律文本必须使用 conkeyref 引用中心源 | Schematron 检查 |
| M-V4 | 框架 key 不得被覆盖为空值 | CI/CD 验证 |
| M-V5 | 新变量必须在此文件中记录 | 架构评审 |

---

## 相关文档

- [01-CONTENT-ARCHITECTURE.md](01-CONTENT-ARCHITECTURE.md) — 整体架构
- [02-METADATA-SCHEMA.md](02-METADATA-SCHEMA.md) — M1-M7 元数据规则
- [05-SUBJECT-SCHEME-GUIDE.md](05-SUBJECT-SCHEME-GUIDE.md) — 受控词表
- [Templates README](../templates/README.md) — 在模板中使用变量
