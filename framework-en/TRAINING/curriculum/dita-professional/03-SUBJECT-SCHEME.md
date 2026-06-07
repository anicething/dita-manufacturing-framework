---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Subject Scheme & Controlled Values
## Enforcing Taxonomy in Manufacturing Documentation

**DITA Professional | Manufacturing Documentation**

---

<!-- _class: divider -->

# Module Overview

---

## What You Will Learn

- Design and implement DITA subject scheme maps
- Create controlled value hierarchies with subjectdef
- Define enumeration constraints with enumerationdef
- Apply attribute-based content filtering
- Validate taxonomy compliance with Schematron
- Integrate subject schemes into oXygen XML Editor
- Extend controlled values for new product lines

---

<!-- _class: keypoint -->

## Why Subject Schemes Matter in Manufacturing

> Subject schemes are the **taxonomy backbone** of your documentation system. They ensure every CNC procedure, safety warning, and part specification carries consistent, machine-validatable metadata.

---

## Real-World Manufacturing Scenario

**Problem:** A global CNC manufacturer has 12,000 DITA topics across 9 product lines. Writers use inconsistent values for `@audience`, `@product`, and `@otherprops`.

- Some write `audience="operator"`
- Others write `audience="machine-operator"`
- A few write `audience="CNC_op"`

Search, filtering, and conditional publishing break.

**Solution:** Subject scheme with controlled enumeration values.

---

## Agenda

1. Subject Scheme Fundamentals (Slides 7-18)
2. Building subjectdef Hierarchies (Slides 19-32)
3. enumerationdef and attributedef (Slides 33-44)
4. Six Manufacturing Controlled Sets (Slides 45-58)
5. Schema Integration & Validation (Slides 59-68)
6. oXygen Configuration (Slides 69-76)
7. Exercise & Hands-On (Slides 77-90)

---

<!-- _class: divider -->

# Part 1: Subject Scheme Fundamentals

---

## What Is a DITA Subject Scheme?

A subject scheme map is a specialized DITA map that defines:

- **Controlled values** for attributes (what values are allowed)
- **Taxonomic hierarchies** (parent-child relationships between values)
- **Subject classifications** (how topics relate to subject categories)

File extension: `.ditamap` (same as regular maps)
Special attribute: The root `<subjectScheme>` element

---

## Subject Scheme vs Regular DITAMAP

| Feature | Regular Map | Subject Scheme |
|---------|------------|----------------|
| Root element | `<map>` | `<subjectScheme>` |
| Purpose | Organize topics | Define taxonomy |
| Contains | `<topicref>` | `<subjectdef>`, `<enumerationdef>` |
| Output | Table of contents | Controlled value lists |
| Processing | DITA-OT transformation | Schema/Schematron processing |

---

## Key Subject Scheme Elements

```
subjectScheme
├── subjectdef           (category definition)
│   ├── subjectdef       (subcategory - nesting)
│   ├── topicref         (links to example topics)
│   └── subjectHead      (grouping without value)
├── enumerationdef       (restrict attribute values)
│   └── attributedef     (specific attribute)
│       └── subjectdef   (allowed values)
├── hasInstance          (classification relationship)
├── hasKind              (type relationship)
├── hasNarrower          (specialization)
└── hasRelated           (association)
```

---

## Three Core Functions of Subject Schemes

1. **Define Controlled Values**
   - Restrict what values attributes like `@audience` and `@product` can take
   - Sources: enumerationdef binds subjectdef values to attributes

2. **Build Taxonomies**
   - Create parent-child hierarchies for browsing/filtering
   - Sources: nested subjectdef elements

3. **Classify Content**
   - Relate topics to subject categories
   - Sources: hasInstance, hasKind, hasPart relationships

---

## Manufacturing Taxonomy Example

```
Manufacturing Domain
├── Process Type
│   ├── Subtractive (CNC milling, turning, grinding)
│   ├── Additive (3D printing, SLS, FDM)
│   └── Forming (stamping, forging, casting)
├── Equipment
│   ├── CNC Machining Centers
│   ├── Lathes
│   └── Inspection Equipment (CMM, optical)
├── Role
│   ├── Operator
│   ├── Maintenance Technician
│   └── Quality Engineer
└── Safety Level
    ├── General Safety
    ├── Lockout/Tagout
    └── Confined Space
```

---

<!-- _class: keypoint -->

## The Binding Mechanism

> A subject scheme **binds** controlled values to DITA attributes through the `enumerationdef` element. Without this binding, subjectdef elements are merely a taxonomy — with it, they become **enforced schema constraints**.

---

## Subject Scheme File Convention

Recommended naming and location:

```
dita-project/
└── schemes/
    ├── audience.subjectscheme.ditamap
    ├── product.subjectscheme.ditamap
    ├── content-type.subjectscheme.ditamap
    └── master.subjectscheme.ditamap    (references all)
```

Or as a single comprehensive scheme:

```
schemes/manufacturing-taxonomy.subjectscheme.ditamap
```

---

## schemeref: Processing Instruction

Reference a subject scheme from a production map:

```xml
<map>
  <title>CNC 5000-Series User Guide</title>
  
  <!-- Bind subject scheme to this map -->
  <topicmeta>
    <data name="subjectScheme">
      <data-about>
        <?dita-scheme scheme="schemes/master.subjectscheme.ditamap"?>
      </data-about>
    </data>
  </topicmeta>
  
  <topicref href="topics/overview.dita"/>
  <!-- ... -->
</map>
```

---

## Schematron: The Validation Engine

Subject schemes generate **Schematron rules** that validate attribute values:

1. DITA-OT reads `enumerationdef` bindings
2. Generates Schematron `.sch` file
3. Validates all topics against the rules
4. Reports violations: "Value 'CNC_op' not in controlled list"

**Schematron phases for subject scheme:**
- Phase 1: Check all @audience values against enumerationdef
- Phase 2: Check all @product values
- Phase 3: Check @otherprops values

---

## Validation Report Example

```
Subject Scheme Validation Report
================================
Map: CNC-5000-User-Guide.ditamap
Scheme: manufacturing-taxonomy.subjectscheme.ditamap

VIOLATIONS:
  topics/safety/lockout-procedure.dita
    @audience: "machine-operator" not in controlled values
    Allowed: operator, maintenance-tech, quality-engineer
    
  topics/cnc/setup-workpiece.dita
    @product: "5000X" not in controlled values
    Allowed: 5000-Basic, 5000-Advanced, 5000-Pro
    
SUMMARY: 2 violations in 127 topics (98.4% compliance)
```

---

<!-- _class: divider -->

# Part 2: Building subjectdef Hierarchies

---

## subjectdef: The Building Block

