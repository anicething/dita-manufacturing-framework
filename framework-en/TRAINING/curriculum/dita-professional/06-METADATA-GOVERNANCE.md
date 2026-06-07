---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Metadata Governance
## Module 6 — DITA Professional (进阶)

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | Why Metadata Governance | 3-5 |
| 2 | The M1-M7 Metadata Rules | 6-12 |
| 3 | Metadata in DITA: prolog & data elements | 13-15 |
| 4 | Subject Scheme Enforcement | 16-18 |
| 5 | Schematron Rules for Metadata | 19-22 |
| 6 | Automated Metadata Validation (CI/CD) | 23-25 |
| 7 | Metadata Quality Metrics | 26-27 |
| 8 | Reporting Dashboards | 28-29 |
| 9 | Hands-on Exercise | 30-31 |
| 10 | Summary | 32-33 |

---

## Why Metadata Governance?

In v1.0 of the framework, metadata was **optional and unvalidated**. The result: chaos.

| v1.0 Problem | Impact | v1.1 Solution |
|-------------|--------|---------------|
| 30% of topics had no `xml:lang` | Broken builds, mixed-language output | M1: xml:lang is mandatory |
| Authors invented their own status values | "Done", "OK", "Verified", "Final" — no consistency | M3: controlled status vocabulary |
| No audience metadata | Training content shipped with operator manuals | M4: audience classification |
| Missing safety metadata | Regulations couldn't verify safety coverage | M5: hazard-severity required |
| No product affinity data | MC-4000 content mixed into MC-5000 manuals | M2: product model linkage |
| 15 different data naming conventions | Tools couldn't process metadata | Standardized `@name` patterns |

---

## What Metadata Looks Like in DITA

Metadata lives in the `<prolog>` element at the top of every DITA topic.

```xml
<task id="task-coolant-change" xml:lang="en-US">
  <title>Changing Coolant on MC-5000</title>
  <prolog>
    <author>Zhang Wei</author>
    <critdates>
      <created date="2026-03-15"/>
      <revised modified="2026-06-01"/>
    </critdates>
    <metadata>
      <category>maintenance</category>
      <category>coolant-system</category>
      <data name="product" value="MC-5000"/>
      <data name="content-type" value="task"/>
      <data name="document-status" value="approved"/>
      <data name="audience" value="maintenance-technician"/>
      <data name="skill-level" value="level-2"/>
      <data name="safety-critical" value="yes"/>
      <data name="review-cycle" value="annual"/>
      <data name="component" value="coolant-reservoir"/>
    </metadata>
  </prolog>
  <taskbody>...</taskbody>
</task>
```

---

## The M1-M7 Metadata Rules: Overview

| Rule | Name | Requirement | Severity |
|------|------|-------------|----------|
| **M1** | Language Declaration | Every topic MUST have `xml:lang` | Blocking |
| **M2** | Product Affinity | Every topic MUST declare product model(s) | Blocking |
| **M3** | Document Status | Every topic MUST use controlled status value | Blocking |
| **M4** | Audience Classification | Every topic MUST declare target audience(s) | Blocking |
| **M5** | Safety Criticality | Safety content MUST be flagged | Blocking |
| **M6** | Content Type | Every topic MUST declare content type | Warning |
| **M7** | Review Cycle | Every topic MUST have review cadence | Warning |

> **Blocking** = CI build fails. **Warning** = CI build passes with warnings, report generated.

---

## M1: Language Declaration

**Rule**: Every DITA topic MUST carry `xml:lang` on the root element.

```xml
<!-- COMPLIANT -->
<task id="task-coolant-change" xml:lang="en-US">

<!-- COMPLIANT -->
<concept id="concept-cnc-overview" xml:lang="zh-CN">

<!-- NON-COMPLIANT — will fail CI build -->
<task id="task-coolant-change">
```

**Validation query:**
```xpath
//*[not(@xml:lang) and
  (contains(@class, ' topic/topic ') or
   contains(@class, ' map/map '))]
```

**Rationale**: Without `xml:lang`, DITA-OT cannot determine which fonts, hyphenation rules, index sorting, or conditional processing rules to apply. It is the single most important metadata attribute in DITA.

