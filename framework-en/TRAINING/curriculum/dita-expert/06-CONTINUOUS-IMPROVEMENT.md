---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Continuous Improvement
## Maturing Your DITA Practice for Manufacturing

**DITA Expert Level - Module 6**
Example Manufacturing Co., Ltd.
v1.1 | 2026-06-07

---

<!-- _class: divider -->

# Agenda

1. The DITA Maturity Model (Levels 1-5)
2. Organizational Self-Assessment
3. 12-Month Improvement Roadmap
4. Quarterly Review Cadence & Metrics
5. User Feedback Collection and Analysis
6. AI/ML in Technical Documentation
7. DITA 2.0 and LwDITA
8. Exercise: 6-Month Roadmap for ExampleCorp
9. Summary

---

<!-- _class: keypoint -->

# Learning Objectives

By the end of this module, you will be able to:

1. **Assess** your organization's DITA maturity across 5 levels and 5 dimensions
2. **Build** a 12-month continuous improvement roadmap with measurable milestones
3. **Design** a quarterly review process with KPIs and corrective actions
4. **Implement** user feedback collection mechanisms for documentation quality
5. **Evaluate** AI/ML opportunities for manufacturing documentation automation
6. **Prepare** for DITA 2.0 migration and lightweight DITA (LwDITA)
7. **Create** a tailored 6-month roadmap for Example Manufacturing

---

# The Case for Continuous Improvement

DITA is not a one-time migration. It is an ongoing practice:

```
Year 1: SURVIVE  -- Get content into DITA, basic publishing
Year 2: OPTIMIZE  -- Reuse, specialization, metrics
Year 3: INNOVATE  -- AI, integrations, community leadership
Year 5: LEAD      -- Contribute to standards, speak at conferences
```

**Manufacturing reality at ExampleCorp:**
- Product lines change (new machines, new features)
- Regulations change (ISO standards revised every 5 years)
- Technology changes (DITA 2.0, AI tools emerge)
- Team changes (writers join and leave, skills evolve)

> "The only constant in technical documentation is change." -- every Doc Manager

---

## The DITA Maturity Model: Overview

```
Level 5: OPTIMIZING
  └── Continuous process improvement, AI-assisted authoring,
      industry leadership, standards contribution

Level 4: MANAGED
  └── Metrics-driven, CI/CD fully automated, CoE operating,
      cross-product reuse, proactive quality management

Level 3: DEFINED
  └── Standards documented, specialization strategy defined,
      reuse library established, training program formalized

Level 2: REPEATABLE
  └── Consistent authoring practices, basic reuse,
      manual build process, template-driven content

Level 1: INITIAL (Ad Hoc)
  └── First migration from unstructured, basic maps/topics,
      writers learning DITA, no governance
```

---

# Level 1: Initial (Ad Hoc)

**Characteristics:**
- First migration from Word/FrameMaker to DITA
- Writers learning DITA basics (maps, topics, conref)
- No formal governance or standards
- Build process is manual (DITA-OT command line)
- No reuse strategy; content is copy-paste within DITA
- DTD is standard OASIS distribution (no specialization)

| Metric | Typical Value |
|---|---|
| Reuse Ratio | < 0.15 |
| Build Time | Manual, 2+ hours |
| Compliance Score | < 70 |
| CI/CD | None |

**Goal: Survive the migration. Get content published.**

---

# Level 2: Repeatable

**Characteristics:**
- Consistent authoring patterns across the team
- Basic reuse using conref for shared warnings and notes
- Build process is scripted (Ant or simple CI)
- DITA maps structured by product and audience
- Writers trained on DITA fundamentals
- Metadata standards defined (but not enforced)

| Metric | Typical Value |
|---|---|
| Reuse Ratio | 0.15 - 0.25 |
| Build Time | Semi-automated, < 30 min |
| Compliance Score | 70 - 80 |
| CI/CD | Basic validation on PR |

**Goal: Establish repeatable processes. Reduce manual effort.**

