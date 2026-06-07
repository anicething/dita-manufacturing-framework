---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Localization Workflow
## Module 5 — DITA Professional (进阶)

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | Why Localization Matters in Manufacturing | 3-5 |
| 2 | DITA Localization Architecture | 6-8 |
| 3 | XLIFF: The Translation Exchange Format | 9-12 |
| 4 | Translation Memories (TMX) | 13-15 |
| 5 | Terminology Management | 16-19 |
| 6 | The 9-Language Framework Concept | 20-23 |
| 7 | Working with Translation Vendors | 24-26 |
| 8 | Automated Translation Pipelines | 27-29 |
| 9 | Hands-on Exercise | 30-31 |
| 10 | Summary | 32-33 |

---

## The Cost of Poor Localization

In 2024, a ExampleCorp distributor in Germany reported a near-miss incident: a technician misinterpreted a machine-translated LOTO procedure because the German translation rendered "lockout" as "aussperren" (to lock someone out of a room) instead of "Energietrennung" (energy isolation).

| Incident | Root Cause | Cost |
|----------|-----------|------|
| German near-miss | Poor machine translation of safety content | Potential liability: EUR 500K+ |
| Thai warranty claim | "tighten" mistranslated as "remove" | USD 12,000 in parts + labor |
| Korean certification delay | Manual had no Korean version, 6-week delay | USD 80,000 in lost production |
| Brazilian fine | No Portuguese manual, regulatory violation | BRL 45,000 fine |

**Lesson**: Localization quality is not a cost center — it is a **safety function** and a **regulatory requirement**.

---

## Regulatory Landscape: Translation Requirements

| Region | Regulation | Language Requirement |
|--------|-----------|---------------------|
| **EU** | Machinery Directive 2006/42/EC | Official language(s) of member state |
| **China** | GB 5226.1, GB/T 15706 | Simplified Chinese (zh-CN) mandatory |
| **Japan** | Industrial Safety & Health Act Art. 33 | Japanese required |
| **South Korea** | Occupational Safety & Health Act Art. 35 | Korean required |
| **Brazil** | NR-12 (Machinery Safety) | Portuguese (pt-BR) mandatory |
| **Canada** | CSA Z432, Quebec Bill 96 | English + French (fr-CA) |
| **USA** | OSHA (no explicit language, but "understood by workers") | English (de facto), Spanish (OSHA guidance) |

> **Golden rule**: If you sell a machine in a country, the operator manual must exist in the official language of that country — before the machine ships.

---

## Why DITA Excels at Localization

| DITA Feature | Localization Benefit |
|--------------|---------------------|
| **Topic granularity** | Translate topic-by-topic; ship partial translations |
| **xml:lang attribute** | Clean language identification per element |
| **Keyref indirection** | Change product name/term once, propagate to all languages |
| **Conditional processing** | Exclude untranslated content per language via ditaval |
| **Conref to shared content** | Translate shared warning once, reuse everywhere |
| **No formatting in content** | Translators work on structure, not visual formatting |
| **Standards alignment** | XLIFF, TMX, ITS — all OASIS/W3C standards |

---

## Localization Architecture: Directory Structure

```
dita-manufacturing-framework/
  content/
    en-US/                        # Source language (English)
      topics/
        safety/
          hazard-flying-chips.dita
          hazard-coolant-mist.dita
        operations/
          task-machine-startup.dita
          concept-cnc-overview.dita
      glossary/
        gloss-loto.dita
      bookmaps/
        bm-dr5000-operator.ditamap

    zh-CN/                        # Simplified Chinese
      topics/
        safety/
          hazard-flying-chips.dita     # Translated
          hazard-coolant-mist.dita     # Translated
        operations/
          task-machine-startup.dita    # Translated
        ...
      glossary/
        gloss-loto.dita                # Translated
      bookmaps/
        bm-dr5000-operator.ditamap     # Translated map

    de-DE/                        # German — same structure
    ja-JP/                        # Japanese
    ko-KR/                        # Korean
    ...
```

---

## The Framework Translation Unit Model

