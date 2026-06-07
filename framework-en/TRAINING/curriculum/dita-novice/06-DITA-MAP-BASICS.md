---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# DITA Map Basics
## Module 06: Organizing Topics into Publications

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Define a DITA map and explain its role in the content lifecycle
- Describe the relationship between maps and topics
- Read and write DITA map XML structure
- Use topicref elements and their key attributes
- Distinguish between maps and bookmaps
- Create a simple DITA map from a template
- Apply best practices for map organization
- Build a complete navigation map for MC-5000 documentation

---

<!-- _class: divider -->

# Part 1: What Is a DITA Map?

---

# What Is a DITA Map?

A DITA map is the **organizational layer** that assembles individual topics into a publication.

```
Topics (standalone)           Map (organizer)           Publication (output)

+------------------+      +----------------------+      +----------------------+
| concept-         | ---> | &lt;topicref          |      |                      |
| overview         |      |   href="concept-     |      |  MC-5000             |
|                  |      |   overview.dita"     |      |  Operator Manual     |
+------------------+      | /&gt;                 |      |                      |
                           |                      |      |  Table of            |
+------------------+      | &lt;topicref          | ---> |  Contents            |
| task-            | ---> |   href="task-        |      |                      |
| startup          |      |   startup.dita"      |      |  Chapter 1: ...      |
|                  |      | /&gt;                 |      |  Chapter 2: ...      |
+------------------+      |                      |      |                      |
                           | &lt;topicref          |      |                      |
+------------------+      |   href="ref-         |      |                      |
| reference-       | ---> |   specs.dita"        |      |                      |
| specs            |      | /&gt;                 |      |                      |
|                  |      +----------------------+      +----------------------+
+------------------+
```

> A map is to topics what a playlist is to songs -- it defines the order, structure, and what gets included.

---

# Why Maps Matter in Manufacturing

| Purpose | Value |
|---|---|
| **Publication Structure** | Organizes hundreds of topics into logical chapters |
| **Multiple Publications** | Same topics, different maps = different manuals |
| **Navigation Control** | Defines what users see in the table of contents |
| **Output Filtering** | Include or exclude topics by product variant |
| **Relationship Definitions** | Links topics hierarchically and by related-links |
| **Metadata Inheritance** | Map-level metadata can override topic metadata |
| **Translation Scope** | Defines exactly what gets sent for localization |

> One topic can appear in five different maps for five different audiences -- without duplication.

---

# Map Analogy: The Table of Contents

Think of a DITA map as a **smart, programmable table of contents**:

| Traditional TOC (Word) | DITA Map |
|---|---|
| Static list of headings | Dynamic assembly of topics |
| Embedded in one document | Standalone file (`.ditamap`) |
| Changed manually | Updated by adding/removing topicrefs |
| One document = one TOC | One map can produce many TOCs |
| Formatting baked in | Formatting applied at publish time |

> The ditamap file is the single source of truth for what a publication contains.

---

# Map File: The `.ditamap` Extension

DITA maps use the `.ditamap` file extension and are valid XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-operator-manual">
  <title>MC-5000 Vertical Machining Center - Operator Manual</title>
  <topicref href="topics/concepts/dr5000-overview.dita"/>
  <topicref href="topics/tasks/daily-startup.dita"/>
  <topicref href="topics/tasks/shutdown.dita"/>
</map>
```

Key points:
- Root element is `<map>` (not `<concept>`, `<task>`, or `<reference>`)
- DTD declaration references `map.dtd`
- `<title>` is required -- it becomes the publication title
- `<topicref>` elements link to individual topic files

---

# Maps vs Topics -- The Separation of Concerns

```
TOPIC file (.dita):
  "What is the Coolant System?"
  "How do I replace the coolant filter?"
  "Coolant specifications table"

MAP file (.ditamap):
  Chapter 1: Introduction
     +-- Topic: What is the Coolant System?
     +-- Topic: Coolant specifications table
  Chapter 2: Procedures
     +-- Topic: How do I replace the coolant filter?
```

| Concern | Lives In |
|---|---|
| **Content** (what is said) | Topic (`.dita`) |
| **Structure** (how it is organized) | Map (`.ditamap`) |
| **Formatting** (how it looks) | Stylesheet (`.xsl`, `.css`) |

> This separation is the heart of DITA -- change the map, and you change the publication without touching any content.

---

# What a Map Controls

A DITA map governs more than just the sequence of topics:

| Map Feature | Controls |
|---|---|
| **Hierarchy** | Parent-child relationships between topics |
| **Navigation** | What appears in TOC, breadcrumbs, next/prev links |
| **Linking** | Related links, cross-references between topics |
| **Chunking** | Whether topics are combined or split in output |
| **Filtering** | Conditional inclusion based on audience/product |
| **Keys** | Named references to topics (keyref system) |
| **Metadata** | Publication-level metadata (title, author, dates) |

> A map is a command center, not just a list.

---

<!-- _class: divider -->

# Part 2: The topicref Element

---

# `<topicref>` -- The Core Building Block

The `<topicref>` element is how you include a topic in a map:

```xml
<topicref href="topics/tasks/daily-startup.dita"/>
```

**Required attribute:**

| Attribute | Purpose |
|---|---|
| `@href` | Path to the topic file (relative to the map file) |

> Every topicref must have an `@href`. Without it, the DITA-OT has nothing to process.

---

# topicref Hierarchy -- Nesting

Maps support unlimited nesting of topicrefs to build a document hierarchy:

```xml
<map>
  <title>MC-5000 Operator Manual</title>
  
  <topicref href="topics/concepts/introduction.dita">
    <topicref href="topics/concepts/machine-overview.dita"/>
    <topicref href="topics/concepts/safety-philosophy.dita"/>
  </topicref>
  
  <topicref href="topics/concepts/operation-overview.dita">
    <topicref href="topics/tasks/daily-startup.dita"/>
    <topicref href="topics/tasks/tool-loading.dita"/>
    <topicref href="topics/tasks/program-execution.dita">
      <topicref href="topics/reference/gcode-quickref.dita"/>
    </topicref>
  </topicref>
