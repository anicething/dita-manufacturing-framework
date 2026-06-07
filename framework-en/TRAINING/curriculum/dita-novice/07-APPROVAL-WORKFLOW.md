---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Approval Workflow
## Module 07: From Draft to Publication

**DITA Novice Course | Manufacturing Documentation**

---

# Learning Objectives

By the end of this module, you will be able to:

- Explain why structured approval matters in manufacturing documentation
- Describe the complete 10-step approval workflow
- Navigate the authoring, review, revision, approval, and publish pipeline
- Use Git-based review processes: branches, pull requests, and approvals
- Identify all review roles and their responsibilities
- Apply labels, milestones, and comment resolution tracking
- Simulate a complete pull request review workflow
- Understand SLAs for each review stage

---

<!-- _class: divider -->

# Part 1: Why Approval Matters in Manufacturing

---

# Documentation Errors in Manufacturing: The Cost

| Error Type | Real-World Consequence | Cost Impact |
|---|---|---|
| **Missing safety warning** | Operator injury, OSHA fine | $100K - $1M+ |
| **Incorrect torque spec** | Machine failure, warranty claim | $50K - $500K |
| **Wrong software version** | CNC crash, production downtime | $10K - $100K/hr |
| **Outdated procedure** | Scrapped parts, rework | $5K - $50K/batch |
| **Untranslated warning** | Regulatory non-compliance (EU, China) | Fines + market ban |

> In manufacturing, documentation errors are not just "typos" -- they are production, safety, and compliance risks.

---

# Why Structured Approval?

Traditional ad-hoc review:

> "Hey, can you look at this manual?" ... _"Looks fine to me."_ ... "OK, sending to print."

Structured DITA approval:

```
Author --> Peer Review --> SME Review --> Quality Review --> 
  Legal/Compliance (if needed) --> Approver Sign-Off --> Publish
```

| Traditional | Structured (Our Framework) |
|---|---|
| One person's opinion | Multiple roles, each with defined scope |
| No record of review | Git history = immutable audit trail |
| "Looks fine" feedback | Checklist-driven review with explicit sign-off |
| Changes lost in email | Every change tracked in pull request |
| Can't prove review happened | Git log proves who reviewed what and when |

---

# Regulatory Requirements for Document Approval

| Standard | Requirement | How Our Workflow Satisfies It |
|---|---|---|
| **ISO 9001:2015, 7.5.3** | Document review and approval before release | PR approval = documented sign-off |
| **ISO 12100** | Safety info reviewed by competent person | SME review stage mandatory for hazard topics |
| **ISO 27001** | Information classification review | Permissions and classification checked at Quality Review |
| **GDPR / EU MDR** | Data protection in technical docs | Legal/Compliance review for customer-facing docs |
| **FDA 21 CFR Part 11** | Electronic signatures and audit trail | Git GPG-signed commits + PR approvals |

> Our Git-based workflow produces an immutable, cryptographically-verifiable audit trail. Auditors accept it as evidence.

---

<!-- _class: divider -->

# Part 2: The 10-Step Approval Workflow Overview

---

# The Complete Pipeline

```
STEP 1     STEP 2      STEP 3      STEP 4       STEP 5
Author --> Draft   --> Self-   --> Create  --> Assign
creates    saved      Review     Feature     Reviewers
topic      to repo    pass       Branch

STEP 6     STEP 7      STEP 8      STEP 9       STEP 10
Peer     --> SME     --> Quality --> Approver --> Merge &
Review     Review      Review      Sign-Off    Publish
```

Each step has:
- A defined **owner** (who performs it)
- A defined **checklist** (what must be verified)
- An **SLA** (how long it should take)
- A **gate** (cannot proceed until completed)

---

# Step-by-Step with Roles and SLAs