---

## M2: Product Affinity

**Rule**: Every topic MUST declare which product model(s) it applies to.

```xml
<metadata>
  <!-- Single product -->
  <data name="product" value="MC-5000"/>

  <!-- Multiple products via separate data elements -->
  <data name="product" value="MC-5000"/>
  <data name="product" value="MC-5000L"/>
  <data name="product" value="MC-5000H"/>

  <!-- Product with variant -->
  <data name="product" value="MC-5000"/>
  <data name="product-variant" value="5-axis-B-axis"/>
</metadata>
```

**Controlled product values** (from Subject Scheme):

| Value | Model | Description |
|-------|-------|-------------|
| MC-5000 | Standard | 5-axis CNC machining center |
| MC-5000L | Long-bed | Extended X-axis travel |
| MC-5000H | High-speed | 24,000 RPM spindle variant |
| MC-5000-5X | Full 5X | Simultaneous 5-axis variant |
| MC-4000 | Previous gen | Legacy model (for migration only) |

---

## M3: Document Status

**Rule**: Every topic MUST declare its document status using a controlled vocabulary.

```xml
<metadata>
  <data name="document-status" value="approved"/>
</metadata>
```

**Controlled status lifecycle:**

```
draft → in-review → approved → published → superseded
  ↑                    ↓
  └────── rejected ────┘
```

| Status | Meaning | Who Can Set | Build Behavior |
|--------|---------|-------------|----------------|
| `draft` | Authoring in progress | Author | Excluded from production builds |
| `in-review` | Under SME/QA review | Author → Reviewer | Excluded from production builds |
| `approved` | Reviewed and approved | QA Lead | Included in all builds |
| `published` | Live in production deliverable | Release Manager | Included, tracks publication date |
| `superseded` | Replaced by newer version | Release Manager | Excluded, retained for history |
| `rejected` | Failed review, return to draft | QA Lead | Excluded, author must revise |

---

## M4: Audience Classification

**Rule**: Every topic MUST declare its target audience. Manufacturing documentation has very different readers.

```xml
<metadata>
  <!-- Single audience -->
  <data name="audience" value="maintenance-technician"/>

  <!-- Multiple audiences (topic serves more than one role) -->
  <data name="audience" value="machine-operator"/>
  <data name="audience" value="shift-supervisor"/>
</metadata>
```

**Controlled audience values:**

| Value | Role | Typical Content |
|-------|------|-----------------|
| `machine-operator` | Operates machine day-to-day | Startup, shutdown, basic operation, alarm response |
| `maintenance-technician` | Installs, repairs, maintains | PM schedules, parts replacement, diagnostics |
| `shift-supervisor` | Manages shift operations | Production planning, QC checks, staffing |
| `safety-officer` | Ensures safety compliance | Hazard registers, incident reports, training records |
| `quality-inspector` | Verifies product quality | Measurement procedures, calibration, defect logs |
| `trainer` | Delivers training | Learning objects, certification materials |
| `integration-engineer` | Integrates with factory systems | Network config, MES/SCADA, API docs |
| `general` | Any role | Company policies, general reference |

---

## M5: Safety Criticality

**Rule**: Content that addresses safety MUST be explicitly flagged. Safety content cannot be accidentally excluded or mishandled.

```xml
<metadata>
  <data name="safety-critical" value="yes"/>
  <data name="hazard-type" value="mechanical"/>
  <data name="iso-reference" value="ISO 12100:2010"/>
  <data name="risk-level" value="R1"/>
</metadata>
```

**Safety metadata sub-fields:**

| Field | Values | Required When |
|-------|--------|---------------|
| `safety-critical` | `yes` / `no` | Always (M5) |
| `hazard-type` | `mechanical`, `electrical`, `thermal`, `chemical`, `ergonomic`, `radiation`, `noise`, `multiple` | When `safety-critical="yes"` |
| `iso-reference` | Free text (e.g., `ISO 12100:2010`, `ISO 13849-1:2023`) | When `safety-critical="yes"` |
| `risk-level` | `R1`, `R2`, `R3`, `R4` (ISO 12100 scale) | When `safety-critical="yes"` |
| `ppe-required` | `yes` / `no` | When `hazard-type` involves physical risk |
| `ppe-type` | `gloves`, `goggles`, `face-shield`, `respirator`, `hearing-protection`, `safety-shoes`, `apron` | When `ppe-required="yes"` |

