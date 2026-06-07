---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->
# 模块 03：DITA XML 基础知识
## 从零掌握 DITA 的 XML 语法核心
### 编写格式正规、结构有效的 DITA 主题

---

# 学习目标

完成本模块后，你将能够：

- 理解 XML 的基本概念：元素、属性、嵌套规则
- 区分 **格式正规** (well-formed) 和 **有效** (valid) 的 XML
- 正确编写 DOCTYPE 声明
- 熟练使用 DITA 常用元素
- 避免 DITA XML 十大常见错误
- 使用 oXygen 进行 DTD 验证

---

# XML 是什么？

**eXtensible Markup Language** — 可扩展标记语言

XML 是 DITA 的基础语言。它是一种用标签描述数据的文本格式。

```xml
<!-- XML 基本结构 -->
<element attribute="value">
  文本内容
</element>
```

三个核心概念：
- **元素 (Element)**：`<tag>内容</tag>` — 信息的基本单元
- **属性 (Attribute)**：`<tag attr="value">` — 元素的附加信息
- **嵌套 (Nesting)**：元素可以包含其他元素，形成树形结构

---

# XML 元素详解

## 元素的结构

```xml
<task id="task_replace_tool">
  <title>更换刀具</title>
  <shortdesc>标准刀具更换流程。</shortdesc>
</task>
```

- **开始标签**：`<task id="task_replace_tool">`
- **内容**：包含子元素 `<title>` 和 `<shortdesc>`
- **结束标签**：`</task>`

> XML 元素名称区分大小写。`<Topic>` 和 `<topic>` 是不同的元素！

---

# XML 属性详解

## 属性提供元素的附加信息

```xml
<topic id="safety_001" xml:lang="zh-CN" outputclass="urgent">
```

| 属性 | 值 | 含义 |
|------|-----|------|
| `id` | `"safety_001"` | 唯一标识符 |
| `xml:lang` | `"zh-CN"` | 内容语言 |
| `outputclass` | `"urgent"` | 自定义分类 |

> 属性值**必须**使用引号包裹（单引号或双引号均可，双引号为 DITA 惯例）。

---

# 格式正规 (Well-formed) vs 有效 (Valid)

<!-- _class: tip -->

| 概念 | 定义 | 验证方式 |
|------|------|----------|
| **格式正规** | 符合 XML 基本语法规则 | XML 解析器 |
| **有效** | 同时满足 DTD/XSD 结构约束 | DTD 验证器 |

**格式正规的条件**：
- 所有标签正确闭合
- 正确的嵌套顺序
- 属性值使用引号
- 只有一个根元素

**有效的条件**：
- 满足格式正规的全部条件
- 符合 DITA DTD 定义的元素顺序和层级

---

# DITA XML 文件结构

```
<?xml version="1.0" encoding="UTF-8"?>    ← XML 声明
<!DOCTYPE topic PUBLIC "... " "topic.dtd"> ← DOCTYPE 声明
<topic id="xxx">                          ← 根元素
  <title>...</title>                      ← 标题
  <shortdesc>...</shortdesc>              ← 简短描述
  <body>                                  ← 正文开始
    <p>...</p>                            ← 段落
    ...
  </body>
</topic>
```

---

# DOCTYPE 声明

DOCTYPE 声明告诉 XML 解析器：
1. 这个文档使用哪套 DTD 规则
2. DTD 文件的公共标识符 (PUBLIC)
3. DTD 文件的本地路径 (SYSTEM)

```xml
<!-- Concept 主题的 DOCTYPE -->
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">

<!-- Task 主题的 DOCTYPE -->
<!DOCTYPE task PUBLIC "-//OASIS//DTD DITA Task//EN" "task.dtd">

<!-- Reference 主题的 DOCTYPE -->
<!DOCTYPE reference PUBLIC "-//OASIS//DTD DITA Reference//EN" "reference.dtd">
```

---

# DITA 常用文本元素

| 元素 | 用途 | 示例 |
|------|------|------|
| `<p>` | 段落 | `<p>这是一段文字。</p>` |
| `<b>` | 粗体 | `<b>重要</b>` |
| `<i>` | 斜体 | `<i>技术术语</i>` |
| `<u>` | 下划线 | `<u>强调</u>` |
| `<term>` | 术语 | `<term>伺服电机</term>` |
| `<codeph>` | 内联代码 | `<codeph>G01 X100</codeph>` |
| `<note>` | 注释/注意 | `<note type="caution">警告...</note>` |

---

# DITA 常用结构元素

| 元素 | 用途 | 层级 |
|------|------|------|
| `<body>` | 正文容器（通用 topic） | 顶层 |
| `<conbody>` | Concept 正文容器 | 顶层 |
| `<taskbody>` | Task 正文容器 | 顶层 |
| `<refbody>` | Reference 正文容器 | 顶层 |
| `<section>` | 分节 | body 内 |
| `<example>` | 示例 | body/section 内 |
| `<ul>` | 无序列表 | body/section 内 |
| `<ol>` | 有序列表（编号） | body/section 内 |

---

# DITA 常用列表元素

```xml
<!-- 无序列表 -->
<ul>
  <li>第一项</li>
  <li>第二项</li>
</ul>

<!-- 有序列表 -->
<ol>
  <li>第一步</li>
  <li>第二步</li>
</ol>

<!-- 简易表格 -->
<simpletable>
  <sthead>
    <stentry>列1标题</stentry>
    <stentry>列2标题</stentry>
  </sthead>
  <strow>
    <stentry>数据1</stentry>
    <stentry>数据2</stentry>
  </strow>
</simpletable>
```

