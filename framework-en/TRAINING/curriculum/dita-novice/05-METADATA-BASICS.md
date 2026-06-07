---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Metadata Basics
## Module 05: The DITA Prolog and Content Metadata

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Define DITA metadata and explain its business purpose
- Identify all major elements of the `<prolog>` structure
- Write correct author, publisher, copyright, and audience metadata
- Populate category, othermeta, and prodinfo elements
- Apply the M1-M7 validation rules from the framework architecture docs
- Distinguish metadata patterns for concept, task, and reference topics
- Add proper metadata to a sample manufacturing topic

---

<!-- _class: divider -->

# Part 1: What Is Metadata?

---

# What Is Metadata?

**Metadata = Data about data**

In DITA, metadata is structured information embedded in every topic that describes:

- **Who** wrote it and owns it
- **What** product and version it relates to
- **When** it was created and last modified
- **Why** it exists (content type, purpose)
- **Who** should read it (audience)
- **How** it should be used (processing, publishing flags)

> Think of metadata as the passport for each topic — it travels with the content across every output channel.

---

# Why Metadata Matters in Manufacturing

| Business Need | Metadata Role |
|---|---|
| **ISO 9001 Audit** | Prove document ownership and review dates |
| **ISO 27001** | Track information classification and handling |
| **Multi-Product Lines** | Filter content by product code (MC-5000 vs MC-8000) |
| **Role-Based Delivery** | Show operators only operator content |
| **Translation Management** | Flag topics as translatable; track source language |
| **Compliance Reporting** | Extract all hazard-tagged topics in one query |
| **Content Reuse Analytics** | Which topics are used in which publications |

---

# Metadata: Traditional vs. DITA

| Traditional (Markdown/Word) | DITA |
|---|---|
| Filename conventions | Structured `<prolog>` element |
| Comments in document header | Machine-readable XML |
| Manual spreadsheet tracking | Queryable XPath/XQuery |
| Inconsistent across authors | Enforced by templates and Subject Scheme |
| No automated validation | CI/CD validates every topic |
| Lost on copy-paste | Travels with the topic always |

> DITA metadata is **embedded, structured, and enforceable**. Free-text MS Word headers cannot match this.

---

# The Prolog: DITA's Metadata Container

```xml
<concept id="concept-spindle-warmup">
  <title>Spindle Warm-Up Cycle</title>
  <prolog>
    <!-- All metadata lives here -->
  </prolog>
  <conbody>
    <!-- Topic content -->
  </conbody>
</concept>
```

The `<prolog>` appears **before** the topic body and is **optional** in the DITA standard — but **mandatory** in our framework.

> Every topic template (T1-T10) includes a complete prolog structure. Never delete it.

---

# Prolog Position in Topic Hierarchy

```
<concept> / <task> / <reference> / <troubleshooting> / <glossentry>
├── <title>           ← Required
├── <shortdesc>       ← Required (our framework)
├── <prolog>          ← Required (our framework)
│   ├── <author>
│   ├── <source>
│   ├── <publisher>
│   ├── <copyright>
│   ├── <critdates>
│   ├── <permissions>
│   ├── <metadata>
│   │   ├── <audience>
│   │   ├── <category>
│   │   ├── <prodinfo>
│   │   └── <othermeta>
│   └── <resourceid>
└── <conbody> / <taskbody> / <refbody>
```

> The prolog always sits between `<shortdesc>` and the body element.

---

<!-- _class: keypoint -->

# Key Rule: Prolog Must Precede the Body

```xml
<!-- CORRECT -->
<concept id="my-topic">
  <title>Topic Title</title>
  <shortdesc>Brief description.</shortdesc>
  <prolog>...</prolog>          <!-- Prolog BEFORE body -->
  <conbody>...</conbody>
</concept>

<!-- WRONG — validation error -->
<concept id="my-topic">
  <title>Topic Title</title>
  <conbody>...</conbody>
  <prolog>...</prolog>          <!-- Prolog AFTER body = INVALID -->
</concept>
```

---

<!-- _class: divider -->

# Part 2: Author and Source Elements

---

# Prolog Building Block: `<author>`

```xml
<prolog>
  <author type="creator">Maria Schmidt</author>
  <author type="contributor">John Chen</author>
  <author type="reviewer">Dr. Anna Wagner</author>
</prolog>
```

**`@type` values in our framework:**

| Type | Meaning | Example |
|---|---|---|
| `creator` | Original author | Technical Writer |
| `contributor` | Added substantial content | SME providing torque specs |
| `reviewer` | Performed technical review | Senior Engineer |
| `editor` | Style/grammar review | Documentation Editor |

> **M1 Rule:** Every topic must have at least one `<author type="creator">`.

---

# Author Element — Manufacturing Examples

```xml
<!-- Task topic: one creator, one SME contributor -->
<prolog>
  <author type="creator">Maria Schmidt</author>
  <author type="contributor">Jens Mueller, Spindle Engineering</author>
</prolog>

<!-- Reference topic: multiple contributors -->
<prolog>
  <author type="creator">John Chen</author>
  <author type="contributor">Spindle Engineering Team</author>
  <author type="contributor">Safety Compliance Office</author>
</prolog>
```

> Include the department or team for traceability — especially when a group contributed data.

---

# Prolog Building Block: `<source>`

```xml
<prolog>
  <source>MC-5000 Engineering Specification ES-2026-0142</source>
</prolog>
```

**What to put in `<source>`:**
- Engineering spec number the content is derived from
- Legacy document reference (for migration tracking)
- Standards document (ISO, ANSI, GB/T)
- SME interview notes reference

```xml
<source>ISO 12100:2010, Section 6.2.3 — Risk Assessment for Moving Parts</source>
<source>MC-8000 Field Service Bulletin FSB-2025-089</source>
```

> **M2 Rule:** If the topic derives from a regulated source document, `<source>` is mandatory.

---

# `<source>` — Migration Tracking Pattern

**Scenario:** You migrated a 500-page Word manual into DITA topics.

```xml
<!-- Each migrated topic carries the trace-back -->
<prolog>
  <source>Legacy Manual M-OP-4500 v3.2, Chapter 5, pp 67-82</source>
  <author type="creator">Maria Schmidt (migration)</author>
  <author type="contributor">Original: H. Wagner, 2019</author>
</prolog>
```

> During ISO audits, the auditor can verify that every requirement in the legacy manual has a corresponding DITA topic.

---

<!-- _class: divider -->

# Part 3: Publisher, Copyright, and Critical Dates

---

# Prolog Building Block: `<publisher>`

```xml
<prolog>
  <publisher>
    <name>Manufacturing Enterprise GmbH</name>
  </publisher>
</prolog>
```

