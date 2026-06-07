---
marp: true
theme: dita-training
class: invert
paginate: true
---

# The Three Core Topic Types
## Module 02: Concept, Task, and Reference

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Distinguish between the three core DITA topic types
- Choose the right topic type for any content situation
- Understand the internal structure of each topic type
- Read and understand DITA XML for each type
- Apply writing rules specific to Concept, Task, and Reference

---

# The Three Pillars of DITA

```
┌────────────────────────────────────────────┐
│              DITA TOPIC TYPES               │
├──────────┬──────────┬──────────────────────┤
│ CONCEPT  │   TASK   │      REFERENCE       │
│   "What  │  "How    │  "What are the       │
│   is?"   │  do I?"  │   specifics?"        │
├──────────┼──────────┼──────────────────────┤
│ Oversight│Procedure │ Specifications       │
│ Features │ Steps    │ Tables               │
│ Benefits │ Install  │ Error Codes          │
│ Why      │ Configure│ Parameters           │
└──────────┴──────────┴──────────────────────┘
```

---

# Content Distribution Rule

In a typical manufacturing documentation set:

```
Reference    20%
████████
          
Task         40%
████████████████

Concept      40%
████████████████
```

> This is a guideline, not a hard rule. Safety-critical documentation may be more Task-heavy. Engineering references will be Reference-heavy.

---

# The Concept Topic (c-)

**Answers: "What is this?" and "Why does it matter?"**

Prefix: `<concept id="c_...">`

Purpose:
- Provide overview and background
- Explain key concepts and principles
- Describe features and benefits
- Set context before procedures

> A reader should understand what something is and why it matters BEFORE learning how to use it.

---

# Concept Topic Structure

```xml
<concept id="c_coolant_system">
  <title>Coolant Circulation System</title>
  <shortdesc>
    The coolant circulation system regulates cutting 
    temperature through a closed-loop fluid cycle.
  </shortdesc>
  <conbody>
    <section>
      <title>Overview</title>
      <p>...</p>
    </section>
    <section>
      <title>Key Components</title>
      <p>...</p>
    </section>
    <section>
      <title>Temperature Control Logic</title>
      <p>...</p>
    </section>
  </conbody>
</concept>
```

---

# Concept Topic: When to Use

| Use Concept For | Do NOT Use Concept For |
|---|---|
| Feature descriptions | Step-by-step instructions |
| Architecture overviews | Specifications and tables |
| Background / history | Error code reference |
| Process explanations | Calibration procedures |
| Benefits and rationale | API documentation |
| Comparison of options | Parts lists |

---

# Concept Topic: Writing Guidelines

- **Length**: 1-3 printed pages (200-800 words)
- **Focus**: One subject, one audience
- **Structure**: General → Specific
- **Tone**: Explanatory, not instructional
- **Sections**: Use `<section>` for logical groupings

```xml
<section>
  <title>Why Coolant Filtration Matters</title>
  <p>Unfiltered coolant introduces three risks:</p>
  <ul>
    <li>Reduced cutting precision from debris interference</li>
    <li>Accelerated tool wear requiring more frequent replacement</li>
    <li>Potential surface defects on finished workpieces</li>
  </ul>
</section>
```

---

# The Task Topic (t-)

**Answers: "How do I ...?"**

Prefix: `<task id="t_...">`

Purpose:
- Provide step-by-step instructions
- Guide users through procedures
- Ensure consistent, safe operation
- Document maintenance and troubleshooting

> Every step must be actionable. If the reader cannot physically perform the step, rewrite it.

---

# Task Topic Structure

```xml
<task id="t_replace_filter">
  <title>Replacing the Coolant Filter</title>
  <shortdesc>Replace the coolant filter every 500 operating 
    hours to maintain system efficiency.</shortdesc>
  <taskbody>
    <prereq>Coolant system must be depressurized and cooled 
      to below 40°C.</prereq>
    <steps>
      <step><cmd>Shut off the main coolant pump at the 
        control panel.</cmd></step>
      <step><cmd>Close the inlet and outlet isolation 
        valves.</cmd></step>
      <step><cmd>Unscrew the filter housing using a 24mm 
        socket wrench.</cmd>
        <info>Rotate counter-clockwise. Expect minor fluid 
          drainage (approx. 100-200 mL).</info>
      </step>
      <step><cmd>Remove the old filter element.</cmd></step>
      <step><cmd>Insert the new filter element (Part # 
        CF-450-X2).</cmd></step>
      <step><cmd>Reattach the filter housing and tighten 
        to 25 N-m.</cmd></step>
      <step><cmd>Open the isolation valves.</cmd></step>
      <step><cmd>Restart the coolant pump.</cmd></step>
    </steps>
    <result>The coolant pressure gauge should read between 
      2.5 and 3.0 bar.</result>
    <postreq>Record the filter replacement in the 
      maintenance log.</postreq>
  </taskbody>
</task>
```

