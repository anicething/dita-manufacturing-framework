---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Team Management for DITA
## Building and Leading a DITA Center of Excellence

**DITA Expert Level - Module 4**
Example Manufacturing Co., Ltd.
v1.1 | 2026-06-07

---

<!-- _class: divider -->

# Agenda

1. The DITA Center of Excellence (CoE)
2. Team Roles and RACI Matrix
3. Content Ownership Models
4. Budgeting for DITA Tools
5. Onboarding New Team Members
6. Career Paths for DITA Specialists
7. Stakeholder Management
8. Collaboration Workflows
9. Exercise
10. Summary

---

<!-- _class: keypoint -->

# Learning Objectives

By the end of this module, you will be able to:

1. **Design** a DITA Center of Excellence organizational structure for a manufacturing enterprise
2. **Assign** roles and responsibilities using a RACI matrix for DITA content operations
3. **Build** a budget for DITA tools, training, and ongoing operations
4. **Create** an onboarding plan for new technical writers joining a DITA team
5. **Manage** stakeholders across engineering, manufacturing, and compliance departments

---

# Why a Center of Excellence?

> The CoE is the organizational vehicle that transforms DITA from a publishing tool into a content strategy.

**Without a CoE:**
- 12 writers following 12 different approaches
- Specialization drift across product lines
- No shared conref library; duplicated content proliferates
- DITA-OT configuration conflicts between teams
- Compliance rules enforced inconsistently

**With a CoE:**
- Single source of truth for framework, styles, and rules
- Shared conref library maintained centrally
- Consistent DITA-OT configuration across all products
- Governance enforced through peer review and automated checks

---

# CoE Organizational Model

```
┌─────────────────────────────────────────────────────────────┐
│                  DITA GOVERNANCE COUNCIL                     │
│          (VP Engineering, Doc Manager, ISO Rep)              │
└──────────────────────────┬──────────────────────────────────┘
                           │
┌──────────────────────────┴──────────────────────────────────┐
│              ARCHITECTURE & STANDARDS TEAM                    │
│          (DITA Architect, Senior Information Architect)       │
└──────────────────────────┬──────────────────────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
┌───────┴───────┐  ┌───────┴───────┐  ┌───────┴───────┐
│  DR-2000 Team  │  │  MC-5000 Team  │  │  MC-8000 Team  │
│  (4 writers)   │  │  (5 writers)   │  │  (3 writers)   │
└───────────────┘  └───────────────┘  └───────────────┘
```

---

# CoE Charter

Every CoE needs a written charter defining scope and authority:

```yaml
# coe-charter.yaml — ExampleCorp DITA Center of Excellence

mission: >
  To establish and maintain a unified DITA-based documentation
  framework that ensures safety, consistency, and efficiency
  across all ExampleCorp product lines.

scope:
  - DTD/XSD specialization development and governance
  - DITA-OT plugin development and maintenance
  - CI/CD pipeline definition and maintenance
  - Shared conref and keydef library curation
  - Author training, onboarding, and certification
  - Content quality metrics and reporting
  - Tool evaluation and procurement

authority:
  - Can block publication of non-compliant content
  - Reviews and approves all specialization requests
  - Sets DITA authoring standards (binding on all writers)
  - Approves DITA tool purchases > $5,000
```

---

# CoE Operating Model

| Function | Cadence | Participants | Output |
|----------|---------|-------------|--------|
| **Architecture Review** | Bi-weekly | DITA Architect, Senior Writers | Approved framework changes |
| **Content Quality Review** | Weekly | All writers | Metrics snapshot, action items |
| **Tool Evaluation** | Quarterly | CoE Lead, IT, Writers | Tool roadmap |
| **Governance Council** | Monthly | VP Eng, Doc Manager, ISO Rep | Strategic decisions |
| **All-Hands Retro** | Monthly | Entire CoE | Process improvements |

```
Hours/week per person for CoE activities:
- DITA Architect: 12h (60% of role)
- Senior Writers: 4h (20% of role)
- Writers: 2h (10% of role)
```

