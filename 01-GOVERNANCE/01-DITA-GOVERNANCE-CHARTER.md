# DITA Governance Charter

**Document ID:** GOV-001  
**Version:** 1.0  
**Last Updated:** 2026-06-06  
**Status:** Approved  
**Classification:** Internal Use Only  
**Review Frequency:** Quarterly

---

## Executive Summary

This charter establishes the governance framework for DITA (Darwin Information Typing Architecture) adoption across our manufacturing enterprise. It defines roles, responsibilities, decision-making authority, and compliance requirements for all technical documentation.

## 1. Vision & Objectives

### Vision
Create a **single source of truth** for technical documentation across all products, enabling:
- 35-40% content reuse across products
- 30-35% reduction in localization costs
- Faster time-to-market for product documentation
- Improved customer satisfaction through consistent, searchable documentation
- Full ISO compliance with audit trails

### Strategic Objectives
1. **Centralize Content** - Move all docs to DITA format within 18 months
2. **Maximize Reuse** - Establish shared content repository for common topics
3. **Ensure Quality** - ISO-compliant review and approval workflows
4. **Enable Localization** - Support 8+ languages simultaneously
5. **Automate Publishing** - CI/CD pipeline for instant distribution
6. **Measure Success** - Track KPIs quarterly

## 2. Governance Structure

### 2.1 Documentation Governance Committee (DGC)

**Charter Members:**
- **Director of Technical Communications** (Chair)
- **Product Manager** (one representative from each product line)
- **QA Lead** (Quality Assurance)
- **Localization Manager**
- **IT/Infrastructure Lead** (Tools & Publishing)
- **Compliance/Legal Representative**
- **Customer Support Lead**

**Responsibilities:**
- Review and approve DITA governance policies
- Resolve cross-product content conflicts
- Approve new specializations or domains
- Review quarterly KPIs and adjust strategy
- Oversee localization strategy
- Ensure ISO compliance

**Meeting Cadence:** Monthly (first Thursday, 10 AM)

### 2.2 Architecture & Standards Team

**Members:**
- **DITA Architect** (1 FTE)
- **Senior Technical Writer** (1 FTE)
- **Metadata Manager** (0.5 FTE)
- **Tools Administrator** (1 FTE)

**Responsibilities:**
- Maintain style guide and templates
- Define metadata standards
- Design information architecture
- Review topic structures before approval
- Manage specializations

**Meeting Cadence:** Bi-weekly

### 2.3 Product Documentation Teams

**Per Product (typical structure for 50+ team):**
- **Senior Writer** (1 FTE) - Team lead, strategy
- **Product Writers** (2-3 FTE) - Content creation
- **Localization Specialist** (0.5 FTE) - Translation coordination
- **QA Writer** (0.5 FTE) - Test scenario documentation

**Responsibilities:**
- Create and maintain product-specific documentation
- Follow style guide and architecture standards
- Participate in content reuse initiatives
- Ensure timely topic updates
- Support customer education

## 3. Content Ownership & Accountability

### Content Categories

| Category | Owner | Approval | Update Frequency |
|----------|-------|----------|------------------|
| **Shared/Common Topics** | DITA Architect | DGC | As-needed |
| **Product-Specific Topics** | Product Writer | Product Manager | Per Release |
| **Compliance/Legal Topics** | Compliance + Product Writer | Legal Review | Quarterly |
| **API/Technical Reference** | Developer Relations | Tech Lead | Per Release |
| **Release Notes** | Product Manager + Writer | Product Manager | Per Release |
| **Knowledge Base/FAQ** | Customer Support | Support Manager | Monthly |

### Change Authority Matrix

```
Minor Updates (Typos, Examples):    Writer → Auto-merge
Content Updates (Technical):         Writer → Product Manager → Merge
Structure Changes (Maps, Topics):    Writer → Architect → DGC → Merge
Specialization Changes:             Architect → DGC → Merge
Style Guide Changes:                Architect → DGC → Merge
```

## 4. Topic Development & Approval Workflow

### Standard Workflow

```
1. PLAN
   ↓
2. DRAFT (Writer creates topic in Git branch)
   ↓
3. SME REVIEW (Subject Matter Expert reviews technical accuracy)
   ↓
4. COMPLIANCE CHECK (Legal/Compliance reviews for ISO compliance)
   ↓
5. EDITORIAL REVIEW (Architect/Senior Writer reviews structure/style)
   ↓
6. APPROVAL (Product Manager signs off)
   ↓
7. PUBLISH (Auto-merge to main, triggers CI/CD pipeline)
   ↓
8. DISTRIBUTE (Multi-channel publishing: Web, PDF, Mobile, etc.)
   ↓
9. LOCALIZE (Automated extraction to translation system)
   ↓
10. MONITOR (Track usage, feedback, issues)
```

### SLA (Service Level Agreements)

| Task | Deadline | Owner |
|------|----------|-------|
| Draft Topic | 3 business days | Writer |
| SME Review | 2 business days | SME |
| Compliance Check | 1 business day | Compliance |
| Editorial Review | 1 business day | Architect |
| Final Approval | 1 business day | Product Manager |
| **Total Time to Publish** | **< 8 business days** | - |

## 5. ISO Compliance Requirements

### 5.1 ISO 27001 (Information Security)