---

# Level 3: Defined

**Characteristics:**
- DITA authoring standards documented in formal guide
- Specialization strategy defined and first domains deployed
- Shared conref library with taxonomy
- CI/CD pipeline with automated validation
- Training program formalized (onboarding, certification)
- DITA Architect role established
- Governance charter (DGC) in place

| Metric | Typical Value |
|---|---|
| Reuse Ratio | 0.25 - 0.40 |
| Build Time | Fully automated, < 15 min |
| Compliance Score | 80 - 90 |
| CI/CD | Full validate + publish pipeline |

**ExampleCorp current level: Level 3 (transitioning to Level 4).**

---

# Level 4: Managed

**Characteristics:**
- Metrics-driven content operations (KPI dashboard)
- CI/CD fully automated with matrix builds
- Center of Excellence operating with full governance
- Cross-product reuse at 40%+
- Proactive quality management (Schematron, compliance scoring)
- Integration with PLM, MES, ERP systems
- Translation optimized with TM leverage > 60%

| Metric | Typical Value |
|---|---|
| Reuse Ratio | 0.40 - 0.60 |
| Build Time | < 10 min (matrix parallel) |
| Compliance Score | 90 - 95 |
| CI/CD | Full pipeline + monitoring |

**Goal: Manage by data, not intuition.**

---

# Level 5: Optimizing

**Characteristics:**
- Continuous process improvement culture
- AI-assisted authoring (content generation, consistency checking)
- Predictive analytics for content quality
- Industry leadership (conference presentations, standards contributions)
- DITA 2.0 adoption and early implementation
- Community engagement (open-source contributions to DITA-OT)

| Metric | Typical Value |
|---|---|
| Reuse Ratio | > 0.60 |
| Build Time | < 5 min |
| Compliance Score | > 95 |
| CI/CD | Predictive quality gates |

**Goal: Lead the industry. Shape the standards.**

---

# Self-Assessment Tool

Score your organization across 5 dimensions (1-5 scale):

| Dimension | Questions to Ask |
|---|---|
| **Governance** | Is there a DITA governance charter? Does Architecture Team meet regularly? Is there a formal specialization request process? |
| **Reuse** | Is reuse ratio calculated and tracked? Does a shared conref library exist with taxonomy? Is cross-product reuse measured? |
| **Automation** | Does CI/CD validate on every PR? Is publishing fully automated on merge? Is metrics collection automated with dashboard? |
| **Quality** | Is compliance scoring automated? Do readability checks run in CI/CD? Are Schematron business rules enforced? |
| **Team** | Is DITA training formalized? Do career paths for DITA specialists exist? Is a Center of Excellence operating? |

---

# ExampleCorp Self-Assessment Q2 2026

| Dimension | Score | Strengths | Gaps |
|---|---|---|---|
| **Governance** | 3.8 | DGC active, Architecture Team bi-weekly | No formal specialization request process |
| **Reuse** | 3.2 | Shared conref library exists | Cross-product reuse not optimized |
| **Automation** | 4.0 | Full CI/CD pipeline | No real-time metrics dashboard |
| **Quality** | 3.5 | Automated compliance scoring | Schematron rules not fully deployed |
| **Team** | 3.3 | Training program, CoE chartered | Career paths need formalization |
| **Overall** | **3.56** | **Level 3 transitioning to 4** | |

**Priority actions to reach Level 4:**
1. Deploy Schematron rules in CI/CD (Quality)
2. Build cross-product reuse strategy (Reuse)
3. Formalize career ladder with HR (Team)

---

# 12-Month Roadmap: Q3-Q4 2026

