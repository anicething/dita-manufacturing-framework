---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Novice Capstone Project
## Module 08: Create Your First MC-5000 Document Set

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this capstone project, you will be able to:

- Plan a complete DITA document set from product requirements
- Create one concept topic, one task topic, and one reference topic
- Assemble topics into a valid DITA map with proper metadata
- Apply all M1-M7 metadata rules learned in Module 05
- Complete a peer review using the checklist from Module 07
- Guide your work through the 10-step approval workflow
- Self-assess your deliverables against the grading rubric

---

# Capstone Overview

You are a technical writer at Example Manufacturing. Your task is to create the **MC-5000 Quick Start Document Set** for new machine operators.

**Scope:**

| Deliverable | Type | Template |
|---|---|---|
| 1 Concept topic | MC-5000 Machine Overview | T1-CONCEPT |
| 1 Task topic | Daily Startup Procedure | T2-TASK |
| 1 Reference topic | Control Panel Quick Reference | T3-REFERENCE |
| 1 DITA Map | Quick Start Map | T7-MAP-SIMPLE / T8-MAP-HIERARCHICAL |
| Metadata | Complete prolog on all 3 topics | M1-M7 rules |

**Time allocation:** 6 hours total (suggested: 3 sessions of 2 hours each)

---

<!-- _class: divider -->

# Part 1: Planning Your Document Set

---

# Phase 1: Planning (60 minutes)

Before you write a single line of XML, plan your document set.

**Step 1.1: Understand the Product (15 min)**

Research the MC-5000 Vertical Machining Center. Review these framework files:

```
framework/product/dr5000/
  +-- product-overview.md
  +-- control-panel-layout.md
  +-- spindle-specs.md
```

Extract the key facts you will need:
- Machine model and version (MC-5000 VMC v2.1)
- Spindle specifications: max 12000 RPM, BT40 taper
- Axis configuration: X 800mm, Y 500mm, Z 500mm travels
- Control system: ExampleCorp iCNC-5000
- Key safety features: full enclosure, interlocks, 3 E-stop locations

---

# Phase 1: Planning (continued)

**Step 1.2: Define Your Audience (10 min)**

| Attribute | Value |
|---|---|
| **Audience** | Machine operator, entry-level |
| **Experience** | New to CNC; completed basic safety training |
| **Goal** | Start the machine safely and perform first operations |
| **Environment** | Factory floor; tablet and printed quick-reference |

Write a one-sentence audience statement:

> "This document set is for new MC-5000 operators who need to safely start the machine and understand the control panel layout before their first shift."

**Step 1.3: Define Topic Scope (15 min)**

For each topic, define the single question it answers:
- Concept: "What is the MC-5000 and how is it laid out?"
- Task: "How do I perform the daily startup procedure?"
- Reference: "What do the control panel buttons and indicators mean?"

---

# Phase 1: Planning (continued)

**Step 1.4: Create Your File Plan (10 min)**

Determine where files will live in the repository:

```
topics/
  concepts/
    dr5000-machine-overview.dita
  tasks/
    dr5000-daily-startup.dita
  reference/
    dr5000-control-panel-quickref.dita
maps/
  dr5000/
    dr5000-quick-start.ditamap
```

**Step 1.5: Create a Metadata Plan (10 min)**

Pre-fill the metadata values for all three topics:

```yaml
Product: MC-5000 Vertical Machining Center
Version: v2.1
Audience: operator
Author: [Your Name]
Status: draft
Language: en
Copyright Year: 2026
```

---

<!-- _class: divider -->

# Part 2: Creating Your Topics

---

# Phase 2: Write the Concept Topic (60 min)

**Topic:** MC-5000 Machine Overview
**Template:** T1-CONCEPT
**Question answered:** "What is the MC-5000 and how is it laid out?"

**Required elements:**

| Element | Description |
|---|---|
| `id` | `concept-dr5000-machine-overview` |
| `title` | "MC-5000 Vertical Machining Center Overview" |
| `shortdesc` | 1-3 sentences summarizing the machine |
| `conbody` | Body with: one `<p>`, one `<section>`, one `<ul>` or `<ol>` |
| `prolog` | Complete metadata (M1-M7 rules) |

