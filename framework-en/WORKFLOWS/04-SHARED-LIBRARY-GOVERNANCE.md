# Shared Library Governance Process

**Document ID:** WF-004
**Version:** 1.0
**Last Updated:** 2026-06-07
**Status:** Approved

---

## Table of Contents

1. [Purpose & Scope](#1-purpose--scope)
2. [Shared Library Structure](#2-shared-library-structure)
3. [Roles & Responsibilities](#3-roles--responsibilities)
4. [Content Lifecycle in Shared Library](#4-content-lifecycle-in-shared-library)
5. [Adding New Content to Shared Library](#5-adding-new-content-to-shared-library)
6. [Modifying Existing Shared Content](#6-modifying-existing-shared-content)
7. [Deprecating & Removing Shared Content](#7-deprecating--removing-shared-content)
8. [Change Communication Rules](#8-change-communication-rules)
9. [Conref Target Stability](#9-conref-target-stability)
10. [Library Versioning Strategy](#10-library-versioning-strategy)
11. [Review Cadence & Quality](#11-review-cadence--quality)
12. [Conflict Resolution](#12-conflict-resolution)
13. [Governance Rules Summary](#13-governance-rules-summary)

---

## 1. Purpose & Scope

### 1.1 Purpose

The shared library (`references/` directory and all conref target topics) is the central asset that drives DITA's content reuse value. Without governance, the shared library degrades into:

- **Orphaned content** — topics nobody knows exist or uses
- **Stale content** — outdated information silently propagated to all products
- **Broken references** — conref targets deleted without checking all dependents
- **Duplicate library entries** — near-identical topics created because authors couldn't find existing ones

This document establishes the governance process to keep the shared library healthy, discoverable, and reliable.

### 1.2 Scope

This process applies to:

| In Scope | Out of Scope |
|----------|-------------|
| `references/` directory topics | Per-product topic directories |
| Conref target topics (`.dita` files with `@id` targets) | Per-product ditamaps |
| Variable definition files (`.ditamap` key definitions) | Per-product variable overrides |
| Shared ditamaps (cross-product maps) | Individual author workspace files |
| Glossary entries | Template files |
| Legal boilerplate topics | — |

### 1.3 Design Principles

1. **Shared once, reused everywhere** — content belongs in the library if two or more products need it
2. **Stability is a service** — authors must be able to trust that library content won't break their topics
3. **Change requires notification** — every library modification must be communicated to all affected authors
4. **Librarian is the gatekeeper** — a designated "Reuse Librarian" role owns the shared library
5. **Discoverability by design** — clear naming, metadata, and indexing for searchability

---

## 2. Shared Library Structure

### 2.1 Directory Layout

```
references/
├── framework-keys.ditamap         # Central key definitions (company, product, standard)
├── variables.ent                  # Legacy XML entity variables
├── en/                            # English shared content
│   ├── company-variables.dita     # Company name, address, contact
│   ├── legal-statements.dita      # Copyright, disclaimer, trademark
│   ├── safety-common.dita         # Shared safety warnings
│   ├── procedures-common.dita     # Cross-product procedures
│   ├── specifications-common.dita # Shared specification tables
│   └── glossary/                  # Glossary entries
├── zh-CN/                         # Chinese shared content
│   ├── company-variables.dita
│   ├── legal-statements.dita
│   ├── safety-common.dita
│   ├── procedures-common.dita
│   ├── specifications-common.dita
│   └── glossary/
├── de/                            # German (Tier-1)
├── fr/                            # French (Tier-1)
└── es/                            # Spanish (Tier-1)
```

### 2.2 Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Library topic files | `{domain}-{purpose}.dita` | `safety-electrical.dita` |
| Conref target IDs | `{topic-shortname}-{target-name}` | `safety-electrical-lockout` |
| Key names | `{category}-{specific-name}` | `company-name`, `standard-iso9001` |
| Glossary entries | `glossentry-{term}` | `glossentry-torque` |
| Variable files | `{language}-{domain}-variables.dita` | `en-company-variables.dita` |

### 2.3 Required Metadata for Library Topics

Every shared library topic MUST include all M1-M7 metadata:

```xml
<prolog>
  <metadata>
    <author>Jane Smith</author>
    <publisher>Documentation Governance Committee</publisher>
    <critdates>
      <created date="2026-01-15"/>
      <revised modified="2026-06-01"/>
    </critdates>
    <audience type="administrator" job="maintenance" level="expert"/>
    <category>Safety</category>
    <prodinfo>
      <prodname>Shared Library</prodname>
      <vrmlist>
        <vrm version="1.0" release="0" modification="3"/>
      </vrmlist>
    </prodinfo>
  </metadata>
</prolog>
```

---

## 3. Roles & Responsibilities

### 3.1 Shared Library Roles

| Role | Assigned To | Responsibilities |
|------|------------|-----------------|
| **Reuse Librarian** | Senior Technical Writer | Gatekeeper for all library changes, quality owner |
| **Content Architect** | Documentation Architect | Design library structure, naming, metadata schema |
| **Library Contributors** | All Technical Writers | Propose additions, report issues, maintain their entries |
| **Change Reviewers** | Peer Writers + SME | Review proposed changes for correctness and impact |
| **DGC (Governance)** | Documentation Governance Committee | Approve structural changes, resolve disputes, set policy |

### 3.2 Reuse Librarian — Detailed Duties

The Reuse Librarian is the most critical role for library health:

**Weekly:**
- Review and merge or reject pending library change requests
- Verify all conref targets are still referenced by at least one topic
- Respond to library-related questions from the author team

**Monthly:**
- Run the reuse scanner and report metrics to the DGC
- Identify orphaned or stale library content
- Facilitate the monthly library review meeting

**Quarterly:**
- Conduct full library audit (content accuracy, metadata completeness)
- Publish library health report
- Recommend library structure improvements

**Incidental:**
- Investigate and resolve broken conref references
- Coordinate emergency library changes (e.g., safety content updates)
- Train new authors on library usage

---

## 4. Content Lifecycle in Shared Library

### 4.1 Lifecycle States

```
[DRAFT] → [REVIEW] → [APPROVED] → [PUBLISHED] → [DEPRECATED] → [ARCHIVED]
                               ↘ [REJECTED] ↗
```

| State | Description | Who Can Transition |
|-------|------------|-------------------|
| **DRAFT** | Proposed library content, not yet reviewed | Any Library Contributor |
| **REVIEW** | Under active review | Librarian (moves from DRAFT) |
| **APPROVED** | Reviewed and approved, awaiting next publish cycle | Change Reviewers |
| **PUBLISHED** | Live in shared library, available for all authors | Librarian |
| **DEPRECATED** | Still available but marked for removal | DGC |
| **ARCHIVED** | Removed from library, kept in git history only | Librarian |
| **REJECTED** | Review determined not suitable for library | Change Reviewers |

### 4.2 State Transition Rules

| From | To | Requires | Notification |
|------|----|----------|-------------|
| DRAFT | REVIEW | Complete metadata + 2 use cases | Contributor notified |
| REVIEW | APPROVED | 2 peer approvals + SME sign-off | Reviewers notified |
| APPROVED | PUBLISHED | Librarian approval | All authors notified |
| PUBLISHED | DEPRECATED | DGC vote + impact analysis | All authors + consumers notified |
| DEPRECATED | ARCHIVED | 90-day notice period + 0 active references | All teams confirmed |
| REVIEW | REJECTED | Documented reason from any reviewer | Contributor notified |

---

## 5. Adding New Content to Shared Library

### 5.1 Eligibility Criteria

Content qualifies for the shared library if it meets **any** of these criteria:

- [ ] Used by 2+ products or product families
- [ ] Required for compliance/regulatory purposes (safety, legal)
- [ ] Company-level variable definitions (name, address, standards)
- [ ] Glossary terms with formal definitions
- [ ] Procedures that are identical across 2+ products

### 5.2 Submission Process

**Step 1: Proposal** (Author)
1. Identify content eligible for sharing
2. Create a DRAFT topic in `references/` on a feature branch
3. Include complete M1-M7 metadata
4. Create a pull request labeled `library-add`

**Step 2: Eligibility Check** (Librarian — 1 business day)
1. Verify eligibility criteria are met
2. Check naming convention compliance
3. Confirm no duplicate content exists
4. Assign change reviewers

**Step 3: Peer Review** (2 reviewers — 2 business days)
1. Review technical accuracy
2. Verify conref targets are correctly marked with `@id`
3. Confirm the topic follows style guide
4. Test that key definitions resolve correctly

**Step 4: Librarian Approval** (Librarian — 1 business day)
1. Final quality check
2. Merge to main branch
3. Announce new library content to all authors

### 5.3 PR Template for Library Additions

```markdown
## Library Addition Proposal

**Topic:** [brief title]
**File:** references/[language]/[filename].dita

### Eligibility
- [ ] Used by 2+ products — which: [list]
- [ ] Compliance/regulatory requirement
- [ ] Company-level variable
- [ ] Glossary term
- [ ] Cross-product procedure

### Metadata
- Author: [name]
- Audience: [type/job/level]
- Category: [choose one]

### Impact
- Products affected: [all / specific]
- Key definitions added: [key names]

### Reviewers
- SME: [name]
- Peer: [name]
```

---

## 6. Modifying Existing Shared Content

### 6.1 Change Classification

| Class | Definition | Examples | Urgency |
|-------|-----------|----------|---------|
| **Patch** | No content change, fixes only | Typo correction, formatting fix | Low |
| **Minor** | Content changes that don't break references | Updated procedure step, new example | Medium |
| **Major** | Content changes that may affect referencing topics | Restructured section, changed values | High |
| **Breaking** | Content changes that will affect referencing topics | Deleted conref target, changed key definition | Critical |

### 6.2 Modification Workflow

```
Patch:      Author fixes → Librarian reviews → Merge → Announce
Minor:      Author branches → Peer review → Librarian → Merge → Announce
Major:      Author branches → Impact analysis → SME review → Librarian → Merge → Announce
Breaking:   DGC approves → Impact analysis → 2-week notice → Migration plan → Execute → Verify
```

### 6.3 Impact Analysis Requirement

For **Major** and **Breaking** changes, the author MUST provide:

```markdown
## Impact Analysis

### Content Impact
- Files referencing this library topic: [count and list]
- Maps referencing this library topic: [count and list]
- Conref targets affected: [list of @id values]

### Reference Impact
- Topics using conref to this file: [count]
- Key references to affected definitions: [count]
- Glossary references to affected entries: [count]

### Migration Plan
1. [Step-by-step update plan]
2. Estimated effort: [hours]
3. Rollback procedure: [description]
```

### 6.4 Version Tracking for Modified Content

Every library topic should track its version in metadata:

```xml
<critdates>
  <created date="2026-01-15"/>
  <revised modified="2026-06-01">Updated electrical specifications per ISO 13849-1:2025</revised>
</critdates>
```

---

## 7. Deprecating & Removing Shared Content

### 7.1 Deprecation Workflow

1. **Proposal**: DGC member proposes deprecation with justification
2. **Impact Assessment**: Librarian identifies all referencing topics
3. **Migration**: Authors of referencing topics update their content
4. **Notice Period**: 90-day deprecation notice for PUBLISHED content
5. **Remove References**: All references must be replaced before removal
6. **Archive**: Content moved to git history (not deleted from git)

### 7.2 Deprecation Notice Template

```markdown
## DEPRECATION NOTICE

**Topic:** [filename.dita]
**Deprecation Date:** [date]
**Scheduled Removal:** [date + 90 days]
**Reason:** [justification]
**Replacement:** [alternative topic or key, if any]

### Action Required
If your topics reference this content, update them before the removal date.

### References Remaining
- [count] direct conref references
- [count] map references
- [count] key references

### Contact
[Librarian name] for questions or migration assistance.
```

### 7.3 Emergency Removal

Content may be removed immediately (no notice period) only in these cases:

| Case | Example | Action |
|------|---------|--------|
| **Safety error** | Incorrect safety procedure | Remove + issue safety advisory |
| **Legal violation** | Copyrighted content, regulatory violation | Remove + notify legal department |
| **Security issue** | Exposed credentials, internal data | Remove + security audit |

Emergency removal requires DGC chair approval and MUST be followed by an incident report within 24 hours.

---

## 8. Change Communication Rules

### 8.1 Notification Matrix

| Change Class | Notification Method | Audience | Lead Time |
|-------------|-------------------|----------|-----------|
| Patch | PR comment | Librarian + author | Immediate |
| Minor | Team channel message | All technical writers | 1 business day |
| Major | Email + team channel | All authors + affected SMEs | 3 business days |
| Breaking | Email + DGC meeting | All authors + DGC + product managers | 2 weeks |
| Deprecation | Email + DGC meeting | All authors + all product managers | 90 days |
| Emergency | Immediate notification | All authors + DGC + legal | Immediate |

### 8.2 Communication Template

```markdown
## Shared Library Update

**Type:** [Patch / Minor / Major / Breaking / Deprecation / Emergency]
**Topic:** [filename.dita]
**Change Summary:** [1-2 sentences]
**Author:** [name]
**Date:** [date]

### Details
[Detailed description of the change]

### Impact
- Products affected: [list]
- Authors who need to act: [list]
- Action required: [description]

### Timeline
- Effective date: [date]
- Migration deadline (if applicable): [date]
```

### 8.3 Subscription Model

Authors can subscribe to notification levels:

| Level | Get Notified For | Typical Role |
|-------|-----------------|-------------|
| **All changes** | Every library modification | Lead writers, content architects |
| **Major+ only** | Major, Breaking, Deprecation, Emergency | Product managers |
| **Breaking+** | Breaking, Deprecation, Emergency | Executives, compliance |
| **Emergency only** | Emergency safety/legal changes | All (mandatory) |

---

## 9. Conref Target Stability

### 9.1 The Golden Rule

> **Never delete or rename a conref target `@id` without verifying zero active references.**

A conref target's `@id` is a contract. Once published, authors across the organization may reference it. Breaking that contract silently propagates errors across all products.

### 9.2 Conref Target Change Rules

| Action | Allowed? | Condition |
|--------|----------|-----------|
| Add new `@id` | ✅ Always | No impact |
| Modify content of existing `@id` | ✅ With caution | Must not change semantic meaning without notification |
| Rename existing `@id` | ❌ Never | Add new ID, deprecate old one |
| Delete existing `@id` | ❌ Never | Deprecate first, verify 0 references after 90 days |
| Change `@id` value | ❌ Never | Same as rename |

### 9.3 Conref Target Verification Script

```bash
#!/bin/bash
# Verify all conref targets in the shared library are referenced

echo "=== Conref Target Reference Check ==="

# Collect all @id values from shared library topics
find references/ -name "*.dita" | while read lib_topic; do
    grep -oP 'id="[^"]*"' "$lib_topic" | sed 's/id="//;s/"//' | while read target_id; do
        # Search for conref references to this target
        ref_count=$(grep -r "conref=.*#.*/$target_id\"" --include="*.dita" . \
            -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
        
        if [ "$ref_count" -eq 0 ]; then
            echo "⚠️  UNREFERENCED: $target_id in $lib_topic"
        else
            echo "✅  OK ($ref_count refs): $target_id"
        fi
    done
done
```

### 9.4 Schematron Rule

```xml
<sch:pattern id="conref-stability">
  <!-- Block conref to deprecated topics -->
  <sch:rule context="*[@conref]">
    <sch:let name="refid" value="substring-after(@conref, '#')"/>
    <sch:let name="topic" value="substring-before(substring-after(@conref, 'references/'), '#')"/>
    <sch:assert test="not(doc(concat('references/', $topic))//*[@id=$refid]/ancestor::*[@status='deprecated'])">
      [SL-B1] Cannot conref to deprecated content: @conref='{@conref}'
    </sch:assert>
  </sch:rule>
</sch:pattern>
```

---

## 10. Library Versioning Strategy

### 10.1 Version Number Scheme

Library topics use semantic versioning: `MAJOR.MINOR.PATCH`

| Component | Increment When | Example |
|-----------|---------------|---------|
| **MAJOR** | Breaking change (conref target removed, key definition changed) | 1.0.0 → 2.0.0 |
| **MINOR** | Content added, modified (new section, updated values) | 1.0.0 → 1.1.0 |
| **PATCH** | Fixes only (typo, formatting) | 1.0.0 → 1.0.1 |

### 10.2 Version Tracking

Track library topic versions in two places:

1. **File metadata** (`<vrmlist>` in prolog):
```xml
<vrmlist>
  <vrm version="1" release="2" modification="3"/>
</vrmlist>
<!-- Represents version 1.2.3 -->
```

2. **CHANGELOG** (`references/CHANGELOG.md`):
```markdown
# Shared Library Changelog

## 2026-06-07
### safety-common.dita v1.1.0
- Added electrical lockout procedure target (`#safety-electrical-lockout`)
- Updated PPE requirements per ISO 13849-1:2025

### company-variables.dita v1.0.2
- Fixed typo in company address (Postal code)
```

### 10.3 Version Compatibility

| Library Version | Compatible With | Notes |
|----------------|----------------|-------|
| Same MAJOR | All consumers | Safe to update |
| New MAJOR | Consumer must update references | Breaking changes |
| Deprecated | Consumer should migrate | No longer maintained |

---

## 11. Review Cadence & Quality

### 11.1 Review Schedule

| Review Type | Frequency | Participants | Focus |
|------------|-----------|-------------|-------|
| **Weekly triage** | Weekly | Librarian | New submissions, pending reviews |
| **Monthly health check** | Monthly | Librarian + Content Architect | Reference counts, orphan detection |
| **Quarterly audit** | Quarterly | Librarian + DGC | Full content audit, metadata completeness |
| **Annual structural review** | Annually | DGC + Content Architect | Library structure, naming, efficiency |

### 11.2 Quality Criteria for Library Content

| Criterion | Standard | Measured By |
|-----------|----------|-------------|
| Metadata completeness | M1-M7 all present | Schematron check |
| Conref target coverage | Each target referenced by 1+ topic | Weekly scan |
| Duplicate detection | No near-duplicate content | Quarterly audit |
| Freshness | No content older than 12 months without review | Quarterly audit |
| Cross-language parity | All language versions exist for Tier-1 | Monthly check |
| Naming compliance | Follows naming conventions | PR review |

### 11.3 Library Health Score

Calculate a library health score each quarter:

```
Health Score = (R + C + D + F + L + N) / 6 × 100

R = Topics with 1+ references / Total library topics
C = Topics with complete metadata / Total library topics
D = Unique content topics / Total library topics (1 - duplicate ratio)
F = Topics reviewed within 12 months / Total library topics
L = Languages with complete parity / Total Tier-1 languages
N = Topics following naming convention / Total library topics
```

**Target Health Score: > 85%**

---

## 12. Conflict Resolution

### 12.1 Common Conflicts

| Conflict | Example | Resolution Process |
|----------|---------|-------------------|
| **Ownership dispute** | Two teams claim ownership of a library topic | Escalate to DGC for decision |
| **Content disagreement** | SMEs disagree on procedure steps | Facilitate meeting, document both views |
| **Naming conflict** | Two proposed files with same name | Librarian decides, adds alias if needed |
| **Priority conflict** | Two teams need conflicting changes simultaneously | DGC prioritizes by release schedule |
| **Quality dispute** | Author disputes rejection of library submission | Librarian + Content Architect final review |

### 12.2 Escalation Path

```
1. Author ↔ Librarian (discuss and resolve)
2. Librarian ↔ Content Architect (if unresolved)
3. DGC Chair (if no agreement at step 2)
4. DGC vote (final, binding)
```

---

## 13. Governance Rules Summary

### 13.1 Blocking Rules

| Rule ID | Rule | Schematron | Violation Action |
|---------|------|-----------|-----------------|
| **SL-B1** | Do not conref to deprecated content | ✅ | Block PR |
| **SL-B2** | All library topics must have complete M1-M7 metadata | ✅ | Block PR |
| **SL-B3** | Do not delete conref target without 0 reference verification | Manual | Block merge |
| **SL-B4** | New library content must have 2+ product use cases | Manual | Block PR |
| **SL-B5** | Major/breaking changes require impact analysis | Manual | Block PR |
| **SL-B6** | Breaking changes require 2-week notice | Manual | Block merge |

### 13.2 Warning Rules

| Rule ID | Rule | Action |
|---------|------|--------|
| **SL-W1** | Library topic has no references in 90+ days | Flag for deprecation |
| **SL-W2** | Library topic not reviewed in 12+ months | Flag for audit |
| **SL-W3** | Library topic missing one or more Tier-1 language versions | Flag for translation |
| **SL-W4** | Conref target referenced by 0 topics | Flag for removal |
| **SL-W5** | Library topic with >500 words (may be too large for single topic) | Flag for review |

### 13.3 Quick Reference Card

```
┌─────────────────────────────────────────────────────────┐
│              SHARED LIBRARY — QUICK RULES               │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ✅ DO                                               │
│  · Propose shared content when 2+ products need it     │
│  · Always include M1-M7 metadata                       │
│  · Announce changes before merging                     │
│  · Use keyref for variable values                      │
│  · Add new @id instead of renaming existing ones       │
│                                                         │
│  ❌ DON'T                                             │
│  · Delete or rename conref targets without checking     │
│  · Modify library content without peer review          │
│  · Create near-duplicate library topics                │
│  · Bypass the Reuse Librarian review                   │
│  · Merge breaking changes without 2-week notice        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## Related Documents

| Document | Location |
|----------|----------|
| Variable Management | [ARCHITECTURE/06-VARIABLE-MANAGEMENT.md](../ARCHITECTURE/06-VARIABLE-MANAGEMENT.md) |
| Reuse Rate Calculation | [WORKFLOWS/03-REUSE-RATE-CALCULATION.md](03-REUSE-RATE-CALCULATION.md) |
| Approval Workflow | [WORKFLOWS/01-APPROVAL-WORKFLOW.md](01-APPROVAL-WORKFLOW.md) |
| Document Set Integrity | [WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md](02-DOCUMENT-SET-INTEGRITY.md) |
| Quality Checklist | [STYLE-GUIDE/03-QUALITY-CHECKLIST.md](../STYLE-GUIDE/03-QUALITY-CHECKLIST.md) |
| Governance Charter | [GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md](../GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md) |