---

## M6: Content Type

**Rule**: Every topic MUST declare its content type. This enables type-specific validation and publishing.

```xml
<metadata>
  <data name="content-type" value="task"/>
</metadata>
```

**Controlled content type values:**

| Value | Corresponding DITA Type | Examples |
|-------|------------------------|----------|
| `concept` | Concept topic | CNC principles, valve operation theory |
| `task` | Task topic | Step-by-step procedures |
| `reference` | Reference topic | Torque tables, parts lists |
| `troubleshooting` | Troubleshooting topic | Alarm diagnostics |
| `glossary` | Glossentry topic | Terminology definitions |
| `hazard-statement` | Reference + hi-d | Safety warnings |
| `learning-object` | Learning Object topic | Training modules |
| `setup-guide` | Topic (general) | Machine setup, configuration |

> **Note**: M6 duplicates information available from DOCTYPE, but exists for downstream tooling that processes content without XML parsing. It is a **warning-level** rule.

---

## M7: Review Cycle

**Rule**: Every topic MUST declare a review cadence. Manufacturing documentation can become stale quickly as procedures, specifications, and regulations change.

```xml
<metadata>
  <data name="review-cycle" value="annual"/>
  <data name="last-reviewed" value="2026-06-01"/>
  <data name="next-review" value="2027-06-01"/>
  <data name="review-owner" value="Zhang Wei"/>
</metadata>
```

**Controlled review cycle values:**

| Value | Interval | Applies To |
|-------|----------|------------|
| `monthly` | 30 days | Safety procedures, emergency response |
| `quarterly` | 90 days | Calibration procedures, regulatory content |
| `semi-annual` | 180 days | Maintenance procedures, parts lists |
| `annual` | 365 days | General operations, reference material |
| `biennial` | 730 days | Stable reference, historical content |

**Staleness alert**: CI dashboard flags topics where `next-review` date is in the past.

---

## Metadata Element Reference

All framework metadata elements use the `<data>` element with standardized `@name` and `@value` attributes.

| @name | @value | Applicability | Controlled? |
|-------|--------|--------------|-------------|
| `product` | MC-5000, MC-5000L, etc. | All topics | Subject Scheme |
| `document-status` | draft, in-review, approved, etc. | All topics | Subject Scheme |
| `audience` | machine-operator, maintenance-technician, etc. | All topics | Subject Scheme |
| `content-type` | task, concept, reference, etc. | All topics | Subject Scheme |
| `safety-critical` | yes, no | All topics | Subject Scheme |
| `hazard-type` | mechanical, electrical, thermal, etc. | safety topics | Subject Scheme |
| `risk-level` | R1, R2, R3, R4 | safety topics | Subject Scheme |
| `review-cycle` | monthly, quarterly, annual, etc. | All topics | Subject Scheme |
| `skill-level` | level-1, level-2, level-3 | All topics | Subject Scheme |

---

## Subject Scheme: The Enforcement Engine

The framework uses a **Subject Scheme map** to define controlled values. A Schematron file is auto-generated from the Subject Scheme.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE subjectScheme PUBLIC
  "-//OASIS//DTD DITA Subject Scheme//EN"
  "subjectScheme.dtd">
<subjectScheme id="ss-metadata-governance">
  <title>Metadata Governance Subject Scheme</title>

  <!-- Product model enumeration -->
  <subjectdef keys="product-values" navtitle="Product Models">
    <hasInstance>
      <subjectdef keys="prod-dr5000">
        <subjectdef keys="prod-dr5000l"/>
        <subjectdef keys="prod-dr5000h"/>
        <subjectdef keys="prod-dr5000-5x"/>
      </subjectdef>
    </hasInstance>
    <enumerationdef>
      <attributedef name="product"/>
      <subjectdef keyref="product-values"/>
    </enumerationdef>
  </subjectdef>
  <!-- ... additional enumerationdefs for each controlled attribute ... -->