```yaml
roadmap_2026_h2:

  q3_2026:
    theme: "Quality and Automation"
    milestones:
      - Deploy Schematron validation in CI/CD pipeline
      - Launch real-time metrics dashboard (Grafana + InfluxDB)
      - Complete MC-8000 metadata remediation
      - Achieve 90% component coverage on MC-8000
      - Pilot AI-assisted terminology checking

  q4_2026:
    theme: "Reuse and Integration"
    milestones:
      - Reuse ratio reaches 30% across all product lines
      - Cross-product conref audit complete
      - SAP ERP integration go-live (auto-push PDF to DMS)
      - MES work instruction pilot on 3 factory floor terminals
      - All writers certified at L2 or higher
      - Annual maturity assessment: target 4.0
```

---

# 12-Month Roadmap: Q1-Q2 2027

```yaml
roadmap_2027_h1:

  q1_2027:
    theme: "Innovation and Expansion"
    milestones:
      - DITA 2.0 readiness assessment complete
      - AI-assisted content gap analysis pilot
      - Reuse ratio reaches 35%
      - Translation TM leverage reaches 65%
      - Subject scheme extended to all MC-5000 features
      - First community contribution: DITA-OT PR accepted

  q2_2027:
    theme: "Leadership and Maturity"
    milestones:
      - DITA maturity Level 4.5 achieved
      - Present at CIDM DITA North America conference
      - DITA 2.0 migration plan published
      - AI content generation pilot for standard procedures
      - Reuse ratio reaches 40%
      - Publish manufacturing domain as open source
```

---

# Quarterly Review Cadence

```
Q1 Review (March)    Q2 Review (June)    Q3 Review (Sept)   Q4 Review (Dec)
     │                     │                    │                   │
     ├─ Metrics            ├─ Metrics           ├─ Metrics          ├─ Metrics
     ├─ Roadmap progress   ├─ Roadmap progress  ├─ Roadmap progress ├─ Roadmap progress
     ├─ User feedback      ├─ User feedback     ├─ User feedback    ├─ User feedback
     ├─ Team health        ├─ AI/ML eval        ├─ Training review  ├─ Annual assessment
     └─ Next quarter OKRs  └─ Mid-year adjust   └─ Budget planning  └─ Next year roadmap
```

**Review template structure:**
1. Executive summary (1 paragraph)
2. KPIs vs targets (table)
3. Milestone progress (completed / on-track / at-risk / blocked)
4. User feedback highlights
5. Team health and training
6. Corrective actions
7. Next quarter OKRs

---

# Quarterly Review KPI Dashboard

| KPI | Q2 Target | Q2 Actual | Status |
|---|---|---|---|
| Reuse Ratio | 28% | 26% | At Risk |
| Build Success Rate | 98% | 99.7% | On Track |
| Compliance Score | 85 | 84.6 | On Track |
| PR Review Time (avg) | < 48h | 52h | At Risk |
| Translation TM Leverage | 60% | 58% | At Risk |
| Topics Published/Month | 45 | 52 | Exceeded |
| Training Certifications (L2+) | 8 | 7 | On Track |
| SME Response Rate | 85% | 74% | At Risk |

---

# User Feedback Collection

| Channel | Frequency | Data Type | Owner |
|---|---|---|---|
| **Online help rating** | Continuous | 1-5 star + free text | Product Manager |
| **Support ticket analysis** | Monthly | "Documentation unclear" tags | Support Lead |
| **Shop floor interviews** | Quarterly | Structured Q&A with operators | Doc Manager |
| **SME feedback form** | Per review | Technical accuracy rating | Review Coordinator |
| **NPS survey** | Semi-annually | Net Promoter Score | Doc Manager |
| **Usage analytics** | Continuous | Page views, search queries, bounce rate | IT / Web Team |

---

# User Feedback: Action Loop

```
COLLECT ────── ANALYZE ────── PRIORITIZE ────── IMPLEMENT ────── MEASURE
   │                │               │                 │               │
   │ Ratings,       │ Find top 3    │ Add to backlog  │ Update topics, │ Track if
   │ interviews,    │ pain points   │ with severity   │ restructure,   │ ratings
   │ support tags   │               │ and effort      │ add content    │ improve
   │                │               │                 │               │
   └──────────────────────────────────────────────────────────────────────┘
                                  (Continuous loop)
```

