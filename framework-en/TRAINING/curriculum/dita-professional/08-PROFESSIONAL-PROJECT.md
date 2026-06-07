---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Professional Capstone Project
## Module 8 -- Complete the MC-5000 Documentation Set

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | Project Overview | 3-6 |
| 2 | Phase 1: Content Authoring (8 Topics) | 7-11 |
| 3 | Phase 2: DITA Map with Keys | 12-14 |
| 4 | Phase 3: Subject Scheme | 15-16 |
| 5 | Phase 4: Bookmap & PDF | 17-18 |
| 6 | Phase 5: Conref Reuse | 19-20 |
| 7 | Phase 6: Localization Preparation | 21-22 |
| 8 | Phase 7: GitHub PR Submission | 23-24 |
| 9 | Evaluation Criteria | 25-27 |
| 10 | Summary | 28-30 |

---

## Capstone: The Challenge

You are a technical writer at **Example Manufacturing**. The company needs complete documentation for the **MC-5000 CNC Lathe**, covering safety, operation, maintenance, and troubleshooting.

**Your mission**: Produce 8 DITA topics (2 concept + 3 task + 2 reference + 1 troubleshooting), a DITA map with keys, a subject scheme for metadata enforcement, a professional bookmap, conref-based content reuse, and localization-ready structure. Submit via GitHub PR.

This capstone demonstrates mastery of all 7 Professional modules.

---

## The MC-5000 CNC Lathe

| Specification | Detail |
|---|---|
| **Model** | MC-5000 |
| **Type** | 5-Axis CNC Lathe with Live Tooling |
| **Max Turning Diameter** | 500 mm |
| **Spindle Speed** | 50 - 6000 RPM |
| **Control System** | Fanuc 31i-B5 |
| **Tool Stations** | 12-station turret |
| **Coolant System** | High-pressure through-tool (70 bar) |
| **Safety** | ISO 12100, ISO 13849-1 PL d |

---

## Learning Objectives

By completing this capstone, you will demonstrate:

1. **Multi-type authoring**: concept, task, reference, troubleshooting topics
2. **Key-based references**: keydefs for product names, variables, and links
3. **Subject Scheme**: Controlled vocabulary enforcement for metadata
4. **Bookmap**: Professional frontmatter/chapter/backmatter structure
5. **Conref reuse**: Shared warnings, notes, and reusable content
6. **Localization prep**: xml:lang attributes, translation notes, XLIFF-ready structure
7. **GitHub workflow**: Branch, PR, review, merge
8. **Professional-quality output**: Chemistry PDF with custom CSS

---

## Deliverables Checklist

| # | Deliverable | Count | Details |
|---|---|---|---|
| 1 | Concept topics | 2 | Safety overview, machine architecture |
| 2 | Task topics | 3 | Daily startup, tool change, coolant maintenance |
| 3 | Reference topics | 2 | Cutting parameters, tool offset specs |
| 4 | Troubleshooting topic | 1 | Common alarms and remedies |
| 5 | DITA Map with keys | 1 | keydefs for product names, shared links |
| 6 | Subject Scheme | 1 | Controlled vocabularies for metadata |
| 7 | Bookmap | 1 | Frontmatter, chapters, backmatter |
| 8 | Conref library | 1 file | Shared safety warnings and notes |
| 9 | Chemistry CSS | 1 | PDF layout (~150 lines) |
| 10 | Build script | 1 | Automated PDF generation |

**Total**: 8 topics + 5 supporting files = 13 deliverables

---

## Topic Allocation

| # | Topic ID | Type | Title |
|---|---|---|---|
| 1 | `concept-dr5000-safety` | concept | MC-5000 Safety Architecture |
| 2 | `concept-dr5000-machine-overview` | concept | MC-5000 Machine Overview |
| 3 | `task-dr5000-daily-startup` | task | Daily Startup Procedure |
| 4 | `task-dr5000-tool-change` | task | Tool Change Procedure |
| 5 | `task-dr5000-coolant-maintenance` | task | Coolant System Maintenance |
| 6 | `reference-dr5000-cutting-params` | reference | Cutting Parameters Reference |
| 7 | `reference-dr5000-tool-offsets` | reference | Tool Offset Specifications |
| 8 | `troubleshooting-dr5000-alarms` | troubleshooting | Common Alarm Codes and Remedies |