Every topic file maps 1:1 to a **translation unit** (TU). This is the fundamental contract with translation vendors.

```
[Source topic: en-US/topics/safety/hazard-flying-chips.dita]
                                    |
                                    v
                         [Translation Unit]
                                    |
                    +---------------+---------------+
                    |               |               |
                    v               v               v
        zh-CN/topics/safety/   de-DE/topics/safety/   ja-JP/...
        hazard-flying-chips.dita   hazard-flying-chips.dita
```

**File naming rule**: Translated files retain the **identical filename** as the source. Only the directory path changes (language folder). This is critical for keyref resolution: `keyref` values do not contain language paths.

---

## What Gets Translated vs What Does NOT

| Element | Translate? | Reason |
|---------|-----------|--------|
| `title` | Yes | User-facing |
| `shortdesc` | Yes | User-facing |
| `p`, `li`, `cmd`, `info` | Yes | User-facing content |
| `note`, `hazardstatement` | Yes | Safety content — critical |
| `fig`, `table`, `section` | Yes | User-facing |
| `image alt-text` | Yes | Accessibility |
| `xml:lang` attribute | **No** | Metadata (must reflect target language) |
| `id` attribute | **No** | Must stay identical for keyref |
| `keyref` / `conref` values | **No** | Reference paths (point to target language content) |
| `outputclass` | **No** | Processing instruction |
| Element/attribute names | **No** | DITA markup itself |
| `prolog/metadata/data` values | Maybe | Only if user-facing text values |
| Code blocks (literal) | **No** | G-code, parameter values |

---

## Preparing Content for Translation

```bash
#!/bin/bash
# prep-translation-batch.sh — Extract translatable topics for vendor

SOURCE_LANG="en-US"
TARGET_LANG="$1"
BATCH_NAME="dr5000-batch-$(date +%Y%m%d)"

mkdir -p "translation/batches/$BATCH_NAME/source"
mkdir -p "translation/batches/$BATCH_NAME/target"

# 1. Copy source topics
cp -r "content/$SOURCE_LANG/topics" \
     "translation/batches/$BATCH_NAME/source/"

# 2. Create translation package manifest
find "content/$SOURCE_LANG" -name "*.dita" > \
     "translation/batches/$BATCH_NAME/source/manifest.txt"

# 3. Generate word count
find "content/$SOURCE_LANG" -name "*.dita" \
  -exec cat {} \; | wc -w > \
     "translation/batches/$BATCH_NAME/source/wordcount.txt"

# 4. Package for vendor
cd "translation/batches/"
zip -r "$BATCH_NAME.zip" "$BATCH_NAME/"

echo "Batch created: translation/batches/$BATCH_NAME.zip"
echo "Word count: $(cat translation/batches/$BATCH_NAME/source/wordcount.txt)"
```

---

## XLIFF: The Translation Exchange Format

XLIFF (XML Localization Interchange File Format) is the **OASIS standard** for exchanging translatable content between DITA and CAT (Computer-Assisted Translation) tools.

```
XLIFF 1.2 (legacy)              XLIFF 2.x (current)
  <xliff>                         <xliff>
    <file>                           <unit id="1">
      <body>                           <segment>
        <trans-unit>                     <source>Lockout/Tagout</source>
          <source>LOTO</source>          <target>Energietrennung</target>
          <target>...</target>          </segment>
        </trans-unit>                 </unit>
      </body>                        </file>
    </file>                         </xliff>
  </xliff>
```

**Framework uses XLIFF 2.1** (aligned with DITA-OT 4.x). Generated via DITA-OT's `dita2xliff` plugin.

---

## Generating XLIFF from DITA

```bash
# Step 1: Generate XLIFF for all English content
dita -i content/en-US/bookmaps/bm-dr5000-operator.ditamap \
     -f xliff \
     -o translation/xliff/

# Output:
# translation/xliff/bm-dr5000-operator.xliff

# Step 2: Split large XLIFF by chapter (for vendor parallelization)
dita -i content/en-US/bookmaps/bm-dr5000-operator.ditamap \
     -f xliff \
     -o translation/xliff/ \
     --args.xliff.split-by=chapter

# Output:
# translation/xliff/bm-dr5000-operator_ch01.xliff
# translation/xliff/bm-dr5000-operator_ch02.xliff
# ...
```