| Step | Action | Owner | SLA |
|---|---|---|---|
| 1. Author | Write topic using template | Technical Writer | Per content plan |
| 2. Draft Saved | Commit to feature branch, push | Author | Immediate after writing |
| 3. Self-Review | Run validation, check against checklist | Author | 1 business day |
| 4. Feature Branch | Create branch: `feature/topic-XXX` | Author | Immediate |
| 5. Assign Reviewers | Set reviewers in PR description | Author | Immediate |
| 6. Peer Review | Technical writing quality check | Peer Writer | 2 business days |
| 7. SME Review | Technical accuracy check | Subject Matter Expert | 3 business days |
| 8. Quality Review | Standards, metadata, framework compliance | Quality Lead | 2 business days |
| 9. Approver Sign-Off | Final approval to publish | Doc Manager | 1 business day |
| 10. Merge & Publish | Merge to main, CI/CD builds output | Author / CI/CD | Automated |

> Total SLA from draft to publish: ~9 business days. Rush track (escalated): ~3-4 business days.

---

# Workflow Visualized: The Topic's Journey

```
                          Topic Lifecycle

  [Draft]           [Review]          [Approved]        [Published]
     |                  |                  |                  |
     v                  v                  v                  v
  Author           Peer/SME/         Approver           CI/CD
  writes            Quality           signs             builds
  topic             review            off                &
                                                     deploys
     |                  |                  |
     +--------+---------+---------+--------+
              |         |         |
           Comments   Revisions  Re-review
           tracked    applied    if needed
           
  Status: draft  ->  review  ->  approved  ->  published
  othermeta:    "draft"     "review"    "approved"   "published"
```

> The `document-status` othermeta field tracks this lifecycle. The CI/CD pipeline reads it and controls what gets published.

---

<!-- _class: divider -->

# Part 3: The Authoring Stage (Steps 1-2)

---

# Step 1: Author Creates the Topic

The author (technical writer) creates a new topic following the framework process:

1. Select the correct template (T1-T6)
2. Write the content following DITA guidelines
3. Add complete prolog metadata
4. Save with correct file name convention
5. Place in the correct directory structure

```xml
<!-- Author stage: othermeta shows "draft" -->
<othermeta name="document-status" content="draft"/>
```

**Author's pre-submission checklist:**
- [ ] Template is correct (Concept, Task, Reference, etc.)
- [ ] All required prolog fields populated (M1-M7 rules pass)
- [ ] Spelling and grammar checked
- [ ] File name follows convention
- [ ] Content is complete (no TODO markers or placeholder text)

---

# Step 2: Draft Saved to Repository

The author commits the new topic to a feature branch:

```bash
# Create and switch to feature branch
git checkout -b feature/dr5000-safety-interlock-concept

# Stage and commit the new topic
git add topics/concepts/dr5000-safety-interlock.dita
git commit -m "feat: Add safety interlock concept topic for MC-5000

- New concept topic explaining the three-layer safety architecture
- Includes prolog with all M1-M7 metadata
- References DS-2026-0142 engineering spec

Co-Authored-By: Author Name <author@mfg-enterprise.com>"

# Push to remote
git push origin feature/dr5000-safety-interlock-concept
```

> Commit messages are part of the audit trail. Use the conventional commits format: `feat:` for new topics, `fix:` for corrections, `docs:` for documentation.

---

# Step 3: Author Self-Review

Before requesting peer review, the author performs a structured self-review:

| Self-Review Item | Check |
|---|---|
| **Validation** | `dita --validate` passes with no errors |
| **M1-M7 Rules** | All metadata validation rules pass |
| **Content Completeness** | Shortdesc, body content, no TODOs |
| **Template Conformance** | Matches T1-T6 template structure |
| **Link Check** | All xrefs, conrefs resolve correctly |
| **Grammar** | Run through grammar checker |
| **File Name** | Matches naming convention |
| **Directory** | Placed in correct product/domain directory |

> Self-review catches 40-50% of issues before anyone else spends time on the topic. It is a required step, not optional.

---

<!-- _class: divider -->

# Part 4: Git-Based Review Process (Steps 4-5)

---

# Step 4: Create Feature Branch

Branch naming follows a strict convention for traceability:

```
Branch Pattern:  <type>/<product>-<domain>-<description>

Examples:
  feature/dr5000-safety-interlock-concept
  fix/dr8000-coolant-filter-torque
  update/dr5000-spindle-warmup-v2.1
  review/archive-dr5000-deprecated-alarm-7010
```