**Content must include:**
- What the MC-5000 is and its primary purpose
- Key specifications: spindle (max 12000 RPM, BT40), axes (X: 800mm, Y: 500mm, Z: 500mm)
- Control system: ExampleCorp iCNC-5000
- Major components: spindle, tool changer (24-tool ATC), coolant system, chip conveyor
- Safety features: full enclosure, interlocks, E-stop buttons (3 locations)

---

# Concept Topic: Structure Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">
<concept id="concept-dr5000-machine-overview" xml:lang="en">
  <title>MC-5000 Vertical Machining Center Overview</title>
  <shortdesc>The MC-5000 is a 3-axis vertical machining center...</shortdesc>
  <prolog>
    <author type="creator">[Your Name]</author>
    <critdates>
      <created date="2026-06-07"/>
    </critdates>
    <metadata>
      <audience type="operator" job="machine-operator"
                experiencelevel="novice"/>
      <prodinfo>
        <prodname>MC-5000 Vertical Machining Center</prodname>
        <vrmlist><vrm version="v2.1"/></vrmlist>
      </prodinfo>
      <othermeta name="document-status" content="draft"/>
      <othermeta name="content-topic-type" content="concept"/>
    </metadata>
  </prolog>
  <conbody>
    <p>...</p>
    <section><title>Key Specifications</title>...</section>
    <section><title>Major Components</title>...</section>
    <section><title>Safety Features</title>...</section>
  </conbody>
</concept>
```

---

# Phase 3: Write the Task Topic (75 min)

**Topic:** MC-5000 Daily Startup Procedure
**Template:** T2-TASK
**Question answered:** "How do I perform the daily startup procedure?"

**Required elements:**

| Element | Description |
|---|---|
| `id` | `task-dr5000-daily-startup` |
| `title` | "Performing the MC-5000 Daily Startup" |
| `shortdesc` | Brief summary of the procedure |
| `taskbody` | Full task: prereq, context, steps, result, postreq |
| `prolog` | Complete metadata (M1-M7 rules) |

**Content must include:**
- Prerequisites: safety glasses, steel-toed boots, completed safety training
- Context: perform at beginning of each shift before any machining
- Steps: 6-8 specific steps (check coolant, check air pressure, power on main, power on CNC, home axes, spindle warm-up, verify tool changer, confirm ready)
- Result: machine is homed, spindle warmed up, ready for tool loading
- Post-requisites: proceed to tool loading and program execution

---

# Task Topic: Structure Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE task PUBLIC "-//OASIS//DTD DITA Task//EN" "task.dtd">
<task id="task-dr5000-daily-startup" xml:lang="en">
  <title>Performing the MC-5000 Daily Startup</title>
  <shortdesc>Complete this procedure at the beginning of each
    shift before any machining operations.</shortdesc>
  <prolog>
    <metadata>
      <othermeta name="content-topic-type" content="task"/>
    </metadata>
  </prolog>
  <taskbody>
    <prereq>
      <p>Ensure you are wearing <uicontrol>safety glasses</uicontrol>
         and <uicontrol>steel-toed boots</uicontrol>.</p>
    </prereq>
    <context>
      <p>Perform this procedure at the beginning of each shift...</p>
    </context>
    <steps>
      <step><cmd>Check the coolant level in the reservoir tank.</cmd></step>
      <step><cmd>Verify air pressure is between 5.5 and 6.5 bar.</cmd></step>
      <step><cmd>Turn the main power disconnect to the ON position.</cmd></step>
      <step><cmd>Press the POWER ON button on the control panel.</cmd></step>
      <step><cmd>After the iCNC-5000 boots, home all axes.</cmd></step>
      <step><cmd>Initiate spindle warm-up cycle at 2000 RPM for 5 minutes,
        then 4000 RPM for 3 minutes.</cmd></step>
      <step><cmd>Verify tool changer magazine is correctly indexed.</cmd></step>
      <step><cmd>Confirm the machine status indicator shows READY.</cmd></step>
    </steps>
    <result>
      <p>The MC-5000 is powered on, axes are homed, and the spindle
         has completed its warm-up cycle. The machine is ready for
         tool loading and program execution.</p>
    </result>
    <postreq>
      <p>Proceed to load the required tools and execute the
         machining program.</p>
    </postreq>
  </taskbody>
</task>
```