---

# Team Roles: Overview

| Role | Count | Reports To | Key Responsibility |
|------|-------|-----------|-------------------|
| **DITA Architect** | 1 | Doc Manager | Framework design, specialization governance |
| **Information Architect** | 1 | DITA Architect | Content model, taxonomy, navigation design |
| **Senior Technical Writer** | 3 | Doc Manager | Product-line lead (one per product) |
| **Technical Writer** | 9 | Senior Writer | Topic authoring, peer review |
| **Tools & Pipeline Engineer** | 1 | DITA Architect | CI/CD, DITA-OT plugins, scripting |
| **Translation Coordinator** | 1 | Doc Manager | L10n vendor management, TM maintenance |
| **Quality & Compliance Lead** | 1 | ISO Representative | Content audits, regulatory alignment |

---

# Role 1: DITA Architect

**The most critical hire for DITA success.**

```yaml
role: DITA Architect
responsibilities:
  - Design and govern all DITA specializations (DTD, XSD, RNG)
  - Define content models for new document types
  - Approve or reject all specialization requests from product teams
  - Audit @class attribute integrity across the corpus
  - Own the DTD shell architecture and catalog resolution
  - Define metadata standards (M1-M7 rules)
  - Lead the Architecture & Standards Team
  - Mentor senior writers on specialization design

required_skills:
  - Expert-level DITA DTD/XSD specialization
  - XML Schema languages (DTD, XSD, Relax NG)
  - XSLT 2.0/3.0 for processing pipeline customization
  - Familiarity with manufacturing terminology and ISO standards
  - Experience with DITA-OT plugin development

typical_background:
  - Senior technical writer with 5+ years DITA experience
  - XML/XSLT developer moving into content architecture
```

---

# Role 2: Tools & Pipeline Engineer

```yaml
role: Tools and Pipeline Engineer
responsibilities:
  - Develop and maintain CI/CD workflows (publish.yml, validate.yml)
  - Build DITA-OT plugins for HTML5, PDF, and custom output formats
  - Write and maintain custom validation scripts (Python)
  - Manage AWS S3/Azure deployment configurations
  - Set up Elasticsearch indexing pipeline
  - Administer GitHub repository: branch protection, CODEOWNERS
  - Manage oXygen XML plugin deployment to writer workstations
  - Automate metrics collection and dashboarding

required_skills:
  - Python, Bash, and JavaScript
  - GitHub Actions or equivalent CI/CD platform
  - DITA-OT internals and Ant build system
  - XSLT and XSL-FO for PDF customization
  - Cloud deployment (AWS S3, CloudFront, Azure)
  - Elasticsearch indexing

typical_background:
  - DevOps engineer with documentation tooling experience
  - Build engineer transitioning to content pipelines
```

---

# Role 3: Senior Technical Writer (Product Lead)

```yaml
role: Senior Technical Writer — Product Lead
reports_to: Documentation Manager
dotted_line_to: DITA Architect

responsibilities:
  - Lead content development for one product line (DR-2000/5000/8000)
  - Review all team members' topics before PR merge
  - Ensure product-specific terminology consistency
  - Liaise with engineering team for source information
  - Mentor junior writers on DITA authoring best practices
  - Identify conref/keystore opportunities and propose to Architecture Team
  - Maintain product-line ditamap structure and navigation

success_metrics:
  - Product-line compliance score >= 90
  - PR review turnaround < 2 business days
  - Team throughput: >= 8 published topics per writer per week
  - Zero safety-critical documentation defects found in field

career_path:
  - Next: Information Architect or DITA Architect (2-3 years)
  - Requires: specialization design training, XSLT basics, CoE leadership
```

---

# RACI Matrix: Content Operations

