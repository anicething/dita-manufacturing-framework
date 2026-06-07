---
marp: true
theme: dita-training
class: invert
paginate: true
---

# DITA XML Basics
## Module 03: Elements, Attributes, and Validation

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Understand XML fundamentals as they apply to DITA
- Identify and use common DITA elements correctly
- Read DOCTYPE declarations and understand their purpose
- Distinguish between well-formed and valid XML
- Avoid common XML errors in DITA authoring
- Create a minimal valid DITA topic from scratch

---

# XML Fundamentals for DITA

XML (eXtensible Markup Language) is the foundation of DITA.

**Key concepts:**

| Term | Definition | Example |
|---|---|---|
| **Element** | A named unit of content surrounded by tags | `<title>Safety Overview</title>` |
| **Tag** | The markers that define elements | `<title>` opening, `</title>` closing |
| **Attribute** | Name-value pair providing additional info about an element | `<topic id="t_123">` |
| **Content** | Text between tags (or nested elements) | `<p>This is the content.</p>` |
| **Self-closing** | Empty element that opens and closes in one tag | `<xref href="topic.dita"/>` |

---

# Well-Formed vs Valid XML

| | Well-Formed | Valid |
|---|---|---|
| **Definition** | Follows XML syntax rules | Follows the DITA grammar (DTD/Schema) |
| **Requirements** | Proper nesting, closed tags, one root | Correct elements in correct order per DITA spec |
| **Checked by** | Any XML parser | DITA-OT, oXygen validator |
| **Analogy** | Correct grammar | Correct grammar + correct document structure |

**Well-formed (syntax OK, but not DITA)**:
```xml
<topic><title>Hello</title><body><p>Text</p></body></topic>
```

**Valid (follows DITA rules)**:
```xml
<concept id="c_hello">
  <title>Hello</title>
  <conbody><p>Text</p></conbody>
</concept>
```

---

# XML Element Structure

```xml
<element-name attribute="value">
  <child-element>Content text here</child-element>
  <empty-element/>
</element-name>
```

**Rules to remember:**

1. Every opening tag must have a closing tag: `<p>` ... `</p>`
2. Tags are case-sensitive: `<title>` is NOT `<Title>`
3. Elements must be properly nested (last opened, first closed)
4. Attribute values must be quoted: `id="value"` not `id=value`
5. Self-closing tags use `/>`: `<xref href="file.dita"/>`

---

# Common XML Error: Unclosed Tags

**Wrong:**
```xml
<steps>
  <step><cmd>Open the valve
  <step><cmd>Check the gauge</cmd></step>
</steps>
```

**Right:**
```xml
<steps>
  <step><cmd>Open the valve</cmd></step>
  <step><cmd>Check the gauge</cmd></step>
</steps>
```

> oXygen will flag unclosed tags with a red underline and error message. Fix them immediately — they prevent validation and publishing.

---

# Common XML Error: Wrong Nesting

**Wrong** (overlapping elements):
```xml
<p><b>Bold text <i>bold and italic</b> italic only</i></p>
```

**Right** (properly nested):
```xml
<p><b>Bold text <i>bold and italic</i></b><i> italic only</i></p>
```

> Think of nesting like stacking bowls: the last bowl placed is the first one removed. Last opened = first closed.

---

# DOCTYPE Declarations

Every DITA topic begins with a DOCTYPE declaration:

```xml
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" 
  "concept.dtd">
```

**Breaking it down:**

| Part | Meaning |
|---|---|
| `<!DOCTYPE concept` | Root element is `<concept>` |
| `PUBLIC` | Uses a public, registered identifier |
| `"-//OASIS//DTD DITA Concept//EN"` | Formal Public Identifier (FPI) — DITA Concept, English |
| `"concept.dtd"` | System identifier — the DTD file path |

> The DOCTYPE tells your editor and the DITA-OT what rules to enforce. Each topic type has its own DOCTYPE: concept, task, reference, ditamap.

---

# DOCTYPE Declarations for Each Type

| Topic Type | DOCTYPE Root | DTD File |
|---|---|---|
| Concept | `<!DOCTYPE concept PUBLIC...` | `concept.dtd` |
| Task | `<!DOCTYPE task PUBLIC...` | `task.dtd` |
| Reference | `<!DOCTYPE reference PUBLIC...` | `reference.dtd` |
| DITA Map | `<!DOCTYPE map PUBLIC...` | `map.dtd` |
| Bookmap | `<!DOCTYPE bookmap PUBLIC...` | `bookmap.dtd` |

> The DOCTYPE **must match** the root element. You cannot use `<!DOCTYPE concept>` with a `<task>` root element.

---

# Common DITA Elements: Block-Level

Elements that create structural blocks:

| Element | Purpose | Parent |
|---|---|---|
| `<topic>` | Generic topic root | (root) |
| `<concept>` | Concept topic root | (root) |
| `<task>` | Task topic root | (root) |
| `<reference>` | Reference topic root | (root) |
| `<title>` | Topic/section title | topic, section, fig, table |
| `<shortdesc>` | One-paragraph summary | topic |
| `<body>` / `<conbody>` / `<taskbody>` / `<refbody>` | Topic body content | topic/concept/task/reference |
| `<section>` | Sub-section within body | body, conbody, refbody |
| `<example>` | Example content block | body, conbody, refbody, section |

