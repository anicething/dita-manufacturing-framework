# Contributing to DITA Manufacturing Framework

**Applies to:** DITA Manufacturing Framework (English)  
**Last Updated:** 2026-06-07

---

## Overview

This framework is designed to be customized and improved for your organization. We welcome contributions that enhance the governance, architecture, templates, or training materials. The English version (`framework-en/`) is the authoritative source; all translations derive from it.

---

## Types of Contributions

### Core Framework Changes
| Area | Description | Owner |
|------|-------------|-------|
| Governance | Charter, ownership matrix, authority matrix | DGC |
| Architecture | Content architecture, metadata schema, topic types | DITA Architect |
| Style Guide | Writing rules, formatting standards, terminology | DITA Architect |
| Templates | DITA topic templates, ditamaps, metadata skeletons | DITA Architect |
| Tooling | Publishing scripts, CI/CD pipeline, transforms | IT/Infrastructure Lead |
| Translations | Localized versions of framework documents | Localization Manager |

### Product Content Contributions
Product-specific documentation is managed through the [Content Ownership Matrix](./GOVERNANCE/02-CONTENT-OWNERSHIP-MATRIX.md). Product writers should follow their product line's approval workflow.

### Translation Contributions
Official translations (zh-CN, ja-JP, ko-KR, de-DE, fr-FR, es-ES, pt-BR) are coordinated by the Localization Manager.

---

## Contribution Workflow

### For Governance & Framework Documents
1. Identify the change needed by reviewing existing governance docs (GOV-001, GOV-002, GOV-003)
2. Create a git branch: `feature/gov-<description>-<initials>`
3. Make changes on the branch
4. Self-review against criteria (see Review Criteria below)
5. Submit a Pull Request (PR) with:
   - Clear description of changes
   - Rationale for the change
   - References to affected documents/roles
   - Risk assessment
6. Obtain approvals per the Change Authority Matrix
7. Merge to main and close PR once approved

### For Templates & Specializations
1. Discuss proposed changes with the DITA Architect
2. Develop schema/DTD/XSD/template changes on a branch
3. Test changes against sample content from at least two product lines
4. Update metadata schema if applicable
5. Submit PR for architecture review
6. DITA Architect approves 1-week grace period before production adoption
7. Notify all writers of new template/specialization availability

---

## Branch Strategy & Git Conventions

### Branch Naming
```
feature/gov-<description>       — Governance document changes
feature/content-<description>    — Product content changes
feature/template-<description>   — Template or specialization changes
feature/tooling-<description>    — Tooling and pipeline changes
fix/<description>                — Bug fixes
translation/<lang>-<description> — Translation contributions
```

### Commit Messages
- 72-character line limit
- Use imperative mood ("Add safety topic" not "Added safety topic")
- Reference document IDs: `[GOV-001] Update review frequency section`
- Reference change requests: `[CR-2026-001] Add new calibration topic`

### PR Requirements
- PR title clearly describes the change
- PR description includes: what, why, who is affected, risk
- All review comments must be resolved before merge
- Minimum one approver (per Change Authority Matrix)

---

## File Organization

```
framework-en/                       # English (authoritative source)
├── GOVERNANCE/                     # Charter, ownership, change authority
├── ARCHITECTURE/                   # Content design, metadata, topic types, variables
├── STYLE-GUIDE/                    # Writing standards, glossary, quality checklist
├── WORKFLOWS/                      # Approval, document integrity, reuse, library
├── TOOLS/                          # Tool setup and configuration
├── TRAINING/                       # Onboarding, quick start, exercises, certification
├── CI-CD/                          # CI/CD pipeline, DITA-OT config, publishing SLA
├── PUBLISHING/                     # Cross-format consistency
├── IMPLEMENTATION-GUIDE.md         # 6-month adoption roadmap
├── RISK-CONTROL-STRATEGY.md        # Risk register and controls
├── INDEX.md                        # Document registry
├── README.md
└── CONTRIBUTING.md
```

- **framework-en/** is the authoritative source. All other languages are translations.
- When discrepancies arise, the `framework-en/` version prevails.
- Changes to `framework-en/` source files trigger update requests for affected translations.

---

## Style Guidelines

### Markdown
- Use consistent heading levels
- Include table of contents for long documents
- Use code blocks for XML/DITA examples
- Link to related documents

### DITA
- Follow OASIS DITA 1.3 standards
- Include all required metadata
- Use conrefs and keyrefs for shared content
- Validate XML syntax

### Metadata
- Update Document ID when changing a document
- Update version number
- Include last updated date
- Note status (Draft / Approved / Superseded)

---

## Review Criteria

All contributions are evaluated against:

| Criterion | Description |
|-----------|-------------|
| Alignment | Does the change align with framework goals? |
| Compliance | What are the ISO compliance implications? |
| Impact | How does it affect multiple products? |
| Clarity | Is the change clear and complete? |
| Consistency | Does it match existing standards? |
| Security | Does it expose sensitive information? |

---

## Communication Channels

| Purpose | Channel | Audience |
|---------|---------|----------|
| Governance discussions & approvals | DGC meetings (monthly) | All DGC members |
| Architecture & standards | Architecture team channel | Architects, senior writers |
| Writer support & questions | Writers channel | All writers |
| Tooling & pipeline issues | IT support ticketing system | IT/Infrastructure Lead |
| Translation coordination | Localization channel | Localization Manager, translators |

---

## Getting Started for First-Time Contributors

1. Read the [Governance Charter (GOV-001)](./GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md)
2. Understand the [Content Ownership Matrix (GOV-002)](./GOVERNANCE/02-CONTENT-OWNERSHIP-MATRIX.md)
3. Review the [Change Authority Matrix (GOV-003)](./GOVERNANCE/03-CHANGE-AUTHORITY-MATRIX.md)
4. Configure Git with appropriate repository access
5. Join the writers communication channel
6. Claim an issue labeled `good first issue` or ask your product manager for assignments
7. Attend the next scheduled DGC meeting as an observer

---

## License

Contributions to this framework are released under the same license as the framework itself. See [LICENSE](../LICENSE) at the project root.

---

## Questions?

Contact the Documentation Governance Committee or open an issue in the repository.

Thank you for improving our DITA framework!