---

# Task Topic: Key Components

| Component | Purpose | Required? |
|---|---|---|
| `<title>` | Procedure name | Yes |
| `<shortdesc>` | What and when to do it | Yes |
| `<prereq>` | What must be true before starting | No (but recommended) |
| `<steps>` | Numbered action sequence | Yes |
| `<step><cmd>` | One action per step | Yes (inside steps) |
| `<step><info>` | Clarification, note, warning | No |
| `<step><substeps>` | Sub-steps for complex actions | No |
| `<result>` | What success looks like | No (but recommended) |
| `<postreq>` | What to do after completing | No |

---

# Task Writing Rules

| Rule | Bad Example | Good Example |
|---|---|---|
| **Action verb** | "The filter should be replaced." | "Replace the filter." |
| **One per step** | "Unscrew and remove and inspect." | Step 1: Unscrew. Step 2: Remove. Step 3: Inspect. |
| **Active voice** | "The button is pressed." | "Press the button." |
| **Be specific** | "Tighten appropriately." | "Tighten to 25 N-m." |
| **Expected result** | (Nothing) | "The pressure gauge should read 2.5-3.0 bar." |

> Include `<info>` for warnings, cautions, explanatory notes within steps. Use `<note>`, `<warning>`, or `<caution>` elements for safety-critical information.

---

# Task Topic: When to Use

| Use Task For | Do NOT Use Task For |
|---|---|
| Installation procedures | Explaining concepts |
| Maintenance procedures | Listing specifications |
| Configuration steps | Background information |
| Troubleshooting workflows | Feature overviews |
| Calibration procedures | Error code reference tables |
| Assembly/disassembly | Rationale for design decisions |

---

# The Reference Topic (r-)

**Answers: "What are the specifics?"**

Prefix: `<reference id="r_...">`

Purpose:
- Provide structured factual data
- Serve as a quick lookup (not reading material)
- Document specifications, parameters, and codes
- Present information in tables and lists

> Reference topics are meant for scanning and lookup, not sequential reading.

---

# Reference Topic Structure

```xml
<reference id="r_coolant_specs">
  <title>Coolant System Specifications</title>
  <shortdesc>Technical specifications for the Coolant 
    Pro-5000 system.</shortdesc>
  <refbody>
    <section>
      <title>Fluid Specifications</title>
      <table>
        <tgroup cols="2">
          <colspec colname="c1" colwidth="1.0*"/>
          <colspec colname="c2" colwidth="1.5*"/>
          <thead>
            <row>
              <entry>Parameter</entry>
              <entry>Value</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry>Fluid Type</entry>
              <entry>Semi-synthetic, water-miscible</entry>
            </row>
            <row>
              <entry>Concentration</entry>
              <entry>5-8% (refractometer reading)</entry>
            </row>
            <row>
              <entry>pH Range</entry>
              <entry>8.8 - 9.2</entry>
            </row>
            <row>
              <entry>Maximum Operating Temperature</entry>
              <entry>45°C</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
    <section>
      <title>Error Codes</title>
      <table>
        <tgroup cols="3">
          <colspec colname="c1"/>
          <colspec colname="c2"/>
          <colspec colname="c3"/>
          <thead>
            <row>
              <entry>Code</entry>
              <entry>Description</entry>
              <entry>Corrective Action</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry>E-201</entry>
              <entry>Low coolant level</entry>
              <entry>Refill to MAX line</entry>
            </row>
            <row>
              <entry>E-202</entry>
              <entry>Filter clogged</entry>
              <entry>Replace filter element</entry>
            </row>
            <row>
              <entry>E-203</entry>
              <entry>Pump overheat</entry>
              <entry>Allow 30-minute cooldown</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
  </refbody>
</reference>
```

---

# Reference Topic: When to Use

| Use Reference For | Do NOT Use Reference For |
|---|---|
| Technical specifications | Conceptual explanations |
| Parameter tables | Step-by-step procedures |
| Error code listings | Feature descriptions |
| Parts lists | Setup guides |
| Torque specifications | Troubleshooting narratives |
| API command reference | Training walkthroughs |
| Settings catalog | Comparative analyses |

---

# Reference Topic: Writing Guidelines

- **Length**: 1-3 pages, but can be longer for comprehensive datasets
- **Structure**: Use tables, simple lists, definition lists
- **Tone**: Factual, concise, no explanation needed
- **Accessibility**: Every row scannable at a glance