| Prefix | Meaning |
|---|---|
| `feature/` | New topic, map, or major addition |
| `fix/` | Content correction or bug fix |
| `update/` | Modification to existing published topic |
| `review/` | Periodic review cycle update |
| `archive/` | Superseded topic archival |

> One branch per topic (or small set of related topics). Never combine unrelated changes in one branch.

---

# Step 5: Create Pull Request and Assign Reviewers

The pull request is the formal review request. Use the PR template:

```markdown
## Topic(s) in this PR
- `topics/concepts/dr5000-safety-interlock.dita` (new)
- `maps/dr5000/submaps/safety-module.ditamap` (updated)

## Type
- [x] New topic
- [ ] Update to existing topic
- [ ] Map change only
- [ ] Metadata update only

## Review Checklist (Author)
- [x] dita --validate passes
- [x] M1-M7 metadata rules pass
- [x] Self-review complete
- [x] Content matches engineering spec DS-2026-0142

## Reviewers Assigned
- Peer Reviewer: @maria-schmidt
- SME Reviewer: @dr-wagner (Safety Engineering)
- Quality Lead: @quality-team

## Labels
`dr5000` `safety` `concept` `new-topic`
```

---

# Pull Request Labels

Labels categorize PRs for filtering and reporting:

| Label | Meaning | Color |
|---|---|---|
| `dr5000` | MC-5000 product content | Blue |
| `dr8000` | MC-8000 product content | Green |
| `concept` | Concept topic type | Yellow |
| `task` | Task topic type | Orange |
| `reference` | Reference topic type | Purple |
| `troubleshooting` | Troubleshooting topic | Red |
| `new-topic` | Brand new content | Light green |
| `update` | Modification to existing | Light blue |
| `urgent` | Safety or production-critical | Red |
| `translation-ready` | Approved for localization | Pink |

> Labels enable dashboard filtering: "Show me all urgent safety PRs for MC-5000" is one click.

---

# PR Milestones

Milestones group PRs into release batches:

```
Milestone Examples:
  v2.1-operator-manual     -- 45 PRs, due July 15
  v2.1-service-manual      -- 32 PRs, due August 1
  alarm-codes-q3-update    -- 12 PRs, due September 30
  safety-annual-review     -- 8 PRs, due October 15
```

```markdown
## Milestone
`v2.1-operator-manual` (due 2026-07-15)
```

> Milestones link documentation releases to product releases. When the MC-5000 v2.1 ships, you know exactly which documentation PRs are in it.

---

<!-- _class: divider -->

# Part 5: Review Roles and Responsibilities

---

# Review Role 1: Peer Reviewer

**Who**: Another technical writer on the documentation team.

**What they check**:

| Area | Specific Check |
|---|---|
| **DITA Structure** | Elements used correctly for the topic type |
| **Metadata** | All prolog fields populated; M1-M7 valid |
| **Clarity** | Content is clear for the target audience |
| **Consistency** | Terminology matches the project glossary |
| **Templates** | Matches T1-T6 template structure |
| **Naming** | File and ID naming conventions followed |
| **Links** | All references resolve correctly |

**SLA**: 2 business days
**Action**: Approve or request changes (with inline comments)

---

# Review Role 2: Subject Matter Expert (SME)

**Who**: An engineer or domain expert who understands the technical content.

**What they check**:

| Area | Specific Check |
|---|---|
| **Technical Accuracy** | Facts, numbers, specs are correct |
| **Procedures** | Steps are in correct order; nothing missing |
| **Safety** | Hazard warnings are in the right places |
| **Completeness** | Covers all necessary information |
| **Source Alignment** | Content matches engineering specs / standards |
| **Translation Readiness** | Content is culturally neutral, unambiguous |

**SLA**: 3 business days
**Action**: Approve or request changes (with technical corrections)

---

# Review Role 3: Quality Lead

**Who**: Documentation quality assurance specialist.

**What they check**:

| Area | Specific Check |
|---|---|
| **Framework Compliance** | All mandatory fields present |
| **Validation** | `dita --validate` passes clean |
| **Subject Scheme** | Controlled values match approved lists |
| **Metadata Quality** | Scorecard >= 90 points (from Module 05) |
| **Audience Correctness** | Topic targets the right role |
| **Permissions** | M5 rule: no danger/warning topics public |
| **Cross-References** | No broken links or circular references |