---

# Phase 4: Write the Reference Topic (45 min)

**Topic:** MC-5000 Control Panel Quick Reference
**Template:** T3-REFERENCE
**Question answered:** "What do the control panel buttons and indicators mean?"

**Required elements:**

| Element | Description |
|---|---|
| `id` | `reference-dr5000-control-panel` |
| `title` | "MC-5000 Control Panel Quick Reference" |
| `shortdesc` | Brief description of the reference content |
| `refbody` | Reference body with `<table>` (8+ rows) and `<section>` elements |
| `prolog` | Complete metadata |

**Content must include:**
- A table covering: POWER ON, POWER OFF, EMERGENCY STOP, CYCLE START, FEED HOLD, SPINDLE OVERRIDE, FEED RATE OVERRIDE, MODE SELECT
- Each row: control name, type, color, function
- Status indicator section
- Alarm indicator section

---

# Reference Topic: Structure Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE reference PUBLIC "-//OASIS//DTD DITA Reference//EN" "reference.dtd">
<reference id="reference-dr5000-control-panel" xml:lang="en">
  <title>MC-5000 Control Panel Quick Reference</title>
  <shortdesc>Quick reference for all control panel buttons,
    switches, and indicators on the MC-5000 VMC.</shortdesc>
  <prolog>
    <metadata>
      <othermeta name="content-topic-type" content="reference"/>
    </metadata>
  </prolog>
  <refbody>
    <section>
      <title>Control Buttons and Switches</title>
      <table>
        <tgroup cols="4">
          <colspec colname="c1" colwidth="1*"/>
          <colspec colname="c2" colwidth="1*"/>
          <colspec colname="c3" colwidth="1*"/>
          <colspec colname="c4" colwidth="2*"/>
          <thead>
            <row>
              <entry>Control</entry>
              <entry>Type</entry>
              <entry>Color</entry>
              <entry>Function</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry>POWER ON</entry>
              <entry>Pushbutton</entry>
              <entry>Green</entry>
              <entry>Energizes the machine control system</entry>
            </row>
            <row>
              <entry>POWER OFF</entry>
              <entry>Pushbutton</entry>
              <entry>Red</entry>
              <entry>De-energizes the control system after shutdown</entry>
            </row>
            <row>
              <entry>EMERGENCY STOP</entry>
              <entry>Mushroom head, twist-to-release</entry>
              <entry>Red</entry>
              <entry>Immediately cuts all power to motors and spindle</entry>
            </row>
            <row>
              <entry>CYCLE START</entry>
              <entry>Pushbutton, illuminated</entry>
              <entry>Green</entry>
              <entry>Starts program execution in AUTO or MDI mode</entry>
            </row>
            <row>
              <entry>FEED HOLD</entry>
              <entry>Pushbutton, illuminated</entry>
              <entry>Yellow</entry>
              <entry>Pauses axis movement; spindle continues</entry>
            </row>
            <row>
              <entry>SPINDLE OVERRIDE</entry>
              <entry>Rotary knob</entry>
              <entry>Black</entry>
              <entry>Adjusts spindle speed 0-120% of programmed value</entry>
            </row>
            <row>
              <entry>FEED RATE OVERRIDE</entry>
              <entry>Rotary knob</entry>
              <entry>Black</entry>
              <entry>Adjusts feed rate 0-150% of programmed value</entry>
            </row>
            <row>
              <entry>MODE SELECT</entry>
              <entry>Rotary switch</entry>
              <entry>Black</entry>
              <entry>Selects operating mode: JOG, MDI, AUTO, EDIT</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
    <section>
      <title>Status Indicators</title>
      <p>The iCNC-5000 displays status via the top-row LED bar:
        POWER (green), READY (green), RUNNING (green flashing),
        ALARM (red), and MESSAGE (yellow).</p>
    </section>
    <section>
      <title>Alarm Indicators</title>
      <p>When an alarm is active, the ALARM LED illuminates and
        the alarm code appears on the iCNC-5000 display. Common
        alarm ranges: 1000-1999 (axis), 2000-2999 (spindle),
        3000-3999 (tool changer), 7000-7999 (safety).</p>
    </section>
  </refbody>