---

<!-- _class: divider -->

# Phase 1: Content Authoring

---

## Concept Topic 1: Safety Architecture

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN"
  "concept.dtd">
<concept id="concept-dr5000-safety" xml:lang="en-US">
  <title>MC-5000 Safety Architecture</title>
  <shortdesc>Multi-layer safety system protecting operators,
    equipment, and workpieces on the MC-5000 CNC lathe.</shortdesc>
  <conbody>
    <p>The MC-5000 implements a three-layer safety architecture
      conforming to ISO 13849-1 Performance Level d.</p>
    <section><title>Physical Layer</title>
      <p>Full enclosure with polycarbonate windows, interlocked
        doors with magnetic reed sensors (IP67).</p></section>
    <section><title>Electronic Layer</title>
      <p>Redundant safety relay module (SRM-2400) monitors door
        position within 10 ms, initiates Category 1 stop within
        150 ms of guard breach.</p></section>
    <section><title>Procedural Layer</title>
      <p>Lockout/Tagout (LOTO) procedures, daily pre-start
        checklist, weekly E-Stop verification.</p></section>
  </conbody>
</concept>
```

---

## Concept Topic 2: Machine Overview

```xml
<concept id="concept-dr5000-machine-overview" xml:lang="en-US">
  <title>MC-5000 Machine Overview</title>
  <shortdesc>Architecture and capabilities of the MC-5000
    5-axis CNC lathe with live tooling.</shortdesc>
  <conbody>
    <p>The MC-5000 is a high-precision turning center for
      medium-batch production of automotive and aerospace
      components.</p>
    <section><title>Machine Configuration</title>
      <ul>
        <li>5 simultaneous axes: X, Z, Y, C (spindle), B (tool)</li>
        <li>12-station turret with live tooling capability</li>
        <li>Through-tool coolant at 70 bar</li>
        <li>Bar capacity: 80 mm diameter</li>
      </ul>
    </section>
    <section><title>Control System</title>
      <p>Fanuc 31i-B5 with 15" touchscreen, Manual Guide i
        conversational programming, and Ethernet/IP connectivity
        for MES integration.</p></section>
  </conbody>
</concept>
```

---

## Task Topic 1: Daily Startup (Prereqs + Steps 1-5)

```xml
<task id="task-dr5000-daily-startup" xml:lang="en-US">
  <title>Performing the Daily Startup on the MC-5000</title>
  <shortdesc>Start the MC-5000 at the beginning of each
    production shift.</shortdesc>
  <taskbody>
    <prereq>Safety training completed. PPE worn. Area clear.
    </prereq>
    <context>Perform at shift start or after 4+ hours downtime.
    </context>
    <steps>
      <step><cmd>Inspect machine area.</cmd>
        <info>No obstructions, tools, or debris near operator
          station, side door, or bar feeder.</info></step>
      <step><cmd>Verify power and air.</cmd>
        <info>Main disconnect ON. Air gauge: 5.5-6.5 bar.</info>
      </step>
      <step><cmd>Power on main disconnect.</cmd>
        <info>Rotate clockwise. Fans start, indicators amber.
        </info></step>
      <step><cmd>Wait for Fanuc control boot.</cmd>
        <info>~90 seconds. "SYSTEM LOADING" then POSITION
          display appears.</info></step>
      <step><cmd>Release all E-Stop buttons.</cmd>
        <info>Twist clockwise: operator panel, side door,
          remote pendant.</info></step>
