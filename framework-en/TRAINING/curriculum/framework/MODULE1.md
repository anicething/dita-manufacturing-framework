---
marp: true
theme: uncover
class:
  - lead
  - invert
paginate: true
header: "DITA Manufacturing Framework — Training Program"
footer: "DITA Governance Framework v1.1 — June 2026"
---

<!-- _class: lead invert -->
# DITA Manufacturing Framework
## Training Module 1: Foundation &amp; Governance

**Target Audience:** Technical Writers, Editors, Content Owners
**Duration:** 4 hours

Manufacturing Enterprise — Documentation Governance Committee

---

## What Is DITA?

**Darwin Information Typing Architecture**

DITA is an XML-based standard for creating, managing, and publishing technical documentation.

Key principles:
- **Topic-oriented** — content is modular, not book-length
- **Reuse by design** — write once, use everywhere
- **Content-typing** — 25+ specialized types available
- **Specialization** — extend the standard for your domain
- **Multi-channel output** — same source → HTML5, PDF, mobile, LMS

> Our framework v1.1 adopts **13 topic types** across three tiers.

---

## Why DITA for Manufacturing?

| Challenge | DITA Solution |
|-----------|---------------|
| Multi-product docs share common content | **Content reuse** (35–40% target) |
| Multi-language (9 languages) | **Topic-level localization** via xml:lang |
| ISO compliance (27001/9001/14001) | **Metadata + Hazard Statement** for audit trails |
| 50+ person documentation team | **Modular authoring** enables parallel work |
| Complex troubleshooting needed | **Dedicated Troubleshooting type** |
| Safety warnings must be auditable | **Machine Industry Domain hazard elements** |
| Terms need translation across 9 languages | **Glossary Entry type** per term |

---

## Framework at a Glance — 8 Modules

```
dita-manufacturing-framework/
├── 01-GOVERNANCE/     Charter, ownership, change authority
├── 02-ARCHITECTURE/   Content strategy, metadata, topic design
│   └── v1.1 adds:    Extended types, Subject Scheme, Hazard guide
├── 03-STYLE-GUIDE/    Writing rules, glossary, quality checks
├── 04-WORKFLOWS/      Approval process, git workflow, reviews
├── 05-TOOLS/          oXygen, DITA-OT, TMS, GitHub setup
├── 06-TEMPLATES/      Ready-to-use DITA XML templates
│   └── v1.1 adds:    7 new templates (T6–T12)
├── 07-TRAINING/       Onboarding program, exercises, cert
└── 08-CI-CD/          GitHub Actions, publishing pipeline
```

---

## Module 01: Governance Charter

**File:** `01-GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md`

This is the foundational document. It defines:

- **Vision & Scope** — why we adopted DITA
- **Governance Committee Structure** — who decides what
- **Decision Rights** — what requires committee approval
- **Compliance Framework** — ISO 27001 / 9001 / 14001
- **Review Cadence** — monthly DGC meetings, quarterly audits

> **Your role:** Read and understand this document. All framework decisions flow from it.

---

## Content Ownership Matrix

**File:** `01-GOVERNANCE/02-CONTENT-OWNERSHIP-MATRIX.md`

Every piece of content has a named owner:

| Role | Responsibility |
|------|---------------|
| **Content Owner** | Accountable for accuracy and timeliness |
| **Technical Writer** | Authors and maintains the topic(s) |
| **Subject Matter Expert (SME)** | Reviews technical accuracy |
| **Editor** | Ensures style guide compliance |
| **Approver** | Signs off for publication |

> **Rule:** No topic is published without a named owner.

---

## Change Authority Matrix

| Type | Example | Who Approves | SLA |
|------|---------|-------------|-----|
| **Type 1: Minor** | Typo, formatting | **Author** (self-service) | < 1 day |
| **Type 2: Moderate** | Value update (torque spec) | **Tech Lead** | < 3 days |
| **Type 3: Major** | New chapter, restructure | **Full governance review** | < 5 days |
| **Type 4: Critical** | Safety, compliance | **Director + Compliance** | 48 hrs (mandatory wait) |

> Adding new topic types (e.g., Hazard Statements for a product line) = **Type 3** change.

---

## Content Architecture — From 3 to 13 Types

v1.0 had only 3 types. v1.1 expands to **13 types in 3 tiers**:

```
Tier 1 — Core (80% of content)
  Concept | Task | Reference

Tier 2 — Specialized (15%)
  Troubleshooting  ← NEW: Diagnose problems by symptom
  Glossary Entry   ← NEW: Reusable, translatable terms
  Hazard Statement ← NEW: ISO-compliant safety warnings
  Task Requirements← NEW: Structured prerequisites
  Subject Scheme   ← NEW: Metadata value governance

Tier 3 — Advanced (5%)
  Bookmap          ← NEW: Print/PDF publication structure
  Learning Object  ← NEW: E-learning modules
```