---

# Common DITA Elements: Text and Lists

Elements for text content:

| Element | Purpose | Example |
|---|---|---|
| `<p>` | Paragraph | `<p>This is a paragraph.</p>` |
| `<ul>` | Unordered (bulleted) list | `<ul><li>Item 1</li></ul>` |
| `<ol>` | Ordered (numbered) list | `<ol><li>Step A</li></ol>` |
| `<li>` | List item | `<li>List item text</li>` |
| `<dl>` | Definition list | `<dl><dlentry>...</dlentry></dl>` |
| `<b>` | Bold text | `<b>important</b>` |
| `<i>` | Italic text | `<i>emphasis</i>` |
| `<u>` | Underline (avoid in DITA) | `<u>text</u>` |

---

# Common DITA Elements: Links, Images, Notes

| Element | Purpose | Example |
|---|---|---|
| `<xref>` | Cross-reference to another topic | `<xref href="topic.dita"/>` |
| `<link>` | Related link (in related-links) | `<link href="topic.dita"/>` |
| `<image>` | Image | `<image href="image.png"/>` |
| `<fig>` | Figure with caption | `<fig><title>Caption</title><image/></fig>` |
| `<note>` | Advisory note | `<note>Tip: Always check...</note>` |
| `<warning>` | Safety/hazard warning | `<warning type="hazard">...</warning>` |
| `<caution>` | Caution (damage possible) | `<caution>Do not exceed...</caution>` |
| `<table>` | Table | `<table><tgroup cols="2">...</tgroup></table>` |

---

# Common DITA Elements: Task-Specific

Elements specific to Task topics:

| Element | Purpose | Location |
|---|---|---|
| `<steps>` | Container for step sequence | `<taskbody>` |
| `<step>` | One action step | `<steps>` |
| `<cmd>` | The command/instruction | `<step>` |
| `<info>` | Additional info within a step | `<step>` |
| `<substeps>` | Sub-steps within a step | `<step>` |
| `<choices>` | Alternative choices | `<step>` |
| `<prereq>` | Prerequisites before task | `<taskbody>` |
| `<result>` | Expected outcome after steps | `<taskbody>` |
| `<postreq>` | Post-task follow-up actions | `<taskbody>` |

---

# Nesting Rules

DITA has strict rules about what elements can contain what:

```xml
<concept>
  <title/>           <!-- Required, first child -->
  <shortdesc/>       <!-- Optional, before conbody -->
  <prolog/>          <!-- Optional, metadata -->
  <conbody>          <!-- Required, main content -->
    <section>        <!-- Can have multiple sections -->
      <title/>       <!-- Section title (optional in body) -->
      <p/>           <!-- Can have multiple paragraphs -->
      <ul/>          <!-- Can have lists -->
      <table/>       <!-- Can have tables -->
      <fig/>         <!-- Can have figures -->
    </section>
  </conbody>
</concept>
```

> Rule: `<conbody>` contains `<section>` elements. `<section>` contains `<p>`, `<ul>`, `<ol>`, `<table>`, `<fig>`, etc.

---

# Common Nesting Errors

**Wrong** — `<p>` directly inside `<conbody>`:
```xml
<conbody>
  <p>This is a paragraph.</p>  <!-- ERROR: <p> requires <section> parent -->
  <p>Another paragraph.</p>
</conbody>
```

**Right** — `<p>` inside `<section>`:
```xml
<conbody>
  <section>
    <p>This is a paragraph.</p>
    <p>Another paragraph.</p>
  </section>
</conbody>
```

> General rule for Concept and Reference bodies: content goes in `<section>` elements. Only `<body>` (generic topic) accepts `<p>` directly.

---

# Attributes: Adding Metadata to Elements

Attributes provide additional information about elements:

```xml
<topic id="t_replace_filter" xml:lang="en-us">
  <title outputclass="procedure-title">Replacing the Filter</title>
  <p audience="maintenance">This procedure requires maintenance certification.</p>
</topic>
```

**Common attributes:**

| Attribute | Purpose | Example |
|---|---|---|
| `id` | Unique identifier | `id="c_coolant_overview"` |
| `href` | Hyperlink reference | `href="tasks/filter.dita"` |
| `conref` | Content reference (reuse) | `conref="shared/warnings.dita#warn_pressure"` |
| `xml:lang` | Language code | `xml:lang="en-us"` |
| `audience` | Target audience filter | `audience="maintenance"` |
| `product` | Product filter | `product="cnc-5000"` |

---

# The id Attribute: Critical Rules

The `id` attribute uniquely identifies every topic:

- **Every topic root must have an `id`**
- **IDs must be unique** across your entire documentation set
- **IDs cannot start with a digit** — use a letter prefix
- **No spaces** — use underscores or hyphens

