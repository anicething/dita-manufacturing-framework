---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# DITA Specialization
## Extending DITA for Manufacturing Domains

**DITA Expert Level - Module 1**
Example Manufacturing Co., Ltd.
v1.1 | 2026-06-07

---

<!-- _class: divider -->

# Agenda
1. What is DITA Specialization?
2. Specialization Hierarchy
3. Domain vs Topic Type Specialization
4. The Specialization Mechanism
5. The Class Attribute
6. Manufacturing Domain Example
7. Integration into DTD Shells
8. Common Pitfalls
9. Tools and Migration
10. Hands-On Exercise

---

# What Is DITA Specialization?

**Definition:** The mechanism for extending base DITA topic types to create domain-specific vocabulary.

In manufacturing, specialization allows you to define elements that match your exact domain:

- Machine maintenance intervals
- Safety hazard classifications
- ISO compliance markup
- Equipment calibration specifications

> Specialization = DITA's answer to "one size doesn't fit all"

---

# Why Specialize? Five Drivers

| Driver | Manufacturing Example |
|--------|----------------------|
| **Semantic Markup** | `<torqueSpec>` instead of generic `<data>` |
| **Validation** | Enforce that every maintenance procedure has a safety warning |
| **Custom Processing** | Render hazard statements with red warning boxes in PDF |
| **Industry Standards** | Machine Industry domain (hi-d), EEP domain (pr-d) |
| **Team Efficiency** | Writers use familiar industry terminology |

---

# The Specialization Philosophy

> Specialization borrows from object-oriented programming: each new type **inherits** from a base type and can **extend** with new elements.

```
Base topic  ──→  Concept, Task, Reference
Task       ──→  MachineryTask (machine industry)
Reference  ──→  HazardStatement (hazard domain)
```

Key principles:
- **Specialize, don't fork** -- always maintain the inheritance chain
- **Add, don't remove** -- new types add elements, never delete base ones
- **Preserve processing** -- a specialized topic must be processable by any DITA-OT

---

# Specialization Hierarchy

```
                    ┌─────────────┐
                    │   topic     │  (root)
                    └──────┬──────┘
            ┌──────────────┼──────────────┐
      ┌─────┴─────┐  ┌─────┴─────┐  ┌─────┴─────┐
      │  concept  │  │    task   │  │ reference │
      └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
            │               │               │
   ┌────────┴────────┐  ┌───┴───┐  ┌────────┴────────┐
   │ troubleshooting │  │  ...  │  │   glossary      │
   └─────────────────┘  └───────┘  │ hazardStatement │
                                   └─────────────────┘
```

The **DITA 1.3 Technical Content** package includes pre-built specializations for the machine industry.

---

# Two Types of Specialization

### Type 1: Topic Type Specialization
Creates an entirely new topic type that extends a base type.

```
<concept>  ──specializes──▶  <troubleshooting>
<reference> ──specializes──▶  <hazardStatement>
```

### Type 2: Domain Specialization
Adds new elements that can be used within existing topic types.

```
(topic hi-d)   →  adds <hazardstatement>, <typeofhazard>, <howtoavoid>
(topic pr-d)   →  adds <prereq>, <personnel>, <safety>, <reqconds>
```

> Most manufacturing frameworks use both: topic types for structural needs, domains for vocabulary needs.

---

# Domain Specialization: The Machine Industry Domain

The **hi-d** (hazard statement domain) is a standard DITA domain specifically for the manufacturing/machinery industries.

```xml
<hazardstatement type="danger">
  <messagepanel>
    <typeofhazard>Rotating spindle</typeofhazard>
    <consequence>Severe entanglement, crushing injury, or death</consequence>
    <howtoavoid>
      Lock out and tag out power before maintenance.
      Never wear loose clothing near operating spindle.
    </howtoavoid>
  </messagepanel>
  <hazardsymbol href="iso-warning-entanglement.svg"/>
</hazardstatement>
```

---

# Domain Specialization: Task Requirements Domain

The **pr-d** (programming requirements domain) is repurposed in manufacturing for task prerequisite management:

```xml
<task id="t_calibrate_spindle">
  <title>Calibrate the MC-5000 Spindle Runout</title>
  <taskbody>
    <prereq domain="(topic pr-d)">
      <personnel>
        <role>CNC Service Technician Level 2</role>
        <certification>ISO 9001 Calibration Certified</certification>
      </personnel>
      <safety>
        <noqual>Must use dial indicator with magnetic base (0.001mm resolution)</noqual>
      </safety>
      <reqconds>
        <reqcond>Spindle temperature: 20 +/- 2 degrees Celsius</reqcond>
        <reqcond>Machine must be warmed up for 30 minutes minimum</reqcond>
      </reqconds>
    </prereq>
  </taskbody>
</task>
```

---

# The Four Files of a Specialization

Every specialization module consists of exactly four files:

| File Extension | Purpose | Contains |
|---------------|---------|----------|
| `.mod` | Module declarations | Element/attribute DTD declarations |
| `.ent` | Entities | Domain entity definitions |
| `.dtd` | DTD Shell | Integrates modules into a working DTD |
| `.xml` (catalog) | Catalog | Maps PUBLIC/SYSTEM IDs to file paths |

Plus a `.dita` topic template for each new topic type specialization.

---

# File 1: The .mod File (Module)

The `.mod` file declares the element entities and content models:

```xml
<!-- maintenanceDomain.mod -->
<!-- Manufacturing Maintenance Domain Module -->

<!-- ============================================ -->
<!-- ELEMENT NAME ENTITIES -->
<!-- ============================================ -->
<!ENTITY % maintenanceInterval
  "maintenanceInterval"
>
<!ENTITY % lubricationPoint
  "lubricationPoint"
>
<!ENTITY % torqueSpec
  "torqueSpec"
>

<!-- ============================================ -->
<!-- ELEMENT DECLARATIONS -->
<!-- ============================================ -->
<!ELEMENT maintenanceInterval
  ((%shortdesc;)?, (%lubricationPoint;)*, (%torqueSpec;)*)
>
<!ATTLIST maintenanceInterval
  %id-atts;
  period CDATA #REQUIRED
  severity (critical|routine|conditional) #REQUIRED
  %localization-atts;
>
```

---

# File 1: .mod File (continued)

Element declarations for domain elements that nest within existing types:

```xml
<!ELEMENT lubricationPoint
  ((%shortdesc;)?, (%note;)*)
>
<!ATTLIST lubricationPoint
  %id-atts;
  component CDATA #REQUIRED
  lubricant CDATA #IMPLIED
  method (manual|automatic|zerk|reservoir) #REQUIRED
  %localization-atts;
>

<!ELEMENT torqueSpec
  (%ph;)*
>
<!ATTLIST torqueSpec
  %id-atts;
  value CDATA #REQUIRED
  unit (Nm|lbf-ft|kgf-cm) #REQUIRED
  tolerance CDATA #IMPLIED
  tool CDATA #IMPLIED
  %localization-atts;
>
```

---

# File 2: The .ent File (Entity Definitions)

The `.ent` file defines the domain entity that maps to the class attribute:

```xml
<!-- maintenanceDomain.ent -->
<!-- Manufacturing Maintenance Domain Entities -->

<!-- Domain entity declaration -->
<!ENTITY % maint-d-dec
  PUBLIC "-//ExampleCorp//ENTITIES DITA Maintenance Domain//EN"
         "maintenanceDomain.ent"
>
%maint-d-dec;

<!-- Class attribute inclusion -->
<!ENTITY % maint-d-def
  "<!ENTITY % maintenanceInterval &#34;
    %maintenanceInterval;
    ...
   &#34;>"
>
%maint-d-def;
```

The `.ent` file bridges the gap between element declarations and the class attribute system.

---

# File 3: The DTD Shell (.dtd)

The `.dtd` shell integrates base DITA modules with specialized modules:

