# Metadata Schema & Validation

**Document ID:** ARCH-002  
**Version:** 1.1  
**Last Updated:** 2026-06-07  
**Status:** Approved  

> **v1.1 Update:** Added metadata fields for all 13 extended topic types.
> See `04-EXTENDED-TOPIC-TYPES.md` for type descriptions.

---

## 1. Required Metadata Fields

Every topic, regardless of type, must include these fields in `<prolog>`:

### 1.1 Universal Fields (All Topic Types)

| Field | Element | Required | Example |
|-------|---------|----------|---------|
| Topic ID | `@id` on root | YES | `alarm-700016-troubleshooting` |
| Language | `@xml:lang` | YES | `en-US` |
| Audience | `<audience>` | YES | `type="maintenance"` |
| Content Type | `<othermeta name="content-type">` | YES | `troubleshooting` |
| Product | `<prodname>` | YES | `MC-5000 VMC` |
| Version | `<vrm>` | YES | `version="2.1"` |
| Document Status | `<othermeta name="document-status">` | YES | `approved` |
| Owner Department | `<othermeta name="owner-department">` | YES | `Safety Engineering` |

### 1.2 Type-Specific Required Fields

| Topic Type | Additional Required Fields |
|------------|--------------------------|
| **troubleshooting** | `alarm-code`, `severity`, `estimated-repair-time` |
| **glossary-entry** | `glossary-domain` ([safety|technical|commercial|legal]), `translatable="yes"` |
| **hazard-statement** | `hazard-type`, `severity` ([danger|warning|caution|notice]), `iso-reference` |
| **task-requirements** | `estimated-time`, `required-skill-level` (uses Subject Scheme) |
| **learning-object** | `duration`, `module-type`, `certification-relevant` |
| **concept** | None beyond universal |
| **task** | `estimated-time`, `required-skill-level` |
| **reference** | None beyond universal |

### 1.3 Example: Troubleshooting Metadata

```xml
<prolog>
  <metadata>
    <audience type="maintenance" job="technician" experiencelevel="experienced"/>
    <category>Troubleshooting</category>
    <prodinfo>
      <prodname>MC-5000 VMC</prodname>
      <vrmlist>
        <vrm version="2.1" release="A" modification="3"/>
      </vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="troubleshooting"/>
    <othermeta name="alarm-code" content="700016"/>
    <othermeta name="severity" content="warning"/>
    <othermeta name="estimated-repair-time" content="15-60 minutes"/>
    <othermeta name="document-status" content="approved"/>
    <othermeta name="owner-department" content="Field Service"/>
  </metadata>
</prolog>
```

### 1.4 Example: Glossary Entry Metadata

```xml
<prolog>
  <metadata>
    <audience type="all"/>
    <category>Glossary</category>
    <othermeta name="content-type" content="glossary-entry"/>
    <othermeta name="glossary-domain" content="safety"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="document-status" content="published"/>
    <othermeta name="iso-reference" content="ISO 14118"/>
  </metadata>
</prolog>
```

### 1.5 Example: Hazard Statement Metadata

```xml
<prolog>
  <metadata>
    <audience type="operator" job="all" experiencelevel="all"/>
    <category>Safety</category>
    <prodinfo>
      <prodname>MC-5000 VMC</prodname>
    </prodinfo>
    <othermeta name="content-type" content="hazard-statement"/>
    <othermeta name="hazard-type" content="mechanical"/>
    <othermeta name="severity" content="caution"/>
    <othermeta name="iso-reference" content="ISO 12100"/>
  </metadata>
</prolog>
```

## 2. Controlled Values

All metadata values are governed by **Subject Scheme**
(see `05-SUBJECT-SCHEME-GUIDE.md`). The authoritative list
is defined in `06-TEMPLATES/12-SUBJECT-SCHEME-TEMPLATE.ditamap`.

### 2.1 Audience Values

```
operator       — Machine operators
maintenance    — Maintenance technicians  
engineer       — Design / application engineers
field-service  — Field service technicians
trainer        — Trainers
all            — All audiences
```

### 2.2 Content Type Values

```
concept              — Concept topic
task                 — Task topic
reference            — Reference topic
troubleshooting      — Troubleshooting topic
glossary-entry       — Glossary entry
hazard-statement     — Hazard statement
task-requirements    — Structured task prerequisites
learning-object      — Learning module
```

### 2.3 Document Status Values

```
draft       — Being authored, not yet submitted for review
review      — In the approval workflow
approved    — Technical + editorial approval received
published   — Released in CI/CD build
archived    — Superseded or removed from active use
```

### 2.4 Hazard Severity Values

```
danger      — Will cause death or serious injury
warning     — Could cause death or serious injury
caution     — May cause minor or moderate injury
notice      — Property damage only
```

## 3. Metadata Validation Rules

| Rule | Description | Enforcement |
|------|-------------|-------------|
| **M1** | All universal fields present | Git pre-commit hook |
| **M2** | `content-type` matches DOCTYPE | CI/CD build check |
| **M3** | All values from Subject Scheme | oXygen validation |
| **M4** | `document-status` consistent with branch | Git workflow |
| **M5** | `product` matches file location | Directory convention |
| **M6** | `xml:lang` matches repository path | Directory convention |
| **M7** | No orphaned topics without incoming xref | Quarterly audit |

---

## 4. Metadata Template

A reusable metadata block is provided in `06-TEMPLATES/04-METADATA-TEMPLATE.xml`.

---

**Document ID:** ARCH-002  
**Related Documents:**
- `04-EXTENDED-TOPIC-TYPES.md` — Topic type definitions
- `05-SUBJECT-SCHEME-GUIDE.md` — Controlled value governance
- `06-TEMPLATES/12-SUBJECT-SCHEME-TEMPLATE.ditamap` — Subject Scheme file

**Next Review Date:** Q4 2026