**Our framework standard value:**

```xml
<publisher>
  <name>Manufacturing Enterprise GmbH</name>
</publisher>
```

This is constant across all topics. The publisher may differ per regional subsidiary:

```xml
<!-- German subsidiary -->
<publisher><name>Manufacturing Enterprise GmbH</name></publisher>

<!-- Chinese subsidiary -->
<publisher><name>Manufacturing Enterprise (Shanghai) Co., Ltd.</name></publisher>
```

> Consistency check: CI/CD validates that `<publisher>` matches one of the approved values.

---

# Prolog Building Block: `<copyright>`

```xml
<prolog>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
</prolog>
```

**Multi-year pattern:**

```xml
<copyright>
  <copyryear year="2019"/>  <!-- Original creation -->
  <copyryear year="2021"/>  <!-- Major revision -->
  <copyryear year="2026"/>  <!-- Current revision -->
  <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
</copyright>
```

> **M3 Rule:** The most recent `<copyryear>` must match the current calendar year or the year of last substantive change.

---

# Copyright — Manufacturing Nuances

**Shared copyright for co-developed content:**

```xml
<copyright>
  <copyryear year="2026"/>
  <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  <copyrholder>Siemens AG (Control System Content)</copyrholder>
</copyright>
```

**Third-party content:**

```xml
<copyright>
  <copyryear year="2025"/>
  <copyrholder>Renishaw plc</copyrholder>
</copyright>
<!-- Used for probe calibration procedures provided by Renishaw -->
```

> When content comes from an OEM partner, their copyright MUST be preserved. Check your supplier agreements.

---

# Prolog Building Block: `<critdates>` — Critical Dates

```xml
<prolog>
  <critdates>
    <created date="2025-03-15"/>
    <revised modified="2026-06-01"/>
  </critdates>
</prolog>
```

**Date format:** `YYYY-MM-DD` (ISO 8601) — no exceptions.

| Element | Meaning | Auto-Managed? |
|---|---|---|
| `<created date="...">` | First commit of this topic | Set once, never changes |
| `<revised modified="...">` | Last content change | Updated by CI/CD on merge |

> **M4 Rule:** `<created>` must be present. `<revised>` must be updated on every substantive change (not just typo fixes).

---

# Critical Dates — Git Integration

Our CI/CD pipeline auto-updates `<revised>` from Git history:

```
GitHub Actions workflow:
  1. Detect topic files changed in merged PR
  2. Git log --diff-filter=M <file> → last modified date
  3. Update <revised modified="YYYY-MM-DD"/> in prolog
  4. Commit and push the metadata update
```

**Manual override:** If the CI/CD pipeline is unavailable, update `<revised>` manually before commit.

```xml
<critdates>
  <created date="2025-03-15"/>
  <revised modified="2026-06-07"/>  <!-- Today's date -->
</critdates>
```

---

<!-- _class: divider -->

# Part 4: Permissions and Resource IDs

---

# Prolog Building Block: `<permissions>`

```xml
<prolog>
  <permissions view="internal" transfer="restricted" duplicate="yes"/>
</prolog>
```

**`@view` attribute values:**

| Value | Meaning | Use Case |
|---|---|---|
| `internal` | All employees | Standard operator manuals |
| `classified` | Security clearance required | Defense-contract machines |
| `entitled` | Customer entitlement | Paid premium documentation |
| `public` | Anyone | Marketing datasheets |

> **M5 Rule:** Default manufacturing topics = `view="internal"`. Never use `view="public"` for safety-critical content.

---

# Permissions — Manufacturing Security Levels

```xml
<!-- Standard operator manual -->
<permissions view="internal"/>

<!-- Proprietary spindle alignment (trade secret) -->
<permissions view="internal" transfer="restricted"/>

<!-- Defense contract machine (ITAR/EAR) -->
<permissions view="classified" transfer="restricted"/>
```

**Processing implications:**

| Permission | HTML Output | PDF Output | Mobile App |
|---|---|---|---|
| `internal` | Behind VPN login | Watermarked "Internal Use Only" | Behind auth |
| `classified` | Encrypted, access-controlled | Printed on controlled printers only | Not available |
| `entitled` | Customer portal login | Custom-branded per customer | Customer app only |

---

# Prolog Building Block: `<resourceid>`

```xml
<prolog>
  <resourceid id="DR5K-SAF-001" appid="cms-dr5k"/>
</resourceid>
</prolog>
```

**Purpose:** A stable identifier for content management systems.

| Attribute | Meaning | Example |
|---|---|---|
| `@id` | Unique ID in the CMS namespace | `DR5K-SAF-001` |
| `@appid` | CMS or repository identifier | `cms-dr5k` |

**Naming convention for `@id`:**
```
<PRODUCT>-<DOMAIN>-<SEQ>
DR5K-SAF-001   → MC-5000, Safety domain, topic 001
DR8K-MNT-042   → MC-8000, Maintenance domain, topic 042
COM-GLO-015    → Common (cross-product), Glossary, topic 015
```

> **M6 Rule:** `<resourceid>` is optional but strongly recommended for all topics that may be ingested into a CCMS.

---

<!-- _class: divider -->

# Part 5: Metadata — Audience, Category, Prodinfo

---

# `<metadata>` — The Container

```xml
<prolog>
  <metadata>
    <!-- audience, category, keywords, prodinfo, othermeta -->
  </metadata>
</prolog>
```

The `<metadata>` element aggregates all classification and filtering metadata. It is the largest and most important section of the prolog.

> Every template includes this block. The Subject Scheme governs the allowed values (see the Professional course for Subject Scheme details).

---

# `<audience>` — Who Should Read This

```xml
<metadata>
  <audience type="operator" job="machine-operator" experiencelevel="2"/>
</metadata>
```

**Subject Scheme controlled values:**

| `@type` Value | Who | Typical Content |
|---|---|---|
| `operator` | Machine operator on factory floor | Daily operation, basic troubleshooting |
| `maintenance` | Maintenance technician | Repair, calibration, PM schedules |
| `engineer` | Manufacturing engineer | Parameter optimization, integration |
| `field-service` | Field service engineer | On-site installation, advanced repair |
| `trainer` | Internal/external trainer | Training materials, certification |
| `all` | All roles | Safety warnings, glossary terms |

> **M7 Rule:** Every topic MUST have an `<audience>` element. "all" is allowed but must be justified.

---

# Audience — Manufacturing Examples

