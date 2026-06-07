---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Multilingual Publishing
## Module 7 -- DITA Professional

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | The Multi-Language Publishing Challenge | 3-5 |
| 2 | Batch Publishing: 9 Languages at Once | 6-9 |
| 3 | DITA-OT Multi-Language Configuration | 10-14 |
| 4 | CJK Font Configuration Deep Dive | 15-18 |
| 5 | Per-Language vs All-in-One Strategy | 19-21 |
| 6 | GitHub Actions Automation | 22-25 |
| 7 | Performance Optimization | 26-28 |
| 8 | Hands-on Exercise | 29-30 |
| 9 | Summary | 31-32 |

---

## The Multi-Language Reality

ExampleCorp ships MC-5000 machines to **15+ countries** across **4 continents**. Every machine requires documentation in the destination country language.

| Challenge | Scale | Consequence |
|---|---|---|
| 9 target languages | 5 bookmaps x 9 = 45 PDFs | Manual builds: 2+ days |
| CJK + Latin + Thai scripts | 3 font systems | Missing glyphs, illegible text |
| Language text expansion | DE 30% > EN, ES 25% > EN | Layout break, table overflow |
| Regulatory variance | Different required sections | Non-compliance, rejected shipments |
| Incremental updates | 10-20 topics change per sprint | Need partial rebuilds |
| CI/CD integration | Every commit triggers validation | Must finish < 15 minutes |

---

## The 9-Language Framework

| Tier | Languages | Markets | Priority |
|---|---|---|---|
| **Tier 1** | en-US, zh-CN, de-DE | US, China, Germany | Full docs day 0 |
| **Tier 2** | ja-JP, ko-KR, pt-BR | Japan, Korea, Brazil | Full docs week 1 |
| **Tier 3** | th-TH, fr-FR, es-ES | Thailand, France, Spain | Core docs week 2 |

```
Build Matrix: 5 bookmaps x 9 languages = 45 PDFs per release
With 3 output formats: 135 total deliveries
```

---

## Multi-Language Strategy: Three Approaches

| Approach | Description | Best For |
|---|---|---|
| **Single-map, multi-lang** | One map, language via `xml:lang` + ditaval | < 3 languages, small doc sets |
| **Per-language maps** | Separate bookmap per language | 3-9 languages, independent teams |
| **Hybrid: shared structure** | One map template, per-language topics | ExampleCorp's approach -- scales cleanly |

**ExampleCorp's hybrid approach**: One canonical map structure, content lives in per-language directories. Build script swaps the content root per target language.

---

## Hybrid Architecture in Practice

```
content/
  en-US/                        # Source language
    bookmaps/
      bm-dr5000-operator.ditamap
    topics/
      ch01-overview.dita
      ch02-safety.dita

  zh-CN/                        # Chinese
    topics/
      ch01-overview.dita        # Translated
      ch02-safety.dita

  de-DE/                        # German
    topics/
      ch01-overview.dita

templates/
  bookmaps/
    bm-dr5000-operator-template.ditamap  # @href placeholders
```

The template map uses relative paths. At build time, the script copies the template into each language directory, and DITA-OT resolves `@href` relative to the map.

---

## Batch Publishing Script: Configuration

```bash
#!/bin/bash
# publish-all-languages.sh -- Full release pipeline
set -euo pipefail

DITA_HOME="/opt/dita-ot-4.x"
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
RELEASE_DIR="releases/$TIMESTAMP"
mkdir -p "$RELEASE_DIR"

declare -A LANG_CSS=(
  [en-US]="dr-5000-manual-latin.css"
  [zh-CN]="dr-5000-manual-cjk.css"
  [de-DE]="dr-5000-manual-de.css"
  [ja-JP]="dr-5000-manual-cjk.css"
  [ko-KR]="dr-5000-manual-cjk.css"
  [th-TH]="dr-5000-manual-thai.css"
  [pt-BR]="dr-5000-manual-latin.css"
  [fr-FR]="dr-5000-manual-fr.css"
  [es-ES]="dr-5000-manual-latin.css"
)

BOOKMAPS=(
  "bm-dr5000-operator"
  "bm-dr5000-installation"
  "bm-dr5000-service"
  "bm-dr5000-parts-catalog"
  "bm-dr5000-training"
)
```

---

## Batch Publishing Script: Build Loop

