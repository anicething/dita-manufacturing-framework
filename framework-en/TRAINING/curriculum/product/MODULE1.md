---
marp: true
theme: uncover
class:
  - lead
  - invert
paginate: true
header: "Example Manufacturing — MC-5000 Training Program"
footer: "Confidential — For Authorized Personnel Only"
---

<!-- _class: lead invert -->
# MC-5000 Vertical Machining Center
## Operator &amp; Maintenance Training Program

**Module 1: Documentation &amp; Safety Awareness**

Example Manufacturing Co., Ltd.
Version 2.1.A — June 2026

---

<!-- _class: default -->
## Course Objectives

By the end of this training, you will be able to:

- **Navigate** the MC-5000 documentation set
- **Identify** the three-layer safety architecture
- **Interpret** Concept, Task, and Reference topics
- **Locate** technical specifications quickly
- **Apply** the safety interlock procedures
- **Follow** the correct change-request workflow

> **Duration:** 4 hours (including practical exercises)

---

## MC-5000 Product Overview

| Parameter | Value |
|-----------|-------|
| Machine type | Vertical Machining Center (VMC) |
| Spindle speed | 12,000 RPM (opt. 15,000) |
| Table size | 1,000 × 550 mm |
| Max. table load | 800 kg |
| Positioning accuracy | ±5 µm (ISO 230-2) |
| Controller | Siemens SINUMERIK ONE |
| Safety standard | ISO 13849-1 Cat.3 / PLd |

> Full specs: see **MC-5000 Technical Specifications** (Reference topic)

---

## Documentation Structure

Our documentation uses the **DITA** standard (Darwin Information Typing Architecture).

Three topic types you will encounter:

| Type | Purpose | Example |
|------|---------|---------|
| **Concept** | Explains *what* something is | Safety system overview |
| **Task** | Shows *how* to do something | Spindle alignment procedure |
| **Reference** | Lists *facts and data* | Technical specifications |

> Documents are organized in a **map** (ditamap) that structures chapters and cross-references.

---

## Finding the Right Information

```
MC-5000 Product Documentation Map
├── Chapter 1: Safety
│   ├── Safety System Overview ➔ **Concept**
│   └── Emergency Stop Procedures ➔ **Task**
├── Chapter 2: Maintenance
│   ├── Spindle Laser Alignment ➔ **Task**
│   └── Lubrication Schedule ➔ **Reference**
├── Chapter 3: Technical Reference
│   └── Technical Specifications ➔ **Reference**
└── Chapter 4: Operations
    └── Basic Operation ➔ **Task**
```

> **Tip:** The ditamap is your navigation hub — always start here.

---

## Safety System — Three-Layer Architecture

<!-- _class: grid -->
<div class="grid">

<div>

### Layer 1: Physical
- Interlocked doors
- Light curtains
- E-Stop buttons
- Chip guards

</div>

<div>

### Layer 2: Control
- SINUMERIK ONE safety PLC
- Dual-channel redundant I/O
- Door / spindle / axis monitoring

</div>

<div>

### Layer 3: Software
- Real-time diagnostics
- Safe torque off (STO)
- Safe brake control (SBC)

</div>

</div>

> Compliance: ISO 13849-1 Category 3 / PLd

---

## Safety Interlock Zones

<!-- _class: small-table -->
| Zone | Coverage | Trigger Action | Reset |
|------|----------|---------------|-------|
| **Zone 1** | Main door &amp; windows | Immediate spindle stop + axis hold | HMI manual reset |
| **Zone 2** | Light curtain (loading) | Rapid deceleration ≤ 50 ms | Auto-reset |
| **Zone 3** | Chip conveyor panel | Conveyor motor cutoff | Panel manual reset |
| **Zone 4** | Coolant tank hatch | Coolant pump shutdown + tagout | Key-lock reset |

> **⚠️ Never override a safety interlock.** Tagout any malfunction immediately and report to the shift supervisor.

---

## Reading a Concept Topic

Example: *MC-5000 Safety System Overview*

```
concept id="dr5000-safety-system"
├── title: "MC-5000 Safety System Overview"
├── shortdesc: (one-sentence summary)
├── prolog: (metadata — audience, product, standards)
└── conbody:
    ├── section: Safety Architecture
    │   └── dl: Three-layer description
    ├── section: Safety Interlock Zones
    │   └── simpletable: Zone details
    └── section: Regulatory Compliance
        └── ul: ISO standards list
```

