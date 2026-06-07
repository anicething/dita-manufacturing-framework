---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Content Reuse Strategies
## Module 2 — DITA Professional (进阶)

**DITA Manufacturing Framework**
*Professional Level Training*

---

## Agenda

| Section | Topic | Slides |
|---------|-------|--------|
| 1 | Why Reuse Matters | 3-4 |
| 2 | Conref: Content References | 5-7 |
| 3 | Conrefend: Reusable Ranges | 8 |
| 4 | Keyref: Key-Based Indirection | 9-11 |
| 5 | Reuse Patterns in the Framework | 12-14 |
| 6 | Conditional Processing | 15-16 |
| 7 | Best Practices & Anti-Patterns | 17-18 |
| 8 | Hands-on Exercise | 19-20 |

---

## Why Reuse Matters

The framework targets **35-40% content reuse** across all deliverables. Why this number?

| Metric | Without Reuse | With 35% Reuse | Improvement |
|--------|---------------|----------------|-------------|
| Authoring time | 1,200 hours | 780 hours | **35% reduction** |
| Translation cost | $48,000/yr | $31,200/yr | **35% reduction** |
| Consistency errors | ~120/yr | ~40/yr | **67% reduction** |
| Update propagation | Manual (3-5 days) | Automatic (minutes) | **99% faster** |

> Based on framework pilot: 4 machine models, 3 languages, 847 topics

---

## The Reuse Landscape

DITA provides three primary reuse mechanisms, each with distinct use cases.

| Mechanism | Scope | Coupling | When to Use |
|-----------|-------|----------|-------------|
| **conref** | Element-level | Direct reference (file+id) | Identical content across topics |
| **conrefend** | Element range | Direct reference | Continuous block reuse |
| **keyref** | Indirect by key | Loose (key defined in map) | Product-specific or variable content |

Two additional mechanisms for completeness:
- **conkeyref**: conref via key (indirect)
- **conrefpush**: push content into target (rare, avoid if possible)

---

<!-- _class: divider -->

# Conref: Content References

---

## Conref Syntax

```
conref="path/to/file.dita#topicid/elementid"
```

**Example**: Pull a shared warning into a task topic.

```xml
<!-- Topic consuming the reference -->
<task id="task-coolant-change">
  <title>Changing Coolant</title>
  <taskbody>
    <steps>
      <step><cmd>LOTO the machine.</cmd></step>
      <!-- more steps -->
    </steps>
    <result/>
    <postreq>
      <note conref="shared/warnings.dita#warn-loto/loto-note"/>
    </postreq>
  </taskbody>
</task>
```

---

## Conref Rules (Critical for Validation)

| Rule | Description | Error if Violated |
|------|-------------|-------------------|
| Same DTD | Source and target must use same DOCTYPE | Validation error |
| Same element type | Cannot conref a `<note>` into a `<p>` | Parse failure |
| No circular references | A → B → A chains forbidden | Infinite loop |
| Valid ID | Referenced element must have @id | Unresolved reference |
| File accessible | Source file must exist in the map context | Build failure |

```xml
<!-- ILLEGAL: Different element types -->
<p conref="safety.dita#haz-001/messagepanel"/>
<!-- messagepanel is not a <p>, this will fail -->

<!-- LEGAL: Same element type -->
<note conref="safety.dita#haz-001/safety-note"/>
<!-- safety-note is a <note>, this works -->
```

---

## Real Example: Shared Warning Fragment

**Source file**: `shared/safety-warnings.dita`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<reference id="safety-warnings" xml:lang="en-US">
  <title>Shared Safety Warnings</title>
  <refbody>
    <section id="elec-hazard">
      <title>Electrical Hazard</title>
      <note id="elec-warning" type="warning">
        <p>Disconnect main power (480V 3-phase)
        before servicing. Verify zero energy
        state using calibrated multimeter.
        Arc flash hazard: wear Category 3 PPE
        per NFPA 70E.</p>
      </note>
    </section>
    <section id="chem-hazard">
      <title>Chemical Hazard</title>
      <note id="chem-warning" type="warning">
        <p>DURA-COOL 4800 contains triethanolamine.
        Avoid skin contact. Wear nitrile gloves
        and safety goggles. See SDS-DC4800 in
        the Safety Data Sheet binder.</p>
      </note>
    </section>
  </refbody>