</reference>
```

---

<!-- _class: divider -->

# Part 3: Creating the DITA Map

---

# Phase 5: Assemble the DITA Map (45 min)

**Map:** MC-5000 Quick Start Guide
**Template:** T7-MAP-SIMPLE or T8-MAP-HIERARCHICAL

**Required elements:**

| Element | Description |
|---|---|
| `map/@id` | `dr5000-quick-start-guide` |
| `title` | "MC-5000 Quick Start Guide" |
| `topicmeta` | Author, publisher, copyright, dates, prodinfo, status |
| `topicref` x3 | References to your three topics with `@navtitle` |

**Map structure (hierarchical):**

```
Chapter 1: Getting to Know Your Machine
  +-- MC-5000 Machine Overview (concept)

Chapter 2: Operating Procedures
  +-- Daily Startup Procedure (task)

Chapter 3: Reference
  +-- Control Panel Quick Reference (reference)
```

> Use `@navtitle` to create clean, short chapter names. Ensure all `@href` paths are relative.

---

# Map Structure Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-quick-start-guide" xml:lang="en">

  <title>MC-5000 Quick Start Guide</title>

  <topicmeta>
    <author type="creator">[Your Name]</author>
    <publisher>
      <name>Example Manufacturing Co., Ltd.</name>
    </publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Example Manufacturing Co., Ltd.</copyrholder>
    </copyright>
    <critdates>
      <created date="2026-06-07"/>
      <revised modified="2026-06-07"/>
    </critdates>
    <prodinfo>
      <prodname>MC-5000 Vertical Machining Center</prodname>
      <vrmlist><vrm version="v2.1"/></vrmlist>
    </prodinfo>
    <othermeta name="document-status" content="draft"/>
    <othermeta name="content-type" content="map"/>
  </topicmeta>

  <topicref
    href="../topics/concepts/dr5000-machine-overview.dita"
    navtitle="Getting to Know Your Machine" toc="yes"/>

  <topicref
    href="../topics/tasks/dr5000-daily-startup.dita"
    navtitle="Operating Procedures" toc="yes"/>

  <topicref
    href="../topics/reference/dr5000-control-panel-quickref.dita"
    navtitle="Reference" toc="yes"/>

</map>
```

---

<!-- _class: divider -->

# Part 4: Peer Review and Final Checklist

---

# Phase 6: Peer Review (30 min)

Swap your document set with a partner and complete the peer review.

**Peer Review Checklist (from Module 07):**

| Category | Check |
|---|---|
| **Concept** | Topic answers "what is this?" clearly. Shortdesc is a standalone summary. |
| **Task** | Steps are in correct order. Prerequisites are complete. Result and postreq present. |
| **Reference** | Table has correct columns. No procedural steps mixed in. |
| **Map** | All three topics included. Map title is meaningful. Topicmeta complete. |
| **Metadata** | All M1-M7 rules pass on all three topics. |
| **Validation** | `dita --validate` passes on all files. No XML errors. |

**Write 3-5 review comments** following the [blocking]/[suggestion]/[question] format.

---

# Phase 7: Final Validation Checklist

```
FINAL CHECKLIST
===============

XML and Validation
  [ ] All .dita files have XML declaration and DOCTYPE
  [ ] dita --validate passes on all 3 topics + map
  [ ] xml:lang="en" on all root elements

Content Quality
  [ ] Each topic answers exactly ONE question
  [ ] Concept: no procedural steps (those go in task)
  [ ] Task: numbered steps with clear cmd elements
  [ ] Reference: no instructions; facts and specs only
  [ ] Shortdesc on every topic is a true standalone summary

Metadata (M1-M7)
  [ ] Author present with type="creator"
  [ ] Created date using ISO 8601 (YYYY-MM-DD)
  [ ] Audience present (type="operator")
  [ ] Prodinfo present with prodname and vrm version
  [ ] document-status present (value "draft")
  [ ] content-topic-type present

Map
  [ ] Map id follows convention
  [ ] All @href paths are relative
  [ ] topicmeta complete: author, publisher, copyright, dates, prodinfo

Repository
  [ ] Files in correct directories
  [ ] File names follow naming convention
```

---

<!-- _class: divider -->

# Part 5: Submission and Grading

---

# Submission Process