```

---

## Task Topic 1: Steps 6-11 + Result

```xml
      <step><cmd>Press Control ON.</cmd>
        <info>Green button. Servo contactor clicks. NOT READY
          alarm clears.</info></step>
      <step><cmd>Reference (home) all axes.</cmd>
        <substeps>
          <substep><cmd>Mode switch to ZERO RETURN.</cmd></substep>
          <substep><cmd>Press +Z. Home lamp on.</cmd></substep>
          <substep><cmd>Repeat for X, Y, C, B.</cmd></substep>
        </substeps>
        <info>Always home Z first to clear tool from workpiece.
        </info></step>
      <step><cmd>Execute warm-up cycle (O9001).</cmd>
        <info>MEM mode, select program, CYCLE START. Spindle
          ramps 500→5000 RPM over 6 minutes.</info>
        <stepresult>Warm-up lamp extinguishes.</stepresult></step>
      <step><cmd>Verify coolant.</cmd>
        <info>Level above MIN. Concentration 7-9% Brix.</info></step>
      <step><cmd>Test coolant through tool (MDI: M08).</cmd></step>
      <step><cmd>First-part verification (if applicable).</cmd>
        <info>One cycle, measure critical dims against IP plan.
        </info></step>
    </steps>
    <result>Machine homed, spindle warm, coolant verified.
      Ready for production.</result>
  </taskbody>
</task>
```

---

## Task Topics 2 & 3 (Outlines)

**Task 2: Tool Change Procedure**

```markdown
Prereq: Machine in E-Stop, spindle stopped, door open
Steps: 1. Identify tool station to change
  2. Clean tool holder taper and turret bore
  3. Release drawbar (foot pedal or MDI M-code)
  4. Remove old tool holder
  5. Insert new tool holder (align drive keys)
  6. Engage drawbar; verify clamping
  7. Measure tool offset (use tool probe or manual)
  8. Enter offset data in register
  9. Test tool change cycle (MDI: Txx M06)
Result: Tool changed, offset set, verified
```

**Task 3: Coolant System Maintenance**

```markdown
Prereq: Machine stopped, PPE including chemical gloves
Steps: 1. Drain coolant tank via drain valve
  2. Clean tank interior (remove sludge and chips)
  3. Clean coolant pump intake screen
  4. Refill with fresh coolant (7-9% semi-synthetic)
  5. Prime pump and verify flow
  6. Record concentration and date in log
Interval: Every 2000 operating hours
```

---

## Reference Topics (2) and Troubleshooting (1)

**Reference 1: Cutting Parameters**

| Material | RPM | Feed (mm/rev) | DOC (mm) | Insert |
|---|---|---|---|---|
| 1045 Steel | 1200-1800 | 0.15-0.30 | 1.0-3.0 | DNMG 4325 |
| 4140 Steel | 800-1200 | 0.10-0.25 | 0.8-2.5 | DNMG 4315 |
| 316 SS | 500-800 | 0.08-0.18 | 0.5-1.5 | DNMG 2025 |
| 6061 Al | 2500-4000 | 0.20-0.40 | 1.5-4.0 | DCGT H10 |

**Reference 2: Tool Offset Specifications**
- OD turning stations 1-4, ID boring 5-8, drilling/threading 9-12
- X offset from gauge line, Z offset from turret face
- Wear offsets in registers 1-64

**Troubleshooting: Common Alarms**
- ALM-7010: Spindle overheat (check coolant, reduce load)
- ALM-7025: Axis following error (check servo, backlash)
- ALM-7033: Tool clamp fail (check drawbar, air pressure)

---

<!-- _class: divider -->

# Phase 2: DITA Map with Keys

---

## Key Definitions

```xml
<map id="bm-dr5000-professional" xml:lang="en-US">
  <title>MC-5000 CNC Lathe Documentation</title>

  <!-- Key definitions for product information -->
  <keydef keys="product-name">
    <topicmeta><keywords>
      <keyword>MC-5000 CNC Lathe</keyword>
    </keywords></topicmeta>
  </keydef>

  <keydef keys="company-name">
    <topicmeta><keywords>
      <keyword>Example Manufacturing Co., Ltd.</keyword>
    </keywords></topicmeta>
  </keydef>

  <keydef keys="control-system">
    <topicmeta><keywords>
      <keyword>Fanuc 31i-B5</keyword>
    </keywords></topicmeta>
  </keydef>

  <keydef keys="coolant-type">
    <topicmeta><keywords>
      <keyword>semi-synthetic emulsion, 7-9% concentration</keyword>
    </keywords></topicmeta>
  </keydef>

  <keydef keys="safety-standard">
    <topicmeta><keywords>
      <keyword>ISO 13849-1 Performance Level d</keyword>
    </keywords></topicmeta>
  </keydef>