**ExampleCorp Q2 2026 feedback summary:**
- Top issue: "Coolant change procedure unclear" (12 support tickets)
- Top request: "Video-based procedure for tool offset setup" (8 operators)
- NPS: 42 (up from 38 in Q1; target is 50)

---

# AI/ML Opportunities in DITA Manufacturing

| Application | Technology | Maturity | Priority |
|---|---|---|---|
| **Terminology Checking** | NLP + Controlled Language | Production-ready | HIGH |
| **Content Gap Analysis** | ML classification | Emerging | HIGH |
| **Automated Translation** | Neural MT (NMT) | Production-ready | MEDIUM |
| **Readability Scoring** | ML regression | Production-ready | MEDIUM |
| **Content Generation** | LLM (GPT, Claude) | Experimental | LOW |
| **Compliance Checking** | NLP + Rule Engine | Emerging | HIGH |
| **Semantic Search** | Embeddings + Vector DB | Production-ready | MEDIUM |

---

# AI Application: Terminology Consistency

```python
import spacy

DEPRECATED_TERMS = {
    "hand wheel": "manual pulse generator",
    "coolant fluid": "cutting fluid",
    "slide way": "guide way",
}

nlp = spacy.load("en_core_web_lg")

def check_terminology(topic_path):
    with open(topic_path) as f:
        text = f.read()
    doc = nlp(text)
    findings = []
    for sent in doc.sents:
        for deprecated, preferred in DEPRECATED_TERMS.items():
            if deprecated in sent.text.lower():
                findings.append({
                    "file": topic_path,
                    "deprecated": deprecated,
                    "preferred": preferred,
                    "context": sent.text.strip()[:100],
                })
    return findings
```

---

# AI Application: Content Gap Detection

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

def detect_gaps(feature_list, docs):
    """Compare product features against documentation coverage."""
    feature_texts = [f['description'] for f in feature_list]
    doc_texts = [extract_text(d) for d in docs]
    all_texts = feature_texts + doc_texts
    tfidf = TfidfVectorizer(stop_words='english').fit_transform(all_texts)

    gaps = []
    for i in range(len(feature_texts)):
        sim = cosine_similarity(tfidf[i], tfidf[len(feature_texts):])
        if np.max(sim) < 0.15:
            gaps.append({"feature": feature_list[i]['name'], "id": feature_list[i]['id']})
    return gaps
```

---

# DITA 2.0: What's Changing

| Change | Impact on Manufacturing | Migration Effort |
|---|---|---|
| **Relax NG replaces DTD** | All specialization modules must be rewritten | HIGH |
| **New `<include>` element** | Simpler reuse; replaces some conref use cases | LOW |
| **Simplified content models** | Fewer required elements; easier authoring | MEDIUM |
| **Key scopes everywhere** | Better key management across large product lines | MEDIUM |
| **Multimedia domain** | Native video/3D model support | LOW |
| **Improved troubleshooting** | Richer troubleshooting topic type | LOW |

**ExampleCorp migration plan:** Q1 2027 readiness assessment, Q2 2027 begin RNG conversion, Q4 2027 full migration target.

---

# LwDITA (Lightweight DITA)

LwDITA is a simplified DITA subset for contributors who do not need full DITA:

| Format | Complexity | Use Case |
|---|---|---|
| **XDITA** | XML-based, simplified | Power users, existing tools |
| **HDITA** | HTML5-based | Web contributors, marketing |
| **MDITA** | Markdown-based | Developers, GitHub-native workflows |

**ExampleCorp LwDITA strategy:**
- **XDITA**: Standard for all manufacturing documentation
- **MDITA**: For engineering release notes and API docs
- **HDITA**: Not currently in use; evaluate for knowledge base portal

---

# LwDITA: Markdown Example (MDITA)

```markdown
---
id: task-shop-floor-cleanup
type: task
xml:lang: en-US
audience: operator
---

# Daily Shop Floor Cleanup