> See `02-ARCHITECTURE/04-EXTENDED-TOPIC-TYPES.md` for the full catalog.

---

## Decision Tree — Pick the Right Type

```
What kind of content is this?
│
├─ Understanding / Explanation?    → Concept
├─ Step-by-step procedure?
│   └─ Complex prerequisites?      → Task + Task Requirements
├─ Look-up data / specifications?  → Reference
├─ Problem diagnosis?              → Troubleshooting
├─ Safety warning?                 → Hazard Statement
├─ Term definition?                → Glossary Entry
├─ E-learning module?              → Learning Object
├─ Print/PDF publication?          → Bookmap
└─ Metadata governance?            → Subject Scheme
```

> Print this decision tree. Post it at your desk.

---

## Metadata Schema

**File:** `02-ARCHITECTURE/02-METADATA-SCHEMA.md`

Every topic requires these fields in its `<prolog>`:

| Field | Example | Purpose |
|-------|---------|---------|
| `audience` | operator, maintenance, engineer | Filter content by role |
| `product` | MC-5000 VMC | Associate with product line |
| `content-type` | troubleshooting, glossary-entry | Topic classification |
| `document-status` | draft, review, approved | Workflow stage |
| `owner-department` | Safety Engineering | Accountability |

> **v1.1:** Values are now governed by **Subject Scheme** — authors select from dropdowns, no more free-text typos.

---

## Subject Scheme — Metadata Governance

**File:** `02-ARCHITECTURE/05-SUBJECT-SCHEME-GUIDE.md`

Subject Scheme enforces **controlled vocabularies**:

```
Without Subject Scheme:        With Subject Scheme:
  audience="Operator"            audience="operator" ✅
  audience="OPERATOR"            audience="operator" ✅
  audience="operators"           ✗ validation error
```

**What it gives us:**
- Dropdown lists in oXygen — pick, don't type
- Automated validation in CI/CD — wrong values block the build
- Consistent reporting — no manual data cleanup

> Template: `06-TEMPLATES/12-SUBJECT-SCHEME-TEMPLATE.ditamap`

---

## Spotlight: Troubleshooting Topic

**What it solves:** Repair manuals were written as Task topics, but a repair isn't a single path — one symptom can have many causes.

```
Troubleshooting topic structure:
├── Condition: "Alarm 700016 — coolant pressure < 0.3 MPa"
├── Cause 1 (80%): Clogged filter
│   └── Remedy: Clean or replace filter element
├── Cause 2 (15%): Pump impeller damage
│   └── Remedy: Replace pump assembly
└── Cause 3 (5%): Low coolant level
    └── Remedy: Top up coolant
```

> **Template:** `06-TEMPLATES/06-TROUBLESHOOTING-TEMPLATE.dita`
> **Example:** `examples/topics/en/new-types/ALARM-700016-TROUBLESHOOTING.dita`

---

## Spotlight: Glossary Entry

**What it solves:** v1.0 had a Markdown glossary table — not translatable, not reusable. Each term is now an independent DITA topic.

```
Glossary entry structure:
├── Term: "Lockout/Tagout (LOTO)"
├── Definition: "Safety procedure that isolates energy sources..."
├── Alternative: Acronym → LOTO
└── Related: Interlock, Zero Energy State
```

**Why this matters for 9-language translation:**
- TMS recognizes each term as a translatable unit
- Changes to one term don't affect others
- Terms can be `conref`-ed into any topic

> **Template:** `06-TEMPLATES/07-GLOSSARY-ENTRY-TEMPLATE.dita`
> **Example:** `examples/topics/en/new-types/GLOSS-LOTO.dita`

---

## Spotlight: Hazard Statement (Machine Industry Domain)

**What it solves:** Prose safety warnings in Concept topics are invisible to compliance audits. Structured hazard elements are machine-readable and extractable for ISO 12100 audits.

```
Hazard statement structure:
├── Type: Caution / Warning / Danger / Notice
├── Hazard type: "Flying chips"
├── Consequence: "Severe eye injury"
├── How to avoid: "Wear safety glasses with side shields"
├── Risk assessment: Severity × Probability table
└── Standards reference: ISO 12100, ISO 13849-1
```

> **Template:** `06-TEMPLATES/08-HAZARD-STATEMENT-TEMPLATE.dita`
> ISO auditors can extract ALL hazard statements from the doc set programmatically.

---

## Spotlight: Subject Scheme

**What it solves:** Metadata quality. Without it, `audience` might be written 5 different ways across 1000 topics.