---

## XLIFF Structure: What Translators See

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xliff xmlns="urn:oasis:names:tc:xliff:document:2.0"
       version="2.0"
       srcLang="en-US"
       trgLang="zh-CN">
  <file id="f1" original="hazard-flying-chips.dita">
    <unit id="hazard-flying-chips">
      <segment id="s1">
        <source>Flying Chips During Machining</source>
        <target>加工过程中的飞屑</target>
      </segment>
      <segment id="s2">
        <source>Flying chips and debris ejected at high
        velocity during CNC machining operations.</source>
        <target>数控加工操作过程中高速飞出的切屑和碎屑。</target>
      </segment>
      <segment id="s3">
        <source>Can cause eye injury, blindness, lacerations,
        or embedded foreign bodies requiring surgical removal.</source>
        <target>可能导致眼部受伤、失明、割伤或需要手术取出的异物嵌入。</target>
      </segment>
    </unit>
  </file>
</xliff>
```

---

## Merging Translated XLIFF Back to DITA

```bash
# Step 1: Receive translated XLIFF from vendor
ls translation/received/zh-CN/
# bm-dr5000-operator_zh-CN.xliff

# Step 2: Validate XLIFF completeness
java -jar dita-ot-4.x/lib/xliff-checker.jar \
  --xliff translation/received/zh-CN/bm-dr5000-operator_zh-CN.xliff \
  --check-completeness \
  --check-ids

# Step 3: Merge XLIFF back into DITA topics
dita -i content/en-US/bookmaps/bm-dr5000-operator.ditamap \
     -f xliff2dita \
     -o content/zh-CN/ \
     --args.xliff.file=translation/received/zh-CN/bm-dr5000-operator_zh-CN.xliff

# Step 4: Verify merged content builds
dita -i content/zh-CN/bookmaps/bm-dr5000-operator.ditamap \
     -f html5 \
     -o output/zh-CN/test/
```

---

## Translation Memories (TMX)

A Translation Memory (TM) is a database that stores previously translated segments. When the same or similar text appears in new content, the CAT tool suggests the existing translation.

**Format**: TMX (Translation Memory eXchange) — an OASIS XML standard.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tmx version="1.4">
  <header
    creationtool="ExampleCorp-TM-Manager"
    srclang="en-US"
    datatype="xml"
    segtype="sentence">
  </header>
  <body>
    <tu>                                  <!-- Translation Unit -->
      <tuv xml:lang="en-US">               <!-- Source -->
        <seg>Lockout/Tagout procedure</seg>
      </tuv>
      <tuv xml:lang="zh-CN">               <!-- Target -->
        <seg>上锁/挂牌程序</seg>
      </tuv>
      <tuv xml:lang="de-DE">               <!-- Target -->
        <seg>Lockout/Tagout-Verfahren</seg>
      </tuv>
    </tu>
  </body>
</tmx>
```

---

## Building a Translation Memory for MC-5000

The framework TM starts with **seed content** and grows with every translation batch.

**Seed sources:**

| Source | Segments | Quality |
|--------|----------|---------|
| MC-5000 v1.0 manuals (aligned) | ~4,200 | Medium (manual alignment) |
| Industry-standard safety phrases (ISO 12100) | ~350 | High (official translations) |
| CNC terminology glossary (GB/T 8129) | ~680 | High (national standard) |
| DITA Learning Object content | ~1,100 | High (professionally translated) |
| UI/HMI strings from MC-5000 control panel | ~520 | High (embedded software team) |

**Total seed TM**: ~6,850 translation units across 4 languages (en, zh-CN, de-DE, ja-JP).

---

## TM Leverage: How Much Can You Reuse?

The first MC-5000 manual translation is expensive. Subsequent updates are cheap.