</reference>
```

---

## Consuming the Shared Warning

```xml
<!-- Task: Electrical Cabinet Maintenance -->
<task id="task-cabinet-maintain">
  <title>Electrical Cabinet Preventive Maintenance</title>
  <prolog>
    <metadata>
      <data name="safety-refs" value="hazard-electrical"/>
    </metadata>
  </prolog>
  <taskbody>
    <prereq>
      <note conref="shared/safety-warnings.dita
        #safety-warnings/elec-warning"/>
    </prereq>
    <steps>
      <step><cmd>Verify LOTO applied at
        main disconnect.</cmd></step>
      <step><cmd>Open cabinet door using
        panel key PN KEY-CAB-001.</cmd></step>
      <!-- ... -->
    </steps>
  </taskbody>
</task>
```

---

<!-- _class: divider -->

# Conrefend: Reusable Content Ranges

---

## Conrefend Syntax

`conrefend` defines the end of a conref range, enabling reuse of a sequence of sibling elements.

```xml
<!-- Source: shared/steps-pool.dita -->
<steps id="startup-sequence">
  <step id="startup-1"><cmd>Verify E-Stop released.</cmd></step>
  <step id="startup-2"><cmd>Turn main disconnect to ON.</cmd></step>
  <step id="startup-3"><cmd>Press <uicontrol>Control ON</uicontrol>.</cmd></step>
  <step id="startup-4"><cmd>Wait for HMI boot (∼45 sec).</cmd></step>
  <step id="startup-5"><cmd>Verify no active alarms on HMI.</cmd></step>
</steps>

<!-- Consumer: reuse steps 2 through 4 -->
<step conref="shared/steps-pool.dita#pool-startup/startup-2"
      conrefend="shared/steps-pool.dita#pool-startup/startup-4"/>
```

> Use sparingly: conrefend is harder to trace than single-element conref. Prefer wrapping reusable sequences in a parent element when possible.

---

<!-- _class: divider -->

# Keyref: Key-Based Indirection

---

## Keyref vs Conref

| Feature | conref | keyref |
|---------|--------|--------|
| Resolution | Direct file+ID reference | Indirect via key defined in map |
| Change scope | Edit each referencing topic | Edit one key definition in map |
| Use case | Static shared content | Product-variable content |
| Example | Same warning everywhere | Different product name per map |

```xml
<!-- conref: hard-coded to specific file -->
<note conref="shared/warnings.dita#warn-loto/loto-note"/>

<!-- keyref: resolved through key definition -->
<ph keyref="product-name"/>
```

---

## Keyref: Map Definition

**In the ditamap** (`MC-5000-operator-manual.ditamap`):

```xml
<map id="dr5000-op-manual">
  <title>MC-5000 Operator Manual</title>

  <!-- Key definitions -->
  <keydef keys="product-name">
    <topicmeta>
      <keywords>
        <keyword>MC-5000 Precision CNC Lathe</keyword>
      </keywords>
    </topicmeta>
  </keydef>

  <keydef keys="product-code">
    <topicmeta>
      <keywords>
        <keyword>DR5K-MK4</keyword>
      </keywords>
    </topicmeta>
  </keydef>

  <keydef keys="support-phone">
    <topicmeta>
      <keywords>
        <keyword>+1-800-555-0199</keyword>
      </keywords>
    </topicmeta>
  </keydef>

  <!-- Topic references -->
  <topicref href="topics/introduction.dita"/>
  <topicref href="topics/safety.dita"/>
  <!-- ... -->
</map>
```

---

## Keyref: Topic Consumption

**In any topic** (works across all DR-series by changing map):

```xml
<topic id="welcome" xml:lang="en-US">
  <title>Welcome</title>
  <body>
    <p>Thank you for purchasing the
    <ph keyref="product-name"/> (model
    <ph keyref="product-code"/>).</p>

    <p>This machine is a precision CNC lathe
    designed for high-volume manufacturing
    of automotive drivetrain components.</p>

    <p>For technical support, call
    <ph keyref="support-phone"/>.</p>

    <note type="tip">The <ph keyref="product-code"/>
    model supports up to 12 tool stations and
    sub-spindle for back-side machining.</note>
  </body>
</topic>
```

> Same topic renders differently for MC-5000, MC-8000, DR-2000 — just change the map.

---

## Keyref: Link Targets

Keyref also works for links and cross-references.

```xml
<!-- Map definition: link to glossary -->
<keydef keys="gloss-loto"
    href="glossary/gloss-loto.dita"/>