</map>
```

This produces:
```
1. Introduction
   1.1 Machine Overview
   1.2 Safety Philosophy
2. Operation
   2.1 Daily Startup
   2.2 Tool Loading
   2.3 Program Execution
       2.3.1 G-Code Quick Reference
```

---

# topicref Key Attributes

Beyond `@href`, several attributes control behavior:

```xml
<topicref 
  href="topics/tasks/startup.dita"
  navtitle="Daily Startup Procedure"
  toc="yes"
  type="task"
  format="dita"
  scope="local"
  print="yes"
  processing-role="normal"
/>
```

| Attribute | Values | Purpose |
|---|---|---|
| `@href` | File path | Location of the topic (required) |
| `@navtitle` | Text | Override the topic's title in navigation |
| `@toc` | yes / no | Include or hide from table of contents |
| `@type` | concept, task, reference... | Hint to processing engine |
| `@format` | dita, html, pdf... | Type of target resource |
| `@scope` | local, peer, external | Relationship to the current map |
| `@print` | yes / no | Include in printed/PDF output |
| `@processing-role` | normal, resource-only | Is it a navigation node or just a resource? |

---

# `@navtitle` -- Overriding the Display Title

Use `@navtitle` when the navigation title should differ from the topic title:

```xml
<!-- Topic title: "MC-5000 Spindle Warm-Up Cycle: Step-by-Step Instructions" -->
<!-- Nav title: "Spindle Warm-Up" (shorter, cleaner in TOC) -->

<topicref 
  href="topics/tasks/spindle-warmup.dita" 
  navtitle="Spindle Warm-Up"
/>
```

**When to use `@navtitle`:**
- The topic title is too long for a TOC entry
- The topic title contains codes that are meaningless in navigation
- You want a different name in different maps (same topic, different context)

> Best practice: Keep topic titles descriptive and self-contained. Use `@navtitle` sparingly -- overuse suggests your topic titles need improvement.

---

# `@toc` -- Hiding Topics from the Table of Contents

```xml
<!-- Standard: appears in TOC -->
<topicref href="topics/concepts/overview.dita" toc="yes"/>

<!-- Hidden: does NOT appear in TOC, but content is still in the output -->
<topicref href="topics/shared/legal-disclaimer.dita" toc="no"/>

<!-- Hidden: copyright page included in output but not in navigation -->
<topicref href="topics/frontmatter/copyright.dita" toc="no" print="yes"/>
```

**Common `toc="no"` use cases in manufacturing:**
- Legal disclaimers and copyright pages
- Auto-included safety warnings (conref'd into multiple topics)
- Hidden troubleshooting notes for field service (searchable but not browsable)

> `toc="no"` hides from navigation only. The topic still exists in the output and is searchable.

---

# `@scope` -- Defining Topic Relationships

| Scope | Meaning | When to Use |
|---|---|---|
| `local` | Part of the same documentation set | Standard: all topics in your repository |
| `peer` | Related but in a separate DITA map | Cross-manual references (Operator <-> Service) |
| `external` | Outside your DITA system entirely | Web links, PDFs, external documents |

```xml
<!-- Local: standard internal topic -->
<topicref href="topics/tasks/startup.dita" scope="local"/>

<!-- Peer: reference to service manual -->
<topicref href="../service-manual/service-map.ditamap" scope="peer"/>

<!-- External: web resource -->
<topicref href="https://supplier.com/specs/coolant-msds.pdf" 
          scope="external" format="pdf"/>
```

> Most of your topicrefs will be `scope="local"`. The framework default is `local` if `@scope` is omitted.

---

# `@format` -- Telling the Processor What to Expect

```xml
<!-- DITA topic -->
<topicref href="topics/tasks/startup.dita" format="dita"/>

<!-- DITA map (cross-map reference) -->
<topicref href="submaps/safety.ditamap" format="ditamap"/>

<!-- External PDF -->
<topicref href="../specs/coolant-msds.pdf" format="pdf" scope="external"/>

<!-- HTML resource -->
<topicref href="../datasheets/spindle-specs.html" format="html" scope="external"/>

<!-- Plain image (resource-only) -->
<topicref href="images/machine-front.png" format="png" 
          processing-role="resource-only"/>
```

> `@format` is optional for DITA topics (the processor auto-detects), but **mandatory** for non-DITA resources.

---

# `@processing-role` -- Content vs. Resource

```xml
<!-- Normal: displays as a content page (default) -->
<topicref href="topics/tasks/startup.dita" processing-role="normal"/>