```xml
<!-- mfg-task.dtd -->
<!-- Manufacturing Task DTD Shell -->

<!-- 1. Topic entity integrations -->
<!ENTITY % topic-type
  PUBLIC "-//OASIS//ELEMENTS DITA Topic//EN"
         "topic.mod"
>
%topic-type;

<!-- 2. Domain entity integrations -->
<!ENTITY % hi-d-dec
  PUBLIC "-//OASIS//ENTITIES DITA Hazard Statement Domain//EN"
         "hazardStatementDomain.ent"
>
%hi-d-dec;

<!-- 3. Custom domain -->
<!ENTITY % maint-d-dec
  PUBLIC "-//ExampleCorp//ENTITIES DITA Maintenance Domain//EN"
         "maintenanceDomain.ent"
>
%maint-d-dec;
```

---

# File 3: DTD Shell (continued)

Module inclusions define what the shell actually redefines:

```xml
<!-- 4. Module inclusions -->
<!ENTITY % hazardstatement
  PUBLIC "-//OASIS//ELEMENTS DITA Hazard Statement//EN"
         "hazardStatement.mod"
>
%hazardstatement;

<!ENTITY % maintenanceInterval
  PUBLIC "-//ExampleCorp//ELEMENTS DITA Maintenance//EN"
         "maintenanceDomain.mod"
>
%maintenanceInterval;

<!-- 5. Redefine infotype entity to include domains -->
<!ENTITY % task-info-types
  "%task-info-types;
   %maintenanceInterval;
   %hazardstatement;"
>
```

---

# File 4: Catalog.xml

The `catalog.xml` maps PUBLIC IDs (used in DOCTYPE) to SYSTEM IDs (file paths):

```xml
<catalog xmlns="urn:oasis:names:tc:entity:xmlns:xml:catalog">

  <!-- OASIS Standard DTD -->
  <public publicId="-//OASIS//DTD DITA Task//EN"
          uri="dtd/technicalContent/dtd/task.dtd"/>

  <!-- Manufacturing Task DTD Shell -->
  <public publicId="-//ExampleCorp//DTD DITA Manufacturing Task//EN"
          uri="dtd/mfg-task.dtd"/>

  <!-- Hazard Statement Domain -->
  <public publicId="-//OASIS//ELEMENTS DITA Hazard Statement//EN"
          uri="dtd/technicalContent/dtd/hazardStatement.mod"/>

  <!-- Custom Maintenance Domain -->
  <public publicId="-//ExampleCorp//ELEMENTS DITA Maintenance//EN"
          uri="dtd/custom/maintenanceDomain.mod"/>

  <!-- Custom Maintenance Entities -->
  <public publicId="-//ExampleCorp//ENTITIES DITA Maintenance Domain//EN"
          uri="dtd/custom/maintenanceDomain.ent"/>

</catalog>
```

---

# The @class Attribute: Backbone of Specialization

Every DITA element carries a `@class` attribute that records its full inheritance chain:

```xml
<!-- Base DITA topic paragraph -->
<p class="- topic/p ">This is a paragraph.</p>

<!-- Machine Industry hazard statement -->
<hazardstatement class="- topic/p        topic/hazardstatement ">

<!-- Concept topic body -->
<conbody class="- topic/body   concept/conbody ">

<!-- Specialized maintenance task body -->
<mfgTaskBody class="- topic/body   task/taskbody   mfgTask/mfgTaskBody ">
```

**Rule:** The `@class` value must be a space-separated list starting with `- topic/root-type` followed by each intermediate specialization.

---

# The @class Attribute Rules

| Rule | Correct | Incorrect |
|------|---------|-----------|
| Starts with `-` | `- topic/p` | `topic/p` |
| Space-separated | `- topic/body concept/conbody` | `- topic/body,concept/conbody` |
| Preserves full chain | `- topic/p topic/hazardstatement` | `- topic/hazardstatement` (missing p) |
| Ends with space | Trailing space required | No trailing space |

```xml
<!-- CORRECT: Full inheritance chain preserved -->
<mfgTaskBody class="- topic/body task/taskbody mfgTask/mfgTaskBody ">

<!-- INCORRECT: Missing intermediate task/taskbody -->
<mfgTaskBody class="- topic/body mfgTask/mfgTaskBody ">
```

