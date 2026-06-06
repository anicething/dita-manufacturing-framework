# DITA Templates - Ready to Use

This folder contains ready-to-use DITA templates for different content types.

## Files in This Directory

1. **01-CONCEPT-TOPIC-TEMPLATE.dita** - For explanatory content
2. **02-TASK-TOPIC-TEMPLATE.dita** - For procedures
3. **03-REFERENCE-TOPIC-TEMPLATE.dita** - For specifications
4. **04-METADATA-TEMPLATE.xml** - Metadata boilerplate
5. **05-DITAMAP-TEMPLATE.ditamap** - Map structure

## How to Use

### Quick Start

1. Copy template file to your project
2. Replace "TEMPLATE" with your topic ID
3. Add your content
4. Validate XML
5. Submit for review

### Example

```bash
# Copy concept template
cp 01-CONCEPT-TOPIC-TEMPLATE.dita ../product-a/topics/c_my_feature.dita

# Edit file
oXygen ../product-a/topics/c_my_feature.dita

# Add your content, then submit
git add ../product-a/topics/c_my_feature.dita
git commit -m "Add: My Feature concept topic"
```

## Template Quick Reference

| Template | Use For | File Size | Est. Time |
|----------|---------|-----------|----------|
| Concept | Explanations | 1-3 pages | 2-4 hours |
| Task | Procedures | 1-5 pages | 2-6 hours |
| Reference | Specifications | 1-3 pages | 1-3 hours |

---

**All templates include:**
- ✅ Proper DITA structure
- ✅ Required metadata fields
- ✅ Comments with guidance
- ✅ Manufacturing-specific examples
- ✅ ISO compliance built-in