---

# 表格元素

DITA 中的 `<table>` 基于 CALS 表格模型：

```xml
<table>
  <tgroup cols="3">                    ← 列数
    <colspec colname="c1" colwidth="1*"/>  ← 列宽度比例
    <colspec colname="c2" colwidth="2*"/>
    <colspec colname="c3" colwidth="1*"/>
    <thead>
      <row>
        <entry>列1标题</entry>
        <entry>列2标题</entry>
        <entry>列3标题</entry>
      </row>
    </thead>
    <tbody>
      <row>
        <entry>数据1</entry>
        <entry>数据2</entry>
        <entry>数据3</entry>
      </row>
    </tbody>
  </tgroup>
</table>
```

---

# DITA 嵌套规则

正确的层级嵌套是 DITA 有效性的关键：

```
topic/concept/task/reference
  └── body / conbody / taskbody / refbody
        └── section
              └── p, ul, ol, table, example, ...
```

```xml
<!-- ✅ 正确的嵌套 -->
<body>
  <section>
    <p>这是正确的嵌套结构。</p>
  </section>
</body>
```

```xml
<!-- ❌ 错误的嵌套：section 直接放在 p 内部 -->
<p>
  <section><title>错误</title></section>
</p>
```

---

# 重要属性详解

| 属性 | 用途 | 示例 |
|------|------|------|
| `id` | 主题/元素的唯一标识符 | `id="task_replace_tool"` |
| `conref` | 引用另一个元素的内容 | `conref="safety.dita#safety/warning"` |
| `href` | 指向其他主题的链接 | `href="spindle_overview.dita"` |
| `xml:lang` | 内容语言标识 | `xml:lang="zh-CN"` |
| `outputclass` | 自定义分类（用于样式控制） | `outputclass="factory-floor"` |
| `format` | 引用格式类型 | `format="dita"` |
| `type` | 引用主题类型 | `type="concept"` |

---

# id 属性命名规范

<!-- _class: tip -->

**规则**：
- 每个主题的 `id` **必须文件夹内唯一**
- 使用小写字母 + 下划线
- 前缀体现内容类型：`concept_` / `task_` / `ref_`

| 正确示例 | 错误示例 |
|----------|----------|
| `concept_spindle_overview` | `Spindle Overview` (含空格) |
| `task_replace_cutting_tool` | `1_task` (数字开头) |
| `ref_alarm_code_list` | `ref-alarm` (连字符) |
| `safety_emergency_stop` | `安全` (非 ASCII 字符) |

---

# DITA XML 十大常见错误

| # | 错误 | 原因 | 解决方法 |
|---|------|------|----------|
| 1 | 未闭合标签 | `<p>文本` 缺少 `</p>` | 确保每个开始标签有结束标签 |
| 2 | 错误嵌套 | `<p><section>...</section></p>` | 检查 DTD 允许的父子关系 |
| 3 | 重复 ID | 两个主题都使用 `id="safety"` | 使用文件夹内唯一 ID |
| 4 | 缺少引号 | `<topic id=safety>` | `id="safety"` |
| 5 | 空主题 | 只有 `<title>` 无正文内容 | 至少包含 `<shortdesc>` + `<body>` |

---

# DITA XML 十大常见错误（续）

| # | 错误 | 原因 | 解决方法 |
|---|------|------|----------|
| 6 | 缺失 DOCTYPE | 文件没有 DOCTYPE 声明 | 在 XML 声明后添加 DOCTYPE |
| 7 | 编码不匹配 | 文件编码与声明不一致 | 确保 UTF-8 一致 |
| 8 | 缺失 `<shortdesc>` | Concept/Task/Reference 要求 shortdesc | 添加简短描述 |
| 9 | 元素顺序错误 | `<steps>` 后放置 `<prereq>` | 按 DTD 定义顺序排列 |
| 10 | `<title>` 为空 | title 元素内容为空 | 所有主题必须有非空标题 |

---

# oXygen XML Editor 验证

## 在 oXygen 中进行 DTD 验证

1. 打开 `.dita` 文件
2. 点击工具栏 **验证** 按钮（或按 `Ctrl+Shift+V`）
3. 查看底部的 **结果** 面板
4. 错误以红色标记，警告以黄色标记
5. 双击错误消息可跳转到问题行

> oXygen 提供**实时验证**：在编写过程中即时检测格式正规性和有效性。

---

# 创建一个完整的 DITA 文件

<!-- _class: tip -->

创建 DITA 文件的步骤：

1. 在 oXygen 中：**文件 → 新建 → DITA → 选择类型**
2. 自动生成模板文件（含 DOCTYPE 声明和基本结构）
3. 修改 `id` 为有意义的值
4. 填写 `<title>` 和 `<shortdesc>`
5. 在 `<body>` 中添加内容
6. 保存为 `.dita` 文件
7. 按 `Ctrl+Shift+V` 验证

---

# 关键要点

| # | 要点 |
|---|------|
| 1 | DITA 文件**必须是格式正规 (well-formed) + 有效 (valid)** 的 XML |
| 2 | DOCTYPE 声明区分主题类型：`concept.dtd` / `task.dtd` / `reference.dtd` |
| 3 | `<title>` 和 `<shortdesc>` 是所有主题的必填元素 |
| 4 | `id` 属性必须**唯一**，使用 `concept_`/`task_`/`ref_` 前缀 |
| 5 | 嵌套遵循 DTD 规则：`body → section → p/ul/ol/table` |
| 6 | 使用 oXygen **实时验证** 功能即时发现错误 |

> **下一步**：学习如何使用模板快速创建 DITA 主题
