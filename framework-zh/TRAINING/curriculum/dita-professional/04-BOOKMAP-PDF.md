---
marp: true
theme: dita-training
class: invert
paginate: true
---

# Bookmap 与 PDF 输出

## DITA Professional — 模块 4

**从主题集合到专业 PDF 出版物**

---

## 学习目标

完成本模块后，您将能够：

- 区分 Bookmap 和 Ditamap 的使用场景
- 构建完整的 Bookmap 组织结构
- 配置前页（frontmatter）和后页（backmatter）
- 使用 DITA-OT 生成 PDF 输出
- 定制 PDF 样式和版式
- 从现有主题创建 Bookmap 并生成 PDF

---

## Bookmap vs Ditamap

| 特性 | Ditamap | Bookmap |
|------|---------|---------|
| 用途 | 主题组织、在线发布 | 出版物、印刷/PDF |
| 元数据 | 基础 map 元数据 | 丰富书籍元数据 |
| 结构 | 层级 topicref | 章节 + 前后页 |
| 目录 | 不支持 | 自动生成目录 |
| 索引 | 不支持 | 支持索引条目 |
| PDF/EPUB | 间接支持 | 原生支持 |
| 适用场景 | Help、网站、单页 | 手册、书籍、报告 |

---

## Bookmap 完整结构

```
bookmap
  ├── booktitle
  │   ├── mainbooktitle        # 主书名
  │   ├── booktitlealt         # 副书名
  │   └── booklibrary          # 丛书名
  ├── bookmeta                 # 书籍元数据
  │   ├── author               # 作者
  │   ├── publisher            # 出版社
  │   ├── bookid               # 书号
  │   ├── bookchangehistory    # 修订历史
  │   └── bookrights           # 版权声明
  ├── frontmatter              # 前页
  ├── chapter × N              # 章节
  ├── appendix                 # 附录
  └── backmatter               # 后页
```

---

## Bookmap 元数据示例

```xml
<bookmap id="coolpro5000-manual" xml:lang="zh-CN">
  <booktitle>
    <mainbooktitle>CoolPro 5000 冷却系统</mainbooktitle>
    <booktitlealt>操作与维护手册</booktitlealt>
  </booktitle>
  <bookmeta>
    <author>技术文档部</author>
    <author>张三 — 主编</author>
    <publisher>Advanced Manufacturing Corporation</publisher>
    <bookid>DOC-CP5K-OPM-2025-001</bookid>
    <bookchangehistory>
      <revision>
        <time>2025-06-01</time>
        <author>张三</author>
        <description>v1.1: 新增故障排除章节，更新安全检查表</description>
      </revision>
    </bookchangehistory>
  </bookmeta>
</bookmap>
```

---

## 前页（Frontmatter）

```xml
<frontmatter>
  <!-- 生成自动目录 -->
  <toc/>

  <!-- 图列表 -->
  <figurelist/>

  <!-- 表列表 -->
  <tablelist/>

  <!-- 缩写列表 -->
  <abbrevlist>
    <topicref href="shared/abbreviations.dita"/>
  </abbrevlist>

  <!-- 前言 -->
  <preface>
    <topicref href="shared/preface.dita"/>
  </preface>

  <!-- 关于本书 -->
  <notices>
    <topicref href="shared/about-this-manual.dita"/>
  </notices>
</frontmatter>
```

---

## 章节（Chapter）

```xml
<chapter href="topics/overview/concept-coolpro5000.dita">
  <topicmeta>
    <navtitle>第 1 章：系统概述</navtitle>
  </topicmeta>

  <!-- 嵌套小节 -->
  <topicref href="topics/overview/concept-work-principle.dita">
    <topicmeta><navtitle>1.1 工作原理</navtitle></topicmeta>
  </topicref>

  <topicref href="topics/overview/reference-specifications.dita">
    <topicmeta><navtitle>1.2 技术规格</navtitle></topicmeta>
  </topicref>
</chapter>

<chapter href="topics/operation/task-startup.dita">
  <topicmeta><navtitle>第 2 章：操作指南</navtitle></topicmeta>
</chapter>
```

---

## 后页（Backmatter）

```xml
<!-- 附录 -->
<appendix href="topics/appendix/reference-torque-table.dita">
  <topicmeta><navtitle>附录 A：紧固件扭矩标准表</navtitle></topicmeta>
</appendix>

<appendix href="topics/appendix/reference-parts-catalog.dita">
  <topicmeta><navtitle>附录 B：备件目录</navtitle></topicmeta>
</appendix>

<backmatter>
  <!-- 术语表（引用共享术语库） -->
  <glossarylist>
    <topicref href="shared/glossary-common.dita"/>
  </glossarylist>

  <!-- 索引（自动生成） -->
  <indexlist/>

  <!-- 修订记录 -->
  <booklists>
    <trademarklist/>
    <revhistory/>
  </booklists>
</backmatter>
```