```

---

## Keyref Usage in Topics

Instead of hardcoding product names, use keyrefs:

```xml
<!-- BEFORE: Hardcoded -->
<p>The <b>MC-5000 CNC Lathe</b> from
  <b>Example Manufacturing Co., Ltd.</b>
  ships with <b>semi-synthetic emulsion, 7-9% concentration</b>
  as standard coolant.</p>

<!-- AFTER: Keyref-based (single source of truth) -->
<p>The <b><keyword keyref="product-name"/></b> from
  <b><keyword keyref="company-name"/></b>
  ships with <b><keyword keyref="coolant-type"/></b>
  as standard coolant.</p>
```

**Benefits**: Change the product name once in the keydef, and every topic using `keyref="product-name"` updates automatically.

---

## Complete Map Structure with Keys

```xml
  <!-- Chapter organization -->
  <chapter href="concept-dr5000-machine-overview.dita"
    keys="ch-overview">
    <topicref href="concept-dr5000-safety.dita"
      keys="ch-safety"/>
  </chapter>

  <chapter href="task-dr5000-daily-startup.dita"
    keys="ch-startup">
    <topicref href="task-dr5000-tool-change.dita"
      keys="ch-toolchange"/>
    <topicref href="task-dr5000-coolant-maintenance.dita"
      keys="ch-coolant"/>
  </chapter>

  <chapter href="reference-dr5000-cutting-params.dita"
    keys="ch-cutparams">
    <topicref href="reference-dr5000-tool-offsets.dita"
      keys="ch-offsets"/>
  </chapter>

  <chapter href="troubleshooting-dr5000-alarms.dita"
    keys="ch-alarms"/>
</map>
```

---

<!-- _class: divider -->

# Phase 3: Subject Scheme

---

## Controlled Vocabularies

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE subjectScheme PUBLIC
  "-//OASIS//DTD DITA Subject Scheme//EN"
  "subjectScheme.dtd">
<subjectScheme id="ss-dr5000-metadata">

  <!-- Document status -->
  <subjectdef keys="status-values">
    <hasInstance>
      <subjectdef keys="status-draft"/>
      <subjectdef keys="status-review"/>
      <subjectdef keys="status-approved"/>
      <subjectdef keys="status-published"/>
    </hasInstance>
    <enumerationdef>
      <attributedef name="document-status"/>
      <subjectdef keyref="status-values"/>
    </enumerationdef>
  </subjectdef>
```

---

## Subject Scheme: Audience and Content Type

```xml
  <!-- Audience -->
  <subjectdef keys="audience-values">
    <hasInstance>
      <subjectdef keys="aud-operator">
        <navtitle>Machine Operator</navtitle></subjectdef>
      <subjectdef keys="aud-maintenance">
        <navtitle>Maintenance Technician</navtitle></subjectdef>
      <subjectdef keys="aud-engineer">
        <navtitle>Service Engineer</navtitle></subjectdef>
    </hasInstance>
    <enumerationdef>
      <attributedef name="audience"/>
      <subjectdef keyref="audience-values"/>
    </enumerationdef>
  </subjectdef>

  <!-- Content type -->
  <subjectdef keys="contenttype-values">
    <hasInstance>
      <subjectdef keys="ct-concept"/>
      <subjectdef keys="ct-task"/>
      <subjectdef keys="ct-reference"/>
      <subjectdef keys="ct-troubleshooting"/>
    </hasInstance>
    <enumerationdef>
      <attributedef name="content-type"/>
      <subjectdef keyref="contenttype-values"/>
    </enumerationdef>
  </subjectdef>

</subjectScheme>
```

