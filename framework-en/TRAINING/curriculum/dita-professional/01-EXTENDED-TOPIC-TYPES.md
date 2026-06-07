---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Extended Topic Types in DITA
## Module 1 — DITA Professional (进阶)

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | DITA Topic Type Landscape | 3-5 |
| 2 | The 3-Tier Adoption Model | 6-7 |
| 3 | Troubleshooting Topic Deep Dive | 8-12 |
| 4 | Glossary Entry Topic | 13-15 |
| 5 | Hazard Statement Topic | 16-18 |
| 6 | Task Requirements Domain | 19 |
| 7 | Learning Object Topic | 20-21 |
| 8 | Bookmap & Subject Scheme | 22 |
| 9 | Decision Tree & Migration | 23-25 |

---

## DITA 1.3 Topic Type Overview

DITA 1.3 defines over **25 topic types** across multiple specializations. The manufacturing framework adopts **13 types** based on real-world requirements.

| Category | Count | Adopted |
|----------|-------|---------|
| Base | 3 | 3 (topic, concept, task, reference) |
| Technical Content | 6 | 4 |
| Learning & Training | 5 | 1 |
| Machinery | 5 | 3 |
| Classification | 3 | 2 |

---

## The 13 Adopted Topic Types

| # | Topic Type | DOCTYPE | Domain |
|---|-----------|---------|--------|
| 1 | Topic | topic | Base |
| 2 | Concept | concept | Base |
| 3 | Task | task | Base (strict) |
| 4 | Reference | reference | Base |
| 5 | Troubleshooting | troubleshooting | Tech Content |
| 6 | Glossary Entry | glossentry | Tech Content |
| 7 | Hazard Statement | reference + hi-d | Machinery |
| 8 | Task Requirements | task + pr-d | Machinery |
| 9 | Learning Object | learningObject | L&T |
| 10 | Bookmap | bookmap | Classification |
| 11 | Subject Scheme | subjectScheme | Classification |
| 12 | Ditamap | map | Classification |
| 13 | Machine Industry | reference + mi-d | Machinery |

---

<!-- _class: divider -->

# The 3-Tier Adoption Model

**80% Core | 15% Specialized | 5% Advanced**

---

## Tier 1: Core Types (80% of Content)

These five types cover the vast majority of manufacturing documentation needs.

| Type | Usage | Example |
|------|-------|---------|
| **Topic** | General information, overviews | System architecture description |
| **Concept** | Explanations, principles | How proportional valves work |
| **Task** | Step-by-step procedures | Calibrating a pressure sensor |
| **Reference** | Tables, specifications, parts lists | Torque specification table |
| **Ditamap** | Content organization | Machine manual structure |

> Core types are mandatory for all contributors. Master these first.

---

## Tier 2: Specialized Types (15% of Content)

Used by technical writers and subject matter experts.

| Type | Primary Use |
|------|-------------|
| **Troubleshooting** | Diagnostic procedures, alarm response |
| **Glossary Entry** | Terminology management, acronym definitions |
| **Hazard Statement** | Safety warnings, risk communication |
| **Task Requirements** | Structured prerequisites for tasks |

> Requires Domain module loading in DITA-OT and authoring tools.

---

## Tier 3: Advanced Types (5% of Content)

Used by information architects and localization leads.

| Type | Primary Use |
|------|-------------|
| **Learning Object** | Training content, LMS integration |
| **Bookmap** | Print/PDF output, formal publications |
| **Subject Scheme** | Controlled vocabulary governance |
| **Machine Industry** | Machinery-specific reference data |

> Requires specialized knowledge of DITA architecture and publishing pipelines.

---

<!-- _class: divider -->

# Troubleshooting Topic
## Theory and Real-World Examples

---

## Troubleshooting Topic: Structure

The troubleshooting DITA topic uses a **cause-remedy** diagnostic pattern.

```
troubleshooting
  +-- title
  +-- shortdesc
  +-- troubleshootingbody
       +-- condition    (symptom/trigger)
       +-- cause        1..*
       |    +-- remedy   1..1
       +-- cause
            +-- remedy
```

**Rule**: One topic per alarm code. Causes ordered by likelihood (%). Each cause maps to exactly one remedy sequence.

---

## Troubleshooting DOCTYPE Declaration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE troubleshooting PUBLIC
  "-//OASIS//DTD DITA Troubleshooting//EN"
  "troubleshooting.dtd">