</subjectScheme>
```

---

## Schematron: The Validation Rules

Schematron validates XML content against business rules. The framework generates Schematron from the Subject Scheme.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://purl.oclc.org/dsdl/schematron"
        queryBinding="xslt2">

  <!-- M1: xml:lang must exist -->
  <pattern id="M1-language-declaration">
    <rule context="*[contains(@class, ' topic/topic ')]">
      <assert test="@xml:lang" role="error" id="M1-001">
        M1 VIOLATION: Topic id="<value-of select="@id"/>"
        is missing xml:lang attribute. Every topic must
        declare its language.
      </assert>
    </rule>
  </pattern>

  <!-- M2: Product affinity must exist -->
  <pattern id="M2-product-affinity">
    <rule context="*[contains(@class, ' topic/topic ')]">
      <assert test=".//data[@name='product']"
              role="error" id="M2-001">
        M2 VIOLATION: Topic id="<value-of select="@id"/>"
        has no product data element.
      </assert>
    </rule>
  </pattern>
  <!-- ... -->
</schema>
```

---

## Schematron: M3-M5 Examples

```xml
  <!-- M3: Document status must use controlled values -->
  <pattern id="M3-document-status">
    <rule context="*[contains(@class, ' topic/topic ')]//data[@name='document-status']">
      <assert test="@value = ('draft','in-review','approved',
        'published','superseded','rejected')"
              role="error" id="M3-001">
        M3 VIOLATION: Topic id="<value-of select="ancestor::*
        [contains(@class,' topic/topic ')][1]/@id"/>"
        has invalid document-status
        "<value-of select="@value"/>".
        Allowed: draft, in-review, approved, published,
        superseded, rejected.
      </assert>
    </rule>
  </pattern>

  <!-- M4: Audience must use controlled values -->
  <pattern id="M4-audience">
    <rule context="*[contains(@class, ' topic/topic ')]//data[@name='audience']">
      <assert test="@value = ('machine-operator',
        'maintenance-technician','shift-supervisor',
        'safety-officer','quality-inspector','trainer',
        'integration-engineer','general')"
              role="error" id="M4-001">
        M4 VIOLATION: Topic id="<value-of select="ancestor::*
        [contains(@class,' topic/topic ')][1]/@id"/>"
        has invalid audience "<value-of select="@value"/>".
      </assert>
    </rule>
  </pattern>

  <!-- M5: Safety content must be flagged -->
  <pattern id="M5-safety-criticality">
    <!-- If hazardstatement exists, safety-critical must be 'yes' -->
    <rule context="*[contains(@class, ' topic/topic ')]
      [.//*[contains(@class,' hazardstatement/hazardstatement ')]]">
      <assert test=".//data[@name='safety-critical' and @value='yes']"
              role="error" id="M5-001">
        M5 VIOLATION: Topic id="<value-of select="@id"/>"
        contains a hazardstatement but does not have
        safety-critical='yes'.
      </assert>
    </rule>
  </pattern>
```

---

## Metadata Validation in CI/CD

```yaml
# .github/workflows/metadata-validation.yml
name: Metadata Validation

on:
  pull_request:
    paths:
      - 'content/**/*.dita'
  push:
    branches: [main]

jobs:
  validate-metadata:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup DITA-OT
        uses: dita-ot/setup-action@v2
        with:
          version: '4.x'

      - name: Run Schematron Validation (M1-M7)
        run: |
          dita -i content/en-US/bookmaps/bm-dr5000-operator.ditamap \
               -f schematron \
               --args.schematron=schematron/metadata-m1-m7.sch \
               --args.haltonerror=false \
               -o output/validation/metadata/

      - name: Parse Schematron Results
        run: |
          python3 tools/parse-schematron-report.py \
            --svrl output/validation/metadata/*.svrl \
            --summary validation-report.json

      - name: Block if M1-M5 Errors
        run: |
          errors=$(python3 tools/count-errors.py \
            --report validation-report.json \
            --patterns M1,M2,M3,M4,M5)
          if [ "$errors" -gt 0 ]; then
            echo "ERROR: $errors M1-M5 metadata violations found"
            python3 tools/print-violations.py --report validation-report.json
            exit 1
          fi
```