| Translation Batch | New Words | TM Matches (100%) | TM Matches (Fuzzy) | Net Cost |
|-------------------|-----------|-------------------|--------------------|----------|
| v1.0 Initial (80K words) | 80,000 | 0 | 0 | 100% base |
| v1.1 Minor update (+5K) | 5,000 | 2,800 (56%) | 1,400 (28%) | ~20% of base |
| v1.2 Safety revision (+3K) | 3,000 | 1,500 (50%) | 900 (30%) | ~15% of base |
| v2.0 Major revision (+25K) | 25,000 | 12,000 (48%) | 6,500 (26%) | ~35% of base |

**Pricing model with TM:**
- 100% match (ICE match): 10-15% of base word rate
- 95-99% fuzzy match: 40-50% of base word rate
- 85-94% fuzzy match: 60-70% of base word rate
- New / no match: 100% of base word rate

---

## Terminology Management

Inconsistent terminology is the #1 quality complaint from local manufacturers and distributors.

| Problem | Example | Impact |
|---------|---------|--------|
| **Multiple translations for one term** | "spindle" translated as "主轴" (zh), "スピンドル" (ja), and "主軸" (zh-TW) in the same doc | Confusion, rework |
| **Literal translation of idioms** | "dead center" (机械 dead center) translated as "死亡中心" (death center) | Safety hazard |
| **Untranslated acronyms** | "LOTO" left as-is in a Thai manual | Thai technicians cannot understand |
| **Brand name inconsistency** | "ExampleCorp" rendered as "鼎瑞", "示例", etc. | Brand damage, trademark risk |

**Solution**: Framework-managed **multilingual termbase** (TBX format) with approved translations per term, per language.

---

## TBX: TermBase eXchange Format

```xml
<?xml version="1.0" encoding="UTF-8"?>
<martif type="TBX" xml:lang="en-US">
  <martifHeader>
    <fileDesc>
      <titleStmt>
        <title>ExampleCorp MC-5000 Multilingual Termbase</title>
      </titleStmt>
    </fileDesc>
  </martifHeader>
  <text>
    <body>
      <termEntry id="tid-loto">
        <descrip type="subjectField">safety</descrip>
        <langSet xml:lang="en-US">
          <ntig>
            <termGrp>
              <term>Lockout/Tagout</term>
              <termNote type="abbreviation">LOTO</termNote>
            </termGrp>
          </ntig>
        </langSet>
        <langSet xml:lang="zh-CN">
          <ntig>
            <termGrp>
              <term>上锁/挂牌</term>
              <termNote type="abbreviation">上锁挂牌</termNote>
              <termNote type="partOfSpeech">noun</termNote>
            </termGrp>
          </ntig>
        </langSet>
        <langSet xml:lang="de-DE">
          <ntig>
            <termGrp>
              <term>Lockout/Tagout-Verfahren</term>
              <termNote type="abbreviation">LOTO</termNote>
              <termNote type="partOfSpeech">noun, neuter</termNote>
            </termGrp>
          </ntig>
        </langSet>
      </termEntry>
    </body>
  </text>
</martif>
```

---

## Terminology Governance Rules

| Rule | Description | Enforcement |
|------|-------------|-------------|
| **Approved-only** | Only use terms from the approved termbase | Schematron check during CI build |
| **No ad-hoc translation** | Translators may not invent new translations | Contractual obligation with vendor |
| **Term change request (TCR)** | New terms or changes require formal TCR process | TCR form, ExampleCorp QA approval |
| **One term = one translation** | Per language, each term has exactly one approved translation | TBX constraint; no duplicate entries |
| **Acronym preservation rules** | Defined per language (e.g., LOTO stays LOTO in DE, translated in ZH) | Language-specific style guide |
| **Annual termbase review** | Full review of all terminology with SME input | Calendar event: Q1 each year |

---

## The 9-Language Framework Concept

The DITA Manufacturing Framework defines a **9-language matrix** covering ExampleCorp's current and planned markets.

