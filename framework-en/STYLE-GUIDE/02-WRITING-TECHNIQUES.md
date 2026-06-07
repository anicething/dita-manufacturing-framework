# Writing Style & Techniques Guide

**Document ID:** STY-002
**Version:** 1.0
**Last Updated:** 2026-06-07
**Status:** Approved

---

## Table of Contents

1. [Purpose](#1-purpose)
2. [Sentence-Level Craft](#2-sentence-level-craft)
3. [Technical Precision & Clarity](#3-technical-precision--clarity)
4. [Advanced Procedural Writing](#4-advanced-procedural-writing)
5. [Writing by DITA Topic Type](#5-writing-by-dita-topic-type)
6. [Safety & Compliance Writing](#6-safety--compliance-writing)
7. [Audience-Aware Writing](#7-audience-aware-writing)
8. [Reuse-Oriented Writing](#8-reuse-oriented-writing)
9. [Self-Editing & Revision](#9-self-editing--revision)
10. [Before & After Examples](#10-before--after-examples)
11. [Common Mistakes & Fixes](#11-common-mistakes--fixes)

---

## 1. Purpose

### 1.1 What This Guide Covers

This guide is a **companion** to the DITA Style Guide (STY-001). Where STY-001 defines *what* is correct (rules, standards, formatting), this document teaches *how* to write well — the craft, technique, and judgment that distinguish excellent technical documentation from merely correct documentation.

### 1.2 The Goal

The goal is not to constrain creativity but to channel it. Effective technical writing is invisible: readers should absorb the information without noticing the writing itself. Every technique in this guide serves that goal.

### 1.3 Who Should Use This Guide

| Role | Focus |
|------|-------|
| New technical writers | Read cover-to-cover as part of onboarding |
| Experienced writers | Reference for specific techniques, self-editing checklist |
| Reviewers | Use the editing techniques section during peer review |
| Content architects | Apply the reuse-oriented writing principles during library design |

---

## 2. Sentence-Level Craft

### 2.1 The 20-Word Rule

> **One sentence = one instruction. Keep it under 20 words.**

Sentences longer than 20 words force the reader to re-read. Manufacturing documentation readers are often working with equipment — they cannot afford re-reading.

**Weak (38 words):**
> To ensure optimal performance and prevent potential damage to the spindle assembly when operating at high speeds for extended periods, you should always verify that the cooling system is functioning properly before beginning any machining operation.

**Strong (16 words):**
> Before high-speed operation, verify the cooling system is working properly.

**Strong (14 words):**
> Check the cooling system before operating the spindle at high speeds.

### 2.2 Subject-Verb-Object Order

Place the subject first, followed by the verb, then the object. This is the most readable sentence structure.

| Principle | Weak | Strong |
|-----------|------|--------|
| Subject first | "To connect the hose, the coupling must be aligned by the operator." | "Align the coupling, then connect the hose." |
| Verb visible | "If the warning light appears, the system should be shut down immediately after recording the error code." | "If the warning light appears, record the error code, then shut down the system." |
| Object clear | "Configuration of the parameters can be done through the control panel." | "Configure the parameters on the control panel." |

### 2.3 One Clause Per Sentence

Compound sentences (joined by "and," "or," "but," "because") often pack too much information. Split them.

**Instead of:**
> Press the start button and wait until the green indicator lights up, then adjust the feed rate using the dial on the right side of the control panel.

**Write:**
> 1. Press the start button.
> 2. Wait for the green indicator to light up.
> 3. Adjust the feed rate using the dial on the right side of the control panel.

### 2.4 Eliminating Weasel Words

Weasel words create ambiguity. Remove or replace them:

| Word | Problem | Replace With |
|------|---------|-------------|
| **may** | "The system may shut down" — does it or doesn't it? | "will" or "can" (be specific) |
| **might** | Same problem | Specific condition or remove |
| **could** | Speculative | Specific scenario or remove |
| **usually** | Vague frequency | "in most cases" or specific percentage |
| **some** | Quantity unknown | Specific number or range |
| **several** | Imprecise | Specific number |
| **various** | Generic | List them or remove |
| **etc.** | Open-ended | Replace with "such as [examples]" |

**Before:** "The machine may overheat if the coolant level is too low, which could cause damage to several components."

**After (18 words):** "Low coolant causes the machine to overheat. Overheating damages the spindle, bearings, and chuck."

### 2.5 Strong Verbs vs. Noun Phrases

Noun phrases (nominalizations) bury the action in a noun, forcing the verb to be a weak helper like "perform," "conduct," or "do."

| Weak Noun Phrase | Strong Verb |
|-----------------|-------------|
| Perform an analysis of | Analyze |
| Conduct an investigation of | Investigate |
| Make a determination of | Determine |
| Give consideration to | Consider |
| Effect a change to | Change |
| Provide a response | Respond |
| Have a requirement for | Require |
| Is in compliance with | Complies with |

**Before:** "The operator should perform a verification of the alignment before making an adjustment to the feed rate."

**After:** "Verify the alignment before adjusting the feed rate."

---

## 3. Technical Precision & Clarity

### 3.1 Writing Measurable Instructions

Vague instructions cause errors. Always specify measurable criteria.

| Vague | Measurable |
|-------|-----------|
| "Tighten the bolt firmly." | "Tighten the bolt to 45 N·m." |
| "Wait for the system to be ready." | "Wait until the status indicator shows green." |
| "Align the components properly." | "Align the shaft within 0.05 mm of the marking." |
| "Set the temperature appropriately." | "Set the temperature to 180 °C ± 5 °C." |
| "Clean the filter regularly." | "Clean the filter every 200 operating hours." |

### 3.2 Quantifying Conditions

When a condition determines an action, make the condition measurable:

**Unclear:** "If the material is too thick, reduce the feed rate."

**Clear:** "If the workpiece thickness exceeds 20 mm, reduce the feed rate to 0.15 mm/revolution."

**Best:** Use a table for multiple conditions:

| Workpiece Thickness | Maximum Feed Rate |
|--------------------|-------------------|
| Up to 10 mm | 0.30 mm/rev |
| 10 mm to 20 mm | 0.20 mm/rev |
| Over 20 mm | 0.15 mm/rev |

### 3.3 Handling Ranges & Tolerances

| Type | Format | Example |
|------|--------|---------|
| Range | X to Y | "Set temperature to 150 °C to 180 °C." |
| Tolerance | X ± Y | "Align to within ±0.02 mm." |
| Minimum | At least X | "Use at least 8 mm diameter bolts." |
| Maximum | No more than X | "Torque no more than 60 N·m." |

### 3.4 Avoiding Ambiguous Pronouns

Pronouns like "it," "this," "that," and "they" often have unclear antecedents.

**Ambiguous:** "Remove the cover plate and inspect the filter. Replace it if it is damaged." *(What is "it" — the cover or the filter?)*

**Clear:** "Remove the cover plate and inspect the filter. Replace the filter if it is damaged."

**Ambiguous:** "The controller sends a signal to the drive unit. This must be configured first." *(What must be configured first — the controller or the drive unit?)*

**Clear:** "Configure the drive unit first, then the controller sends a signal."

### 3.5 Signal Words for Sequence

Help readers navigate procedures with clear sequence signals:

| Signal Word | Meaning | When to Use |
|-------------|---------|-------------|
| **First** | Initial step | Beginning of a sequence |
| **Then** | Next step | Between sequential steps |
| **Next** | Subsequent step | Between steps in a multi-step |
| **Afterward** | Following completion | When timing matters |
| **Simultaneously** | At the same time | Parallel actions |
| **While** | During another action | Overlapping actions |
| **Until** | Continuously until condition met | Conditional duration |
| **Once** | After condition fulfilled | Conditional transition |

**Example with signals:**
1. **First**, isolate the power supply.
2. **Then**, lock the disconnect switch with a padlock.
3. **While** the system is powered down, inspect the wiring harness.
4. **Once** the inspection is complete, remove the padlock.
5. **Finally**, restore power and verify operation.

---

## 4. Advanced Procedural Writing

### 4.1 Complex Procedures: The Decision Table Pattern

Some procedures have branching paths. A decision table prevents confusion:

**Scenario:** Troubleshooting a spindle that will not start

| Condition | Action |
|-----------|--------|
| Power indicator is OFF | Check main power supply (see step 3a) |
| Power indicator is ON, error code is E-001 | Reset spindle drive (see step 3b) |
| Power indicator is ON, error code is E-002 | Check coolant level (see step 3c) |
| Power indicator is ON, no error code | Contact support (see step 3d) |

**Write this as:**

> **Step 3: Diagnose the spindle issue**
>
> 1. Check the power indicator on the control panel.
> 2. Match the indicator state and error code to the table above.
> 3. Follow the corresponding sub-procedure:
>
>    **3a. Power indicator is OFF** — Check main power supply...
>
>    **3b. Error E-001** — Reset spindle drive...
>
>    **3c. Error E-002** — Check coolant level...
>
>    **3d. No error code** — Contact ExampleCorp support...

### 4.2 Consecutive vs. Independent Steps

Mark step types so readers know what depends on what:

| Step Type | Marker | Meaning | Example |
|-----------|--------|---------|---------|
| **Consecutive** | Numbered (1, 2, 3...) | Must follow in order | "1. Remove screws. 2. Lift cover." |
| **Independent** | Bullet (•) | Order does not matter | "• Check oil level • Inspect belts" |
| **Optional** | "If needed" | Skip if not applicable | "If needed, adjust the tension." |
| **Conditional** | "If X, then Y" | Branch based on condition | "If the alarm sounds, press Stop." |

### 4.3 Checking Understanding with Expected Results

Every critical step should include the expected result:

**Without expected result:**
> 1. Press the calibration button.
> 2. Wait for 10 seconds.
> 3. Turn the adjustment screw clockwise.

**With expected result:**
> 1. Press the calibration button.
>    The display shows "CAL" and the indicator light flashes green.
> 2. Wait for 10 seconds.
>    The indicator light stops flashing and stays solid green.
> 3. Turn the adjustment screw clockwise until the reading shows 0.00.
>    The calibration is complete when the display shows "CAL DONE."

### 4.4 Handling Warnings Within Procedures

Integrate warnings at the step where the hazard is relevant — not before, not after:

**Wrong:**
> ⚠️ WARNING: Risk of crushing. Keep hands clear of the clamp area.
>
> 1. Open the safety guard.
> 2. Position the workpiece in the clamp.
> 3. Close the safety guard.
> 4. Start the cycle.

**Right:**
> 1. Open the safety guard.
> 2. Position the workpiece in the clamp.
>    ⚠️ WARNING: Keep hands clear of the clamp area.
>    The clamp closes automatically when the guard is closed.
> 3. Close the safety guard.
> 4. Start the cycle.

### 4.5 Multi-Person Procedures

When a procedure requires two or more operators, label each operator role clearly:

> **Procedure: Lifting the spindle assembly**
>
> **Operators:** Two (Operator A and Operator B)
>
> 1. **Both operators:** Position yourselves at opposite ends of the spindle.
> 2. **Operator A:** Insert the lifting bar into the forward lifting point.
> 3. **Operator B:** Insert the lifting bar into the rear lifting point.
> 4. **Both operators:** Lift simultaneously on the count of three.
> 5. **Both operators:** Move the spindle to the service cart.
> 6. **Both operators:** Lower simultaneously.

---

## 5. Writing by DITA Topic Type

### 5.1 Concept Topics (40% of Content)

**Purpose:** Explain *what* something is and *why* it matters.

**Structure:**
- Short definition (1-2 sentences)
- Key characteristics (3-5 bullet points)
- Optional diagram or illustration
- How it relates to other components

**Techniques:**
- Start with the simplest possible definition
- Use analogies for complex mechanisms
- Show, don't just tell — use diagrams
- Connect to the user's goal: "The cooling system keeps the spindle at operating temperature to maintain precision."

**Checklist:**
- [ ] Can a new operator understand the concept without prior knowledge?
- [ ] Does the concept explain both "what" and "why"?
- [ ] Are there no step-by-step instructions in a concept topic?
- [ ] Is the concept linked to related tasks?

### 5.2 Task Topics (40% of Content)

**Purpose:** Explain *how* to do something.

**Structure:**
- Brief context (when to do this task)
- Prerequisites (tools, permissions, conditions)
- Steps (numbered, one action per step)
- Expected results
- Post-requisites (what to do next)

**Techniques:**
- Start every step with an imperative verb
- Include expected results for critical steps
- Keep prerequisites minimal — only what's strictly required
- Use "You" for simple tasks, "The operator" for safety-critical tasks

**Checklist:**
- [ ] Is each step a single action?
- [ ] Are all interface element names exact?
- [ ] Are warnings placed at the relevant step, not before the procedure?
- [ ] Does every step have a clearly identifiable subject (who does it)?
- [ ] Have I removed all explanations (move to concept topics)?

### 5.3 Reference Topics (20% of Content)

**Purpose:** Provide data, specifications, and look-up information.

**Structure:**
- Tables, lists, diagrams
- Minimal prose
- Consistent column layout

**Techniques:**
- One table per topic (don't mix specification categories)
- Sort rows logically (alphabetically, by size, by part number)
- Use `<simpletable>` for 4 or fewer columns
- Include units in column headers, not in cells

**Checklist:**
- [ ] Can the needed information be found in under 5 seconds?
- [ ] Are all measurements consistent (same units, same precision)?
- [ ] Is every abbreviation defined?
- [ ] Can reference topics be understood without reading other topics?

### 5.4 Troubleshooting Topics

**Purpose:** Diagnose and resolve problems.

**Structure:**

```
Troubleshooting: [System/Component]
├── Symptom 1 → Cause → Solution
├── Symptom 2 → Cause → Solution
└── Symptom 3 → Cause → Solution
```

**Techniques:**
- List symptoms in order of likelihood (most common first)
- Use a table format for scanability
- Include Error Codes when available
- Tell the user how to verify the problem is resolved

**Checklist:**
- [ ] Are symptoms observable without special tools?
- [ ] Is there a clear "call support" step for unresolved issues?
- [ ] Are error codes cross-referenced to the relevant system documentation?

### 5.5 Hazard Statement Topics

**Purpose:** Communicate safety-critical information.

**Writing formula:**

```
HAZARD: [Serious consequence]
Source: [What creates the hazard]
Result: [What happens if uncontrolled]
Prevention: [What to do to stay safe]
```

**Example:**
> **HAZARD:** Crush injury from uncontrolled spindle descent.
>
> **Source:** Hydraulic pressure loss or valve failure.
>
> **Result:** The spindle can drop suddenly, crushing anything in its path.
>
> **Prevention:**
> - Always engage the mechanical safety lock before working under the spindle.
> - Inspect hydraulic hoses weekly for wear.
> - Never work under a raised spindle without a mechanical support.

---

## 6. Safety & Compliance Writing

### 6.1 The Hazard Communication Formula

All safety writing follows a fixed pattern for consistency and legal defense:

```
[Signal Word] + [Hazard Type] + [Consequence] + [Avoidance]
```

| Element | Purpose | Example |
|---------|---------|---------|
| **Signal word** | Capture attention | WARNING, CAUTION, DANGER |
| **Hazard type** | What is the danger | Electrical shock, Crush hazard |
| **Consequence** | What happens | "causes severe injury or death" |
| **Avoidance** | How to stay safe | "Disconnect power before servicing" |

### 6.2 Signal Word Hierarchy

| Signal Word | Meaning | Consequence | Usage |
|-------------|---------|-------------|-------|
| **DANGER** | Immediate hazard | Death or severe injury | Rare, extreme hazards |
| **WARNING** | Potential hazard | Death or serious injury | Common safety notices |
| **CAUTION** | Minor hazard | Minor or moderate injury | Less critical risks |
| **NOTICE** | Property damage | Equipment damage, no injury | Non-safety risks |

### 6.3 Writing ISO-Compliant Hazard Statements

For ISO 20607 (safety communication), each hazard statement must contain:

1. **The hazard** — "Rotating parts"
2. **The potential harm** — "Can cause crushing or amputation"
3. **The consequence** — "Severe injury or death"
4. **The avoidance** — "Keep hands clear. Lock out power before maintenance."

**Template:**
> **DANGER: [Hazard]**
>
> **What can happen:** [Harm + Consequence]
>
> **How to avoid:** [Action 1]. [Action 2]. [Action 3].

### 6.4 Regulatory Language Patterns

| Standard | Language Pattern | Example |
|----------|-----------------|---------|
| ISO 12100 | Risk reduction | "The safety function must achieve PLr per ISO 13849-1" |
| ISO 13849-1 | Performance Level | "This circuit achieves PL = d, Category 3" |
| ISO 14119 | Interlocking | "The interlock switch must be positively operated" |
| ISO 13850 | Emergency stop | "Emergency stop must be within reach of the operator" |

### 6.5 Legal Disclaimer Language

Legal disclaimers follow fixed patterns that should never be rewritten:

> **Copyright:** The content of this document is protected by copyright. No part may be reproduced without written permission from ExampleCorp.
>
> **Disclaimer:** ExampleCorp makes no representations or warranties regarding the completeness or accuracy of this information.
>
> **Trademark:** All product names mentioned are trademarks of their respective owners.

These are best managed as `conkeyref` targets in the shared library — never hand-copied.

---

## 7. Audience-Aware Writing

### 7.1 Audience Levels

| Level | Description | Language Characteristics |
|-------|-------------|------------------------|
| **Operator** | Runs the machine day-to-day | Short commands, minimal theory, clear warnings |
| **Technician** | Maintains and repairs | Some theory, step-by-step procedures, troubleshooting |
| **Engineer** | Designs and configures | Technical depth, formulas, parameters, architecture |
| **Manager** | Oversees operations | High-level, outcomes, compliance, timelines |

### 7.2 Writing for Operators

- Use short, imperative sentences
- Focus on observable actions and results
- Avoid theoretical explanations

> ✅ "Press Start. The green light turns on. Wait for the cycle to complete."
> ❌ "Upon activation of the start mechanism, the system initiates the programmed cycle sequence."

### 7.3 Writing for Technicians

- Include theory of operation (how it works)
- Provide diagnostic cues and error code tables
- Include maintenance schedules

> ✅ "The spindle drive monitors motor current. If current exceeds 15A for more than 2 seconds, the drive faults with error E-001. This usually indicates bearing wear."

### 7.4 Writing for Engineers

- Include formulas, tolerances, design parameters
- Reference applicable standards
- Use precise technical language

> ✅ "The safety circuit achieves PL = d per ISO 13849-1. The dual-channel architecture with cross-monitoring detects single faults within 100 ms."

### 7.5 Writing for Managers

- Focus on outcomes and business value
- Include compliance information
- Be concise — executive audiences have limited time

> ✅ "This procedure meets ISO 13849-1 PL = d requirements. Estimated 4 hours for annual certification. No recurring costs."

### 7.6 Multi-Audience Topics

When a topic serves multiple audiences, use sections:

```
Title: Spindle Assembly

1. Overview (all audiences)
2. Operation (operator focus)
3. Maintenance (technician focus)
4. Technical Specifications (engineer focus)
5. Compliance (manager focus)
```

---

## 8. Reuse-Oriented Writing

### 8.1 Writing for Conref

Conref works best when content is written in **modular fragments**. Plan your topics so that paragraphs can be independently referenced.

**Technique 1: Separate warnings and notes**
```xml
<!-- Instead of embedding warnings inside task steps, make them standalone IDs -->
<section id="electrical-warning">
  <title>Electrical Safety</title>
  <warning conref="references/safety-common.dita#safety/electrical-warning"/>
</section>
```

**Technique 2: One paragraph per `<p>`**
```xml
<!-- Each p should be independently conref-able -->
<p id="coolant-intro">Coolant prevents overheating during machining.</p>
<p id="coolant-mix">Mix coolant concentrate with water at a 1:20 ratio.</p>
<p id="coolant-check">Check coolant level before each shift.</p>
```

**Technique 3: Standardize introductory and transitional phrases**
```xml
<!-- Standard intros for reusability -->
<p id="prereq-intro">Before you begin, ensure the following conditions are met:</p>
<p id="result-intro">After completing these steps, verify the following:</p>
```

### 8.2 Writing for Keyref

Variables used via keyref require fixed naming patterns. Plan your variable strategy before writing:

| Variable Pattern | Example | Usage |
|-----------------|---------|-------|
| `company-{attribute}` | `company-name` | `<keyword keyref="company-name"/>` |
| `product-{name}-{attr}` | `product-mc5000-weight` | `<keyword keyref="product-mc5000-weight"/>` |
| `standard-{id}` | `standard-iso13849` | `<keyword keyref="standard-iso13849"/>` |
| `part-{number}-{attr}` | `part-10045-description` | `<keyword keyref="part-10045-description"/>` |

### 8.3 Writing for Map-Level Reuse

When writing a topic that will be reused across product maps:
- Do not mention product names in the topic title
- Reference product names via keyref
- Keep prerequisites generic enough to apply to all products
- Use condition profiling (`@product`, `@platform`) for product-specific content

**Weak (product-specific):**
```xml
<title>MC-5000 Maintenance Schedule</title>
<p>For MC-5000 CNC Machining Centers...</p>
```

**Strong (reuse-ready):**
```xml
<title>Maintenance Schedule</title>
<p><keyword keyref="product-flagship"/> maintenance schedule...</p>
```

### 8.4 Avoiding Conref Pitfalls

| Pitfall | Problem | Solution |
|---------|---------|----------|
| **Deep nesting** | `<p conref="a.dita#a/b">` → `<p conref="c.dita#c/d">` | Limit to one level |
| **Circular refs** | A conrefs to B, B conrefs to A | Never allow |
| **Context dependence** | A paragraph references "as shown above" but the image is in the source | Use absolute references |
| **Mixed languages** | English source conrefed into Chinese topic | Verify `@xml:lang` alignment |

---

## 9. Self-Editing & Revision

### 9.1 The Four-Pass Editing Method

**Pass 1 — Structure (read for organization):**
- Does the topic type match the content?
- Is the information in the right order?
- Are transitions between sections smooth?
- Would an outline make sense?

**Pass 2 — Clarity (read for comprehension):**
- Can each sentence be understood on first reading?
- Are there any ambiguous terms?
- Would a new operator understand this?
- Are all abbreviations defined?

**Pass 3 — Conciseness (read for word count):**
- Can any sentence be shortened without losing meaning?
- Are there redundant phrases ("in order to" → "to")?
- Is every word necessary?
- Are there weak verb-noun constructions?

**Pass 4 — Correctness (read for accuracy):**
- Are all specifications correct?
- Are cross-references accurate?
- Are warnings properly placed?
- Does the topic follow DITA best practices?

### 9.2 The 10-Second Test

Read the first sentence of your topic. Does it answer:

1. What is this topic about?
2. Who should read it?
3. What will they learn?

If a reader cannot answer all three in 10 seconds, rewrite the opening.

### 9.3 Self-Review Questions

**Before submitting any topic, ask yourself:**

**About the reader:**
- [ ] Do I know who will read this?
- [ ] What is their skill level?
- [ ] What do they need to accomplish?

**About the content:**
- [ ] Is the topic type correct?
- [ ] Does the title match the content?
- [ ] Would a diagram help?
- [ ] Is the most important information first?

**About the writing:**
- [ ] Are sentences under 20 words?
- [ ] Are all steps imperative verbs?
- [ ] Are all measurements specific?
- [ ] Is the language translation-friendly?

---

## 10. Before & After Examples

### 10.1 Procedure Rewrite

**Before (confusing, wordy):**
> In the event that the machine is not functioning correctly, a diagnostic test should be conducted. This test can be initiated by pressing the diagnostic button, which is located on the right side of the control panel. In the event that the diagnostic test reveals an error code, the operator should refer to the troubleshooting section of the manual for further guidance.

**After (clear, direct):**
> 1. Press the **Diagnostic** button on the right side of the control panel.
>    The system runs a diagnostic test.
> 2. If an error code appears, see Troubleshooting (page XX).

### 10.2 Concept Rewrite

**Before (passive, wordy):**
> The cooling system is responsible for the regulation of temperature within the spindle assembly, which is critical for the prevention of thermal expansion that could negatively impact machining precision. Proper functioning of this system ensures that the spindle operates within its designed temperature range.

**After (active, concise):**
> The cooling system keeps the spindle at operating temperature. This prevents thermal expansion, which reduces machining precision. The system circulates coolant through the spindle housing at a rate of 5 L/min.

### 10.3 Description Rewrite

**Before (vague, cluttered):**
> The machine comes in different models with various specifications to meet the needs of different customers. Some models have more power while others have more features. The basic model is suitable for small shops.

**After (specific, structured):**
> Three models are available:

> | Model | Power | Spindle Speed | Working Area |
> |-------|-------|--------------|--------------|
> | MC-5000 | 15 kW | 10,000 RPM | 500 × 400 mm |
> | MC-8000 | 22 kW | 8,000 RPM | 800 × 600 mm |
> | DR-2000 | 7.5 kW | 15,000 RPM | 300 × 200 mm |

### 10.4 Hazard Statement Rewrite

**Before (generic, easy to ignore):**
> Be careful when working near the spindle. It can cause injury.

**After (specific, impossible to ignore):**
> ⚠️ WARNING: Crush hazard — Rotating spindle.
>
> **What can happen:** Loose clothing, hair, or tools can catch on the rotating spindle, causing severe crushing or amputation.
>
> **How to avoid:**
> - Remove jewelry and secure loose clothing before operating.
> - Use only the recommended tools. Never use rags near the spindle.
> - Wait for the spindle to come to a complete stop before reaching into the work area.

---

## 11. Common Mistakes & Fixes

### 11.1 The Top 10 Writing Mistakes in Manufacturing Documentation

| Mistake | Example | Fix |
|---------|---------|-----|
| **1. Passive voice** | "The button should be pressed." | "Press the button." |
| **2. Noun pileups** | "Spindle bearing temperature monitoring system" | "System that monitors spindle bearing temperature" |
| **3. Vague quantities** | "Allow sufficient time for cooling." | "Allow 30 minutes for cooling." |
| **4. Hidden verbs** | "Perform a check of the alignment." | "Check the alignment." |
| **5. Missing condition** | "Reduce feed rate." | "If vibration exceeds 0.02 mm, reduce feed rate." |
| **6. Ambiguous pronoun** | "Remove the cover and inspect the belt. Replace it if worn." | "Remove the cover and inspect the belt. Replace the belt if worn." |
| **7. Long sentences** | "After completing the alignment procedure, which should be performed after every tool change and before the first cut of each shift, tighten the locking bolts to 55 N·m." | "Align after every tool change and before the first cut. Then tighten the locking bolts to 55 N·m." |
| **8. Assumed knowledge** | "Adjust the offset as needed." | "Set the X-axis offset to -0.05 mm. Set the Y-axis offset to +0.02 mm." |
| **9. Not naming the subject** | "Open the valve." (Who?) | "Turn the valve handle counterclockwise to open." |
| **10. Over-explaining** | "The coolant pump, which circulates cutting fluid to reduce heat and flush chips from the cutting zone, should be turned on." | "Turn on the coolant pump." (Explanation goes in a concept topic.) |

### 11.2 Quick Self-Correction Table

| If You Wrote | Change To | Reason |
|-------------|-----------|--------|
| "in order to" | "to" | Remove 2 unnecessary words |
| "is able to" | "can" | Shorter, more direct |
| "in the event of" | "if" | Simpler and clearer |
| "due to the fact that" | "because" | Cut 3 unnecessary words |
| "utilize" | "use" | Use the simpler word |
| "a number of" | "several" or specific number | Be precise |
| "at this point in time" | "now" | Replace 5 words with 1 |
| "the majority of" | "most" | Shorter, clearer |
| "in the vicinity of" | "near" | Use plain language |
| "with the exception of" | "except" | Simpler preposition |

### 11.3 The "Read Aloud" Test

The most effective editing technique costs nothing: read your writing aloud.

- **Awkward phrases** sound awkward when spoken.
- **Missing words** are obvious when read aloud.
- **Run-on sentences** force you to breathe in the middle.
- **Unnatural word choices** will feel wrong in your mouth.

If it doesn't sound natural when spoken, rewrite it until it does.

---

## Related Documents

| Document | Location |
|----------|----------|
| DITA Style Guide | [STYLE-GUIDE/01-STYLE-GUIDE.md](01-STYLE-GUIDE.md) |
| Glossary | [STYLE-GUIDE/02-GLOSSARY.md](02-GLOSSARY.md) |
| Quality Checklist | [STYLE-GUIDE/03-QUALITY-CHECKLIST.md](03-QUALITY-CHECKLIST.md) |
| Approval Workflow | [WORKFLOWS/01-APPROVAL-WORKFLOW.md](../WORKFLOWS/01-APPROVAL-WORKFLOW.md) |
| Shared Library Governance | [WORKFLOWS/04-SHARED-LIBRARY-GOVERNANCE.md](../WORKFLOWS/04-SHARED-LIBRARY-GOVERNANCE.md) |