```xml
<!-- Concept: How the interlock system works — for everyone -->
<audience type="all"/>

<!-- Task: Daily spindle warm-up — operator -->
<audience type="operator" job="machine-operator" experiencelevel="1"/>

<!-- Task: Spindle taper alignment — maintenance only -->
<audience type="maintenance" job="service-technician" experiencelevel="3"/>

<!-- Reference: G-code macro reference — engineer -->
<audience type="engineer" job="manufacturing-engineer" experiencelevel="3"/>
```

**Level definitions:**
- Level 1: Novice (can follow basic procedures)
- Level 2: Experienced (understands the machine)
- Level 3: Expert (can diagnose and adapt)

> Audience filtering in the output: The HTML help system shows only topics matching the user's role.

---

# Audience Targeting in Output Channels

| Audience | HTML5 Portal | PDF Manual | Mobile HMI | AR Glasses |
|---|---|---|---|---|
| `operator` | Operator Dashboard | Operator's Guide | Full access | Step-by-step overlays |
| `maintenance` | Maintenance Portal | Service Manual | Diagnostic mode | Repair procedures |
| `engineer` | Engineering Portal | Technical Reference | Parameter mode | Data overlays |
| `trainer` | LMS Integration | Instructor Guide | Demo mode | Not shown |

> One source, filtered output. DITA conditional processing uses `@audience` to include/exclude content.

---

# `<category>` — Content Classification

```xml
<metadata>
  <category>Maintenance Procedures</category>
  <category>Spindle System</category>
  <category>MC-5000 VMC</category>
</metadata>
```

**Category naming convention:**
```
<MAJOR SYSTEM> / <SUBSYSTEM> / <COMPONENT>

Examples:
  Spindle System / Taper Alignment
  Coolant System / Filtration
  Safety Systems / Interlocks
  Control System / G-Code Macros
  Axis Drive / Servo Tuning
```

> Categories are free-text (not Subject Scheme governed), but follow the naming convention. Use with care — inconsistent spellings break faceted search.

---

# Category Taxonomy — Full Manufacturing Map

```
Machine Systems Categories:
├── Safety Systems
│   ├── Interlocks
│   ├── Emergency Stop
│   └── Light Curtains
├── Spindle System
│   ├── Taper
│   ├── Drawbar
│   └── Cooling
├── Axis Drive
│   ├── X-Axis
│   ├── Y-Axis
│   ├── Z-Axis
│   └── Rotary Axes
├── Coolant System
│   ├── Filtration
│   ├── Pumps
│   └── Nozzles
├── Control System
│   ├── CNC (Siemens 840D)
│   ├── HMI
│   └── G-Code Macros
└── Tool Changer
    ├── ATC Mechanism
    └── Magazine
```

> Assign 1-3 categories per topic. More than 3 = the topic is probably trying to do too much.

---

# `<prodinfo>` — Product Information

```xml
<metadata>
  <prodinfo>
    <prodname>MC-5000 Vertical Machining Center</prodname>
    <vrmlist>
      <vrm version="v2.1"/>
      <vrm version="v2.0"/>
    </vrmlist>
    <component>Spindle Assembly</component>
    <brand>PrecisionLine</brand>
    <series>DR-Series</series>
  </prodinfo>
</metadata>
```

| Element | Meaning | Example |
|---|---|---|
| `<prodname>` | Full product name | `MC-5000 Vertical Machining Center` |
| `<vrmlist>` | Applicable versions | `v2.0, v2.1` |
| `<component>` | Subsystem/assembly | `Spindle Assembly` |
| `<brand>` | Brand family | `PrecisionLine` |
| `<series>` | Series identifier | `DR-Series` |

---

# Prodinfo — Multi-Product Topic Pattern

**Scenario:** A coolant filter replacement procedure is identical for MC-5000 and MC-8000.

```xml
<prodinfo>
  <prodname>MC-5000 Vertical Machining Center</prodname>
  <prodname>MC-8000 Vertical Machining Center</prodname>
  <vrmlist>
    <vrm version="v2.0"/>
    <vrm version="v2.1"/>
  </vrmlist>
  <component>Coolant Filtration System</component>
  <brand>PrecisionLine</brand>
  <series>DR-Series</series>
</prodinfo>
```

> Multiple `<prodname>` elements = this topic applies to all listed products. The CI/CD pipeline generates cross-product topic usage reports from this data.

---

# `<othermeta>` — Custom Name/Value Pairs

```xml
<metadata>
  <othermeta name="content-type" content="task"/>
  <othermeta name="document-status" content="review"/>
  <othermeta name="owner-department" content="Spindle Engineering"/>
  <othermeta name="translatable" content="yes"/>
  <othermeta name="hazard-severity" content="caution"/>
</othermeta>
```

**Framework-required `<othermeta>` entries:**

| `@name` | Allowed Values (Subject Scheme) | Mandatory? |
|---|---|---|
| `content-type` | concept, task, reference, troubleshooting, glossary-entry, hazard-statement, learning-object | Yes |
| `document-status` | draft, review, approved, published, archived | Yes |
| `owner-department` | Free text (department name) | Yes |
| `translatable` | yes, no | Yes |
| `hazard-severity` | danger, warning, caution, notice | If hazard content |

---

# `<othermeta>` — All Framework Fields

```xml
<othermeta name="content-type" content="troubleshooting"/>
<othermeta name="document-status" content="review"/>
<othermeta name="owner-department" content="Spindle Engineering"/>
<othermeta name="owner-email" content="spindle-eng@mfg-enterprise.com"/>
<othermeta name="translatable" content="yes"/>
<othermeta name="source-language" content="en"/>
<othermeta name="review-cycle" content="annual"/>
<othermeta name="compliance-standard" content="ISO 12100"/>
<othermeta name="alarm-code" content="700016"/>
<othermeta name="hazard-severity" content="warning"/>
<othermeta name="product-line" content="DR-Series"/>
<othermeta name="git-branch" content="feature/alarm-700016-troubleshoot"/>
```

> All `content` values for governed fields (`content-type`, `document-status`, `hazard-severity`, `product-line`) **must** pass Subject Scheme validation.

---

# Metadata in Glossary Entries (Special Case)

```xml
<glossentry id="gloss-lockout-tagout">
  <glossterm>Lockout/Tagout (LOTO)</glossterm>
  <glossdef>...</glossdef>
  <prolog>
    <author type="creator">Safety Documentation Team</author>
    <copyright><copyryear year="2026"/><copyrholder>...</copyrholder></copyright>
    <metadata>
      <audience type="all"/>
      <category>Safety Systems</category>
      <othermeta name="content-type" content="glossary-entry"/>
      <othermeta name="translatable" content="yes"/>
      <othermeta name="owner-department" content="Safety Engineering"/>
    </metadata>
  </prolog>
</glossentry>
```