```bash
TOTAL=$((${#LANG_NAMES[@]} * ${#BOOKMAPS[@]}))
CURRENT=0

for lang in "${!LANG_CSS[@]}"; do
  if [ ! -d "content/$lang" ]; then
    echo "SKIP: content/$lang/ not found"
    continue
  fi
  for bm in "${BOOKMAPS[@]}"; do
    CURRENT=$((CURRENT + 1))
    echo "[$CURRENT/$TOTAL] Building $lang: $bm"

    CSS="templates/css/${LANG_CSS[$lang]}"

    "$DITA_HOME/bin/dita" \
      -i "content/$lang/bookmaps/${bm}.ditamap" \
      -f chemistry-pdf \
      -o "$RELEASE_DIR/$lang/$bm" \
      -Dpdf.css="$CSS" \
      --filter="ditavals/$lang.ditaval" \
      2>&1 | tee "$RELEASE_DIR/$lang/${bm}-build.log"

    if [ -f "$RELEASE_DIR/$lang/$bm.pdf" ]; then
      echo "  OK: $bm.pdf ($(stat -c%s "$RELEASE_DIR/$lang/$bm.pdf") bytes)"
    else
      echo "  FAIL: $bm.pdf not generated!"
    fi
  done
done
```

---

## Per-Language Build Script

```bash
#!/bin/bash
# build-language.sh -- Build one language
LANG="$1"
DITA_HOME="${DITA_HOME:-/opt/dita-ot-4.x}"

case "$LANG" in
  zh-CN|ja-JP|ko-KR) CSS="cjk" ;;
  th-TH)             CSS="thai" ;;
  de-DE)             CSS="de" ;;
  fr-FR)             CSS="fr" ;;
  *)                 CSS="latin" ;;
esac

mkdir -p "output/$LANG/pdf"

"$DITA_HOME/bin/dita" \
  -i "content/$LANG/bookmaps/bm-dr5000-operator.ditamap" \
  -f chemistry-pdf \
  -o "output/$LANG/pdf/" \
  -Dpdf.css="templates/css/dr-5000-manual-${CSS}.css" \
  --filter="ditavals/$LANG.ditaval" \
  --args.draft=no \
  2>&1 | tee "output/$LANG/build.log"
```

---

## DITA-OT Multi-Language Configuration

DITA-OT handles multi-language through three mechanisms:

| Mechanism | Config Location | Purpose |
|---|---|---|
| **xml:lang** | Topic root attribute | Language identification |
| **ditaval filtering** | `ditavals/LANG.ditaval` | Include/exclude per language |
| **CSS per language** | `templates/css/*.css` | Font, spacing, typography |

**DITA-OT pipeline for multi-language:**

```
Source: content/zh-CN/bookmaps/bm.ditamap
  |
  +-- ditaval: zh-CN.ditaval (include xml:lang="zh-CN")
  +-- CSS: manuals/zh-CN.css (Noto Sans CJK SC)
  +-- Output: output/zh-CN/pdf/bm.pdf
```

---

## Configuring xml:lang Across Content

```xml
<!-- Topic-level xml:lang -->
<task id="task-coolant-change" xml:lang="de-DE">
  <title>Kuhlmittel wechseln</title>
</task>

<!-- Element-level override for inline code -->
<task id="task-software-update" xml:lang="de-DE">
  <title>Software-Aktualisierung</title>
  <steps>
    <step><cmd>Geben Sie folgenden Befehl ein:</cmd>
      <info>
        <codeblock xml:lang="en-US" outputclass="language-bash">
          sudo systemctl restart dr5000-cnc
        </codeblock>
      </info>
    </step>
  </steps>
</task>
```

---

## xml:lang Validation Rules

| Rule | Description | Severity |
|---|---|---|
| Every topic root must have `xml:lang` | Non-negotiable | Blocking error |
| `xml:lang` must match BCP 47 | `zh-CN`, NOT `zh_CN`, `chinese` | Blocking error |
| Language must be on approved list | One of the 9 supported | Warning |
| Code blocks may differ from parent | Allow `en-US` in non-English topics for code | OK |
| Directory-language match | `content/de-DE/topics/` means `xml:lang="de-DE"` | Blocking error |

**Schematron check:**