| # | Language | xml:lang | Priority | Market Status |
|---|----------|----------|----------|---------------|
| 1 | **English (US)** | en-US | Tier 0 (Source) | All exports; source language |
| 2 | **Simplified Chinese** | zh-CN | Tier 1 | Domestic China (mandatory) |
| 3 | **German** | de-DE | Tier 1 | EU: Germany, Austria, Switzerland |
| 4 | **Japanese** | ja-JP | Tier 1 | Japan, key export market |
| 5 | **Korean** | ko-KR | Tier 2 | South Korea |
| 6 | **Thai** | th-TH | Tier 2 | Thailand (ASEAN hub) |
| 7 | **Portuguese (BR)** | pt-BR | Tier 2 | Brazil |
| 8 | **French** | fr-FR | Tier 3 | EU: France, Belgium; Canada |
| 9 | **Spanish** | es-ES | Tier 3 | EU: Spain; Latin America |

---

## Language Tier Definitions

| Tier | Coverage | Translation Method | Review Process | Update Cadence |
|------|----------|-------------------|----------------|----------------|
| **Tier 0 (en-US)** | 100% | Source authoring | Internal SME review | Continuous |
| **Tier 1** | 100% | Professional translation with TM | SME + vendor QA | Within 5 business days of EN update |
| **Tier 2** | 100% of safety, 80% of ops/maintenance, 50% of reference | Professional translation with TM | Vendor QA + spot-check | Within 15 business days |
| **Tier 3** | 100% safety, 60% operations, 0% reference | Professional translation with TM | Vendor QA | Within 30 business days |

---

## Language-Specific Complexity Factors

| Language | Expansion Rate (vs EN) | CJK? | RTL? | Font Challenges | Special Notes |
|----------|----------------------|------|------|-----------------|---------------|
| zh-CN | -35% (shorter) | Yes | No | Font weight, line height | Must use GB 2312 terminology |
| de-DE | +30% (longer) | No | No | Umlauts, compound words | Long words break tables |
| ja-JP | -15% | Yes | No | Ruby text, vertical text (rare) | Katakana for foreign terms |
| ko-KR | -10% | Yes | No | Hangul syllable composition | Sino-Korean terminology |
| th-TH | +15% | No | No | Complex glyphs, no word breaks | Paragraph-level line breaking |
| pt-BR | +20% | No | No | Accented characters | NR-12 terminology |
| fr-FR | +25% | No | No | Accented characters | Spacing before punctuation |
| es-ES | +25% | No | No | Accented characters, inverted punctuation | LatAm vs Spain variants |

---

## Working with Translation Vendors

**Vendor selection criteria for ExampleCorp:**

| Criterion | Weight | Requirement |
|-----------|--------|-------------|
| ISO 17100 certification | Mandatory | Certified translation services |
| DITA/XLIFF experience | Mandatory | Must demonstrate DITA-OT XLIFF roundtrip |
| Manufacturing domain | High | Prior CNC/machinery experience |
| CAT tool compatibility | High | Must accept XLIFF 2.x, TMX, TBX |
| CJK capability | High | In-house CJK translators (not outsourced) |
| Quality metrics | Medium | LISA QA Model or MQM/DQF scoring |
| Turnaround time SLA | Medium | Tier 1 < 5 days, Tier 2 < 15 days |
| Price per word (per language pair) | Medium | Competitive with industry benchmarks |

---

## Translation Package: What to Send to Vendors

```
dr5000-translation-batch-2026-Q3.zip
  +-- README.txt                        # Instructions
  +-- manifest.csv                       # File list with word counts
  +-- source/
  |    +-- xliff/
  |    |    +-- bm-dr5000-operator.xliff
  |    |    +-- bm-dr5000-service.xliff
  |    +-- reference/
  |         +-- operator-manual-en.pdf   # For context
  |         +-- screenshots/
  +-- assets/
  |    +-- termbase.tbx                  # Multilingual termbase
  |    +-- tm-dr5000-en-zh.tmx           # Translation memory (EN → ZH)
  |    +-- tm-dr5000-en-de.tmx           # Translation memory (EN → DE)
  +-- style-guide/
  |    +-- dr5000-style-guide-en.pdf
  |    +-- dr5000-style-guide-zh-CN.pdf
  |    +-- dr5000-style-guide-de-DE.pdf
  |    +-- acronym-list.xlsx
  +-- ditaval/
       +-- zh-CN-only.ditaval
       +-- de-DE-only.ditaval
```

