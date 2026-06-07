# 跨格式输出一致性控制

**文档 ID：** PUB-001
**版本：** 1.0
**最后更新：** 2026-06-07
**状态：** 已批准

---

## 目录

1. [挑战](#1-挑战)
2. [输出格式矩阵](#2-输出格式矩阵)
3. [跨格式内容写作规则](#3-跨格式内容写作规则)
4. [CSS 策略：共享基础 + 格式覆盖](#4-css-策略共享基础--格式覆盖)
5. [图片与媒体策略](#5-图片与媒体策略)
6. [跨格式表格渲染](#6-跨格式表格渲染)
7. [交叉引用与链接策略](#7-交叉引用与链接策略)
8. [排版与品牌控制](#8-排版与品牌控制)
9. [按格式的条件内容](#9-按格式的条件内容)
10. [DITA-OT 多格式构建配置](#10-dita-ot-多格式构建配置)
11. [自动化跨格式测试](#11-自动化跨格式测试)
12. [治理规则](#12-治理规则)

---

## 1. 挑战

### 1.1 核心问题

DITA 单一来源发布的核心承诺是**一次编写，多格式输出**：

```
                    ┌──────────┐
                    │  源文件   │
                    │  (DITA)  │
                    └────┬─────┘
                         │
         ┌───────────────┼───────────────┐
         │               │               │
         ▼               ▼               ▼
    ┌────────┐     ┌──────────┐     ┌──────────┐
    │ HTML5  │     │   PDF    │     │  移动端   │
    │  (网页) │     │  (打印)   │     │ (ePub)   │
    └────────┘     └──────────┘     └──────────┘
```

挑战在于：每种格式的能力不同。HTML5 中好看的表格在 PDF 打印中可能溢出；桌面上可读的图片在手机上可能看不清；HTML5 中的超链接在纸张上没有意义。

**DITA 不会自动解决这个问题。它只是让解决成为可能。** 解决方案需要精心编排的写作实践、细致的配置和自动化验证。

### 1.2 "一致性"在不同格式中的含义

| 维度 | HTML5（网页） | PDF（打印） | 移动端（ePub） | 一致性的定义 |
|------|-------------|-----------|--------------|------------|
| **内容** | 所有内容呈现 | 所有内容呈现 | 所有内容呈现 | 任何格式不丢失内容 |
| **结构** | 标题、层级 | 标题、层级 | 标题、层级 | 相同的逻辑结构 |
| **导航** | 超链接、目录 | 页码、目录 | 超链接、目录 | 各格式均有可用的导航方式 |
| **品牌** | 颜色、Logo、字体 | 颜色、Logo、字体 | 简化品牌 | 一致的企业形象 |
| **可读性** | 屏幕优化 | 打印优化 | 小屏优化 | 各媒介均有良好体验 |
| **完整性** | 完整文档 | 完整文档 | 完整文档 | 所有格式信息相同 |
| **时效性** | 同一发布周期 | 同一发布周期 | 同一发布周期 | 所有格式同时更新 |

---

## 2. 输出格式矩阵

### 2.1 DITA 功能兼容性矩阵

| DITA 功能 | HTML5 | PDF (打印) | 移动端 (ePub) | 风险等级 |
|-----------|-------|-----------|--------------|---------|
| **`<p>`、列表、标题** | ✅ 完整 | ✅ 完整 | ✅ 完整 | 低 |
| **`<simpletable>`** | ✅ 完整 | ✅ 完整 | ✅ 完整 | 低 |
| **`<table>`（复杂）** | ✅ 完整 | ✅ 完整 | ⚠️ 横向滚动 | **中** |
| **`<image>`** | ✅ 完整 | ✅ 完整 | ⚠️ 需要缩放 | **中** |
| **`<xref>`（内部）** | ✅ 超链接 | ✅ 页码引用 | ✅ 超链接 | 低 |
| **`<xref>`（外部URL）** | ✅ 超链接 | ⚠️ 打印URL | ✅ 超链接 | **中** |
| **SVG 图** | ✅ 可交互 | ✅ 静态 | ⚠️ 可能丢失层次 | **中** |
| **`<pre>`（代码块）** | ✅ 语法高亮 | ✅ 等宽字体 | ⚠️ 换行问题 | **中** |
| **DITAVAL 过滤** | ✅ 已应用 | ✅ 已应用 | ✅ 已应用 | **高（如错误）** |

---

## 3. 跨格式内容写作规则

### 3.1 黄金法则

> **优先为约束最强的格式写作。**
>
> 如果内容能在 ePub（约束最强的格式）中正常工作，它就能在所有格式中正常工作。如果内容仅在 HTML5 中可用，它可能在 PDF 或 ePub 中出问题。

### 3.2 写作约束规则

| 规则 | 约束 | 理由 |
|------|------|------|
| **R1** | 表格最多 8 列 | 8+ 列表格在移动端和打印中溢出 |
| **R2** | 图片最大宽度 800px，优先用 SVG | 大位图增加移动端下载量，打印中像素化 |
| **R3** | 图片缩放不超过正文宽度 100% | 过大图片破坏 PDF 排版 |
| **R4** | 提示框内不用 HTML `<table>` | 嵌套表格在 PDF Chemistry 中失败 |
| **R5** | 有序列表最多 3 层 | 深层嵌套在移动端重排中出问题 |
| **R6** | `<pre>` 代码块每行最多 90 字符 | 超长行溢出 PDF 页面宽度 |
| **R7** | 不使用空 `<p>` 做间距 | PDF 会合并空段落 |
| **R8** | 外部 URL 用 `@scope="external"` | 原始 URL 打印在 PDF 中不专业 |
| **R9** | 文件路径用 `<filepath>` 元素 | 语义化元素支持格式专用渲染 |
| **R10** | 缩略词先在 `<glossentry>` 中定义 | 通过术语表在所有格式中一致渲染 |

### 3.3 内容完整性验证

```xml
<sch:pattern id="format-completeness">
  <!-- 所有图片必须有 alt 文本（可访问性 + PDF 回退） -->
  <sch:rule context="*[contains(@class, ' topic/image ')]">
    <sch:assert test="@alt or @longdescref">
      [F1] 图片必须有 @alt 或 @longdescref 以支持 PDF 回退
    </sch:assert>
  </sch:rule>

  <!-- 所有代码块必须指定语言 -->
  <sch:rule context="*[contains(@class, ' topic/codeblock ')]">
    <sch:assert test="@outputclass">
      [F2] 代码块必须通过 @outputclass 指定语言以支持语法高亮
    </sch:assert>
  </sch:rule>
</sch:pattern>
```

---

## 4. CSS 策略：共享基础 + 格式覆盖

### 4.1 架构

```
framework/
  styles/
    shared/           ← 所有格式共享
      ├── base.css    ← 核心：字体、颜色、间距、品牌
      └── tokens.css  ← 设计令牌
    html5/
      └── html5.css   ← Web 专用：悬停、交互、响应式
    pdf/
      └── pdf.css     ← 打印专用：分页、页眉、页脚
    mobile/
      └── epub.css    ← 移动端专用：单列、流式布局
```

### 4.2 设计令牌系统

```css
/* shared/tokens.css — 设计令牌 */
:root {
  /* 排版 */
  --font-primary: 'Noto Sans SC', 'Source Han Sans', sans-serif;
  --font-mono: 'JetBrains Mono', 'Cascadia Code', monospace;
  --font-size-body: 10pt;
  --font-size-h1: 18pt;
  --font-size-h2: 14pt;

  /* 颜色 */
  --color-primary: #1a5276;
  --color-secondary: #2e86c1;
  --color-accent: #e67e22;
  --color-warning: #e74c3c;

  /* 间距 */
  --spacing-paragraph: 2mm;
}
```

### 4.3 格式专用覆盖

**HTML5 网页：**
```css
body { max-width: 960px; margin: 0 auto; padding: 20px; }
table { width: 100%; border-collapse: collapse; }
pre { background: #282c34; border-radius: 8px; }
```

**PDF 打印：**
```css
@page { size: A4; margin: 20mm; }
body { font-size: 10pt; line-height: 1.5; }
table { font-size: 9pt; page-break-inside: avoid; }
h1 { page-break-before: always; }
```

**移动端 ePub：**
```css
body { font-size: 1em; line-height: 1.6; }
table { overflow-x: auto; }
pre { white-space: pre-wrap; word-break: break-all; }
img { max-width: 100%; height: auto; }
```

---

## 5. 图片与媒体策略

### 5.1 图片格式决策树

```
需要新图片
    │
    ├── 是图表/示意图？→ SVG（所有格式首选）
    │
    ├── 是照片？→ JPEG（1080px 最大，sRGB）
    │
    ├── 是截图？→ PNG（2x DPI，最大宽度 800px）
    │
    └── 是图标？→ SVG（单色，可缩放）
```

### 5.2 多格式图片属性

```xml
<image href="images/spindle-assembly.svg"
       alt="带标签的主轴装配示意图"
       width="100%" scale="80" placement="break">
  <ph>图 1：主轴装配 — MC-5000</ph>
</image>
```

| 属性 | HTML5 | PDF | 移动端 |
|------|-------|-----|--------|
| `@width` | CSS max-width | 页面宽度缩放 | 设备宽度缩放 |
| `@scale` | 忽略（用CSS） | 原始尺寸百分比 | 忽略（用CSS） |
| `@placement="break"` | 块级显示 | 新段落前后 | 块级显示 |
| `@alt` | Alt 文本（可访问性） | 图片缺失时渲染 | Alt 文本 |

### 5.3 SVG 写作规则

- 使用 `viewBox`（而非固定 `width`/`height`）实现自动缩放
- 将文本转为路径或嵌入字体（打印渲染可能缺字体）
- 文件大小控制在 500KB 以下
- 不使用外部依赖（无指向外部字体/图片的 `href`）
- 颜色须匹配设计令牌

### 5.4 位图图片指南

| 格式 | 最大宽度 | DPI | 说明 |
|------|---------|-----|------|
| Web (HTML5) | 800px | 72 | 通过 CSS 实现响应式 |
| 打印 (PDF) | 原始尺寸 | 300 | 避免放大 |
| 移动端 (ePub) | 设备宽度 | 72 | 单张图片不超过 500KB |

---

## 6. 跨格式表格渲染

### 6.1 表格列数风险

| 列数 | HTML5 | PDF | 移动端 |
|------|-------|-----|--------|
| 3 列或更少 | ✅ 优秀 | ✅ 优秀 | ✅ 良好 |
| 4-6 列 | ✅ 良好 | ✅ 良好 | ⚠️ 需横向滚动 |
| 7-8 列 | ⚠️ 压缩 | ⚠️ 字体缩小 | ❌ 不可读 |
| 9+ 列 | ❌ 需滚动 | ❌ 页面溢出 | ❌ 完全不可读 |

### 6.2 宽表缓解策略

| 策略 | 如何操作 | 适用格式 | 权衡 |
|------|---------|---------|------|
| **拆分为多个表** | 8 列表拆为 2×4 列 | 所有格式 | 重复表头行 |
| **旋转为横排** | `@orient="land"` | 仅 PDF | 与竖排页面不一致 |
| **缩减字体** | 宽表使用 `font-size: 7pt` | HTML5 + PDF | 更难阅读 |
| **键值转换** | 横向列转为纵向行 | 所有格式 | 改变表格语义 |

### 6.3 自动列数检查

```xml
<sch:rule context="*[contains(@class, ' table/entry ')]">
  <sch:let name="cols" value="count(../../tgroup/col)"/>
  <sch:report test="$cols > 8">
    [T1] 表格有 <sch:value-of select="$cols"/> 列（上限 8 列）。
    请拆分为多个表或转换为键值格式。
  </sch:report>
</sch:rule>
```

---

## 7. 交叉引用与链接策略

### 7.1 引用类型与格式行为

| 链接类型 | HTML5 | PDF | 移动端 |
|---------|-------|-----|--------|
| **主题链接** | 超链接 | 页码 + 链接 | 超链接 |
| **Key 引用** | 超链接 | 页码 | 超链接 |
| **外部 URL** | 超链接，新窗口打开 | URL 以括号打印 | 超链接 |
| **文件下载** | 下载链接 | 内部页码 | 下载链接 |

### 7.2 引用写作规则

**使用 keyref 进行所有内部交叉引用（不使用直接 href）：**

```xml
<!-- ❌ 避免：脆弱的 href 路径 -->
<xref href="../reference/coolant-specs.dita">冷却液规格</xref>

<!-- ✅ 推荐：基于 key 的引用 -->
<xref keyref="ref-coolant-specs">冷却液规格</xref>
```

**外部 URL 必须使用 `@scope="external"`：**

```xml
<xref href="https://examplecorp.com/support"
      format="html" scope="external">支持门户</xref>
```

**始终提供显式链接文本：**

```xml
<!-- ❌ 避免：自动生成链接文本 -->
<xref keyref="ref-spindle-specs"/>

<!-- ✅ 推荐：显式、简短的链接文本 -->
<xref keyref="ref-spindle-specs">主轴规格表</xref>
```

---

## 8. 排版与品牌控制

### 8.1 字体策略

| 格式 | 正文字体 | 代码字体 | CJK 字体 |
|------|---------|---------|---------|
| HTML5 | 系统 UI / web-safe | 等宽字体栈 | 系统 CJK |
| PDF（打印） | Noto Sans / 已嵌入 | JetBrains Mono 已嵌入 | Noto Sans SC 已嵌入 |
| 移动端（ePub） | 设备默认 | 设备等宽字体 | 设备 CJK |

### 8.2 跨格式品牌元素

| 元素 | HTML5 | PDF | 移动端 |
|------|-------|-----|--------|
| Logo | 页眉 `img` | 页眉 `fo:external-graphic` | 封面仅（小尺寸） |
| 配色 | 完整 CSS 颜色 | CMYK 转换 | 设备颜色模式 |
| 公司名 | `<keyword keyref="company-name"/>` | 同一 keyref | 相同 |
| 页码 | 不适用（滚动） | 页脚/页码 | 设备页码 |

---

## 9. 按格式的条件内容

### 9.1 DITAVAL 配置

```xml
<!-- ditaval/html5.ditaval — Web 输出 -->
<val>
  <prop att="audience" val="operator" action="include"/>
  <prop att="platform" val="web" action="include"/>
  <prop att="platform" val="print" action="exclude"/>
</val>
```

```xml
<!-- ditaval/pdf.ditaval — 打印输出 -->
<val>
  <prop att="platform" val="print" action="include"/>
  <prop att="platform" val="web" action="exclude"/>
</val>
```

### 9.2 条件内容写作

```xml
<!-- 仅 HTML5 出现：交互元素 -->
<p platform="web">点击图示可缩放。 <image href="interactive-spindle.svg"/></p>

<!-- 仅 PDF 出现：页码引用 -->
<p platform="print">参见对面页的主轴装配图。</p>

<!-- 所有格式：核心信息 -->
<p>要检查主轴，请拆下前面盖板。</p>
```

---

## 10. DITA-OT 多格式构建配置

### 10.1 并行构建

```yaml
jobs:
  build-all-formats:
    strategy:
      matrix:
        format: [html5, pdf, epub]
    steps:
      - name: Build ${{ matrix.format }}
        run: |
          dita --input=products/mc5000/map/mc5000-manual.ditamap \
               --format=${{ matrix.format }} \
               --output=output/${{ matrix.format }}
```

### 10.2 跨格式通用参数

```yaml
# 所有格式共享的参数
common-args: >
  --property=args.css=framework/styles/shared/base.css
  --property=args.draft=false
  --property=generate.toc=yes
  --property=nav-toc=full
  --property=toc.section.depth=4
```

---

## 11. 自动化跨格式测试

### 11.1 测试清单

| 测试 | 检测内容 | 可接受标准 |
|------|---------|-----------|
| **内容一致性** | 某格式缺少内容 | 零遗漏 |
| **链接验证** | 各格式中断裂引用 | 零断裂 |
| **视觉回归** | 意外的布局变化 | < 5% 变化 |
| **表格一致性** | 各格式中表格行列数相同 | 完全一致 |
| **图片存在** | 任何格式中缺失图片 | 零缺失 |
| **字体嵌入** | PDF 中未嵌入的字体 | 零缺失 |

### 11.2 构建后内容一致性验证

```bash
# 提取各格式中的所有标题并比较
for format in html5 pdf epub; do
  case $format in
    html5) grep -rh '<h1' output/web/ | sed 's/<[^>]*>//g' | sort > /tmp/titles-html5.txt ;;
    pdf) pdftotext output/pdf/manual.pdf /tmp/pdf-text.txt
         grep "^[0-9]" /tmp/pdf-text.txt > /tmp/titles-pdf.txt ;;
    epub) unzip -p output/epub/manual.epub "*.xhtml" | grep '<h1' | sed 's/<[^>]*>//g' | sort > /tmp/titles-epub.txt ;;
  esac
done

diff /tmp/titles-html5.txt /tmp/titles-pdf.txt || echo "HTML5 vs PDF 内容差异！"
```

---

## 12. 治理规则

### 12.1 多格式发布检查清单

发布前必须确认：

- [ ] **内容一致性**：构建所有格式，验证标题列表一致
- [ ] **表格检查**：无表格超过 8 列
- [ ] **图片检查**：所有图片有 `@alt` 文本
- [ ] **链接检查**：所有交叉引用可解析
- [ ] **条件检查**：DITAVAL 覆盖了所有条件属性
- [ ] **CSS 同步**：所有格式 CSS 文件中的设计令牌一致
- [ ] **PDF 字体检查**：PDF 输出中所有字体已嵌入
- [ ] **回归检查**：与基线相比无意外变化

### 12.2 快速参考：跨格式写作

| 应该做 | 不要做 |
|--------|--------|
| 使用 `<simpletable>`（4 列及以下） | 使用 9+ 列的 `<table>` |
| 图表使用 SVG | 文字多的图片使用 JPEG |
| 所有交叉引用使用 keyref | 使用硬编码 `href` 路径 |
| 所有图片加 `@alt` 文本 | 假设图片在所有格式中都能渲染 |
| 发布前在所有格式中测试 | 假设"HTML5 能用 = 所有格式都能用" |
| PDF 中嵌入字体 | PDF 依赖系统字体 |

---

## 相关文档

| 文档 | 位置 |
|------|------|
| CI/CD 流水线 | [CI-CD/01-CICD-PIPELINE.md](../CI-CD/01-CICD-PIPELINE.md) |
| 风格指南 | [STYLE-GUIDE/01-STYLE-GUIDE.md](../STYLE-GUIDE/01-STYLE-GUIDE.md) |
| 文档成套完整性 | [WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md](../WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md) |
| 风险控制策略 | [RISK-CONTROL-STRATEGY.md](../RISK-CONTROL-STRATEGY.md) |
