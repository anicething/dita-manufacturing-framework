# Cross-Format Output Consistency

**Document ID:** PUB-001
**Version:** 1.0
**Last Updated:** 2026-06-07
**Status:** Approved

---

## Table of Contents

1. [The Challenge](#1-the-challenge)
2. [Output Format Matrix](#2-output-format-matrix)
3. [Content Authoring Rules for Multi-Format](#3-content-authoring-rules-for-multi-format)
4. [CSS Strategy: Shared Base + Format Overrides](#4-css-strategy-shared-base--format-overrides)
5. [Image & Media Strategy](#5-image--media-strategy)
6. [Table Rendering Across Formats](#6-table-rendering-across-formats)
7. [Cross-Reference & Link Strategy](#7-cross-reference--link-strategy)
8. [Typography & Branding Control](#8-typography--branding-control)
9. [Conditional Content per Format](#9-conditional-content-per-format)
10. [DITA-OT Configuration for Multi-Format](#10-dita-ot-configuration-for-multi-format)
11. [Automated Cross-Format Testing](#11-automated-cross-format-testing)
12. [Governance Rules](#12-governance-rules)

---

## 1. The Challenge

### 1.1 The Core Problem

With DITA single-source publishing, **one source produces many outputs**:

```
                    ┌──────────┐
                    │  Source  │
                    │  (DITA)  │
                    └────┬─────┘
                         │
         ┌───────────────┼───────────────┐
         │               │               │
         ▼               ▼               ▼
    ┌────────┐     ┌──────────┐     ┌──────────┐
    │ HTML5  │     │   PDF    │     │  Mobile  │
    │  (Web) │     │  (Print) │     │ (ePub)   │
    └────────┘     └──────────┘     └──────────┘
```

The challenge: each format has different capabilities. A table that looks good in HTML5 may overflow in PDF print. An image readable on desktop may be illegible on mobile. A hyperlink works in HTML5 but has no meaning on paper.

**DITA does not solve this problem automatically. It only makes it possible to solve.** The solution requires deliberate authoring practices, careful configuration, and automated verification.

### 1.2 What "Consistency" Means Across Formats

| Aspect | HTML5 (Web) | PDF (Print) | Mobile (ePub) | Definition of Consistency |
|--------|-------------|-------------|---------------|--------------------------|
| **Content** | All content present | All content present | All content present | No content lost in any format |
| **Structure** | Headings, hierarchy | Headings, hierarchy | Headings, hierarchy | Same logical structure |
| **Navigation** | Hyperlinks, TOC | Page numbers, TOC | Hyperlinks, TOC | Navigable in each format's idiom |
| **Branding** | Colors, logo, fonts | Colors, logo, fonts | Simplified branding | Consistent identity |
| **Readability** | Screen-optimized | Print-optimized | Small-screen optimized | Good experience in each medium |
| **Completeness** | Full docs | Full docs | Full docs | Same information in all formats |
| **Timeliness** | Same publish cycle | Same publish cycle | Same publish cycle | All formats updated simultaneously |

---

## 2. Output Format Matrix

### 2.1 Supported Formats

| Format | Channel | DITA-OT Transform | Audience Use Case | Priority |
|--------|---------|-------------------|-------------------|----------|
| **HTML5** | Web browser | `--format=html5` | Online help, knowledge base | **Primary** |
| **PDF (Print)** | Physical print / download | `--format=pdf` (Chemistry) | Service manuals, regulatory | **Primary** |
| **PDF (Screen)** | Digital download | `--format=pdf2` | Operator manuals, reference | **Secondary** |
| **ePub** | Mobile devices | `--format=epub` | Field service, tablets | **Tertiary** |
| **HTML Help** | Legacy systems | `--format=htmlhelp` | Older equipment | Legacy |

### 2.2 Feature Compatibility Matrix

| DITA Feature | HTML5 | PDF (Print) | Mobile (ePub) | Risk Level |
|-------------|-------|-------------|---------------|------------|
| **`<p>`, lists, headings** | ✅ Full | ✅ Full | ✅ Full | Low |
| **`<simpletable>`** | ✅ Full | ✅ Full | ✅ Full | Low |
| **`<table>` (complex)** | ✅ Full | ✅ Full | ⚠️ Horizontal scroll | **Medium** |
| **`<image>`** | ✅ Full | ✅ Full | ⚠️ Rescaling needed | **Medium** |
| **`<xref>` (internal)** | ✅ Hyperlink | ✅ Page ref | ✅ Hyperlink | Low |
| **`<xref>` (external URL)** | ✅ Hyperlink | ⚠️ URL printed | ✅ Hyperlink | **Medium** |
| **`<fn>` (footnote)** | ✅ Tooltip | ✅ Page footer | ⚠️ Endnotes | Low |
| **SVG diagrams** | ✅ Interactive | ✅ Static | ⚠️ May lose layers | **Medium** |
| **`<codeph>`** | ✅ Monospace | ✅ Monospace | ✅ Monospace | Low |
| **`<pre>` (code block)** | ✅ Syntax highlight | ✅ Monospace | ⚠️ Line wrap | **Medium** |
| **Keyref injection** | ✅ Resolved | ✅ Resolved | ✅ Resolved | Low |
| **Conref inclusion** | ✅ Resolved | ✅ Resolved | ✅ Resolved | Low |
| **DITAVAL filtering** | ✅ Applied | ✅ Applied | ✅ Applied | **High (if wrong)** |
| **MathML** | ✅ Browser render | ✅ Static | ⚠️ Limited | **Medium** |

### 2.3 Format Risk Matrix

| Risk | Formats Affected | Cause | Mitigation |
|------|-----------------|-------|------------|
| **Content loss** | ePub | Unsupported elements | Test all formats; use only supported features |
| **Layout broken** | PDF print | Table/page overflow | Set `@scale` on images; use `@orient="land"` on wide tables |
| **Navigation broken** | PDF print | Orphan pages, no hyperlinks | Verify PDF bookmarks; use `@print='yes'` for page-range links |
| **Visual mismatch** | All | Different CSS/FO processing | Use shared design tokens; automated pixel-diff detection |

---

## 3. Content Authoring Rules for Multi-Format

### 3.1 The Golden Rule

> **Write for the most constrained format first.**
>
> If content works in ePub (the most constrained format), it will work everywhere. If content only works in HTML5, it may break in PDF or ePub.

### 3.2 Authoring Constraints

| Rule | Constraint | Rationale | Schematron Check |
|------|-----------|-----------|-----------------|
| **R1** | Table max width: 8 columns | 8+ column tables overflow in mobile and print | ✅ |
| **R2** | Image max width: 800px, prefer SVG | Large bitmaps bloat mobile downloads and look pixelated in print | ✅ |
| **R3** | Image scale: max 100% of body text width | Oversized images break page layout in PDF | ✅ |
| **R4** | No HTML `<table>` inside notes/admonitions | Nested tables fail in PDF Chemistry | ✅ |
| **R5** | Ordered list max depth: 3 levels | Deep nesting breaks in mobile reflow | ✅ |
| **R6** | Line length in `<pre>`: max 90 chars | Longer lines overflow PDF page width | ✅ |
| **R7** | No empty `<p>` for spacing | PDF collapses empty paragraphs; use CSS `margin` instead | ✅ |
| **R8** | External URLs: use `<xref>` with `@format="html"` | Raw URLs printed in PDF look unprofessional | Manual |
| **R9** | File paths in code blocks: use `<filepath>` | Semantic element enables format-specific rendering | ✅ |
| **R10** | Acronyms: define in `<glossentry>` first | Consistent rendering across all formats via glossary | ✅ |

### 3.3 Writing for Different Reading Behaviors

| Aspect | Web (HTML5) | Print (PDF) | Mobile (ePub) |
|--------|-------------|-------------|---------------|
| **Reading pattern** | Scanning, non-linear | Linear, sequential | Vertical scrolling |
| **Attention span** | Short (30s-2min) | Medium (10-30min) | Short (1-5min) |
| **Search** | Ctrl+F, browser search | Index, TOC | Device search |
| **Reference behavior** | Look up, jump back | Bookmark, tab | Scroll, pinch-zoom |

**Implication:** Content must be equally consumable via scanning (web), linear reading (print), and vertical scrolling (mobile). This means:
- Headings must be descriptive (not "Introduction" but "Spindle Speed Configuration")
- Step numbering must be explicit (not "1, 2, 3" in wrong order)
- Warnings must be visually distinctive in all formats (use `<hazardstatement>`, not colored `<note>`)

### 3.4 Content Completeness Verification

Every format build must verify:

```xml
<sch:pattern id="format-completeness">
  <!-- All images must have alt text (accessibility + fallback for print) -->
  <sch:rule context="*[contains(@class, ' topic/image ')]">
    <sch:assert test="@alt or @longdescref">
      [F1] Image must have @alt or @longdescref for PDF fallback
    </sch:assert>
  </sch:rule>

  <!-- All code blocks must specify a language -->
  <sch:rule context="*[contains(@class, ' topic/codeblock ')]">
    <sch:assert test="@outputclass">
      [F2] Code blocks must specify language via @outputclass for syntax highlighting
    </sch:assert>
  </sch:rule>
</sch:pattern>
```

---

## 4. CSS Strategy: Shared Base + Format Overrides

### 4.1 Architecture

```
framework/
  styles/
    shared/           ← Shared across all formats
      └── base.css    ← Core: fonts, colors, spacing, brand
    html5/
      └── html5.css   ← Web-specific: hover, interactive, responsive
    pdf/
      └── pdf.css     ← Print-specific: page breaks, headers, footers
    mobile/
      └── epub.css    ← Mobile-specific: single column, fluid layout
    print/
      └── print.css   ← Printer-specific: crop marks, no background
```

### 4.2 Design Token System

To ensure visual consistency, define design tokens in all formats from a single source:

```css
/* shared/base.css — Design Tokens */
:root {
  /* Typography */
  --font-primary: 'Noto Sans SC', 'Segoe UI', sans-serif;
  --font-mono: 'JetBrains Mono', 'Cascadia Code', monospace;
  --font-size-body: 10pt;           /* PDF base */
  --font-size-h1: 18pt;
  --font-size-h2: 14pt;
  --font-size-h3: 12pt;

  /* Colors */
  --color-primary: #1a5276;         /* Dark blue */
  --color-secondary: #2e86c1;       /* Medium blue */
  --color-accent: #e67e22;          /* Orange */
  --color-warning: #e74c3c;         /* Red */
  --color-success: #27ae60;         /* Green */

  /* Spacing */
  --spacing-unit: 4mm;
  --spacing-paragraph: 2mm;

  /* Page (PDF specific) */
  --page-width: 210mm;
  --page-height: 297mm;
  --page-margin: 20mm;
}
```

### 4.3 Format-Specific Overrides

**HTML5 (web):**
```css
/* html5/html5.css */
body {
  max-width: 960px;
  margin: 0 auto;
  padding: 20px;
  font-family: var(--font-primary);
}
h1 { font-size: 2rem; border-bottom: 3px solid var(--color-secondary); }
table { width: 100%; border-collapse: collapse; }
pre { background: #282c34; border-radius: 8px; padding: 1em; }
```

**PDF (print):**
```css
/* pdf/pdf.css */
@page { size: A4; margin: 20mm; }
body { font-size: 10pt; line-height: 1.5; font-family: var(--font-primary); }
table { font-size: 9pt; page-break-inside: avoid; }
pre { font-size: 8pt; page-break-inside: avoid; white-space: pre-wrap; }
h1 { page-break-before: always; }
h1, h2, h3 { page-break-after: avoid; }
```

**Mobile (ePub):**
```css
/* mobile/epub.css */
body { font-size: 1em; line-height: 1.6; padding: 0; }
table { font-size: 0.9em; overflow-x: auto; }
pre { font-size: 0.8em; white-space: pre-wrap; word-break: break-all; }
img { max-width: 100%; height: auto; }
```

### 4.4 Automated CSS Synchronization

Design tokens must be kept in sync across formats. Use a shared CSS variables file:

```bash
# build-sync-css.sh — Generate format-specific CSS from shared tokens
# 1. Shared tokens → base.css (all formats import this)
# 2. Format-specific overrides are thin layers on top
# 3. CI/CD validates: same hex color in all CSS files = no drift

grep -o '--color-[^:]*:[^;]*' framework/styles/shared/base.css | sort > /tmp/shared-tokens.txt
grep -o '--color-[^:]*:[^;]*' framework/styles/html5/html5.css | sort > /tmp/html5-tokens.txt
diff /tmp/shared-tokens.txt /tmp/html5-tokens.txt || echo "CSS token drift detected!"
```

---

## 5. Image & Media Strategy

### 5.1 Image Format Decision Tree

```
New image needed
    │
    ├── Is it a diagram/chart? → SVG (preferred for all formats)
    │
    ├── Is it a photo? → JPEG (1080px max, sRGB)
    │
    ├── Is it a screenshot? → PNG (2x DPI for HiDPI, max 800px width)
    │
    └── Is it an icon? → SVG (single color, scalable)
```

### 5.2 Image Attributes for Multi-Format

```xml
<image href="images/spindle-assembly.svg"
       alt="Spindle assembly diagram with labeled components"
       width="100%"
       scale="80"
       placement="break"
       id="img-spindle-assembly">
  <ph>Figure 1: Spindle Assembly — MC-5000</ph>
</image>
```

| Attribute | HTML5 Behavior | PDF Behavior | Mobile Behavior |
|-----------|---------------|-------------|-----------------|
| `@width` | CSS max-width | Page width scaling | Device width scaling |
| `@scale` | Ignored (use CSS) | Percentage of original | Ignored (use CSS) |
| `@placement="break"` | Block display (new line) | New paragraph before/after | Block display |
| `@placement="inline"` | Inline with text | Inline | Inline |
| `@alt` | Alt text (accessibility) | May render in place of missing image | Alt text |

### 5.3 Responsive SVG Strategy

SVG is the recommended image format because it scales perfectly across all formats:

```xml
<image href="images/coolant-flow-diagram.svg"
       alt="Coolant flow path diagram"
       width="100%">
  <!-- SVG scales natively — no need for multiple resolution variants -->
</image>
```

**SVG authoring rules:**
- Use `viewBox` (not fixed `width`/`height`) for automatic scaling
- Convert text to paths or embed fonts (print rendering may lack your fonts)
- Keep file size under 500KB (measure with `wc -c`)
- No external dependencies (no `href` to external fonts/images)
- Color palette must match design tokens

### 5.4 Bitmap Image Guidelines

For screenshots and photos that cannot be SVG:

| Format | Max Width | DPI | Notes |
|--------|-----------|-----|-------|
| Web (HTML5) | 800px | 72 | Responsive via CSS |
| Print (PDF) | Native (scaled to page) | 300 | Avoid upscaling; pre-scale to target size |
| Mobile (ePub) | Device-width | 72 | Images must be under 500KB each |

**Single image, all formats:** Use the largest needed resolution (800px @ 300dpi for print) and let CSS/FO scale down for web and mobile. Do not maintain separate resolution variants — this creates consistency risk.

---

## 6. Table Rendering Across Formats

### 6.1 Table Format Risks

| Table Type | HTML5 | PDF | Mobile |
|-----------|-------|-----|--------|
| **3 columns or fewer** | ✅ Excellent | ✅ Excellent | ✅ Good |
| **4-6 columns** | ✅ Good | ✅ Good | ⚠️ Horizontal scroll |
| **7-8 columns** | ⚠️ Compressed | ⚠️ Font shrinks | ❌ Unreadable |
| **9+ columns** | ❌ Scroll required | ❌ Page overflow | ❌ Completely broken |

### 6.2 Table Authoring Rules

**Rule: Prefer `<simpletable>` over `<table>`:** Simple tables handle responsive layout better.

```xml
<!-- ✅ RECOMMENDED: simpletable with 4 columns max -->
<simpletable id="specs-spindle" relcolwidth="0.25* 0.25* 0.25* 0.25*">
  <sthead>
    <stentry>Parameter</stentry>
    <stentry>MC-5000</stentry>
    <stentry>MC-4000</stentry>
    <stentry>MC-3000</stentry>
  </sthead>
  <strow>
    <stentry>Max Speed</stentry>
    <stentry>15,000 RPM</stentry>
    <stentry>12,000 RPM</stentry>
    <stentry>10,000 RPM</stentry>
  </strow>
</simpletable>
```

**For complex tables (6+ columns):** Force landscape layout for PDF:

```xml
<table id="wiring-specs-full" orient="land" frame="all">
  <!-- orient="land" forces landscape page in PDF -->
  <title>Complete Wiring Specifications</title>
  <tgroup cols="8">
    <!-- ... -->
  </tgroup>
</table>
```

### 6.3 Wide Table Mitigation Strategies

| Strategy | How | Works In | Trade-off |
|----------|-----|----------|-----------|
| **Split into multiple tables** | Break 8-col table into 2 × 4-col tables | All formats | Repeats header rows |
| **Rotate to landscape** | `@orient="land"` in table element | PDF only | Inconsistent with portrait pages |
| **Font reduction** | CSS `font-size: 7pt` for wide tables | HTML5 + PDF | Harder to read |
| **Truncation** | Abbreviate column headers; expand in `<desc>` | All formats | Must maintain both full and short forms |
| **Key-value transformation** | Convert horizontal columns to vertical rows | All formats | Changes table semantics |

**Decision rule:** If a table has 6+ columns, consider whether it should be restructured:

```xml
<!-- ❌ AVOID: 8-column horizontal table -->
<table>
  <tgroup cols="8">
    <thead>
      <row><entry>Part</entry><entry>MC-5000</entry><entry>MC-4000</entry>...</row>
    </thead>
  </tgroup>
</table>

<!-- ✅ PREFER: 3-column vertical (key-value) table -->
<table>
  <tgroup cols="3">
    <thead>
      <row><entry>Part</entry><entry>MC-5000</entry><entry>MC-4000</entry></row>
    </thead>
    <tbody>
      <row><entry>Spindle</entry><entry>SKU-5001</entry><entry>SKU-4001</entry></row>
    </tbody>
  </tgroup>
</table>
```

### 6.4 Automated Column Count Check

```xml
<sch:pattern id="table-column-limit">
  <sch:rule context="*[contains(@class, ' table/entry ')]">
    <sch:let name="cols" value="count(../../tgroup/col)"/>
    <sch:report test="$cols > 8">
      [T1] Table has <sch:value-of select="$cols"/> columns (max 8).
      Restructure into multiple tables or convert to key-value format.
    </sch:report>
  </sch:rule>
</sch:pattern>
```

---

## 7. Cross-Reference & Link Strategy

### 7.1 Link Types and Format Behavior

| Link Type | DITA Element | HTML5 | PDF | Mobile |
|-----------|-------------|-------|-----|--------|
| **Topic link** | `<xref href="topic.dita"/>` | Hyperlink | Page number + link | Hyperlink |
| **Key reference** | `<xref keyref="topic-key"/>` | Hyperlink | Page number | Hyperlink |
| **External URL** | `<xref href="https://..." format="html" scope="external"/>` | Hyperlink, opens new tab | URL printed in parentheses | Hyperlink |
| **Email** | `<xref href="mailto:..." format="html"/>` | Opens mail client | Email printed | Opens mail |
| **File download** | `<xref href="file.pdf" format="pdf" scope="external"/>` | Download link | Page number internal | Download |

### 7.2 Cross-Reference Authoring Rules

**Rule: Use keyref for all internal cross-references (not direct href):**

```xml
<!-- ❌ AVOID: brittle href path -->
<xref href="../reference/coolant-specs.dita">Coolant specifications</xref>

<!-- ✅ RECOMMENDED: key-based reference -->
<xref keyref="ref-coolant-specs">Coolant specifications</xref>
```

Key-based references resolve correctly in all formats because the key-to-topic mapping is defined once in the map, not scattered across topics.

**Rule: External URLs must use `@scope="external"`:**

```xml
<!-- ✅ PDF will print: "See https://examplecorp.com/support" -->
<xref href="https://examplecorp.com/support"
      format="html" scope="external">Support Portal</xref>
```

**Rule: Always provide link text (don't rely on auto-generated title):**

```xml
<!-- ❌ AVOID: auto-generates "Spindle Specifications" as link text -->
<xref keyref="ref-spindle-specs"/>

<!-- ✅ RECOMMENDED: explicit, short link text -->
<xref keyref="ref-spindle-specs">Spindle spec sheet</xref>
```

### 7.3 PDF Page Reference Handling

DITA-OT Chemistry (PDF) converts internal references to page numbers. Control this with:

```xml
<!-- In the ditamap, for PDF page reference behavior -->
<topicmeta>
  <linktext>See <keyword keyref="ref-spindle-specs"/> on page %p</linktext>
</topicmeta>
```

---

## 8. Typography & Branding Control

### 8.1 Font Strategy

| Format | Body Font | Code Font | CJK Font |
|--------|-----------|-----------|----------|
| HTML5 | System UI / web-safe | Monospace stack | System CJK |
| PDF (Print) | Noto Sans / embedded | JetBrains Mono embedded | Noto Sans SC embedded |
| Mobile (ePub) | Device default | Device monospace | Device CJK |

**Rule:** For PDF, embed fonts that match the web experience. For HTML5, use web-safe stacks with fallbacks:

```css
/* Font stacks that produce consistent visual weight across formats */
--font-primary: 'Noto Sans', 'Segoe UI', Roboto, Arial, sans-serif;
--font-jp: 'Noto Sans JP', 'Hiragino Sans', 'Yu Gothic', sans-serif;
--font-sc: 'Noto Sans SC', 'Microsoft YaHei', 'PingFang SC', sans-serif;
```

### 8.2 Branding Elements Across Formats

| Element | HTML5 | PDF | Mobile |
|---------|-------|-----|--------|
| **Logo** | `<img>` in header | `<fo:external-graphic>` in page header | Front matter only (small) |
| **Color scheme** | Full CSS colors | CMYK-converted | Device-color-mode |
| **Company name** | `<keyword keyref="company-name"/>` | Same keyref → resolved | Same |
| **Footer text** | `<footer>` element | `<fo:static-content>` in page-footer | Simplified |
| **Page numbers** | N/A (scroll) | `footer/page-number` | Device page number |

### 8.3 PDF vs. HTML5 Color Handling

Colors in PDF (CMYK print) will look different from HTML5 (RGB screen). To mitigate:

```css
/* HTML5 (screen) */
:root {
  --color-primary: #1a5276;  /* RGB: screen-optimized */
}

/* PDF (print) — use CMYK-equivalent or darker RGB */
:root {
  --color-primary: #143c58;  /* Slightly darker to compensate for print ink spread */
}
```

---

## 9. Conditional Content per Format

### 9.1 DITAVAL for Format-Specific Content

Use DITAVAL profiles to include/exclude content per format:

```xml
<!-- ditaval/html5.ditaval — for web output -->
<val>
  <prop att="audience" val="operator" action="include"/>
  <prop att="platform" val="web" action="include"/>
  <prop att="platform" val="print" action="exclude"/>
  <prop att="rev" val="draft" action="exclude"/>
</val>
```

```xml
<!-- ditaval/pdf.ditaval — for print output -->
<val>
  <prop att="audience" val="operator" action="include"/>
  <prop att="platform" val="print" action="include"/>
  <prop att="platform" val="web" action="exclude"/>
</val>
```

### 9.2 Authoring Conditional Content

```xml
<!-- Content that only appears in HTML5: interactive element -->
<p platform="web">Click the diagram to zoom. <image href="interactive-spindle.svg" width="100%"/></p>

<!-- Content that only appears in PDF: page reference -->
<p platform="print">See the spindle assembly diagram on the facing page.</p>

<!-- Content for all formats: the core information -->
<p>To inspect the spindle, remove the front cover panel.</p>
```

### 9.3 Conditional Content Validation

**Rule:** Every conditional attribute must have a matching DITAVAL rule. No bare conditionals:

```xml
<sch:pattern id="conditional-must-have-ditaval">
  <sch:rule context="*[@platform or @audience or @product]">
    <sch:report test="not(
      exists(//val/prop[@att=local-name(current()/attribute::*[1])])
    )">
      [D1] Conditional attribute '<sch:value-of select="local-name(current()/attribute::*[1])"/>'
      without matching DITAVAL rule.
    </sch:report>
  </sch:rule>
</sch:pattern>
```

---

## 10. DITA-OT Configuration for Multi-Format

### 10.1 Parallel Build Configuration

```bash
#!/bin/bash
# Multi-Format Parallel Build — build-all-formats.sh
set -euo pipefail

MAP_PATH="${1:-products/mc5000/map/mc5000-manual.ditamap}"
CSS_BASE="${2:-framework/styles/shared/base.css}"
OUTPUT_BASE="${3:-output}"

echo "=== Building all output formats in parallel ==="

# Build HTML5
(
  echo "[html5] Building..."
  dita --input="$MAP_PATH" \
       --format=html5 \
       --output="${OUTPUT_BASE}/web" \
       --property=args.css="${CSS_BASE}" \
       --property=args.css.param=framework/styles/html5/html5.css
  echo "[html5] Complete"
) &

# Build PDF
(
  echo "[pdf] Building..."
  dita --input="$MAP_PATH" \
       --format=pdf \
       --pdf-engine=chemistry \
       --output="${OUTPUT_BASE}/pdf" \
       --property=args.css="${CSS_BASE}" \
       --property=args.css.param=framework/styles/pdf/pdf.css
  echo "[pdf] Complete"
) &

# Build EPUB
(
  echo "[epub] Building..."
  dita --input="$MAP_PATH" \
       --format=epub \
       --output="${OUTPUT_BASE}/epub" \
       --property=args.css="${CSS_BASE}" \
       --property=args.css.param=framework/styles/mobile/epub.css
  echo "[epub] Complete"
) &

# Wait for all builds to finish
wait
echo "=== All formats built successfully ==="
```

### 10.2 Common DITA-OT Parameters Across Formats

```yaml
# Common parameters shared by ALL formats
common-args: >
  --property=args.css=framework/styles/shared/base.css
  --property=args.draft=false
  --property=args.gen.default.parent.nav=false
  --property=args.gen.task.lbl=SECTION
  --property=args.rellinks=none
  --property=args.copycss=yes
  --property=generate.toc=yes
  --property=nav-toc=full
  --property=toc.section.depth=4
  --property=figure.title.text-align=center
  --property=table.title.text-align=center
```

Using a common base ensures structural consistency. Only format-specific parameters differ:

| Parameter | HTML5 | PDF | Mobile |
|-----------|-------|-----|--------|
| `args.css` | + html5.css | + pdf.css | + epub.css |
| `args.css.path` | Relative | Absolute (PDF FO) | Relative |
| `nav-toc` | full | full | partial |
| `generate.toc` | yes | yes | yes |
| `args.rellinks` | all | none (to save space) | none |

### 10.3 Build Verification: Same Source, Different Outputs

After building all formats, verify content parity:

```bash
# build-verify-content.sh — Check content completeness across formats
#!/bin/bash

echo "=== Cross-Format Content Verification ==="

# Extract all topic titles from each output
for format in html5 pdf epub; do
  echo ""
  echo "--- $format ---"
  case $format in
    html5)
      grep -rh '<h1' output/web/ | sed 's/<[^>]*>//g' | sort > /tmp/titles-html5.txt
      ;;
    pdf)
      pdftotext output/pdf/mc5000-manual.pdf /tmp/pdf-text.txt
      grep "^[0-9]" /tmp/pdf-text.txt | head -50 > /tmp/titles-pdf.txt
      ;;
    epub)
      unzip -p output/epub/mc5000-manual.epub "*.xhtml" | grep '<h1' | sed 's/<[^>]*>//g' | sort > /tmp/titles-epub.txt
      ;;
  esac
done

# Compare title lists across formats
echo ""
echo "Title count:"
wc -l /tmp/titles-*.txt

# Check for titles missing in any format
echo ""
echo "Titles in HTML5 but missing in PDF:"
diff /tmp/titles-html5.txt /tmp/titles-pdf.txt | grep '^<' || echo "None"
```

---

## 11. Automated Cross-Format Testing

### 11.1 What to Test

| Test | What It Detects | Tool | Acceptable |
|------|----------------|------|------------|
| **Content parity** | Missing content in one format | Title diff across outputs | Zero missing |
| **Link validation** | Broken references in each format | Link checker per output | Zero broken |
| **Visual regression** | Unexpected layout changes | Pixel-level diff (HTML5) | < 5% change |
| **Table consistency** | Tables identical in all formats | Row/col count extraction | Identical |
| **Image presence** | Missing images in any format | File existence check | Zero missing |
| **Font availability** | Unembedded fonts in PDF | pdffonts | Zero missing |
| **File size boundary** | Output size constraints | du -sh | HTML5 < 50MB, PDF < 20MB, ePub < 10MB |

### 11.2 HTML5 Visual Regression Testing

```bash
#!/bin/bash
# Visual Regression Check — visual-regression.sh
set -euo pipefail

MAP_PATH="${1:-products/mc5000/map/mc5000-manual.ditamap}"

echo "=== Visual Regression Check ==="

# Build HTML5 from current branch
echo "[PR] Building current branch HTML5..."
dita --input="$MAP_PATH" \
     --format=html5 --output=output/pr-build

# Build HTML5 from baseline (main branch)
echo "[Baseline] Building main branch HTML5..."
git stash
dita --input="$MAP_PATH" \
     --format=html5 --output=output/baseline

# Compare with baseline
echo "[Diff] Comparing output..."
for page in output/pr-build/*.html; do
  basename=$(basename "$page")
  diff <(head -50 "output/pr-build/$basename") \
       <(head -50 "output/baseline/$basename") || true
done

echo "=== Visual regression check complete ==="
```

### 11.3 PDF Quality Gate

```bash
# PDF Quality Verification — pdf-quality-check.sh
echo "=== PDF Quality Check ==="

# Check page count is reasonable
PAGES=$(pdfinfo output/pdf/mc5000-manual.pdf | grep Pages | awk '{print $2}')
if [ "$PAGES" -lt 10 ]; then
  echo "ERROR: PDF has only $PAGES pages — likely a build failure"
  exit 1
fi

# Check all fonts are embedded
EMBEDDED=$(pdffonts output/pdf/mc5000-manual.pdf | tail -n +3 | grep -v 'yes' | wc -l)
if [ "$EMBEDDED" -gt 0 ]; then
  echo "WARNING: $EMBEDDED fonts not embedded"
fi
echo "=== PDF quality check complete ==="
```

---

## 12. Governance Rules

### 12.1 Multi-Format Publishing Checklist

Before publishing a document set to all formats:

- [ ] **Content Parity**: Build all formats; verify title lists match (see 10.3)
- [ ] **Table Check**: No table exceeds 8 columns (Schematron rule T1)
- [ ] **Image Check**: All images have `@alt` text; no oversized bitmaps
- [ ] **Link Check**: All cross-references resolve (DITA-OT build log)
- [ ] **Conditional Check**: DITAVAL profiles cover all conditional attributes used
- [ ] **CSS Sync**: Design tokens match across all format CSS files
- [ ] **PDF Font Check**: All fonts embedded in PDF output
- [ ] **File Size**: Outputs within size limits
- [ ] **Regression**: No unexpected visual changes vs. baseline

### 12.2 Format Release Cadence

| Format | Update Frequency | Approval Gate | Distribution Channel |
|--------|-----------------|---------------|---------------------|
| **HTML5** | On every merge to main | CI/CD pass | Web server (auto-deploy) |
| **PDF** | On every merge to main | CI/CD + manual spot-check | Download portal |
| **ePub** | Weekly snapshot | CI/CD pass | Mobile app / download |
| **Print (physical)** | Per product release | Full DGC approval | Physical fulfillment |

### 12.3 Breaking Format Changes

If a change breaks one format but not others:

```
1. Identify which format broke and why
   ↓
2. Fix at the source (DITA topic or CSS) — do NOT create format-specific topic variants
   ↓
3. Rebuild all formats — verify fix in all
   ↓
4. If format-specific fix is unavoidable:
   - Use DITAVAL conditional (e.g., @platform="web") as LAST resort
   - Document why in the topic metadata
   - Flag for architecture review in next DGC
```

### 12.4 Roles & Responsibilities

| Role | Responsibility |
|------|---------------|
| **Content Architect** | Defines format strategy; approves format-specific conditionals |
| **Author** | Follows multi-format authoring rules; verifies content in all formats |
| **IT/DevOps** | Maintains build pipeline for all formats; CSS token sync |
| **QA** | Runs visual regression tests; verifies content parity |
| **DGC** | Approves new format additions; reviews format strategy annually |

---

## Appendices

### A. Quick Reference Card: Authoring for All Formats

| Do | Don't |
|----|-------|
| Use `<simpletable>` for 4 or fewer columns | Use `<table>` with 9+ columns |
| Use SVG for all diagrams | Use JPEG for text-heavy images |
| Use keyref for all cross-references | Use hard-coded `href` paths |
| Provide `@alt` text on all images | Assume images will render in all formats |
| Use DITAVAL for format-conditional content | Use colored `<note>` for warnings (use `<hazardstatement>`) |
| Test in all formats before publishing | Assume "it works in HTML5 = it works everywhere" |
| Embed fonts in PDF | Rely on system fonts for PDF |
| Keep code blocks under 90 chars per line | Use long lines without wrapping strategy |

### B. Diagnostic Commands

```bash
# Check for broken images across all formats
find output/ -name "*.html" -exec grep -l "broken-image\|img-alt-missing" {} \;

# Count tables per format that exceed column limit
for fmt in output/*/; do
  echo "$fmt:"
  grep -r "<tgroup" "$fmt" | grep -o 'cols="[0-9]*"' | sort | uniq -c | sort -rn
done

# Verify font embedding in all PDFs
find output/ -name "*.pdf" -exec sh -c 'echo "$1:"; pdffonts "$1" | tail -n +3' _ {} \;
```

### C. Related Documents

| Document | Location |
|----------|----------|
| CI/CD Pipeline | [CI-CD/01-CICD-PIPELINE.md](../CI-CD/01-CICD-PIPELINE.md) |
| Style Guide | [STYLE-GUIDE/01-STYLE-GUIDE.md](../STYLE-GUIDE/01-STYLE-GUIDE.md) |
| Document Set Integrity | [WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md](../WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md) |
| Risk Control Strategy | [RISK-CONTROL-STRATEGY.md](../RISK-CONTROL-STRATEGY.md) |
