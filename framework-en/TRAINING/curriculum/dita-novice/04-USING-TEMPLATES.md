---
marp: true
theme: dita-training
class: invert
paginate: true
---

# Using Templates
## Module 04: Accelerating Authoring with Pre-Built Structures

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Understand the template types available and when to use each
- Create Concept, Task, and Reference topics from templates
- Identify and replace all placeholders in a template
- Avoid common template-use pitfalls
- Validate template-generated topics successfully

---

# Why Templates?

Starting from a blank file is slow and error-prone. Templates provide:

| Benefit | Description |
|---|---|
| **Consistency** | All topics start with the same structure |
| **Speed** | No need to type boilerplate every time |
| **Compliance** | Required elements are already in place |
| **Guidance** | Comments in templates explain what goes where |
| **Reduced errors** | Structure is pre-validated; less chance of nesting mistakes |

> Templates reduce topic creation time by 50-70% compared to starting from scratch.

---

# Template Types Overview

| Template | File Name | For Creating |
|---|---|---|
| **T1 Concept** | `T1-concept.dita` | Concept topics (what/why) |
| **T2 Task** | `T2-task.dita` | Task topics (how to) |
| **T3 Reference** | `T3-reference.dita` | Reference topics (specs/data) |
| **T4 Metadata** | `T4-metadata.xml` | Reusable prolog metadata block |
| **T5 Ditamap** | `T5-ditamap.ditamap` | DITA maps for topic organization |

All templates are located in:

```
templates/en/
```

---

# Template Workflow

```
COPY TEMPLATE ──→ RENAME FILE ──→ FILL PLACEHOLDERS ──→ VALIDATE
      │                │                  │                    │
      ▼                ▼                  ▼                    ▼
  cp T2-task     Use naming       Replace all            Green
  .dita →        convention:      [bracketed text]       checkmark
  t_replace_     c_/t_/r_ prefix  with real              in oXygen
  filter.dita    + descriptive    content
                 name
```

> Never edit the original template files. Always copy first.

---

# Template File Naming Convention

| Pattern | Example |
|---|---|
| `c_<topic-subject>.dita` | `c_coolant_system.dita` |
| `t_<verb-object>.dita` | `t_replace_filter.dita` |
| `r_<subject>.dita` | `r_coolant_specs.dita` |
| `w_<element-type>.dita` | `w_pressure_warning.dita` |

**Rules:**
- Lowercase letters only
- Underscores between words
- Descriptive, specific names
- No spaces, special characters, or uppercase

---

# T1: Concept Template (Structure)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">
<concept id="c_[topic-subject]">
  <title>[Topic Title — Descriptive Noun Phrase]</title>
  <shortdesc>[One-sentence summary of what this concept 
    explains and why it matters to the reader.]</shortdesc>
  <prolog>
    <!-- See T4 Metadata template -->
  </prolog>
  <conbody>
    <section>
      <title>[Section Heading — e.g., Overview]</title>
      <p>[Explanatory paragraph. Provide context and 
        background information.]</p>
    </section>
    <section>
      <title>[Section Heading — e.g., Key Concepts]</title>
      <p>[Explanatory paragraph. Break down complex ideas 
        into digestible points.]</p>
      <ul>
        <li>[Key point 1]</li>
        <li>[Key point 2]</li>
        <li>[Key point 3]</li>
      </ul>
    </section>
  </conbody>
</concept>
```

---

# Step-by-Step: Creating a Concept from T1

**Scenario**: Create a concept explaining the "Automated Tool Changer (ATC)" feature.

**Step 1: Copy the template**
```bash
cp templates/en/T1-concept.dita concepts/c_atc_overview.dita
```

**Step 2: Rename and open in oXygen**

**Step 3: Replace the `id`**
```xml
<concept id="c_atc_overview">
```

**Step 4: Replace `[Topic Title]`**
```xml
<title>Automated Tool Changer (ATC)</title>
```

**Step 5: Write the `shortdesc`**
```xml
<shortdesc>The Automated Tool Changer (ATC) enables 
  unattended tool swaps during CNC operations, reducing 
  cycle time by up to 30%.</shortdesc>
```

---

# Step-by-Step: Creating a Concept from T1 (continued)

**Step 6: Fill section content**

```xml
<conbody>
  <section>
    <title>Overview</title>
    <p>The ATC is a carousel-style tool magazine that 
      holds up to 24 tools and interfaces with the CNC 
      controller to execute tool changes without operator 
      intervention.</p>
  </section>
  <section>
    <title>How the ATC Works</title>
    <p>The ATC operates in four phases:</p>
    <ol>
      <li><b>Pre-select</b>: The controller queues the 
        next tool while the current operation runs.</li>
      <li><b>Exchange</b>: The spindle releases the current 
        tool and the carousel rotates to the target.</li>
      <li><b>Clamp</b>: The new tool is secured and verified 
        by the drawbar sensor.</li>
      <li><b>Resume</b>: The program resumes with the new 
        tool, typically within 2.5 seconds.</li>
    </ol>
  </section>