---

<!-- _class: divider -->

# Phase 4: Bookmap & PDF Publishing

---

## Bookmap Structure

```xml
<!DOCTYPE bookmap PUBLIC "-//OASIS//DTD DITA BookMap//EN"
  "bookmap.dtd">
<bookmap id="bm-dr5000-complete" xml:lang="en-US">
  <booktitle>
    <mainbooktitle>MC-5000 CNC Lathe</mainbooktitle>
    <booktitlealt>Operator and Maintenance Manual</booktitlealt>
  </booktitle>

  <bookmeta>
    <author>Your Name</author>
    <publisher><name>Example Manufacturing</name></publisher>
    <copyright><copyryear year="2026"/></copyright>
    <edition>Edition 1.0, June 2026</edition>
  </bookmeta>

  <frontmatter>
    <booklists><toc/><figurelist/><tablelist/></booklists>
    <preface href="concept-dr5000-safety.dita"/>
    <notices href="concept-dr5000-machine-overview.dita"/>
  </frontmatter>

  <chapter href="task-dr5000-daily-startup.dita" keys="ch-startup"/>
  <chapter href="task-dr5000-tool-change.dita" keys="ch-toolchange"/>
  <chapter href="task-dr5000-coolant-maintenance.dita" keys="ch-coolant"/>

  <appendix href="reference-dr5000-cutting-params.dita"/>
  <appendix href="reference-dr5000-tool-offsets.dita"/>
  <appendix href="troubleshooting-dr5000-alarms.dita"/>

  <backmatter>
    <booklists><glossarylist/><indexlist/></booklists>
  </backmatter>
</bookmap>
```

---

## Chemistry CSS (Key Rules)

```css
/* Cover page */
@page cover {
  background-color: #1a3a5c;
  @top-center { content: none; }
}
.cover-title { color: white; font-size: 28pt; }

/* Chapter titles */
h1 { color: #1a3a5c; font-size: 24pt;
     border-bottom: 3pt solid #d42114; }

/* Hazard styling */
.hazard-danger { border-left: 5pt solid #cc0000;
  background: #fff0f0; padding: 10pt; }
.hazard-warning { border-left: 5pt solid #ff8800;
  background: #fff8f0; }

/* Table styling */
table { border-collapse: collapse; }
th { background: #1a3a5c; color: white; padding: 6pt; }
tr:nth-child(even) { background: #f5f5f5; }
```

---

<!-- _class: divider -->

# Phase 5: Conref Content Reuse

---

## Shared Content Library

Create `shared/conref-library.dita`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE topic PUBLIC "-//OASIS//DTD DITA Topic//EN" "topic.dtd">
<topic id="conref-library" xml:lang="en-US">
  <title>Shared Content Library</title>
  <body>

    <!-- Shared warning: Gloves prohibition -->
    <note id="warn-gloves" type="warning">
      <p>Gloves must NEVER be worn near the rotating spindle.
        Entanglement can cause severe injury or death.</p>
    </note>

    <!-- Shared warning: LOTO requirement -->
    <note id="warn-loto" type="danger">
      <p>Perform full Lockout/Tagout (LOTO) before any
        maintenance procedure. Verify zero energy state.</p>
    </note>

    <!-- Shared note: Coolant concentration -->
    <note id="note-coolant-check" type="note">
      <p>Check coolant concentration with refractometer.
        Target: 7-9% Brix. Record reading in maintenance log.
      </p>
    </note>

    <!-- Shared step: E-Stop verification -->
    <step id="step-verify-estop">
      <cmd>Verify all E-Stop buttons are released.</cmd>
      <info>Twist each E-Stop clockwise. The NOT READY alarm
        will clear from the display.</info>
    </step>

  </body>