**Shortdesc:** Clean the machine area at end of shift.

## Prerequisites
- Machine in E-Stop
- All tools removed from work area

## Procedure

1. Remove all chips from chip tray using chip hook
2. Vacuum coolant sump area
3. Wipe down all external surfaces with approved cleaner
4. Empty chip bin into recycling container
5. Verify coolant level; refill if below MIN mark

## Result
Machine area is clean, coolant level verified, ready for next shift.
```

---

# Building a Culture of Improvement

```
PLAN ────────▶ DO ────────▶ CHECK ────────▶ ACT
  │              │              │              │
  Identify       Implement      Measure        Adjust
  improvement    change on      results        based on
  opportunity    pilot scale    against KPI    findings
  │              │              │              │
  └──────────────────────────────────────────────┘
```

**Kaizen for documentation:**
- Monthly retrospectives
- "One small improvement per sprint" policy
- Blameless post-mortems for CI/CD failures
- Innovation time: 10% of work week for experiments

---

# Innovation Time: 10% Rule

Reserve 4 hours per week per writer for improvement:

| Area | Example Projects |
|---|---|
| **Tooling** | Write a script to automate a manual step |
| **Quality** | Improve one Schematron rule |
| **Reuse** | Refactor 3 topics to use shared conref |
| **Learning** | Complete a training module |
| **Community** | Answer one dita-users question |
| **Documentation** | Improve internal authoring guide |

**Q2 2026 results at ExampleCorp:**
- 3 scripts automated, 27 topics refactored for conref
- 2 Schematron rules added, 8 training modules completed
- Estimated value: ~$45K annual impact from innovation time

---

<!-- _class: exercise -->

# Exercise: 6-Month Roadmap for ExampleCorp

**Scenario:** You are the DITA Architect at ExampleCorp. Current maturity is Level 3.56. The VP of Engineering has asked for a focused 6-month plan to reach Level 4.0 by year-end.

**Deliverable:** A document with:

1. **Current State Assessment** -- Score all 5 maturity dimensions. Identify top 3 gaps.

2. **6-Month Milestone Plan** -- For each month (July-December 2026), list 2-3 concrete milestones with owners. Example format:

```
July:  Deploy Schematron rules in CI/CD [Owner: Quality Lead]
       Begin cross-product conref audit [Owner: Reuse Architect]
```

3. **KPI Targets** -- Set end-of-December targets for: reuse ratio, build success rate, compliance score, PR review time, SME response rate.

4. **Risk Register** -- Identify 3 risks and mitigation strategies.

5. **Executive Summary** -- One paragraph for the VP.

**Time:** 30 minutes

---

# Key Takeaways

| # | Takeaway |
|---|---|
| 1 | Maturity model (L1-L5) provides a structured path from ad-hoc to optimizing |
| 2 | Self-assessment across 5 dimensions identifies concrete gaps to address |
| 3 | 12-month roadmap should have quarterly themes, milestones, and named owners |
| 4 | Quarterly reviews with KPIs enable course correction and accountability |
| 5 | User feedback (ratings, support tickets, interviews) drives content priorities |
| 6 | AI/ML for terminology checking, gap detection, and compliance is ready today |
| 7 | DITA 2.0 (RNG replaces DTD) is the biggest upcoming change -- start planning now |
| 8 | LwDITA (MDITA/HDITA/XDITA) expands DITA to non-specialist contributors |
| 9 | Continuous improvement is a culture: retrospectives, innovation time, Kaizen |

---

# Resources

- **DITA Maturity Assessment Tool**: Internal Confluence page
- **Quarterly Review Template**: `templates/quarterly-review.md`
- **User Feedback Dashboard**: Grafana at `dashboards.dingrui.cn`
- **DITA 2.0 Working Draft**: github.com/oasis-tcs/dita
- **LwDITA Spec**: docs.oasis-open.org/dita/LwDITA/
- **DITA-OT Community**: github.com/dita-ot/dita-ot
