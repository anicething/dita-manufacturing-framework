# Change Authority Matrix — 示例制造 (Filled Example)

> Reference: Framework → 01-GOVERNANCE → 03-CHANGE-AUTHORITY-MATRIX.md

## 1. Change Categories

| Category | Definition | Example | Authority Level |
|---|---|---|---|
| **Type 1: Minor** | Cosmetic, non-substantive changes | Fixing typo, reformatting table | **Self-service** (Author may approve) |
| **Type 2: Moderate** | Content update without structural change | Updating torque spec, adding note | **Tech Lead approval** required |
| **Type 3: Major** | Structural or substantive change | Adding new chapter, rewriting procedure | **Full governance review** required |
| **Type 4: Critical** | Safety, compliance, or regulatory change | Updating safety warning, ISO re-cert | **Engineering Director + Compliance** approval required |

## 2. Authority Matrix by Change Type

| Action | Type 1 (Minor) | Type 2 (Moderate) | Type 3 (Major) | Type 4 (Critical) |
|---|---|---|---|---|
| Author | **Approve** | Propose | Propose | Propose |
| Tech Writing Lead | Inform | **Approve** | Recommend | Recommend |
| SME (Domain Expert) | — | Recommend | **Approve** | Recommend |
| Engineering Director | — | Inform | Inform | **Approve** |
| Compliance Officer | — | — | Inform | **Approve** |
| DGC Chair (Monthly) | — | — | Inform | Inform |

## 3. Escalation Rules

- **Disagreement**: Any reviewer may escalate to the next level in the authority chain.
- **Deadline pressure**: If SLA is at risk (> 8 business days), the Engineering Director may grant expedited approval for Type 2 changes.
- **Safety-critical**: Any Type 4 change must undergo a 48-hour mandatory waiting period after final approval before publishing — no exceptions.

## 4. Real-World Scenario: Changing the MC-5000 Spindle Torque Spec

**Context**: After field testing, the mechanical engineering team determines the MC-5000 spindle continuous torque should be revised from 120 Nm to 130 Nm (S1 rating).

**Change classification**: Type 2 (Moderate) — a numerical update to an existing specification, no structural change.

**Process flow**:
```
Day 1:  Author (Sun Lei, Mechanical Eng.) submits change request in GitHub Issue
Day 1:  Tech Writing Lead (Li Na) validates scope → assigns SME
Day 2:  SME Review (Huang Tao, Electrical Eng.) — confirms motor drive supports 130 Nm
Day 3:  Tech Writing Lead approves; update merged to staging branch
Day 4:  CI/CD builds updated PDF/HTML; editorial check passes
Day 5:  Staged for next publishing cycle
```

**Total: 5 business days** (within SLA)

**Change artifacts**:
- Updated SPECS-REFERENCE.dita → spindle table row
- Metadata update: `revision-field` incremented, `change-log` entry added
- GitHub PR #142 — referenced in release notes
