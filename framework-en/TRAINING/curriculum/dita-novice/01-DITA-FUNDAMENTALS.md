---
marp: true
theme: dita-training
class: invert
paginate: true
---

# DITA Fundamentals
## Module 01: Introduction to Darwin Information Typing Architecture

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Define DITA and explain its core principles
- Describe the history and evolution of the standard
- Identify key benefits of DITA for manufacturing documentation
- Contrast DITA with traditional authoring approaches
- Understand the DITA content lifecycle

---

# What is DITA?

**Darwin Information Typing Architecture**

- An **XML-based standard** for authoring, managing, and publishing technical content
- Named after **Charles Darwin** — the principle of "specialization" from a common base
- An **OASIS open standard** (not proprietary or vendor-locked)
- Designed specifically for **technical documentation**

> DITA is not a tool — it is a standard. Many tools implement DITA, but DITA itself is an open specification.

---

# Core Principle: Topic-Based Authoring

Traditional documentation is **book-based**:

> "Chapter 1, Chapter 2, Appendix A..."

DITA documentation is **topic-based**:

> "What is this?" + "How do I do this?" + "What are the specifics?"

| Book-Based | Topic-Based |
|---|---|
| Linear, sequential reading | Standalone, self-contained units |
| Content mixed with formatting | Content separated from presentation |
| Hard to reuse across documents | Each topic = one reusable chunk |
| Single output format | Multi-channel from single source |

---

# History of DITA

| Year | Milestone |
|---|---|
| 1999 | IBM internal project "Information Architecture Workgroup" |
| 2001 | IBM donates DITA to the public domain |
| 2004 | DITA 1.0 — first release as OASIS standard |
| 2005 | DITA becomes OASIS standard |
| 2007 | DITA 1.1 — bookmap, indexing, glossary |
| 2010 | DITA 1.2 — keyref, conkeyref, constraint modules |
| 2015 | DITA 1.3 — scoped keys, branch filtering, troubleshooting |
| 2024+ | DITA 2.0 — emerging with simplification and modernization |

---

# DITA Adoption

DITA is the standard for technical documentation in industries that demand:

- **Precision** — aerospace, defense, medical devices
- **Compliance** — ISO 9001, ISO 20607, S1000D
- **Localization** — 40+ language manufacturing operations
- **Scale** — hundreds of thousands of topics

> Major adopters: IBM, Boeing, Nokia, Ericsson, Oracle, SAP, and thousands of manufacturing companies worldwide.

---

# DITA Benefits for Manufacturing

| Benefit | Impact |
|---|---|
| **Content Reuse** | 35-40% reduction in authoring effort |
| **Single-Source Publishing** | Write once → PDF, HTML, mobile, embedded help |
| **Multi-Channel Output** | Paper, tablet, HMI, augmented reality, chatbot |
| **ISO Compliance** | ISO 9001, ISO 20607 documentation requirements |
| **Localization Efficiency** | Reuse translated topics; ~40% cost reduction |
| **Consistency** | Enforced structure = uniform documentation |
| **Audit Trail** | XML-aware version control for every topic |

---

# DITA vs Traditional Documentation

| Aspect | Traditional (Word/FrameMaker) | DITA |
|---|---|---|
| **Content Structure** | Freeform / ad-hoc | Strictly typed (Concept/Task/Reference) |
| **Reuse** | Copy-paste | Conref, keyref, topicref |
| **Formatting** | Embedded in content | Separated (applied at publish time) |
| **Version Control** | Binary files or manual tracking | Text-based, full git/diff support |
| **Publishing** | One format at a time | Multi-channel from single source |
| **Collaboration** | Serial (one author per file) | Parallel (many authors, many topics) |
| **Translation** | Re-translate everything | Translate changes only; reuse rest |
| **Search** | Full-text only | Metadata-driven, faceted search |

---

# The DITA Content Lifecycle

```
  AUTHOR ──→ MANAGE ──→ PUBLISH ──→ DISTRIBUTE
    │           │           │             │
    │           │           │             │
  Create     Store in    Transform     Deliver to
  topics     CCMS/Git    to outputs    end users
    │           │           │             │
    ▼           ▼           ▼             ▼
  XML in      Version     PDF, HTML,    Tablet,
  oXygen      Control     Web Help      Print, HMI
```

---

# Lifecycle Stage 1: Author

**Create structured content using XML**

- Write individual topics (Concept, Task, Reference)
- Use templates for consistency
- Add metadata for search and filtering
- Validate against DITA DTDs/Schemas
- Store in version control (git) or CCMS

```
author ──> topic-concept-001.dita
            topic-task-001.dita
            topic-reference-001.dita
            product-map.ditamap
```

---

# Lifecycle Stage 2: Manage

**Store, version, and control content**

- git repositories with branching strategy
- CCMS (Component Content Management System) for enterprise
- Metadata-driven search and reporting
- Link management (keeping cross-references valid)
- Translation management integration

> Common CCMS options: Ixiasoft, Tridion Docs, Astoria, easyDITA, Heretto

---

# Lifecycle Stage 3: Publish

**Transform XML into deliverable formats**

- DITA Open Toolkit (DITA-OT): open-source publishing engine
- Commercial options: oXygen Publishing Engine, Antenna House
- Output formats:
  - **PDF** — print and offline reading
  - **HTML5** — responsive web help
  - **Eclipse Help** — embedded in applications
  - **JSON/XML** — feed to other systems