```xml
<subjectdef keys="manufacturing_processes"
            navtitle="Manufacturing Processes">
  <subjectdef keys="subtractive"
              navtitle="Subtractive Manufacturing">
    <subjectdef keys="cnc_milling"
                navtitle="CNC Milling"/>
    <subjectdef keys="cnc_turning"
                navtitle="CNC Turning"/>
  </subjectdef>
  <subjectdef keys="additive"
              navtitle="Additive Manufacturing">
    <subjectdef keys="slm" navtitle="Selective Laser Melting"/>
    <subjectdef keys="fdm" navtitle="Fused Deposition Modeling"/>
  </subjectdef>
</subjectdef>
```

---

## subjectdef Attributes Deep Dive

| Attribute | Required | Purpose |
|-----------|----------|---------|
| `keys` | Yes | Unique identifier, used as attribute value |
| `navtitle` | No | Display name in UIs |
| `href` | No | Link to defining topic |
| `outputclass` | No | Styling/behavior hook |
| `processing-role` | No | `normal` or `resource-only` |

The **keys value** becomes the controlled attribute value:

```xml
<!-- subjectdef keys="operator" -->
<topic audience="operator">...</topic>
```

---

## subjectdef with href: Linked Definitions

```xml
<subjectdef keys="lockout_tagout"
            navtitle="Lockout/Tagout Safety"
            href="reference/safety-levels.dita#lockout">
  <subjectdef keys="loto_level_1"
              navtitle="Level 1 - Basic Lockout"/>
  <subjectdef keys="loto_level_2"
              navtitle="Level 2 - Complex Lockout"/>
</subjectdef>
```

The `href` provides a **glossary-style reference** to a topic that defines the term — useful for onboarding new writers.

---

## subjectHead: Grouping Without Value

Use `<subjectHead>` to create organizational groups that do not become controlled values:

```xml
<subjectdef keys="audience">
  <subjectHead>
    <subjectHeadMeta>
      <navtitle>Internal Teams</navtitle>
    </subjectHeadMeta>
    <subjectdef keys="operator"/>
    <subjectdef keys="maintenance"/>
  </subjectHead>
  <subjectHead>
    <subjectHeadMeta>
      <navtitle>External</navtitle>
    </subjectHeadMeta>
    <subjectdef keys="distributor"/>
    <subjectdef keys="end-customer"/>
  </subjectHead>
</subjectdef>
```

"Internal Teams" and "External" are not selectable values — they are UI grouping.

---

## Taxonomy with hasNarrower Relationship

```xml
<subjectdef keys="cnc_machine">
  <hasNarrower>
    <subjectdef keys="vertical_machining_center"/>
    <subjectdef keys="horizontal_machining_center"/>
    <subjectdef keys="five_axis_machine"/>
  </hasNarrower>
</subjectdef>
```

Alternative to nesting — produces the same parent-child hierarchy but with explicit relationship semantics.

---

## hasKind: Type-of Relationship

```xml
<subjectdef keys="safety_procedure">
  <hasKind>
    <subjectdef keys="lockout_tagout"/>
    <subjectdef keys="confined_space_entry"/>
    <subjectdef keys="hot_work_permit"/>
    <subjectdef keys="ppe_requirement"/>
  </hasKind>
</subjectdef>
```

Use `hasKind` when subcategories are **types/kinds** of the parent (not mere subcategories).

---

## hasPart: Component Relationship

```xml
<subjectdef keys="cnc_machining_center">
  <hasPart>
    <subjectdef keys="spindle_assembly"/>
    <subjectdef keys="tool_changer"/>
    <subjectdef keys="coolant_system"/>
    <subjectdef keys="cnc_controller"/>
    <subjectdef keys="workholding_system"/>
  </hasPart>
</subjectdef>
```

Use for **meronymic** (part-whole) relationships — ideal for equipment BOM taxonomies.

---

## hasRelated: Association

```xml
<subjectdef keys="cnc_milling">
  <hasRelated>
    <subjectdef keys="cutting_tools"/>
    <subjectdef keys="coolant_selection"/>
    <subjectdef keys="workpiece_material"/>
    <subjectdef keys="surface_finish_spec"/>
  </hasRelated>
</subjectdef>
```

Cross-category associations — not hierarchical, just "see also" relationships.

---

## Relationship Types Summary

| Element | Semantics | Manufacturing Example |
|---------|-----------|----------------------|
| (nesting) | Default hierarchy | CNC > Milling > 5-Axis |
| `hasNarrower` | Explicit subcategory | Machine > Vertical MC |
| `hasKind` | Type-of | Safety > Lockout |
| `hasPart` | Part-whole | Machine > Spindle |
| `hasRelated` | Association | Milling > Coolant |

Choose the right relationship type — it affects how UIs display taxonomies and how search facets work.

---

## Deep Nesting Best Practices

**Recommended:** Maximum 4 levels deep

```
Level 1: Manufacturing (root)
  Level 2: Process Type
    Level 3: Machining
      Level 4: Specific Process (milling, turning, drilling, grinding)
```

**Deeper than 4 levels?** Split into multiple top-level categories or use cross-references.

---

## Key Management Strategy

```xml
<!-- GOOD: Consistent, hierarchical key naming -->
<subjectdef keys="process_subtractive_milling_5axis"/>
<subjectdef keys="process_subtractive_milling_3axis"/>
<subjectdef keys="process_subtractive_turning_cnc"/>

<!-- BAD: Inconsistent naming -->
<subjectdef keys="5axis"/>
<subjectdef keys="milling-3"/>
<subjectdef keys="cncTurning"/>
```

Recommended pattern: `{domain}_{category}_{subcategory}_{specific}`

---

## Localizing subjectdef Titles

```xml
<subjectdef keys="operator">
  <title>
    <ph xml:lang="en">Machine Operator</ph>
    <ph xml:lang="de">Maschinenbediener</ph>
    <ph xml:lang="zh-CN">机器操作员</ph>
    <ph xml:lang="ja">機械オペレーター</ph>
  </title>
</subjectdef>
```

Controlled values (`keys`) remain machine-readable and language-independent. Titles provide UI display in each language.

---

<!-- _class: divider -->

# Part 3: enumerationdef and attributedef

---

## enumerationdef: The Gatekeeper

```xml
<enumerationdef>
  <attributedef name="audience"/>
  <subjectdef keyref="manufacturing_audience"/>
</enumerationdef>
```

This says: *"The `@audience` attribute may only contain values defined under the `manufacturing_audience` subject scheme branch."*

Without this binding, the subject scheme is a taxonomy only — no enforcement.

---

## Full enumerationdef Example

```xml
<subjectScheme>
  <!-- Define the taxonomy -->
  <subjectdef keys="all_products" navtitle="Product Lines">
    <subjectdef keys="cnc_5000_basic" navtitle="CNC 5000 Basic"/>
    <subjectdef keys="cnc_5000_adv" navtitle="CNC 5000 Advanced"/>
    <subjectdef keys="cnc_5000_pro" navtitle="CNC 5000 Pro"/>
    <subjectdef keys="lathe_3000" navtitle="Lathe 3000 Series"/>
    <subjectdef keys="mill_7000" navtitle="Mill 7000 Series"/>
  </subjectdef>
  
  <!-- Bind to @product attribute -->
  <enumerationdef>
    <attributedef name="product"/>
    <subjectdef keyref="all_products"/>
  </enumerationdef>
</subjectScheme>
```

