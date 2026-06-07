---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Bookmap & PDF Publishing
## Module 4 — DITA Professional (进阶)

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | Bookmap vs Ditamap | 3-5 |
| 2 | Bookmap Structure Deep Dive | 6-9 |
| 3 | Frontmatter & Backmatter | 10-13 |
| 4 | DITA-OT PDF Pipeline | 14-17 |
| 5 | Chemistry (PDF-CSS) Plugin | 18-22 |
| 6 | Page Layout Customization | 23-26 |
| 7 | Troubleshooting PDF Output | 27-29 |
| 8 | Hands-on Exercise | 30-31 |
| 9 | Summary | 32-33 |

---

## Why Bookmap Matters for Manufacturing

Manufacturing documentation requires **formal publication structures** that a flat ditamap cannot provide.

| Requirement | Ditamap | Bookmap |
|-------------|---------|---------|
| Table of Contents (auto-generated) | Manual | Automatic |
| List of Figures / Tables | Not supported | Built-in |
| Front cover / copyright page | Not supported | Built-in |
| Glossary (auto-collected) | Manual links | Automatic |
| Index (auto-generated) | Not supported | Built-in |
| Revision history / amendments | Manual | Structured slots |
| Chapter numbering | Manual | Automatic |
| Print-ready pagination | No | Yes |

---

## Bookmap vs Ditamap: Structural Comparison

```
ditamap                         bookmap
  +-- topicref (chapter)          +-- booktitle
  +-- topicref (section)          +-- bookmeta (metadata block)
  +-- topicref (subsection)       +-- frontmatter
  +-- topicref (appendix?)        |    +-- booklists (TOC, figures, tables)
                                  |    +-- preface
                                  |    +-- abbreviations
                                  +-- chapter (1..*)
                                  |    +-- topicref (sections)
                                  +-- backmatter
                                       +-- appendix
                                       +-- glossarylist
                                       +-- indexlist
                                       +-- amendments
```

---

## When to Use Each Map Type

| Use Case | Map Type | Reason |
|----------|----------|--------|
| HTML help center | Ditamap | Chunked, searchable, no pagination |
| Online knowledge base | Ditamap | Dynamic TOC, faceted navigation |
| Operator's Manual (PDF) | Bookmap | Formal structure, numbered chapters |
| Installation Guide (print) | Bookmap | Page numbers, index, TOC |
| Service Manual (PDF) | Bookmap | Front/back matter, revision control |
| Training Workbook | Bookmap | Structured lessons, glossary |
| Quick Reference Card | Ditamap | Small, single-page, no formal structure |
| Regulatory submission | Bookmap | Required formal elements (ISO/IEC 82079) |

---

## Bookmap DOCTYPE Declaration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE bookmap PUBLIC
  "-//OASIS//DTD DITA BookMap//EN"
  "bookmap.dtd">
<bookmap id="bm-dr5000-operator" xml:lang="en-US">
  <booktitle>
    <mainbooktitle>MC-5000 CNC Machining Center
      Operator's Manual</mainbooktitle>
    <booktitlealt>DR系列五轴加工中心操作手册</booktitlealt>
  </booktitle>
  <bookmeta>
    <author>ExampleCorp Technical Publications</author>
    <publisher>Example Manufacturing Co., Ltd.</publisher>
    <bookid>DOC-MC5000-OM-EN</bookid>
    <edition>Revision 3.2</edition>
    <bookchangehistory>
      <approved>
        <organization>ExampleCorp Quality Assurance</organization>
      </approved>
    </bookchangehistory>
  </bookmeta>
  ...
</bookmap>
```

---

## Bookmeta: Metadata That Drives Publishing

```xml
<bookmeta>
  <!-- Primary metadata -->
  <author>ExampleCorp Technical Publications</author>
  <author>Zhang Wei, Senior Technical Writer</author>
  <publisher>Example Manufacturing Co., Ltd.</publisher>
  <publisherinformation>
    <address>
      <locality>Suzhou</locality>
      <administrativearea>Jiangsu</administrativearea>
      <country>China</country>
      <postalcode>215000</postalcode>
    </address>
  </publisherinformation>
  <bookid>DOC-MC5000-OM-EN</bookid>
  <edition>Revision 3.2, June 2026</edition>
  <isbn>978-7-XXXX-XXXX-X</isbn>

  <!-- Copyright -->
  <bookrights>
    <copyrfirst>
      <copyrightyear year="2024"/>
      <bookowner>Example Manufacturing Co., Ltd.</bookowner>
    </copyrfirst>
    <bookrestriction value="confidential"/>
  </bookrights>