```
What Subject Scheme controls:
┌──────────────────────────────────────────┐
│  Attribute        Controlled Values      │
├──────────────────────────────────────────┤
│  audience          operator, maintenance,│
│                    engineer, field-service│
│                    trainer, all           │
├──────────────────────────────────────────┤
│  content-type      concept, task, ref,   │
│                    troubleshooting, gloss,│
│                    hazard, learning       │
├──────────────────────────────────────────┤
│  document-status   draft, review,        │
│                    approved, published,   │
│                    archived              │
├──────────────────────────────────────────┤
│  product           MC-5000, MC-8000,    │
│                    DR-2000, common       │
├──────────────────────────────────────────┤
│  hazard-severity   danger, warning,      │
│                    caution, notice       │
└──────────────────────────────────────────┘
```

---

## Exercise 1: Classifying Content (15 min)

Categorize each piece of content into the **correct DITA topic type**:

| Content | Type? |
|---------|-------|
| Instructions for changing the coolant filter | ? |
| Explanation of how the safety interlock system works | ? |
| Alarm 700016 — coolant pressure low, possible causes | ? |
| Define "Lockout/Tagout" as an official term | ? |
| Table of spindle speeds and torque values | ? |
| Steps to calibrate the touch probe | ? |
| Warning about flying chips during machining | ? |
| List of error codes and their meanings | ? |

> **Hint:** Some of these fit the new v1.1 types introduced today.

---

## Exercise 1 — Answers

| Content | Type | Why |
|---------|------|-----|
| Instructions for changing coolant filter | **Task** | Step-by-step procedure |
| Explanation of safety interlock system | **Concept** | Conceptual understanding |
| Alarm 700016 — troubleshooting | **Troubleshooting** | Symptom → multiple causes |
| Define "Lockout/Tagout" | **Glossary Entry** | Official term definition |
| Table of spindle speeds and torque | **Reference** | Lookup data |
| Calibrate the touch probe | **Task** | Procedural |
| Warning about flying chips | **Hazard Statement** | Structured safety warning |
| List of error codes | **Reference** | Quick reference data |

> **8/8?** You've mastered the full type spectrum. Well done!

---

## Style Guide & Quality

**Files in 03-STYLE-GUIDE/:**
| Document | Purpose |
|----------|---------|
| `01-STYLE-GUIDE.md` | Voice, tone, terminology, formatting rules |
| `02-GLOSSARY.md` | Approved terms (being migrated to DITA Glossary Entry) |
| `03-QUALITY-CHECKLIST.md` | Pre-publication verification (15+ checks) |

**New quality checks for extended types (v1.1):**
- Troubleshooting: every `cause` has at least one `remedy`?
- Glossary: `glossdef` present and translatable?
- Hazard: `hazardstatement` type matches severity metadata?
- Subject Scheme: all metadata values pass validation?

---

## Module 1 — Knowledge Check

1. How many topic types does our v1.1 framework adopt? Name the Tier 2 types.
2. What makes **Troubleshooting** different from writing a repair as a Task?
3. Why are **Glossary Entries** separate DITA topics instead of a Markdown table?
4. What's the purpose of **Subject Scheme**?
5. What are the four severity levels for **Hazard Statements**?

> Write your answers. We'll review in 10 minutes.

---

<!-- _class: lead invert -->
## Answers

1. **13 types.** Tier 2: Troubleshooting, Glossary Entry, Hazard Statement, Task Requirements, Subject Scheme
2. Troubleshooting captures **multiple causes per symptom** with remediation for each. A Task assumes one fixed path.
3. Each term must be independently **translatable** (9 languages), **reusable** via conref, and **findable** by the TMS.
4. **Enforce controlled vocabularies** for metadata — dropdown in oXygen, validation in CI/CD, consistent reporting.
5. **Danger** (death), **Warning** (serious injury), **Caution** (minor injury), **Notice** (property damage)

---

## Module 1 — Summary

**v1.0 → v1.1 evolution:**
- From 3 topic types to **13 types** across 3 tiers
- From free-text metadata to **Subject Scheme** governance
- From prose warnings to **structured Hazard Statements**
- From Markdown glossary to **DITA Glossary Entry** topics

**Before Module 2:**
- [ ] Read `04-EXTENDED-TOPIC-TYPES.md` — full type catalog
- [ ] Review `05-SUBJECT-SCHEME-GUIDE.md` — metadata governance
- [ ] Open `06-TROUBLESHOOTING-TEMPLATE.dita` in oXygen

---

<!-- _class: lead invert -->
# Module 1 Complete

**Next session: Module 2 — Authoring Extended Types & Publishing**

DITA Manufacturing Framework v1.1
Manufacturing Enterprise — Documentation Governance Committee