---

## attributedef Properties

| Attribute | Values | Description |
|-----------|--------|-------------|
| `name` | attribute name | The DITA attribute to constrain |
| `element` | element name(s) | Optional: limit to specific elements |

```xml
<!-- Only constrain @audience on <note> elements -->
<enumerationdef>
  <attributedef name="audience" element="note"/>
  <subjectdef keyref="audience_values"/>
</enumerationdef>

<!-- Constrain @audience on all elements -->
<enumerationdef>
  <attributedef name="audience"/>
  <subjectdef keyref="audience_values"/>
</enumerationdef>
```

---

## Controlling @otherprops

`@otherprops` is the catch-all conditional attribute. Subject schemes bring structure to it:

```xml
<enumerationdef>
  <attributedef name="otherprops"/>
  <subjectdef keyref="otherprops_master"/>
</enumerationdef>

<!-- In the referenced branch -->
<subjectdef keys="otherprops_master">
  <subjectdef keys="certification_required"/>
  <subjectdef keys="export_controlled"/>
  <subjectdef keys="proprietary"/>
  <subjectdef keys="field_service_only"/>
  <subjectdef keys="engineering_review"/>
</subjectdef>
```

---

## Multi-Attribute Subject Scheme Architecture

```xml
<subjectScheme>
  <subjectdef keys="root">
    
    <!-- Branch 1: Audience -->
    <subjectdef keys="all_audiences">
      <subjectdef keys="operator"/>
      <subjectdef keys="maintenance-tech"/>
      <subjectdef keys="quality-engineer"/>
      <subjectdef keys="process-planner"/>
    </subjectdef>
    
    <!-- Branch 2: Product -->
    <subjectdef keys="all_products">
      <subjectdef keys="cnc-5000"/>
      <subjectdef keys="lathe-3000"/>
    </subjectdef>
    
    <!-- Branch 3: Content Type -->
    <subjectdef keys="all_content_types">
      <subjectdef keys="procedure"/>
      <subjectdef keys="reference"/>
      <subjectdef keys="troubleshooting"/>
    </subjectdef>
    
  </subjectdef>
  
  <enumerationdef>
    <attributedef name="audience"/>
    <subjectdef keyref="all_audiences"/>
  </enumerationdef>
  
  <enumerationdef>
    <attributedef name="product"/>
    <subjectdef keyref="all_products"/>
  </enumerationdef>
  
  <enumerationdef>
    <attributedef name="otherprops"/>
    <subjectdef keyref="all_content_types"/>
  </enumerationdef>
  
</subjectScheme>
```

---

## Optional vs Mandatory Values

**Default behavior:** enumerationdef makes the value set **closed** — only listed values are valid.

**To allow empty (no audience specified):**

```xml
<subjectdef keys="all_audiences">
  <subjectdef keys="none" navtitle="(Unspecified)">
    <hasNarrower>
      <subjectdef keys="operator"/>
      <subjectdef keys="maintenance"/>
    </hasNarrower>
  </subjectdef>
</subjectdef>
```

Or use a processing instruction hint: `<?dita-use-conref-target?>` on the base content.

---

## Hierarchical Filtering from Enumeration

When enumerationdef binds hierarchical subjectdef trees:

```
audience
├── internal
│   ├── operator
│   └── engineer
└── external
    ├── distributor
    └── customer
```

Selecting `audience="internal"` in filters includes both `operator` and `engineer` topics. This is **hierarchical filtering** — a key benefit of structured taxonomy.

---

## Cascading Enumeration Constraints

Multiple subject schemes can contribute to the same attribute:

```xml
<!-- base-taxonomy.subjectscheme -->
<enumerationdef>
  <attributedef name="product"/>
  <subjectdef keyref="base_products"/>
</enumerationdef>

<!-- division-specific.subjectscheme -->
<enumerationdef>
  <attributedef name="product"/>
  <subjectdef keyref="division_products"/>
</enumerationdef>
```

Schematron merges both sets. Be careful: this can create unexpected allowed values.

**Best practice:** One master subject scheme per attribute.

---

## enumerationdef with element Restriction

Constrain only on specific elements for granular control:

```xml
<!-- Only <note> elements need audience constraint for safety -->
<enumerationdef>
  <attributedef name="audience" element="note"/>
  <subjectdef keyref="safety_audience_values"/>
</enumerationdef>

<!-- All <step> elements use the same process taxonomy -->
<enumerationdef>
  <attributedef name="otherprops" element="step"/>
  <subjectdef keyref="process_taxonomy"/>
</enumerationdef>
```

---

## Detecting Taxonomy Gaps

Common issues with subject scheme coverage:

1. **Orphan values:** Attribute values used in topics but not in any subjectdef
2. **Dead branches:** subjectdef keys never referenced in any topic
3. **Inconsistent depth:** Some branches 2 levels deep, others 5
4. **Duplicate keys:** Same key used in different branches

```bash
# Check for values in topics not in subject scheme
grep -roh 'audience="[^"]*"' topics/ | sort -u > used-values.txt
grep 'keys="' scheme.ditamap | sort -u > defined-values.txt
comm -23 used-values.txt defined-values.txt  # orphans
```

---

<!-- _class: divider -->

# Part 4: Six Manufacturing Controlled Sets

---

## Set 1: Audience (who reads this)

```xml
<subjectdef keys="all_audiences" navtitle="Audience">
  <subjectdef keys="aud_operator" navtitle="Machine Operator">
    <subjectdef keys="aud_op_novice" navtitle="Novice Operator"/>
    <subjectdef keys="aud_op_experienced" navtitle="Experienced Operator"/>
    <subjectdef keys="aud_op_setup" navtitle="Setup Operator"/>
  </subjectdef>
  <subjectdef keys="aud_maintenance" navtitle="Maintenance Technician">
    <subjectdef keys="aud_maint_mechanical" navtitle="Mechanical"/>
    <subjectdef keys="aud_maint_electrical" navtitle="Electrical"/>
    <subjectdef keys="aud_maint_calibration" navtitle="Calibration"/>
  </subjectdef>
  <subjectdef keys="aud_quality" navtitle="Quality Engineer"/>
  <subjectdef keys="aud_process" navtitle="Process Planner"/>
  <subjectdef keys="aud_safety" navtitle="Safety Officer"/>
  <subjectdef keys="aud_trainer" navtitle="Technical Trainer"/>
</subjectdef>

<enumerationdef>
  <attributedef name="audience"/>
  <subjectdef keyref="all_audiences"/>
</enumerationdef>
```