---

## Validation Report Output

```
=============================================
  METADATA VALIDATION REPORT
  Bookmap: bm-dr5000-operator
  Date: 2026-06-07 14:32:45
=============================================

Total topics scanned: 247
  PASS: 203 (82.2%)
  FAIL: 44 (17.8%)

--- Blocking Errors (M1-M5) ---
  M1 (xml:lang):        3 errors
  M2 (product):         5 errors
  M3 (document-status): 2 errors
  M4 (audience):        4 errors
  M5 (safety-critical): 1 error
  TOTAL BLOCKING:       15 errors

--- Warnings (M6-M7) ---
  M6 (content-type):    12 warnings
  M7 (review-cycle):    17 warnings
  TOTAL WARNINGS:       29 warnings

--- Error Details ---
M2-001: product missing | topic id="task-quick-clean" (topics/maintenance/task-quick-clean.dita)
M4-001: audience missing | topic id="concept-coolant-types" (topics/reference/concept-coolant-types.dita)
M5-001: hazardstatement without safety-critical | topic id="hazard-chemical" (topics/safety/hazard-chemical.dita)
...
```

---

## Implementing Metadata in New Topics

**Template with all M1-M7 metadata pre-filled:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE task PUBLIC
  "-//OASIS//DTD DITA Task//EN" "task.dtd">
<task id="task-TOPIC-ID" xml:lang="en-US">
  <title>TOPIC TITLE HERE</title>
  <shortdesc>SHORT DESCRIPTION HERE</shortdesc>
  <prolog>
    <author>AUTHOR NAME</author>
    <critdates>
      <created date="YYYY-MM-DD"/>
    </critdates>
    <metadata>
      <!-- M1: Language - defined on root element above -->

      <!-- M2: Product affinity — choose from controlled list -->
      <data name="product" value="MC-5000"/>

      <!-- M3: Document status — start as draft -->
      <data name="document-status" value="draft"/>

      <!-- M4: Audience — choose from controlled list -->
      <data name="audience" value="maintenance-technician"/>

      <!-- M5: Safety criticality -->
      <data name="safety-critical" value="no"/>

      <!-- M6: Content type — match the DOCTYPE -->
      <data name="content-type" value="task"/>

      <!-- M7: Review cycle -->
      <data name="review-cycle" value="annual"/>
      <data name="review-owner" value="AUTHOR NAME"/>

      <!-- Optional: skill level -->
      <data name="skill-level" value="level-2"/>
    </metadata>
  </prolog>
  <taskbody>
    <context>...</context>
    <steps>...</steps>
  </taskbody>
</task>
```

---

## Metadata Quality Metrics

The framework tracks metadata health as a key quality metric.

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Metadata Completeness** | 100% M1-M7 | (topics with all 7 rules passing) / (total topics) |
| **Controlled Value Compliance** | 100% | (topics using controlled values) / (total topics with metadata) |
| **Staleness Rate** | < 5% | (topics past next-review date) / (total topics) |
| **Safety Metadata Coverage** | 100% of safety topics | (safety topics with complete M5 fields) / (total safety topics) |
| **Audience Classification Coverage** | 98% | (topics with at least one audience) / (total topics) |

**Current MC-5000 baseline (June 2026):**
- Completeness: 82.2% (target: 100% by Q3 2026)
- Controlled Value Compliance: 94.1%
- Staleness Rate: 12.3% (target: <5%)
- Safety Metadata Coverage: 88.7% (target: 100%)

---

## Metadata Dashboard: What to Monitor

A real-time dashboard tracks metadata compliance across all topics.

```
METADATA GOVERNANCE DASHBOARD — MC-5000
=======================================================

M1: Language Declaration     ████████████████████░  97% (239/247)
M2: Product Affinity          ██████████████████░░░  94% (232/247)
M3: Document Status           ████████████████████░  99% (245/247)
M4: Audience Classification   ██████████████████░░░  95% (234/247)
M5: Safety Criticality        ████████████████░░░░░  89% (22/25 safety topics)
M6: Content Type              ███████████████░░░░░░  82% (203/247)
M7: Review Cycle              ██████████████░░░░░░░  79% (195/247)