<!-- Resource-only: used by other topics but does NOT produce its own page -->
<topicref href="images/dr5000-front.png" processing-role="resource-only"/>
<topicref href="shared/safety-warnings.dita" processing-role="resource-only"/>
```

| Role | Behavior |
|---|---|
| `normal` | Produces a page in the output (default) |
| `resource-only` | Available for referencing but generates no standalone page |

> Use `resource-only` for images, conref source files, and reusable warning fragments.

---

# topicref Summary Cheat Sheet

| Use Case | Attributes | Example |
|---|---|---|
| **Standard topic** | `href` | `<topicref href="topics/foo.dita"/>` |
| **Short TOC name** | `href`, `navtitle` | `<topicref href="topics/foo.dita" navtitle="Short Name"/>` |
| **Hidden from TOC** | `href`, `toc="no"` | `<topicref href="topics/legal.dita" toc="no"/>` |
| **External web link** | `href`, `scope`, `format` | `<topicref href="https://..." scope="external" format="html"/>` |
| **Image resource** | `href`, `processing-role`, `format` | `<topicref href="img/front.png" format="png" processing-role="resource-only"/>` |
| **Sub-map** | `href`, `format="ditamap"` | `<topicref href="submaps/safety.ditamap" format="ditamap"/>` |

> Start simple: `href` and `navtitle` cover 80% of your needs. Add other attributes as you need them.

---

<!-- _class: divider -->

# Part 3: Map Structure and Organization

---

# Anatomy of a Complete DITA Map

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-operator-manual" xml:lang="en">
  
  <!-- 1. Map title (required) -->
  <title>MC-5000 Vertical Machining Center - Operator Manual</title>
  
  <!-- 2. Map-level metadata (publication cover page) -->
  <topicmeta>
    <author type="creator">Technical Documentation Team</author>
    <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
    </copyright>
    <critdates>
      <created date="2026-01-15"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <othermeta name="document-status" content="approved"/>
  </topicmeta>
  
  <!-- 3. Topic references (the actual content) -->
  <topicref href="topics/frontmatter/copyright.dita" toc="no"/>
  <topicref href="topics/concepts/introduction.dita">
    <topicref href="topics/concepts/safety-overview.dita"/>
    <topicref href="topics/concepts/machine-specs.dita"/>
  </topicref>
  <topicref href="topics/concepts/operation.dita">
    <topicref href="topics/tasks/daily-startup.dita"/>
    <topicref href="topics/tasks/tool-loading.dita"/>
  </topicref>
  
</map>
```

---

# Map Organization Patterns

| Pattern | Structure | When to Use |
|---|---|---|
| **Flat** | All topicrefs at root level | Small documents (under 20 topics) |
| **Hierarchical** | Nested topicrefs with chapter heads | Medium to large manuals |
| **Sub-map** | topicrefs point to other `.ditamap` files | Very large publications, shared modules |
| **Bookmap** | Structured with frontmatter/chapter/appendix/backmatter | Formal publications and regulatory submissions |

---

# Flat Map Pattern (Under 20 Topics)

```xml
<map>
  <title>MC-5000 Quick Start Guide</title>
  <topicref href="topics/concepts/welcome.dita"/>
  <topicref href="topics/concepts/safety-brief.dita"/>
  <topicref href="topics/tasks/unpacking.dita"/>
  <topicref href="topics/tasks/installation.dita"/>
  <topicref href="topics/tasks/first-power-on.dita"/>
  <topicref href="topics/tasks/first-part-run.dita"/>
  <topicref href="topics/reference/specifications.dita"/>
  <topicref href="topics/reference/troubleshooting-quickref.dita"/>
</map>
```

> Simple, flat, fast. Perfect for quick-start guides, field service bulletins, and single-procedure documents.

---

# Hierarchical Map Pattern (20+ Topics)

```xml
<map>
  <title>MC-5000 Operator Manual</title>
  
  <!-- Chapter 1: Introduction -->
  <topicref href="topics/concepts/ch01-intro.dita" navtitle="Introduction">
    <topicref href="topics/concepts/machine-overview.dita"/>
    <topicref href="topics/concepts/safety.dita"/>
    <topicref href="topics/concepts/controls-layout.dita"/>
  </topicref>
  
  <!-- Chapter 2: Operation -->
  <topicref href="topics/concepts/ch02-operation.dita" navtitle="Operation">
    <topicref href="topics/tasks/startup.dita"/>
    <topicref href="topics/tasks/tool-setup.dita"/>
    <topicref href="topics/tasks/program-load.dita"/>
    <topicref href="topics/tasks/monitoring.dita"/>
  </topicref>
  
  <!-- Chapter 3: Maintenance -->
  <topicref href="topics/concepts/ch03-maintenance.dita" navtitle="Maintenance">
    <topicref href="topics/concepts/maint-overview.dita"/>
    <topicref href="topics/tasks/daily-cleaning.dita"/>
    <topicref href="topics/tasks/coolant-check.dita"/>
    <topicref href="topics/reference/maint-schedule.dita"/>
  </topicref>
</map>
```

---

# Sub-Map Pattern (Large Publications)

When a map becomes too large, split it into sub-maps:

**Main map (`dr5000-operator-manual.ditamap`):**
```xml
<map>
  <title>MC-5000 Operator Manual</title>
  <topicref href="submaps/safety-module.ditamap" format="ditamap"/>
  <topicref href="submaps/operation-module.ditamap" format="ditamap"/>
  <topicref href="submaps/maintenance-module.ditamap" format="ditamap"/>
  <topicref href="submaps/reference-module.ditamap" format="ditamap"/>
</map>
```

**Sub-map (`submaps/safety-module.ditamap`):**
```xml
<map>
  <title>Safety Module</title>
  <topicref href="../topics/concepts/safety-overview.dita">
    <topicref href="../topics/concepts/interlocks.dita"/>
    <topicref href="../topics/concepts/estop-system.dita"/>
    <topicref href="../topics/concepts/light-curtains.dita"/>
  </topicref>
</map>
```

> Sub-maps enable team-based authoring: the safety team owns `safety-module.ditamap`, the operations team owns `operation-module.ditamap`.

---

# Sub-Maps in the MC-5000 Framework

Our framework organizes maps by product and audience:

```
maps/
+-- dr5000/
|   +-- operator-manual/
|   |   +-- dr5000-operator-manual.ditamap       <-- Root map
|   |   +-- submaps/
|   |       +-- safety-module.ditamap
|   |       +-- operation-module.ditamap
|   |       +-- maintenance-module.ditamap
|   |       +-- reference-module.ditamap
|   +-- service-manual/
|   +-- quick-start/
+-- dr8000/
|   +-- ...
+-- common/
    +-- shared-safety.ditamap
    +-- shared-glossary.ditamap
```

> The `common/` directory holds sub-maps reused across product lines. This is where MC-5000 and MC-8000 share safety warnings.

---

<!-- _class: divider -->

# Part 4: Maps vs. Bookmaps

---

# What Is a Bookmap?

A **bookmap** is a specialized DITA map for formal, book-like publications:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE bookmap PUBLIC "-//OASIS//DTD DITA BookMap//EN" "bookmap.dtd">
<bookmap id="dr5000-service-book">
  <booktitle>
    <mainbooktitle>MC-5000 VMC Service Manual</mainbooktitle>
  </booktitle>
  
  <!-- Structured sections -->
  <frontmatter>
    <booklists/>
    <notices/><dedication/><preface/>
  </frontmatter>
  
  <chapter href="topics/concepts/safety.dita">
    <topicref href="topics/concepts/hazards.dita"/>
  </chapter>
  
  <appendix href="topics/reference/torque-specs.dita"/>
  
  <backmatter>
    <booklists>
      <indexlist/>
    </booklists>
  </backmatter>
</bookmap>
```

---

# Maps vs. Bookmaps -- When to Use Each

| Feature | Map (`<map>`) | Bookmap (`<bookmap>`) |
|---|---|---|
| **Structure** | Free-form hierarchy | Rigid: frontmatter, chapters, appendix, backmatter |
| **Title** | Simple `<title>` | Structured `<booktitle>` with main/sub titles |
| **Metadata** | Optional `<topicmeta>` | Required `<bookmeta>` with dates, publishers |
| **TOC/Index** | Manual (add topicrefs) | Auto-generated `<booklists>` |
| **Chapter numbering** | Manual | Automatic |
| **PDF optimization** | Good | Better (full book publishing features) |
| **Regulatory submission** | Acceptable | Preferred |
| **Use case** | Web help, quick guides, training | Printed manuals, formal specs, regulatory docs |

---

# Bookmap Sections

```xml
<bookmap>
  <booktitle>
    <mainbooktitle>MC-5000 VMC Operator Manual</mainbooktitle>
    <booktitlealt><edition>Revision 2.1</edition></booktitlealt>
  </booktitle>
  
  <bookmeta>
    <author>Technical Documentation Team</author>
    <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
    <critdates>
      <created date="2025-06-01"/>
      <revised modified="2026-06-07"/>
    </critdates>
  </bookmeta>
  
  <frontmatter>
    <preface/>
    <notices/>
    <booklists>
      <toc/>
      <figurelist/>
      <tablelist/>
    </booklists>
  </frontmatter>
  
  <chapter href="topics/concepts/intro.dita">
    <topicref href="topics/concepts/safety.dita"/>
  </chapter>
  
  <chapter href="topics/concepts/operation.dita">
    <topicref href="topics/tasks/startup.dita"/>
    <topicref href="topics/tasks/shutdown.dita"/>
  </chapter>
  
  <appendix href="topics/reference/specs.dita"/>
  
  <backmatter>
    <booklists>
      <indexlist/>
      <trademarklist/>
    </booklists>
  </backmatter>
</bookmap>
```

---

# Bookmap Structure Reference

```
<bookmap>
+-- <booktitle>            <-- Publication title (structured)
+-- <bookmeta>             <-- Publication metadata
+-- <frontmatter>          <-- Pre-content (optional)
|   +-- <preface/>
|   +-- <notices/>
|   +-- <booklists>
|       +-- <toc/>         <-- Auto-generated table of contents
|       +-- <figurelist/>  <-- Auto-generated list of figures
|       +-- <tablelist/>   <-- Auto-generated list of tables
+-- <chapter> x N          <-- Main content (each chapter = a topicref)
|   +-- <topicref> x N     <-- Topics within the chapter
+-- <appendix> x N         <-- Supplementary content
|   +-- <topicref> x N
+-- <backmatter>           <-- Post-content
    +-- <booklists>
        +-- <indexlist/>   <-- Auto-generated index
        +-- <trademarklist/>