</topic>
```

---

## Using Conref in Topics

```xml
<!-- Pull in shared warnings instead of rewriting -->
<task id="task-dr5000-tool-change">
  <title>Tool Change Procedure</title>
  <taskbody>
    <prereq>
      <note conref="shared/conref-library.dita#conref-library/warn-loto"/>
    </prereq>
    <steps>
      <step conref="shared/conref-library.dita#conref-library/step-verify-estop"/>
      <step><cmd>Identify the tool station to change.</cmd></step>
      <!-- ... -->
    </steps>
  </taskbody>
</task>
```

**Conref reuse rules:**
- Write once, use everywhere -- update the source, all references update
- Must reference an element of same or more general type
- Can reuse `<note>`, `<step>`, `<p>`, `<section>`, `<table>`, entire `<li>` items
- File paths must be correct relative to the referencing topic

---

<!-- _class: divider -->

# Phase 6: Localization Preparation

---

## Structure for Localization

```
content/
  en-US/                          # Source
    topics/
    bookmaps/
    shared/
      conref-library.dita         # Shared content

  zh-CN/                          # Target (Chinese)
    topics/                       # Will hold translated topics
    bookmaps/
```

**Localization prep steps:**

1. Ensure all topics have `xml:lang="en-US"` on root
2. Extract translatable text to XLIFF
3. Mark non-translatable content with `translate="no"`
4. Prepare translation notes (`<tnotes>`)
5. Generate and validate zh-CN versions

---

## XLIFF Extraction and Translation Notes

```bash
# Extract all topics for translation
dita -i content/en-US/bookmaps/bm-dr5000-complete.ditamap \
     -f xliff \
     -o translation/

# After translation, merge back
dita -i content/en-US/bookmaps/bm-dr5000-complete.ditamap \
     -f xliff2dita \
     -o content/zh-CN/
```

**Translation notes in topics:**

```xml
<task id="task-dr5000-daily-startup" xml:lang="en-US">
  <title translate="yes">Daily Startup Procedure</title>
  <shortdesc translate="yes">Start the MC-5000 at each shift.
  </shortdesc>
  <prolog>
    <metadata>
      <othermeta name="translation-status" content="ready"/>
      <othermeta name="translation-priority" content="high"/>
      <othermeta name="translation-notes"
        content="Verify 'E-Stop' term against approved
        Chinese glossary entry 紧急停止"/>
    </metadata>
  </prolog>
```

---

<!-- _class: divider -->

# Phase 7: GitHub PR Submission

---

## Git Workflow

```bash
# 1. Create feature branch
git checkout -b capstone/dr5000-professional