Overall Health Score: 86% (Target: 95%)

Top 5 Non-Compliant Topics:
  1. task-quick-clean.dita        (Missing: M2, M7)
  2. ref-part-numbers-legacy.dita (Missing: M1, M3, M6)
  3. concept-coolant-types.dita   (Missing: M4)
  ...

Last validated: 2026-06-07 14:32 UTC
Next scheduled: 2026-06-08 02:00 UTC
```

---

## Governance Enforcement Strategy

| Phase | Strategy | Timeline |
|-------|----------|----------|
| **Phase 1: Education** | Train all authors on M1-M7; provide templates and checklists | Month 1 |
| **Phase 2: Warning mode** | CI runs Schematron but only warns; report sent to authors | Month 2-3 |
| **Phase 3: Soft enforcement** | M1-M5 become blocking in CI for new topics; existing topics get warnings | Month 4 |
| **Phase 4: Full enforcement** | All M1-M7 are blocking for all topics; no exceptions without waiver | Month 5+ |
| **Phase 5: Continuous improvement** | Quarterly review of metadata rules; add new rules as needed | Ongoing |

**Waiver process**: Topics that cannot meet a metadata rule (e.g., legacy migrated content) require a formal waiver. Waivers are tracked in the dashboard, reviewed quarterly, and must be resolved within 90 days.

---

## Metadata Migration: v1.0 to v1.1

The framework v1.0 had no metadata governance. Migrating 847 legacy topics required an automated approach.

```bash
#!/bin/bash
# migrate-metadata.sh — Batch-add M1-M7 metadata to legacy topics
# Usage: ./migrate-metadata.sh content/en-US/topics/

TARGET_DIR="$1"

find "$TARGET_DIR" -name "*.dita" | while read topic; do
  echo "Processing: $topic"

  # M1: Add xml:lang from directory name (content/en-US/ → en-US)
  lang=$(echo "$topic" | grep -oP 'content/\K[^/]+')
  xmlstarlet ed -L -i "/*" -t attr -n "xml:lang" -v "$lang" "$topic"

  # M2: Add product data (default: MC-5000 for all migrated content)
  xmlstarlet ed -L \
    -s "//metadata" -t elem -n "data" -v "" \
    -i "//metadata/data[last()]" -t attr -n "name" -v "product" \
    -i "//metadata/data[last()]" -t attr -n "value" -v "MC-5000" \
    "$topic"

  # M3: Add document-status (migrated = "approved" since it was previously published)
  xmlstarlet ed -L \
    -s "//metadata" -t elem -n "data" -v "" \
    -i "//metadata/data[last()]" -t attr -n "name" -v "document-status" \
    -i "//metadata/data[last()]" -t attr -n "value" -v "approved" \
    "$topic"

  # M4: Add audience (default "general" if not determinable)
  xmlstarlet ed -L \
    -s "//metadata" -t elem -n "data" -v "" \
    -i "//metadata/data[last()]" -t attr -n "name" -v "audience" \
    -i "//metadata/data[last()]" -t attr -n "value" -v "general" \
    "$topic"

  # M5: Add safety-critical (infer from content)
  if grep -q "hazardstatement" "$topic"; then
    xmlstarlet ed -L \
      -s "//metadata" -t elem -n "data" -v "" \
      -i "//metadata/data[last()]" -t attr -n "name" -v "safety-critical" \
      -i "//metadata/data[last()]" -t attr -n "value" -v "yes" \
      "$topic"
  else
    xmlstarlet ed -L \
      -s "//metadata" -t elem -n "data" -v "" \
      -i "//metadata/data[last()]" -t attr -n "name" -v "safety-critical" \
      -i "//metadata/data[last()]" -t attr -n "value" -v "no" \
      "$topic"
  fi

  # M6: Add content-type (infer from DTD)
  if grep -q "task.dtd" "$topic"; then
    ctype="task"
  elif grep -q "concept.dtd" "$topic"; then
    ctype="concept"
  elif grep -q "reference.dtd" "$topic"; then
    ctype="reference"
  else
    ctype="topic"
  fi
  xmlstarlet ed -L \
    -s "//metadata" -t elem -n "data" -v "" \
    -i "//metadata/data[last()]" -t attr -n "name" -v "content-type" \
    -i "//metadata/data[last()]" -t attr -n "value" -v "$ctype" \
    "$topic"

  # M7: Add review-cycle (default: annual)
  xmlstarlet ed -L \
    -s "//metadata" -t elem -n "data" -v "" \
    -i "//metadata/data[last()]" -t attr -n "name" -v "review-cycle" \
    -i "//metadata/data[last()]" -t attr -n "value" -v "annual" \
    "$topic"