```

> For the MC-5000 service manual and formal submissions, use bookmap. For quick-start guides, web help, and training materials, `<map>` is sufficient.

---

# Map-Bookmap Decision Guide

```
START: What kind of publication?
|
+-- Formal, printed manual?
|   +-- Yes --> Use <bookmap>
|   |   +-- Includes: Service manuals, regulatory submissions,
|   |                customer-facing documentation
|   |
|   +-- No --> Continue
|
+-- Needs auto-generated TOC, figures list, index?
|   +-- Yes --> Use <bookmap>
|   +-- No --> Continue
|
+-- Quick-start, training, or web help?
|   +-- Yes --> Use <map>
|   +-- No --> Continue
|
+-- Default for our framework: Use <map> for most documentation.
    Only use <bookmap> for formal publications bound for
    print or regulatory submission.
```

---

<!-- _class: divider -->

# Part 5: Creating Maps

---

# Creating Maps from Templates

Our framework provides map templates (T7-T9) in the templates directory:

```
templates/
+-- T7-MAP-SIMPLE.ditamap           <-- Flat map with basic structure
+-- T8-MAP-HIERARCHICAL.ditamap     <-- Multi-level with chapter heads
+-- T9-BOOKMAP-FORMAL.ditamap       <-- Formal bookmap for publications
```

**To start from a template:**

1. Copy the template to your `maps/` directory
2. Rename it following the naming convention
3. Update the `<title>` and `<topicmeta>`
4. Add or replace `<topicref>` elements with your topic files
5. Validate with `dita -validate`

> Templates save time and enforce consistency. Always start from a template, never from an empty file.

---

# Map Template: T7-MAP-SIMPLE

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="MAP-ID-PLACEHOLDER" xml:lang="en">
  
  <title>PUBLICATION TITLE</title>
  
  <topicmeta>
    <author type="creator">YOUR NAME</author>
    <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
    </copyright>
    <critdates>
      <created date="2026-06-07"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <othermeta name="document-status" content="draft"/>
  </topicmeta>
  
  <!-- ADD YOUR TOPICREFS BELOW -->
  <topicref href="topics/DIR/TOPIC-FILE.dita"/>
  
</map>
```

> Replace all UPPERCASE placeholders with your actual values.

---

# Map Template: T8-MAP-HIERARCHICAL

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="MAP-ID-PLACEHOLDER" xml:lang="en">
  
  <title>PUBLICATION TITLE</title>
  
  <topicmeta>
    <author type="creator">YOUR NAME</author>
    <publisher><name>Manufacturing Enterprise GmbH</name></publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Manufacturing Enterprise GmbH</copyrholder>
    </copyright>
    <critdates>
      <created date="2026-06-07"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <othermeta name="document-status" content="draft"/>
  </topicmeta>
  
  <!-- Chapter 1 -->
  <topicref href="topics/concepts/CHAPTER-01-HEAD.dita" navtitle="CHAPTER 1 TITLE">
    <topicref href="topics/concepts/TOPIC-1.dita"/>
    <topicref href="topics/tasks/TASK-1.dita"/>
  </topicref>
  
  <!-- Chapter 2 -->
  <topicref href="topics/concepts/CHAPTER-02-HEAD.dita" navtitle="CHAPTER 2 TITLE">
    <topicref href="topics/concepts/TOPIC-2.dita"/>
    <topicref href="topics/tasks/TASK-2.dita"/>
  </topicref>
  
  <!-- ADD MORE CHAPTERS AS NEEDED -->
  
</map>
```

---

# Creating Maps Manually (Without Templates)

If you must create a map from scratch:

1. **File**: Create a file with `.ditamap` extension
2. **Declaration**: Add XML and DTD declarations
3. **Root**: `<map>` element with an `@id`
4. **Title**: `<title>` (always present, always meaningful)
5. **Metadata**: `<topicmeta>` with author, dates, status
6. **Topics**: `<topicref>` elements with `@href`
7. **Validate**: Run validation before committing

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-my-guide">
  <title>My Guide Title</title>
  <topicref href="topics/my-topic.dita"/>
</map>
```

> Manual creation is error-prone. Always prefer a template unless you have a specific reason not to.

---

# Map Naming Convention

Follow our framework's naming convention for map files:

```
<ProductCode>-<PublicationType>.ditamap

Examples:
  dr5000-operator-manual.ditamap
  dr5000-service-manual.ditamap
  dr5000-quick-start.ditamap
  dr8000-maintenance-guide.ditamap
  common-shared-safety-warnings.ditamap
```

**Rules:**
- Lowercase kebab-case
- Product code first (or `common` for cross-product)
- Descriptive publication type
- No spaces, no special characters beyond hyphens

> Consistent naming makes findability trivial. Never name a map `map.ditamap` or `test.ditamap`.

---

<!-- _class: keypoint -->

# Key Rule: Every Map Must Be Valid

Before committing a map, validate it:

```bash
# Using DITA-OT from the command line
dita --input=maps/dr5000-operator-manual.ditamap \
     --format=html5 \
     --validate

# Using the framework's validation script
python scripts/validate-map.py maps/dr5000-operator-manual.ditamap
```

**The validation checks:**
- All `@href` paths exist and are valid
- No circular references between maps
- Map DTD is valid
- topicmeta uses framework-approved fields
- No broken links (missing topics)

> An invalid map = a broken build. CI/CD will reject invalid maps at the pull request stage.

---

<!-- _class: divider -->

# Part 6: Best Practices for Map Organization

---

# Map Design Principles

| Principle | What It Means |
|---|---|
| **One Publication, One Map** | Each output document has exactly one root map |
| **Shallow Depth** | Maximum 3-4 levels. If deeper, consider sub-maps |
| **Consistent Structure** | Same chapter numbering, similar layouts across product lines |
| **Reuse via Keys** | Use keyrefs instead of hardcoded paths for flexible linking |
| **Minimal Hardcoding** | Keep `@navtitle` minimal; let topic titles do the work |
| **Validate Early** | Run validation before pushing; don't wait for CI/CD |

---

# Organizing Maps for Multiple Audiences

One topic set, multiple maps, different audiences:

```
maps/dr5000/
+-- dr5000-operator-manual.ditamap       <-- Operators (audience=operator)
+-- dr5000-maintenance-manual.ditamap    <-- Maintenance (audience=maintenance)
+-- dr5000-engineer-reference.ditamap    <-- Engineers (audience=engineer)
+-- dr5000-quick-start.ditamap           <-- New operators (audience=all, simplified)
+-- dr5000-full-technical.ditamap        <-- All roles (comprehensive)
```

Each map includes a **different subset** of the same topic pool. A topic about advanced servo tuning appears in the engineer reference but not the operator manual.

> The DITA-OT uses the map's filter configuration to include or exclude content by audience.

---

# Organizing Maps by Product Variant

For the MC-5000 sold in multiple configurations:

```
topics/
+-- common/          <-- Content shared by ALL variants (200 topics)
+-- basic/           <-- MC-5000 Basic only (15 topics)
+-- pro/             <-- MC-5000 Pro only (20 topics)
+-- enterprise/      <-- MC-5000 Enterprise only (25 topics)

maps/dr5000/
+-- dr5000-basic-manual.ditamap          <-- common + basic topics
+-- dr5000-pro-manual.ditamap            <-- common + pro topics
+-- dr5000-enterprise-manual.ditamap     <-- common + enterprise topics
```

> Result: ~230 topics total, not ~690 (230 x 3). This is the power of topic-based authoring at scale.

---

# Good vs. Bad Map Practices

| Good | Bad |
|---|---|
| 3-4 levels of nesting max | 8+ levels deep (unusable navigation) |
| Descriptive `<title>` | `title="Manual"` (meaningless) |
| Relative paths | Absolute paths (breaks on other machines) |
| Topics grouped by chapter | Arbitrary topic ordering |
| Regular validation | Validating "just before release" |
| Updating `<revised>` date on changes | Leaving stale revision dates |
| Using sub-maps for large publications | One monolithic 500-line map |
| topicref + format="ditamap" for sub-maps | No sub-maps; 500 lines in one file |

---

# Path Conventions in Maps

**Always use relative paths** in `@href`:

```xml
<!-- CORRECT: relative path -->
<topicref href="../topics/tasks/startup.dita"/>

<!-- WRONG: absolute path (only works on your machine) -->
<topicref href="D:/projects/docs/topics/tasks/startup.dita"/>

<!-- WRONG: absolute path (only works on CI/CD server) -->
<topicref href="/home/runner/work/docs/topics/tasks/startup.dita"/>
```

Relative path FROM the map file TO the topic file:
```
maps/dr5000/dr5000-operator-manual.ditamap   <-- The map
  ../..                                      <-- Up to project root
  topics/tasks/startup.dita                  <-- Down to the topic

Correct: href="../../topics/tasks/startup.dita"
```

> Relative paths are portable. Your map works on your laptop, your colleague's workstation, and the CI/CD server.

---

# Handling Large Maps: When to Split

**Split to sub-maps when:**
- The map exceeds 200 topicrefs
- Multiple team members need to edit the map simultaneously
- Logical modules emerge (safety, operation, maintenance)
- Parts of the map are reused across publications

```xml
<!-- BEFORE: one huge map (500 lines, hard to manage) -->
<map>
  <title>Everything</title>
  <!-- 150 topicrefs... -->
</map>

<!-- AFTER: root map + sub-maps (modular, team-friendly) -->
<map>
  <title>MC-5000 Full Documentation</title>
  <topicref href="submaps/safety.ditamap" format="ditamap"/>
  <topicref href="submaps/operation.ditamap" format="ditamap"/>
  <topicref href="submaps/maintenance.ditamap" format="ditamap"/>
</map>
```

---

<!-- _class: divider -->

# Part 7: Map Topics in Context -- The MC-5000 Map

---

# MC-5000 Documentation Map Hierarchy

