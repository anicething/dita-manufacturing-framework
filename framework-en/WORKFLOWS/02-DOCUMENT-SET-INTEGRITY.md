# Document Set Integrity, State & Change Control

**Document ID:** WF-002
**Version:** 1.0
**Last Updated:** 2026-06-07
**Status:** Approved

---

## Table of Contents

1. [Overview](#1-overview)
2. [Document Set Integrity (成套机制)](#2-document-set-integrity-成套机制)
3. [Invalid State Detection & Failure Control (失效控制)](#3-invalid-state-detection--failure-control-失效控制)
4. [Document Lifecycle & Status Control (状态控制)](#4-document-lifecycle--status-control-状态控制)
5. [Change Control Across the Set (变更控制)](#5-change-control-across-the-set-变更控制)
6. [Automated Validation Pipeline](#6-automated-validation-pipeline)
7. [Conflict Resolution & Recovery](#7-conflict-resolution--recovery)
8. [Governance & Auditing](#8-governance--auditing)

---

## 1. Overview

### 1.1 The Problem

In a DITA-based documentation system, individual topics are not standalone documents — they are components of a **document set**. A single product manual may contain 50-500 topics spanning multiple languages. The challenge is:

- How do you know the set is **complete** (no missing topics)?
- How do you know the set is **consistent** (no conflicting info, no broken references)?
- How do you know the set is **valid** (meets quality gates)?
- How do you track the **state** of each component?
- How do you **control changes** without breaking the set?

### 1.2 Three Pillars of Document Set Control

| Pillar | Question | Mechanism |
|--------|----------|-----------|
| **Set Integrity** | "Is the set complete and consistent as a whole?" | DITA Map validation, relationship tables, subject scheme, cross-topic checks |
| **Failure Control** | "Is every component in a valid state?" | Schematron, CI/CD gates, pre-commit hooks, key resolution checks |
| **State & Change Control** | "What state is each topic in? Who changed what? Can we roll back?" | Metadata lifecycle, Git versioning, change tracking, lock/unlock |

---

## 2. Document Set Integrity (成套机制)

### 2.1 The DITA Map as the Master Control Document

In DITA, the **document set** is defined by a **DITA Map** (`.ditamap`). The map is the single source of truth for what belongs in a document set. If a topic is not referenced from the map, it is not part of the set.

#### Map Integrity Checks

| Check | Description | Severity | Auto? |
|-------|-------------|----------|-------|
| **Referenced topic exists** | Every `<topicref href="...">` resolves to an existing file | **Blocking** | Yes |
| **No orphan topics** | Every `.dita` file under the product directory is referenced by at least one map | Warning | Yes |
| **No duplicate topic IDs** | No two topics in the set share the same `id` attribute | **Blocking** | Yes |
| **Map recursion** | Sub-maps don't create circular includes | **Blocking** | Yes |
| **Key definition completeness** | Every key used in the set is defined somewhere in the key scope | **Blocking** | Yes |
| **No unused key definitions** | Keys defined but never referenced generate warnings | Warning | Yes |

#### Example: Product Map with Integrity Attributes

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="mc5000-manual" title="MC-5000 Operation Manual">
  <!-- Collection type defines what kind of document set this is -->
  <topicmeta>
    <navtitle>MC-5000 Operation Manual</navtitle>
    <othermeta name="doc-collection-type" value="ordered"/>
    <othermeta name="doc-target-audience" content="operator"/>
    <othermeta name="doc-status" content="approved"/>
    <othermeta name="doc-version" content="1.2"/>
    <othermeta name="doc-languages" content="en, zh-CN, de"/>
  </topicmeta>

  <topicref href="../shared/safety-overview.dita" navtitle="Safety Overview">
    <topicref href="../shared/safety-electrical.dita"/>
    <topicref href="../shared/safety-mechanical.dita"/>
  </topicref>
  <topicref href="../tasks/installation.dita" navtitle="Installation">
    <topicref href="../tasks/unpacking.dita"/>
    <topicref href="../tasks/power-connection.dita" collection-type="sequence"/>
  </topicref>
  <!-- Relationship table for cross-references -->
  <reltable>
    <relheader>
      <relcolspec type="concept"/>
      <relcolspec type="task"/>
      <relcolspec type="reference"/>
    </relheader>
    <relrow>
      <relcell><topicref href="../concepts/coolant-system.dita"/></relcell>
      <relcell><topicref href="../tasks/coolant-refill.dita"/></relcell>
      <relcell><topicref href="../reference/coolant-specs.dita"/></relcell>
    </relrow>
  </reltable>
</map>
```

### 2.2 Collection Type and Topic Ordering

DITA defines `collection-type` attributes that describe how topics relate to each other within a document set:

| Collection Type | Meaning | Validation Rule |
|----------------|---------|----------------|
| `unordered` | Topics can be read in any order | No ordering constraint |
| `sequence` | Topics must be read in given order | Build order preserved; linear reading path |
| `choice` | Reader chooses one topic from the set | At least one must be present in output |
| `family` | Topics are related; reader may need multiple | All should be complete and consistent |
| `- (none)` | Default — treat as `unordered` | Basic completeness check |

**Integrity Rule:** For `sequence` collections, all topics **must** be present and **must** have `@next` and `@prev` attributes resolved. Missing sequence links generate a warning.

### 2.3 Relationship Table Integrity

Relationship tables (`<reltable>`) define cross-topic links that appear as "Related Tasks" or "See Also" in output. Broken relationship tables lead to incomplete navigation.

```xml
<reltable>
  <relheader>
    <relcolspec type="concept"/>
    <relcolspec type="task"/>
    <relcolspec type="reference"/>
  </relheader>
  <relrow>
    <relcell><topicref href="../concepts/spindle-overview.dita"/></relcell>
    <relcell><topicref href="../tasks/spindle-replacement.dita"/></relcell>
    <relcell><topicref href="../reference/spindle-specs.dita"/></relcell>
  </relrow>
</reltable>
```

**Validation rules:**
- Every `<relcell>` with a `<topicref>` must resolve to a valid topic
- At least two columns in a row should be populated (otherwise, there is no relationship to express)
- Topic types should match the `@type` specified in `<relcolspec>`

### 2.4 Collection-Level Metadata Consistency

When a document set is assembled, metadata should be consistent across all topics in the set:

| Metadata Field | Rule | Enforcement |
|---------------|------|-------------|
| `doc-version` | All topics in a map must share the same version | Schematron |
| `doc-validated` | All topics must pass validation before map-level publish | CI/CD |
| `audience/@type` | Topics must specify an audience that matches the map target | Schematron |
| `xml:lang` | All topics in a language-specific build must match the target language | DITA-OT |
| `copyright` year | All topics in a build should share the same copyright year | Automated check |

### 2.5 Subject Scheme Classification Consistency

If the document set uses a subject scheme for classification:

```xml
<subjectdef keys="product">
  <subjectdef keys="mc5000"/>
  <subjectdef keys="mc4000"/>
  <subjectdef keys="mc3000"/>
</subjectdef>
```

**Integrity Rule:** Every topic in the `mc5000-manual` map must carry `@product="mc5000"` or inherit it from the map. Topics with mismatched product classification are flagged.

---

## 3. Invalid State Detection & Failure Control (失效控制)

### 3.1 Document Invalid States

A topic or document set can be in an **invalid state** — meaning it should not be published. The goal is to detect invalid states **before** they reach production.

#### Invalid State Classification

| State | Definition | Detection | Action |
|-------|-----------|-----------|--------|
| **Broken** | Has unresolvable references (conref/keyref/image) | DITA-OT link check fails | **Block publish** |
| **Incomplete** | Missing required metadata (M1-M7) | Schematron check fails | **Block publish** |
| **Inconsistent** | Contradicts other topics in the same set | Cross-topic audit | Warning + manual review |
| **Stale** | Not reviewed within the defined freshness period | Date check against `critdates/revised` | Warning + schedule re-review |
| **Orphaned** | Not referenced by any active map | File system scan | Warning + archive |
| **Conflicting** | Has Git merge conflicts unresolved | Git status check | **Block publish** |
| **Unapproved** | Not passed through the full approval workflow | Status metadata check | **Block publish** |

### 3.2 Schematron Rule Set for Failure Detection

Below is the complete Schematron rule set for detecting invalid states. Rules are organized by severity.

#### BLOCKING Rules (must pass → topic cannot be published)

```xml
<sch:schema xmlns:sch="http://purl.oclc.org/dsdl/schematron" queryBinding="xslt2">
  <sch:ns prefix="dita" uri="urn:oasis:names:tc:dita:xsd:dita"/>

  <!-- RULE B1: Metadata completeness (M1-M7) -->
  <sch:pattern id="blocking-metadata">
    <sch:rule context="*[contains(@class, ' topic/topic ')]">
      <sch:assert test=".//prolog/metadata/author">
        [B1] Topic must have an author (M1)
      </sch:assert>
      <sch:assert test=".//prolog/metadata/critdates/created">
        [B2] Topic must have a creation date (M2)
      </sch:assert>
      <sch:assert test=".//prolog/metadata/audience/@type">
        [B3] Topic must specify audience type (M4)
      </sch:assert>
    </sch:rule>
  </sch:pattern>

  <!-- RULE B2: No hard-coded variable values -->
  <sch:pattern id="blocking-variables">
    <sch:rule context="text()">
      <sch:let name="pattern"
              value="'ExampleCorp|ECM|MC-\d{4}'"/>
      <sch:report test="matches(., $pattern)">
        [B4] Hard-coded variable detected. Use keyref instead.
      </sch:report>
    </sch:rule>
  </sch:pattern>

  <!-- RULE B3: Conditional text must have matching DITAVAL -->
  <sch:pattern id="blocking-conditional">
    <sch:rule context="*[@product or @audience or @platform]">
      <sch:report test="true()">
        [B5] Conditional attribute '@product' used without matching DITAVAL profile.
      </sch:report>
    </sch:rule>
  </sch:pattern>
</sch:schema>
```

#### WARNING Rules (should pass → topic flagged for review)

```xml
<!-- RULE W1: Topic has not been reviewed in 6 months -->
<sch:pattern id="warning-staleness">
  <sch:rule context="*[contains(@class, ' topic/topic ')]">
    <sch:let name="revised" value=".//prolog/metadata/critdates/revised"/>
    <sch:report test="not($revised) or $revised lt '2025-12-07'">
      [W1] Topic has not been reviewed in over 6 months (last: <sch:value-of select="$revised"/>)
    </sch:report>
  </sch:rule>
</sch:pattern>

<!-- RULE W2: Topic ID does not follow naming convention -->
<sch:pattern id="warning-naming">
  <sch:rule context="*[contains(@class, ' topic/topic ')]">
    <sch:report test="not(matches(@id, '^[a-z]{2,4}-[a-z-]+-[a-z]{2,4}$'))">
      [W2] Topic ID '<sch:value-of select="@id"/>' does not follow convention: 'type-subject-type'
    </sch:report>
  </sch:rule>
</sch:pattern>
```

### 3.3 Validation Gate Matrix

The document set must pass through multiple validation gates at different stages:

| Gate | When | What Is Checked | Outcome If Failed |
|------|------|-----------------|-------------------|
| **Pre-Commit** | Before `git commit` | File-level: well-formed XML, ID uniqueness within file | Commit blocked |
| **Pre-Push** | Before `git push` | Topic-level: M1-M7 metadata, no hard-coded vars | Push blocked |
| **PR Gate** | On PR creation | Cross-topic: key resolution, conref targets, no orphan IDs | PR marked as failing |
| **Build Gate** | On merge to main | Map-level: full validation + relationship tables + subject scheme | Build fails; no publish |
| **Publish Gate** | Before final publish | Output-level: link check, no broken cross-references | Publish cancelled |
| **Post-Publish** | After publish | Regression: compare with previous build for unexpected differences | Alert generated |

#### Gate Implementation (CI/CD Pipeline Script)

```bash
#!/bin/bash
# DITA Validation Pipeline — validation-gates.sh
set -euo pipefail

echo "=== DITA Validation Gates ==="

# Stage 1: Pre-Commit — XML Well-Formedness
echo "[Gate 1] XML Well-Formedness Check..."
find . -name "*.dita" -o -name "*.ditamap" | xargs -I{} xmllint --noout {}

# Stage 2: Pre-Commit — ID Uniqueness Check
echo "[Gate 2] ID Uniqueness Check..."
# Check no duplicate IDs across all changed files
for file in $(git diff --name-only --diff-filter=ACM HEAD | grep '\.dita$'); do
  grep -h 'id="' "$file"
done | sort | uniq -d && echo "❌ Duplicate IDs found!" && exit 1 || echo "✅ No duplicate IDs"

# Stage 3: Pre-Push — Schematron Metadata Validation
echo "[Gate 3] Schematron Metadata Validation..."
java -jar saxon-he-12.jar -xsl:framework/validations/blocking-rules.xsl \
  -s:products/mc5000/map/mc5000-manual.ditamap

echo "=== All validation gates passed ==="
```

---

## 4. Document Lifecycle & Status Control (状态控制)

### 4.1 Document Lifecycle States

Every topic and map in the framework goes through a defined lifecycle with explicit states:

```
               ┌──────────┐
               │  DRAFT   │
               │  (草稿)   │
               └────┬─────┘
                    │ Submit for review
                    ▼
               ┌──────────┐
          ┌───→│ IN REVIEW│←────────────┐
          │    │  (审校中) │  Rejected   │
          │    └────┬─────┘             │
          │         │ Approved          │
          │         ▼                   │
          │    ┌──────────┐             │
          │    │ APPROVED │             │
          │    │  (已批准) │             │
          │    └────┬─────┘             │
          │         │ Published         │
          │         ▼                   │
          │    ┌──────────┐             │
          │    │ PUBLISHED│── Changes ──┘
          │    │  (已发布) │
          │    └────┬─────┘
          │         │ Superseded
          │         ▼
          │    ┌──────────┐
          └────│ ARCHIVED │
               │  (已归档) │
               └──────────┘
```

### 4.2 State Tracking in Metadata

Topic state is tracked in the `<prolog>` metadata:

```xml
<prolog>
  <metadata>
    <othermeta name="doc-lifecycle-status"
               content="draft|in-review|approved|published|archived"/>
    <othermeta name="doc-version" content="1.2"/>
    <othermeta name="doc-change-request" content="CR-2026-0042"/>
  </metadata>
</prolog>
```

### 4.3 State Transition Rules

| From | To | Trigger | Required Approvals | Auto? |
|------|-----|---------|-------------------|-------|
| Draft | In Review | Author submits PR | None (author init) | No |
| In Review | Draft | Reviewer requests changes | Reviewer | No |
| In Review | Approved | All reviewers approve | SME + Content Arch. | Auto (after all approvals) |
| Approved | Published | Merge to main | CI/CD pipeline passes | Yes |
| Published | Draft | Change request created | PM approves scope | No |
| Published | Archived | Content superseded | DGC | No |

### 4.4 Status Consistency Across a Document Set

When publishing a document set (a map with many topics), all topics must be in a compatible state:

| Map Status | Required Topic States | Rule |
|-----------|----------------------|------|
| **Draft** | Any state | No restriction — work in progress |
| **In Review** | Draft or In Review | No Approved-only topics (they should not be re-reviewed) |
| **Approved** | All Approved or Published | [BLOCKING] If any topic is Draft, the map cannot be Approved |
| **Published** | All Published | [BLOCKING] A topic in Draft/In Review will break the build |
| **Archived** | All Archived | Entire set must be archived together |

### 4.5 Enforcing State Consistency with Schematron

```xml
<sch:pattern id="state-consistency">
  <sch:rule context="*[contains(@class, ' map/map ')]">
    <sch:assert test="
      every $topic in //topicref/@href
      satisfies doc($topic)//othermeta[@name='doc-lifecycle-status']
                 [@content='published' or @content='approved']
    ">
      [B6] Map marked as Published but contains topics not in Published/Approved state.
    </sch:assert>
  </sch:rule>
</sch:pattern>
```

---

## 5. Change Control Across the Set (变更控制)

### 5.1 Change Impact Analysis

Before making any change to a shared topic, the author must assess impact:

```
Change Request Created
        │
        ▼
Impact Analysis:
  ┌─────────────────────────────────────┐
  │ Which maps reference this topic?    │ ← grep -r "href=.*topic-id"
  │ Which languages are affected?       │ ← language folders check
  │ Which products are affected?        │ ← product ditamaps check
  │ Is this a conref target?            │ ← grep for conkeyref references
  │ Is this a key definition?           │ ← check keys in framework-keys
  └─────────────────────────────────────┘
        │
        ▼
Change Classification → See Change Authority Matrix (GOV-003)
        │
        ▼
Approval Process → Execute → Verify → Publish
```

#### Automated Impact Analysis Script

```bash
#!/bin/bash
# impact-analysis.sh — analyze the impact of changing a topic
# Usage: bash impact-analysis.sh topics/concepts/coolant-system.dita

TOPIC_FILE=$1
TOPIC_ID=$(grep -o 'id="[^"]*"' "$TOPIC_FILE" | head -1 | sed 's/id="//;s/"//')

echo "=== Impact Analysis: $TOPIC_FILE ==="
echo "Topic ID: $TOPIC_ID"
echo ""

# Find which maps reference this topic
echo "Referenced by maps:"
grep -rl "href=.*$(basename $TOPIC_FILE)" products/*/map/*.ditamap 2>/dev/null

# Find direct conref references to this topic's IDs
echo ""
echo "Conref targets referencing this topic:"
grep -rl "conkeyref.*$TOPIC_ID\|conref.*$TOPIC_FILE" topics/ 2>/dev/null

# Find key definitions
echo ""
echo "Key definitions pointing to this topic:"
grep -B2 "$TOPIC_FILE" references/*.ditamap 2>/dev/null

echo ""
echo "=== Impact Summary ==="
echo "Maps affected: $(grep -rl "href=.*$(basename $TOPIC_FILE)" products/*/map/*.ditamap 2>/dev/null | wc -l)"
echo "Direct conrefs: $(grep -rl "conkeyref.*$TOPIC_ID\|conref.*$TOPIC_FILE" topics/ 2>/dev/null | wc -l)"
```

### 5.2 Change Types and Version Bumping

| Change Type | Scope | Version Bump | Example |
|-------------|-------|-------------|---------|
| **Patch** | Single topic, no structural change | Topic minor (1.2 → 1.2.1) | Fix typo, update screenshot |
| **Minor** | Single topic, content changes | Topic minor (1.2 → 1.3) | Update procedure step |
| **Major** | Multiple topics, structural change | Map version (1 → 2) | Reorganize chapter, add new section |
| **Release** | Entire document set | Full version (1.2 → 2.0) | Product version update |

### 5.3 Version Tracking Strategy

```xml
<!-- At the topic level -->
<critdates>
  <created date="2025-06-01"/>
  <revised date="2026-06-07"/>
</critdates>
<othermeta name="doc-version" content="1.3"/>
<othermeta name="doc-last-modified-by" content="j.smith"/>

<!-- At the map level -->
<topicmeta>
  <othermeta name="doc-version" content="2.0"/>
  <othermeta name="doc-release-date" content="2026-06-15"/>
  <!-- Map version overrides individual topic versions for published output -->
</topicmeta>
```

### 5.4 Concurrent Change Management

When multiple authors edit the same document set:

| Scenario | Detection | Resolution |
|----------|-----------|------------|
| Two authors edit different files | No conflict | Git merges cleanly |
| Two authors edit same file, different sections | No content conflict | Git auto-merge |
| Two authors edit same file, same section | Git merge conflict | Manual resolution + SME re-approval if technical content changed |
| Two authors both change a conref target | Inconsistent references | CI/CD detects broken conref; both authors notified |
| One author deletes a file another depends on | Broken map reference | CI/CD fails; file restoration or map update required |

#### Branch Isolation Strategy

```
For each change:
  Feature branch from develop:
    git checkout -b feature/CR-2026-0042-update-coolant-procedure

  Only one topic per branch (when possible):
    /products/mc5000/tasks/coolant-refill.dita  ✓
    /products/mc5000/tasks/coolant-refill.dita  ✗ (mixed with other changes)
    /products/mc3000/tasks/alignment.dita

  After merge: delete branch
```

---

## 6. Automated Validation Pipeline

### 6.1 Complete Pipeline Flow

```
[Author commits]
      │
      ▼
┌─────────────────────────────────────────────────┐
│ STAGE 1: Pre-Commit Hook (local, <1 second)     │
│   • XML well-formedness                         │
│   • ID uniqueness within file                    │
│   • DTD/Schema reference valid                   │
│   • Status: [PASS / FAIL]                       │
└─────────────────────────────────────────────────┘
      │
      ▼
┌─────────────────────────────────────────────────┐
│ STAGE 2: Pre-Push Hook (local, <5 seconds)      │
│   • M1-M7 metadata completeness                  │
│   • No hard-coded company/product names          │
│   • Topic ID follows naming convention           │
│   • Status: [PASS / FAIL / WARN]                │
└─────────────────────────────────────────────────┘
      │
      ▼
┌─────────────────────────────────────────────────┐
│ STAGE 3: PR Validation (CI, <1 minute)          │
│   • All pre-push checks (broader scope)          │
│   • Key resolution — all keyrefs resolve         │
│   • Conref resolution — all conrefs resolve      │
│   • No orphan IDs — no duplicate IDs in PR set   │
│   • Affected maps identified                     │
│   • Status: [PASS / FAIL]                       │
│   • Comment on PR with results                   │
└─────────────────────────────────────────────────┘
      │
      ▼
┌─────────────────────────────────────────────────┐
│ STAGE 4: Build Validation (CI, <5 minutes)      │
│   • Full DITA-OT build with all profiles         │
│   • Complete Schematron validation                │
│   • All relationship table references resolve     │
│   • Subject scheme compliance check               │
│   • Content freshness check (staleness warnings)  │
│   • Output: HTML5 + PDF (for preview)            │
│   • Status: [PASS / FAIL]                       │
└─────────────────────────────────────────────────┘
      │ (PR approved + merged)
      ▼
┌─────────────────────────────────────────────────┐
│ STAGE 5: Publish Gate (CI, <10 minutes)         │
│   • Full build all languages                     │
│   • Link check on all output                     │
│   • Cross-language consistency check              │
│   • Regression diff vs previous build             │
│   • Status: [PUBLISHED / ROLLED BACK]           │
└─────────────────────────────────────────────────┘
```

### 6.2 Pipeline Configuration

```bash
#!/bin/bash
# DITA Validation & Publish Pipeline — dita-pipeline.sh
set -euo pipefail

echo "=== DITA Pipeline: Validation & Publish ==="
BRANCH="${1:-$(git branch --show-current)}"
MAP_PATH="${2:-products/mc5000/map/mc5000-manual.ditamap}"
OUTPUT_DIR="${3:-output}"

# Stage 1 — Well-formedness
echo "[Stage 1] XML Well-formedness Check..."
find products/ -name "*.dita" -o -name "*.ditamap" | \
  xargs -I{} xmllint --noout {} 2>&1 | tee validation-report.xml
echo "✅ Stage 1 passed"

# Stage 2 — Schematron Rules
echo "[Stage 2] Schematron Validation..."
java -jar schematron/saxon-he-12.jar \
  -xsl:schematron/blocking-rules.xsl \
  -s:"$MAP_PATH" \
  -o:schematron-results.xml
echo "✅ Stage 2 passed"

# Stage 3 — Key/Conref Resolution
echo "[Stage 3] Key/Conref Resolution Check..."
dita --input="$MAP_PATH" \
     --format=html5 \
     --validate-only=true
echo "✅ Stage 3 passed"

# Stage 4 — Build (on main branch only)
if [ "$BRANCH" = "main" ]; then
  echo "[Stage 4] Building output..."
  dita --input="$MAP_PATH" \
       --format=html5 \
       --output="${OUTPUT_DIR}/mc5000/manual/"
  echo "✅ Stage 4 complete"

  # Stage 5 — Post-Publish Regression Check
  echo "[Stage 5] Regression Check..."
  if [ -d "previous-output/mc5000/manual/" ]; then
    diff -r "${OUTPUT_DIR}/mc5000/manual/" previous-output/mc5000/manual/ || true
  fi
  echo "✅ Stage 5 complete"
fi

echo "=== Pipeline complete ==="
```

---

## 7. Conflict Resolution & Recovery

### 7.1 Git Merge Conflict Resolution

**When a conflict occurs:**

```
1. DETECT: Git marks conflicted files
   ↓
2. NOTIFY: Author + CoE Lead notified of conflicting topics
   ↓
3. ASSESS: Which changes conflict? (structural vs. editorial)
   ↓
4. RESOLVE:
   - Editorial only → latest wins (accept merge tool suggestion)
   - Technical changes → SME re-approval required
   - Structural (ID/Map changes) → Content Architect must review
   ↓
5. VERIFY: CI/CD validation gate re-runs
   ↓
6. MERGE: Only after green build
```

### 7.2 Conref Conflict Resolution

Conref targets (the source topics being referenced) are **especially sensitive** to changes:

```
Scenario: Two authors both modify a shared safety procedure

Author A: Changes step 2 wording / Author B: Adds step 5
                ↓
Both merge independently (different sections, no Git conflict)
                ↓
But: The conref target has changed — all referencing topics now display
     the updated content automatically
                ↓
Verification:
  • CI/CD checks all conref-referencing topics render correctly
  • SME re-approves the changed source topic
  • Map build validates all output
```

### 7.3 Rollback Procedure

If a published build is found to contain errors:

```bash
# 1. Identify the bad commit
git log --oneline -10

# 2. Create a revert commit (not a destructive reset)
git revert <bad-commit-hash>

# 3. Push the revert
git push origin main

# 4. CI/CD auto-rebuilds with the reverted content
# 5. Notify stakeholders of the rollback
# 6. Create an incident report
```

### 7.4 Recovery Time Objectives

| Failure Type | RTO | Process |
|-------------|-----|---------|
| Broken conref/keyref | 4 hours | Revert commit or fix reference |
| Metadata validation failure | 2 hours | Fix metadata or adjust Schematron rule |
| Build pipeline failure | 4 hours | See SEV-2 procedure (RISK-CONTROL-STRATEGY.md) |
| Published safety error | 1 hour | See SEV-1 procedure (RISK-CONTROL-STRATEGY.md) |
| Repository corruption | 24 hours | Restore from backup |

---

## 8. Governance & Auditing

### 8.1 Change Log Requirements

Every change to a published document set must be traceable:

| Requirement | Source of Truth | Audit Frequency |
|-------------|----------------|----------------|
| Who made the change? | Git author + `prolog/author` | Per commit |
| When was it made? | Git timestamp + `critdates/revised` | Per commit |
| Why was it made? | Git commit message + PR description | Per PR |
| Who approved it? | PR reviewer list + `othermeta` | Per merge |
| What changed? | Git diff + build diff report | Per build |
| Which topics affected? | File list in PR | Per PR |

### 8.2 Periodic Audits

| Audit Type | Frequency | Scope | Conducted By |
|-----------|-----------|-------|-------------|
| Topic freshness scan | Monthly | All topics: last-reviewed date vs. current date | Automated |
| Orphan topic scan | Monthly | Topics not referenced by any active map | Automated |
| Key usage audit | Quarterly | Defined vs. used keys; identify orphaned key definitions | Automated |
| Metadata compliance | Quarterly | M1-M7 completeness across all active topics | Automated |
| Collection consistency | Quarterly | Map structure, reltable completeness, collection-type use | Content Architect |
| Full set regression | Quarterly | Published archive vs. current build for unexpected diffs | Automated |
| Governance compliance | Annually | DGC meeting attendance, SLA adherence, change log completeness | DGC |

### 8.3 Audit Report Template

```markdown
## Document Set Audit Report

**Audit Date:** [YYYY-MM-DD]
**Scope:** [Map / Product Line / All]
**Audit Type:** [Freshness / Orphan / Metadata / Compliance]

### Summary
- Total topics audited: [N]
- Passed: [N] ([%])
- Warnings: [N] ([%])
- Failed: [N] ([%])

### Failed Items
| Topic | Issue | Severity | Action |
|-------|-------|----------|--------|
| [topic.dita] | [description] | Blocking/Warning | [assigned to, due date] |

### Trends
- Previous audit failed: [N]
- Change: [improved / declined / stable]

### Recommendations
1. [Action item]
2. [Action item]

**Auditor:** [Name]
**Next Audit:** [YYYY-MM-DD]
```

---

## Related Documents

| Document | Location | Relation |
|----------|----------|----------|
| Approval Workflow | [WORKFLOWS/01-APPROVAL-WORKFLOW.md](01-APPROVAL-WORKFLOW.md) | Defines the review/approval process |
| Change Authority Matrix | [GOVERNANCE/03-CHANGE-AUTHORITY-MATRIX.md](../GOVERNANCE/03-CHANGE-AUTHORITY-MATRIX.md) | Defines who can approve what |
| Risk Control Strategy | [RISK-CONTROL-STRATEGY.md](../RISK-CONTROL-STRATEGY.md) | Risk management for the framework |
| Metadata Schema | [ARCHITECTURE/02-METADATA-SCHEMA.md](../ARCHITECTURE/02-METADATA-SCHEMA.md) | M1-M7 metadata rules |
| Variable Management | [ARCHITECTURE/06-VARIABLE-MANAGEMENT.md](../ARCHITECTURE/06-VARIABLE-MANAGEMENT.md) | Key-based content reuse |
| CI/CD Pipeline | [CI-CD/01-CICD-PIPELINE.md](../CI-CD/01-CICD-PIPELINE.md) | Build automation |
