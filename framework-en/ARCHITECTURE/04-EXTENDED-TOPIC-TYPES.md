# Extended Topic Types — Beyond Concept / Task / Reference

**Document ID:** ARCH-004  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved  

---

## 1. Purpose

DITA 1.3 defines over 25 topic types. While Concept / Task / Reference cover the majority of content needs, manufacturing enterprises require specialized types for safety, troubleshooting, terminology, training, and structured requirements.

This document catalogs **all supplementary types** adopted for this framework, their use cases, and implementation guidance.

---

## 2. Quick Reference

| # | Type | DOCTYPE | When to Use | Priority |
|---|------|---------|-------------|----------|
| 1 | **Troubleshooting** | `troubleshooting` | Diagnose and resolve problems — the #1 gap in v1.0 | P0 |
| 2 | **Glossary Entry** | `glossentry` | Define terms in a reusable, translatable way | P0 |
| 3 | **Hazard Statement** | `hazard` (domain) | Structured safety warnings for ISO compliance | P1 |
| 4 | **Subject Scheme** | `subjectScheme` | Controlled-value map for metadata governance | P1 |
| 5 | **Bookmap** | `bookmap` | Formal publication structure (chapters, front/back matter) | P2 |
| 6 | **Task Requirements** | `taskreq` | Structured prerequisites (skills, tools, conditions) | P2 |
| 7 | **Learning Object** | `learningObject` | E-learning content package | P3 |
| 8 | **Learning Overview** | `learningOverview` | Learning module introduction | P3 |
| 9 | **Learning Content** | `learningContent` | Core instructional body | P3 |
| 10 | **Learning Summary** | `learningSummary` | Module recap and assessment | P3 |
| 11 | **Glossary Group** | `glossgroup` | Group of related glossary entries | P1 |
| 12 | **API Reference** | `reference` (with API domain) | Programming interface documentation | P3 |
| 13 | **Task** (extended) | `task` (with manufacturing domain) | Enriched procedures with parts, tools, fluids | P1 |

---

## 3. Topic Type Decision Tree

Use this flowchart to select the correct type:

```
What kind of content is this?
│
├─ Understanding / Explanation?
│   └─► Concept
│
├─ Step-by-step procedure?
│   └─► Task (with Task Requirements if complex prerequisites)
│
├─ Look-up data (specs, parameters)?
│   └─► Reference
│
├─ Problem diagnosis?
│   └─► Troubleshooting
│
├─ Safety warning?
│   └─► Hazard Statement (Machine Industry Domain)
│
├─ Term definition?
│   └─► Glossary Entry
│
├─ Training / e-learning?
│   └─► Learning Object → Overview → Content → Summary
│
└─ Publication structure?
    └─► Ditamap (web) or Bookmap (print/PDF)
```

---

## 4. Type-by-Type Specification

### 4.1 Troubleshooting (`troubleshooting`)

**Purpose:** Diagnose problems by symptom. Each topic covers one alarm code or observable symptom and lists all possible causes with corresponding remedies.

**Covers the gap:** The framework's v1.0 had no structured way to write repair guides. Writers used Task topics for repairs, but a Task assumes a single fixed path — a troubleshooting topic captures the reality that one symptom can have multiple causes.

**Key structure:**

```xml
<troubleshooting id="alarm-700016">
  <title>Coolant Pressure Low (Alarm 700016)</title>
  <troublebody>
    <condition>
      <p>Alarm 700016 displayed. Coolant pressure below 0.3 MPa.</p>
    </condition>
    <cause>
      <p>Return filter clogged (80% of cases)</p>
      <remedy>Clean or replace coolant return filter.</remedy>
    </cause>
    <cause>
      <p>Pump impeller damaged (15% of cases)</p>
      <remedy>
        <cmd>Replace coolant pump impeller.</cmd>
        <info>See Pump Replacement Task topic for steps.</info>
      </remedy>
    </cause>
  </troublebody>
</troubleshooting>
```

**Template:** `06-TEMPLATES/06-TROUBLESHOOTING-TEMPLATE.dita`

---

### 4.2 Glossary Entry (`glossentry`)

**Purpose:** Define a term as an independent, reusable, translatable topic. Essential for multi-language environments (9 languages in this framework).

**Covers the gap:** v1.0 used a Markdown glossary (`02-GLOSSARY.md`) — a plain table that cannot be translated via TMS or reused via conref. Per-term DITA topics solve both.

**Key structure:**

```xml
<glossentry id="gloss-lockout-tagout">
  <glossterm>Lockout/Tagout (LOTO)</glossterm>
  <glossdef>
    <p>Safety procedure that isolates energy sources and locks
       the disconnect device before maintenance.</p>
  </glossdef>
  <glossAlternative>
    <glossSurfaceForm>LOTO</glossSurfaceForm>
    <glossAcronym>LOTO</glossAcronym>
  </glossAlternative>
</glossentry>
```

**Template:** `06-TEMPLATES/07-GLOSSARY-ENTRY-TEMPLATE.dita`

**Glossary Group** (`glossgroup`) collects related entries under one topic — e.g., all safety terms in one group.

---

### 4.3 Hazard Statement (Machine Industry Domain)

**Purpose:** Write structured, machine-readable safety warnings that can be extracted and audited for ISO 12100 / GB/T 15706 compliance.

**Covers the gap:** v1.0 used prose warnings inside Concept topics. These are invisible to compliance audits and inconsistent across products. The Machine Industry Domain provides dedicated hazard elements.

**Key structure:**

