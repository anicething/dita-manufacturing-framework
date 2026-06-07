# Risk Control Strategy — DITA Manufacturing Framework

**Document ID:** RCS-001
**Version:** v1.0 | **Last Updated:** 2026-06-07

---

## Table of Contents

1. [Risk Management Framework](#1-risk-management-framework)
2. [Risk Classification & Levels](#2-risk-classification--levels)
3. [Risk Register: Implementation Risks](#3-risk-register-implementation-risks)
4. [Risk Register: Operational Risks](#4-risk-register-operational-risks)
5. [Risk Register: Content Quality Risks](#5-risk-register-content-quality-risks)
6. [Risk Register: Technology Risks](#6-risk-register-technology-risks)
7. [Risk Register: Organizational Risks](#7-risk-register-organizational-risks)
8. [Control Points by Phase](#8-control-points-by-phase)
9. [Emergency Response Procedures](#9-emergency-response-procedures)
10. [Monitoring & Review](#10-monitoring--review)

---

## 1. Risk Management Framework

### 1.1 Objective

Establish a systematic approach to identify, assess, control, and monitor risks throughout the DITA framework lifecycle — from initial implementation through ongoing operations.

### 1.2 Risk Management Process

```
Identify → Assess (P×I) → Plan Controls → Implement → Monitor → Review → (loop)
```

| Step | Description | Owner | Frequency |
|------|-------------|-------|-----------|
| Identify | Discover potential risks via workshops, audits, retrospectives | CoE Lead | Continuous |
| Assess | Score probability × impact to determine risk level | CoE Lead + DGC | Monthly |
| Plan Controls | Define prevention (proactive) and contingency (reactive) measures | CoE Lead | Per risk |
| Implement | Execute control measures, assign owners | Risk Owner | As scheduled |
| Monitor | Track risk indicators, control effectiveness | CoE Lead | Weekly (automated) |
| Review | Reassess risk level quarterly; close resolved risks | DGC | Quarterly |

### 1.3 Risk Ownership

| Role | Risk Ownership |
|------|---------------|
| **CoE Lead** | Overall risk management, escalation |
| **Content Architect** | Content quality risks, metadata integrity |
| **IT Lead** | Technology/toolchain risks, data loss |
| **Training Lead** | Skills gap, adoption risks |
| **DGC (Governance)** | Organizational risks, compliance, strategic |
| **All authors** | Content quality risks (first line of defense) |

---

## 2. Risk Classification & Levels

### 2.1 Risk Scoring Matrix

| Probability \ Impact | Negligible (1) | Minor (2) | Moderate (3) | Major (4) | Critical (5) |
|---------------------|---------------|-----------|-------------|-----------|--------------|
| **Almost Certain (5)** | Medium (5) | High (10) | High (15) | Extreme (20) | Extreme (25) |
| **Likely (4)** | Low (4) | Medium (8) | High (12) | High (16) | Extreme (20) |
| **Possible (3)** | Low (3) | Medium (6) | Medium (9) | High (12) | High (15) |
| **Unlikely (2)** | Low (2) | Low (4) | Medium (6) | Medium (8) | High (10) |
| **Rare (1)** | Low (1) | Low (2) | Low (3) | Medium (4) | Medium (5) |

### 2.2 Risk Level Definitions

| Level | Score Range | Response | Reporting |
|-------|-------------|----------|-----------|
| **Low** | 1-3 | Accept — monitor only | Logged in register |
| **Medium** | 4-8 | Mitigate — assign owner, implement controls | Reviewed monthly |
| **High** | 9-16 | Actively manage — dedicated control plan | Reviewed weekly; report to DGC |
| **Extreme** | 17-25 | Immediate escalation — executive sponsor | Daily monitoring; emergency DGC |

### 2.3 Risk Categories

```
RISK-XXX-[Category Code]
Implementation: IMP
Operational:    OPS
Content:        CON
Technology:     TEC
Organizational: ORG
```

---

## 3. Risk Register: Implementation Risks

Risks specific to the framework adoption project (Phases 0-3, ~24 weeks).

| ID | Risk | P | I | Score | Level | Prevention | Contingency | Owner |
|----|------|---|---|-------|-------|------------|-------------|-------|
| RISK-IMP-01 | **Low author adoption** — writers resist switching from Word/FrameMaker | 4 | 4 | 16 | **High** | Early & continuous training; executive mandate; peer champions program; show quick wins in pilot | Mandatory DITA certification; performance metrics tied to DITA adoption | CoE Lead |
| RISK-IMP-02 | **SME unavailability** — subject matter experts too busy to review content | 4 | 3 | 12 | **High** | Schedule SME time upfront; batch reviews; limit SME workload to 0.25-0.5 FTE per project | SME peer review; author SME shadowing; escalation to product VP | PM |
| RISK-IMP-03 | **Scope creep** — pilot expands beyond agreed boundaries | 3 | 3 | 9 | **Medium** | Written scope agreement signed by all stakeholders; strict change control board | Freeze pilot; defer additions to next wave | CoE Lead + PM |
| RISK-IMP-04 | **Template iteration loop** — never-ending template refinement delays content creation | 3 | 3 | 9 | **Medium** | Time-box template customization to 2 weeks; "good enough" criterion; log improvements for v2 | Template freeze after pilot kickoff | Content Arch. |
| RISK-IMP-05 | **Perfectionism / 80% trap** — authors spend too long polishing individual topics | 3 | 2 | 6 | **Medium** | "Publish first, polish later" policy; set time budgets per topic (4h concept, 6h task) | Peer review cutoff; editorial triage | Writing Lead |
| RISK-IMP-06 | **Underestimated migration effort** — legacy content volume exceeds capacity | 3 | 4 | 12 | **High** | Thorough content audit in Phase 0; triage matrix (P1-P5); realistic timeline | Reduce migration scope; archive P5 content in original format | CoE Lead |
| RISK-IMP-07 | **Key stakeholder disengagement** — executives lose interest after kickoff | 2 | 4 | 8 | **Medium** | Monthly executive dashboard; celebrate milestones publicly ("first DITA topic published!") | Schedule quarterly face-to-face review with sponsor | CoE Lead |
| RISK-IMP-08 | **Training not sticking** — classroom learning doesn't transfer to day-to-day work | 3 | 3 | 9 | **Medium** | Hands-on, project-based training (not lecture); paired authoring for first 2 weeks; cheat sheets & quick reference cards | Follow-up clinic sessions; peer mentoring program | Training Lead |

---

## 4. Risk Register: Operational Risks

Risks that emerge during steady-state operations (Phase 4+).

| ID | Risk | P | I | Score | Level | Prevention | Contingency | Owner |
|----|------|---|---|-------|-------|------------|-------------|-------|
| RISK-OPS-01 | **Pipeline failure** — CI/CD build breaks, no output delivered on schedule | 3 | 3 | 9 | **Medium** | Automated build testing; staging environment; build monitoring alerts | Fallback to last-known-good build; manual publish procedure | IT Lead |
| RISK-OPS-02 | **Translation bottleneck** — localization team can't keep up with content velocity | 3 | 3 | 9 | **Medium** | Parallelize translation with authoring; TMS automation; translation memory | Priority languages only (Tier 1); defer Tier 2 languages | L10n Mgr |
| RISK-OPS-03 | **Key person dependency** — single point of failure (e.g., only one person knows DITA-OT) | 3 | 4 | 12 | **High** | Cross-training; documented runbooks; at least 2 people per role | Emergency contractor; vendor support agreement | CoE Lead |
| RISK-OPS-04 | **DITA-OT version upgrade breaks build** | 2 | 4 | 8 | **Medium** | Pin DITA-OT version in CI; test upgrade in staging environment; read changelog before upgrading | Rollback to previous version; delay upgrade by 1 quarter | IT Lead |
| RISK-OPS-05 | **Repository corruption / data loss** | 1 | 5 | 5 | **Medium** | Offsite backups; Git best practices (no force push to main); branch protection rules | Restore from backup; git reflog recovery | IT Lead |
| RISK-OPS-06 | **License / renewal surprises** — oXygen or other tool licenses expire | 2 | 3 | 6 | **Medium** | Calendar reminders 90 days before renewal; annual budget planning includes all licenses | Emergency PO process; temporary open-source alternatives (VS Code + DITA plugin) | IT Lead |
| RISK-OPS-07 | **Content drift** — topics fall out of date; no one updates them | 3 | 3 | 9 | **Medium** | Automated "last-reviewed" date tracking; quarterly content audit rotation; dashboards showing stale content | Flag stale topics as "Archived" in output; assign refresh owners | Content Arch. |

---

## 5. Risk Register: Content Quality Risks

Risks that degrade the quality, consistency, or accuracy of published content.

| ID | Risk | P | I | Score | Level | Prevention | Contingency | Owner |
|----|------|---|---|-------|-------|------------|-------------|-------|
| RISK-CON-01 | **Metadata inconsistency** — M1-M7 rules not followed, metadata is incomplete or wrong | 4 | 3 | 12 | **High** | Schematron validation in CI/CD; template-enforced defaults; pre-commit hook checks | Automated metadata repair script; weekly quality report with top offenders | Content Arch. |
| RISK-CON-02 | **Hard-coded variables** — authors write company/product names directly instead of using keyref | 4 | 3 | 12 | **High** | Schematron rule flagging hard-coded values; template placeholders use keyref by default; code review checklist | Automated sed replacement script; add to quality checklist | Content Arch. |
| RISK-CON-03 | **Broken conref / keyref** — content references break after restructuring | 3 | 3 | 9 | **Medium** | CI/CD build checks all references; automated link checker; never delete a conref target without checking references | Git revert the breaking change; restore from backup | All authors |
| RISK-CON-04 | **Translation quality degradation** — machine translation or rushed translators produce poor output | 3 | 3 | 9 | **Medium** | Translation memory; terminology database; bilingual reviewer in loop; quality sampling | Reject poor translation; escalate to TMS vendor | L10n Mgr |
| RISK-CON-05 | **SME technical accuracy gaps** — content contains factual errors | 3 | 4 | 12 | **High** | Mandatory SME review for all technical topics; signed-off review checklist | Errata process; emergency correction within 24h for safety-critical errors | SME Lead |
| RISK-CON-06 | **Conditional text bleed** — DITAVAL-filtered content leaks into wrong output | 2 | 3 | 6 | **Medium** | Validate with multiple DITAVAL profiles in CI/CD; Schematron rule: no bare conditional without matching DITAVAL | Patch the output; republish within 24h | Content Arch. |
| RISK-CON-07 | **Inconsistent terminology** — multiple terms for the same concept across topics | 3 | 2 | 6 | **Medium** | Centralized glossary (glossentry topics); automated term audit; style guide enforces approved terms | Batch replace with sed; update glossary | Content Arch. |

---

## 6. Risk Register: Technology Risks

Risks related to tools, infrastructure, and automation.

| ID | Risk | P | I | Score | Level | Prevention | Contingency | Owner |
|----|------|---|---|-------|-------|------------|-------------|-------|
| RISK-TEC-01 | **DITA-OT plugin incompatibility** — custom plugins break after DITA-OT upgrade | 2 | 4 | 8 | **Medium** | Plugin version pinning; test upgrade in isolated environment | Rollback; defer upgrade | IT Lead |
| RISK-TEC-02 | **Build performance degradation** — build time grows linearly with content volume | 3 | 2 | 6 | **Medium** | Incremental builds; parallel processing; build time dashboards | Increase CI runner capacity; optimize DITA-OT parameters | IT Lead |
| RISK-TEC-03 | **Schema/DTD validation false positives** — strict validation blocks valid content | 2 | 2 | 4 | **Low** | Customize DTD to remove unnecessary constraints; maintain override catalog | Skip validation temporarily; fix root cause | Content Arch. |
| RISK-TEC-04 | **CMS adoption failure** — CMS too complex, authors bypass it | 3 | 3 | 9 | **Medium** | Thorough CMS evaluation; phased rollout; dedicated CMS admin support | Fallback to Git-based workflow | IT Lead |
| RISK-TEC-05 | **Security breach** — unauthorized access to documentation repository | 1 | 5 | 5 | **Medium** | 2FA enforcement; branch protection; access audit quarterly; least-privilege principle | Revoke access; security audit; notification to stakeholders | IT Lead |

---

## 7. Risk Register: Organizational Risks

Risks related to people, structure, and culture.

| ID | Risk | P | I | Score | Level | Prevention | Contingency | Owner |
|----|------|---|---|-------|-------|------------|-------------|-------|
| RISK-ORG-01 | **Reorganization / leadership change** — new executive doesn't support DITA | 2 | 4 | 8 | **Medium** | Document ROI; build broad support beyond one sponsor; show measurable results | Re-pitch the business case; adjust to new strategy | CoE Lead |
| RISK-ORG-02 | **Team turnover** — trained DITA writers leave the company | 3 | 3 | 9 | **Medium** | Cross-training; documented processes; competitive compensation | Accelerate next training cohort; contractor bridge | HR + CoE |
| RISK-ORG-03 | **Governance committee atrophy** — DGC stops meeting regularly | 2 | 3 | 6 | **Medium** | Standing monthly calendar invite; clear agenda + action items; rotate facilitator | Escalate to executive sponsor; reduce meeting frequency if needed | DGC Chair |
| RISK-ORG-04 | **Siloed content ownership** — teams don't share topics, defeating reuse | 3 | 3 | 9 | **Medium** | Clear content ownership matrix; shared topic library; cross-team reuse targets | DGC intervention; restructure ownership | CoE Lead |

---

## 8. Control Points by Phase

Specific control points where risks are checked and mitigated during the implementation lifecycle.

### Phase 0: Readiness Assessment

| Control Point | Check | Frequency | Owner |
|--------------|-------|-----------|-------|
| CP-0.1 | Content audit completeness check | Once (W2) | CoE Lead |
| CP-0.2 | Stakeholder buy-in confirmation | Once (W2) | CoE Lead |
| CP-0.3 | Team availability validated | Once (W2) | PM |
| CP-0.4 | Pilot scope reviewed and locked | Once (W2) | CoE Lead + PM |

### Phase 1: Foundation Setup

| Control Point | Check | Frequency | Owner |
|--------------|-------|-----------|-------|
| CP-1.1 | Toolchain smoke test — can one author publish a topic from start to finish? | Once (W4) | IT Lead |
| CP-1.2 | CI/CD pipeline green build | Once (W5) | IT Lead |
| CP-1.3 | Template review — all 12 templates accepted by writing team | Once (W5) | Content Arch. |
| CP-1.4 | Governance charter signed by all DGC members | Once (W4) | CoE Lead |
| CP-1.5 | Core team practical exam — each member creates and publishes 1 topic independently | Once (W6) | Training Lead |

### Phase 2: Pilot

| Control Point | Check | Frequency | Owner |
|--------------|-------|-----------|-------|
| CP-2.1 | Weekly pilot health check — RAG status, topics created, blockers | Weekly | CoE Lead |
| CP-2.2 | Peer review participation rate — every author has reviewed at least 3 others' topics | Weekly | Writing Lead |
| CP-2.3 | Build success rate — rolling 7-day average | Daily (auto) | IT Lead |
| CP-2.4 | SME review turnaround — average time from submission to sign-off | Weekly | PM |
| CP-2.5 | Reuse rate tracking — what percentage of topics contain conref or keyref? | Weekly | Content Arch. |
| CP-2.6 | Team sentiment check — anonymous pulse survey | Bi-weekly | CoE Lead |

### Phase 3: Full Rollout

| Control Point | Check | Frequency | Owner |
|--------------|-------|-----------|-------|
| CP-3.1 | Translation pipeline throughput — words translated per week | Weekly | L10n Mgr |
| CP-3.2 | Cohort 2 training completion — remaining writers trained and assessed | Once (W14) | Training Lead |
| CP-3.3 | Wave migration burn-down — topics remaining per product line | Weekly | PM |
| CP-3.4 | Build stability index — 95%+ success rate over 2 weeks | Weekly | IT Lead |
| CP-3.5 | Quality audit score — random sample of published topics scored against checklist | Bi-weekly | Content Arch. |

### Phase 4: Operations

| Control Point | Check | Frequency | Owner |
|--------------|-------|-----------|-------|
| CP-4.1 | KPI dashboard review — all tracked metrics | Monthly | CoE Lead |
| CP-4.2 | Content freshness audit — % of topics reviewed in last 6 months | Quarterly | Content Arch. |
| CP-4.3 | DGC governance review — committee meeting held, decisions recorded | Monthly | DGC Chair |
| CP-4.4 | User feedback analysis — support tickets, survey results | Quarterly | CoE Lead |
| CP-4.5 | Training needs assessment — skills gaps identified | Quarterly | Training Lead |
| CP-4.6 | Annual maturity assessment | Yearly | CoE Lead + DGC |

---

## 9. Emergency Response Procedures

### 9.1 Severity Levels

| Level | Criteria | Response Time | Example |
|-------|----------|--------------|---------|
| **SEV-1** | Published output contains safety-critical error | 1 hour | Incorrect safety procedure, missing hazard warning |
| **SEV-2** | Build pipeline completely down, no output possible | 4 hours | CI/CD failure, DITA-OT crash |
| **SEV-3** | Quality degradation, partial output, or delay | 24 hours | Broken conref, wrong DITAVAL profile |
| **SEV-4** | Minor issue, workaround available | 1 week | Cosmetic formatting issue, outdated metadata |

### 9.2 SEV-1 Response Procedure (Safety-Critical)

```
1. DETECT — automated check / user report / auditor identifies safety error
   ↓
2. STOP — immediately halt publishing pipeline (all languages)
   ↓
3. NOTIFY — email + IM to: CoE Lead, Content Architect, DGC Chair, Legal
   ↓
4. ASSESS — Content Architect + SME determine scope (which topics, which products)
   ↓
5. FIX — author corrects the source topic; SME + Content Arch. approve
   ↓
6. REPUBLISH — expedited build; bypass normal queue
   ↓
7. NOTIFY — confirm fix to all affected stakeholders; recall old copies if distributed
   ↓
8. ROOT CAUSE — post-mortem within 5 business days; update controls
```

### 9.3 SEV-2 Response Procedure (Pipeline Down)

```
1. DETECT — CI/CD failure alert received
   ↓
2. TRIAGE — IT Lead determines cause (DITA-OT error / CI/CD system outage / plugin issue)
   ↓
3. RESTORE — try last-known-good build; rollback if DITA-OT upgrade caused failure
   ↓
4. NOTIFY — inform CoE Lead and writing team of downtime ETA
   ↓
5. MANUAL FALLBACK — if automated build down > 4 hours: use local oXygen publish
   ↓
6. RESOLVE — root cause fix deployed; pipeline green
   ↓
7. DOCUMENT — incident report added to Knowledge Base
```

### 9.4 Emergency Contact Tree

```
SEV-1 Safety Issue:
  Author → Content Arch. (within 1h) → SME → DGC Chair → Legal → Executive Sponsor
  Escalation: 1h → 2h → 4h → 8h

SEV-2 Pipeline Down:
  IT On-Call → IT Lead (within 2h) → CoE Lead → (if > 8h) Executive Sponsor
  Escalation: 2h → 4h → 8h → 24h

SEV-3 Quality Issue:
  Author → Content Arch. (within 24h) → Fix in next build cycle
  Escalation: 24h → 48h → next DGC meeting

SEV-4 Minor Issue:
  Log in issue tracker → Fix in next scheduled release
```

---

## 10. Monitoring & Review

### 10.1 Risk Review Cadence

| Type | Frequency | Participants | Agenda |
|------|-----------|-------------|--------|
| Operational risk review | Weekly | CoE Lead, IT Lead, Content Arch. | RAG status, new risks, control effectiveness |
| DGC risk review | Monthly | Full DGC | High/extreme risks, mitigation progress |
| Quarterly risk deep-dive | Quarterly | CoE Lead + DGC | Re-score all risks, close/retire resolved risks |
| Annual risk refresh | Yearly | CoE Lead + all owners | Full reassessment, new risk identification |

### 10.2 Risk Dashboard Metrics

| Metric | Target | Source |
|--------|--------|--------|
| Open high/extreme risks | ≤ 3 | Risk register |
| Risk resolution time | ≤ 30 days for high, ≤ 14 days for extreme | Incident tracker |
| Control effectiveness score | ≥ 80% | Quarterly audit |
| SEV-1 incidents / quarter | 0 | Incident log |
| Risk register completeness | 100% (all categories populated) | Quarterly review |

### 10.3 Risk Closure Criteria

A risk may be closed when:
- The risk has materialized and been fully resolved, OR
- The probability has dropped to "Rare" with controls in place, OR
- The associated system/process has been decommissioned
- Closure must be approved by the DGC

### 10.4 Escalation Path

```
Risk Owner → CoE Lead → DGC Chair → Executive Sponsor
(within escalation times defined in section 9.4)
```

---

## Appendix A: Risk Register Template

```markdown
| ID | RISK-XXX-001 |
|----|-------------|
| **Risk** | [Brief description] |
| **Category** | [IMP / OPS / CON / TEC / ORG] |
| **Probability** | 1-5 |
| **Impact** | 1-5 |
| **Score** | [P × I] |
| **Level** | [Low / Medium / High / Extreme] |
| **Prevention** | [Proactive measures] |
| **Contingency** | [Reactive / fallback plan] |
| **Owner** | [Role] |
| **Status** | [Open / Mitigated / Closed] |
| **Review Date** | [YYYY-MM-DD] |
```

## Appendix B: Incident Report Template

```markdown
## Incident Report

**Date:** [YYYY-MM-DD]
**Severity:** [SEV-1 / SEV-2 / SEV-3 / SEV-4]
**Risk ID(s):** [Related risk IDs]

### What happened
[Description of the incident]

### Impact
[Which topics/products/languages affected]

### Root cause
[What caused it]

### Resolution
[What was done to fix it]

### Timeline
- Detection: [timestamp]
- Notification: [timestamp]
- Mitigation: [timestamp]
- Resolution: [timestamp]

### Preventive measures
[What controls will prevent recurrence]

### Lessons learned
[What the team learned]
```