---

## Set 2: Content Type (what kind of topic)

```xml
<subjectdef keys="all_content_types" navtitle="Content Type">
  <subjectdef keys="ct_concept" navtitle="Concept"/>
  <subjectdef keys="ct_task" navtitle="Task/Procedure"/>
  <subjectdef keys="ct_reference" navtitle="Reference"/>
  <subjectdef keys="ct_troubleshooting" navtitle="Troubleshooting"/>
  <subjectdef keys="ct_safety" navtitle="Safety/Hazard"/>
  <subjectdef keys="ct_specification" navtitle="Specification Sheet"/>
  <subjectdef keys="ct_quickref" navtitle="Quick Reference"/>
  <subjectdef keys="ct_glossary" navtitle="Glossary Entry"/>
  <subjectdef keys="ct_releasenotes" navtitle="Release Notes"/>
</subjectdef>

<enumerationdef>
  <attributedef name="otherprops"/>
  <subjectdef keyref="all_content_types"/>
</enumerationdef>
```

---

## Set 3: Document Status (workflow state)

```xml
<subjectdef keys="all_doc_status" navtitle="Document Status">
  <subjectdef keys="status_draft" navtitle="Draft"/>
  <subjectdef keys="status_review" navtitle="In Review"/>
  <subjectdef keys="status_approved" navtitle="Approved"/>
  <subjectdef keys="status_published" navtitle="Published"/>
  <subjectdef keys="status_obsolete" navtitle="Obsolete"/>
  <subjectdef keys="status_hold" navtitle="On Hold"/>
</subjectdef>

<enumerationdef>
  <attributedef name="otherprops"/>
  <subjectdef keyref="all_doc_status"/>
</enumerationdef>
```

---

## Set 4: Product Line (which equipment)

```xml
<subjectdef keys="all_products" navtitle="Product Line">
  <subjectdef keys="prod_cnc_5axis" navtitle="CNC Machining Centers">
    <subjectdef keys="prod_cnc_vmc5000" navtitle="VMC-5000 Series"/>
    <subjectdef keys="prod_cnc_hmc7000" navtitle="HMC-7000 Series"/>
    <subjectdef keys="prod_cnc_5axis_ultra" navtitle="5-Axis Ultra"/>
  </subjectdef>
  <subjectdef keys="prod_lathe" navtitle="CNC Lathes">
    <subjectdef keys="prod_lathe_lt3000" navtitle="LT-3000"/>
    <subjectdef keys="prod_lathe_lt5000" navtitle="LT-5000"/>
  </subjectdef>
  <subjectdef keys="prod_grinding" navtitle="Precision Grinding">
    <subjectdef keys="prod_grind_surface" navtitle="Surface Grinder SG-200"/>
    <subjectdef keys="prod_grind_cylindrical" navtitle="Cylindrical CG-400"/>
  </subjectdef>
  <subjectdef keys="prod_cmm" navtitle="Coordinate Measuring">
    <subjectdef keys="prod_cmm_bridge" navtitle="Bridge CMM"/>
    <subjectdef keys="prod_cmm_gantry" navtitle="Gantry CMM"/>
  </subjectdef>
</subjectdef>
```

---

## Set 5: Hazard Severity (safety classification)

```xml
<subjectdef keys="all_hazard_severity" navtitle="Hazard Severity">
  <subjectdef keys="haz_danger" navtitle="DANGER - Imminent Death/Severe Injury">
    <subjectdef keys="haz_danger_electrical" navtitle="Electrical Hazard"/>
    <subjectdef keys="haz_danger_mechanical" navtitle="Mechanical Hazard (Crush/Cut)"/>
    <subjectdef keys="haz_danger_chemical" navtitle="Chemical Exposure"/>
    <subjectdef keys="haz_danger_confined" navtitle="Confined Space"/>
  </subjectdef>
  <subjectdef keys="haz_warning" navtitle="WARNING - Potential Serious Injury">
    <subjectdef keys="haz_warn_ergo" navtitle="Ergonomic Hazard"/>
    <subjectdef keys="haz_warn_thermal" navtitle="Thermal Burn"/>
    <subjectdef keys="haz_warn_noise" navtitle="High Noise Level"/>
  </subjectdef>
  <subjectdef keys="haz_caution" navtitle="CAUTION - Minor/Moderate Injury"/>
  <subjectdef keys="haz_notice" navtitle="NOTICE - Property Damage Only"/>
</subjectdef>
```

---

## Set 6: Skill Level (prerequisite knowledge)

```xml
<subjectdef keys="all_skill_levels" navtitle="Skill Level">
  <subjectdef keys="skill_basic" navtitle="Basic (No Prerequisites)">
    <subjectdef keys="skill_basic_safety" navtitle="Safety Awareness"/>
    <subjectdef keys="skill_basic_tools" navtitle="Basic Hand Tools"/>
  </subjectdef>
  <subjectdef keys="skill_intermediate" navtitle="Intermediate">
    <subjectdef keys="skill_int_cnc" navtitle="CNC Operation Fundamentals"/>
    <subjectdef keys="skill_int_gcode" navtitle="G-Code Reading"/>
    <subjectdef keys="skill_int_blueprint" navtitle="Blueprint Reading"/>
  </subjectdef>
  <subjectdef keys="skill_advanced" navtitle="Advanced">
    <subjectdef keys="skill_adv_macro" navtitle="Macro Programming"/>
    <subjectdef keys="skill_adv_calibration" navtitle="Laser Calibration"/>
    <subjectdef keys="skill_adv_troubleshoot" navtitle="Advanced Diagnostics"/>
  </subjectdef>
</subjectdef>
```

---

## Complete Master Subject Scheme Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE subjectScheme PUBLIC "-//OASIS//DTD DITA Subject Scheme Map//EN" 
  "map.dtd">
<subjectScheme>
  <title>Manufacturing Documentation Taxonomy - Master Subject Scheme</title>
  
  <subjectdef keys="root" navtitle="Manufacturing Taxonomy">
    
    <!-- 1. Audience -->
    <subjectdef keys="all_audiences">...</subjectdef>
    
    <!-- 2. Content Type -->
    <subjectdef keys="all_content_types">...</subjectdef>
    
    <!-- 3. Document Status -->
    <subjectdef keys="all_doc_status">...</subjectdef>
    
    <!-- 4. Product Line -->
    <subjectdef keys="all_products">...</subjectdef>
    
    <!-- 5. Hazard Severity -->
    <subjectdef keys="all_hazard_severity">...</subjectdef>
    
    <!-- 6. Skill Level -->
    <subjectdef keys="all_skill_levels">...</subjectdef>
    
  </subjectdef>
  
  <!-- Bindings -->
  <enumerationdef>
    <attributedef name="audience"/>
    <subjectdef keyref="all_audiences"/>
  </enumerationdef>
  
  <enumerationdef>
    <attributedef name="product"/>
    <subjectdef keyref="all_products"/>
  </enumerationdef>
  
  <enumerationdef>
    <attributedef name="otherprops"/>
    <subjectdef keyref="all_content_types"/>
  </enumerationdef>
  
  <enumerationdef>
    <attributedef name="otherprops"/>
    <subjectdef keyref="all_doc_status"/>
  </enumerationdef>
  
  <!-- Note: hazard-severity and skill-level use @otherprops with colon separator -->
  <!-- e.g., otherprops="haz_danger" or otherprops="skill_advanced" -->
  