> **Concept** = the "what" and the "why". Use it to build understanding.

---

## Reading a Task Topic

Example: *Spindle Laser Alignment*

```
task id="spindle-alignment-procedure"
├── title: "Aligning the MC-5000 Spindle"
├── prereq: (tools, safety conditions)
└── taskbody:
    └── steps:
        ├── step 1: Prepare machine
        ├── step 2: Mount laser target
        ├── step 3: Perform measurement
        ├── step 4: Adjust spindle (with decision table)
        └── step 5: Verify alignment
```

> **Task** = the "how". Follow steps in order. Each step has a **result** to confirm success.

---

## Reading a Reference Topic

Example: *MC-5000 Technical Specifications*

```
reference id="dr5000-specs"
├── title: "MC-5000 Technical Specifications"
└── refbody:
    ├── Spindle Specifications
    ├── Axis Travel & Feed Rates
    ├── Worktable
    ├── ATC (Automatic Tool Changer)
    ├── Positioning Accuracy
    ├── Utility Requirements
    └── Available Options
```

> **Reference** = the "data". Look up specs, tolerances, and requirements. Designed for quick scanning.

---

## Practical Exercise (15 min)

**Scenario:** You need to perform spindle alignment on MC-5000 serial number MC-5000-2210.

1. **Find** the relevant Task topic in the ditamap
2. **Read** the prerequisites — what tools are needed?
3. **Identify** the three possible outcomes of the measurement
4. **Locate** the safety concept topic cross-referenced in the task

> Discuss your findings with a partner. Use the printed documentation sheets provided.

---

## Documentation Change Process

If you find an error or outdated information:

```
1. Open a GitHub Issue in the documentation repository
2. Select the change type:
   - Minor: typo, formatting
   - Moderate: value update (e.g., torque spec)
   - Major: structural change
   - Critical: safety/compliance
3. Assign to the content owner (see ownership matrix)
4. Await review — typically 3-5 business days
5. Changes are published in the next CI/CD cycle
```

> **SLA:** Minor changes < 1 day, Critical changes < 48 hours (with mandatory wait)

---

## Knowledge Check

1. What are the **three DITA topic types** and when do you use each?
2. What does **Zone 2** (light curtain) do when triggered?
3. If the spindle offset is ±5 µm after measurement, what action is required?
4. Where do you find the **maximum table load** for MC-5000?
5. Who approves a **Type 3 (Major)** documentation change?

> Write your answers. We will review them together in 10 minutes.

---

<!-- _class: lead invert -->
## Answers

1. **Concept** (what/why), **Task** (how), **Reference** (data)
2. Rapid axis deceleration within 50 ms — auto-resets
3. Minor misalignment — adjust using fine screws (1/4 turn increments)
4. MC-5000 Technical Specifications → Worktable section (800 kg)
5. SME (Subject Matter Expert) recommends, Engineering Director approves

---

## Summary & Next Steps

**You have learned:**
- How to navigate DITA documentation
- The three-layer safety architecture
- How to read Concept / Task / Reference topics
- The documentation change process

**Next training session:**
- Module 2: Hands-on Maintenance Procedures
- Bring your PPE and printed reference cards

> [ ] Complete the knowledge check quiz
> [ ] Review the Safety System Concept topic before Module 2
> [ ] Schedule Module 2 with your supervisor

---

<!-- _class: lead invert -->
## Thank You

**Example Manufacturing Co., Ltd.**

Training Department — Gao Ling
Documentation Team — Li Na

*"Safe operation starts with knowing your documentation."*

---

## Appendix: Quick Reference Card

| I Need To... | Go To... | Topic Type |
|-------------|----------|------------|
| Understand how the safety system works | Safety System Overview | Concept |
| Align the spindle | Spindle Laser Alignment | Task |
| Check max. spindle speed | Technical Specifications | Reference |
| Learn the lubrication schedule | Lubrication Schedule | Reference |
| Perform emergency stop | Emergency Stop Procedures | Task |
| Report a documentation error | GitHub Issues → Doc Repo | N/A |