```xml
<pattern id="LANG-001">
  <rule context="*[contains(@class, ' topic/topic ')]">
    <assert test="contains(document-uri(/), @xml:lang)"
      role="error">Language mismatch: xml:lang does not match
      directory path</assert>
  </rule>
</pattern>
```

---

## Installing CJK Fonts on Build Servers

```bash
#!/bin/bash
# Debian/Ubuntu
apt-get update && apt-get install -y \
  fonts-noto-cjk fonts-noto-cjk-extra fonts-noto-color-emoji

# Verify
fc-list :lang=zh | head -5
fc-list :lang=ja | head -5
fc-list :lang=ko | head -5
```

**Docker-based build:**

```dockerfile
FROM ubuntu:24.04
RUN apt-get update && apt-get install -y \
    fonts-noto-cjk fonts-noto-cjk-extra default-jre \
    && fc-cache -fv
RUN fc-list :lang=zh | wc -l  # Must be > 0
```

---

## CJK Font CSS Configuration

```css
/* Simplified Chinese */
@font-face {
  font-family: "CJK Body";
  src: url("../fonts/NotoSansCJKsc-Regular.otf");
  font-weight: normal;
}
@font-face {
  font-family: "CJK Body";
  src: url("../fonts/NotoSansCJKsc-Bold.otf");
  font-weight: bold;
}

/* Japanese */
@font-face {
  font-family: "CJK JP Body";
  src: url("../fonts/NotoSansCJKjp-Regular.otf");
}

/* Korean */
@font-face {
  font-family: "CJK KR Body";
  src: url("../fonts/NotoSansCJKkr-Regular.otf");
}

:root[xml\:lang="zh-CN"] {
  font-family: "CJK Body", "SimSun", sans-serif;
  line-height: 1.8;
  text-align: justify;
}
```

---

## CJK Typography Rules

```css
:root[xml\:lang="zh-CN"],
:root[xml\:lang="ja-JP"],
:root[xml\:lang="ko-KR"] {

  /* Prevent line break before punctuation */
  line-break: strict;

  /* Allow hanging punctuation at line ends */
  hanging-punctuation: allow-end;

  /* Distribute space between characters */
  text-justify: inter-character;

  /* No italics for CJK -- use emphasis marks */
  em {
    font-style: normal;
    text-emphasis: filled dot;
  }
}
```

---

## CJK vs Latin: Key Differences

| Property | Latin Fonts | CJK Fonts |
|---|---|---|
| Glyph count | ~200-400 | 8,000-20,000+ |
| Font file size | ~100-500 KB | 5-15 MB per weight |
| Line height | 1.2-1.5x | 1.6-1.8x |
| Word wrapping | Space-delimited | Character-delimited |
| Bold rendering | Separate font | Separate font (no synthetic) |
| Italic | Native glyphs | Rare; synthetic oblique |

---

## Language-Specific Styling: German

```css
/* German text is ~30% longer than English */
:root[xml\:lang="de-DE"] {
  font-family: "Noto Sans", "Arial", sans-serif;
  font-size: 9.5pt;           /* Smaller to fit expansion */
  line-height: 1.5;
  hyphenate: auto;
  hyphenate-limit-chars: 6 3 2;
}

:root[xml\:lang="de-DE"] table {
  font-size: 8.5pt;           /* Table text even smaller */
}
```

## Language-Specific: Thai

```css
:root[xml\:lang="th-TH"] {
  font-family: "Noto Sans Thai", "Leelawadee UI", sans-serif;
  line-height: 2.0;           /* Generous spacing */
  font-size: 11pt;            /* Slightly larger for legibility */
  word-break: normal;         /* Dictionary-based breaking */
  overflow-wrap: anywhere;
}
```

---

## Per-Language vs All-in-One Strategy

| Factor | Per-Language | All-in-One |
|---|---|---|
| **Map management** | One map per language | One master map |
| **Build complexity** | Many independent builds | Single build, complex config |
| **Incremental updates** | Build only changed languages | Rebuild everything |
| **Font management** | Per-build CSS selection | CSS with lang selectors |
| **CI/CD parallelism** | Matrix builds naturally | Single job, slower |
| **Recommendation** | **ExampleCorp's approach** for 3+ languages | Small projects, < 3 languages |

**ExampleCorp uses per-language maps with a shared template** -- best of both worlds. One template to maintain, independent builds per language.

---

