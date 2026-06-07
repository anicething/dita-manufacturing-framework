---
marp: true
theme: uncover
class:
  - lead
  - invert
paginate: true
header: "DITA Manufacturing Framework — Training Program"
footer: "DITA Governance Framework v1.1 — June 2026"
---

<!-- _class: lead invert -->
# DITA Manufacturing Framework
## Training Module 2: Authoring Extended Types, Workflows &amp; Publishing

**Target Audience:** Technical Writers, Editors
**Duration:** 8 hours (includes hands-on exercises)

Manufacturing Enterprise — Documentation Governance Committee

---

## Module 2 Objectives

By the end of this session, you will be able to:

- **Write** Concept, Task, and Reference topics from templates
- **Write** Troubleshooting, Glossary Entry, and Hazard Statement topics
- **Use** Task Requirements for complex procedures
- **Configure** Subject Scheme for metadata governance
- **Structure** a Bookmap for print/PDF output
- **Apply** the 10-step approval workflow
- **Follow** the Git branching strategy
- **Trigger** a CI/CD publishing pipeline

---

## Module 2 Roadmap

```
┌──────────────────────────────────────────────────┐
│  Part 1: Core Authoring (2 hrs)                 │
│  ├── Anatomy of a DITA topic                    │
│  ├── Templates T1–T5 (Concept / Task / Ref)     │
│  └── Templates T6–T8: Troubleshooting, Glossary,│
│       Hazard Statement                          │
├──────────────────────────────────────────────────┤
│  Part 2: Advanced Topics (2 hrs)                │
│  ├── Template T9: Task Requirements             │
│  ├── Template T12: Subject Scheme               │
│  ├── Cross-references & conref                  │
│  └── Conditional processing                     │
├──────────────────────────────────────────────────┤
│  Part 3: Workflows (2 hrs)                      │
│  ├── 10-step approval workflow                  │
│  ├── Git branching strategy                     │
│  └── Review criteria                            │
├──────────────────────────────────────────────────┤
│  Part 4: Publishing (2 hrs)                     │
│  ├── Template T11: Bookmap for PDF              │
│  ├── DITA-OT transformation                     │
│  ├── GitHub Actions CI/CD                       │
│  └── Publishing SLA & metrics                   │
└──────────────────────────────────────────────────┘
```

---

## Templates Overview — T1 to T12

v1.1 adds 7 new templates. All 12 are in `06-TEMPLATES/`:

```
T1  01-CONCEPT-TOPIC-TEMPLATE.dita
T2  02-TASK-TOPIC-TEMPLATE.dita
T3  03-REFERENCE-TOPIC-TEMPLATE.dita
T4  04-METADATA-TEMPLATE.xml
T5  05-DITAMAP-TEMPLATE.ditamap
─── v1.1 additions below ───
T6  06-TROUBLESHOOTING-TEMPLATE.dita     ← NEW
T7  07-GLOSSARY-ENTRY-TEMPLATE.dita      ← NEW
T8  08-HAZARD-STATEMENT-TEMPLATE.dita    ← NEW
T9  09-TASK-REQUIREMENTS-TEMPLATE.dita   ← NEW
T10 10-LEARNING-OBJECT-TEMPLATE.dita     ← NEW
T11 11-BOOKMAP-TEMPLATE.ditamap          ← NEW
T12 12-SUBJECT-SCHEME-TEMPLATE.ditamap   ← NEW
```

> Today we focus on T6, T7, T8, T9, T11, T12. T10 is covered in the advanced training.

---

## Writing a Troubleshooting Topic

**Template T6** — Diagnose problems by symptom.