---

# Creating a Manufacturing Maintenance Domain

**Scenario:** ExampleCorp needs markup for:
- Preventive maintenance schedules
- Lubrication specifications
- Torque values for fasteners

**New elements:**
- `<maintenanceInterval>` -- a scheduled maintenance period
- `<lubricationPoint>` -- a machine lubrication requirement
- `<torqueSpec>` -- a fastener torque specification

---

# The Maintenance Topic Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE task PUBLIC "-//ExampleCorp//DTD DITA Manufacturing Task//EN"
  "mfg-task.dtd">
<task id="t_maintenance_schedule">
  <title>Preventive Maintenance: MC-5000 Spindle Assembly</title>

  <taskbody>
    <maintenanceInterval period="500h" severity="critical">
      <shortdesc>Replace spindle bearings and check runout
        after every 500 operating hours.</shortdesc>

      <lubricationPoint component="spindle-bearings"
                        lubricant="Klueber Isoflex NBU 15"
                        method="manual">
        <note type="caution">
          Do not over-grease. Apply 2.5g per bearing.
        </note>
      </lubricationPoint>

      <torqueSpec value="45" unit="Nm" tolerance="+/- 2"
                  tool="calibrated-torque-wrench">
        Spindle nose bolts
      </torqueSpec>
    </maintenanceInterval>
  </taskbody>
</task>
```

---

# Integrating Into the Framework

### Modifying the MC-5000 DTD Shell

When adding a new domain to a product shell, update three sections:

```xml
<!-- 1. Add domain entity declaration -->
<!ENTITY % maint-d-dec
  PUBLIC "-//ExampleCorp//ENTITIES DITA Maintenance Domain//EN"
         "maintenanceDomain.ent"
>
%maint-d-dec;

<!-- 2. Add domain module reference -->
<!ENTITY % maintenanceInterval
  PUBLIC "-//ExampleCorp//ELEMENTS DITA Maintenance//EN"
         "maintenanceDomain.mod"
>
%maintenanceInterval;

<!-- 3. Extend the concept/task info-types -->
<!ENTITY % task-info-types
  "%task-info-types; %maintenanceInterval;"
>
```

---

# Integration Testing Checklist

Before deploying a new specialization, verify:

| Test | Check |
|------|-------|
| XML Validation | Topic validates against the new DTD without errors |
| @class Integrity | `@class` attribute preserves full chain on every element |
| DITA-OT processing | HTML5 and PDF transforms complete without errors |
| Domain atts | `@domains` attribute on root element lists all domains |
| Cross-references | xref/@href resolves correctly within specialized context |
| Conref reuse | conref/@conref works for elements from the new domain |
| Keyref resolution | @keyref attributes resolve for specialized elements |

---

# Domain Attributes on Root Elements

Every topic must declare its domains on the root element:

```xml
<!-- Standard concept topic -->
<concept id="c_overview" domains="(topic concept)">

<!-- Concept with hazard statements -->
<concept id="c_safety_overview"
  domains="(topic concept)
           (topic hi-d)">

<!-- Task with maintenance, hazard, and task requirements -->
<task id="t_procedure"
  domains="(topic task)
           (topic hi-d)
           (topic pr-d)
           (topic maint-d)">
```

The `@domains` attribute tells processors which domain elements to expect in the topic.

---

# Common Pitfalls

### 1. Broken @class Attribute

```xml
<!-- WRONG: Missing intermediate step -->
<mfgTaskBody class="- topic/body mfgTask/mfgTaskBody ">

<!-- CORRECT -->
<mfgTaskBody class="- topic/body task/taskbody mfgTask/mfgTaskBody ">
```

### 2. Missing Module Reference

Symptom: DITA-OT fails with "element not defined"
Fix: Verify all four files are referenced in shell and catalog

### 3. Catalog Mismatch

Symptom: PUBLIC ID not found
Fix: Ensure catalog.xml PUBLIC ID matches the DOCTYPE declaration exactly

---

# Common Pitfalls (continued)

### 4. Entity Definition Before Declaration

```xml
<!-- WRONG: Using %maint-d-dec before declaring it -->
%maint-d-dec;    <!-- fails! -->
<!ENTITY % maint-d-dec ...>