## Fallback Language Strategy

When content is not yet translated:

| Level | Behavior | Configured Via |
|---|---|---|
| **Exact match** | Use content with matching `xml:lang` | Default |
| **Language family** | `pt-BR` falls back to `pt` | ditaval `@props` |
| **Primary fallback** | Non-English falls back to `en-US` | ditaval chain |
| **Placeholder** | "Not yet translated in LANGUAGE" | Custom XSLT |

```xml
<!-- ditaval: fallback chain for Portuguese -->
<val>
  <prop action="include" att="xml:lang" val="pt-BR"/>
  <prop action="include" att="xml:lang" val="pt"/>
  <prop action="include" att="xml:lang" val="en-US"/>
</val>
```

---

## GitHub Actions: Matrix Build

```yaml
name: Multilingual Publish

on:
  push:
    tags: ['v*']
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        language: ['en-US','zh-CN','de-DE','ja-JP','ko-KR']
        bookmap:
          - bm-dr5000-operator
          - bm-dr5000-service
      max-parallel: 4

    steps:
      - uses: actions/checkout@v4

      - name: Install CJK Fonts
        run: sudo apt-get install -y fonts-noto-cjk fonts-noto-cjk-extra

      - name: Setup DITA-OT
        run: |
          wget -q https://github.com/dita-ot/dita-ot/releases/download/4.x/dita-ot-4.x.zip
          unzip -q dita-ot-4.x.zip -d /opt/
          echo "DITA_HOME=/opt/dita-ot-4.x" >> $GITHUB_ENV

      - name: Build ${{ matrix.language }} / ${{ matrix.bookmap }}
        run: bash tools/build-single.sh "${{ matrix.language }}" "${{ matrix.bookmap }}"
```

---

## GitHub Actions: Artifacts and Release

```yaml
      - name: Upload PDF artifact
        uses: actions/upload-artifact@v4
        with:
          name: pdf-${{ matrix.language }}-${{ matrix.bookmap }}
          path: output/${{ matrix.language }}/*.pdf

  create-release:
    needs: build
    runs-on: ubuntu-latest
    if: startsWith(github.ref, 'refs/tags/v')
    steps:
      - uses: actions/download-artifact@v4
        with:
          path: all-pdfs/

      - name: Create ZIP per language
        run: |
          for lang in en-US zh-CN de-DE ja-JP ko-KR; do
            zip -j "MC5000-Docs-${lang}.zip" all-pdfs/pdf-${lang}-*/*.pdf
          done

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          files: MC5000-Docs-*.zip
          body: |
            MC-5000 Documentation Release
            Languages: en-US, zh-CN, de-DE, ja-JP, ko-KR
```

---

## Multi-Format Build Matrix

| Format | Transtype | Purpose | All Languages? |
|---|---|---|---|
| **PDF** | `chemistry-pdf` | Print, regulatory | Yes |
| **HTML5** | `html5` | Online help, knowledge base | Yes |
| **HTMLHelp** | `htmlhelp` | Windows kiosk | Tier 1 only |
| **Eclipse Help** | `eclipsehelp` | MES/SCADA integration | en-US, zh-CN |
| **Markdown** | `markdown` | Git-based review | en-US (source) |
| **JSON** | Custom plugin | HMI API delivery | Tier 1 only |

---

## Performance Optimization: Incremental Builds

```bash
#!/bin/bash
# incremental-build.sh -- Build only affected languages
CHANGED=$(git diff --name-only HEAD~1)

AFFECTED_LANGS=""
for f in $CHANGED; do
  if [[ $f =~ ^content/([^/]+)/topics/.*\.dita$ ]]; then
    lang="${BASH_REMATCH[1]}"
    AFFECTED_LANGS="$AFFECTED_LANGS $lang"
  fi
done

# Also rebuild en-US if templates/CSS changed
if echo "$CHANGED" | grep -qE "(templates/|ditavals/|\.css$)"; then
  AFFECTED_LANGS="$AFFECTED_LANGS en-US"
fi

# Deduplicate
AFFECTED_LANGS=$(echo "$AFFECTED_LANGS" | tr ' ' '\n' | sort -u)

for lang in $AFFECTED_LANGS; do
  bash tools/build-language.sh "$lang"
done
```

---

## Performance: Parallel Builds with GNU Parallel