```
<troubleshooting id="alarm-700016">
  <title>Troubleshooting: Coolant Pressure Low (Alarm 700016)</title>
  <shortdesc>Diagnose alarm 700016 — coolant pressure below 0.3 MPa.</shortdesc>
  <troublebody>
    <condition>
      <p>What the operator sees. Quantitative thresholds.</p>
    </condition>
    <cause id="cause-80pct">
      <p><b>Probable cause (80%):</b> Clogged return filter.</p>
      <remedy>
        <cmd>Clean or replace filter element.</cmd>
        <stepresult>Pressure returns to 0.4–0.6 MPa.</stepresult>
      </remedy>
    </cause>
    <cause id="cause-15pct">
      <p><b>Probable cause (15%):</b> Pump impeller damage.</p>
      <remedy>
        <cmd>Replace pump assembly.</cmd>
      </remedy>
    </cause>
  </troublebody>
</troubleshooting>
```

**Key rules:**
- One topic = one symptom or alarm code
- Order causes by likelihood (most likely first)
- Each `<cause>` must include at least one `<remedy>`

---

## Troubleshooting — Best Practices

**Do's:**
- ✅ Include frequency estimates (80%, 15%, 5%) — helps technicians prioritize
- ✅ Use quantitative thresholds in `<condition>` ("pressure < 0.3 MPa")
- ✅ Cross-reference to Task topics for detailed repair steps (`<xref>`)
- ✅ Add `alarm-code` and `severity` in metadata

**Don'ts:**
- ❌ Don't merge multiple symptoms into one topic
- ❌ Don't write a Troubleshooting topic if there's only one cause (use a Task instead)
- ❌ Don't forget the `stepresult` — tells the technician the repair worked

> **Example:** `examples/topics/en/new-types/ALARM-700016-TROUBLESHOOTING.dita`

---

## Writing a Glossary Entry

**Template T7** — Define terms as reusable, translatable topics.

```xml
<glossentry id="gloss-lockout-tagout">
  <glossterm>Lockout/Tagout (LOTO)</glossterm>
  <glossdef>
    <p>A safety procedure that isolates energy sources before
       maintenance begins.</p>
  </glossdef>
  <glossAlternative>
    <glossAcronym>LOTO</glossAcronym>
  </glossAlternative>
  <glossRelated>
    <glossxref href="GLOSS-INTERLOCK.dita"/>
  </glossRelated>
</glossentry>
```

**Key rules:**
- **One term per file** — enables granular translation
- Always include `<glossAlternative>` if the term has an acronym
- Use `<glossRelated>` to build a term network
- Set `translatable="yes"` in metadata

> **Example:** `examples/topics/en/new-types/GLOSS-LOTO.dita`

---

## Writing a Hazard Statement

**Template T8** — Structured safety warnings using the Machine Industry Domain.

```xml
<reference id="hazard-flying-chips">
  <refbody>
    <section id="warning">
      <p hazard="">
        <hazardstatement type="caution">
          <messagepanel>
            <typeofhazard>Flying chips</typeofhazard>
            <consequence>Severe eye injury</consequence>
            <howtoavoid>Wear safety glasses with side shields</howtoavoid>
          </messagepanel>
        </hazardstatement>
      </p>
    </section>
    <section id="risk-assessment">
      <!-- ISO 12100 risk table -->
    </section>
  </refbody>
</reference>
```

**Severity levels:** Danger > Warning > Caution > Notice

| Type | Meaning | Example |
|------|---------|---------|
| `danger` | Will cause death | Electrical shock |
| `warning` | Could cause death | Moving parts |
| `caution` | Minor injury | Flying chips |
| `notice` | Property damage | Coolant spill |

> **Example:** `examples/topics/en/new-types/HAZARD-FLYING-CHIPS.dita`

---

## Writing Task Requirements

**Template T9** — Structured prerequisites for complex procedures.