> Glossary entries differ: the prolog is inside `<glossentry>`, not before a body. But the metadata fields are the same.

---

<!-- _class: divider -->

# Part 6: Metadata Templates and Patterns

---

# Template: Concept Topic Metadata

```xml
<prolog>
  <author type="creator">Maria Schmidt</author>
  <author type="reviewer">Dr. Anna Wagner</author>
  <source>Engineering Design Spec DS-2026-0142</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2026-06-01"/>
    <revised modified="2026-06-07"/>
  </critdates>
  <permissions view="internal"/>
  <resourceid id="DR5K-CON-014" appid="cms-dr5k"/>
  <metadata>
    <audience type="operator" job="machine-operator" experiencelevel="2"/>
    <category>Safety Systems</category>
    <category>Interlocks</category>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="concept"/>
    <othermeta name="document-status" content="draft"/>
    <othermeta name="owner-department" content="Safety Engineering"/>
    <othermeta name="translatable" content="yes"/>
  </metadata>
</prolog>
```

---

# Template: Task Topic Metadata

```xml
<prolog>
  <author type="creator">John Chen</author>
  <author type="contributor">Jens Mueller, Spindle SME</author>
  <source>Field Service Procedure FSP-2025-089</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2025"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2025-11-15"/>
    <revised modified="2026-06-03"/>
  </critdates>
  <permissions view="internal"/>
  <metadata>
    <audience type="maintenance" job="service-technician" experiencelevel="3"/>
    <category>Spindle System</category>
    <category>Taper Alignment</category>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="task"/>
    <othermeta name="document-status" content="approved"/>
    <othermeta name="owner-department" content="Spindle Engineering"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="hazard-severity" content="warning"/>
  </metadata>
</prolog>
```

---

# Template: Reference Topic Metadata

```xml
<prolog>
  <author type="creator">Engineering Data Team</author>
  <author type="contributor">Siemens CNC Support</author>
  <source>Siemens SINUMERIK 840D sl Manual, Chapter 9</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2026-04-20"/>
    <revised modified="2026-06-07"/>
  </critdates>
  <permissions view="internal"/>
  <metadata>
    <audience type="engineer" job="manufacturing-engineer" experiencelevel="3"/>
    <category>Control System</category>
    <category>G-Code Macros</category>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="reference"/>
    <othermeta name="document-status" content="draft"/>
    <othermeta name="owner-department" content="Controls Engineering"/>
    <othermeta name="translatable" content="no"/>
  </metadata>
</prolog>
```

> Reference data like G-code tables is often NOT translatable — numeric codes, parameters, and commands stay in English.

---

# Metadata for Troubleshooting Topics

```xml
<prolog>
  <author type="creator">Maria Schmidt</author>
  <author type="contributor">Field Service Team</author>
  <source>Alarm Database ALDB-2026 v3.1</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2026-05-10"/>
    <revised modified="2026-06-07"/>
  </critdates>
  <permissions view="internal"/>
  <metadata>
    <audience type="maintenance" job="service-technician" experiencelevel="2"/>
    <category>Coolant System</category>
    <category>Pumps</category>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="troubleshooting"/>
    <othermeta name="document-status" content="review"/>
    <othermeta name="owner-department" content="After-Sales Service"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="alarm-code" content="700016"/>
  </metadata>
</prolog>
```

---

# Metadata for Hazard Statements

```xml
<prolog>
  <author type="creator">Safety Documentation Team</author>
  <author type="contributor">Safety Compliance Officer</author>
  <source>ISO 12100:2010 Risk Assessment RA-2026-0032</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2026-02-14"/>
    <revised modified="2026-06-01"/>
  </critdates>
  <permissions view="internal"/>
  <metadata>
    <audience type="all"/>
    <category>Safety Systems</category>
    <category>Hazard Warnings</category>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="hazard-statement"/>
    <othermeta name="document-status" content="approved"/>
    <othermeta name="owner-department" content="Safety Engineering"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="hazard-severity" content="danger"/>
    <othermeta name="compliance-standard" content="ISO 12100"/>
  </metadata>
</prolog>
```

> Hazard severity must match the `<hazardstatement>` `@type` attribute. Validation rule: `othermeta[hazard-severity]` == `hazardstatement/@type`.

---

<!-- _class: divider -->

# Part 7: M1-M7 Validation Rules

---

# M1: Author Requirement

**Rule:** Every topic must have at least one `<author type="creator">`.

```
Validation (XPath):
  exists(/prolog/author[@type='creator'])

CI/CD check:
  ❌ Missing <author type="creator"> → Build FAILS with error
  ✅ At least one creator author present → Pass
```

**Correct:**
```xml
<author type="creator">Maria Schmidt</author>
```

**Incorrect (fails M1):**
```xml
<!-- No author at all -->
<!-- Author with wrong type -->
<author type="reviewer">Dr. Anna Wagner</author>
```

---

# M2: Source Requirement for Regulated Content

**Rule:** If the topic derives from a regulated standard or engineering spec, `<source>` is mandatory.

```
Validation (conditional):
  IF othermeta[@name='compliance-standard'] exists
  THEN exists(/prolog/source)

CI/CD check:
  ❌ compliance-standard set but no <source> → Build FAILS
  ✅ Source present when compliance-standard is set → Pass
```

```xml
<!-- Passes M2 -->
<othermeta name="compliance-standard" content="ISO 12100"/>
<source>ISO 12100:2010 Risk Assessment RA-2026-0032</source>

<!-- Fails M2 -->
<othermeta name="compliance-standard" content="ISO 12100"/>
<!-- Missing <source>! -->
```

---

# M3: Copyright Year Currency

**Rule:** The most recent `<copyryear>` must not be more than 2 years behind the current year unless the topic is explicitly marked as reviewed-and-still-current.

```
Validation:
  max(//copyryear/@year) >= (current_year - 2)

  2026: max copyryear >= 2024  ✅
  2026: max copyryear = 2023   ❌ (stale)

Exception:
  If <revised> is within 2 years AND <othermeta name="review-cycle" content="biennial">,
  the check allows older copyryear if a review note exists.
```

> Stale copyright dates are a red flag for ISO auditors. The CI/CD pipeline flags them.

---

# M4: Critical Dates Requirement

**Rule:** `<created>` must be present. `<revised>` must be updated on every substantive change.

```
Validation:
  exists(/prolog/critdates/created/@date)
  exists(/prolog/critdates/revised/@modified)

CI/CD check:
  ❌ Missing <created> or <revised> → Build FAILS
  ✅ Both present → Pass
```