</subjectScheme>
```

---

<!-- _class: note -->

## Important: @otherprops Overloading

> When multiple enumerationdef bindings target the same attribute (`@otherprops`), the Schematron merges all values into one allowed set. A topic can carry `otherprops="ct_task status_approved haz_warning"` — all must validate against the merged enumeration.

---

## Using Custom Specialized Attributes

For cleaner separation, consider **DITA specialization** to create custom attributes:

```xml
<!-- Then in subject scheme, bind to specialized attributes -->
<enumerationdef>
  <attributedef name="hazardseverity"/>   <!-- specialized attribute -->
  <subjectdef keyref="all_hazard_severity"/>
</enumerationdef>
```

This avoids overloading `@otherprops` and provides clearer semantics.

---

<!-- _class: divider -->

# Part 5: Schema Integration & Validation

---

## Subject Scheme Processing Pipeline

```
Subject Scheme Map (.ditamap)
        │
        ▼
  DITA-OT Subject Scheme Processor
        │
        ├──► Generate Schematron (.sch) rules
        │         │
        │         ▼
        │    Validate all topics against .sch
        │         │
        │         ▼
        │    Validation Report (HTML/XML)
        │
        └──► Generate Controlled Value List (HTML)
                  │
                  ▼
            Writer Reference Documentation
```

---

## DITA-OT Subject Scheme Processing

The DITA-OT has built-in subject scheme processing in the preprocess stage:

```
preprocess/
├── subject-scheme/
│   ├── read-subject-scheme.xsl    (parses subjectScheme maps)
│   ├── merge-subject-scheme.xsl   (merges multiple schemes)
│   └── generate-schematron.xsl   (creates validation rules)
```

To run subject scheme validation explicitly:

```bash
dita -i map.ditamap \
     -f subjectScheme \
     -o output/subject-scheme-report
```

---

## Running Validation with DITA-OT

```bash
# Generate subject scheme report
dita -i production/maps/cnc-5000-user-guide.ditamap \
     -f subject-scheme \
     -o reports/subject-scheme/ \
     -Dsubject-scheme=schemes/manufacturing-taxonomy.subjectscheme.ditamap

# Output will include:
#   index.html         (readable report)
#   validation.xml     (machine-readable)
#   controlled-values.html  (taxonomy browser)
```

---

## CI/CD Integration: GitHub Actions

```yaml
name: Subject Scheme Validation
on: [push, pull_request]