| Activity | Architect | Info Arch | Sr Writer | Writer | Pipeline Eng | QA Lead |
|----------|-----------|-----------|-----------|--------|-------------|---------|
| **Specialization design** | A | R | C | I | C | C |
| **DTD shell maintenance** | R | C | I | I | A | I |
| **Topic authoring** | I | I | R | R | I | C |
| **Peer review** | I | I | A | R | I | C |
| **CI/CD pipeline** | C | I | I | I | R | I |
| **Tool procurement** | R | C | C | C | C | I |
| **Metrics reporting** | A | R | C | I | C | R |
| **ISO compliance audit** | C | C | C | I | I | R |
| **Writer onboarding** | C | R | A | I | I | I |

**R = Responsible** (does the work) | **A = Accountable** (approves) | **C = Consulted** | **I = Informed**

---

# RACI Matrix: Release Activities

| Activity | Architect | Sr Writer | Writer | Pipeline Eng | Doc Manager |
|----------|-----------|-----------|--------|-------------|-------------|
| **Content freeze** | I | R | C | I | A |
| **Final review** | R | R | I | I | A |
| **DTD/Shell validation** | R | C | I | I | I |
| **CI/CD publish** | I | I | I | R | A |
| **Post-publish smoke test** | C | R | R | I | A |
| **Release notes** | I | R | I | I | A |
| **Metrics snapshot** | A | I | I | R | I |
| **Stakeholder notification** | I | I | I | I | R |

---

# Content Ownership Models

Three models for organizing who owns what content:

| Model | Description | Best For |
|-------|-------------|----------|
| **Product-Aligned** | Writer owns all content for one product | Dedicated product teams |
| **Component-Aligned** | Writer owns one component type across all products | High specialization on subsystems |
| **Hybrid** | Product leads own product content; shared services team owns cross-product conref | ExampleCorp's model |

**ExampleCorp uses the Hybrid model:**

```
Product Teams (owns product-specific content):
  DR-2000 Team → owns src/dr-2000/
  MC-5000 Team → owns src/dr-5000/
  MC-8000 Team → owns src/dr-8000/

Shared Services (owns cross-product content):
  Architecture Team → owns framework/, src/shared/conref/
  Pipeline Engineer → owns .github/workflows/, scripts/
```

---

# Content Ownership: Boundary Rules

```yaml
ownership_rules:

  product_teams_own:
    - Product-specific topics (*.dita under src/<product>/)
    - Product ditamaps (src/<product>/maps/*.ditamap)
    - Product-specific images (src/<product>/images/)
    - Product DITAVAL files (src/<product>/filters/)

  shared_services_own:
    - DTD shells (framework/dtd/*.dtd)
    - Domain modules (framework/mod/*.mod)
    - Entity files (framework/ent/*.ent)
    - Shared conref library (src/shared/conref/)
    - Subject scheme maps (src/subject-scheme/)
    - CI/CD workflows (.github/workflows/)
    - Catalog files (catalog.xml)

  change_request_process:
    - Product writer can propose changes to shared conref via PR
    - Architecture Team must approve before merge (CODEOWNERS)
    - Architecture-initiated changes to DTD shells require
      2-week notice to product teams with migration guidance
```

---

# Enforcing Ownership with CODEOWNERS

```yaml
# .github/CODEOWNERS — content ownership enforcement

# Architecture Team owns framework
framework/            @dingrui/dita-architects

# Each product team owns their content
src/dr-2000/          @dingrui/dr2000-writers
src/dr-5000/          @dingrui/dr5000-writers
src/dr-8000/          @dingrui/dr8000-writers

# Shared conref requires architect approval
src/shared/            @dingrui/dita-architects

# CI/CD and scripts require pipeline engineer
.github/workflows/     @dingrui/tools-pipeline
scripts/               @dingrui/tools-pipeline

# Catalog, DTD shells — architects only
catalog.xml            @dingrui/dita-architects
framework/dtd/         @dingrui/dita-architects
framework/mod/         @dingrui/dita-architects
framework/ent/         @dingrui/dita-architects

# Documentation manager owns the charter and docs
GOVERNANCE.md          @dingrui/doc-manager
```

---

# Budgeting: Tooling Costs

Annual tooling budget for a 15-person DITA team:

| Tool | License Type | Users | Cost/Year (USD) |
|------|-------------|-------|-----------------|
| oXygen XML Editor | Named User | 15 | $10,500 (15 x $700) |
| oXygen XML WebHelp | Floating | 3 | $4,500 |
| GitHub Enterprise | Named User | 15 | $4,950 (15 x $330) |
| Antenna House XSL Formatter | Server | 1 | $5,000 |
| Acrolinx (terminology) | Named User | 15 | $18,000 |
| Slack Enterprise Grid | Named User | 15 | $2,250 |
| **Total Tooling** | | | **$45,200/yr** |

Additional one-time costs: DITA training ($15,000 for team bootcamp), oXygen onboarding ($3,000).

---

# Budgeting: Infrastructure and Services

| Service | Tier | Cost/Month | Cost/Year (USD) |
|---------|------|-----------|-----------------|
| AWS S3 (docs hosting) | 500 GB | $12 | $144 |
| AWS CloudFront (CDN) | 2 TB transfer | $170 | $2,040 |
| Elasticsearch (search) | t3.medium | $150 | $1,800 |
| Translation vendor (8 languages) | Per-word | $53,000 | $636,000 |
| GitHub Actions (CI/CD minutes) | 10,000 min/mo | $80 | $960 |
| InfluxDB Cloud (metrics) | Free tier | $0 | $0 |
| **Total Infrastructure** | | | **$640,944/yr** |

---

# Budgeting: Total Cost of Ownership (TCO)

| Category | Annual Cost | % of TCO |
|----------|------------|----------|
| **Personnel** (15 FTE) | $1,350,000 | 65.6% |
| **Translation Services** | $636,000 | 30.9% |
| **Tooling Licenses** | $45,200 | 2.2% |
| **Infrastructure** | $4,944 | 0.2% |
| **Training & Conferences** | $22,000 | 1.1% |
| **Total** | **$2,058,144** | **100%** |

**ROI calculation:**
- Cost avoidance from reuse (reduced translation): $180,000/yr
- Support ticket reduction (70% fewer): ~$120,000/yr (based on avg $500/ticket)
- Writer productivity gain (20% faster with DITA): ~$270,000/yr
- **Total annual ROI: ~$570,000 (27.7% of TCO)**

---

# Onboarding: The First 30 Days

```yaml
onboarding_plan:
  week_1:
    - "Access setup: oXygen, GitHub, Slack, Confluence"
    - "DITA fundamentals self-study (LearningDITA.com)"
    - "Shadow a senior writer for one day"
    - "Read: ExampleCorp DITA Authoring Guide"
    - "Set up local DITA-OT environment"

  week_2:
    - "Complete first simple task topic (with mentor guidance)"
    - "Learn: our DTD shell structure and specialization modules"
    - "Understand: M1-M7 metadata rules"
    - "Attend: Architecture & Standards Team meeting (observer)"
    - "Practice: peer review using GitHub PR workflow"

  week_3:
    - "Author 3-5 topics (concept, task, reference)"
    - "Complete: subject scheme and conditional processing training"
    - "Attend: product engineering sync meeting"
    - "Submit first PR for review"

  week_4:
    - "Author independently; mentor reviews asynchronously"
    - "Complete: CI/CD pipeline overview"
    - "Join: on-call rotation for CI/CD failure triage (shadow)"
    - "30-day check-in with Doc Manager"
```

---

# Onboarding: DITA Certification Path

Internal certification levels for ExampleCorp writers:

| Level | Title | Requirements | Timeline |
|-------|-------|-------------|----------|
| **L1** | DITA Author | oXygen proficiency, 20 published topics, pass metadata audit | Month 1 |
| **L2** | DITA Practitioner | Author all 3 topic types, use conref/conkeyref, pass compliance check | Month 3 |
| **L3** | DITA Specialist | Design a simple domain specialization, contribute to shared conref library | Month 6 |
| **L4** | DITA Architect (Candidate) | DTD/XSD specialization, DITA-OT plugin development, mentor junior writers | Month 12+ |