```
MC-5000 Documentation Suite
|
+-- dr5000-operator-manual.ditamap
|   +-- submaps/safety-module.ditamap
|   +-- submaps/operation-module.ditamap
|   +-- submaps/basic-maintenance.ditamap
|   +-- submaps/reference-module.ditamap
|
+-- dr5000-service-manual.ditamap (bookmap)
|   +-- frontmatter
|   +-- chapter: Safety and Hazard Mitigation
|   +-- chapter: Mechanical Systems
|   +-- chapter: Electrical Systems
|   +-- chapter: Control Systems
|   +-- chapter: Diagnostics and Troubleshooting
|   +-- appendix: Torque Specifications
|   +-- appendix: Wiring Diagrams
|   +-- backmatter (index)
|
+-- dr5000-quick-start.ditamap
|   +-- 8 flat topicrefs (simple, fast read)
|
+-- dr5000-training.ditamap
    +-- Learning maps for each role level
```

---

# The Operator Manual Map (In Practice)

The actual map used in production for MC-5000 operators:

```
dr5000-operator-manual.ditamap
|
+-- > Safety (sub-map)
|   +-- General Safety Principles
|   +-- Personal Protective Equipment
|   +-- Interlock System
|   +-- Emergency Stop Procedures
|   +-- Hazard Zone Map
|
+-- > Machine Overview (sub-map)
|   +-- MC-5000 at a Glance
|   +-- Control Panel Layout
|   +-- Axis Configuration
|   +-- Technical Specifications
|
+-- > Daily Operation (sub-map)
|   +-- Pre-Start Checklist
|   +-- Power-On Sequence
|   +-- Spindle Warm-Up Cycle
|   +-- Tool Loading and Presetting
|   +-- Program Loading from USB/Network
|   +-- Dry Run Procedure
|   +-- Production Run Monitoring
|   +-- End-of-Shift Shutdown
|
+-- > Basic Maintenance (sub-map)
|   +-- Daily Cleaning Protocol
|   +-- Coolant Level Check
|   +-- Chip Removal
|   +-- Weekly Inspection Checklist
|
+-- > Reference (sub-map)
    +-- G-Code Quick Reference
    +-- Alarm Code Directory
    +-- Tooling Specifications
    +-- Cutting Parameters Table
```

---

<!-- _class: divider -->

# Part 8: Exercise -- Create a MC-5000 DITA Map

---

<!-- _class: exercise -->

# Exercise: Build the MC-5000 Quick Start Map (45 min)

You are the technical writer for the MC-5000 Quick Start Guide. Your task is to create a complete, valid DITA map.

**Scenario:**
- Publication: MC-5000 Quick Start Guide for new operators
- Audience: Operators (level 1, novice)
- Product: MC-5000 VMC, version v2.1
- Status: Draft
- Author: You

---

# Exercise: Topic Files Available

The following topic files are ready in the repository. Build a map that includes them all:

```
topics/concepts/welcome-to-dr5000.dita          <-- Welcome and machine overview
topics/concepts/control-panel-tour.dita          <-- Control panel layout
topics/concepts/safety-first.dita                <-- Critical safety rules
topics/tasks/pre-start-checklist.dita            <-- What to check before powering on
topics/tasks/power-on-sequence.dita              <-- How to power on the machine
topics/tasks/spindle-warmup.dita                 <-- Warm-up procedure
topics/tasks/load-first-tool.dita                <-- How to load a tool
topics/tasks/run-first-program.dita              <-- Load and run a simple G-code
topics/tasks/shutdown.dita                       <-- End-of-shift shutdown
topics/reference/control-panel-quickref.dita     <-- Button/switch quick reference
topics/reference/alarm-codes-starter.dita        <-- 10 most common alarm codes
```

> These are real topic files in our framework. You only need to create the map (`.ditamap` file).

---

# Exercise: Map Requirements

Your map must include:

1. **Correct XML declaration and DTD**
2. **Map `<title>`**: "MC-5000 Quick Start Guide"
3. **Complete `<topicmeta>`** with:
   - Author (creator = your name)
   - Publisher (Manufacturing Enterprise GmbH)
   - Copyright (year 2026)
   - Created and revised dates (today: 2026-06-07)
   - Document status: draft
   - Product info (MC-5000 VMC, v2.1)
4. **Organized topicrefs**: Group into logical chapters
5. **Navigation titles**: Use `@navtitle` for clean TOC entries

---

# Exercise: Map Structure Hint

Organize your 11 topics into chapters:

```
Chapter 1: Welcome (2 topics)
  +-- Welcome to the MC-5000
  +-- Meet Your Control Panel
  
Chapter 2: Safety First (1 topic)
  +-- Critical Safety Rules
  
Chapter 3: Getting Started (5 topics)
  +-- Pre-Start Checklist
  +-- Power-On Sequence
  +-- Spindle Warm-Up
  +-- Loading Your First Tool
  +-- Running Your First Program
  
Chapter 4: Finishing Up (1 topic)
  +-- End-of-Shift Shutdown
  
Chapter 5: Quick Reference (2 topics)
  +-- Control Panel Quick Reference
  +-- Top 10 Alarm Codes
```

---

# Exercise: Starter Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-quick-start" xml:lang="en">
  
  <title>MC-5000 Quick Start Guide</title>
  
  <topicmeta>
    <!-- COMPLETE THE METADATA HERE -->
  </topicmeta>
  
  <!-- ADD YOUR TOPICREFS HERE -->
  