<troubleshooting id="alarm-700016"
    xml:lang="en-US">
  <title>Alarm 700016: Coolant Pressure Low</title>
  <shortdesc>Diagnostic procedure for low coolant
    pressure alarm on DR-series machines.</shortdesc>
  <troubleshootingbody>
    <condition>
      <p>Alarm code <b>700016</b> appears on
      the HMI panel. Machine enters safe state.
      Coolant pump stops.</p>
    </condition>
    ...
  </troubleshootingbody>
</troubleshooting>
```

---

## Real Example: Alarm 700016 — Cause 1 (80% Likelihood)

```xml
    <cause>
      <title>Coolant level below minimum (80%)</title>
      <p>The most common cause. Coolant reservoir
      level has dropped below the MIN mark.</p>
      <remedy>
        <steps>
          <step><cmd>Press <uicontrol>E-Stop</uicontrol>
          and lock out per LOTO procedure GLOSS-LOTO.</cmd>
          </step>
          <step><cmd>Open the coolant reservoir cap
          on the right side of the machine.</cmd>
          </step>
          <step><cmd>Check the sight glass.
          If level is below MIN mark, refill with
          DURA-COOL 4800 synthetic coolant.</cmd>
            <stepresult>Level shows between MIN and
            MAX on sight glass.</stepresult>
          </step>
          <step><cmd>Release E-Stop, reset alarm on
          HMI, and run test cycle T-047.</cmd>
            <stepresult>Pump pressure reads
            4.2–5.0 bar on gauge.</stepresult>
          </step>
        </steps>
      </remedy>
    </cause>
```

---

## Alarm 700016 — Causes 2 and 3

**Cause 2 (15% likelihood):**

```xml
    <cause>
      <title>Clogged coolant filter (15%)</title>
      <p>Filter element PN MC-5000-F-047 is
      saturated with swarf.</p>
      <remedy>
        <steps>
          <step><cmd>LOTO the machine.</cmd></step>
          <step><cmd>Remove filter housing
          (4x M8 bolts).</cmd></step>
          <step><cmd>Replace filter element
          PN MC-5000-F-047.</cmd>
            <stepresult>No visible debris on
            new element after test cycle.</stepresult>
          </step>
        </steps>
      </remedy>
    </cause>
```

---

## Alarm 700016 — Cause 3 (Edge Case)

**Cause 3 (5% likelihood):**

```xml
    <cause>
      <title>Failed pressure transducer (5%)</title>
      <p>Rare: transducer PN SEN-420-B gives
      false reading or has electrically failed.</p>
      <remedy>
        <steps>
          <step><cmd>LOTO the machine.</cmd></step>
          <step><cmd>Disconnect transducer at
          connector J17 on I/O board.</cmd></step>
          <step><cmd>Measure resistance across
          pins 1–3. Expected: 120–180 Ω at 20°C.
          If open circuit or short, replace
          transducer PN SEN-420-B.</cmd>
            <stepresult>Resistance within
            specification.</stepresult>
          </step>
          <step><cmd>Reconnect, clear alarm,
          run calibration C-012.</cmd>
            <stepresult>Transducer reading
            matches gauge within ±0.1 bar.</stepresult>
          </step>
        </steps>
      </remedy>
    </cause>
```

---

## Troubleshooting Best Practices

| Practice | Rationale |
|----------|-----------|
| One topic per alarm code | Enables precise content reuse and translation memory |
| Causes ordered by likelihood | Technician efficiency: most probable first |
| Each cause has one remedy | Clean separation; each remedy reusable independently |
| LOTO always first step | Safety compliance; ISO 12100 |
| cmd + stepresult pattern | Verifiable outcomes; training validation |
| Use uicontrol for panel elements | Semantic markup for HMI components |
| Reference PN in remedy steps | Direct part lookup without leaving procedure |

---

<!-- _class: exercise -->

## Exercise 1: Troubleshooting Topic

**Task**: Create a troubleshooting topic for Alarm 800023 — "Spindle Temperature High."

**Requirements**:
1. Use the troubleshooting DOCTYPE
2. Define the condition (symptom)
3. Identify 2-3 causes ordered by likelihood (percentages)
4. Each cause must have one remedy with `cmd + stepresult` pattern
5. First step of each remedy must reference LOTO procedure
6. Include at least one part number reference
7. Include one `uicontrol` element

**Time**: 15 minutes

---

<!-- _class: divider -->

# Glossary Entry Topic
## Terminology Governance for Manufacturing

---

## Why Glossary Entry Matters

Manufacturing documentation contains hundreds of domain-specific terms. Inconsistent definitions lead to:

- **Safety risks**: Different interpretations of "LOTO" or "confined space"
- **Translation errors**: Translators guess when definitions are unclear
- **Training gaps**: New technicians learn inconsistent terminology
- **Compliance failures**: Regulators expect controlled terminology

**Solution**: One `glossentry` topic per term, with fine-grained elements for definition, alternatives, and relationships.

---

## Glossary Entry Structure

```
glossentry
  +-- glossterm          (the term being defined)
  +-- glossdef           (the primary definition)
  +-- glossBody
       +-- glossAlt       (alternative forms 0..*)
       |    +-- glossAlternateForm
       |    +-- glossSynonym
       |    +-- glossAbbreviation
       |    +-- glossShortForm
       +-- glossRelated    (related terms 0..*)
            +-- glossRelatedTerm