done

echo "Migration complete. Run Schematron validation to verify."
```

---

## Migration Statistics: Before and After

| Metric | v1.0 (Before) | v1.1 (After Script) | After Manual Fixes |
|--------|---------------|---------------------|---------------------|
| Topics with xml:lang | 54% (457/847) | 100% (847/847) | 100% |
| Topics with product | 0% | 100% | 100% |
| Topics with document-status | 12% (ad-hoc values) | 100% (default: "approved") | 100% (correct status) |
| Topics with audience | 0% | 100% (default: "general") | 92% (corrected manually) |
| Safety-critical flags | 0% | 100% (inferred) | 100% (verified by SME) |
| Content type | 0% (metadata) | 100% (inferred from DTD) | 100% |
| Review cycle | 0% | 100% (default: "annual") | 100% |

**Time saved by automation**: ~40 hours of manual metadata entry reduced to 1 hour of script execution + 8 hours of manual review/correction.

---

## Metadata Inheritance: Maps vs Topics

Metadata can be set at the **map level** and inherited by all child topics. This reduces duplication for shared metadata.

```xml
<!-- In the ditamap: define shared metadata -->
<map id="map-dr5000-maintenance" xml:lang="en-US">
  <topicmeta>
    <metadata>
      <!-- These apply to ALL topics in this map unless overridden -->
      <data name="product" value="MC-5000"/>
      <data name="audience" value="maintenance-technician"/>
      <data name="skill-level" value="level-2"/>
      <data name="review-cycle" value="annual"/>
    </metadata>
  </topicmeta>

  <!-- Child topics inherit map-level metadata -->
  <topicref href="topics/task-coolant-change.dita"/>
  <topicref href="topics/task-spindle-maintenance.dita"/>
  <!-- This topic can override: -->
  <topicref href="topics/task-safety-inspection.dita">
    <topicmeta>
      <metadata>
        <!-- Override: safety topics need level-3 skill -->
        <data name="skill-level" value="level-3"/>
        <data name="safety-critical" value="yes"/>
      </metadata>
    </topicmeta>
  </topicref>
</map>
```

**Inheritance rules:**
- Map-level metadata applies to all direct child topics
- Topic-level metadata overrides map-level metadata
- Nested sub-maps cascade: map > sub-map > topic
- `safety-critical` is never inherited (must be explicit per topic)

---

## Case Study: Metadata Failure at ExampleCorp (2025)

In Q2 2025, a MC-5000 shipped to a Thailand customer with missing safety documentation because metadata was not governed.

**Root cause chain:**
1. Safety topic `hazard-chemical-coolant.dita` was authored with no metadata
2. `xml:lang` was missing from the topic (M1 violation)
3. `safety-critical` was not flagged (M5 violation)
4. `product` not set (M2 violation)
5. The Thai translation vendor skipped the topic because it had no `xml:lang`
6. The automated build excluded it because it had no `product` matching the Thai configuration
7. The Thai manual shipped without chemical safety warnings

**Impact:**
- Customer discovered the gap during a safety audit
- 3-week remediation: emergency translation, expedited shipping of revised manual
- Total cost: $18,500 (translation rush fee + shipping + customer compensation)
- Root cause: zero metadata governance

**Lesson**: This exact scenario led to the M1-M7 framework. If any ONE of M1, M2, or M5 had been enforced, the failure would have been caught.

---

## Automated Metadata Remediation Tools

The framework includes Python tools for ongoing metadata maintenance.

```python
#!/usr/bin/env python3
# tools/fix-metadata.py — Auto-fix common metadata issues