| ID Pattern | Purpose | Example |
|---|---|---|
| `c_*` | Concept topics | `c_coolant_overview` |
| `t_*` | Task topics | `t_replace_filter` |
| `r_*` | Reference topics | `r_coolant_specs` |
| `w_*` | Shared/reusable content | `w_pressure_warning` |

> Duplicate IDs are one of the most common DITA errors. The DITA-OT will fail to build if two topics share the same ID.

---

# Validation: Catching Errors Early

**DTD Validation** checks that your XML follows DITA rules:

1. Required elements are present (e.g., `<title>` inside `<concept>`)
2. Elements are in the correct order
3. Elements contain valid child elements
4. Attributes have valid values

**In oXygen XML Editor:**
- Red underline = error (invalid)
- Yellow underline = warning
- Document toolbar: green check = valid, red x = invalid

> Always validate before committing. A single validation error can block the entire build.

---

# Common Validation Errors

| Error Message | Likely Cause | Fix |
|---|---|---|
| "Element 'p' is not allowed here" | `<p>` directly in `<conbody>` | Wrap in `<section>` |
| "Missing required element 'title'" | No `<title>` after root element | Add `<title>` as first child |
| "Duplicate ID" | Two topics share an `id` | Make each ID unique |
| "Element 'taskbody' must have 'steps'" | Task has no `<steps>` element | Add `<steps>` with at least one `<step>` |
| "Content of element 'step' is incomplete" | `<step>` missing `<cmd>` | Add `<cmd>` inside every `<step>` |

---

# <!-- _class: exercise -->

# Exercise: Identify the Errors

Find and list all XML errors in this topic:

```xml
<concept id=bad_concept>
  <title>Machine Safety
  <conbody>
    <p>Always follow these safety guidelines.</p>
    <section>
      <p>Wear <b>PPE<i> at all times</b> in the work area.</i>
    </section>
</concept>
```

> How many errors can you find? Write them down, then we'll review as a group.

---

# <!-- _class: exercise -->

# Exercise: Create a Minimal Valid Concept Topic

Create a valid DITA Concept topic by hand:

1. Open a new XML file in oXygen
2. Add the `<!DOCTYPE concept>` declaration
3. Create a `<concept>` root element with a unique `id`
4. Add `<title>`, `<shortdesc>`, and `<conbody>`
5. Include at least one `<section>` with `<title>` and `<p>`
6. Validate — you should see the green checkmark

**Topic**: "Machine Startup Safety Checklist" — explain what operators must verify before starting any machine on the shop floor.

---

# Working with Special Characters

Some characters have special meaning in XML and must be **escaped**:

| Character | Escape Code | Use When |
|---|---|---|
| `<` | `&lt;` | Less-than sign |
| `>` | `&gt;` | Greater-than sign |
| `&` | `&amp;` | Ampersand |
| `"` | `&quot;` | Double quote in attribute value |
| `'` | `&apos;` | Apostrophe in attribute value |

**Example:**
```xml
<p>The temperature must be &lt; 45°C.</p>
<p>Press Start &amp; hold for 3 seconds.</p>
```

> Use CDATA sections for blocks of example code: `<codeblock><![CDATA[if (temp < 40) { ... }]]></codeblock>`

---

# Using Images in DITA

```xml
<fig>
  <title>Coolant System Diagram</title>
  <image href="images/coolant-diagram.png" 
         width="600px" height="400px" 
         placement="break">
    <alt>Diagram showing coolant flow from reservoir 
      through pump to cutting head and return</alt>
  </image>
</fig>
```

**Image best practices:**
- Always include `<alt>` text for accessibility
- Use `placement="break"` for images between paragraphs
- Store images in an `images/` subdirectory
- Use relative paths (referenced from topic location)

---

# Linking Between Topics

**Cross-reference (inline link in body text):**

```xml
<p>For pressure specifications, see 
  <xref href="r_coolant_specs.dita">Coolant Specifications</xref>.
</p>
```

**Related links (appear at end of topic):**

```xml
<related-links>
  <link href="c_coolant_overview.dita">
    <linktext>Coolant System Overview</linktext>
  </link>
  <link href="t_replace_filter.dita">
    <linktext>Replacing the Coolant Filter</linktext>
  </link>
</related-links>
```

---

# Module Summary

DITA XML Basics: What we covered

- XML fundamentals: elements, attributes, well-formed vs valid
- DOCTYPE declarations and their role in validation
- Common DITA elements for each topic type
- Nesting rules and common mistakes
- The critical `id` attribute and naming conventions
- Validation and common error messages
- Images, links, and special characters

---

# Key Takeaways

1. **Well-formed is not enough** — DITA must also be valid per the DTD

2. **IDs must be unique** across your entire documentation set

3. **Content goes in sections** — `<p>` needs a `<section>` parent in concepts

4. **Every `<step>` needs a `<cmd>`** — one action per step

5. **The DOCTYPE defines the rules** — it must match your root element

6. **Validate early, validate often** — catch errors before they block your build

7. **Use ID prefixes consistently** — `c_`, `t_`, `r_` make topic types identifiable at a glance

> Next Module: Using Templates — Accelerating Authoring with Pre-Built Structures