<!-- CORRECT -->
<!ENTITY % maint-d-dec ...>
%maint-d-dec;    <!-- succeeds -->
```

### 5. Domain Scope Leakage

Adding domain elements to a base type without updating `@domains`:

```xml
<!-- WRONG: Topic uses maintenance elements but doesn't declare domain -->
<task id="t_test" domains="(topic task)">

<!-- CORRECT -->
<task id="t_test" domains="(topic task) (topic maint-d)">
```

---

# Tools for Specialization

### oXygen XML Editor
- **Specialization Support**: New > DITA Specialization wizard
- **DTD/Schema Validation**: Real-time validation of specialized content
- **Content Completion**: Auto-suggests specialized elements based on @domains

### DITA-OT
```bash
# Validate a topic against the specialized DTD
dita --input=src/topics/t_maintenance.dita \
     --format=validate \
     --args.validate.dita=true
```

### Ant Integration
```xml
<target name="check-class-attribute">
  <xslt in="${input.file}" out="${output.report}"
    style="check-class.xsl"/>
</target>
```

---

# When to Specialize vs When to Use Existing Types

| Situation | Recommendation |
|-----------|---------------|
| Need domain-specific vocabulary not in standard DITA | **Specialize** |
| Markup that drives specific rendering behavior | **Specialize** |
| Industry-required structural elements (safety, compliance) | **Specialize** |
| Just need additional metadata fields | Use `<data>` or `@othermeta` first |
| Custom rendering without new elements | Use DITA-OT custom plugin, not specialization |
| One-off requirement for single product | Use existing types with conditional processing |

---

# Migration Strategy: Introducing Specialization

### Phase 1: Pilot (2-4 weeks)
- Choose one product line (e.g., MC-5000)
- Implement maintenance domain on 10-15 topics
- Validate pipeline, gather writer feedback

### Phase 2: Rollout (4-8 weeks)
- Update all writers to use specialized DTD shells
- Migrate existing generic markup to specialized elements
- Update CI/CD pipeline for validation

### Phase 3: Governance (ongoing)
- DITA Architect reviews all new specialization requests
- Specialization changes go through Architecture & Standards Team
- Version compatibility maintained across product lines

---

<!-- _class: exercise -->

# Hands-On Exercise

**Goal:** Create a simple manufacturing domain specialization for a "calibration" domain.

**Steps:**

1. Create `calibrationDomain.mod` with these elements:
   - `<calibrationProcedure>` (container, child of taskbody)
   - `<calibrationPoint>` (single measurement point)
   - `<measurement>` (inline measured value)

2. Create `calibrationDomain.ent` with entity definitions

3. Create `mfg-task-cal.dtd` that integrates: task base + hi-d + calibration

4. Create `catalog.xml` with PUBLIC-to-SYSTEM mappings

5. Create `t_calibrate_probe.dita` -- a task topic using the new elements

**Deliverable:** Valid XML topic that processes through DITA-OT HTML5 transform.

---

# Key Takeaways

| # | Takeaway |
|---|----------|
| 1 | Specialization extends DITA for domain-specific needs without breaking processing |
| 2 | Two types: **topic type** (new document structures) and **domain** (new element vocabularies) |
| 3 | Every specialization requires four files: `.mod`, `.ent`, `.dtd`, `catalog.xml` |
| 4 | The `@class` attribute is the **backbone** -- never break the inheritance chain |
| 5 | Always declare domains on the root element via the `@domains` attribute |
| 6 | Specialize for structural/semantic needs; use existing types + processing for rendering needs |
| 7 | Test every specialization through the full DITA-OT pipeline before rollout |
| 8 | Govern specialization through Architecture & Standards Team and DGC approval |
