# Subject Scheme — Metadata Governance Guide

**Document ID:** ARCH-005  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved  

---

## 1. What Is Subject Scheme?

Subject Scheme is a DITA specialization that defines **controlled vocabularies** for metadata attributes. Instead of authors typing free-text values like `audience="operator"` (which could be "Operator", "OPERATOR", "operators", etc.), Subject Scheme enforces a predefined list:

- Author sees a dropdown in oXygen → picks "operator"
- Validation checks every topic against the scheme
- Reporting aggregates consistently

## 2. Why Our Framework Needs It

| Without Subject Scheme | With Subject Scheme |
|------------------------|---------------------|
| `audience="operator"` in one topic, `audience="Operator"` in another | Both validated against the same control list |
| Cannot filter topics by product programmatically | CI/CD can check: "all MC-5000 topics" |
| Authors invent new metadata fields ad-hoc | Only defined fields and values are allowed |
| No automated quality check on metadata | Pre-commit hook can validate against scheme |

## 3. Controlled Values — Complete Reference

### 3.1 `audience`

| Key | Display Name | Description |
|-----|-------------|-------------|
| `operator` | Operator | Machine operators — need "how to run" content |
| `maintenance` | Maintenance Technician | Repair and service content |
| `engineer` | Engineer | Design, application, installation engineering |
| `field-service` | Field Service | On-site commissioning and repair |
| `trainer` | Trainer | Trainers delivering in-person courses |
| `all` | All Audiences | Content relevant to everyone |

### 3.2 `content-type`

| Key | Display Name | DTD |
|-----|-------------|-----|
| `concept` | Concept | `concept.dtd` |
| `task` | Task | `task.dtd` |
| `reference` | Reference | `reference.dtd` |
| `troubleshooting` | Troubleshooting | `troubleshooting.dtd` |
| `glossary-entry` | Glossary Entry | `glossentry.dtd` |
| `hazard-statement` | Hazard Statement | `reference.dtd` + hi-d domain |
| `task-requirements` | Task Requirements | `task.dtd` + pr-d domain |
| `learning-object` | Learning Object | `learningObject.dtd` |

### 3.3 `document-status`

| Key | Description |
|-----|-------------|
| `draft` | Being written — not yet submitted for review |
| `review` | Submitted — in the approval workflow |
| `approved` | Technical and editorial approval received |
| `published` | Released in the latest CI/CD build |
| `archived` | Superseded or removed from active use |

### 3.4 `product`

| Key | Display Name |
|-----|-------------|
| `MC-5000` | MC-5000 VMC |
| `MC-8000` | MC-8000 5-Axis |
| `DR-2000` | DR-2000 High-Speed |
| `common` | Shared / Common |

### 3.5 `hazard-severity`

| Key | Display Name | Meaning |
|-----|-------------|---------|
| `danger` | Danger | Will cause death or serious injury |
| `warning` | Warning | Could cause death or serious injury |
| `caution` | Caution | May cause minor or moderate injury |
| `notice` | Notice | Property damage only |

### 3.6 `skill-level`

| Key | Display Name |
|-----|-------------|
| `level-1` | Level 1 — Trainee (supervised only) |
| `level-2` | Level 2 — Junior Technician |
| `level-3` | Level 3 — Experienced Technician |
| `level-4` | Level 4 — Senior Technician |
| `level-5` | Level 5 — Master Technician / Engineer |

## 4. Implementation in oXygen XML Editor

### 4.1 Setup

1. Save the scheme file to a version-controlled location:
   `04-SUBJECT-SCHEME/METADATA-CONTROLLED-VALUES.ditamap`

2. In oXygen: **Options → Preferences → DITA → Subject Scheme**
   Add the scheme file path.

3. You can now:
   - See dropdown lists for `audience`, `product`, etc. when editing `.dita` files
   - Run **DITA → Validate → Validate with Subject Scheme** to check compliance
   - Generate content references filtered by subject

### 4.2 Git Hook Integration

Add a pre-commit hook to validate subject scheme compliance:

```bash
#!/bin/bash
# .git/hooks/pre-commit — DITA metadata validation
for file in $(git diff --cached --name-only --diff-filter=ACM | grep '\.dita$'); do
  if ! dita validate --subject-scheme=subject-scheme.ditamap "$file"; then
    echo "ERROR: $file failed subject scheme validation"
    exit 1
  fi
done
```

## 5. Extending Subject Scheme

To add a new controlled value (e.g., a new product line):

```xml
<subjectdef keys="DR-9000">
  <topicmeta><navtitle>DR-9000 Heavy-Duty VMC</navtitle></topicmeta>
</subjectdef>
```

Insert this under the `<hasInstance>` block for `product`, and re-run validation. No changes to individual topics required — the scheme is a single source of truth.

## 6. Relationship to Metadata Schema

Subject Scheme works with, not instead of, the metadata schema defined in `02-METADATA-SCHEMA.md`:

| Concern | Handled By |
|---------|-----------|
| Which fields are required? | Metadata Schema |
| What values are allowed? | Subject Scheme |
| Is the topic valid? | Subject Scheme + Quality Checklist |
| How is it displayed in oXygen? | Subject Scheme + oXygen config |

---

**File:** `templates/12-SUBJECT-SCHEME-TEMPLATE.ditamap`  
**Next Review Date:** Q4 2026