```

---

## Glossary Entry DOCTYPE and Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE glossentry PUBLIC
  "-//OASIS//DTD DITA Glossary//EN"
  "glossary.dtd">
<glossentry id="gloss-loto" xml:lang="en-US">
  <glossterm>Lockout/Tagout</glossterm>
  <glossdef>A safety procedure used to ensure
    that dangerous machines are properly shut off
    and not able to be started up again prior to
    the completion of maintenance or repair work.
    Required by ISO 12100:2010 Section 6.2.11.4
    and OSHA 29 CFR 1910.147.
  </glossdef>
  <glossBody>
    <glossAlt>
      <glossAbbreviation>LOTO</glossAbbreviation>
      <glossShortForm>lockout</glossShortForm>
    </glossAlt>
    <glossRelated>
      <glossRelatedTerm keyref="gloss-zero-energy"/>
      <glossRelatedTerm keyref="gloss-energy-isolation"/>
      <glossRelatedTerm keyref="gloss-osha-1910-147"/>
    </glossRelated>
  </glossBody>
</glossentry>
```

---

## Glossary Entry: Extended with Acronym Pronunciation

```xml
<glossentry id="gloss-scada" xml:lang="en-US">
  <glossterm>SCADA</glossterm>
  <glossdef>Supervisory Control and Data
    Acquisition — a control system architecture
    using computers, networked data communications,
    and graphical user interfaces for high-level
    process supervisory management.
  </glossdef>
  <glossBody>
    <glossAlt>
      <glossFullForm>Supervisory Control and
        Data Acquisition</glossFullForm>
      <glossAbbreviation>SCADA</glossAbbreviation>
    </glossAlt>
    <glossRelated>
      <glossRelatedTerm keyref="gloss-plc"/>
      <glossRelatedTerm keyref="gloss-hmi"/>
      <glossRelatedTerm keyref="gloss-mes"/>
      <glossRelatedTerm keyref="gloss-iot-gateway"/>
    </glossRelated>
  </glossBody>
</glossentry>
```

---

## Glossary Governance in the Framework

| Rule | Description |
|------|-------------|
| One term per file | Enables fine-grained translation memory and reuse |
| Unique ID pattern | `gloss-{abbreviation-lowercase}` e.g., `gloss-loto` |
| ISO reference in glossdef | Traceable to international standards |
| Related terms via keyref | Builds semantic network; auto-link on publish |
| Language variants via xml:lang | `gloss-loto.xml` in en/, zh-CN/, de-DE/ |
| No inline glossary | Always use glossentry; never define in prose |

> **Migration note**: Framework v1.0 used markdown-based glossary files. v1.1 requires all terms migrated to `glossentry` DITA topics.

---

<!-- _class: exercise -->

## Exercise 2: Glossary Entry

**Task**: Create a glossary entry for "Zero Energy State."

**Requirements**:
1. Use the glossentry DOCTYPE
2. Full definition referencing ISO 12100
3. Include abbreviation "ZES" in glossAlt
4. Link to 2-3 related terms via keyref
5. Use proper ID convention

**Time**: 10 minutes

---

<!-- _class: divider -->

# Hazard Statement Topic
## Safety Communication in DITA

---

## Why Hazard Statements Need Structure

Prose warnings in v1.0 had three critical problems:

1. **Inconsistent formatting**: Bold, colors, icons varied per author
2. **Missing risk information**: Authors forgot consequence/how-to-avoid
3. **Translation errors**: Machine translation mangled warning text

**Solution**: DITA hazard statement with the `hi-d` (hazard information domain) provides a machine-readable, structured format that ensures all three safety components are always present.

---

## Hazard Statement Structure

```
topic (reference type + hi-d domain)
  +-- title
  +-- prolog (metadata: severity, ISO reference)
  +-- body
       +-- hazardstatement (type="danger|warning|caution|notice")
            +-- messagepanel
                 +-- typeofhazard
                 +-- consequence
                 +-- howtoavoid
```

**Severity levels** (from ISO 12100 / ANSI Z535):