jobs:
  validate-taxonomy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Subject Scheme Validation
        uses: dita-ot/action@v2
        with:
          input: production/maps/*.ditamap
          transtype: subject-scheme
          args: |
            --subject-scheme=schemes/manufacturing-taxonomy.subjectscheme.ditamap
            --output=reports/subject-scheme/
      
      - name: Check for Violations
        run: |
          violations=$(grep -c '<violation' reports/subject-scheme/validation.xml)
          if [ $violations -gt 0 ]; then
            echo "ERROR: $violations subject scheme violations found"
            exit 1
          fi
```

---

## Schematron Rule Generation: What Happens

Input subjectdef hierarchy:
```
audience
├── operator
└── maintenance
```

Generated Schematron (conceptual):

```xml
<schema xmlns="http://purl.oclc.org/dsdl/schematron">
  <pattern id="subject-scheme-audience">
    <rule context="*[@audience]">
      <assert test="@audience = ('operator', 'maintenance')"
              flag="warning">
        Attribute 'audience' must be one of: operator, maintenance
      </assert>
    </rule>
  </pattern>
</schema>
```

---

## Validation with oXygen XML Editor

oXygen provides built-in subject scheme validation:

1. Associate subject scheme via **Document Type Association**
2. Open any DITA topic
3. **Document > Validate > Validate with Subject Scheme**
4. Errors appear in the Results panel with one-click navigation

oXygen also provides **content completion** for controlled values — only valid values appear in the attribute editor.

---

## Validation Error Types

| Error Code | Description | Severity |
|-----------|-------------|----------|
| SS-VAL-001 | Value not in controlled list | ERROR |
| SS-VAL-002 | Required value missing | ERROR |
| SS-VAL-003 | Deprecated value used | WARNING |
| SS-VAL-004 | Case mismatch (value differs only in case) | WARNING |
| SS-VAL-005 | Hierarchical path incomplete | INFO |

---

## Batch Validation Script

```bash
#!/bin/bash
# batch-validate-taxonomy.sh
# Validates all maps in production against subject scheme

SCHEME="schemes/manufacturing-taxonomy.subjectscheme.ditamap"
REPORT_DIR="reports/subject-scheme-validation/$(date +%Y%m%d)"
mkdir -p $REPORT_DIR

for MAP in production/maps/*.ditamap; do
  MAP_NAME=$(basename "$MAP" .ditamap)
  echo "Validating: $MAP_NAME"
  
  dita -i "$MAP" \
       -f subject-scheme \
       -o "$REPORT_DIR/$MAP_NAME" \
       -Dsubject-scheme="$SCHEME"
  
  if [ $? -ne 0 ]; then
    echo "  FAILED: $MAP_NAME" >> $REPORT_DIR/summary.txt
  else
    echo "  PASSED: $MAP_NAME" >> $REPORT_DIR/summary.txt
  fi
done

echo "All reports saved to: $REPORT_DIR"
```

---

## Automated Correction Suggestions

```python
# auto-correct-taxonomy.py
# Suggests fixes for common taxonomy violations

import re, sys, lxml.etree as ET

SCHEME_FILE = "schemes/manufacturing-taxonomy.subjectscheme.ditamap"

def load_allowed_values(attr_name):
    """Parse subject scheme to extract allowed values for attribute."""
    tree = ET.parse(SCHEME_FILE)
    allowed = set()
    
    for enumdef in tree.findall('.//{*}enumerationdef'):
        attrdef = enumdef.find('{*}attributedef')
        if attrdef is not None and attrdef.get('name') == attr_name:
            subjectdef = enumdef.find('{*}subjectdef')
            keyref = subjectdef.get('keyref')
            # Resolve keyref to collect all descendant keys
            branch = tree.find(f'.//*[@keys="{keyref}"]')
            if branch is not None:
                for elem in branch.iter():
                    if elem.get('keys'):
                        allowed.add(elem.get('keys'))
    return allowed

def suggest_fix(used_value, allowed_values):
    """Fuzzy match to suggest closest allowed value."""
    from difflib import get_close_matches
    matches = get_close_matches(used_value, list(allowed_values), n=3, cutoff=0.4)
    return matches

# Example usage:
# python auto-correct-taxonomy.py topics/*.dita
```

---

## Creating a Taxonomy Change Log

```xml
<!-- taxonomy-changelog.xml -->
<taxonomy-history>
  <change id="CHG-003" date="2026-05-15" author="jdoe">
    <action>ADDED</action>
    <path>all_products > prod_cnc_5axis > prod_cnc_vmc5000s</path>
    <value>prod_cnc_vmc5000s</value>
    <label>VMC-5000s (Spindle Upgrade)</label>
    <reason>New spindle upgrade option for VMC-5000 series</reason>
    <affected-topics>0 (new value)</affected-topics>
  </change>
  
  <change id="CHG-002" date="2026-04-20" author="asmith">
    <action>DEPRECATED</action>
    <path>all_audiences > aud_op_novice</path>
    <replacement>aud_op_novice_safety</replacement>
    <reason>Split novice into novice-basic and novice-safety</reason>
    <affected-topics>47</affected-topics>
  </change>
</taxonomy-history>
```

---

<!-- _class: divider -->

# Part 6: oXygen Configuration

---

## Configuring Subject Scheme in oXygen Framework

File: `[oXygen-install]/frameworks/dita/manufacturing_framework.framework`

```xml
<framework>
  <name>Manufacturing DITA Framework</name>
  
  <subjectSchemeMap>
    <!-- Associate subject scheme for validation -->
    <file path="schemes/manufacturing-taxonomy.subjectscheme.ditamap"/>
  </subjectSchemeMap>
  
  <author>
    <actions>
      <!-- Content completion driven by subject scheme -->
      <action id="subjectSchemeCompletion" 
              activationMode="contentCompletion"/>
    </actions>
  </author>
</framework>
```

---

## oXygen Content Completion with Subject Scheme

When configured, oXygen provides:

1. **Dropdown lists** for controlled attributes:
   - `@audience` shows: operator, maintenance-tech, quality-engineer...
   - Invalid values flagged instantly

2. **Hierarchical browser**:
   - See the taxonomy tree while editing
   - Understand parent-child relationships

3. **Quick Assist**:
   - Right-click > "Apply Subject Scheme Value"
   - Batch-applies values to multiple topics

---

## oXygen Validation Scenarios for CI/CD

Create a validation scenario that can be shared with CI/CD:

```xml
<!-- validation-scenario.ditasv -->
<validationScenario>
  <name>Manufacturing Taxonomy - Full</name>
  <validationUnit>
    <documentType>DITA</documentType>
    <validateSubjectScheme>true</validateSubjectScheme>
    <validateSchematron>
      <schematron phase="#ALL"/>
    </validateSchematron>
    <validateDITASpecialization>true</validateDITASpecialization>
  </validationUnit>
</validationScenario>
```

Export and commit this file — CI/CD uses the same configuration.

---

## oXygen Project File Integration

```xml
<!-- manufacturing-docs.xpr -->
<project>
  <meta>
    <subjectSchemeMaps>
      <subjectSchemeMap>
        <path>schemes/manufacturing-taxonomy.subjectscheme.ditamap</path>
        <autoValidate>true</autoValidate>
      </subjectSchemeMap>
    </subjectSchemeMaps>
  </meta>
  
  <projectTree name="Documentation">
    <folder name="Production Maps"/>
    <folder name="Topics"/>
    <folder name="Schemes"/>
    <folder name="Reports"/>
  </projectTree>
</project>
```

---

## Creating Author Actions for Common Values

```xml
<!-- oXygen Author Actions for rapid metadata tagging -->
<authorAction id="set-audience-operator">
  <name>Set Audience: Operator</name>
  <description>Marks current element audience="aud_operator"</description>
  <operation>
    <ro.sync.ecss.extensions.commons.operations.SetAttributeOperation>
      <attributeName>audience</attributeName>
      <attributeValue>aud_operator</attributeValue>
    </ro.sync.ecss.extensions.commons.operations.SetAttributeOperation>
  </operation>
</authorAction>
```

Assign keyboard shortcuts for rapid tagging workflow.

---

## Subject Scheme in Web Author (Collaborative)

For oXygen Web Author / Content Fusion:

```xml
<webAuthorConfig>
  <plugins>
    <plugin id="subjectSchemeGuide">
      <name>Manufacturing Taxonomy Guide</name>
      <description>Shows valid attribute values from subject scheme</description>
      <cssResource path="css/taxonomy-guide.css"/>
      <jsResource path="js/taxonomy-guide.js"/>
    </plugin>
  </plugins>
</webAuthorConfig>
```

Ensures web-based contributors also follow the taxonomy.

---

<!-- _class: keypoint -->

## oXygen Integration Best Practice

> Configure subject schemes at the **framework level** (not per-project) so all writers in the organization get consistent validation, content completion, and author actions — regardless of which project they open.

---

<!-- _class: divider -->

# Part 7: Exercise & Reporting

---

<!-- _class: exercise -->

## Exercise: Subject Scheme for Manufacturing

**Goal:** Design and implement a complete subject scheme with controlled values for a CNC manufacturing documentation set, then validate sample content.

**Time:** 90 minutes
**Format:** Individual or pair exercise

---

<!-- _class: exercise -->

## Exercise Setup (1/3)

You are given a documentation set for **PrecisionCut CNC Solutions**, a manufacturer with three product lines:

- **VMC-5000** (Vertical Machining Center) — their flagship
- **HMC-7000** (Horizontal Machining Center)
- **LT-3000** (CNC Lathe)

They serve four audience types: operators, maintenance technicians, quality engineers, and process planners.

They maintain content across five statuses: draft, review, approved, published, and obsolete.

---

<!-- _class: exercise -->

## Exercise Setup (2/3)

**Sample topics provided** (conceptual — you will create the taxonomy):

| Topic | Filename | Has Issues |
|-------|----------|------------|
| VMC-5000 Setup | `vmc5000-setup.dita` | `audience="setup-operator"` (not in taxonomy) |
| HMC-7000 Calibration | `hmc7000-cal.dita` | `audience="quality"` (ambiguous) |
| LT-3000 Maintenance | `lt3000-maint.dita` | `product="lathe"` (wrong key format) |
| Safety - Lockout | `safety-lockout.dita` | No audience assigned |
| Spindle Replacement | `spindle-replace.dita` | `otherprops="urgent"` (not controlled) |

---

<!-- _class: exercise -->

## Exercise Tasks (3/3)

**Task 1: Design the Taxonomy** (20 min)
- Create audience, product, document-status, and content-type subjectdef trees
- Minimum 3 levels deep for audience and product
- Include parent-child relationships

**Task 2: Build the enumerationdef Bindings** (15 min)
- Bind audience, product, and one otherprops set
- Ensure all sample topics have valid paths in the taxonomy

**Task 3: Validate with Sample Topics** (15 min)
- Create 5 DITA topics (concept, task, reference, troubleshooting, hazard)
- Apply controlled values
- Generate Schematron validation report

---

<!-- _class: exercise -->

## Exercise Task 4: Fix Violations (20 min)

Given these validation errors, determine the fix:

```
VIOLATION 1: vmc5000-setup.dita
  @audience "setup-operator" not in controlled values

VIOLATION 2: hmc7000-cal.dita  
  @audience "quality" not in controlled values

VIOLATION 3: lt3000-maint.dita
  @product "lathe" not in controlled values

VIOLATION 4: safety-lockout.dita
  Missing required @audience attribute

VIOLATION 5: spindle-replace.dita
  @otherprops "urgent" not in controlled values
```

**For each violation:** Decide whether to (a) add the value to the taxonomy, or (b) change the topic's attribute value. Justify your choice.

---

<!-- _class: exercise -->

## Exercise Task 5: Extend for New Product Line (20 min)

A new product line is launching: **5-Axis Ultra (5AU-1000)**

1. Where does it fit in the existing product taxonomy?
2. Add the appropriate subjectdef entries
3. Create a new topic for 5AU-1000 calibration
4. Assign correct controlled values
5. Validate that existing content is not affected

---

<!-- _class: exercise -->

## Exercise Deliverables

1. **manufacturing-taxonomy.subjectscheme.ditamap** — complete subject scheme file
2. **validation-report.txt** — Schematron validation output (clean, no violations)
3. **taxonomy-design-rationale.md** — short write-up answering:
   - Why did you choose 3 levels for audience taxonomy?
   - What naming convention did you use for keys?
   - How would you handle future product lines?
4. **exercise-reflection.md** — 1-2 paragraphs on what you learned

---

<!-- _class: divider -->

# Part 8: Common Patterns & Pitfalls

---

## Pattern: Progressive Disclosure Filtering

Use hierarchy to enable progressive disclosure in the content delivery portal:

```
Filter 1: Select Product → VMC-5000
  Filter 2: Select Audience → Operator
    Filter 3: Select Content Type → Procedure
      RESULTS: 23 procedures for VMC-5000 operators
```

Each filter narrows using subject scheme hierarchy. A filter at `aud_operator` automatically includes all child audiences.

---

## Pattern: Taxonomy-Driven Search Facets

```xml
<!-- Subject scheme drives search facet configuration -->
<searchFacetConfig>
  <facet attribute="product" label="Product" 
         subjectScheme="all_products" ui="tree"/>
  <facet attribute="audience" label="Role" 
         subjectScheme="all_audiences" ui="dropdown"/>
  <facet attribute="otherprops" label="Content Type"
         subjectScheme="all_content_types" ui="checkbox"/>
  <facet attribute="otherprops" label="Status"
         subjectScheme="all_doc_status" ui="checkbox"/>
</searchFacetConfig>
```

Facets auto-update when taxonomy changes — no UI code changes needed.

---

## Pattern: Version-Specific Taxonomies

```xml
<subjectdef keys="all_products">
  <subjectdef keys="prod_vmc5000">
    <subjectdef keys="prod_vmc5000_v1" navtitle="VMC-5000 (Gen 1)"/>
    <subjectdef keys="prod_vmc5000_v2" navtitle="VMC-5000 (Gen 2)">
      <subjectdef keys="prod_vmc5000_v2_spindle" 
                  navtitle="VMC-5000v2 with Speed Spindle"/>
    </subjectdef>
  </subjectdef>
</subjectdef>
```

Lifecycle-aware taxonomy helps retire content for discontinued models.

---

## Pitfall: Too Many Top-Level Categories

**Bad (11 top-level peers):**
```
root
├── audience
├── product
├── content-type
├── doc-status
├── hazard
├── skill
├── language
├── region
├── department
├── certification
└── revision
```

**Good (categorized):**
```
root
├── content-classification (audience, content-type, doc-status)
├── product-lifecycle (product, revision, certification)
├── safety-compliance (hazard, region)
└── people (skill, department, language)
```

---

## Pitfall: Keys That Are Too Short

| Bad Key | Good Key |
|---------|----------|
| `op` | `aud_operator` |
| `vmc5` | `prod_cnc_vmc5000` |
| `d` | `status_draft` |
| `t1` | `skill_intermediate` |

Keys are **machine-readable identifiers**. Future-proof them by being descriptive within reasonable length (20-50 characters).

---

## Pitfall: Circular Taxonomy References

```xml
<!-- WRONG: Circular reference -->
<subjectdef keys="machining">
  <hasRelated>
    <subjectdef keyref="milling"/>  <!-- where milling has ->
      <hasRelated><subjectdef keyref="machining"/></hasRelated> -->
  </hasRelated>
</subjectdef>
```

Circular references cause infinite loops in facet generation and browser rendering. Always keep taxonomy as a DAG (directed acyclic graph).

---

## Pitfall: Taxonomy Without Maintenance Plan

A subject scheme is a living document. Without a plan:

- New products don't get taxonomy entries
- Deprecated values stay forever
- Taxonomy drifts from reality

**Maintenance plan essentials:**
- Quarterly taxonomy review meeting
- Taxonomy change request form
- Version-controlled subject scheme, semantic versioning
- Automated drift detection script

---

<!-- _class: divider -->

# Part 9: Advanced Topics

---

## Combining Multiple Subject Schemes

```xml
<!-- master.subjectscheme.ditamap -->
<subjectScheme>
  <title>Master Manufacturing Taxonomy</title>
  
  <!-- Include sub-schemes via topicref with format="ditamap" -->
  <topicref href="audience.subjectscheme.ditamap" 
            format="ditamap" processing-role="resource-only"/>
  <topicref href="product.subjectscheme.ditamap" 
            format="ditamap" processing-role="resource-only"/>
  <topicref href="content-type.subjectscheme.ditamap" 
            format="ditamap" processing-role="resource-only"/>
  <topicref href="hazard-severity.subjectscheme.ditamap" 
            format="ditamap" processing-role="resource-only"/>
</subjectScheme>
```

Each sub-scheme owned by a different team. Master merges them for validation.

---

## Subject Scheme Extension Points

Allow local extensions without modifying the base taxonomy:

```xml
<!-- base taxonomy -->
<subjectdef keys="all_products">
  <subjectdef keys="prod_standard">
    <!-- Base products defined here -->
  </subjectdef>
  <!-- Extension point marker -->
  <subjectdef keys="prod_local_extensions" 
              navtitle="[LOCAL EXTENSIONS - Add region-specific products here]">
    <!-- Empty intentionally - to be extended by local schemes -->
  </subjectdef>
</subjectdef>
```

---

## Programmatic Taxonomy Generation

```python
# generate-taxonomy.py
# Generate subject scheme from product lifecycle database

import xml.etree.ElementTree as ET

def generate_product_taxonomy(products_csv):
    """Generate product subject scheme from PLM export."""
    
    subjectScheme = ET.Element('subjectScheme')
    root = ET.SubElement(subjectScheme, 'subjectdef', 
                         keys='all_products', navtitle='Product Lines')
    
    for product in parse_csv(products_csv):
        family = ET.SubElement(root, 'subjectdef', 
                               keys=f"prod_{product.family_code}",
                               navtitle=product.family_name)
        for model in product.models:
            ET.SubElement(family, 'subjectdef',
                         keys=f"prod_{product.family_code}_{model.code}",
                         navtitle=f"{model.name} ({model.code})")
    
    # Add enumerationdef
    enumdef = ET.SubElement(subjectScheme, 'enumerationdef')
    ET.SubElement(enumdef, 'attributedef', name='product')
    ET.SubElement(enumdef, 'subjectdef', keyref='all_products')
    
    return ET.tostring(subjectScheme, encoding='unicode', method='xml')

# Schedule: run nightly from PLM database
```

---

## Schematron Extensions Beyond Basic Validation

```xml
<!-- custom-validation.sch -->
<schema xmlns="http://purl.oclc.org/dsdl/schematron">
  
  <!-- Rule: If hazard=danger, must have hazardstatement -->
  <pattern id="manufacturing-safety-rules">
    <rule context="*[@otherprops='haz_danger']">
      <assert test="ancestor::*//hazardstatement" flag="error">
        Topics marked haz_danger MUST contain a hazardstatement element
      </assert>
    </rule>
  </pattern>
  
  <!-- Rule: Published topics must have audience assigned -->
  <pattern id="publishing-rules">
    <rule context="*[@otherprops='status_published']">
      <assert test="@audience" flag="error">
        Published topics MUST have an audience attribute
      </assert>
    </rule>
  </pattern>
  
  <!-- Rule: Operator tasks must reference skill level -->
  <pattern id="skill-rules">
    <rule context="*[@audience='aud_operator']">
      <assert test="@otherprops[contains(., 'skill_')]" flag="warning">
        Operator-targeted content should specify required skill level
      </assert>
    </rule>
  </pattern>
  