```xml
<taskbody>
  <taskrequirements id="spindle-reqs">
    <reqcond>
      <personnel>
        <skill level="3">Level 3 Technician</skill>
        <certification>Spindle Alignment Certified</certification>
      </personnel>
      <equipment>
        <item>Laser alignment kit (HZ-5000-KIT)</item>
        <item>Torque wrench (5-50 Nm)</item>
      </equipment>
      <parts>
        <item>O-ring for taper seal (P/N: OR-1024)</item>
      </parts>
      <environment>
        <temp min="10" max="40">10-40°C</temp>
      </environment>
      <safety>
        <hazardref href="HAZARD-FLYING-CHIPS.dita"/>
      </safety>
    </reqcond>
  </taskrequirements>
  <prereq>text prose</prereq>
  <steps>...</steps>
</taskbody>
```

> Use `<taskrequirements>` **only** for non-trivial prerequisites. Simple tasks can use plain `<prereq>`.

---

## Exercise 1a: Write an Extended-Type Topic (30 min)

Choose **one** scenario and write a short DITA topic in the correct type:

1. **Troubleshooting:** Alarm 700045 — "Door interlock open" on MC-5000.
   Machine stops when door is ajar. 3 possible causes: door not fully closed
   (60%), interlock sensor misaligned (30%), wiring fault (10%).
2. **Glossary Entry:** Define "Spindle Warm-Up Cycle" — a programmed routine
   that runs the spindle at increasing speeds to distribute lubrication before
   cutting begins. Acronym: SWC.
3. **Hazard Statement:** Pressurized coolant spray during tool changes.
   Caution level. Consequence: chemical eye irritation. Avoidance: wear
   splash-proof goggles and keep door closed during ATC cycle.

> Use the appropriate template. You have 30 minutes.

---

## Part 2: Subject Scheme Configuration

**Template T12** — Defining controlled values for metadata.

```xml
<subjectScheme id="dr5000-scheme">
  <hasInstance>
    <subjectdef keys="audience">
      <subjectdef keys="operator"/>
      <subjectdef keys="maintenance"/>
      <subjectdef keys="engineer"/>
    </subjectdef>
  </hasInstance>
  <enumerationdef>
    <attributedef name="audience"/>
    <subjectdef keyref="audience"/>
  </enumerationdef>
</subjectScheme>
```

**Setup process:**
1. Edit `12-SUBJECT-SCHEME-TEMPLATE.ditamap` for your product line
2. Register in oXygen: Preferences → DITA → Subject Scheme
3. Validate compliance: DITA → Validate → Validate with Subject Scheme
4. Add pre-commit git hook for CI/CD validation

> **All values must come from the scheme.** See `02-ARCHITECTURE/05-SUBJECT-SCHEME-GUIDE.md`.

---

## Exercise 1b: Metadata with Subject Scheme (15 min)

Using the Subject Scheme values defined in the template, correct these metadata entries:

**Before (what's wrong?):**
```xml
<audience type="operators"/>
<othermeta name="content-type" content="Troubleshooting"/>
<othermeta name="document-status" content="waiting for review"/>
<othermeta name="product" content="MC5000 Machining Center"/>
```

> **Hint:** The scheme defines these exact values:
> - audience: `operator`, `maintenance`, `engineer`, `field-service`, `trainer`, `all`
> - content-type: `concept`, `task`, `reference`, `troubleshooting`, `glossary-entry`, `hazard-statement`, `learning-object`
> - document-status: `draft`, `review`, `approved`, `published`, `archived`
> - product: `MC-5000`, `MC-8000`, `DR-2000`, `common`

---

## Exercise 1b — Answers

**After (corrected):**
```xml
<audience type="operator"/>
<othermeta name="content-type" content="troubleshooting"/>
<othermeta name="document-status" content="review"/>
<othermeta name="product" content="MC-5000"/>
```

**What was wrong:**
- `operators` → `operator` (Subject Scheme value, plural not allowed)
- `Troubleshooting` → `troubleshooting` (lowercase, kebab-case)
- `waiting for review` → `review` (not in controlled list)
- `MC5000 Machining Center` → `MC-5000` (must match product code)

> Subject Scheme validation catches all of these automatically.

---

## Cross-References to New Types

All new types support cross-referencing the same way:

```xml
<!-- Link to a Troubleshooting topic from a Task -->
<xref href="ALARM-700016-TROUBLESHOOTING.dita">coolant pressure troubleshooting</xref>

<!-- Link to a Glossary Entry to define a term inline -->
<xref href="GLOSS-LOTO.dita" format="dita">LOTO</xref>

<!-- Link to a Hazard Statement from safety prerequisites -->
<xref href="HAZARD-FLYING-CHIPS.dita">flying chip hazard details</xref>

<!-- A Task Requirements block can reference a Hazard -->
<hazardref href="HAZARD-FLYING-CHIPS.dita"/>
```

> **Tip:** Cross-reference troubleshooting → task (for detailed repair) and task → troubleshooting (for diagnosis). This creates a navigation loop that helps technicians.

---

## Part 3: Approval Workflow (10 Steps)

The approval workflow applies to ALL topic types, including new ones:

| Step | Stage | Who | SLA | Validates |
|------|-------|-----|-----|-----------|
| 1–2 | Authoring | Technical Writer | — | — |
| 3 | Self-review | Technical Writer | 1 day | Template adherence |
| 4 | Peer review | Another writer | 1 day | Type correctness |
| 5 | SME Review | Subject Matter Expert | 2 days | Technical accuracy |
| 6 | Compliance Review | Compliance Officer | 2 days | ISO requirements |
| 7 | Editorial Review | Editor | 1 day | Style guide |
| 8 | Final Approval | Content Owner | 1 day | — |
| 9–10 | Staging → Publish | CI/CD | Auto | Scheme validation |

> **New:** Step 6 (Compliance) now specifically validates Hazard Statements against ISO 12100.

---

## Part 4: Bookmap for Print/PDF Publishing

**Template T11** — Organize content for formal publications.

```
bookmap
├── booktitle: "MC-5000 User Guide v2.1"
├── frontmatter
│   ├── TOC, Figure List, Table List
│   └── Preface
├── chapter: Safety
│   ├── Safety System Overview (Concept)
│   ├── Hazard Statements (Hazard)
│   └── LOTO Procedure (Task)
├── chapter: Maintenance
│   ├── Spindle Alignment (Task + Requirements)
│   ├── Coolant Filter Replacement (Task)
│   └── Alarm 700016 (Troubleshooting)
├── chapter: Specifications
│   └── Technical Specifications (Reference)
├── backmatter
│   ├── Appendix: Glossary (Glossary Entry topics)
│   └── Index
```

> **Rule:** Use Bookmap for PDF → use Ditamap for HTML/web. The same topics feed both.

---

## Exercise 2: Create a Bookmap (30 min)

Create a bookmap for a "MC-5000 Maintenance Guide" with:

1. **Three chapters:**
   - Safety (1 Concept + 1 Hazard topic)
   - Maintenance (2 Task topics + 1 Troubleshooting topic)
   - Reference (1 Reference topic)

2. **Front matter:** TOC, Figure List

3. **Back matter:** 1 Glossary appendix + Index

**Requirements:**
- Use `<chapter>` with meaningful `@navtitle` values
- Include `<bookmeta>` with author and copyright
- Add a placeholder for a topic not yet written (use `processing-role="resource-only"`)

> Reference: `06-TEMPLATES/11-BOOKMAP-TEMPLATE.ditamap`

---

## Publishing Pipeline — v1.1 Additions

New validation steps for extended types:

```
Writer pushes feature branch → PR to develop →
GitHub Actions triggers:
├── 1. Validate XML (all 12 DTDs)
├── 2. Subject Scheme validation (metadata values)
├── 3. Troubleshooting check: every <cause> has <remedy>?
├── 4. Hazard check: severity metadata matches @type?
├── 5. Glossary check: <glossdef> present and non-empty?
├── 6. Build HTML5 + PDF (via Bookmap for book content)
├── 7. Deploy to staging
└── 8. Notify reviewers
```

> **New:** Subject Scheme validation is now a required step — the build fails if any topic has an invalid metadata value.

---

## Module 2 — Knowledge Check

1. What DTD does a **Troubleshooting** topic use?
2. In a **Glossary Entry**, which element holds the acronym?
3. What are the four severity levels for **Hazard Statements**?
4. Where does `<taskrequirements>` sit in a Task's `<taskbody>`?
5. What's the difference between a **Bookmap** and a **Ditamap**?
6. Which template defines controlled values for metadata?

> Write your answers. Review in 10 minutes.

---

<!-- _class: lead invert -->
## Answers

1. `<!DOCTYPE troubleshooting PUBLIC "-//OASIS//DTD DITA Troubleshooting//EN" "troubleshooting.dtd">`
2. `<glossAcronym>` inside `<glossAlternative>`
3. **Danger** > **Warning** > **Caution** > **Notice**
4. Between `<prereq>` and `<steps>` inside `<taskbody>`
5. **Bookmap** = formal publication (front matter, chapters, back matter, index) for PDF/print.
   **Ditamap** = web navigation structure.
6. **Template T12** — `12-SUBJECT-SCHEME-TEMPLATE.ditamap`

---

## Module 2 — Sign-off Checklist

| Skill | Template | Demonstrated |
|-------|----------|-------------|
| Write a Concept topic | T1 | ☐ |
| Write a Task topic | T2 | ☐ |
| Write a Reference topic | T3 | ☐ |
| Write a Troubleshooting topic | T6 | ☐ |
| Write a Glossary Entry | T7 | ☐ |
| Write a Hazard Statement | T8 | ☐ |
| Use Task Requirements | T9 | ☐ |
| Configure Subject Scheme | T12 | ☐ |
| Structure a Bookmap | T11 | ☐ |
| Use xref for cross-references | — | ☐ |
| Follow the 10-step workflow | — | ☐ |
| Understand CI/CD pipeline | — | ☐ |

> **Next:** Module 3 — Tools Setup & Advanced Authoring

---

<!-- _class: lead invert -->
# Module 2 Complete

**Next session:** Module 3 — Tools Setup & Advanced Authoring

DITA Manufacturing Framework v1.1
Manufacturing Enterprise — Documentation Governance Committee

---

## Quick Reference: Extended-Type Elements

| Element | Purpose | Used In |
|---------|---------|---------|
| `<troublebody>` | Container for troubleshooting content | T6 Troubleshooting |
| `<condition>` | Describes the observable symptom | T6 Troubleshooting |
| `<cause>` | A possible root cause (ordered by likelihood) | T6 Troubleshooting |
| `<remedy>` | Steps to resolve a specific cause | T6 Troubleshooting |
| `<glossentry>` | Root element for glossary term | T7 Glossary |
| `<glossterm>` | The term being defined | T7 Glossary |
| `<glossdef>` | The definition | T7 Glossary |
| `<glossAlternative>` | Acronyms, abbreviations, surface forms | T7 Glossary |
| `<hazardstatement>` | Structured safety warning | T8 Hazard |
| `<typeofhazard>` | Type of hazard ("Flying chips") | T8 Hazard |
| `<consequence>` | What happens if not avoided | T8 Hazard |
| `<howtoavoid>` | Preventive measures | T8 Hazard |
| `<taskrequirements>` | Structured prerequisites block | T9 Task |
| `<reqcond>` | Container for all requirement conditions | T9 Task |
| `<personnel>` / `<equipment>` / `<parts>` | Resource requirements | T9 Task |
| `<bookmap>` | Formal publication root element | T11 Bookmap |
| `<chapter>` | Book chapter (maps to Ditamap or topic) | T11 Bookmap |
| `<subjectScheme>` | Metadata governance root | T12 Scheme |
| `<subjectdef>` | Defines a controlled value | T12 Scheme |
| `<enumerationdef>` | Binds values to an attribute | T12 Scheme |