| Level | Signal Word | Risk | Color |
|-------|-------------|------|-------|
| danger | DANGER | Death or severe injury | Red |
| warning | WARNING | Possible death or severe injury | Orange |
| caution | CAUTION | Minor or moderate injury | Yellow |
| notice | NOTICE | Property damage only | Blue |

---

## Hazard Statement: Real Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE reference PUBLIC
  "-//OASIS//DTD DITA Reference//EN" "reference.dtd">
<reference id="hazard-flying-chips"
    xml:lang="en-US"
    xmlns:hi-d="http://www.oasis-open.org/...">
  <title>HAZARD: Flying Chips During Machining</title>
  <prolog>
    <metadata>
      <category>safety</category>
      <data name="severity" value="danger"/>
      <data name="iso-reference" value="ISO 12100:2010"/>
      <data name="risk-level" value="R1"/>
    </metadata>
  </prolog>
  <refbody>
    <hazardstatement type="danger">
      <messagepanel>
        <typeofhazard>Flying chips and debris
        ejected at high velocity during CNC
        machining operations.</typeofhazard>
        <consequence>Can cause eye injury,
        blindness, lacerations, or embedded
        foreign bodies requiring surgical
        removal.</consequence>
        <howtoavoid>Always wear ANSI Z87.1
        impact-rated safety goggles with side
        shields. Verify machine guards are in
        place before operation. Post warning
        signs in chip hazard zone (3m radius
        from spindle).</howtoavoid>
      </messagepanel>
    </hazardstatement>
  </refbody>
</reference>
```

---

## ISO Risk Assessment Table

Each hazard maps to a severity-probability matrix per ISO 12100.

| Hazard ID | Type | Severity (S) | Probability (P) | Risk Level |
|-----------|------|-------------|-----------------|------------|
| HAZ-001 | Flying chips | S3 (severe) | P3 (high) | R1 (intolerable) |
| HAZ-002 | Coolant mist | S2 (moderate) | P4 (very high) | R2 (significant) |
| HAZ-003 | Electrical arc | S4 (fatal) | P1 (low) | R2 (significant) |
| HAZ-004 | Pinch point | S2 (moderate) | P2 (medium) | R3 (tolerable) |

**Formula**: R = f(S, P). Risk reduction hierarchy: eliminate > guard > warn > PPE > train.

---

## Migration: Prose Warnings to Hazard Statements

**v1.0 (prose warning — will be rejected by v1.1 CI/CD):**

```markdown
> **Warning**: Watch out for sharp edges on the
> machine frame. You could cut yourself.
```

**v1.1 (structured hazard statement — compliant):**

```xml
<hazardstatement type="warning">
  <messagepanel>
    <typeofhazard>Sharp edges on sheet metal
    frame panels (edges not deburred during
    fabrication).</typeofhazard>
    <consequence>Laceration injury to hands
    or forearms. May require sutures.</consequence>
    <howtoavoid>Wear ANSI/ISEA 105 cut-resistant
    gloves (Level A4 minimum). Inspect edges
    before contact. Report un-deburred edges
    via maintenance work order.</howtoavoid>
  </messagepanel>
</hazardstatement>
```

---

<!-- _class: exercise -->

## Exercise 3: Hazard Statement

**Task**: Create a hazard statement for coolant mist inhalation.

**Requirements**:
1. Use reference DOCTYPE with hi-d domain
2. Assign appropriate severity level (warning or caution)
3. Complete all three messagepanel sub-elements
4. Include prolog with metadata (severity, ISO reference, risk level)
5. Reference specific PPE requirements
6. Create a mini risk assessment: severity S2, probability P4

**Time**: 12 minutes

---

<!-- _class: divider -->

# Task Requirements & Learning Object

---

## Task Requirements (pr-d Domain)

The `pr-d` (product requirements domain) adds structured prerequisite blocks to task topics.

```xml
<task id="task-coolant-replace" xml:lang="en-US">
  <title>Replacing the Coolant</title>
  <taskrequirements>
    <reqcond>
      <personnel>Maintenance Technician,
        Level 2 certified</personnel>
      <equipment>
        <ul>
          <li>DURA-COOL 4800 (20L drum)</li>
          <li>PPE: gloves, goggles, apron</li>
          <li>Drain pan (40L capacity)</li>
        </ul>
      </equipment>
      <environment>Well-ventilated area.
        Spill kit within 5m.</environment>
      <safety>LOTO per GLOSS-LOTO. Review
        HAZARD-CHEMICAL-BURN.</safety>
    </reqcond>
  </taskrequirements>
  <taskbody>...</taskbody>