**SLA**: 2 business days
**Action**: Approve or request changes

---

# Review Role 4: Approver

**Who**: Documentation Manager or designated content owner.

**What they check**:

| Area | Specific Check |
|---|---|
| **All Reviews Complete** | Peer, SME, Quality have all approved |
| **Comments Resolved** | No unresolved review comments |
| **Document Status** | `othermeta` set to "approved" |
| **Release Readiness** | Topic aligns with milestone/release plan |
| **Final Sanity Check** | Content is publication-ready |

**SLA**: 1 business day
**Action**: Final approval -- merge to main

> The approver is the **last line of defense**. They do not re-review content -- they verify the process was followed.

---

# Review Roles Summary Table

| Role | Who | Checks | SLA | Can Merge? |
|---|---|---|---|---|
| **Peer Reviewer** | Fellow technical writer | DITA structure, clarity, consistency | 2 days | No |
| **SME Reviewer** | Engineer / domain expert | Technical accuracy, completeness | 3 days | No |
| **Quality Lead** | QA specialist | Framework compliance, metadata | 2 days | No |
| **Approver** | Doc Manager | Process compliance, final sign-off | 1 day | Yes |

> Minimum required approvals before merge: Peer Reviewer + SME Reviewer + Quality Lead + Approver = 4 approvals. Urgent safety topics can skip Quality with Approver override.

---

<!-- _class: divider -->

# Part 6: Review, Comments, and Resolution

---

# Pull Request Review Workflow

```
1. Author creates PR, assigns reviewers
            |
            v
2. Reviewer reviews diff in GitHub/GitLab
            |
            +-- APPROVE --> move to next reviewer
            |
            +-- REQUEST CHANGES --> 
            |       |
            |       v
            |   3a. Reviewer adds inline comments
            |       |
            |       v
            |   3b. Author addresses comments
            |       |
            |       v
            |   3c. Author pushes updates
            |       |
            |       v
            |   3d. Reviewer re-reviews
            |       |
            |       +-- APPROVE --> move to next
            |       +-- REQUEST CHANGES --> back to 3a
            |
            v
4. All reviewers approved --> Approver merges
```

---

# Comment Types and Conventions

| Comment Type | Prefix | Example | Action Required |
|---|---|---|---|
| **Must-Fix** | `[blocking]` | `[blocking] Missing hazard warning` | Must resolve before approval |
| **Suggestion** | `[suggestion]` | `[suggestion] Consider active voice` | Address or acknowledge |
| **Question** | `[question]` | `[question] Is this torque spec for BT40 or HSK?` | Answer required |
| **Praise** | `[nice]` | `[nice] Great explanation of interlocks` | None (acknowledge) |
| **Meta Note** | `[note]` | `[note] This aligns with the Q3 style guide update` | For awareness |

```markdown
## PR Comment Example

[blocking] The `<shortdesc>` should stand alone and cannot 
contain `<ph>` elements. Replace with plain text.

[suggestion] Consider splitting this section into a 
separate concept topic -- it covers three distinct ideas.

[question] The source field says DS-2026-0142, but I only 
see DS-2026-0142-A in the document register. Which is correct?
```

---

# Comment Resolution Tracking

Every comment must be resolved before approve can merge:

```
PR #247: MC-5000 Safety Interlock Concept

Comments: 7 total

Peer Review (Maria Schmidt):
  [x] [blocking] Missing <audience> element in prolog     -- RESOLVED
  [x] [suggestion] Use active voice in shortdesc           -- RESOLVED
  [x] [question] Confirm file name convention              -- RESOLVED
  [ ] [suggestion] Add a diagram reference                 -- WON'T FIX (no diagram available)

SME Review (Dr. Wagner):
  [x] [blocking] Interlock response time should be 150ms   -- RESOLVED
  [x] [question] Source doc version?                       -- RESOLVED

Quality Review:
  [x] [blocking] Run metadata scorecard check              -- RESOLVED (score: 95)

Resolution status: 6/7 resolved, 1 wont-fix (acknowledged)
```

> A comment marked "wont-fix" must include a justification. The approver reviews all wont-fix items before merge.