**Sub-rule M4a (date format):**
```
  //critdates//@date matches '\d{4}-\d{2}-\d{2}'
  Invalid formats: "2026/06/07", "06/07/2026", "June 7, 2026"
```

---

# M5: Permissions — No Public Safety Content

**Rule:** Topics with `hazard-severity` set to `danger` or `warning` must not have `view="public"`.

```
Validation:
  IF othermeta[@name='hazard-severity'] = ('danger','warning')
  THEN /prolog/permissions/@view != 'public'

CI/CD check:
  ❌ Danger topic with view="public" → Build FAILS (security block)
  ✅ Danger topic with view="internal" → Pass
```

> This is a **hard block**. Safety warnings with "severe injury or death" consequences must never be publicly accessible without authentication.

---

# M6: Resource ID Naming Convention

**Rule:** If `<resourceid>` is present, the `@id` must follow the convention `<PRODUCT>-<DOMAIN>-<SEQ>`.

```
Validation (regex):
  resourceid/@id matches '^(DR5K|DR8K|DR2K|COM|GEN)-[A-Z]{3}-\d{3}$'

Examples:
  DR5K-SAF-001 ✅  DR8K-MNT-042 ✅  COM-GLO-015 ✅
  dr5k-saf-001 ❌  (lowercase)
  MC-5000-SAF-001 ❌ (product code not abbreviated)
  SAF-001 ❌ (missing product prefix)
```

> This rule is a **warning**, not an error. Topics can build without `<resourceid>`, but CCMS ingestion requires it.

---

# M7: Audience Requirement

**Rule:** Every topic must have an `<audience>` element. Using `type="all"` requires justification in a review comment.

```
Validation:
  exists(/prolog/metadata/audience)

CI/CD check:
  ❌ Missing <audience> → Build FAILS
  ⚠️ audience type="all" → Warning (requires PR justification)
  ✅ Specific audience → Pass
```

**Justification template for `all`:**
```markdown
Audience = "all" justified because:
- This glossary term is used across all roles
- This safety warning applies to anyone near the machine
```

---

# M1-M7 Validation Summary Table

| Rule | Check | Severity | Auto-Fix? |
|---|---|---|---|
| M1 | Author type="creator" exists | Error | No |
| M2 | Source exists if compliance-standard set | Error | No |
| M3 | Copyright year within 2 years | Warning | CI/CD can update |
| M4 | Created and revised dates present | Error | CI/CD can update revised |
| M5 | No public access for danger/warning | Error | No (security) |
| M6 | Resource ID naming convention | Warning | No |
| M7 | Audience element present | Error | No |

> Errors = build fails. Warnings = build succeeds with notice. All errors must be resolved before merge.

---

<!-- _class: divider -->

# Part 8: Metadata in DITA Maps

---

# Metadata in Topicref (Map-Level Metadata)

DITA maps can also carry metadata — at the topicref level or in `<topicmeta>`:

```xml
<map>
  <topicref href="topics/DR5K-SAF-001.dita">
    <topicmeta>
      <audience type="operator"/>
      <category>Safety Systems</category>
      <othermeta name="document-status" content="approved"/>
    </topicmeta>
  </topicref>
</map>
```

**When to use map-level metadata:**
- Override topic-level metadata for a specific publication
- Add publication-specific `document-status`
- Filter content without modifying the source topic

> Map-level metadata **overrides** topic-level metadata. Use with care.

---

# Common Map Metadata Pattern

```xml
<map>
  <title>MC-5000 Operator Manual</title>
  <topicmeta>
    <author type="creator">Documentation Team</author>
    <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
    </copyright>
    <critdates>
      <created date="2026-06-01"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <othermeta name="document-status" content="approved"/>
  </topicmeta>
  <!-- topicrefs... -->
</map>
```

> The map's `<topicmeta>` is the "cover page" metadata for the publication.

---

<!-- _class: divider -->

# Part 9: Metadata Workflow and Governance

---

# Metadata Lifecycle

```
┌──────────────┐
│  1. CREATE   │  Author fills prolog from template (T4)
│  (Author)    │  Status: "draft"
└──────┬───────┘
       ↓
┌──────────────┐
│  2. REVIEW   │  Peer/SME reviews metadata accuracy
│  (Reviewer)  │  Status: "review"
└──────┬───────┘
       ↓
┌──────────────┐
│  3. APPROVE  │  Content owner signs off
│  (Approver)  │  Status: "approved"
└──────┬───────┘
       ↓
┌──────────────┐
│  4. PUBLISH  │  CI/CD validates, builds, deploys
│  (CI/CD)     │  Status: "published"
└──────┬───────┘
       ↓
┌──────────────┐
│  5. ARCHIVE  │  Topic superseded or product EOL
│  (Editor)    │  Status: "archived"
└──────────────┘
```

---

# Metadata Governance: Subject Scheme Connection

The Subject Scheme (covered in detail in the Professional course) enforces controlled values:

```
Without Subject Scheme:
  author types "operator" → "operAtor" ❌
  author types "maintenance" → "maint" ❌
  author types "review" → "under review" ❌

With Subject Scheme:
  oXygen shows dropdown: [operator, maintenance, engineer, ...]
  Invalid values are impossible to enter
  CI/CD catches any remaining errors
```

> For now, use the values listed in the template. The Subject Scheme just automates what you'll do manually.

---

# Metadata Quality Scorecard

Rate your metadata against this scorecard before submitting for review:

| Check | Points |
|---|---|
| All M1-M7 rules pass | 40 |
| Author includes creator + reviewer | 10 |
| Categories match taxonomy (1-3 max) | 15 |
| Prodinfo complete (name + VRM + component) | 15 |
| othermeta includes all framework-required fields | 10 |
| Resource ID follows naming convention | 5 |
| Copyright year is current | 5 |
| **Total** | **100** |

> Target: 90+ points for approval-ready metadata. Below 70 = return to author.

---

# Common Metadata Mistakes — And Fixes

| Mistake | Fix |
|---|---|
| "operators" (plural) | "operator" (Subject Scheme value) |
| `type=` on `<audience>` is descriptive text | Use the controlled values from the scheme |
| Missing `<revised>` date | Run the CI/CD metadata update script |
| Category = "stuff" or "other" | Use the defined taxonomy |
| `<source>` = "n/a" | Either provide a real source or remove the element |
| Copyright year = 2020 | Update to current year |
| No `<audience>` for a glossary term | Set `type="all"` |
| `hazard-severity` = "severe" | Use controlled: danger, warning, caution, notice |

---

<!-- _class: divider -->

# Part 10: Metadata for Compliance and Audit

---

# ISO 9001: Document Control Requirements

ISO 9001:2015, Clause 7.5.3 requires:

| Requirement | DITA Metadata Field |
|---|---|
| Document identification | `<resourceid>`, file name, `@id` |
| Format (electronic, paper) | Output channel (not in prolog) |
| Review and approval evidence | `<critdates>`, `<author type="reviewer">`, `document-status` |
| Revision status | `<revised modified="...">` |
| Distribution controls | `<permissions>` |

> A DITA topic with complete metadata satisfies ISO 9001 document control requirements **automatically**. No separate document register needed.

---

# ISO 12100: Safety Information Traceability

ISO 12100 requires that safety information be traceable to risk assessments:

```xml
<!-- This satisfies the traceability requirement -->
<source>ISO 12100:2010 Risk Assessment RA-2026-0032</source>
<othermeta name="compliance-standard" content="ISO 12100"/>
<othermeta name="risk-assessment-id" content="RA-2026-0032"/>
```

**Auditor's query (XPath):**
```xpath
//topic[othermeta[@name='compliance-standard' and @content='ISO 12100']]
```

> One XPath query extracts every safety-tagged topic in the repository. Auditors love this.

---

# Audit Trail: What Metadata Proves

| Auditor Asks | Metadata Answers |
|---|---|
| "Who approved this safety procedure?" | `<author type="reviewer">` |
| "When was this last reviewed?" | `<revised modified="...">` |
| "Is this the current version?" | `document-status = "approved"` or `"published"` |
| "Who can access this document?" | `<permissions view="...">` |
| "What product does this apply to?" | `<prodinfo>` |
| "What regulation requires this?" | `<source>`, `compliance-standard` |
| "Has this topic been reviewed recently?" | `<revised>` within last 12 months |

---

<!-- _class: divider -->

# Part 11: Exercise — Add Metadata to a Sample Topic

---

<!-- _class: exercise -->

# Exercise: Metadata Workshop (45 min)

You are given a bare concept topic about the MC-5000 safety interlock system. Add complete metadata to its prolog.

**Scenario:**
- Topic: "How the Safety Interlock System Works" (concept)
- Product: MC-5000 VMC, version v2.1
- Author: You (as creator); reviewer: Safety SME "Dr. Anna Wagner"
- Source: Engineering Design Spec DS-2026-0142
- Audience: Operators (level 2), Maintenance (level 3)
- Categories: Safety Systems, Interlocks
- Created: today's date
- Status: draft
- Translatable: yes
- Department: Safety Engineering

---

# Exercise: Starter Topic (No Metadata)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">
<concept id="concept-safety-interlock">
  <title>How the Safety Interlock System Works</title>
  <shortdesc>The MC-5000 safety interlock system prevents access to hazardous
  areas during machine operation.</shortdesc>
  <conbody>
    <p>The safety interlock system uses redundant sensors on all access doors.
    When any door is opened during automatic operation, the machine enters a
    controlled stop within 150 ms.</p>
    <section>
      <title>Interlock Components</title>
      <ul>
        <li>Door position sensors (magnetic, dual-channel)</li>
        <li>Safety relay module (SRM-2400)</li>
        <li>Guard locking mechanism (solenoid-actuated)</li>
        <li>Override key switch (maintenance mode only)</li>
      </ul>
    </section>
  </conbody>
</concept>
```

> Your task: Add a complete `<prolog>` with all required metadata fields between `<shortdesc>` and `<conbody>`.

---

# Exercise: Prolog Template to Complete

```xml
  <prolog>
    <!-- ADD: author (creator = you, reviewer = Dr. Anna Wagner) -->

    <!-- ADD: source (Engineering Design Spec) -->

    <!-- ADD: publisher -->

    <!-- ADD: copyright (current year, company) -->

    <!-- ADD: critdates (created today, revised today) -->

    <!-- ADD: permissions (internal) -->

    <!-- ADD: metadata block -->
    <!--   - audience: operator + maintenance  -->
    <!--   - categories: Safety Systems, Interlocks  -->
    <!--   - prodinfo: MC-5000 VMC v2.1  -->
    <!--   - othermeta: content-type=concept, status=draft,  -->
    <!--                translatable=yes, department=Safety Engineering  -->
  </prolog>
```

> Fill in each comment with the correct XML. Refer to the template slides in Part 6.

---

<!-- _class: exercise -->

# Exercise: Solution (Part 1 — Prolog Start)

```xml
  <prolog>
    <author type="creator">[Your Name]</author>
    <author type="reviewer">Dr. Anna Wagner</author>
    <source>Engineering Design Spec DS-2026-0142</source>
    <publisher>
      <name>Manufacturing Enterprise GmbH</name>
    </publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
    </copyright>
    <critdates>
      <created date="2026-06-07"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <permissions view="internal"/>
    <resourceid id="DR5K-SAF-001" appid="cms-dr5k"/>
```

> Continue with the `<metadata>` block on the next slide.

---

# Exercise: Solution (Part 2 — Metadata Block)

```xml
    <metadata>
      <audience type="operator" job="machine-operator" experiencelevel="2"/>
      <audience type="maintenance" job="service-technician" experiencelevel="3"/>
      <category>Safety Systems</category>
      <category>Interlocks</category>
      <prodinfo>
        <prodname>MC-5000 Vertical Machining Center</prodname>
        <vrmlist>
          <vrm version="v2.1"/>
        </vrmlist>
        <component>Safety Interlock System</component>
        <brand>PrecisionLine</brand>
        <series>DR-Series</series>
      </prodinfo>
      <othermeta name="content-type" content="concept"/>
      <othermeta name="document-status" content="draft"/>
      <othermeta name="owner-department" content="Safety Engineering"/>
      <othermeta name="translatable" content="yes"/>
      <othermeta name="source-language" content="en"/>
    </metadata>
  </prolog>