</task>
```

---

## Learning Object Topic (SCORM/LMS Integration)

The `learningObject` type structures training content for Learning Management Systems.

```
learningObject
  +-- learningOverview    (objectives, prerequisites, duration)
  +-- learningContent     (instructional material)
  +-- learningSummary     (review, assessment, next steps)
```

**Use cases in manufacturing:**
- Operator certification modules
- New-hire safety training
- Machine-specific procedure training
- Annual refresher courses

---

## Learning Object: Example

```xml
<learningObject id="lo-safety-loto" xml:lang="en-US">
  <title>LOTO Safety Certification</title>
  <learningOverview>
    <lcObjective>Identify all 7 energy sources
    on a MC-5000 machine. Demonstrate correct
    LOTO sequence in under 3 minutes.</lcObjective>
    <lcDuration>45 minutes</lcDuration>
    <lcPrereqs>General Safety Orientation (LO-SAFE-101)</lcPrereqs>
  </learningOverview>
  <learningContent>
    <section>
      <title>Energy Source Identification</title>
      <p>DR-series machines have 7 energy sources:
      electrical (480V 3-phase), pneumatic (6 bar),
      hydraulic (210 bar), coolant (5 bar), thermal
      (spindle), kinetic (rotating mass), and
      gravitational (Z-axis).</p>
    </section>
  </learningContent>
  <learningSummary>
    <lcAssessment>Practical demonstration with
    certified evaluator. Passing score: 100%.</lcAssessment>
  </learningSummary>
</learningObject>
```

---

## Bookmap: Print/PDF Output

Bookmap specializes a ditamap for print publication with formal front/back matter.

```
bookmap
  +-- booktitle
  +-- bookmeta (author, publisher, copyright, edition)
  +-- frontmatter (TOC, figures, tables, abbreviations, preface)
  +-- chapter (1..*)
  +-- backmatter (appendix, glossary, index, amendments)
```

**When to use**: Operator's Manual (PDF/print), Installation Guide, Service Manual, Training Workbook

---

## Subject Scheme: Vocabulary Governance

Subject Scheme enforces controlled values for metadata attributes.

```
subjectScheme
  +-- subjectdef (for each controlled attribute)
       +-- hasInstance block (allowed values)
            +-- subjectdef (each value)
       +-- enumerationdef block (binding to attribute)
```

**Framework controls**: audience (6), content-type (8), document-status (5), product (4), hazard-severity (4), skill-level (5)

> Covered in detail in Module 3: Subject Scheme Governance.

---

## Decision Tree: Choosing the Right Topic Type

```
Content contains a procedure with steps?
  YES → Task (general) or Troubleshooting (diagnostic)?
         Diagnostic/conditions? → Troubleshooting
         Structured prereqs needed? → Task + pr-d domain
         Standard procedure? → Task (strict)

Content defines a term?
  YES → Glossary Entry
  NO  → Is it safety-related?
         YES → Hazard Statement (reference + hi-d)
         NO  → Explains a concept? → Concept
               Lists specifications/tables? → Reference
               Training content? → Learning Object
               Book/publication? → Bookmap
               Vocabulary rules? → Subject Scheme
               General info? → Topic
```

---

## Migration Path: v1.0 to v1.1

| v1.0 Approach | v1.1 Target | Migration Task |
|---------------|-------------|----------------|
| Markdown glossary files | glossentry topics | Extract terms, create one file per term, add ISO refs |
| Prose warnings in tasks | hazardstatement topics | Extract each warning, classify severity, add messagepanel |
| Ad-hoc prerequisites | taskrequirements block | Move personnel/equipment/environment/safety |
| No training content type | learningObject topics | Wrap existing training in LO structure |
| Free-text severity labels | Subject Scheme controlled values | Run validation, fix all non-conforming values |
| Uncontrolled xml:lang | Required xml:lang per topic | Script: add xml:lang based on directory |

---

<!-- _class: keypoint -->

## Key Takeaways

1. **13 types across 3 tiers**: Core (5) covers 80% of content; Specialized (4) and Advanced (4) cover the rest
2. **Troubleshooting**: One topic per alarm, causes ordered by likelihood, cmd+stepresult pattern
3. **Glossary Entry**: One term per file, ISO references in glossdef, related terms via keyref
4. **Hazard Statement**: Structured typeofhazard+consequence+howtoavoid; severity from danger to notice
5. **Task Requirements**: pr-d domain adds structured personnel/equipment/environment/safety blocks
6. **Learning Object**: 3-part structure (overview→content→summary) for LMS integration
7. **Decision tree** and **migration path** from v1.0 prose conventions to v1.1 structured types