---

# Re-Review Cycle

When an author pushes changes after a review:

1. Reviewer receives notification: "New commits pushed after your review"
2. Reviewer checks only the changed lines (GitHub shows "changes since your last review")
3. Unchanged portions are hidden by default
4. Reviewer re-approves or adds new comments
5. Loop continues until all reviewers approve

```bash
# Author addresses review comments
git add topics/concepts/dr5000-safety-interlock.dita
git commit -m "fix: Address peer review comments

- Add missing <audience> element (M7)
- Rewrite shortdesc in active voice  
- Update interlock response time to 150ms per SME

Reviewed-by: Maria Schmidt"

git push origin feature/dr5000-safety-interlock-concept
```

> Each fix commit should reference the reviewer. The audit trail shows who found the issue and who fixed it.

---

# Conflict Resolution

When reviewers disagree:

| Conflict | Resolution Process |
|---|---|
| **Peer vs SME on terminology** | SME wins (technical authority) |
| **Peer vs Quality on style** | Quality wins (standards authority) |
| **SME vs Quality on metadata** | Quality wins unless SME provides regulatory justification |
| **Two SMEs disagree** | Escalate to Engineering Manager |
| **Author vs any reviewer** | Reviewer wins (review is a gate, not a suggestion) |
| **Deadlock** | Documentation Manager decides |

> The escalation path is defined. Never let a review stall because reviewers disagree -- escalate immediately.

---

<!-- _class: divider -->

# Part 7: The Publish Stage (Steps 9-10)

---

# Step 9: Approver Sign-Off

The approver performs the final checks:

```
Approver Checklist:
  [ ] All required reviewers have approved
  [ ] All [blocking] comments resolved
  [ ] All [question] comments answered
  [ ] Wont-fix items have justification
  [ ] document-status is "approved" in othermeta
  [ ] Branch is up-to-date with main (no merge conflicts)
  [ ] CI/CD checks are green (validation passes)
  [ ] Milestone is correct
  [ ] Labels are accurate
```

> The approver's merge is the electronic equivalent of a signed approval form for ISO 9001.

---

# Step 10: Merge and Publish

After approval, merge triggers the CI/CD pipeline:

```yaml
# .github/workflows/publish.yml
on:
  push:
    branches: [main]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - name: Validate all topics
        run: dita --validate --all
  
  build:
    needs: validate
    runs-on: ubuntu-latest
    steps:
      - name: Build operator manual (PDF)
        run: dita -i maps/dr5000-operator-manual.ditamap -f pdf
      
      - name: Build operator manual (HTML5)
        run: dita -i maps/dr5000-operator-manual.ditamap -f html5
      
      - name: Update document-status to "published"
        run: python scripts/update-status.py --status published
      
      - name: Deploy to documentation portal
        run: python scripts/deploy.py --env production
```

---

# Merge Strategies

| Strategy | When to Use | Effect |
|---|---|---|
| **Merge Commit** | Standard topics | Preserves full history; creates merge commit |
| **Squash and Merge** | Single-topic PRs with many fix commits | Clean history; one commit on main |
| **Rebase and Merge** | Linear history preference | No merge commit; commits replayed on main |

Framework default: **Squash and Merge** for standard topics, **Merge Commit** for map changes.

```
Squash and Merge example:
  feature/dr5000-safety-interlock-concept (5 commits)
    --> squashed to:
  main: "feat: Add MC-5000 safety interlock concept topic"
```

> Squash and Merge keeps the main branch history clean while preserving the full PR discussion.

---

# Post-Publish: Status Update

After CI/CD publishes, the topic's status is automatically updated:

```xml
<!-- Before publish -->
<othermeta name="document-status" content="approved"/>

<!-- After CI/CD publish (script updates this) -->
<othermeta name="document-status" content="published"/>
```

The publish event is logged:

```
Publish Log:
  Topic: DR5K-SAF-001
  Action: Published to production
  Date: 2026-06-07 14:32 UTC
  Trigger: Merge of PR #247
  Outputs: PDF (v2.1), HTML5 (docs.mfg-enterprise.com)
  Published by: CI/CD (merge by doc-manager@mfg-enterprise.com)
```