</bookmeta>
```

---

## Bookmap with Chapter Structure: Real Example

```xml
<bookmap id="bm-dr5000-operator" xml:lang="en-US">
  <booktitle>...</booktitle>
  <bookmeta>...</bookmeta>

  <!-- FRONT MATTER -->
  <frontmatter>
    <booklists>
      <toc/>
      <figurelist/>
      <tablelist/>
      <abbrevlist/>
    </booklists>
    <preface href="topics/preface.dita"/>
    <notices href="topics/safety-notices.dita"/>
  </frontmatter>

  <!-- CHAPTERS -->
  <chapter href="topics/ch01-overview.dita">
    <topicref href="topics/ch01-01-specifications.dita"/>
    <topicref href="topics/ch01-02-layout.dita"/>
    <topicref href="topics/ch01-03-control-panel.dita"/>
  </chapter>
  <chapter href="topics/ch02-safety.dita">
    <topicref href="topics/ch02-01-hazards.dita"/>
    <topicref href="topics/ch02-02-loto.dita"/>
    <topicref href="topics/ch02-03-ppe.dita"/>
  </chapter>
  <chapter href="topics/ch03-installation.dita">
    <topicref href="topics/ch03-01-foundation.dita"/>
    <topicref href="topics/ch03-02-electrical.dita"/>
    <topicref href="topics/ch03-03-pneumatic-hydraulic.dita"/>
  </chapter>
  ...
```

---

## Frontmatter Elements in Detail

| Element | Content | Auto-Generated? |
|---------|---------|-----------------|
| `toc` | Table of Contents | Yes (from chapter structure) |
| `figurelist` | List of Figures | Yes (from `<fig>` elements) |
| `tablelist` | List of Tables | Yes (from `<table>` elements) |
| `abbrevlist` | List of Abbreviations | Yes (from `<abbreviated-form>`) |
| `trademarklist` | Trademarks | Semi (from `<tm>` elements) |
| `bookabstract` | Abstract / Summary | No (authored) |
| `preface` | Preface | No (authored) |
| `dedication` | Dedication | No (authored) |
| `notices` | Legal / Safety Notices | No (authored) |

> The Framework requires **at minimum**: toc, figurelist, tablelist, notices, and preface for all MC-5000 production manuals.

---

## Backmatter Elements in Detail

```xml
<backmatter>
  <!-- Appendices -->
  <appendix href="topics/apx-a-error-codes.dita">
    <topicref href="topics/apx-a-01-70000-series.dita"/>
    <topicref href="topics/apx-a-02-80000-series.dita"/>
  </appendix>
  <appendix href="topics/apx-b-torque-specs.dita"/>
  <appendix href="topics/apx-c-wiring-diagrams.dita"/>

  <!-- Auto-generated lists -->
  <booklists>
    <glossarylist/>    <!-- Collects all glossentry keyrefs -->
    <indexlist/>       <!-- Collects all <indexterm> elements -->
    <trademarklist/>
  </booklists>

  <!-- Amendment / revision records -->
  <amendments href="topics/amendments.dita"/>
</backmatter>
```

---

## Glossarylist: Auto-Collecting Glossary Entries

The `<glossarylist/>` element in backmatter automatically collects all glossary entries referenced throughout the bookmap. This is a **game-changer** for manufacturing documentation.

**How it works:**
1. Each `glossentry` topic has a unique key in the root map
2. Content topics reference glossary terms via `<abbreviated-form keyref="gloss-..."/>`
3. At publish time, DITA-OT collects all referenced glossary keys
4. Generates a unified glossary appendix with page numbers

**Requirements for auto-collection:**
- All glossentry topics must be key-def'd in the bookmap or a sub-map
- Terms must be referenced via keyref (not text, not href)
- Terms without any reference do NOT appear in glossary (by design)

---

```xml
<!-- In the bookmap: key definitions -->
<bookmap>
  <frontmatter>
    <keydef keys="gloss-loto"
            href="glossary/gloss-loto.dita"/>
    <keydef keys="gloss-scada"
            href="glossary/gloss-scada.dita"/>
    <keydef keys="gloss-cnc"
            href="glossary/gloss-cnc.dita"/>
    <!-- ...80+ glossary entries for MC-5000... -->
  </frontmatter>
  ...
  <backmatter>
    <booklists>
      <glossarylist/>
    </booklists>
  </backmatter>