```python
# Certification is tracked programmatically
certification_checks = {
    "L1": lambda w: w.published_topics >= 20 and w.metadata_pass_rate >= 0.95,
    "L2": lambda w: w.topic_types_used >= {'task', 'concept', 'reference'}
                     and w.has_used_conref and w.compliance_score >= 90,
    "L3": lambda w: w.specialization_contributions >= 1
                     and w.shared_conref_contributions >= 3,
}
```

---

# Career Paths: DITA Specialist Ladder

```
                    ┌─────────────────────┐
Level 6             │  VP of Content      │
                    │  Strategy           │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
Level 5             │  DITA Architect     │
                    │  / Doc Manager      │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
Level 4             │  Information        │
                    │  Architect          │
                    └──────────┬──────────┘
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
┌─────────┴─────────┐ ┌───────┴───────┐ ┌─────────┴─────────┐
Level 3  │ Sr Tech Writer  │ │Tools & Pipe-  │ │Quality & Com-    │
         │ (Product Lead)  │ │line Engineer  │ │pliance Lead      │
         └─────────┬───────┘ └───────┬───────┘ └─────────┬───────┘
                   │                 │                    │
         ┌─────────┴─────────┐ ┌─────┴──────┐ ┌─────────┴─────────┐
Level 2  │ Technical Writer   │ │Jr Pipeline │ │Compliance         │
         │ (DITA Practitioner) │ │Engineer    │ │Analyst            │
         └─────────┬─────────┘ └────────────┘ └───────────────────┘
                   │
         ┌─────────┴─────────┐
Level 1  │ Associate Writer   │
         │ (DITA Author)      │
         └───────────────────┘
```

---

# Career Paths: Skill Development Matrix

| Role Transition | Technical Skills Needed | Leadership Skills |
|----------------|------------------------|-------------------|
| Writer L1 to L2 | conref, conkeyref, DITAVAL, subject scheme | Peer review, estimation |
| Writer to Senior | DTD specialization basics, map design | Mentoring, stakeholder mgmt |
| Writer to Pipeline Eng | Python, Bash, GitHub Actions, XSLT | CI/CD ownership |
| Writer to QA Lead | ISO standards, audit methodology | Cross-functional collaboration |
| Senior to Info Architect | Taxonomy design, content modeling, UX writing | Architecture governance |
| Senior to DITA Architect | Advanced DTD/XSD, DITA-OT plugins, XSL-FO | CoE leadership, vendor mgmt |

**Training budget per person:** $2,500/year for courses, conferences (CIDM DITA NA, ConVEx).

---

# Stakeholder Map