import xml.etree.ElementTree as ET
import sys, os, glob

FIXES = {
    "add-xml-lang": lambda root, path: add_xml_lang(root, path),
    "add-product": lambda root, path: add_metadata(root, "product", "MC-5000"),
    "add-status": lambda root, path: add_metadata(root, "document-status", "draft"),
    "add-audience": lambda root, path: add_metadata(root, "audience", "general"),
    "add-safety": lambda root, path: infer_safety(root),
    "add-content-type": lambda root, path: infer_content_type(root),
    "add-review-cycle": lambda root, path: add_metadata(root, "review-cycle", "annual"),
}

def fix_topics(directory, fix_list):
    for dita_file in glob.glob(f"{directory}/**/*.dita", recursive=True):
        tree = ET.parse(dita_file)
        root = tree.getroot()
        modified = False

        for fix_name in fix_list:
            if fix_name in FIXES:
                if FIXES[fix_name](root, dita_file):
                    modified = True

        if modified:
            tree.write(dita_file, encoding="UTF-8", xml_declaration=True)
            print(f"  FIXED: {dita_file}")

# Usage:
# python3 tools/fix-metadata.py content/en-US/topics/ \
#   --fixes add-xml-lang,add-product,add-status
```

---

## Metadata Governance ROI

| Investment | Annual Cost | Annual Savings/Benefit |
|-----------|-------------|----------------------|
| Schematron development | $5,000 (one-time) | N/A |
| CI/CD pipeline maintenance | $2,000/year | $8,000/year (prevented rework) |
| Author training (metadata) | $3,000 (initial) | $7,500/year (reduced QA time) |
| Dashboard development | $4,000 (one-time) | $3,000/year (visibility) |
| Quarterly metadata review | $2,000/year | $5,000/year (staleness prevention) |
| **TOTAL** | **$16,000 initial + $4,000/yr** | **$23,500/year savings** |

**ROI**: 100% payback in < 1 year. Cumulative 5-year savings: ~$110,000.

**Intangible benefits:**
- No regulatory compliance failures (cost avoidance: $50K-$500K per incident)
- Consistent search and filtering (improved author productivity)
- Clean translation handoffs (20% fewer vendor questions)
- Auditability (ISO 9001 documentation control compliance)

---

<!-- _class: exercise -->

## Exercise: Metadata Audit and Remediation

**Task**: Audit a set of topics for M1-M7 compliance and fix all violations.

**Requirements:**
1. Run Schematron validation on the provided topic set (15 topics with known metadata issues)
2. Parse the SVRL report and categorize all errors by rule (M1-M7):
   - Count violations per rule
   - List the 5 most common violation types
3. Fix 10 violations across at least 4 different M-rules:
   - Add missing `xml:lang` to 3 topics
   - Add missing `product` data element to 2 topics
   - Fix invalid `document-status` value in 2 topics
   - Add `audience` to 2 topics
   - Add `safety-critical` flag to 1 hazard topic
4. Re-run validation and confirm all fixes pass
5. Generate a before-and-after compliance report
6. Submit 2 topics for waiver with justification (hypothetical legacy content)

**Time**: 30 minutes

---

<!-- _class: keypoint -->

## Key Takeaways

1. **Metadata is infrastructure**: Without governed metadata, you cannot filter, search, automate, audit, or translate content reliably
2. **M1-M7 rules** form the mandatory metadata backbone: Language (M1), Product (M2), Status (M3), Audience (M4), Safety (M5), Content Type (M6), Review Cycle (M7)
3. **Subject Scheme** defines controlled vocabularies; Schematron enforces them; CI/CD blocks non-compliant content
4. **Blocking vs Warning**: M1-M5 are blocking (CI fails), M6-M7 are warnings (CI passes, report generated)
5. **Safety metadata (M5)** is non-negotiable: every hazardstatement must be flagged with safety-critical, hazard-type, ISO reference, and risk-level
6. **Dashboards** track metadata health in real-time; target is 95%+ overall compliance
7. **Staleness tracking** (M7) prevents outdated safety procedures: next-review dates trigger automated alerts
8. **Enforcement is phased**: Education → Warning → Soft → Full → Continuous improvement