```xml
<section>
  <title>Torque Specifications</title>
  <simpletable>
    <sthead>
      <stentry>Component</stentry>
      <stentry>Torque (N-m)</stentry>
      <stentry>Thread</stentry>
    </sthead>
    <strow>
      <stentry>Filter housing</stentry>
      <stentry>25</stentry>
      <stentry>M24 x 2.0</stentry>
    </strow>
    <strow>
      <stentry>Pump mounting bolts</stentry>
      <stentry>45</stentry>
      <stentry>M12 x 1.75</stentry>
    </strow>
  </simpletable>
</section>
```

---

# Real-World Example: Coolant System Documentation Set

**Full documentation for a coolant system, structured correctly:**

| File | Type | Content |
|---|---|---|
| `c-coolant-overview.dita` | Concept | What the system is, how it works, key design principles |
| `c-coolant-safety.dita` | Concept | Hazards, PPE requirements, emergency procedures |
| `t-install-coolant.dita` | Task | How to install the coolant system |
| `t-replace-filter.dita` | Task | How to replace the coolant filter |
| `t-calibrate-flow.dita` | Task | How to calibrate coolant flow rate |
| `t-troubleshoot-leaks.dita` | Task | How to diagnose and fix common leaks |
| `r-coolant-specs.dita` | Reference | Fluid specs, operating parameters, tolerances |
| `r-error-codes.dita` | Reference | All controller error codes and meanings |
| `r-replacement-parts.dita` | Reference | Parts list with part numbers and intervals |

---

# Decision Tree: Choosing a Topic Type

```
Content has...

Procedures and steps? ──── YES ──── Use TASK
     │
     NO
     │
     ▼
Tables, specs, codes? ──── YES ──── Use REFERENCE
     │
     NO
     │
     ▼
Explanations, overview? ── Use CONCEPT
```

> When in doubt: if it has numbered steps, it's a Task. If it has tables of data, it's a Reference. Everything else is a Concept.

---

# Common Mistake: Mixing Types

**Wrong** — Task content inside a Concept:

```xml
<concept id="c_bad_example">
  <title>Installing the Software</title>
  <conbody>
    <p>This software provides monitoring capabilities.</p>
    <!-- WRONG: Steps in a Concept! -->
    <p>Step 1: Insert the USB drive.</p>
    <p>Step 2: Run setup.exe.</p>
  </conbody>
</concept>
```

**Right** — Separate Concept and Task:

```xml
<!-- c_software_overview.dita -->
<concept id="c_software_overview">...</concept>

<!-- t_install_software.dita -->
<task id="t_install_software">...</task>
```

---

# <!-- _class: exercise -->

# Exercise: Classify the Content

For each content item, decide: Concept, Task, or Reference?

1. Machine safety guidelines and PPE requirements
2. How to calibrate the laser alignment system
3. Torque specifications for all fasteners (by component)
4. Overview of the automated material handling system
5. How to perform the weekly preventive maintenance checklist
6. Error code table for the CNC controller (E-100 through E-499)
7. How the closed-loop quality inspection system works

---

# <!-- _class: exercise -->

# Exercise: Outline a Documentation Set

**Scenario**: You are documenting a new **Automated Pallet Changer (APC-200)**.

Create an outline showing:

- 3 Concept topics (with titles)
- 4 Task topics (with titles)
- 2 Reference topics (with titles)

For each topic, write a one-sentence `shortdesc`.

> Hint: Think about what an operator needs to understand, do, and look up.

---

# Summary: The Three Pillars

| | Concept | Task | Reference |
|---|---|---|---|
| **Question** | What is? Why? | How do I? | What are specifics? |
| **Prefix** | c_ | t_ | r_ |
| **Core Element** | `<conbody>` | `<taskbody>` | `<refbody>` |
| **Key Content** | Sections, paragraphs, images | Steps, commands, results | Tables, lists, specifications |
| **Reading Mode** | Read to understand | Follow to perform | Scan to find |
| **Typical Length** | 1-3 pages | 1-5 pages | 1-5 pages |

---

# Key Takeaways

1. **Three types, three purposes**: Concept = understand, Task = do, Reference = find

2. **One topic = one type = one file = one purpose**: Never mix types in a single topic

3. **Content distribution guideline**: ~40% Concept, ~40% Task, ~20% Reference

4. **Task topics use action verbs and numbered steps**: One action per step

5. **Reference topics use tables**: Make data scannable, not readable

6. **Choose the right type before you write**: This decision shapes everything

> Next Module: DITA XML Basics — Understanding Elements, Attributes, and Validation