# 2. Add all deliverables
git add content/en-US/topics/*.dita
git add content/en-US/bookmaps/*.ditamap
git add content/en-US/shared/conref-library.dita
git add governance/ss-dr5000-metadata.subjectscheme
git add templates/css/dr5000-manual.css
git add tools/build-dr5000.sh

# 3. Commit with descriptive message
git commit -m "feat: MC-5000 complete documentation set

- 8 DITA topics (2 concept, 3 task, 2 reference, 1 troubleshooting)
- DITA map with keydefs for product names and shared links
- Subject scheme for metadata enforcement
- Bookmap with frontmatter/chapters/backmatter
- Conref library for shared warnings and notes
- Chemistry CSS for professional PDF output
- Localization-ready with xml:lang and translation notes"

# 4. Push and create PR
git push origin capstone/dr5000-professional
gh pr create --title "MC-5000 Professional Capstone" --body "..."
```

---

## PR Description Template

```markdown
## MC-5000 Professional Capstone Project

**Author:** [Your Name]
**Date:** 2026-06-07

### Deliverables
- [x] 8 DITA topics (2 concept + 3 task + 2 reference + 1 troubleshooting)
- [x] DITA map with 5 keydefs
- [x] Subject scheme with 3 controlled vocabularies
- [x] Bookmap with frontmatter, 3 chapters, appendices, backmatter
- [x] Conref library with 4 reusable elements
- [x] Chemistry CSS (~150 lines)
- [x] Build script for automated PDF generation
- [x] Localization prep (xml:lang, translation notes)

### Validation
- [ ] All topics pass DITA-OT validation: 0 errors
- [ ] All keyrefs resolve correctly
- [ ] PDF builds without errors
- [ ] Conref references resolve in build
- [ ] CSS produces professional output

### Self-Assessment
| Criteria | Score (1-5) |
|---|---|
| Content quality | |
| DITA compliance | |
| Key usage | |
| Subject scheme | |
| Conref reuse | |
| Localization prep | |
```

---

<!-- _class: divider -->

# Phase 8: Evaluation Criteria

---

## Grading Rubric

| Dimension | Weight | Max | Criteria |
|---|---|---|---|
| **Content Quality** | 20% | 20 | 8 topics complete, accurate, audience-appropriate |
| **DITA Compliance** | 20% | 20 | Correct DTDs, valid XML, proper element usage |
| **DITA Map & Keys** | 15% | 15 | keydefs present, keyrefs used in topics, correct map structure |
| **Subject Scheme** | 10% | 10 | 3+ controlled vocabularies, valid scheme syntax |
| **Conref Reuse** | 10% | 10 | Shared library, proper conref usage in at least 3 topics |
| **Bookmap & PDF** | 10% | 10 | Professional bookmap, CSS produces clean PDF |
| **Localization Prep** | 10% | 10 | xml:lang attributes, translation notes, XLIFF-ready |
| **GitHub PR** | 5% | 5 | Clean branch, descriptive commit, complete PR template |
| **TOTAL** | 100% | 100 | |

---

## Passing Criteria

| Score | Grade | Meaning |
|---|---|---|
| **90-100** | Excellent | Production-ready. Demonstrates Professional mastery. |
| **80-89** | Proficient | Passing. Strong DITA skills demonstrated. |
| **70-79** | Developing | Passing with notes. Some areas need refinement. |
| **Below 70** | Revise | Does not yet demonstrate Professional competency. |

**Target score: 85+**

---

## Common Pitfalls

| Pitfall | Prevention |
|---|---|
| **Missing keyref usage** | At least 3 topics must use keyrefs, not hardcoded text |
| **Conref path errors** | Verify relative paths from each referencing topic |
| **Invalid subject scheme** | Validate against subjectScheme.dtd |
| **No translation notes** | At least 5 topics must have `<othermeta name="translation-notes">` |
| **DTD mismatches** | Double-check DOCTYPE for each topic type |
| **Missing xml:lang** | Every topic root must have `xml:lang="en-US"` |
| **Keyref not resolving** | Build map and check DITA-OT log for [DOTJ036W] |

---

<!-- _class: keypoint -->

## Key Takeaways

1. **Professional level = real documentation**: 8 topics, 5 supporting files, complete bookmap
2. **Keys are infrastructure**: Centralize product names, shared values, and link targets
3. **Subject scheme enforces quality**: Controlled vocabularies prevent metadata drift
4. **Conref eliminates duplication**: Shared warnings and notes written once, referenced everywhere
5. **Localization starts at authoring**: xml:lang, translation notes, and clean separation from day one
6. **Bookmaps produce professional PDFs**: Frontmatter, chapters, backmatter replicate traditional manuals
7. **GitHub PR is your submission**: Clean commits, descriptive messages, complete checklist
8. **Passing score is 80/100** across 8 dimensions

---

## Resources

- **Templates**: `D:/OpenSources/dita-manufacturing-framework/templates/`
- **DITA 1.3 Spec**: docs.oasis-open.org/dita/dita/v1.3/
- **DITA-OT**: www.dita-ot.org
- **Subject Scheme Guide**: See Module 03 materials
- **Conref Reference**: See Module 02 materials