> Every publish is traceable. The `published` timestamp in git is immutable evidence for ISO auditors.

---

<!-- _class: divider -->

# Part 8: Special Workflow Scenarios

---

# Scenario 1: Urgent Safety Update

When a safety topic needs immediate correction:

```
Normal Workflow (9 days):
  Author -> Self-Review -> PR -> Peer(2d) -> SME(3d) -> Quality(2d) -> Approver(1d) -> Merge

Rush Track (3-4 days):
  Author -> Self-Review -> PR (URGENT label) -> SME(1d) -> Approver(1d) -> Merge
                                                       |
                                                  Quality skipped
                                                  (Approver override)
```

**Rush track rules:**
- Only for safety issues or production-halting errors
- Must have `urgent` label and `safety` label
- Quality Review can be waived by Approver with justification
- Post-merge retrospective required: review why the urgency occurred

> Rush track is an exception, not the norm. Abusing it triggers a process audit.

---

# Scenario 2: Translation Workflow

When a topic is ready for localization:

```
Author (English) -> Review -> Approved -> Published (English)
                                              |
                                              v
                                        Translation Request
                                        (translation-ready label)
                                              |
                                              v
                                        Translation Vendor
                                        (LinguaTech GmbH)
                                              |
                                              v
                                        Translated Topics
                                        (de, zh, ja, fr...)
                                              |
                                              v
                                        Translation Review
                                        (In-country reviewer)
                                              |
                                              v
                                        Published (multilingual)
```

**Translation metadata updated:**
```xml
<othermeta name="translatable" content="yes"/>
<othermeta name="source-language" content="en"/>
<othermeta name="translation-status" content="in-progress"/>
```

> The translation workflow is covered in detail in Professional Module 05.

---

# Scenario 3: Periodic Review Cycle

Every topic must be reviewed annually (or per its `review-cycle` setting):

```
12 months after publish:
  CI/CD flags topic for review
         |
         v
  Author opens review PR:
    branch: review/dr5000-safety-interlock-concept
         |
         v
  Review process:
    - Verifies content is still accurate
    - Updates copyright year if needed
    - Updates <revised> date
    - Checks engineering specs for changes
         |
         v
  Outcome:
    A) No changes needed --> update <revised>, merge
    B) Minor changes --> fix, review, merge
    C) Major changes --> new feature branch, full review
```

> The `check-review-cycle.py` script runs weekly and opens automated PRs for topics due for review.

---

<!-- _class: divider -->

# Part 9: Approval Workflow Governance

---

# SLAs and Escalation

| SLA Breach | Escalation | Action |
|---|---|---|
| **Peer Review > 2 days** | Team lead notified | Reassign reviewer or escalate priority |
| **SME Review > 3 days** | Engineering manager notified | SME to prioritize or delegate |
| **Quality Review > 2 days** | Quality lead notified | Add reviewer or escalate |
| **Approver > 1 day** | Doc manager backup notified | Backup approver steps in |
| **Total > 12 days** | Full escalation to Director | Root cause analysis required |

> SLA tracking is automated. A dashboard shows all open PRs with time-in-status. Amber at 80% of SLA, red at 100%.

---

# Workflow Metrics Dashboard

The documentation team tracks these metrics weekly:

```
Weekly Approval Workflow Report
================================
PRs created: 24
PRs merged: 19
PRs in review: 31
PRs blocked: 3

Average time to merge: 7.2 days (target: 9 days)  [ON TRACK]
Average review cycles: 1.8 (target: < 2)          [ON TRACK]
Urgent PRs merged: 2 (in SLA: 2/2)                [PASS]
Review SLA compliance: 94% (target: > 90%)         [PASS]

Blocked PRs:
  #312 - MC-8000 spindle specs - waiting on SME (5 days)
  #318 - Coolant MSDS update - legal review pending (8 days)
  #324 - G-code macro reference - SME on vacation (3 days)
```

> If the average time to merge exceeds 9 days, the Documentation Manager reviews the process for bottlenecks.

---

# Audit Trail: What the Workflow Proves