```bash
# Build all 9 languages in parallel (max 4 concurrent)
parallel -j 4 \
  "dita -i content/{1}/bookmaps/{2}.ditamap \
        -f chemistry-pdf \
        -o output/{1}/pdf/{2}/ \
        -Dpdf.css=templates/css/dr-5000-manual-{3}.css \
        --filter=ditavals/{1}.ditaval" \
  ::: en-US zh-CN de-DE ja-JP ko-KR \
  ::: bm-dr5000-operator bm-dr5000-service \
  :::+ latin cjk de cjk cjk
```

**Performance comparison:**

| Method | 9 langs x 5 bookmaps | Time |
|---|---|---|
| Sequential | 45 builds | ~90 minutes |
| Parallel -j 4 | 45 builds | ~30 minutes |
| Incremental | Only changed | ~5-15 minutes |
| CI/CD matrix | 45 parallel jobs | ~2 minutes |

---

## Integration Testing: Cross-Language Validation

```bash
#!/bin/bash
for lang in zh-CN de-DE ja-JP ko-KR th-TH pt-BR fr-FR es-ES; do
  echo "=== Testing $lang ==="

  dita -i "content/$lang/bookmaps/bm-dr5000-operator.ditamap" \
       -f html5 \
       -o "/tmp/test/$lang" \
       --filter="ditavals/$lang.ditaval" \
       2>&1 | tee "/tmp/test/$lang/build.log"

  echo "--- Broken keyrefs ---"
  grep "\[DOTJ036W\]" "/tmp/test/$lang/build.log" | wc -l
  echo "--- Broken conrefs ---"
  grep "\[DOTJ047E\]" "/tmp/test/$lang/build.log" | wc -l
  echo "--- Missing images ---"
  grep "\[DOTX031E\]" "/tmp/test/$lang/build.log" | wc -l
done
```

---

<!-- _class: exercise -->

## Exercise: Configure Multi-Language Build

**Task**: Set up a complete multi-language build pipeline for 3 languages.

**Requirements:**

1. Install CJK fonts on your build machine (Noto Sans CJK SC)
2. Create a bookmap template with relative paths
3. Write `build-language.sh` that:
   - Accepts language code as argument
   - Selects correct CSS (cjk vs latin vs de)
   - Runs DITA-OT with ditaval and CSS
   - Logs to `output/LANG/build.log`
4. Write `publish-all.sh` that loops over en-US, zh-CN, de-DE
5. Run full build and verify:
   - All 3 PDFs generated without errors
   - CJK text renders correctly (no tofu)
   - German PDF shows hyphenation
   - All cross-references resolve

**Bonus**: Implement incremental build with `git diff`.

**Time**: 35 minutes

---

<!-- _class: keypoint -->

## Key Takeaways

1. **Hybrid architecture** scales from 1 to 9+ languages -- one template, per-language content
2. **Per-language maps** are cleaner than all-in-one for 3+ languages
3. **CJK fonts are non-negotiable**: install Noto Sans CJK on all build servers
4. **Language-specific CSS**: DE needs smaller fonts, TH needs generous line height, CJK needs character-based justification
5. **GitHub Actions matrix builds** parallelize by language -- 45 PDFs in ~2 minutes
6. **Incremental builds** use `git diff` to rebuild only changed languages
7. **xml:lang governance**: BCP 47 format, directory match enforced, fallback chain configured
8. **Performance**: Sequential=90min, Parallel=30min, Incremental=10min, CI=2min

---

## Quick Reference: Build Commands

```bash
# Single language, single bookmap
dita -i content/zh-CN/bookmaps/bm-dr5000-operator.ditamap \
     -f chemistry-pdf \
     -o output/zh-CN/pdf/ \
     -Dpdf.css=templates/css/dr-5000-manual-cjk.css \
     --filter=ditavals/zh-CN.ditaval

# All languages, parallel (4 at a time)
parallel -j 4 \
  "dita -i content/{1}/bookmaps/{2}.ditamap \
        -f chemistry-pdf \
        -o output/{1}/pdf/{2}/ \
        -Dpdf.css=templates/css/dr-5000-manual-{3}.css" \
  ::: en-US zh-CN de-DE ja-JP ko-KR \
  ::: bm-dr5000-operator bm-dr5000-service \
  :::+ latin cjk de cjk cjk
```
