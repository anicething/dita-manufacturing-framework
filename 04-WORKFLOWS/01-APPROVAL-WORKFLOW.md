# Approval Workflow & Process

**Document ID:** WF-001  
**Version:** 1.0  
**Last Updated:** 2026-06-06  
**Status:** Approved  

---

## 1. 10-Step Approval Process

```
1. PLAN
   ↓ Writer creates work item
   ↓
2. DRAFT
   ↓ Writer creates topic in Git feature branch
   ↓
3. SELF-REVIEW
   ↓ Writer runs quality checklist
   ↓
4. SME REVIEW (2 business days)
   ↓ Subject Matter Expert verifies technical accuracy
   ↓
5. COMPLIANCE CHECK (1 business day)
   ↓ Compliance officer reviews for ISO/legal requirements
   ↓
6. EDITORIAL REVIEW (1 business day)
   ↓ Architect/Senior Writer reviews structure & style
   ↓
7. FINAL APPROVAL (1 business day)
   ↓ Product Manager approves for publication
   ↓
8. PUBLISH
   ↓ Auto-merge to main branch, triggers CI/CD
   ↓
9. DISTRIBUTE
   ↓ Multi-channel publishing (Web, PDF, Mobile)
   ↓
10. LOCALIZE
    ↓ Automated extraction for translation
    ↓ Complete
```

**Total Time to Publish: < 8 business days**

---

## 2. Git Workflow

### 2.1 Branch Strategy

**Main Branch:** Production-ready content

**Feature Branches:** `feature/{product}/{topic-id}`

```bash
# Create feature branch
git checkout -b feature/product-a/c-overview

# Work on content
git add topics/c_overview.dita
git commit -m "Draft: Product A Overview topic"

# Push to remote
git push origin feature/product-a/c-overview

# Create Pull Request (for review)
```

### 2.2 Pull Request Template

```markdown
## Topic Information
- **Document ID:** [e.g., DOC-PROD-001-v1.0]
- **Topic ID:** [e.g., c_overview]
- **Product:** [Product A/B/C]
- **Audience:** [admin/user/developer/executive]
- **Status:** [Draft/Ready for Review]

## Changes Summary
- Brief description of what this topic covers
- Why it's needed
- Any related topics

## Checklist
- [ ] Metadata complete
- [ ] No spelling errors
- [ ] No broken links
- [ ] Images optimized and alt-text included
- [ ] Follows style guide
- [ ] Complies with ISO requirements
- [ ] Ready for SME review

## Reviewers Needed
- [ ] @subject-matter-expert
- [ ] @compliance-officer
- [ ] @architect
- [ ] @product-manager
```

---

## 3. Review Stages

### Stage 1: Self-Review (Writer)

**Writer checks:**
- [ ] Metadata complete
- [ ] No spelling errors
- [ ] Grammar correct
- [ ] No broken links
- [ ] Images present and optimized
- [ ] Topic structure correct
- [ ] Follows style guide
- [ ] No sensitive information
- [ ] Ready for peer review

### Stage 2: SME Review (2 business days)

**Subject Matter Expert verifies:**
- [ ] Technical accuracy
- [ ] Completeness of information
- [ ] Correct terminology
- [ ] No conflicting information
- [ ] Examples are correct
- [ ] Prerequisites accurate
- [ ] Expected results accurate

**Feedback Options:**
- ✅ Approved
- ✅ Approved with suggestions
- ⚠️ Changes required
- ❌ Rejected

### Stage 3: Compliance Review (1 business day)

**Compliance Officer checks:**
- [ ] Meets ISO 27001 requirements
- [ ] Meets ISO 9001 requirements
- [ ] Meets ISO 14001 requirements
- [ ] No confidential information leaked
- [ ] Legal disclaimers present (if needed)
- [ ] Safety warnings appropriate
- [ ] Privacy requirements met
- [ ] Document traceability complete

### Stage 4: Editorial Review (1 business day)

**Architect/Senior Writer reviews:**
- [ ] Structure matches topic type template
- [ ] Metadata consistent with standards
- [ ] Style guide compliance
- [ ] Consistent terminology
- [ ] Reuse opportunities identified
- [ ] Cross-references appropriate
- [ ] Localization-friendly writing
- [ ] Quality level meets standards

### Stage 5: Final Approval (1 business day)

**Product Manager approves:**
- [ ] Aligns with product strategy
- [ ] Meets customer needs
- [ ] Timing appropriate for release
- [ ] Business requirements met
- [ ] Quality acceptable

**Final Approval:**
- ✅ Approved - Ready to publish
- ⏸️ Hold for later release
- ❌ Cannot approve

---

## 4. Escalation Process

If review is blocked:

```
Review overdue?
  ↓
1. Send reminder email to reviewer (1 business day late)
  ↓
2. Escalate to reviewer's manager (2 business days late)
  ↓
3. Escalate to Director of Tech Comms (3 business days late)
  ↓
4. DGC resolves at monthly meeting (4+ days late)
```

---

## 5. Publishing SLA

**After Final Approval:**

| Step | Time | Owner |
|------|------|-------|
| Auto-merge to main | < 5 min | GitHub |
| Trigger CI/CD pipeline | < 1 min | GitHub Actions |
| HTML5 generation | < 15 min | DITA-OT |
| PDF generation | < 20 min | DITA-OT |
| Mobile generation | < 10 min | DITA-OT |
| Upload to web portal | < 5 min | CI/CD |
| Search indexing | < 10 min | Elasticsearch |
| **Total Time to Live** | **< 1 hour** | **Automated** |

---

**Document ID:** WF-001  
**Next Review Date:** Q3 2026  