```
┌─────────────────────────────────────────────────────────────┐
│                     STAKEHOLDER ECOSYSTEM                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │  Engineering  │  │ Manufacturing│  │   Quality    │       │
│  │  (SMEs, PMs)  │  │(Floor Mgrs,  │  │(ISO Auditors,│       │
│  │               │  │ Operators)   │  │ Compliance)  │       │
│  └───────┬───────┘  └───────┬──────┘  └───────┬──────┘      │
│          │                  │                  │              │
│          └──────────────────┼──────────────────┘              │
│                             │                                │
│                    ┌────────┴────────┐                       │
│                    │   DITA CoE      │                       │
│                    │   (You)         │                       │
│                    └────────┬────────┘                       │
│                             │                                │
│          ┌──────────────────┼──────────────────┐              │
│          │                  │                  │              │
│  ┌───────┴───────┐  ┌───────┴───────┐  ┌───────┴───────┐      │
│  │   Leadership   │  │  Translation  │  │     IT        │      │
│  │(VP Eng, CTO,   │  │  Vendors      │  │(Infra, Sec,   │      │
│  │ Finance)       │  │               │  │ DevOps)       │      │
│  └───────────────┘  └───────────────┘  └───────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

---

# Stakeholder Management: Communication Cadence

| Stakeholder | What They Care About | Communication | Frequency |
|------------|---------------------|---------------|-----------|
| **VP Engineering** | Time-to-publish, cost, quality | Executive dashboard | Monthly |
| **Product Managers** | Feature coverage, release alignment | Product doc status | Weekly (standup) |
| **SMEs (Engineers)** | Accuracy of technical content | Review requests, SME interviews | Per-topic |
| **Floor Managers** | Operator safety, procedure clarity | Usability feedback sessions | Quarterly |
| **ISO Auditors** | Compliance evidence, audit trail | Audit report package | Annually (pre-audit) |
| **IT / DevOps** | Infrastructure stability, security | Change requests, incident response | As needed |
| **Finance** | TCO, ROI, budget adherence | Budget review package | Quarterly |
| **Translation Vendors** | Scope, quality, turnaround | TM updates, quality reports | Monthly |

---

# Stakeholder Management: The SME Relationship

SMEs (Subject Matter Experts) are engineers who review technical content for accuracy. This is often the hardest relationship to manage.

```yaml
sme_engagement_strategy:

  recruitment:
    - "Identify one SME per subsystem (spindle, tool changer, control, coolant)"
    - "Get VP Engineering sign-off on SME time commitment (2-4 hours/week)"
    - "Recognize SME contributions in performance reviews"

  workflow:
    - "Writer drafts topic, self-reviews against DITA checklist"
    - "SME reviews only for technical accuracy (NOT grammar/style)"
    - "SME feedback via GitHub PR comments (structured: line-by-line)"
    - "SME has 5 business day SLA; escalate to PM if overdue"

  sme_burnout_prevention:
    - "Rotate SMEs quarterly for large product lines"
    - "Provide SME with 'Technical Review Checklist' (single page)"
    - "Writer pre-fills 'Known Areas of Concern' to focus SME attention"
    - "Limit review batches to 5 topics per request"
```

---

# Collaboration Workflows

### Git-Based Collaboration Model

```
1. Writer creates feature branch: feature/dr5000-spindle-maintenance
2. Writer authors topics in oXygen, commits to branch
3. Writer opens Pull Request (PR) against main
4. Automated validation runs (validate.yml):
   - XML well-formedness
   - Metadata M1-M7
   - Spell check
   - Link validation