</conbody>
```

**Step 7: Validate** — green checkmark in oXygen.

---

# T2: Task Template (Structure)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE task PUBLIC "-//OASIS//DTD DITA Task//EN" "task.dtd">
<task id="t_[verb-object]">
  <title>[Task Title — Imperative Verb Phrase]</title>
  <shortdesc>[What to do, when/why to do it, and who 
    should do it.]</shortdesc>
  <prolog>
    <!-- See T4 Metadata template -->
  </prolog>
  <taskbody>
    <prereq>[Conditions that must be met before starting:
      - Required certifications or training
      - Tools, parts, or materials needed
      - System state (powered off, cooled down, etc.)
      - Safety precautions]
    </prereq>
    <steps>
      <step><cmd>[Action verb + object + specifics.]</cmd></step>
      <step><cmd>[Action verb + object + specifics.]</cmd>
        <info>[Clarification, expected response, or note.]</info>
      </step>
      <step><cmd>[Action verb + object + specifics.]</cmd></step>
    </steps>
    <result>[What the operator should see/observe if the 
      procedure was performed correctly.]</result>
    <postreq>[Follow-up actions: log entries, subsequent 
      procedures, verification steps.]</postreq>
  </taskbody>
</task>
```

---

# Step-by-Step: Creating a Task from T2

**Scenario**: Create a task for "Replacing the Air Filter on the APC-200 Control Cabinet."

**Step 1: Copy and rename**
```bash
cp templates/en/T2-task.dita tasks/t_replace_apc_air_filter.dita
```

**Step 2: Set the ID**
```xml
<task id="t_replace_apc_air_filter">
```

**Step 3: Write title and shortdesc**
```xml
<title>Replacing the APC-200 Control Cabinet Air Filter</title>
<shortdesc>Replace the air filter every 3 months or when 
  the filter-change indicator illuminates. This procedure 
  requires basic maintenance certification.</shortdesc>
```

---

# Creating a Task from T2 (continued)

**Step 4: Write prerequisites**
```xml
<prereq>
  <ul>
    <li>Replacement filter: Part # AF-200-R (one per cabinet)</li>
    <li>Phillips #2 screwdriver</li>
    <li>Power down the APC-200 controller (see 
      <xref href="t_power_down_apc.dita"/>)</li>
    <li>Wait 2 minutes for capacitor discharge</li>
  </ul>
</prereq>
```

**Step 5: Write numbered steps**
```xml
<steps>
  <step><cmd>Locate the air filter panel on the right 
    side of the control cabinet.</cmd></step>
  <step><cmd>Remove the four Phillips screws securing 
    the filter panel.</cmd>
    <info>The screws are captive — they will not fall 
      out of the panel.</info>
  </step>
  <step><cmd>Slide the filter panel outward and set 
    aside.</cmd></step>
  <step><cmd>Pull the old filter element straight out 
    of the slot.</cmd>
    <info>Note the airflow direction arrow printed on 
      the filter frame.</info>
  </step>
  <step><cmd>Insert the new filter element with the 
    airflow arrow pointing inward.</cmd></step>
  <step><cmd>Reattach the filter panel and tighten the 
    four screws.</cmd></step>
</steps>
```

---

# Creating a Task from T2 (continued)

**Step 6: Write result and postreq**
```xml
<result>
  <p>The filter-change indicator should turn off within 
    30 seconds of powering on the controller.</p>
</result>
<postreq>
  <p>Record the filter replacement in the APC-200 
    maintenance log. Reset the filter-hour counter in 
    the maintenance menu.</p>
</postreq>
```

**Step 7: Validate** — green checkmark.

---

# T3: Reference Template (Structure)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE reference PUBLIC "-//OASIS//DTD DITA Reference//EN" 
  "reference.dtd">
<reference id="r_[subject]">
  <title>[Reference Title — Descriptive Noun Phrase]</title>
  <shortdesc>[One sentence describing what data this 
    reference contains.]</shortdesc>
  <prolog>
    <!-- See T4 Metadata template -->
  </prolog>
  <refbody>
    <section>
      <title>[Table Group Title]</title>
      <table>
        <tgroup cols="[N]">
          <colspec colname="c1" colwidth="1.0*"/>
          <colspec colname="c2" colwidth="1.5*"/>
          <thead>
            <row>
              <entry>[Column 1 Header]</entry>
              <entry>[Column 2 Header]</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry>[Data cell]</entry>
              <entry>[Data cell]</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
  </refbody>