---

## 完整 Bookmap 文件结构总览

```
manual-coolpro5000.bookmap
  ├── 元数据区
  │   ├── 书名、副书名
  │   └── 作者、版本、修订历史
  ├── 前页
  │   ├── 目录(自动)
  │   ├── 图列表(自动)
  │   ├── 表列表(自动)
  │   ├── 缩写对照表
  │   └── 前言
  ├── 正文（14 章）
  ├── 附录（3 个）
  └── 后页
      ├── 术语表
      ├── 索引(自动)
      └── 修订记录
```

---

## DITA-OT PDF 转换

**三种 PDF 生成引擎**：

| 引擎 | 类型 | 质量 | 许可 | 适用场景 |
|------|------|------|------|----------|
| **FOP** | 开源 | 基础 | Apache 2.0 | 内部文档、草稿 |
| **Chemistry** | RTF/PDF | 商业 | 商业许可 | 正式出版 |
| **XEP** | PDF | 高 | 商业许可 | 高质量交付 |

```bash
# 使用 FOP 生成 PDF
dita -i manual-coolpro5000.bookmap \
     -f pdf2 \
     -o output/pdf/

# 使用 Chemistry PDF
dita -i manual-coolpro5000.bookmap \
     -f chemistry-pdf \
     --filter=ditaval/product-coolpro5000.ditaval \
     -o output/pdf/
```

---

## PDF 样式定制（FOP）

**自定义 XSL 变量**（`Customization/custom.xsl`）：

```xml
<xsl:stylesheet version="2.0">
  <!-- 页面尺寸：A4 -->
  <xsl:variable name="page-width">210mm</xsl:variable>
  <xsl:variable name="page-height">297mm</xsl:variable>

  <!-- 字体配置 -->
  <xsl:variable name="body-font-family">Noto Sans SC</xsl:variable>
  <xsl:variable name="title-font-family">Noto Sans SC Bold</xsl:variable>
  <xsl:variable name="monospace-font-family">Source Code Pro</xsl:variable>

  <!-- 页眉页脚 -->
  <xsl:variable name="header-text">CoolPro 5000 — 操作与维护手册</xsl:variable>
  <xsl:variable name="show-footer-text">true</xsl:variable>
</xsl:stylesheet>
```

---

## PDF 样式定制（页面布局）

```
┌─────────────────────────────────────┐
│  CoolPro 5000 — 操作与维护手册      │ ← 页眉
├─────────────────────────────────────┤
│                                     │
│          第 2 章：操作指南           │
│                                     │
│  2.1 开机前检查                      │
│   1. 检查冷却液位...                 │
│   2. 检查各阀门位置...               │
│                                     │
├─────────────────────────────────────┤
│  DOC-CP5K-OPM-2025-001    Page 2-1 │ ← 页脚
└─────────────────────────────────────┘
```

---

## PDF 生成参数

```bash
dita -i manual-coolpro5000.bookmap \
     -f pdf2 \
     --filter=ditaval/product-coolpro5000.ditaval \
     --propertyfile=build.properties \
     -Doutput.dir=output/pdf/ \
     -Dargs.css=css/custom-pdf.css \
     -Dargs.copycss=yes \
     -Dargs.grammar.cache=yes \
     -Dclean.temp=yes \
     -v
```

**关键参数说明**：
- `--filter`：条件过滤，生成特定产品的 PDF
- `-Dargs.css`：自定义 CSS 样式
- `-Dclean.temp`：构建后清理临时文件
- `-v`：详细日志输出，便于调试

---

<!-- _class: exercise -->

## 练习：从现有主题创建 Bookmap 并生成 PDF

**任务**：

1. 选择 3-4 个已有主题（概念、任务、故障排查、参考各一个）
2. 创建 `my-manual.bookmap` 文件，包含完整的书名和元数据
3. 配置 `frontmatter`（目录、缩写列表）
4. 将主题按章节组织（至少 2 个 chapter）
5. 配置 `backmatter`（术语表）
6. 运行 DITA-OT 生成 PDF

**验证**：
- PDF 应包含自动生成的目录
- 章节目录层级正确
- 页码连续
- 页眉页脚信息完整

---

## 关键要点

- **Bookmap** 适用于 PDF/EPUB 出版物，**Ditamap** 适用于在线 Help
- Bookmap 提供自动**目录、图列表、索引**等出版必备元素
- 三种 PDF 引擎：FOP（免费入门）、Chemistry（商业标准）、XEP（高端）
- `frontmatter` 和 `backmatter` 是 PDF 专业度的关键
- 条件过滤（DITAVAL）可在生成时排除不需要的内容
- CSS/XSL 定制实现企业品牌化 PDF 输出

---

## 下一步

模块 5：**本地化架构**

学习多语言文档管理和翻译工作流，支持 GB/T 标准转换