---

# Lifecycle Stage 4: Distribute

**Deliver content to end users**

| Channel | Use Case |
|---|---|
| **Printed Manual** | Shop floor, regulatory submission |
| **Online Help** | Intranet, customer portal |
| **Tablet/HMI** | Machine-mounted display |
| **QR Code** | Quick access at point of use |
| **Portal/Knowledge Base** | Centralized support hub |
| **API/Data Feed** | Integration with MES, PLM, ERP |

---

# Key Concepts: Building Blocks

| Concept | Definition |
|---|---|
| **Topic** | A standalone, self-contained unit of information |
| **Map** | Organizes topics into a deliverable structure |
| **Metadata** | Data about topics — enables filtering, search, reporting |
| **Conref** | Content reference — reuse content by referencing, not copying |
| **Specialization** | Create new topic types from existing ones |

---

# Topics: The Atomic Unit

Each topic answers **one question** for **one audience**:

```
Concept topic:  "What is the Coolant Circulation System?"
Task topic:     "How do I replace the coolant filter?"
Reference topic: "Coolant specifications and pressure ratings"
```

> Rule of thumb: If you need a table of contents for a single topic, it should probably be multiple topics.

---

# Maps: The Organization Layer

A DITA map is the **table of contents** that assembles topics into a publication:

```xml
<map>
  <title>Coolant System Manual</title>
  <topicref href="concepts/coolant-overview.dita"/>
  <topicref href="tasks/replace-filter.dita"/>
  <topicref href="reference/coolant-specs.dita"/>
</map>
```

Maps enable:
- Organizing topics hierarchically
- Creating different publications from the same topics
- Controlling navigation, search, and output behavior

---

# Manufacturing Use Cases

| Documentation Type | DITA Approach |
|---|---|
| **Machine Manuals** | Map of Concept + Task + Reference topics |
| **Safety Documentation** | Specialized hazard statements, conref'd warnings |
| **Training Materials** | Task-focused topics; learning maps |
| **Service Bulletins** | Short, standalone topic with metadata for distribution |
| **Parts Catalogs** | Reference topics with structured tables |
| **Field Service Guides** | Task topics with augmented-reality outputs |
| **Compliance Submissions** | Bookmap with regulatory-required structure |

---

# Case Study: Single-Source in Action

**Scenario**: A machine is sold in three configurations (Basic, Pro, Enterprise)

**Traditional approach**:
- 3 separate manuals = 3x to write, 3x to maintain, 3x to translate

**DITA approach**:
- ~200 shared topics (same across all models)
- ~30 model-specific topics (filtered by configuration)
- 3 ditamaps = one per configuration
- Total: 230 topics instead of ~690 pages created 3 times

**Result**: ~67% reduction in content volume, faster updates, consistent terminology.

---

# The DITA Ecosystem

```
┌─────────────────────────────────────────────────────┐
│                    AUTHORING TOOLS                   │
│  oXygen XML Editor | XMetaL | FrameMaker (DITA mode)│
├─────────────────────────────────────────────────────┤
│                   PROCESSING ENGINE                  │
│         DITA-OT (Open Toolkit) | Commercial PE       │
├─────────────────────────────────────────────────────┤
│                 MANAGEMENT SYSTEMS                   │
│      CCMS | git | SharePoint | Network shares        │
├─────────────────────────────────────────────────────┤
│              TRANSLATION MANAGEMENT                  │
│     SDL Trados | MemoQ | XTM | Across                │
└─────────────────────────────────────────────────────┘
```

---

# Current Standard: DITA 1.3

Key features you will use daily:

- **Scoped keys** — context-dependent linking
- **Branch filtering** — conditional content by product/audience
- **Troubleshooting topic** — structured problem-solving
- **Lightweight DITA (LwDITA)** — simplified authoring for contributors

> DITA 2.0 is emerging with a focus on simplification: fewer element types, easier specialization, and better multimedia support. This course uses DITA 1.3 as the stable baseline.

---

# <!-- _class: exercise -->

# Exercise: DITA Awareness Check

1. **Discussion**: How does your organization currently manage documentation? What are the biggest pain points?

2. **Identify**: List 3 document types in your work that could benefit from topic-based authoring.

3. **Reflect**: Consider a recent documentation update. How would a single-source approach have changed the process?

---

# Module Summary

DITA Fundamentals: What we covered

- DITA is an XML-based OASIS standard for topic-based authoring
- It originated at IBM and has evolved through multiple versions
- Core benefits: reuse, single-source publishing, multi-channel output
- Content lifecycle: Author → Manage → Publish → Distribute
- Key concepts: topics, maps, metadata, conref, specialization
- Separates content from formatting for maximum flexibility

---

# Key Takeaways

1. **DITA is about structure, not appearance** — you define what content *is*, not what it *looks like*

2. **Topic-based authoring** means each file answers one question for one audience

3. **Single-source publishing** enables PDF, HTML, and mobile from the same XML

4. **Content reuse** (not copy-paste) is the primary efficiency driver

5. **DITA is an open standard** — your content is never locked into a vendor's format

> Next Module: The Three Core Topic Types — Concept, Task, and Reference