</bookmap>
```

---

## Indexlist: Auto-Generating Back-of-Book Index

`<indexlist/>` collects all `<indexterm>` elements from topics and generates a professional index with page numbers.

```xml
<!-- In a topic: index entries -->
<p>The <indexterm>coolant system</indexterm>
uses a closed-loop filtration circuit.
The <indexterm>coolant pump</indexterm>
<indexterm>pump, coolant</indexterm> is rated
for continuous operation at
<indexterm>pressure, coolant, nominal</indexterm>
5.0 bar.</p>
```

**Index conventions for MC-5000 documentation:**

| Pattern | Example | Purpose |
|---------|---------|---------|
| Noun | `coolant system` | Primary lookup |
| Inverted | `pump, coolant` | Alternate lookup |
| Qualified | `pressure, coolant, nominal` | Specific aspect |
| See also | `LOTO. See Lockout/Tagout` | Cross-reference |

---

## DITA-OT PDF Pipeline Overview

```
[Source DITA]
     |
     v
[DITA-OT Preprocess] — keyref resolution, conref expansion, filtering
     |
     v
[Merge / Normalize] — flatten topic hierarchy
     |
     v
[FO Transform] — if using legacy PDF2 (XSL-FO)
     |              OR
[CSS Transform] — if using Chemistry (PDF-CSS)
     |
     v
[FO Processor (Apache FOP / Antenna House / RenderX)]
     |              OR
[CSS Layout Engine (Chemistry / Prince / Antenna House CSS)]
     |
     v
[PDF Output]
```

---

## DITA-OT PDF Plugins Compared

| Plugin | Engine | Approach | Strengths | Weaknesses |
|--------|--------|----------|-----------|------------|
| **org.dita.pdf2** (legacy) | Apache FOP | XSL-FO | Free, stable, Mature | Difficult customization, CJK issues |
| **org.dita.pdf2.fop** | Apache FOP | XSL-FO | Same as above | Limited CSS support |
| **Chemistry (PDF-CSS)** | Custom CSS engine | CSS + JS | CSS for layout, modern | Commercial license required |
| **Antenna House** | AH Formatter | CSS + XSL-FO | Excellent CJK, fastest | Expensive ($5K+/yr) |
| **Prince XML** | Prince engine | CSS | Clean CSS, good CJK | Commercial ($500-$3,800) |

> **Framework recommendation**: Chemistry for internal PDFs (cost-effective), Antenna House for production/regulatory PDFs (superior CJK quality).

---

## Why Chemistry (PDF-CSS) for Manufacturing

Chemistry replaces arcane XSL-FO with standard CSS for PDF layout. This is revolutionary for manufacturing teams where **technical writers are not XSLT developers**.

| Legacy PDF2 (XSL-FO) | Chemistry (CSS) |
|----------------------|-----------------|
| 10,000+ lines of XSLT | ~500 lines of CSS |
| XSLT developer needed | CSS knowledge sufficient |
| Weeks to customize layout | Hours to customize |
| CJK fonts brittle | CSS `@font-face`, native CJK |
| Debug via XSLT trace | Browser dev tools first, then PDF |

**Framework CSS file**: `dita-manufacturing-framework/templates/css/dr-5000-manual.css`
- Page size: A4 (210 x 297 mm)
- Margins: 25mm all sides (20mm binding offset for left pages)
- Font: Noto Sans (body), Noto Sans CJK SC (zh-CN), Noto Mono (code)

---

## Chemistry Plugin Installation & Setup

```bash
# 1. Download Chemistry plugin
# From: https://www.xmlrocks.com/chemistry/

# 2. Install into DITA-OT
cd /opt/dita-ot-4.x
./bin/dita --install /path/to/com.xmlrocks.chemistry.zip

# 3. Verify installation
./bin/dita --plugins | grep chemistry
# Expected: com.xmlrocks.chemistry (4.x.x)

# 4. Copy framework CSS to Chemistry config
cp dita-manufacturing-framework/templates/css/dr-5000-manual.css \
   /opt/dita-ot-4.x/plugins/com.xmlrocks.chemistry/css/

# 5. Run PDF build with Chemistry
./bin/dita -i bookmaps/bm-dr5000-operator.ditamap \
           -f chemistry-pdf \
           -o output/pdf/operator-manual