| Auditor's Question | Evidence |
|---|---|
| "Who wrote this safety procedure?" | Git commit author |
| "Who reviewed it for technical accuracy?" | SME reviewer approval in PR |
| "When was it approved?" | Approver merge timestamp |
| "What changes were made after review?" | Commit history in feature branch |
| "Were all required reviews completed?" | PR approvals list |
| "Who decided to skip quality review?" | Approver comment with justification |
| "Can you prove this is the current version?" | Git log on main branch + `published` status |

> The Git-based workflow produces an immutable, timestamped, cryptographically-signed audit trail. This satisfies ISO 9001, ISO 12100, and FDA 21 CFR Part 11 requirements.

---

<!-- _class: divider -->

# Part 10: Exercise -- Simulate a PR Review Workflow

---

<!-- _class: exercise -->

# Exercise: Complete a Pull Request Review (60 min)

You are the technical writer assigned to review a peer's new topic. Simulate the full review workflow.

**Scenario:**
- Topic: `MC-5000 Coolant System Overview` (concept topic)
- Author: Your peer has submitted PR #312
- You are the **Peer Reviewer**
- The topic has some issues that need to be caught

---

# Exercise: The Topic to Review

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">
<concept id="coolant-overview">
  <title>Coolant System</title>
  <shortdesc>This topic describes the coolant system.</shortdesc>
  <conbody>
    <p>The MC-5000 coolant system uses a high-pressure pump to 
    deliver coolant to the cutting zone. The system consists of 
    a tank, pump, filtration unit, and distribution nozzles.</p>
    <section>
      <title>Components</title>
      <ul>
        <li>Coolant tank (200L capacity)</li>
        <li>High-pressure pump (max 70 bar)</li>
        <li>Filtration system (25 micron)</li>
        <li>Programmable coolant nozzles (x4)</li>
      </ul>
    </section>
    <section>
      <title>Daily Check</title>
      <p>Inspect coolant level and top up with CoolMix-2000 
      concentrate at 5% mixture.</p>
    </section>
  </conbody>
</concept>
```

> Read this topic carefully. Use the Peer Review checklist to find issues.

---

# Exercise: Review Tasks

1. **Identify issues** using the Peer Review checklist (below)
2. **Classify each issue**: [blocking], [suggestion], or [question]
3. **Write review comments** for each issue
4. **Determine**: Approve or Request Changes?
5. **Write the review summary**

**Peer Review Checklist:**
- [ ] Metadata: Prolog present with author, dates, status, audience?
- [ ] Shortdesc: Standalone sentence that explains the topic?
- [ ] Title: Specific and meaningful?
- [ ] ID: Matches naming convention?
- [ ] Content: Complete, clear, correct topic type?
- [ ] Safety: Hazard warning needed for coolant chemicals?
- [ ] Elements: Correct DITA elements used?

---

<!-- _class: exercise -->

# Exercise: Review Findings (Issues to Catch)

Here are the issues you should have found as Peer Reviewer:

| # | Issue | Type | Line |
|---|---|---|---|
| 1 | **No prolog at all**. Missing author, dates, audience, status, all metadata. M1-M7 all fail. | [blocking] | After `<shortdesc>` |
| 2 | **Shortdesc is vague**: "This topic describes the coolant system." Should be a summary. | [blocking] | Line 6 |
| 3 | **Title too generic**: "Coolant System" -- should be "MC-5000 Coolant System Overview" | [suggestion] | Line 5 |
| 4 | **Missing safety warning**: Coolant chemicals require PPE. No hazard statement. | [blocking] | Before component list |
| 5 | **Mixing topic types**: "Daily Check" section is a task in a concept topic. Should be separate task topic. | [blocking] | Line 18-21 |
| 6 | **ID doesn't follow convention**: `coolant-overview` should be `concept-dr5000-coolant-overview` per naming standard. | [suggestion] | Line 4 |
| 7 | **Missing `xml:lang`** attribute on root element. | [blocking] | Line 4 |

> Score: 4 [blocking], 3 [suggestion]. Verdict: **Request Changes**.

---

# Exercise: Write the Review Summary

```markdown
## Peer Review: MC-5000 Coolant System Overview (PR #312)

**Reviewer**: [Your Name]
**Date**: 2026-06-07
**Verdict**: REQUEST CHANGES