</map>
```

> Create this file as `maps/dr5000/dr5000-quick-start.ditamap`. Use relative paths for `@href`.

---

<!-- _class: exercise -->

# Exercise: Solution (Part 1 -- Map Header and Metadata)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-quick-start" xml:lang="en">
  
  <title>MC-5000 Quick Start Guide</title>
  
  <topicmeta>
    <author type="creator">[Your Name]</author>
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
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="document-status" content="draft"/>
    <othermeta name="content-type" content="map"/>
  </topicmeta>
```

---

# Exercise: Solution (Part 2 -- Chapters 1 and 2)

```xml
  <!-- Chapter 1: Welcome -->
  <topicref href="../topics/concepts/welcome-to-dr5000.dita"
            navtitle="Welcome" toc="yes">
    <topicref href="../topics/concepts/control-panel-tour.dita"
              navtitle="Meet Your Control Panel"/>
  </topicref>
  
  <!-- Chapter 2: Safety First -->
  <topicref href="../topics/concepts/safety-first.dita"
            navtitle="Safety First"/>
```

---

# Exercise: Solution (Part 3 -- Chapter 3)

```xml
  <!-- Chapter 3: Getting Started -->
  <topicref href="../topics/tasks/pre-start-checklist.dita"
            navtitle="Getting Started">
    <topicref href="../topics/tasks/power-on-sequence.dita"
              navtitle="Power-On Sequence"/>
    <topicref href="../topics/tasks/spindle-warmup.dita"
              navtitle="Spindle Warm-Up"/>
    <topicref href="../topics/tasks/load-first-tool.dita"
              navtitle="Loading Your First Tool"/>
    <topicref href="../topics/tasks/run-first-program.dita"
              navtitle="Running Your First Program"/>
  </topicref>
```

---

# Exercise: Solution (Part 4 -- Chapters 4, 5, and Close)

```xml
  <!-- Chapter 4: Finishing Up -->
  <topicref href="../topics/tasks/shutdown.dita"
            navtitle="Finishing Up"/>
  
  <!-- Chapter 5: Quick Reference -->
  <topicref href="../topics/reference/control-panel-quickref.dita"
            navtitle="Quick Reference">
    <topicref href="../topics/reference/alarm-codes-starter.dita"
              navtitle="Top 10 Alarm Codes"/>
  </topicref>
  
</map>
```

---

# Exercise: Self-Check

Validate your map against this checklist:

- [ ] XML and DTD declarations are present and correct
- [ ] Map `@id` is set (e.g., `dr5000-quick-start`)
- [ ] `<title>` is meaningful and not a placeholder
- [ ] `<topicmeta>` includes author, publisher, copyright, dates, status, prodinfo
- [ ] All 11 topic files are included as topicrefs
- [ ] Topics are grouped into logical chapters (Welcome, Safety, Getting Started, Finishing Up, Reference)
- [ ] All `@href` paths are relative (no absolute paths)
- [ ] Navigation titles are clean and human-readable
- [ ] File follows the naming convention: `dr5000-quick-start.ditamap`

> If all items are checked, your map is ready for validation.

---

<!-- _class: divider -->

# Part 9: Summary and Key Takeaways

---

# Module 06 Summary

**DITA Map Basics: What we covered**

1. A DITA map is the organizational layer that assembles topics into publications
2. `<topicref>` elements link to topic files via `@href`
3. Maps support unlimited nesting to create document hierarchies
4. Key topicref attributes: `@href`, `@navtitle`, `@toc`, `@type`, `@format`, `@scope`, `@processing-role`
5. Maps can be flat, hierarchical, or use sub-maps for scaling
6. Bookmaps are specialized maps for formal, book-like publications
7. Our framework provides templates T7 (simple), T8 (hierarchical), and T9 (bookmap)
8. Map-level `<topicmeta>` defines publication metadata and can override topic metadata
9. Always use relative paths, validate before committing, and follow naming conventions

---

# Key Takeaways

1. **Separate content from structure** -- Topics hold the words; maps organize them. Never mix the two.

2. **One publication, one map** -- Each output (PDF, web help, quick start) has its own `.ditamap`.

3. **Start from a template** -- Templates (T7-T9) save time, enforce consistency, and prevent errors.

4. **Use relative paths** -- Absolute paths break when the repository moves. Relative paths are portable.

5. **Validate before you commit** -- Invalid maps block the CI/CD build. Run validation locally first.

6. **Maps are the command center** -- They control structure, navigation, filtering, linking, and metadata for every publication.

> Next Module: 07 -- Approval Workflow: From Draft to Publication

---

<!-- _class: note -->

# Before Module 07

**Preparation checklist:**

- [ ] Open `maps/dr5000/dr5000-operator-manual.ditamap` in oXygen and study it
- [ ] Compare the operator manual map to the quick-start map you just created
- [ ] Review the T7, T8, and T9 templates in the `templates/` directory
- [ ] Practice creating a map from T8-MAP-HIERARCHICAL: choose any machine system (coolant, spindle, axis drive) and organize 5-8 topics
- [ ] Run `dita --validate` on your exercise map to check for errors

---

<!-- _class: divider -->

# Module 06 Complete

**Next Module: 07 -- Approval Workflow**

DITA Manufacturing Framework v1.1
Manufacturing Enterprise -- Documentation Governance Committee