---

## Translation Quality Assurance

```bash
#!/bin/bash
# qa-translation.sh — Automated QA checks for received translations

XLIFF_FILE="$1"
TARGET_LANG="$2"

echo "=== QA Checks for $XLIFF_FILE ($TARGET_LANG) ==="

# 1. Completeness: no untranslated segments
echo "--- Untranslated segments ---"
xmlstarlet sel -t -v "//unit/segment[not(target) or target='']/@id" \
  "$XLIFF_FILE" | wc -l

# 2. Consistency: check termbase adherence
echo "--- Terminology violations ---"
python3 tools/check-terminology.py \
  --xliff "$XLIFF_FILE" \
  --termbase "assets/termbase.tbx" \
  --target-lang "$TARGET_LANG"

# 3. Inline tag integrity
echo "--- Broken inline tags ---"
python3 tools/check-inline-tags.py \
  --xliff "$XLIFF_FILE"

# 4. Placeholder preservation (variables, keyrefs, conrefs)
echo "--- Missing placeholders ---"
python3 tools/check-placeholders.py \
  --xliff "$XLIFF_FILE" \
  --source-lang en-US

# 5. Length deviation (>150% or <50% of source)
echo "--- Length outliers ---"
python3 tools/check-length-deviation.py \
  --xliff "$XLIFF_FILE" \
  --min-ratio 0.5 \
  --max-ratio 1.5
```

---

## Automated Translation Pipeline (CI/CD)

```yaml
# .github/workflows/translation-pipeline.yml
name: Translation Pipeline

on:
  push:
    branches: [main]
    paths:
      - 'content/en-US/**'    # Trigger when English source changes
  workflow_dispatch:
    inputs:
      target_languages:
        description: 'Target languages'
        default: 'zh-CN,de-DE,ja-JP'

jobs:
  generate-xliff:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate XLIFF packages
        run: |
          for bm in bookmaps/bm-dr5000-*.ditamap; do
            dita -i "content/en-US/$bm" -f xliff \
              -o "translation/outgoing/xliff/"
          done
      - name: Upload XLIFF as artifact
        uses: actions/upload-artifact@v4
        with:
          name: xliff-packages
          path: translation/outgoing/xliff/

  notify-vendor:
    needs: generate-xliff
    runs-on: ubuntu-latest
    steps:
      - name: Notify translation vendor
        run: |
          curl -X POST https://vendor-api.translate.example.com/jobs \
            -H "Authorization: Bearer ${{ secrets.TRANSLATION_API_KEY }}" \
            -d '{"project": "MC-5000", "languages": ["zh-CN","de-DE","ja-JP"]}'
```

---

## Auto-Translation with Post-Editing

For Tier 3 languages or draft purposes, the framework supports NMT (Neural Machine Translation) with mandatory human post-editing.

```bash
# mt-postedit-pipeline.sh — NMT + human post-edit workflow

# 1. Machine translation (using open-source NMT)
python3 tools/mt-translate.py \
  --input-xliff translation/outgoing/bm-operator.xliff \
  --target-lang es-ES \
  --engine deepl-api \
  --glossary assets/glossary-es.csv \
  --output translation/mt-output/es-ES/

# 2. Human post-edit in CAT tool
#    (Editor corrects MT output, records changes)

# 3. Quality estimation: score machine output
python3 tools/mt-qe-score.py \
  --mt-output translation/mt-output/es-ES/ \
  --reference "content/es-ES/" 2>/dev/null || true

# 4. If QE score < 0.80: full human translation required
#    If QE score >= 0.80: post-edit is sufficient
```

**NMT quality thresholds for MC-5000:**

| Content Type | Minimum QE Score | Action if Below |
|-------------|------------------|-----------------|
| Safety / hazard | 0.95 | Full human translation |
| Task procedures | 0.85 | Full human translation |
| Reference / specs | 0.75 | Post-edit |
| Glossary / simple text | 0.70 | Post-edit |
| Marketing / high-visibility | 0.90 | Full human translation |