</schema>
```

---

## Taxonomy Visualization with Graphviz

```python
# taxonomy-viz.py - Generate Graphviz DOT from subject scheme
# pip install graphviz lxml

import graphviz
from lxml import etree

def visualize_taxonomy(scheme_file, output='taxonomy'):
    tree = etree.parse(scheme_file)
    dot = graphviz.Digraph(comment='Manufacturing Taxonomy')
    
    def add_node(parent_key, elem):
        key = elem.get('keys', '')
        label = elem.get('navtitle', key)
        if key and parent_key:
            dot.edge(parent_key, key)
        for child in elem:
            add_node(key if key else parent_key, child)
    
    root = tree.getroot()
    add_node(None, root)
    dot.render(output, format='png', cleanup=True)
    return f"Taxonomy graph saved to {output}.png"

visualize_taxonomy('schemes/manufacturing-taxonomy.subjectscheme.ditamap')
```

---

## Subject Scheme in CCMS (Component CMS)

When using a CCMS like Tridion Docs, Ixiasoft, or Astoria:

```xml
<!-- CCMS-managed subject scheme -->
<subjectScheme>
  <title>CCMS-Aware Taxonomy</title>
  
  <subjectdef keys="all_products">
    <!-- Auto-populated from CCMS product hierarchy -->
    <subjectdef keys="prod_vmc5000" navtitle="VMC-5000">
      <!-- CMS metadata links -->
      <data name="cms-object-id" value="OBJ-50001"/>
      <data name="cms-version" value="3.2"/>
      <data name="cms-lifecycle" value="active"/>
    </subjectdef>
  </subjectdef>