5. Peer reviewer (another writer) reviews for DITA compliance
6. SME reviews for technical accuracy (via PR comments)
7. DITA Architect reviews if specialization or conref changes involved
8. All checks pass → merge to main → auto-publish (publish.yml)
```

---

# Collaboration: Branch Strategy

```
main                    ★ Production (published docs)
  │
  ├── feature/*         Topic authoring and edits
  │   ├── feature/dr5000-spindle-calibration
  │   ├── feature/dr8000-coolant-system
  │   └── feature/shared-hazard-statements
  │
  ├── release/*         Preparing a documentation release
  │   └── release/v2.3-docs (content freeze → QA → publish)
  │
  ├── hotfix/*          Urgent safety/regulatory updates
  │   └── hotfix/dr5000-safety-warning-spindle
  │
  └── framework/*       DTD, DITA-OT, plugin changes
      └── framework/add-calibration-domain
```

---

# Collaboration: Daily Team Rhythm

```
09:00 — Daily Standup (15 min, async in Slack)
  - What I published yesterday
  - What I'm working on today
  - Blockers (tag relevant person)

09:15 — Focus Time: Authoring (3h block)
  - oXygen XML Editor, closed Slack, deep work

12:00 — Lunch

13:00 — Collaboration Window (2h)
  - PR reviews, SME meetings, CoE sync

15:00 — Focus Time: Authoring (2h block)

17:00 — End of day: commit work-in-progress to branch

Wednesday 15:00 — Architecture & Standards Team meeting
Friday 14:00 — Weekly Retro (30 min, all writers)
```

---

# Managing Remote/Hybrid DITA Teams

With writers potentially distributed across facilities, tools matter:

| Practice | Tool | Rationale |
|----------|------|-----------|
| **Async PR reviews** | GitHub | No timezone dependency |
| **Pair writing (remote)** | oXygen + screen share | Senior mentors junior live |
| **SME interviews** | Recorded Teams/Zoom | One SME session feeds multiple writers |
| **Virtual watercooler** | Slack #docs-random | Maintains team cohesion |
| **Knowledge base** | Confluence/Notion | Self-service for common questions |
| **Framework changelog** | CHANGELOG.md in repo | All writers notified of DTD changes |

```markdown
# CHANGELOG.md excerpt
## [2.4.0] - 2026-06-01
### Added
- calibrationDomain: new domain for measurement/calibration procedures
- mfg-task-cal.dtd: new DTD shell integrating hi-d + calibration
### Changed
- Updated catalog.xml with calibration domain PUBLIC ID mappings
### Deprecated
- Old `repair-procedure` element — migrate to `maintenanceInterval`
```

---

# Conflict Resolution: Writer vs Architect

Disagreements happen. Here is the escalation path:

```
Level 1: Writer and Architect discuss directly
  ↓ (no resolution)

Level 2: Bring to Architecture & Standards Team meeting
  - Both sides present rationale
  - Team votes; Architect breaks tie
  ↓ (no resolution or policy issue)

Level 3: Escalate to DITA Governance Council
  - VP Engineering, Doc Manager, ISO Rep
  - Decision is binding and documented in GOVERNANCE.md
```

Example dispute: "Should safety warnings use `<hazardstatement>` or `<note type='danger'>`?"

**Resolution (Level 1):** Use `<hazardstatement>` per ISO 3864-2 requirements. Documented in authoring guide.

---

# Team Health Metrics

Measure the team, not just the content:

| Metric | Current | Target | Indicator |
|--------|---------|--------|-----------|
| **Writer satisfaction (eNPS)** | 42 | > 50 | Quarterly survey |
| **Voluntary turnover** | 8% | < 10% | Annual |
| **SME response SLA met** | 78% | > 90% | Monthly |
| **PR review turnaround** | 1.8 days | < 2 days | Weekly |
| **Onboarding time to L2** | 4.2 months | < 3 months | Per new hire |
| **Training hours/writer/year** | 28h | > 40h | Annual |

**Red flags requiring Doc Manager attention:**
- PR review turnaround exceeds 3 days for 2 consecutive weeks
- Any writer has no PR merged for 5+ business days
- SME satisfaction score drops below 3.5/5

---

<!-- _class: exercise -->

# Hands-On Exercise

**Goal:** Design the organizational model and onboarding plan for a new DITA team.

**Scenario:** ExampleCorp is adding a fourth product line, DR-12000 (large gantry mill). You need to staff and onboard a team of 4 writers.

**Steps:**

1. **Design the team structure:**
   - Assign roles (1 Senior Writer, 3 Writers)
   - Determine reporting lines and dotted-line relationships
   - Define CoE touchpoints (architecture reviews, conref contributions)

2. **Build the RACI matrix** for the new team covering 8 key activities (authoring, review, CI/CD, etc.)

3. **Create a 30-day onboarding plan** for a new writer joining the DR-12000 team. Include specific DITA skills to acquire, topics to write, and checkpoints.

4. **Draft CODEOWNERS entries** for `src/dr-12000/` content and any new shared content.

5. **Write the stakeholder communication plan** specifically for the DR-12000 engineering team.

**Deliverable:** `dr12000-team-plan.md` with all five sections.

---

# Key Takeaways

| # | Takeaway |
|---|----------|
| 1 | A DITA Center of Excellence is the organizational key to consistent, governed content |
| 2 | The DITA Architect role is critical — hire or develop one before scaling |
| 3 | RACI matrices eliminate ambiguity: everyone knows who does what and who approves |
| 4 | The hybrid ownership model (product teams + shared services) balances autonomy with consistency |
| 5 | Budget for total cost of ownership, not just licenses — translation is the #2 cost after personnel |
| 6 | Structured onboarding (30-60-90 day plans) accelerates DITA proficiency |
| 7 | Career paths for DITA specialists keep talent: writer → senior → architect or pipeline engineer |
| 8 | Stakeholder management is as important as technical skill — communicate in their language |
| 9 | Measure team health alongside content health — burned-out writers produce poor documentation |