</reference>
```

---

# Best Practices for Replacing Placeholders

**Search for all bracket patterns before finalizing:**

| Pattern to Find | Example of Incorrect | Correct |
|---|---|---|
| `[bracketed text]` | `See the [manual reference].` | `See the APC-200 User Manual.` |
| `[N]` (in `cols="[N]"`) | `<tgroup cols="[N]">` | `<tgroup cols="3">` |
| `[Topic Title]` | `<title>[Topic Title]</title>` | `<title>Real Title</title>` |
| `[verb-object]` | `id="t_[verb-object]"` | `id="t_replace_filter"` |

**Checklist before validating:**
- [ ] Every `[placeholder]` replaced with real content
- [ ] `id` attribute is unique and follows naming convention
- [ ] DOCTYPE matches the root element type
- [ ] All sections that are kept have content (delete empty ones)
- [ ] No placeholder comments left in final text

---

# XML Comments in Templates

Templates include comments to guide you. Read them — they are your documentation:

```xml
<conbody>
  <!-- 
    CONCEPT BODY INSTRUCTIONS:
    - Each <section> should cover one aspect of the concept
    - Use 2-4 sections for a standard concept topic
    - First section: Overview (what is this?)
    - Middle sections: Key concepts, how it works, examples
    - Last section: Related information or next steps
    - Delete any sections you do not use
  -->
  <section>
    <title>[Section Heading]</title>
    <p>[Content]</p>
  </section>
</conbody>
```

> **Always delete instructional comments** from your final topic. They are for the author, not the reader.

---

# Common Pitfalls with Templates

| Pitfall | Consequence | Prevention |
|---|---|---|
| Forgetting to change `id` | Duplicate ID errors across all T1 copies | Change `id` FIRST, before writing content |
| Leaving `[placeholder]` text | Published docs show "[Topic Title]" | Full-text search for `[` before validating |
| Not deleting unused sections | Empty sections in output | Delete any `<section>` block you do not fill |
| Copying wrong template type | Concept structure for a Task procedure | Verify template type before copying |
| Editing original template | Lost template for future use | Always copy, never edit originals |
| Wrong DOCTYPE after template | Validation errors | Verify DOCTYPE matches opening element |

---

# Template Locations

All templates are version-controlled alongside documentation:

```
project-root/
  templates/
    en/                        ← English templates
      T1-concept.dita
      T2-task.dita
      T3-reference.dita
      T4-metadata.xml
      T5-ditamap.ditamap
      README.txt               ← Template usage instructions
    de/                        ← German templates (if localized)
    zh/                        ← Chinese templates (if localized)
  concepts/                    ← Your Concept topics go here
  tasks/                       ← Your Task topics go here
  references/                  ← Your Reference topics go here
  maps/                        ← Your ditamaps go here
```

> Templates are shared across all documentation projects. Do not customize templates for individual topics — that's what the placeholders are for.

---

# <!-- _class: exercise -->

# Exercise: Create a Concept from T1

**Scenario**: A new "Vibration Monitoring System (VMS)" has been added to the CNC-5000 machine.

Using the T1 Concept template, create a topic `c_vms_overview.dita` that explains:

- What the VMS is and why it was added to the CNC-5000
- How vibration monitoring works (sensors, thresholds, alerts)
- The three alert levels: Advisory, Warning, Critical

**Requirements:**
- Valid `id` following naming convention
- Meaningful `shortdesc`
- At least 3 `<section>` elements
- No remaining `[bracketed placeholders]`
- Passes DTD validation in oXygen

---

# <!-- _class: exercise -->

# Exercise: Create a Task from T2

**Scenario**: Document the procedure "Calibrating the VMS Sensors."

Using the T2 Task template, create a topic `t_calibrate_vms.dita` with:

- Prerequisites section (tools, certifications, machine state)
- At least 6 numbered steps (with `<cmd>` and at least one `<info>`)
- A `<result>` describing the expected calibration-success indicator
- A `<postreq>` for recording the calibration in the maintenance log

**Requirements:**
- Every step starts with an action verb
- One action per step
- No remaining placeholders
- Passes DTD validation

---

# Module Summary

Using Templates: What we covered

- Five template types (T1-T5) for all DITA content needs
- The template workflow: Copy → Rename → Replace → Validate
- Step-by-step walkthroughs for Concept (T1), Task (T2), Reference (T3)
- Placeholder patterns and how to find/replace them all
- XML comments as in-template documentation
- Common pitfalls and how to avoid them

---

# Key Takeaways

1. **Always copy, never edit** original template files

2. **Change the `id` first** — before writing any content

3. **Search for `[`** before finalizing — it reveals missed placeholders

4. **Delete unused sections** — empty sections produce empty output

5. **Read the XML comments** — they explain what each section is for, then delete them

6. **Validate after every template-based topic** — catch errors immediately

7. **Templates reduce errors and increase speed** — use them for every new topic

> Next Module: Metadata Basics — Adding Prolog Data to Your Topics