```

> Your complete topic should now pass all M1-M7 validation rules.

---

# Exercise: Self-Check Against M1-M7

| Rule | Your Topic | Pass? |
|---|---|---|
| M1 | Author type="creator" exists | Check your `<author>` |
| M2 | Source exists (no compliance-standard, so N/A) | N/A |
| M3 | Copyright year 2026 (current) | Check `<copyryear>` |
| M4 | Created and revised dates present | Check `<critdates>` |
| M5 | No public access + no danger/warning | `view="internal"` + no hazard-severity |
| M6 | Resource ID follows convention | `DR5K-SAF-001` matches pattern |
| M7 | Audience element present | Two `<audience>` elements |

> All checks passed? Congratulations — you have an approval-ready topic!

---

# Extended Exercise: Troubleshooting Metadata (15 min)

Now add metadata to a troubleshooting topic for Alarm 700016.

**Bonus requirements:**
- Content-type: `troubleshooting`
- Audience: maintenance (level 2)
- Add `<othermeta name="alarm-code" content="700016"/>`
- Add `<othermeta name="translatable" content="yes"/>`
- Source: Alarm Database ALDB-2026 v3.1
- Category: Coolant System, Pumps

> Challenge: Can you also add the `<resourceid>` following the naming convention?

---

# Extended Exercise: Solution

```xml
<prolog>
  <author type="creator">[Your Name]</author>
  <author type="reviewer">Field Service Lead</author>
  <source>Alarm Database ALDB-2026 v3.1</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2026-06-07"/>
    <revised modified="2026-06-07"/>
  </critdates>
  <permissions view="internal"/>
  <resourceid id="DR5K-CLN-016" appid="cms-dr5k"/>
  <metadata>
    <audience type="maintenance" job="service-technician" experiencelevel="2"/>
    <category>Coolant System</category>
    <category>Pumps</category>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
      <component>Coolant Pump Assembly</component>
    </prodinfo>
    <othermeta name="content-type" content="troubleshooting"/>
    <othermeta name="document-status" content="draft"/>
    <othermeta name="owner-department" content="After-Sales Service"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="source-language" content="en"/>
    <othermeta name="alarm-code" content="700016"/>
  </metadata>
</prolog>
```

---

<!-- _class: tip -->

# Pro Tip: Copy-Paste Your Prolog Template

Once you write one correct prolog, copy it as your starting point for the next topic:

```xml
<!-- ===== PROLOG TEMPLATE START ===== -->
<prolog>
  <author type="creator">[YOUR NAME]</author>
  <author type="reviewer">[REVIEWER NAME]</author>
  <source>[SOURCE DOCUMENT]</source>
  <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
  <copyright>
    <copyryear year="2026"/>
    <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
  </copyright>
  <critdates>
    <created date="2026-06-07"/>
    <revised modified="2026-06-07"/>
  </critdates>
  <permissions view="internal"/>
  <metadata>
    <audience type="[AUDIENCE]" job="[JOB]" experiencelevel="[LEVEL]"/>
    <category>[CATEGORY 1]</category>
    <category>[CATEGORY 2]</category>
    <prodinfo>
      <prodname>[PRODUCT]</prodname>
      <vrmlist><vrm version="[VERSION]"/></vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="[TYPE]"/>
    <othermeta name="document-status" content="draft"/>
    <othermeta name="owner-department" content="[DEPARTMENT]"/>
    <othermeta name="translatable" content="[yes/no]"/>
  </metadata>
</prolog>
<!-- ===== PROLOG TEMPLATE END ===== -->
```

---

<!-- _class: divider -->

# Part 12: Tools and Automation

---

# oXygen: Metadata Editing

oXygen XML Editor supports structured metadata editing:

1. Open the topic in Author mode
2. Navigate to the prolog section
3. Use the DITA Maps Manager to view topic metadata
4. Metadata fields appear as editable form fields (if Subject Scheme is configured)

**Shortcut:** In oXygen, Ctrl+Shift+M opens the metadata panel.

```
oXygen Menu:
  DITA → Edit Properties → [metadata form opens]
```

> When Subject Scheme is configured, metadata fields become dropdown lists — no more typos.

---

# CI/CD Validation Script

Our GitHub Actions workflow validates metadata on every push:

```yaml
# .github/workflows/validate-metadata.yml
name: Validate DITA Metadata
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Validate M1-M7 Rules
        run: |
          python scripts/validate_metadata.py --rules M1-M7 --dir topics/
      - name: Check Subject Scheme Compliance
        run: |
          python scripts/validate_subject_scheme.py --scheme schemes/ --dir topics/
```

> Every commit triggers validation. Errors prevent merging.

---

# Metadata Query Examples (for Reporting)

Once metadata is in place, querying becomes trivial:

```xpath
<!-- All topics owned by Safety Engineering -->
//topic[othermeta[@name='owner-department' and @content='Safety Engineering']]

<!-- All troubleshooting topics last revised before 2025 -->
//*[contains(@class,'troubleshooting/troubleshooting')]
   [prolog/critdates/revised/@modified < '2025-01-01']

<!-- Topics applicable to MC-5000 AND MC-8000 -->
//topic[prodinfo/prodname[contains(.,'MC-5000')]
       and prodinfo/prodname[contains(.,'MC-8000')]]

<!-- All translatable topics in the Safety Systems category -->
//topic[othermeta[@name='translatable' and @content='yes']]
       [metadata/category='Safety Systems']
```

> These queries power dashboards, reports, and automated audits.

---

# Metadata Dashboard (Concept)

A weekly automated report based on metadata:

```
Weekly DITA Content Health Report
=================================
Total topics: 2,847
Draft: 124 (4.4%)
Review: 89 (3.1%)
Approved: 1,842 (64.7%)
Published: 792 (27.8%)

Stale topics (not revised in 12+ months): 47 ⚠️
Missing audience: 3 ❌
Copyright beyond 2-year window: 12 ⚠️
"Danger" topics with public access: 0 ✅

Top departments by topic count:
  1. Spindle Engineering: 423
  2. Controls Engineering: 387
  3. Safety Engineering: 312
```

> All generated from DITA metadata via XPath queries. No manual counting.

---

<!-- _class: divider -->

# Part 13: Special Metadata Scenarios

---

# Scenario: Multilingual Metadata

```xml
<prolog>
  <author type="creator">Maria Schmidt</author>
  <metadata>
    <othermeta name="source-language" content="en"/>
    <othermeta name="target-language" content="de"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="translation-status" content="in-progress"/>
    <othermeta name="translation-vendor" content="LinguaTech GmbH"/>
    <othermeta name="translation-due" content="2026-07-15"/>
  </metadata>
</prolog>
```

**Translation metadata fields:**

| Field | Meaning |
|---|---|
| `source-language` | Original language (always `en` for our framework) |
| `target-language` | Target locale for this copy |
| `translation-status` | not-started, in-progress, translated, reviewed, final |
| `translation-vendor` | LSP handling this language |
| `translation-due` | Deadline date |

> Covered in depth in Professional Module 05: Localization.

---

# Scenario: Conditional Content Metadata

Use metadata to control what content appears in which output:

```xml
<concept id="concept-spindle" otherprops="print-only">
  <!-- This topic only appears in PDF output -->
</concept>

<concept id="concept-spindle-video" otherprops="web-only">
  <!-- This topic replaces the above in HTML output (includes video) -->