</subjectScheme>
```

CCMS can auto-sync product taxonomy with the PLM/PDM system.

---

<!-- _class: divider -->

# Part 10: Summary & Next Steps

---

<!-- _class: keypoint -->

## Key Takeaways

1. **Subject schemes are the taxonomy backbone** — they ensure every topic carries consistent, validatable metadata
2. **enumerationdef binds taxonomy to attributes** — without it, subjectdef is just documentation
3. **Hierarchical taxonomies enable progressive disclosure** — filter at any level and include children
4. **Integrate validation into CI/CD** — catch taxonomy violations before they reach production
5. **Treat subject schemes as living documents** — quarterly reviews, version control, change management

---

## Manufacturing-Specific Benefits

| Benefit | Impact |
|---------|--------|
| Consistent safety classification | Zero audit findings for mislabeled hazards |
| Product-line filtering | Users see only content for their machine |
| Role-based content delivery | Operators don't wade through engineering specs |
| Lifecycle tracking | Obsolete content automatically hidden |
| Translation scoping | Filter by product/audience to compute word counts |

---

## What Modules Use This Knowledge

| Upstream (Prerequisites) | Downstream (Depends On) |
|--------------------------|------------------------|
| DITA Novice: Introduction to DITA | PROF-04: Bookmap & PDF |
| DITA Professional 01: Advanced Topics | PROF-05: Localization |
| DITA Professional 02: Maps & Linking | PROF-06: Metadata Governance |

---

## Self-Check Quiz

1. What element binds subjectdef values to DITA attributes?
2. How does hierarchical filtering work with nested subjectdef?
3. What is the difference between `hasKind` and `hasNarrower`?
4. How do you reference a subject scheme from a production DITA map?
5. What naming convention is recommended for subjectdef keys?
6. How do you handle taxonomy changes that affect existing content?
7. What is the maximum recommended taxonomy depth?
8. How do you validate subject scheme compliance in CI/CD?

---

## Answers

1. `enumerationdef` with `attributedef`
2. Selecting a parent value automatically includes all descendants
3. `hasKind` = type-of, `hasNarrower` = generic subcategory
4. `<?dita-scheme scheme="path/to/scheme.ditamap"?>` processing instruction
5. `{domain}_{category}_{subcategory}_{specific}` (e.g., `prod_cnc_vmc5000`)
6. Deprecate old values, add new, update affected topics, track in change log
7. 4 levels maximum
8. DITA-OT subject-scheme transtype in CI pipeline, fail build on violations

---

## Additional Resources

- OASIS DITA 1.3 Specification: Subject Scheme
- DITA-OT Documentation: Subject Scheme Processing
- oXygen XML Editor: Configuring Subject Scheme Maps
- Manufacturing Documentation Framework: `schemes/` directory
- Schematron ISO Standard (ISO/IEC 19757-3)

---

<!-- _class: title -->

# End of Module
## Subject Scheme & Controlled Values

**DITA Professional | Manufacturing Documentation**

**Next Module: PROF-04 — Bookmap & PDF**