---

## Managing Translation Updates

When English source content changes, only the **changed topics** need re-translation.

```bash
#!/bin/bash
# translation-diff.sh — Find changed topics since last translation

LAST_TRANSLATION_DATE="2026-05-15"

echo "Topics changed since $LAST_TRANSLATION_DATE:"

# Find changed EN source files
git diff --name-only --diff-filter=AM \
  $(git rev-list -1 --before="$LAST_TRANSLATION_DATE" HEAD) \
  HEAD -- "content/en-US/topics/" | while read f; do

  # Check if corresponding target language file exists
  for lang in zh-CN de-DE ja-JP ko-KR; do
    target_file="${f/en-US/$lang}"
    if [ ! -f "$target_file" ]; then
      echo "  NEW/$lang: $f"
    elif [ "$f" -nt "$target_file" ]; then
      echo "  OUTDATED/$lang: $f"
    fi
  done
done
```

---

## Localization Cost Model

| Cost Factor | Estimate | Notes |
|-------------|----------|-------|
| **Base translation rate** (per word) | $0.12-$0.18 (EN→ZH, EN→JA) | Manufacturing/technical domain premium |
| **TM leverage discount** (100% match) | 85-90% discount | Only pay 10-15% of base rate |
| **Fuzzy match discount** (95-99%) | 50-60% discount | Pay 40-50% of base rate |
| **Editing/proofreading** | $0.04-$0.06/word | Included in some vendor packages |
| **Desktop publishing (DTP)** | $25-$40/page | Layout adjustment for CJK/DE |
| **Engineering** (DITA roundtrip) | $500-$1,000/batch | XLIFF merge, build verification |
| **PM surcharge** | 15-20% of total | Vendor project management |
| **Annual TM maintenance** | $2,000-$5,000/year | TM cleanup, alignment, dedup |

**Estimated annual localization budget (2026):** $65,000-$95,000 for 4 active languages + 2 Tier 3 languages (safety-only).

---

<!-- _class: exercise -->

## Exercise: Complete a Localization Workflow

**Task**: Take a set of DITA topics through a full localization roundtrip.

**Requirements:**
1. Select 3 DITA topics from the framework (1 task, 1 concept, 1 hazard statement)
2. Generate XLIFF 2.x from the source (en-US) content
3. Manually translate 10 segments into Simplified Chinese (zh-CN):
   - Translate a `<cmd>` with `uicontrol` elements (preserve inline tags)
   - Translate a `<hazardstatement>` (typeofhazard, consequence, howtoavoid)
   - Translate a `<shortdesc>` and a `<note>` element
4. Create a mini-TBX termbase with 5 key terms (EN + ZH approved translations)
5. Merge the XLIFF back into DITA topics in the zh-CN directory
6. Verify: `xml:lang="zh-CN"`, all ids unchanged, inline tags intact
7. Build the Chinese output: `dita -i content/zh-CN/bookmaps/bm-test.ditamap -f html5`
8. Document one term change request (TCR) for a new term you encountered

**Time**: 35 minutes

---

<!-- _class: keypoint -->

## Key Takeaways

1. **Localization is safety-critical** in manufacturing: poor translations cause accidents, regulatory violations, and warranty claims
2. **DITA's architecture enables localization**: topic granularity, xml:lang, keyref indirection, and conditional processing make multi-language manageable
3. **XLIFF 2.x** is the standard exchange format; DITA-OT handles XLIFF generation and roundtrip merging
4. **Translation Memory (TMX)** reduces costs 50-85% on updates; seed the TM with existing translations
5. **Terminology Management (TBX)** ensures consistent translations of critical manufacturing terms across all languages
6. **9-Language Framework**: Tier 1 (zh-CN, de-DE, ja-JP) gets full coverage and fast turnaround; Tier 2-3 gets tiered coverage
7. **Automated QA**: Completeness checks, terminology validation, inline tag integrity, placeholder preservation
8. **CI/CD pipeline**: Generate XLIFF on source changes, route to vendors, auto-validate on return