### Summary
This topic needs significant work before it can proceed to SME review. 
The critical issues are missing metadata, an inadequate shortdesc, and 
a missing safety warning for coolant chemicals. Additionally, the "Daily 
Check" section should be extracted into its own task topic.

### Blocking Issues (must fix)
1. **Missing prolog**: The entire `<prolog>` block is absent. This means 
   all M1-M7 validation rules fail. Add a complete prolog from template 
   T4 (Concept metadata). [M1-M7 violation]

2. **Inadequate shortdesc**: "This topic describes the coolant system" 
   tells the reader nothing. Rewrite: "The MC-5000 coolant system delivers 
   high-pressure coolant to the cutting zone through a programmable 
   four-nozzle distribution system with 25-micron filtration."

3. **Missing safety warning**: Coolant chemicals require a hazard 
   statement. Add `<hazardstatement type="warning">` before the 
   components list to address chemical handling and PPE requirements.

4. **Task content in a concept**: The "Daily Check" section belongs 
   in a separate task topic. Extract it to 
   `topics/tasks/dr5000-coolant-daily-check.dita`.

### Suggestions (improve, not required)
5. Title should explicitly mention MC-5000: "MC-5000 Coolant System Overview"
6. ID should follow convention: `concept-dr5000-coolant-overview`
7. Add `xml:lang="en"` to the root element

### Next Steps
Address all blocking items and push updated commits. I will re-review.
```

---

# Exercise: Self-Check

Compare your review to the expected findings:

- [ ] Found at least 4 of the 7 issues
- [ ] Classified each as [blocking] or [suggestion] correctly
- [ ] Wrote constructive, actionable review comments
- [ ] Provided specific fix instructions (not just "fix this")
- [ ] Wrote a clear review summary with verdict
- [ ] Used the review comment prefixes consistently

> If you found 5+ issues and wrote clear comments, you are ready for real peer reviews.

---

<!-- _class: divider -->

# Part 11: Summary and Key Takeaways

---

# Module 07 Summary

**Approval Workflow: What we covered**

1. Structured approval prevents documentation errors that cause safety incidents
2. The 10-step workflow: Author --> Self-Review --> Feature Branch --> Assign Reviewers --> Peer Review --> SME Review --> Quality Review --> Approver Sign-Off --> Merge & Publish
3. Git-based review via branches, pull requests, and labels
4. Four review roles: Peer Reviewer, SME, Quality Lead, Approver -- each with distinct scope
5. Comments use [blocking], [suggestion], [question], [nice], [note] prefixes
6. All comments must be resolved before the approver can merge
7. CI/CD pipeline validates and publishes automatically on merge
8. Rush track exists for urgent safety updates (3-4 days instead of 9)
9. The workflow produces an immutable, auditable trail that satisfies ISO requirements

---

# Key Takeaways

1. **Review is not optional** -- Four approvals minimum. Every topic passes through Peer, SME, Quality, and Approver.

2. **Git is your audit trail** -- Every commit, comment, and approval is timestamped and immutable. Auditors accept this as evidence.

3. **Self-review first** -- The author catches 40-50% of issues before anyone else spends time. This is required, not optional.

4. **Comments must be actionable** -- "Fix this" is not helpful. Provide the exact fix and why it matters.

5. **SLAs drive accountability** -- Automated tracking and escalations prevent reviews from going stale.

6. **Merge = Publish** -- Once merged to main, CI/CD builds and deploys automatically. The approver's merge IS the publish command.

> Next Module: 08 -- Hands-On Capstone Project

---

<!-- _class: note -->

# Before Module 08

**Preparation checklist:**

- [ ] Review a real PR in your team's repository -- read the comments and resolution flow
- [ ] Memorize the four review roles and their scope
- [ ] Practice writing review comments in the [blocking]/[suggestion]/[question] format
- [ ] Review the PR template your team uses
- [ ] Check the workflow metrics dashboard (if available) to understand current SLAs
- [ ] Ensure you have push access to the repository for the capstone project

---

<!-- _class: divider -->

# Module 07 Complete

**Next Module: 08 -- Hands-On Capstone Project**

DITA Manufacturing Framework v1.1
Manufacturing Enterprise -- Documentation Governance Committee