<!-- Topic usage -->
<p>Always follow
<xref keyref="gloss-loto" format="dita">
Lockout/Tagout</xref> procedure before
entering the machine envelope.</p>
```

**Benefits**:
- If glossary file moves, update the map, not every topic
- Different maps can point to different language versions
- Conkeyref = conref via key: `conkeyref="safety-notes/loto-warning"`

---

<!-- _class: divider -->

# Reuse Patterns in the Framework

---

## Reuse Distribution Target

The framework targets a strategic reuse breakdown:

```
Total Content: 100%
├── Shared Topics (30%)       ← Identical across products
│   ├── Company info, legal
│   ├── Contact & support
│   ├── Safety standards
│   └── Glossary
├── Content Fragments (20%)   ← Reused across topics
│   ├── Common steps (startup, LOTO, shutdown)
│   ├── Warnings & hazards
│   └── Examples & notes
└── Product-Specific (50%)    ← Unique per product
    ├── Machine specifications
    ├── Part-specific procedures
    └── Custom configurations
```

---

## Pattern 1: Shared Topics (30%)

Content that does not change between products.

```
shared/
├── company/
│   ├── about-us.dita
│   ├── contact-support.dita
│   └── warranty-terms.dita
├── legal/
│   ├── terms-of-use.dita
│   └── export-compliance.dita
├── safety/
│   ├── general-safety.dita
│   ├── safety-warnings.dita
│   └── hazard-statements/
│       ├── hazard-electrical.dita
│       ├── hazard-flying-chips.dita
│       └── hazard-coolant-mist.dita
└── glossary/
    ├── gloss-loto.dita
    ├── gloss-zero-energy.dita
    ├── gloss-ppe.dita
    └── ... (50+ terms)
```

---

## Pattern 2: Content Fragments (20%)

Reusable building blocks embedded in product-specific topics.

```xml
<!-- shared/steps/common-procedures.dita -->
<steps id="loto-die-side">
  <step id="loto-1"><cmd>Press E-Stop on operator panel.</cmd></step>
  <step id="loto-2"><cmd>Turn main disconnect to OFF.</cmd></step>
  <step id="loto-3"><cmd>Apply personal lock to disconnect.</cmd></step>
  <step id="loto-4"><cmd>Verify zero energy at test points
    TP1-TP4 using calibrated multimeter.</cmd></step>
</steps>
```

**Usage in product task**:
```xml
<step conref="shared/steps/common-procedures.dita
  #proc-loto/loto-1" conrefend="shared/steps/common-procedures.dita
  #proc-loto/loto-4"/>
```

---

## Pattern 3: Product-Specific (50%)

Content unique to each machine model. Uses keyref for variable elements.

```
MC-5000/
├── dr5000-operator-manual.ditamap
├── specifications/
│   ├── spec-dr5000-technical.dita
│   └── spec-dr5000-dimensions.dita
├── tasks/
│   ├── task-dr5000-tool-change.dita
│   └── task-dr5000-calibrate.dita
└── troubleshooting/
    ├── alarm-700016-coolant-low.dita
    └── alarm-800023-spindle-temp.dita

MC-8000/
├── dr8000-operator-manual.ditamap  ← Different map, same keys
├── specifications/                  ← Unique content
├── tasks/                           ← Unique content
└── troubleshooting/                 ← Unique content
```

---

<!-- _class: divider -->

# Conditional Processing

---

## Condition Attributes

DITA provides three primary conditional processing attributes:

```xml
<!-- Filter by product -->
<p product="MC-5000 MC-8000">This applies to MC-5000 and MC-8000 only.</p>
<p product="DR-2000">This applies to DR-2000 only.</p>

<!-- Filter by audience -->
<step audience="maintenance">
  <cmd>Calibrate using oscilloscope at test point TP7.</cmd>
</step>
<step audience="operator">
  <cmd>Press <uicontrol>Auto Calibrate</uicontrol> on HMI.</cmd>
</step>

<!-- Filter by platform -->
<note platform="win">Siemens S7-1500 configuration software requires Windows 10/11.</note>
<note platform="linux">Edge gateway runs Ubuntu 22.04 LTS.</note>
```

---

## DITAVAL: Filter Definition Files

DITAVAL files define which values to include or exclude during transformation.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<val>
  <!-- Include only MC-5000 content -->
  <prop att="product" val="MC-5000" action="include"/>
  <prop att="product" val="MC-8000" action="exclude"/>
  <prop att="product" val="DR-2000" action="exclude"/>

  <!-- Exclude maintenance-audience content for operator manual -->
  <prop att="audience" val="operator" action="include"/>
  <prop att="audience" val="maintenance" action="exclude"/>
  <prop att="audience" val="engineer" action="exclude"/>

  <!-- Always exclude draft content from publication -->
  <prop att="document-status" val="draft" action="exclude"/>
  <prop att="document-status" val="review" action="exclude"/>
</val>
```