**Requirements:**
- All DITA files stored in secure Git repository with access control
- Change logs tracked automatically (Git history)
- Sensitive information (passwords, API keys) NEVER in docs
- Encryption for files at rest and in transit
- Access control via role-based permissions

**Implementation:**
- GitHub Enterprise with 2FA requirement
- Branch protection rules
- Audit logs enabled
- Regular security reviews

### 5.2 ISO 9001 (Quality Management)

**Requirements:**
- Documented process for content creation
- Quality metrics tracked quarterly
- Corrective action process for errors
- Customer feedback incorporated
- Document review and approval documented

**Implementation:**
- Approval workflow (see section 4)
- Review checklist in each PR
- Feedback loop from support
- Monthly quality reports

### 5.3 ISO 14001 (Environmental Management)

**Requirements:**
- Minimize paper documentation
- Digital-first publishing
- Optimize file sizes for reduced transmission

**Implementation:**
- Web-first publishing strategy
- PDF on-demand only
- Minimize image sizes
- Report environmental impact quarterly

### 5.4 Document Traceability

**All documents must include:**
```
- Document ID (e.g., DOC-PROD-001-v2.1)
- Version number
- Last updated date
- Author name
- Review status
- Classification level
- Review frequency
```

**Metadata stored in:**
- Git commit history
- DITA prolog element
- Document control system

## 6. Content Standards & Compliance

### 6.1 Mandatory Metadata

Every topic MUST include:
- `docid` - Unique document identifier
- `audience` - Target audience (admin, user, developer, etc.)
- `product` - Product name/code
- `version` - Product version applicability
- `status` - draft | review | approved | obsolete
- `created` - Creation date
- `revised` - Last revision date
- `reviewed-by` - Approver name
- `security-level` - public | internal | confidential
- `revision-reason` - Why this revision was made

### 6.2 Topic Type Requirements

**Concept Topics (40% of content)**
- Answer "What is..." questions
- Include diagrams/examples
- Cross-reference related topics

**Task Topics (40% of content)**
- Step-by-step procedures
- Prerequisites stated upfront
- Expected outcome described
- Troubleshooting info included

**Reference Topics (20% of content)**
- Command/configuration documentation
- Data tables
- API specifications
- Menu/screen descriptions

## 7. Key Performance Indicators (KPIs)

### Quarterly Metrics

| Metric | Target | Method | Owner |
|--------|--------|--------|-------|
| **Content Reuse Ratio** | 35-40% | Manual audit | Architect |
| **Publishing Cycle** | <2 hours | CI/CD logs | Tools Admin |
| **Translation Time** | <1 week | TMS reports | Localization Mgr |
| **Customer Satisfaction** | >4.0/5.0 | Surveys | Support Lead |
| **Error Rate** | <2% | QA audits | QA Lead |
| **Topic Completeness** | >95% | Metadata review | Writers |
| **Localization Cost** | 30-35% ↓ | Budget analysis | Finance |
| **Time-to-Update** | <24 hrs | Git logs | Writers |

## 8. Training & Onboarding

### Mandatory Training

**All Writers (40 hours):**
1. DITA Fundamentals (8 hours)
2. Style Guide Deep Dive (8 hours)
3. Tool Training (8 hours)
4. Hands-on Project (16 hours)

**Leadership (8 hours):**
1. Governance Overview (2 hours)
2. KPI Tracking (2 hours)
3. ISO Compliance (2 hours)
4. Localization Strategy (2 hours)

### Annual Recertification

All staff must complete 4-hour annual refresher training.

## 9. Risk Management

### Identified Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Tool failure | Medium | High | Backup systems, redundancy |
| Knowledge loss | Medium | High | Documentation, mentoring |
| Scope creep | High | Medium | Change control process |
| Language barriers | Medium | Medium | Professional translators |
| Compliance gaps | Low | High | Regular audits, checklists |
| Version conflicts | Medium | Medium | Strict version control |

## 10. Budget & Resources

### Year 1 Investment

```
Personnel (50% allocation):        $500,000
Tools (oXygen, TMS, CI/CD):        $150,000
Training & Onboarding:             $75,000
Localization (5 languages):        $200,000
Infrastructure/Publishing:         $100,000
────────────────────────────────────────────
TOTAL YEAR 1:                      $1,025,000

Expected ROI (Year 2-3):
- Reduced localization cost:       $150,000/year
- Reduced documentation time:      $200,000/year
- Improved customer support:       $100,000/year
────────────────────────────────────────────
Total annual benefit:              $450,000
```

## 11. Success Criteria (12-Month)

- [ ] 80% of documentation migrated to DITA
- [ ] 35-40% content reuse achieved
- [ ] All 5 Tier-1 languages supported
- [ ] Publishing cycle < 2 hours
- [ ] ISO compliance audit passed
- [ ] Customer satisfaction > 4.0/5.0
- [ ] Team trained and productive
- [ ] CI/CD pipeline operational

## 12. Approval & Sign-Off

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Director, Tech Comms | _____________ | _____________ | _______ |
| VP, Products | _____________ | _____________ | _______ |
| VP, Operations | _____________ | _____________ | _______ |
| Compliance Officer | _____________ | _____________ | _______ |

---

**Document ID:** GOV-001  
**Next Review Date:** Q3 2026  
**Change Log:**
- v1.0 - Initial governance charter (2026-06-06)