Follow the approval workflow you learned in Module 07:

1. Create feature branch: `git checkout -b feature/capstone-dr5000-quickstart`
2. Commit all 4 files with a descriptive message
3. Push and create a pull request using the PR template
4. Assign your peer and instructor as reviewers

```bash
git add topics/concepts/dr5000-machine-overview.dita
git add topics/tasks/dr5000-daily-startup.dita
git add topics/reference/dr5000-control-panel-quickref.dita
git add maps/dr5000/dr5000-quick-start.ditamap
git commit -m "feat: Add MC-5000 Quick Start document set (Capstone)"
git push -u origin feature/capstone-dr5000-quickstart
```

---

# Grading Rubric

Your capstone project is graded on 100 points across five categories:

| Category | Max Points | Criteria |
|---|---|---|
| **Content Quality** | 30 | Clear, accurate, well-structured; correct topic types used |
| **DITA Markup** | 25 | Valid XML, correct elements, proper nesting |
| **Metadata** | 20 | All M1-M7 rules satisfied; complete and accurate |
| **Map Assembly** | 15 | Correct structure, relative paths, complete topicmeta |
| **Review Readiness** | 10 | Self-review done, peer review incorporated, PR template filled |

**Grade scale:**

| Score | Grade | Description |
|---|---|---|
| 90-100 | **Pass with Distinction** | Production-ready; no corrections needed |
| 75-89 | **Pass** | Minor corrections; ready after one revision |
| 60-74 | **Conditional Pass** | Significant issues; second submission required |
| Below 60 | **Revise and Resubmit** | Major gaps; redo with instructor guidance |

---

# Grading Rubric: Detailed Breakdown

**Content Quality (30 points):**

| Criterion | Points | What "Excellent" Looks Like |
|---|---|---|
| Concept topic clarity | 10 | Standalone understanding of the MC-5000 without consulting other documents |
| Task topic correctness | 10 | Steps flow logically; a novice operator could follow this safely |
| Reference topic usefulness | 10 | Operator can find any button function in under 10 seconds |

**DITA Markup (25 points):**

| Criterion | Points | What "Excellent" Looks Like |
|---|---|---|
| Valid XML | 10 | `dita --validate` exits with zero errors |
| Correct element usage | 10 | `<cmd>` in steps; `<section>` in concept; `<table>` in reference |
| Proper structure | 5 | Required elements in correct order; no missing mandatory elements |

---

# Grading Rubric: Detailed Breakdown (continued)

**Metadata (20 points):**

| Criterion | Points | Rule |
|---|---|---|
| Author and dates | 5 | M1, M2: author with type, created date ISO 8601 |
| Product info | 5 | M3: prodname, vrm version |
| Audience | 3 | M7: type, job, experiencelevel |
| Status and type | 4 | M1: document-status, content-topic-type |
| Language and base | 3 | xml:lang present; DITAArchVersion present |

**Map Assembly (15 points):**

| Criterion | Points |
|---|---|
| All three topics included with correct @href | 5 |
| Map metadata complete (author, pub, copyright, dates, prodinfo) | 5 |
| Map title meaningful; navtitle used appropriately | 3 |
| Map id follows convention | 2 |

**Review Readiness (10 points):**

| Criterion | Points |
|---|---|
| Self-review checklist completed honestly | 3 |
| Peer review feedback incorporated | 4 |
| PR template filled completely | 3 |

---

<!-- _class: divider -->

# Part 6: Common Mistakes and How to Avoid Them

---

# Top 10 Capstone Mistakes

| # | Mistake | How to Avoid |
|---|---|---|
| 1 | **Missing shortdesc** | Every topic must have a shortdesc. First thing readers see. |
| 2 | **Metadata in only one topic** | Copy metadata to ALL three topics. Each is standalone. |
| 3 | **Procedural content in concept** | "To do X, press Y" belongs in task. Concept = understanding. |
| 4 | **Instructions in reference** | References are lookup tables. No "how to" content. |
| 5 | **Absolute paths in @href** | `C:\Users\...` breaks everywhere. Use relative paths. |
| 6 | **No prolog at all** | The prolog block is mandatory. All 7 rules require it. |
| 7 | **Vague titles** | "Overview" is meaningless. Be specific and product-aware. |
| 8 | **Non-standard IDs** | Use `task-dr5000-daily-startup`, not `task001`. |
| 9 | **Forgetting to validate** | Run `dita --validate` on every file before committing. |
| 10 | **Self-review skipped** | The #1 cause of peer reviewer frustration. Check your own work. |