```

---

## Chemistry CSS: Page Layout Basics

```css
/* dr-5000-manual.css */
@page {
  size: A4;
  margin: 25mm 25mm 25mm 25mm;

  @top-left {
    content: string(chaptertitle);
    font-size: 8pt;
    color: #666;
    font-family: "Noto Sans", sans-serif;
  }

  @top-right {
    content: "MC-5000 Operator's Manual";
    font-size: 8pt;
    color: #666;
  }

  @bottom-center {
    content: counter(page);
    font-size: 9pt;
    font-family: "Noto Sans", sans-serif;
  }
}

@page :left {
  margin-left: 45mm;  /* 25mm + 20mm binding */
  @top-left {
    content: string(chaptertitle);
  }
}

@page :right {
  margin-right: 25mm;
  @top-right {
    content: "MC-5000 Operator's Manual";
  }
}
```

---

## Chemistry CSS: Cover Page

```css
@page cover {
  @top-left { content: none; }
  @top-right { content: none; }
  @bottom-center { content: none; }
  background-color: #1a3a5c;  /* ExampleCorp Navy */
}

*[class~="bookmap/frontpage"] {
  page: cover;
  color: white;
  text-align: center;
  padding-top: 80mm;
}

*[class~="bookmap/frontpage"] .mainbooktitle {
  font-size: 28pt;
  font-weight: bold;
  font-family: "Noto Sans CJK SC", "Noto Sans", sans-serif;
  margin-bottom: 20mm;
}

*[class~="bookmap/frontpage"] .booktitlealt {
  font-size: 18pt;
  color: #a0c4e8;
  font-family: "Noto Sans CJK SC", sans-serif;
}

*[class~="bookmap/frontpage"] .publisher {
  font-size: 12pt;
  margin-top: 40mm;
  border-top: 1px solid #a0c4e8;
  padding-top: 10mm;
}
```

---

## Chemistry CSS: Chapter & Heading Styling

```css
/* Chapter title pages */
*[class~="topic/topic"][is-chapter] {
  break-before: page;
  page-break-before: always;
}

*[class~="topic/title"][is-chapter] {
  font-size: 24pt;
  color: #1a3a5c;
  font-weight: bold;
  border-bottom: 3px solid #d42114;  /* ExampleCorp Red accent */
  padding-bottom: 8mm;
  string-set: chaptertitle self;
  margin-top: 40mm;
}

