# Topic Templates — Complete Reference

**Document ID:** ARCH-003  
**Version:** 1.1  
**Last Updated:** 2026-06-07  
**Status:** Approved  

> **v1.1 Update:** Added templates for all 8 specialized topic types.
> Templates are available in `templates/`.

---

## 1. Template Index

| # | Template File | DTD | Intended Use |
|---|--------------|-----|-------------|
| T1 | `01-CONCEPT-TOPIC-TEMPLATE.dita` | `concept.dtd` | Explain "what" and "why" |
| T2 | `02-TASK-TOPIC-TEMPLATE.dita` | `task.dtd` | Step-by-step procedures |
| T3 | `03-REFERENCE-TOPIC-TEMPLATE.dita` | `reference.dtd` | Look-up data and specifications |
| T4 | `04-METADATA-TEMPLATE.xml` | — | Boilerplate metadata block |
| T5 | `05-DITAMAP-TEMPLATE.ditamap` | `map.dtd` | Web navigation structure |
| **T6** | **`06-TROUBLESHOOTING-TEMPLATE.dita`** | **`troubleshooting.dtd`** | **Diagnose problems by symptom** |
| **T7** | **`07-GLOSSARY-ENTRY-TEMPLATE.dita`** | **`glossentry.dtd`** | **Reusable term definitions** |
| **T8** | **`08-HAZARD-STATEMENT-TEMPLATE.dita`** | **`reference.dtd` + hi-d** | **Structured safety warnings** |
| **T9** | **`09-TASK-REQUIREMENTS-TEMPLATE.dita`** | **`task.dtd` + pr-d** | **Structured prerequisites** |
| **T10** | **`10-LEARNING-OBJECT-TEMPLATE.dita`** | **`learningObject.dtd`** | **E-learning modules** |
| **T11** | **`11-BOOKMAP-TEMPLATE.ditamap`** | **`bookmap.dtd`** | **Print/PDF publication** |
| **T12** | **`12-SUBJECT-SCHEME-TEMPLATE.ditamap`** | **`subjectScheme.dtd`** | **Metadata governance** |

> Templates T6–T12 are new in v1.1. See `04-EXTENDED-TOPIC-TYPES.md` for design rationale.

---

## 2. When to Use Each Template

```
┌──────────────────────────────────────────────────────────────────┐
│                          DECISION TREE                           │
├──────────────────────────────────────────────────────────────────┤
│ What kind of content are you creating?                           │
│                                                                  │
│ Explaining a concept or system?           → Template T1 (Concept)│
│ Writing step-by-step instructions?        → Template T2 (Task)   │
│    └─ Complex prerequisites (skills,       → Template T9 (+TaskReq)
│        tools, conditions)?                                        │
│ Listing data or specifications?           → Template T3 (Ref)    │
│ Diagnosing a problem?                      → Template T6 (Trouble)│
│ Writing a safety warning?                  → Template T8 (Hazard)│
│ Defining a term?                           → Template T7 (Gloss) │
│ Creating e-learning content?               → Template T10 (Learn)│
│ Organizing a publication for print?        → Template T11 (Book) │
│ Defining metadata allowed values?          → Template T12 (Scheme)│
└──────────────────────────────────────────────────────────────────┘
```

---

## 3. Template Quick-Reference Cards

### T6 — Troubleshooting

```xml
<troubleshooting id="unique-id">
  <title>Troubleshooting: [Problem or Alarm]</title>
  <shortdesc>One-sentence summary of the symptom.</shortdesc>
  <troublebody>
    <condition>
      <p>What the operator sees. Quantitative thresholds.</p>
    </condition>
    <cause>
      <p>Probable cause (XX%): description</p>
      <remedy>
        <cmd>Step to fix this cause.</cmd>
        <stepresult>Verification step.</stepresult>
      </remedy>
    </cause>
    <!-- Multiple <cause> elements, ordered by likelihood -->
  </troublebody>
</troubleshooting>
```

**Key rules:**
- One troubleshooting topic = one symptom/alarm code
- List causes from most to least likely, with estimated frequency
- Each `<cause>` has one or more `<remedy>` elements
- Cross-reference to related Task topics for detailed repair steps

---

### T7 — Glossary Entry

```xml
<glossentry id="gloss-term-name">
  <glossterm>Term Name</glossterm>
  <glossdef>
    <p>Clear, concise definition.</p>
  </glossdef>
  <glossAlternative>
    <glossAcronym>ACRONYM</glossAcronym>
  </glossAlternative>
  <glossRelated>
    <glossxref href="related-term.dita"/>
  </glossRelated>
</glossentry>
```

**Key rules:**
- One term per file — enables fine-grained translation and reuse
- Use `<glossAlternative>` for acronyms, abbreviations, and surface variants
- Use `<glossRelated>` to build a term network
- Group related terms into `<glossgroup>` topics for organized output

---

### T8 — Hazard Statement