---

# Quality Self-Check: Before You Submit

**Concept topic:**
- [ ] If I read only this topic, do I understand what the MC-5000 is?
- [ ] Are the specifications clear and accurate?
- [ ] Would a new operator feel oriented after reading this?

**Task topic:**
- [ ] Could I physically perform this startup by following these steps?
- [ ] Are the steps in the correct order?
- [ ] Did I include what the operator needs BEFORE starting (prereq)?
- [ ] Did I describe what "success" looks like (result)?

**Reference topic:**
- [ ] Can I find a specific button's function in under 10 seconds?
- [ ] Is every control listed with its color, type, and function?
- [ ] Are there no instructions hiding in this reference?

---

<!-- _class: divider -->

# Part 7: Example Deliverables (Reference)

---

# Example: Concept Topic Shortdesc Quality

**Poor (does not stand alone):**
```xml
<shortdesc>This topic describes the MC-5000 machine.</shortdesc>
```

**Good (standalone summary):**
```xml
<shortdesc>The MC-5000 is a 3-axis vertical machining center with a
  12,000 RPM BT40 spindle, 24-tool automatic tool changer, and
  ExampleCorp iCNC-5000 control system, designed for precision
  milling, drilling, and tapping of medium-sized workpieces.</shortdesc>
```

**Excellent (standalone + value):**
```xml
<shortdesc>The MC-5000 Vertical Machining Center delivers high-precision
  3-axis machining with a 12,000 RPM BT40 spindle and 24-tool ATC.
  Designed for medium-batch production of components up to
  800x500x500mm, it features the ExampleCorp iCNC-5000 control system
  with conversational programming and full safety interlocks.</shortdesc>
```

> Your shortdesc is the most important 1-3 sentences you will write. It is the search result snippet, the TOC description, and the reader's first impression.

---

# Example: Task Step Quality

**Poor (vague, no specific action):**
```xml
<step><cmd>Start the spindle warm-up.</cmd></step>
```

**Good (specific action with parameters):**
```xml
<step>
  <cmd>Initiate the spindle warm-up cycle.</cmd>
  <info>Set the <uicontrol>Spindle Override</uicontrol> knob to 100%.
    On the iCNC-5000, navigate to
    <menucascade><uicontrol>MDI</uicontrol>
    <uicontrol>Spindle</uicontrol></menucascade>
    and enter <userinput>S2000 M03</userinput>, then press
    <uicontrol>CYCLE START</uicontrol>.</info>
</step>
```

**Excellent (specific + safety + verification):**
```xml
<step>
  <cmd>Initiate the spindle warm-up cycle at 2000 RPM.</cmd>
  <info>
    <note type="caution">Ensure the spindle area is clear of tools,
    workpieces, and personnel before starting spindle rotation.</note>
  </info>
  <substeps>
    <substep><cmd>Set <uicontrol>Spindle Override</uicontrol>
      to 100%.</cmd></substep>
    <substep><cmd>Press <uicontrol>MDI</uicontrol> mode.</cmd></substep>
    <substep><cmd>Enter <userinput>S2000 M03</userinput>.</cmd></substep>
    <substep><cmd>Press <uicontrol>CYCLE START</uicontrol>.</cmd></substep>
  </substeps>
  <stepresult>The spindle accelerates to 2000 RPM. Verify the spindle load
    meter reads below 15% within 10 seconds.</stepresult>
</step>
```

---

# Example: Map Quality

**Poor (minimal, no metadata):**
```xml
<map><title>Manual</title>
  <topicref href="topic1.dita"/>
</map>
```

