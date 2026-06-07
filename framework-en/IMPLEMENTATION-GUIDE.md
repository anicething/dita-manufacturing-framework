# DITA Manufacturing Framework — Implementation Guide

**Document ID:** IMP-001
**Version:** v1.0 | **Last Updated:** 2026-06-07

---

## Table of Contents

1. [Overview](#1-overview)
2. [Phase 0: Readiness Assessment (Weeks 1-2)](#2-phase-0-readiness-assessment-weeks-1-2)
3. [Phase 1: Foundation Setup (Weeks 3-6)](#3-phase-1-foundation-setup-weeks-3-6)
4. [Phase 2: Pilot Project (Weeks 7-12)](#4-phase-2-pilot-project-weeks-7-12)
5. [Phase 3: Full Rollout (Weeks 13-24)](#5-phase-3-full-rollout-weeks-13-24)
6. [Phase 4: Operations & Continuous Improvement (Week 25+)](#6-phase-4-operations--continuous-improvement-week-25)
7. [Staffing & Roles](#7-staffing--roles)
8. [Risk Register](#8-risk-register)
9. [Success Criteria](#9-success-criteria)
10. [Appendices](#10-appendices)

---

## 1. Overview

### 1.1 Purpose

This document provides a **step-by-step implementation roadmap** for adopting the DITA Manufacturing Framework. It is designed for manufacturing enterprises of 50+ team members transitioning from ad-hoc documentation practices to a structured DITA-based information environment.

### 1.2 Implementation Phases at a Glance

| Phase | Name | Duration | Key Deliverable |
|-------|------|----------|----------------|
| 0 | Readiness Assessment | 2 weeks | Assessment report, gap analysis |
| 1 | Foundation Setup | 4 weeks | Toolchain, templates, governance |
| 2 | Pilot Project | 6 weeks | One product line in DITA |
| 3 | Full Rollout | 12 weeks | All product lines migrated |
| 4 | Operations | Ongoing | Maturity improvement |

**Total estimated timeline:** 24 weeks (6 months) to full production.

### 1.3 Prerequisites

Before starting, ensure:

- Executive sponsorship secured (budget, headcount, timeline commitment)
- A DITA Center of Excellence (CoE) core team identified
- At least one existing product line selected for pilot
- Basic understanding of XML and structured authoring within the team

---

## 2. Phase 0: Readiness Assessment (Weeks 1-2)

### 2.1 Goals

- Assess current documentation state
- Identify gaps between current practice and framework requirements
- Secure buy-in from stakeholders
- Build the implementation roadmap

### 2.2 Activities

| Activity | Owner | Duration | Output |
|----------|-------|----------|--------|
| 0.1 Current state audit | CoE Lead | 3 days | Documentation inventory |
| 0.2 Stakeholder interviews | CoE Lead | 3 days | Needs & pain points document |
| 0.3 Toolchain gap analysis | IT Lead | 2 days | Tool recommendation report |
| 0.4 Skills assessment | Training Lead | 2 days | Training needs matrix |
| 0.5 Pilot selection | CoE Lead + PM | 1 day | Pilot scope document |
| 0.6 Implementation plan | CoE Lead | 2 days | This roadmap, finalized |

### 2.3 Current State Audit Checklist

For each product line, assess:

- [ ] Total number of existing documents
- [ ] Document formats (Word, PDF, FrameMaker, HTML, other)
- [ ] Number of languages supported
- [ ] Existing template usage (if any)
- [ ] Translation workflow (manual/TMS/hybrid)
- [ ] Storage and version control (shared drive/CMS/Git)
- [ ] Review and approval process
- [ ] Document reuse rate (estimated)
- [ ] Update frequency per document
- [ ] Team size and roles

### 2.4 Phase 0 Gate: Approval to Proceed

**Criteria:**
- Executive sponsor has approved budget and timeline
- Pilot product line has been confirmed with the product owner
- Core team members have been assigned
- Tooling budget has been allocated

---

## 3. Phase 1: Foundation Setup (Weeks 3-6)

### 3.1 Goals

- Install and configure the DITA toolchain
- Customize framework templates for the organization
- Establish governance committee
- Train the core team
- Set up the CI/CD pipeline

### 3.2 Activities

| Activity | Owner | Duration | Week | Output |
|----------|-------|----------|------|--------|
| 1.1 Tool installation | IT Lead | 1 week | W3 | Configured oXygen/DITA-OT |
| 1.2 Repository setup | IT Lead | 2 days | W3 | Git repo with branching model |
| 1.3 Template customization | Content Arch. | 2 weeks | W3-4 | Branded templates in repo |
| 1.4 Governance kickoff | CoE Lead | 1 day | W4 | DGC charter signed |
| 1.5 CI/CD pipeline | IT Lead | 1 week | W5 | CI/CD system for build |
| 1.6 Core team training | Training Lead | 1 week | W5 | Trained 5-8 core members |
| 1.7 Style guide adaptation | Content Arch. | 1 week | W6 | Manufacturing style guide v1 |
| 1.8 Variable setup | Content Arch. | 2 days | W6 | framework-keys.ditamap |

### 3.3 Toolchain Configuration

#### Required Tools

| Tool | Purpose | Recommendation |
|------|---------|---------------|
| XML Editor | DITA authoring | oXygen XML Editor (enterprise) |
| DITA-OT | DITA processing/publishing | DITA-OT 4.x |
| Version Control | Source management | Git-based (on-premises or cloud) |
| CI/CD | Automated build & test | CI/CD platform (see CI-CD module) |
| Review Platform | Collaborative review | oXygen Content Fusion or equivalent |
| CMS (optional) | Content management | Recommended for 50+ authors |

#### Repository Structure

```
docs-repository/
├── ci-cd/                      ← CI/CD pipeline definitions
├── framework/                 ← Governance & architecture (from this framework)
├── references/                ← Centralized variables & legal boilerplate
├── templates/                 ← Customized DITA templates
├── products/
│   ├── mc5000/                ← Each product line
│   │   ├── map/               ← Ditamaps & bookmaps
│   │   ├── concepts/          ← Concept topics
│   │   ├── tasks/             ← Task topics
│   │   ├── reference/         ← Reference topics
│   │   ├── troubleshooting/   ← Troubleshooting topics
│   │   └── images/            ← Graphics & media
│   ├── mc4000/
│   └── mc3000/
├── shared/
│   ├── concepts/              ← Reusable concept topics
│   └── tasks/                 ← Reusable task topics (conref targets)
├── translations/              ← Localized content
│   ├── zh-CN/
│   ├── de/
│   └── ...
└── output/                    ← Build output (gitignored)
```

### 3.4 Template Customization

Taking the 12 framework templates, customize:

- [ ] Company branding (logo, colors, fonts)
- [ ] Metadata schema aligned to M1-M7 rules
- [ ] Keyref placeholders replaced with framework keys
- [ ] Conref hooks to legal boilerplate
- [ ] Example content replaced with real product samples
- [ ] DITAVAL conditions for your product variants

### 3.5 Governance Committee Kickoff

**DITA Governance Committee (DGC) — First Meeting Agenda:**

1. Review and sign the Governance Charter
2. Approve content ownership matrix
3. Establish change authority levels
4. Agree on review SLAs
5. Schedule recurring meetings (monthly)
6. Define escalation path

### 3.6 Phase 1 Gate: Toolchain Ready

**Criteria:**
- At least one author can create a topic from template and generate HTML5/PDF
- CI/CD pipeline successfully builds a sample project
- Governance committee has held first meeting
- Core team has completed hands-on training
- Repository structure is established and accessible

---

## 4. Phase 2: Pilot Project (Weeks 7-12)

### 4.1 Goals

- Migrate one product line to DITA
- Validate the framework with real content
- Train writers through actual production
- Establish and calibrate metrics

### 4.2 Activities

| Activity | Owner | Duration | Week | Output |
|----------|-------|----------|------|--------|
| 2.1 Pilot kickoff | CoE Lead | 1 day | W7 | Pilot team, scope confirmed |
| 2.2 Content migration | Team + SME | 3 weeks | W7-9 | ~50 topics in DITA |
| 2.3 Review cycle test | Team | 1 week | W10 | PR workflow validated |
| 2.4 First publication | IT Lead | 1 week | W11 | HTML5 + PDF output |
| 2.5 Pilot retrospective | CoE Lead | 2 days | W12 | Lessons learned report |

### 4.3 Content Migration Strategy

Use a **triage approach** for migrating existing content:

| Priority | Content Type | Action | Rationale |
|----------|-------------|--------|-----------|
| P1 | Safety documentation | Create new in DITA | Compliance-critical, often needs rewrite |
| P2 | Most-frequently updated docs | Migrate to DITA | High ROI for reuse |
| P3 | Installation & operation manuals | Migrate to DITA | Core product docs |
| P4 | Reference specs | Create new in DITA | Easy to re-enter |
| P5 | Legacy content, rarely updated | Archive in original format | Low ROI for migration |

### 4.4 Pilot Team Composition

| Role | FTE | Source |
|------|-----|--------|
| Content Architect / Lead | 1 | Core CoE |
| Technical Writers | 2-3 | Documentation team |
| Subject Matter Expert | 0.5 | Product engineering |
| IT Support | 0.25 | IT department |
| Reviewer | 0.25 | Cross-team |

### 4.5 Metrics to Track During Pilot

- Topics created per week
- Reuse rate (conref + keyref count / total topics)
- Build success rate
- Review cycle time (average PR open to merge)
- Team confidence survey score
- Translation-ready content volume (word count)

### 4.6 Common Pilot Pitfalls & Mitigations

| Pitfall | Sign | Mitigation |
|---------|------|------------|
| Scope creep | "Let's also migrate X product" | Scope-lock the pilot at kickoff |
| Template iteration | "This element should be different" | Log improvements, implement in Phase 3 |
| SME availability | Reviews blocked | Escalate through DGC; dedicate SME time |
| Tool issues | Authors revert to Word | Pair IT with writers; daily standups |
| Perfectionism | "It's not DITA-compliant enough" | 80% guideline: publish first, polish later |

### 4.7 Phase 2 Gate: Pilot Validated

**Criteria:**
- Pilot topics published and approved by stakeholders
- Team can independently author and publish DITA topics
- Build pipeline is stable (90%+ success rate)
- Template issues documented and prioritized
- Pilot retrospective completed with actionable items

---

## 5. Phase 3: Full Rollout (Weeks 13-24)

### 5.1 Goals

- Scale to all product lines
- Train remaining writers
- Establish translation workflow
- Implement quality metrics

### 5.2 Rollout Sequence

| Wave | Weeks | Product Lines | Authors | Topics (est.) |
|------|-------|---------------|---------|---------------|
| Wave 1 | 13-16 | MC-5000 + MC-4000 | 5-8 | 150 |
| Wave 2 | 17-20 | MC-3000 + Accessories | 8-12 | 300 |
| Wave 3 | 21-24 | All products + translations | 12-15 | 500+ |

### 5.3 Activities

| Activity | Owner | Duration | Week |
|----------|-------|----------|------|
| 3.1 Writer training (cohort 2) | Training Lead | 1 week | W13 |
| 3.2 Wave 1 migration | Writer team | 3 weeks | W13-15 |
| 3.3 Translation workflow setup | IT + L10n | 2 weeks | W14-15 |
| 3.4 Wave 2 migration | Writer team | 3 weeks | W17-19 |
| 3.5 Template v2 release | Content Arch. | 1 week | W19 |
| 3.6 Wave 3 migration | Full team | 4 weeks | W21-24 |
| 3.7 Quality framework | Content Arch. | 2 weeks | W23-24 |

### 5.4 Scaling Training

| Training Cohort | When | Who | Format |
|----------------|------|-----|--------|
| Cohort 1 (Core) | Phase 1 | CoE + pilot writers | In-person, 1 week |
| Cohort 2 | Phase 3 Wave 1 | Remaining writers | Blended, 1 week |
| Cohort 3 | Phase 3 Wave 2 | New hires + SMEs | Self-paced, ongoing |
| Ongoing | From Phase 4 | All | Monthly workshops |

### 5.5 Translation Workflow

```
Source topic (EN) → 
  Export XLIFF →
    Translation (TMS) →
      Import XLIFF →
        Target topics (zh-CN, de, fr, ...) →
          Validation →
            Publish
```

**Key checklist for translation readiness:**
- [ ] XLIFF export configured in DITA-OT / oXygen
- [ ] TMS (Translation Management System) connected
- [ ] Translation memory repository established
- [ ] Terminology database created (from glossary topics)
- [ ] Language-specific DITAVAL conditions defined
- [ ] Translation review workflow defined
- [ ] Key-based variables flagged as DO NOT TRANSLATE

### 5.6 Phase 3 Gate: Full Production

**Criteria:**
- All product lines have DITA documentation
- 80%+ of writers are active DITA authors
- Translation pipeline operational
- Quality metrics are being collected
- Build pipeline is fully automated
- Documentation published on schedule

---

## 6. Phase 4: Operations & Continuous Improvement (Week 25+)

### 6.1 Goals

- Maintain and improve the DITA system
- Track and optimize KPIs
- Evolve the framework
- Mentor new team members

### 6.2 Operational Cadence

| Frequency | Activity | Owner |
|-----------|----------|-------|
| Daily | Automated build + publish | CI/CD (automatic) |
| Weekly | Content quality spot checks | Content Architect |
| Bi-weekly | Writer office hours / clinic | Training Lead |
| Monthly | DGC meeting | CoE Lead |
| Monthly | KPI dashboard review | Content Architect |
| Quarterly | Framework release | CoE Lead |
| Quarterly | Training needs assessment | Training Lead |
| Annual | Full maturity assessment | CoE Lead + DGC |

### 6.3 KPI Dashboard

Track these metrics monthly:

| KPI | Target | Measurement |
|-----|--------|-------------|
| Reuse rate | ≥ 35% | conref + keyref topics / total topics |
| Build success rate | ≥ 95% | Successful builds / total builds |
| Publish on-time rate | ≥ 90% | On-time publications / total publications |
| Review cycle time | ≤ 3 days (minor), ≤ 10 days (major) | PR open to merge |
| Translation turnaround | ≤ 5 days per 1,000 words | TMS data |
| Writer proficiency | Cohort-specific targets | Internal assessment |
| Topic quality score | ≥ 80% | Schematron + manual audit |
| User satisfaction | ≥ 4.0 / 5.0 | Annual survey |

### 6.4 Framework Version Management

| Version | Frequency | Content | Process |
|---------|-----------|---------|---------|
| v1.x (Patches) | Monthly | Bug fixes, minor template updates | DGC approval, 1-week review |
| v2.x (Minor) | Quarterly | New templates, toolchain updates | DGC + pilot, 1-month review |
| v3.x (Major) | Annual | Breaking changes, DITA-OT upgrade | Full assessment, 3-month review |

### 6.5 Maturity Model

Use this model to assess progress annually:

| Level | Name | Characteristics |
|-------|------|----------------|
| 1 | Ad-hoc | No DITA, manual publishing, no reuse |
| 2 | Defined | DITA adopted, templates in use, basic reuse |
| 3 | Managed | Metrics tracked, CI/CD, translation workflow |
| 4 | Measured | KPIs optimized, automated quality gates |
| 5 | Optimized | AI-assisted authoring, continuous innovation |

**Target for Year 1:** Level 2
**Target for Year 2:** Level 3
**Target for Year 3:** Level 4

---

## 7. Staffing & Roles

### 7.1 Recommended Team Structure (50+ person org)

| Role | Phase 1 | Phase 2 | Phase 3 | Phase 4 | Description |
|------|---------|---------|---------|---------|-------------|
| CoE Lead | 1 FTE | 1 FTE | 1 FTE | 1 FTE | Drives DITA adoption |
| Content Architect | 1 FTE | 1 FTE | 1 FTE | 0.5 FTE | Designs info architecture |
| IT / DevOps | 0.5 FTE | 0.5 FTE | 0.5 FTE | 0.25 FTE | Tooling & pipeline |
| Training Lead | 0.5 FTE | 0.5 FTE | 0.5 FTE | 0.25 FTE | Training program |
| Technical Writers | 0 | 2-3 | 5-8 | 8-15 | Content authors |
| SMEs (rotating) | 0 | 0.5 | 1-2 | 1-2 per team | Product expertise |
| Localization Mgr | 0 | 0 | 0.5 | 0.5 | Translation management |
| DGC Members | 5-7 | 5-7 | 7-9 | 7-9 | Governance committee |

### 7.2 RACI Matrix

| Activity | CoE Lead | Content Arch. | Writer | IT | SME | DGC |
|----------|----------|---------------|--------|-----|-----|-----|
| Framework adoption | **R** | C | I | C | I | A |
| Template creation | C | **R** | C | I | I | A |
| Content authoring | I | C | **R** | I | C | I |
| Technical review | I | I | C | I | **R** | I |
| Editorial review | I | **R** | C | I | I | A |
| Tool configuration | C | I | I | **R** | I | I |
| Training delivery | C | I | I | I | I | A |
| KPI tracking | **R** | C | I | C | I | A |
| Publishing | I | I | C | **R** | I | A |

> **R** = Responsible, **A** = Accountable, **C** = Consulted, **I** = Informed

---

## 8. Risk Register

| Risk | Probability | Impact | Mitigation | Contingency |
|------|------------|--------|------------|-------------|
| Low adoption by writers | Medium | High | Strong training, continuous support, peer champions | Performance management |
| Toolchain issues | Medium | High | Thorough testing in Phase 1; IT on-call | Fallback to oXygen standalone |
| SME availability | High | Medium | Scheduled SME time, batch reviews | SME peer review |
| Scope creep | Medium | Medium | Strict phase gates, change control board | Defer to next wave |
| Translation delays | Medium | Medium | Parallelize translation with authoring | Priority languages only |
| Governance inertia | Low | Medium | Monthly DGC with clear agenda | Executive escalation |
| Budget cuts | Low | High | Phase-gated funding | Reduce scope, not quality |

---

## 9. Success Criteria

### 9.1 6-Month Success (End of Phase 3)

- [ ] All product lines documented in DITA
- [ ] 90%+ of authors actively using DITA
- [ ] CI/CD pipeline is operational and stable
- [ ] Published output quality meets or exceeds pre-DITA quality
- [ ] Translation workflow is operational
- [ ] At least one measurable efficiency gain (e.g., 20% reduction in review time)

### 9.2 12-Month Success

- [ ] Reuse rate ≥ 35%
- [ ] All 9 languages in production
- [ ] Content quality score ≥ 80%
- [ ] Writer satisfaction score ≥ 4.0 / 5.0
- [ ] Publication cycle time reduced by 50% vs. pre-DITA
- [ ] Framework at maturity Level 2+

### 9.3 KPIs at a Glance

| KPI | Baseline (Pre-DITA) | Phase 2 Target | Phase 3 Target | Phase 4 Target |
|-----|-------------------|----------------|----------------|----------------|
| Authoring time / topic | 4 hours | 3 hours | 2.5 hours | 2 hours |
| Review cycle time | 10 days | 7 days | 5 days | 3 days |
| Build + publish time | 2 days (manual) | 1 hour | 30 min | 15 min |
| Content reuse rate | ~5% | 15% | 25% | 35%+ |
| Translation cost / word | $0.12 | $0.10 | $0.08 | $0.06 |

---

## 10. Appendices

### A. Glossary

| Term | Definition |
|------|------------|
| CoE | Center of Excellence |
| DGC | DITA Governance Committee |
| DITA-OT | DITA Open Toolkit |
| SME | Subject Matter Expert |
| TMS | Translation Management System |
| XLIFF | XML Localization Interchange File Format |

### B. Reference Documents

| Document | Location |
|----------|----------|
| Governance Charter | `GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md` |
| Content Architecture | `ARCHITECTURE/01-CONTENT-ARCHITECTURE.md` |
| Metadata Schema | `ARCHITECTURE/02-METADATA-SCHEMA.md` |
| Topic Templates Guide | `ARCHITECTURE/03-TOPIC-TEMPLATES.md` |
| Variable Management | `ARCHITECTURE/06-VARIABLE-MANAGEMENT.md` |
| Style Guide | `STYLE-GUIDE/01-STYLE-GUIDE.md` |
| Tool Setup Guide | `TOOLS/01-TOOL-SETUP.md` |
| CI/CD Pipeline | `CI-CD/01-CICD-PIPELINE.md` |

### C. Phase Gate Checklist Template

```
Phase Gate: [Name]
Date: [YYYY-MM-DD]
Status: [Pass / Conditional Pass / Fail]

Criteria:
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

Reviewers:
- Name (Role) — Decision: [Approve / Conditional / Reject]
- Name (Role) — Decision: [Approve / Conditional / Reject]

Decisions / Conditions:
1. [Condition if applicable]

Next Phase Start: [Date]
```

### D. Weekly Status Report Template

```
Project: DITA Framework Implementation
Week: [WXX]
Date: [YYYY-MM-DD]

Accomplished This Week:
- 

Planned Next Week:
- 

Blockers:
- 

Metrics:
  Topics created: 
  Build success rate: 
  Open action items: 

RAG Status: [Green / Yellow / Red]
```