</concept>
```

Ditaval filtering:
```xml
<prop att="otherprops" val="print-only" action="include"/>
<prop att="otherprops" val="web-only" action="exclude"/>
```

> `@otherprops` is not in the prolog — it is on the root element. But metadata in `<othermeta>` should document the filtering intent.

---

<!-- _class: note -->

# Important: The Prolog Travels With Reused Content

When you `conref` a topic or element, the **referencing topic's** prolog applies, not the source's prolog.

```xml
<!-- Source topic: DR5K-SAF-001 (audience: all) -->
<!-- Referencing topic: -->
<topicref href="topics/DR5K-SAF-001.dita">
  <topicmeta>
    <audience type="operator"/>   <!-- OVERRIDES source prolog -->
  </topicmeta>
</topicref>
```

> This is why map-level metadata is powerful — you can reuse the same topic in five publications, each filtered to a different audience.

---

<!-- _class: divider -->

# Part 14: Knowledge Check

---

# Knowledge Check — Part A (Multiple Choice)

1. Which element container holds all DITA metadata?
   - a) `<body>`  b) `<prolog>`  c) `<header>`  d) `<meta>`

2. What date format does the framework require?
   - a) MM/DD/YYYY  b) DD.MM.YYYY  c) YYYY-MM-DD  d) Month DD, YYYY

3. Which audience type is allowed for a topic that applies to everyone?
   - a) `everyone`  b) `all`  c) `public`  d) `general`

4. What M rule checks that `hazard-severity=danger` topics are not public?
   - a) M1  b) M3  c) M5  d) M7

---

# Knowledge Check — Part A Answers

1. **b) `<prolog>`** — The prolog element contains all metadata (author, copyright, critdates, permissions, metadata).

2. **c) YYYY-MM-DD** — ISO 8601 format. Required by M4a sub-rule. Example: `2026-06-07`.

3. **b) `all`** — The Subject Scheme defines `all` as the value for cross-role topics (glossary, general safety). Must be justified in PR.

4. **c) M5** — The permission security rule. Danger/warning topics must never have `view="public"`.

---

# Knowledge Check — Part B (Fill In)

Complete the prolog for a task topic:
- ___(1)___ type="creator">Maria Schmidt</___>
- ___(2)___>DS-2026-0142</___>
- ___(3)___ view="internal"/>
- ___(4)___ name="content-type" content="task"/>
- ___(5)___ name="document-status" content="draft"/>

---

# Knowledge Check — Part B Answers

1. `<author type="creator">Maria Schmidt</author>`
2. `<source>DS-2026-0142</source>`
3. `<permissions view="internal"/>`
4. `<othermeta name="content-type" content="task"/>`
5. `<othermeta name="document-status" content="draft"/>`

> Need 5/5 in Part A and 5/5 in Part B to demonstrate metadata competency.

---

# Knowledge Check — Part C (Spot the Errors)

Find the errors in this prolog:
```xml
<prolog>
  <author>Maria Schmidt</author>
  <author type="reviwer">Dr. Anna Wagner</author>
  <source></source>
  <copyright>
    <copyryear year="2020"/>
    <copyrholder>Manufacturing Enterprise</copyrholder>
  </copyright>
  <critdates>
    <created date="06/07/2026"/>
  </critdates>
  <metadata>
    <audience type="operators"/>
    <othermeta name="content-type" content="Concept"/>
    <othermeta name="document-status" content="under review"/>
  </metadata>
</prolog>
```

---

# Spot the Errors — Answers

1. `<author>Maria Schmidt</author>` — missing `type="creator"`
2. `type="reviwer"` — typo, should be `"reviewer"`
3. `<source></source>` — empty element; provide real source or remove
4. `year="2020"` — copyright year stale (2026); missing updated `<copyryear>`
5. `date="06/07/2026"` — wrong format; should be `"2026-06-07"`
6. Missing `<revised>` date (M4 rule)
7. `type="operators"` — plural; should be `"operator"`
8. `content="Concept"` — wrong case; should be `"concept"`
9. `content="under review"` — not in Subject Scheme; should be `"review"`
10. Missing `<permissions>` element

> **10 errors found.** Each one would be caught by our CI/CD validation or Subject Scheme checks.

---

<!-- _class: divider -->

# Part 15: Summary and Next Steps

---

# Module 05 Summary

**Key takeaways:**

1. The `<prolog>` is the metadata passport for every DITA topic
2. M1-M7 validation rules enforce metadata quality automatically
3. `author`, `copyright`, `critdates`, `audience`, and `othermeta` are the minimum required fields
4. Subject Scheme governs the allowed values (learned more in the Professional course)
5. `document-status` tracks every topic through the content lifecycle: draft -> review -> approved -> published -> archived
6. Complete metadata = automated ISO 9001 and ISO 12100 compliance
7. CI/CD validates metadata on every commit — errors block the build

---

# Before Module 06

**Preparation checklist:**

- [ ] Open `06-TEMPLATES/04-METADATA-TEMPLATE.xml` in oXygen and study it
- [ ] Compare the template to the exercise solution from this module
- [ ] Memorize the M1-M7 rules (they will be on the certification exam)
- [ ] Practice writing a prolog from memory (no copy-paste)
- [ ] Review `02-ARCHITECTURE/02-METADATA-SCHEMA.md` in the framework docs

---

# Prolog Quick Reference Card

```
╔════════════════════════════════════════════════╗
║             DITA PROLOG — QUICK REF           ║
╠════════════════════════════════════════════════╣
║ <prolog>                                      ║
║   <author type="creator|contributor|..."/>    ║
║   <source/>       ← Regulated source doc      ║
║   <publisher><name/></publisher>              ║
║   <copyright>                                 ║
║     <copyryear year="YYYY"/>                  ║
║     <copyrholder/>                            ║
║   </copyright>                                ║
║   <critdates>                                 ║
║     <created date="YYYY-MM-DD"/>              ║
║     <revised modified="YYYY-MM-DD"/>          ║
║   </critdates>                                ║
║   <permissions view="internal|classified|.."/>║
║   <resourceid id="XXX-XXX-NNN"/>              ║
║   <metadata>                                  ║
║     <audience type="" job="" explevel=""/>    ║
║     <category/>  ← Max 3                      ║
║     <prodinfo>                                ║
║       <prodname/>                             ║
║       <vrmlist><vrm version=""/></vrmlist>    ║
║       <component/>                            ║
║     </prodinfo>                               ║
║     <othermeta name="" content=""/>           ║
║   </metadata>                                 ║
║ </prolog>                                     ║
╚════════════════════════════════════════════════╝
```

---

<!-- _class: divider -->

# Module 05 Complete

**Next Module: 06 — DITA Map Basics**

DITA Manufacturing Framework v1.1
Manufacturing Enterprise — Documentation Governance Committee