**Excellent (complete, production-ready):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="dr5000-quick-start-guide" xml:lang="en">
  <title>MC-5000 Vertical Machining Center - Quick Start Guide</title>
  <topicmeta>
    <author type="creator">[Your Name]</author>
    <publisher><name>Example Manufacturing Co., Ltd.</name></publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>Example Manufacturing Co., Ltd.</copyrholder>
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
    <othermeta name="audience" content="operator"/>
  </topicmeta>
  <topicref href="../topics/concepts/dr5000-machine-overview.dita"
            navtitle="Getting to Know Your Machine" toc="yes"/>
  <topicref href="../topics/tasks/dr5000-daily-startup.dita"
            navtitle="Operating Procedures" toc="yes">
    <topicref href="../topics/reference/dr5000-control-panel-quickref.dita"
              navtitle="Control Panel Reference"/>
  </topicref>
</map>
```

---

<!-- _class: divider -->

# Part 8: Post-Submission and Next Steps

---

# After Submission: What Happens Next

1. **Peer review** -- Your partner reviews your submission within 2 business days
2. **Instructor review** -- Your instructor grades against the rubric within 5 business days
3. **Feedback session** -- 15-minute one-on-one feedback session scheduled
4. **Revision** -- Address any [blocking] items within 3 business days
5. **Final grade** -- Resubmitted work is re-graded; highest grade counts

**If you pass (75+):**
- Your content may be integrated into the framework's sample content library
- You receive the **DITA Novice Certificate of Completion**
- You are eligible to enroll in the DITA Professional course

**If you receive Conditional Pass (60-74):**
- Specific revision requirements provided
- One resubmission allowed within 5 business days
- Office hours available for guidance

---

# Earning the DITA Novice Certification

To earn the **DITA Manufacturing Framework -- Novice Certification**, you must:

| Requirement | Status |
|---|---|
| Complete Modules 01-07 | [ ] All 7 modules reviewed |
| Pass all module quizzes | [ ] Average score > 80% |
| Complete Capstone Project | [ ] Score > 75 on rubric |
| Attend peer review session | [ ] Participation confirmed |
| Submit final revised capstone | [ ] All blocking items resolved |

**Certification entitles you to:**
- DITA Novice digital badge (LinkedIn-compatible)
- Access to Professional-level course enrollment
- Framework contributor access (comment and raise issues)
- Listing in the ExampleCorp Certified Technical Writers directory

---

# Key Takeaways

1. **Plan before you write** -- Spend time understanding the product, audience, and scope. The file plan and metadata plan prevent rework.

2. **One topic, one question** -- The concept explains, the task instructs, the reference lists. Never mix types within a single topic.

3. **Shortdesc is your headline** -- It is the search result, the TOC entry, and the reader's first impression. Make it count.

4. **Metadata is not optional** -- All M1-M7 rules apply to every topic. Metadata makes content findable and filterable.

5. **Validate, then validate again** -- `dita --validate` catches XML errors. Peer review catches content errors. Use both.

6. **The map is the publication** -- Without a valid, complete map, your topics are just files. The map transforms them into a publication.

---

<!-- _class: note -->

# Capstone Project: Quick Reference Card

```
MC-5000 QUICK START DOCUMENT SET -- CAPSTONE CHECKLIST

Files to Create (4):
  [ ] topics/concepts/dr5000-machine-overview.dita
  [ ] topics/tasks/dr5000-daily-startup.dita
  [ ] topics/reference/dr5000-control-panel-quickref.dita
  [ ] maps/dr5000/dr5000-quick-start.ditamap

By Topic:
  Concept: title, shortdesc, conbody (p + 3 sections), prolog
  Task: title, shortdesc, taskbody (prereq, context, 6-8 steps,
        result, postreq), prolog
  Reference: title, shortdesc, refbody (table 8+ rows,
             2 sections), prolog
  Map: title, topicmeta (complete), 3 topicrefs with navtitle

Validation:
  [ ] dita --validate on all 4 files
  [ ] All M1-M7 rules pass
  [ ] All @href paths are relative
  [ ] Peer review completed and addressed

Submission:
  [ ] Branch: feature/capstone-dr5000-quickstart
  [ ] PR created with template filled
  [ ] Self-review checklist completed

DUE: 7 days from module start
```

---

<!-- _class: divider -->

# Module 08 Complete

**Congratulations! You have completed the DITA Novice Course.**

**Next: DITA Professional Course -- Module 01: Extended Topic Types**

DITA Manufacturing Framework v1.1
Example Manufacturing Co., Ltd. -- Documentation Governance Committee