/* Section headings */
h2 { font-size: 16pt; color: #2a4a6c; }
h3 { font-size: 13pt; color: #3a5a7c; }
h4 { font-size: 11pt; color: #4a6a8c; }

/* Note styling */
*[class~="note/note"] {
  border-left: 4px solid #d42114;
  padding-left: 6mm;
  margin: 8mm 0;
  background-color: #fff5f5;
}
```

---

## Chemistry CSS: Table & Figure Styling

```css
/* Tables */
table {
  border-collapse: collapse;
  width: 100%;
  margin: 6mm 0;
  font-size: 9pt;
}

th {
  background-color: #1a3a5c;
  color: white;
  padding: 3mm 4mm;
  text-align: left;
  font-weight: bold;
}

td {
  padding: 2mm 4mm;
  border-bottom: 1px solid #ddd;
}

tr:nth-child(even) td {
  background-color: #f5f7fa;
}

/* Figures */
figcaption, *[class~="fig/fig"] > *[class~="fig/title"] {
  font-size: 9pt;
  font-style: italic;
  color: #666;
  text-align: center;
  margin-top: 3mm;
}
```

---

## Chemistry CSS: CJK Font Configuration

```css
/* CJK font face definitions */
@font-face {
  font-family: "Noto Sans CJK SC";
  src: url("fonts/NotoSansCJKsc-Regular.otf");
  font-weight: normal;
}

@font-face {
  font-family: "Noto Sans CJK SC";
  src: url("fonts/NotoSansCJKsc-Bold.otf");
  font-weight: bold;
}

/* CJK-aware line height and spacing */
:root[xml\:lang="zh-CN"] {
  font-family: "Noto Sans CJK SC", "SimSun", sans-serif;
  line-height: 1.8;         /* CJK needs more line height */
  text-align: justify;       /* CJK commonly justified */
  hanging-punctuation: allow-end;
}

/* CJK headings */
:root[lang="zh-CN"] *[class~="topic/title"] {
  font-size: 22pt;           /* Slightly adjusted for CJK glyph size */
  font-weight: bold;
  letter-spacing: 0.05em;    /* Slight tracking for readability */
}
```

---

## Chemistry CSS: Hazard Statement Rendering

```css
/* Hazard statement severity-based styling */
*[class~="hazardstatement/hazardstatement"][type="danger"] {
  border: 3px solid #cc0000;
  background-color: #fff0f0;
  padding: 6mm;
  margin: 8mm 0;
}

*[class~="hazardstatement/hazardstatement"][type="warning"] {
  border: 3px solid #ff6600;
  background-color: #fff5e6;
  padding: 6mm;
  margin: 8mm 0;
}

*[class~="hazardstatement/hazardstatement"][type="caution"] {
  border: 3px solid #ffcc00;
  background-color: #fffde6;
  padding: 6mm;
  margin: 8mm 0;
}

*[class~="typeofhazard/typeofhazard"] {
  font-weight: bold;
  font-size: 11pt;
  text-transform: uppercase;
}

*[class~="consequence/consequence"]::before {
  content: "⚠ ";
  color: #cc0000;
}
```

---

## Running the PDF Build

```bash
# Standard Chemistry PDF build
dita -i bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/pdf/operator-manual-en \
     -Dpdf.css=../../../templates/css/dr-5000-manual.css

# With filtering (English only)
dita -i bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/pdf/operator-manual-en \
     -Dpdf.css=../../../templates/css/dr-5000-manual.css \
     --filter=ditavals/en-only.ditaval

# With filtering + draft-comment removal
dita -i bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/pdf/operator-manual-en \
     -Dpdf.css=../../../templates/css/dr-5000-manual.css \
     --filter=ditavals/en-only.ditaval \
     --args.draft=no

# Chinese build (different CSS with CJK fonts)
dita -i bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/pdf/operator-manual-zh \
     --filter=ditavals/zh-only.ditaval \
     -Dpdf.css=../../../templates/css/dr-5000-manual-zh.css
```

---

## Page Layout Customization: A4 vs US Letter

ExampleCorp serves both domestic (China) and export markets. Page size is market-dependent.

```css
/* China / EU market: A4 */
@page {
  size: A4;  /* 210 x 297 mm */
  margin: 25mm 25mm 25mm 25mm;
}

/* US market: US Letter */
/* In a separate CSS file: dr-5000-manual-us.css */
@page {
  size: letter;  /* 8.5 x 11 in = 215.9 x 279.4 mm */
  margin: 1in 1in 1in 1in;
}
```

**Build matrix for multi-market PDFs:**

| Market | Page Size | Language(s) | CSS File |
|--------|-----------|-------------|----------|
| China domestic | A4 | zh-CN | `dr-5000-manual-zh.css` |
| EU | A4 | en, de, fr, es | `dr-5000-manual-en.css` |
| North America | US Letter | en | `dr-5000-manual-us.css` |
| Japan | A4 | ja-JP | `dr-5000-manual-ja.css` |
| Southeast Asia | A4 | en, th, vi | `dr-5000-manual-en.css` |

---

## Customizing Headers & Footers with Running Headers

Chemistry supports CSS running headers for dynamic chapter titles.

```css
/* Capture chapter title as a string */
*[class~="topic/title"][is-chapter] {
  string-set: chaptertitle self, chapter-number counter(chapter);
}

/* Use captured string in page header */
@page :right {
  @top-right {
    content: string(chaptertitle);
    font-size: 8pt;
    color: #666;
    font-style: italic;
  }
}

@page :left {
  @top-left {
    content: "Chapter " string(chapter-number) ": " string(chaptertitle);
    font-size: 8pt;
    color: #666;
  }
}

/* First page of each chapter: no header */
@page chapter-first {
  @top-left { content: none; }
  @top-right { content: none; }
}

*[class~="topic/topic"][is-chapter] {
  page: chapter-first;
}
```

---

## Watermarks & Draft Markings

```css
/* DRAFT watermark */
@page {
  @watermark {
    content: "DRAFT — FOR INTERNAL REVIEW ONLY";
    font-size: 72pt;
    color: rgba(255, 0, 0, 0.08);
    transform: rotate(-30deg);
    text-align: center;
    vertical-align: middle;
  }
}

/* Confidential marking on every page */
@page {
  @top-right-corner {
    content: "CONFIDENTIAL";
    font-size: 6pt;
    color: #cc0000;
    text-transform: uppercase;
    font-weight: bold;
    border: 1px solid #cc0000;
    padding: 1mm 3mm;
  }
}

/* To disable for production: swap CSS file */
/* build-dev uses: dr-5000-manual-draft.css */
/* build-prod uses: dr-5000-manual.css (no watermark) */
```

---

## Troubleshooting PDF Output: Common Issues

| Symptom | Likely Cause | Solution |
|---------|-------------|----------|
| Missing text / empty pages | Font not installed on build server | Install CJK fonts: `apt install fonts-noto-cjk` |
| CJK characters as tofu (□□□) | Font missing in DITA-OT font config | Add `@font-face` to CSS or configure `font-mappings.xml` |
| Broken table across pages | Default pagination | Add `page-break-inside: avoid` to `table` selector |
| Images overflow page | No `max-width` constraint | Add `img { max-width: 100%; height: auto; }` |
| Page numbers restart mid-document | Incorrect page counter | Check for stray `counter-reset: page` in CSS |
| TOC empty | No chapters marked `is-chapter` | Verify `@outputclass` or `chunk` attributes |
| Glossary empty | Glossentries not keyref'd | Add `<glossref>` or `<keydef>` to bookmap |

---

## Debugging DITA-OT PDF Builds

```bash
# 1. Run with verbose logging
dita -i bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/pdf/ \
     -v -Dpdf.css=../../../templates/css/dr-5000-manual.css \
     2>&1 | tee build.log

# 2. Check for missing topics
grep "\[DOTJ013E\]" build.log           # Missing file references
grep "\[DOTJ036W\]" build.log           # Missing keyrefs
grep "\[DOTX008E\]" build.log           # XSLT errors

# 3. Inspect intermediate FO/CSS output
ls output/pdf/stage1.xml                # Merged XML (pre-FO)
ls output/pdf/stage2.fo                 # XSL-FO (legacy PDF2)
ls output/pdf/topic.fo                  # Individual topic FO

# 4. Chemistry: use --args.debug-chemistry for CSS debug output
dita -i bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/pdf/ \
     --args.debug-chemistry=yes
```

---

## Automating Multi-Bookmap PDF Builds

The framework typically has **4-6 bookmaps per machine model**. A shell script orchestrates all builds.

```bash
#!/bin/bash
# build-all-pdfs.sh — Batch PDF builder for MC-5000
set -e

DITA_HOME="/opt/dita-ot-4.x"
BOOKMAPS=(
  "bookmaps/bm-dr5000-operator.ditamap"
  "bookmaps/bm-dr5000-installation.ditamap"
  "bookmaps/bm-dr5000-service.ditamap"
  "bookmaps/bm-dr5000-parts-catalog.ditamap"
  "bookmaps/bm-dr5000-training.ditamap"
)
LANGUAGES=("en-US" "zh-CN")
OUTPUT_BASE="output/pdf"

for lang in "${LANGUAGES[@]}"; do
  CSS="templates/css/dr-5000-manual-${lang%%.*}.css"
  DITAVAL="ditavals/${lang}-only.ditaval"
  for bm in "${BOOKMAPS[@]}"; do
    name=$(basename "$bm" .ditamap)
    echo "Building: $name ($lang)"
    "$DITA_HOME/bin/dita" \
      -i "$bm" \
      -f chemistry-pdf \
      -o "$OUTPUT_BASE/$name-$lang" \
      -Dpdf.css="$CSS" \
      --filter="$DITAVAL" \
      --args.draft=no
  done
done
echo "All PDFs built. Output: $OUTPUT_BASE/"
```

---

## PDF Quality Checklist

| Check | Criterion | Tool/Method |
|-------|-----------|-------------|
| **Page count** | Matches expected (no missing/spurious pages) | PDF reader |
| **TOC accuracy** | All entries jump to correct page | Manual click-through |
| **Figure/Table lists** | All numbered, correct titles and pages | Manual verification |
| **Cross-references** | All xref links work (internal and external) | Click in PDF viewer |
| **CJK rendering** | No tofu, correct glyphs, proper line breaks | Visual scan entire doc |
| **Font embedding** | All fonts embedded in PDF | File > Properties > Fonts |
| **Print test** | Print 5 random pages; check margins, colors | Physical printer |
| **File size** | < 50 MB for operator manual, < 100 MB for service | `ls -lh` |
| **PDF/A compliance** | For archival: PDF/A-2b or PDF/A-3 | VeraPDF validator |
| **Bookmarks** | PDF bookmarks match TOC structure | PDF reader bookmark pane |

---

## Performance Tuning for Large Bookmaps

A full MC-5000 Service Manual can be **800+ pages**. Build performance matters.

| Technique | Impact | Implementation |
|-----------|--------|----------------|
| **Incremental build** | 5-10x faster for single-topic changes | Use `--args.input` + `--args.filter` |
| **Image pre-optimization** | Reduces PDF size 40-60% | Run `pngquant` before build |
| **Font subsetting** | Reduces PDF size 30-50% | Chemistry option: `--args.font-subset=true` |
| **Parallel builds** | N builds simultaneously for N languages | GNU Parallel or CI matrix |
| **Cache DITA-OT temp** | Avoid re-processing unchanged topics | Use `--temp` with persistent dir |
| **RAM allocation** | Prevent OOM on large builds | `export ANT_OPTS="-Xmx4g"` |

```bash
# Parallel build for 5 languages (GNU Parallel)
parallel -j 4 \
  "dita -i bookmaps/bm-dr5000-service.ditamap \
        -f chemistry-pdf \
        -o output/pdf/service-{} \
        --filter=ditavals/{}-only.ditaval" \
  ::: en-US zh-CN de-DE ja-JP ko-KR
```

---

## PDF/A Archiving for Compliance

ISO and regulatory bodies increasingly require PDF/A for long-term archival.

```css
/* Chemistry: PDF/A-2b configuration */
@chemistry {
  pdf-a: 2b;
  pdf-a-metadata: true;
  pdf-a-color-profile: "sRGB IEC61966-2.1";
}

/* Required metadata for PDF/A */
@metadata {
  dc:title: "MC-5000 CNC Operator's Manual";
  dc:creator: "ExampleCorp Technical Publications";
  dc:subject: "CNC Machining Center";
  dc:publisher: "Example Manufacturing Co., Ltd.";
  dc:language: "en-US";
  xmp:CreateDate: "2026-06-07";
  xmp:CreatorTool: "DITA-OT 4.x + Chemistry";
}
```

**Compliance requirements for MC-5000:**
- CE marking submission: PDF/A-2b (EU Machinery Directive 2006/42/EC)
- UL submission: PDF/A-2b (UL 508A)
- China GB submission: PDF/A or paper (GB 5226.1)

---

<!-- _class: exercise -->

## Exercise: Build a Complete Bookmap

**Task**: Create a bookmap for a "MC-5000 Quick Start Guide" and build it to PDF.

**Requirements:**
1. Create `bm-dr5000-quickstart.ditamap` with bookmap DOCTYPE
2. Include: `booktitle` (English + Chinese), `bookmeta` (author, edition, bookid)
3. Frontmatter: `toc`, `preface` (safety summary), `notices`
4. Structure: 3 chapters (Unboxing & Setup, First Power-On, Basic Operation)
5. Each chapter must have at least 2 topicref sub-topics
6. Backmatter: `glossarylist`, `indexlist`
7. Write a 100-line Chemistry CSS file with:
   - A4 page layout
   - Running headers (chapter title)
   - Danger/Warning/Caution styling
   - Custom cover page (dark background, white text)
8. Build with: `dita -i bookmaps/bm-dr5000-quickstart.ditamap -f chemistry-pdf`
9. Verify: TOC entries, page numbers, font embedding

**Time**: 40 minutes

---

<!-- _class: keypoint -->

## Key Takeaways

1. **Bookmap > Ditamap** for formal publications: automatic TOC, figure/table lists, glossary, index, front/back matter
2. **Structure**: booktitle + bookmeta + frontmatter + chapters + backmatter (appendices, glossary, index, amendments)
3. **Chemistry (PDF-CSS)** replaces XSL-FO with standard CSS: faster customization, better CJK support, lower skill barrier
4. **`@page` rules** control page size (A4/US Letter), margins, running headers/footers, watermarks
5. **CJK font configuration**: `@font-face` with Noto Sans CJK, language-specific CSS files, adjusted line-height and letter-spacing
6. **Automation**: Shell scripts or CI pipelines batch-build all bookmaps across all target languages
7. **PDF/A compliance** required for regulatory submissions; Chemistry supports PDF/A-2b out of the box
8. **Performance tuning**: Incremental builds, parallel language builds, font subsetting, image pre-optimization