```xml
<p hazards="">
  <hazardstatement type="caution">
    <messagepanel>
      <typeofhazard>Flying chips</typeofhazard>
      <consequence>Severe eye injury</consequence>
      <howtoavoid>Wear safety glasses with side shields</howtoavoid>
    </messagepanel>
    <hazardsymbol href="safety-goggles.svg"/>
  </hazardstatement>
</p>
```

**Template:** `06-TEMPLATES/08-HAZARD-STATEMENT-TEMPLATE.dita`

---

### 4.4 Subject Scheme (`subjectScheme`)

**Purpose:** Define controlled values for metadata fields so authors choose from a list instead of typing free text. Enables validation, filtering, and consistent reporting.

**Covers the gap:** v1.0 metadata had lists like `audience="admin|user|developer|executive"` but no enforcement. Subject Scheme makes these values machine-checkable.

**Key structure:**

```xml
<subjectScheme>
  <schemehandler>
    <topicref href="http://docs.oasis-open.org/dita/v1.3/...
                 base-type/scheme.shell.ditamap"/>
  </schemehandler>
  <hasInstance>
    <subjectdef keys="audience">
      <subjectdef keys="operator"/>
      <subjectdef keys="maintenance"/>
      <subjectdef keys="engineer"/>
    </subjectdef>
  </hasInstance>
  <enumerationdef>
    <attributedef name="audience"/>
    <subjectdef keyref="audience"/>
  </enumerationdef>
</subjectScheme>
```

**Repository:** `02-ARCHITECTURE/05-SUBJECT-SCHEME-GUIDE.md`  
**Template:** `06-TEMPLATES/12-SUBJECT-SCHEME-TEMPLATE.ditamap`

---

### 4.5 Bookmap (`bookmap`)

**Purpose:** Structure content for print/PDF publication with formal front matter (TOC, lists), chapters, appendices, and index.

**Covers the gap:** v1.0 only had Ditamap, which is adequate for web navigation but lacks book-specific features: part numbers, edition info, chapter numbering, dedicated backmatter, index generation.

**Key structure:**

```xml
<bookmap id="dr5000-user-guide">
  <booktitle>
    <mainbooktitle>MC-5000 User Guide</mainbooktitle>
    <booktitlealt>Version 2.1.A</booktitlealt>
  </booktitle>
  <frontmatter>
    <booklists>
      <toc/>
      <figurelist/>
      <tablelist/>
    </booklists>
  </frontmatter>
  <chapter href="safety-overview.dita"/>
  <chapter href="operations.dita"/>
  <backmatter>
    <appendix href="specifications.dita"/>
    <amendments/>
  </backmatter>
</bookmap>
```

**Template:** `06-TEMPLATES/11-BOOKMAP-TEMPLATE.ditamap`

---

### 4.6 Task Requirements (`taskreq`)

**Purpose:** Define structured prerequisites for a task: required skill level, certifications, tools, parts, environmental conditions, and safety precautions.

**Covers the gap:** v1.0 Task topics had only a plain `<prereq>` paragraph. Complex maintenance tasks (e.g., spindle alignment) need machine-readable requirements that can be checked against technician qualifications and inventory.

**Key structure:**

```xml
<taskrequirements id="spindle-alignment-reqs">
  <reqcond>
    <personnel>
      <skill level="3">Level 3 Technician</skill>
      <certification>Spindle Alignment Certified</certification>
    </personnel>
    <equipment>
      <item>Laser alignment kit (HZ-5000-KIT)</item>
      <item>Torque wrench (5-50 Nm, calibrated)</item>
    </equipment>
    <parts>
      <item>O-ring for taper seal (P/N: OR-1024)</item>
    </parts>
    <environment>
      <temp min="10" max="40">10-40°C ambient</temp>
      <cleanliness class="ISO 8">Clean room optional</cleanliness>
    </environment>
    <safety>
      <hazardref href="gloss-lockout-tagout.dita">LOTO required</hazardref>
    </safety>
  </reqcond>
</taskrequirements>
```

**Template:** `06-TEMPLATES/09-TASK-REQUIREMENTS-TEMPLATE.dita`

---

### 4.7 Learning & Training Types

**Purpose:** Create structured e-learning content that can be rendered as self-paced HTML courses, SCORM packages for LMS, or printed instructor guides.

**Four related types:**
- `learningObject` — Container: groups overview + content + summary
- `learningOverview` — Module intro: objectives, prerequisites, duration
- `learningContent` — Core instructional body (can include Concept/Task/Reference content)
- `learningSummary` — Recap, knowledge check, next steps

**Template:** `06-TEMPLATES/10-LEARNING-OBJECT-TEMPLATE.dita`

---

## 5. Migration Path from v1.0

| v1.0 Practice | v1.1 Target | Effort |
|---------------|-------------|--------|
| Prose safety warnings in Concept topics | Dedicated Hazard Statement topics | Medium |
| Markdown glossary table | Per-term DITA Glossary Entry topics | High (one-time conversion) |
| Repair procedures as Task topics | Troubleshooting + Task Requirements | Medium |
| Ditamap-only publishing | Bookmap for PDF output | Low |
| Free-text metadata values | Subject Scheme controlled values | Medium |

---

## 6. Implementation Order

**Phase 1 (P0, immediate):**
- Troubleshooting templates + examples
- Glossary Entry templates + conversion of existing glossary

**Phase 2 (P1, next quarter):**
- Hazard Statement integration into safety content
- Subject Scheme for all metadata fields
- Manufacturing domain task enrichment (tools, parts, fluids)

**Phase 3 (P2-P3, ongoing):**
- Bookmap for formal publications
- Task Requirements for complex procedures
- Learning Object types for training content

---

**Document ID:** ARCH-004  
**Next Review Date:** Q4 2026