```xml
<reference id="hazard-name">
  <title>Hazard: [Name]</title>
  <refbody>
    <section>
      <p hazard="">
        <hazardstatement type="caution|warning|danger|notice">
          <messagepanel>
            <typeofhazard>Description</typeofhazard>
            <consequence>Injury or damage</consequence>
            <howtoavoid>Preventive measures</howtoavoid>
          </messagepanel>
        </hazardstatement>
      </p>
    </section>
  </refbody>
</reference>
```

**Key rules:**
- Each hazard = one topic (enables cross-referencing from Task topics)
- Severity is defined by `@type` on `<hazardstatement>`: danger > warning > caution > notice
- Include ISO risk assessment table in the same topic
- Hazard symbols reference SVG files for accessible rendering

---

### T9 — Task Requirements (embedded in Task)

```xml
<task id="task-id">
  <taskbody>
    <taskrequirements id="reqs">
      <reqcond>
        <personnel>
          <skill level="3">Skill description</skill>
          <certification>Certification name</certification>
        </personnel>
        <equipment>
          <item>Tool name (P/N)</item>
        </equipment>
        <parts>
          <item>Part name (P/N)</item>
        </parts>
        <environment>
          <temp min="10" max="40">Temperature range</temp>
        </environment>
        <safety>
          <hazardref href="hazard-topic.dita">Reference</hazardref>
        </safety>
      </reqcond>
    </taskrequirements>
    <prereq>text prose</prereq>
    <steps>...</steps>
  </taskbody>
</task>
```

**Key rules:**
- `<taskrequirements>` sits between `<prereq>` and `<steps>` in `<taskbody>`
- Only use for procedures with non-trivial prerequisites (tools, skills, environment)
- Simple tasks with only a few prerequisites can use plain `<prereq>` instead

---

### T10 — Learning Object

```xml
<learningObject id="module-id">
  <learningObjectbody>
    <learningOverview>
      <!-- Objectives, prerequisites, duration -->
    </learningOverview>
    <learningContent>
      <!-- Core instructional body -->
      <learningPractice>
        <!-- Hands-on exercise -->
      </learningPractice>
    </learningContent>
    <learningSummary>
      <!-- Recap, assessment, next steps -->
      <learningAssessment>
        <lcQuestion>...</lcQuestion>
      </learningAssessment>
    </learningSummary>
  </learningObjectbody>
</learningObject>
```

**Key rules:**
- A learningObject always contains exactly one each of: overview, content, summary
- learningContent can include Concept, Task, and Reference content as needed
- learningSummary must include an assessment (multiple choice recommended)
- Duration, audience level, and prerequisites are declared in the overview

---

### T11 — Bookmap

```xml
<bookmap id="guide-id">
  <booktitle>
    <mainbooktitle>Guide Title</mainbooktitle>
  </booktitle>
  <frontmatter>
    <booklists><toc/><figurelist/><tablelist/></booklists>
  </frontmatter>
  <chapter href="chapter-file.dita"/>
  <backmatter>
    <appendix href="appendix-file.dita"/>
    <indexlist/>
  </backmatter>
</bookmap>
```

**Key rules:**
- Use Bookmap when the output targets **print/PDF**; use Ditamap for web
- Bookmap adds: book title, edition info, front matter (TOC), back matter (appendix, index)
- Chapters can reference Ditamaps (nested structure)
- Always include `<indexlist/>` in back matter for PDF output

---

### T12 — Subject Scheme

```xml
<subjectScheme id="scheme-id">
  <hasInstance>
    <subjectdef keys="attribute-name">
      <subjectdef keys="allowed-value-1"/>
      <subjectdef keys="allowed-value-2"/>
    </subjectdef>
  </hasInstance>
  <enumerationdef>
    <attributedef name="attribute-name"/>
    <subjectdef keyref="attribute-name"/>
  </enumerationdef>
</subjectScheme>
```

**Key rules:**
- One scheme file for the entire enterprise (not per-product)
- Each `<subjectdef>` maps to one controlled value
- Each `<enumerationdef>` binds a list to a metadata attribute
- See `05-SUBJECT-SCHEME-GUIDE.md` for the complete setup guide

---

## 4. File Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Concept | `[SUBJECT]-CONCEPT.dita` | `SAFETY-SYSTEM-CONCEPT.dita` |
| Task | `[SUBJECT]-TASK.dita` | `SPINDLE-ALIGNMENT-TASK.dita` |
| Reference | `[SUBJECT]-REFERENCE.dita` | `MC5000-SPECS-REFERENCE.dita` |
| Troubleshooting | `ALARM-[CODE]-TROUBLESHOOTING.dita` | `ALARM-700016-TROUBLESHOOTING.dita` |
| Glossary Entry | `GLOSS-[TERM].dita` | `GLOSS-LOTO.dita` |
| Hazard Statement | `HAZARD-[TYPE].dita` | `HAZARD-FLYING-CHIPS.dita` |
| Learning Object | `TRAIN-[MODULE]-MODULE.dita` | `TRAIN-SAFETY-MODULE.dita` |

---

**Document ID:** ARCH-003  
**Templates Location:** `templates/`  
**Next Review Date:** Q4 2026