**Usage**: `dita -i map.ditamap -f html5 -filter=dr5000-operator.ditaval`

---

## Conditional Processing Best Practices

| Practice | Why |
|----------|-----|
| Default to include | Unexpected exclusion is worse than extra content |
| Use multiple attributes | `product="MC-5000" audience="operator"` — both must match |
| Avoid deep nesting | Conditional within conditional is hard to test |
| Test all filter combinations | CI/CD pipeline should build every variant |
| Document DITAVAL purpose | Each `.ditaval` file should have a header comment |
| Keep DITAVAL with map | `map.ditamap` + `map-operator.ditaval` + `map-maintenance.ditaval` |

---

<!-- _class: divider -->

# Best Practices & Anti-Patterns

---

## When to Reuse vs. When to Write Unique

| Scenario | Decision | Rationale |
|----------|----------|-----------|
| Identical content, all products | **Reuse** (conref) | Single source of truth |
| Same structure, different values | **Keyref** | Product-specific via map |
| Similar but not identical | **Write unique** | Slight differences cause maintenance pain |
| Warning/hazard statement | **ALWAYS reuse** (conref) | Safety content must be identical |
| Product specifications | **ALWAYS unique** | Values differ; conref adds complexity |
| Glossary terms | **Reuse** (keyref link) | Term defined once, linked everywhere |

---

## Anti-Patterns: What to Avoid

### Anti-Pattern 1: Deep Conref Chains

```
A conrefs → B conrefs → C conrefs → D
```
Each hop adds build complexity and debugging difficulty.

**Fix**: Flatten chains. If D is the ultimate source, reference D directly.

### Anti-Pattern 2: Semantic Override via Conref

```xml
<!-- BAD: conref pulls content that contradicts the topic context -->
<warning conref="shared/notes.dita#notes/gentle-reminder"/>

<!-- GOOD: Define an actual warning in shared warnings -->
<warning conref="shared/warnings.dita#warn-elec/loto-required"/>
```

---

## Anti-Patterns (Continued)

### Anti-Pattern 3: Over-Reuse of Trivial Content

```xml
<!-- WASTEFUL: conref for a single word adds indirection -->
<ph conref="shared/terms.dita#terms/label-save">Save</ph>

<!-- BETTER: Just write it (or use keyref if it varies) -->
<ph>Save</ph>
```

### Anti-Pattern 4: Conref Without Validation

Always validate conref targets exist before commit:

```bash
# Pre-commit hook check
dita --input=map.ditamap --format=validate \
     --filter=review.ditaval
```

> Rule of thumb: If the reusable content is shorter than the conref attribute, write it inline.

---

<!-- _class: exercise -->

## Exercise 4: Content Reuse

**Part 1 — Create a Shared Warning Fragment**:

Create a file `shared/safety-fragments.dita` containing a `<note type="warning">` for "High Pressure Hydraulic Fluid" hazard. Include:
- Consequence: injection injury
- How to avoid: check pressure gauge, wear face shield, bleed pressure before disconnecting

**Part 2 — Conref into Two Topics**:

1. Create task `task-hydraulic-filter-change.dita` that conrefs the warning
2. Create task `task-hose-inspection.dita` that conrefs the same warning

**Part 3 — Keyref Product Name**:

Define key "machine-series" in a ditamap and use `<ph keyref="machine-series"/>` in a topic.

**Time**: 20 minutes

---

<!-- _class: keypoint -->

## Key Takeaways

1. **35-40% reuse target** reduces authoring cost, translation cost, and errors
2. **Conref** for identical static content: `conref="file.dita#topicid/elementid"`
3. **Conrefend** for reusable element ranges (use sparingly)
4. **Keyref** for variable content: define in map, consume in topic, change per product
5. **Three reuse patterns**: Shared topics (30%), content fragments (20%), product-specific (50%)
6. **Conditional processing**: `@product`, `@audience`, `@platform` + DITAVAL filter files
7. **Avoid**: deep conref chains, semantic overrides, over-reuse of trivial content
8. **Always validate** conref targets before commit — broken references fail the build
