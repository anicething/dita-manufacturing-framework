# Content Architecture & Design

**Document ID:** ARCH-001  
**Version:** 1.0  
**Last Updated:** 2026-06-06  
**Status:** Approved  

---

## 1. Information Architecture Overview

### 1.1 High-Level Structure

```
Corporate Documentation Portal
├── Shared/Common Content (30% of topics)
│   ├── Company Information
│   ├── Product Families
│   ├── Safety & Compliance
│   ├── Environmental Standards
│   ├── Shared Terminology
│   └── Common Procedures
│
├── Product Lines (70% of topics)
│   ├── Product A Documentation
│   │   ├── Getting Started
│   │   ├── Administrator Guide
│   │   ├── User Guide
│   │   ├── Technical Reference
│   │   ├── API Documentation
│   │   ├── Troubleshooting
│   │   └── Release Notes
│   ├── Product B Documentation
│   └── Product C Documentation
│
└── Supporting Documentation
    ├── Release Notes (all products)
    ├── Known Issues
    ├── FAQ
    ├── Glossary
    ├── Legal/Compliance
    └── Contact/Support
```

### 1.2 Topic Distribution

**Recommended Distribution:** 40% Concept / 40% Task / 20% Reference

```
Content Type        Count    Purpose
─────────────────────────────────────────────────────────
Concept Topics      400      Explain features, architecture, concepts
Task Topics         400      Step-by-step procedures, how-to guides
Reference Topics    200      Command docs, APIs, configuration
─────────────────────────────────────────────────────────
TOTAL              1,000     topics

By Product:
Product A:          350 topics
Product B:          350 topics
Product C:          300 topics
Shared/Common:      200 topics (reused across all products)
```

## 2. Content Reuse Strategy

### 2.1 Reuse Patterns

#### Pattern 1: Shared Topics (30% of topics)

**Use Case:** Content identical across all products

```
Shared Topics:
├── Company Name & Logo References
├── Contact Information
├── Support Hours & Procedures
├── Legal Disclaimer
├── Warranty Information
├── Safety Warnings
├── Environmental Compliance
├── Certification Information
├── Glossary Terms
└── Product Family Overview
```

#### Pattern 2: Content Fragments (20% of topics)

**Use Case:** Partial reuse within topics (steps, warnings, examples)

```
Fragments:
├── Common Task Steps (setup, configuration, installation)
├── Warnings & Cautions (safety, data loss, security)
├── Support Information (contact, escalation paths)
├── Examples & Use Cases
└── Troubleshooting Steps (general)
```

#### Pattern 3: Product-Specific Topics (50% of topics)

**Use Case:** Unique content per product

```
Product-Specific:
├── Product Overview
├── Feature Documentation
├── Configuration Guides (product-specific)
├── API Reference
├── Troubleshooting (product-specific)
└── Release Notes
```

### 2.2 Content Ownership

**Shared Content:**
- Owner: DITA Architect
- Review: DGC monthly
- Update: As-needed
- Reuse Target: 100% across products

**Product-Specific Content:**
- Owner: Product Writer (per product)
- Review: Product Manager
- Update: Per product release
- Reuse Target: 35-40% within product

## 3. Metadata Schema

### 3.1 Required Metadata (All Topics)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE topic PUBLIC "-//OASIS//DTD DITA Topic//EN" "topic.dtd">
<topic id="example-topic">
  <title>Example Topic Title</title>
  <prolog>
    <metadata>
      <!-- Document Identification -->
      <data name="docid" value="DOC-PROD-001-v2.1"/>
      <data name="internal-id" value="example-topic"/>
      
      <!-- Classification -->
      <data name="product" value="Product A"/>
      <data name="product-version" value="2.1+"/>
      <data name="audience" value="admin|user|developer|executive"/>
      <data name="topic-type" value="concept|task|reference"/>
      
      <!-- Compliance & Status -->
      <data name="status" value="approved"/>
      <data name="security-level" value="public|internal|confidential"/>
      <data name="iso-relevant" value="yes|no"/>
      
      <!-- Review & Maintenance -->
      <data name="created-date" value="2026-01-15"/>
      <data name="revised-date" value="2026-06-06"/>
      <data name="author" value="John Smith"/>
      <data name="reviewed-by" value="Jane Doe"/>
      <data name="next-review-date" value="2026-09-06"/>
      <data name="review-frequency" value="quarterly|biannual|annual"/>
      
      <!-- Localization -->
      <data name="translatable" value="yes|no"/>
      <data name="translation-status" value="original|translated"/>
      <data name="language" value="en-US"/>
    </metadata>
  </prolog>
  
  <body>
    <!-- Topic content -->
  </body>
</topic>
```

### 3.2 Metadata Validation Rules

| Field | Required | Valid Values | Example |
|-------|----------|--------------|----------|
| docid | YES | `DOC-PROD-NNN-vX.Y` | DOC-PROD-001-v1.0 |
| product | YES | Product code | PROD-A, PROD-B |
| audience | YES | admin, user, developer, executive | admin |
| status | YES | draft, review, approved, obsolete | approved |
| created-date | YES | YYYY-MM-DD | 2026-01-15 |
| reviewed-by | YES | Person name | Jane Doe |
| security-level | YES | public, internal, confidential | internal |
| translatable | YES | yes, no | yes |

## 4. Topic Type Specifications

### 4.1 Concept Topic

**Purpose:** Explain "What is?" and "Why?"

**Structure:**
```
Title
├─ Short Description (2-3 sentences)
├─ Purpose/Background
├─ Key Concepts
├─ Diagrams/Illustrations
├─ Examples
└─ Related Topics
```

**Length:** 1-3 pages  
**Audience:** Everyone  
**Reusability:** High  

### 4.2 Task Topic

**Purpose:** Step-by-step "How to" procedures

**Structure:**
```
Title (Action Verb + Object)
├─ Short Description
├─ Purpose
├─ Prerequisites
├─ Steps (Numbered)
├─ Expected Result
├─ Tips/Troubleshooting
└─ Related Topics
```

**Length:** 1-5 pages  
**Audience:** Practitioners  
**Reusability:** Very High  

### 4.3 Reference Topic

**Purpose:** Look-up information (dictionary-style)

**Structure:**
```
Title
├─ Short Description
├─ Purpose
├─ Reference Data (tables, lists, specs)
├─ Examples
└─ Related References
```

**Length:** 1-3 pages  
**Audience:** Practitioners  
**Reusability:** Very High  

## 5. Map Organization

### 5.1 Master Map Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map>
  <title>Product A Documentation</title>
  <topicmeta>
    <author>Tech Comm Team</author>
    <publisher>Manufacturing Inc.</publisher>
    <created date="2026-01-15"/>
  </topicmeta>
  
  <topicref href="maps/c_getting_started.ditamap" navtitle="Getting Started"/>
  <topicref href="maps/c_admin_guide.ditamap" navtitle="Administrator Guide"/>
  <topicref href="maps/c_user_guide.ditamap" navtitle="User Guide"/>
  <topicref href="maps/c_reference.ditamap" navtitle="Technical Reference"/>
  <topicref href="maps/c_troubleshooting.ditamap" navtitle="Troubleshooting"/>
  <topicref href="topics/c_release_notes.dita" navtitle="Release Notes"/>
</map>
```

### 5.2 Bookmap for Print/PDF

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE bookmap PUBLIC "-//OASIS//DTD DITA BookMap//EN" "bookmap.dtd">
<bookmap>
  <booktitle>
    <mainbooktitle>Product A User Guide</mainbooktitle>
    <booktitlealt>Version 2.1</booktitlealt>
  </booktitle>
  
  <bookmeta>
    <author>Technical Communication Team</author>
    <copyrfirst>
      <year>2026</year>
    </copyrfirst>
    <critdates>
      <created date="2026-01-15"/>
      <revised date="2026-06-06"/>
    </critdates>
  </bookmeta>
  
  <frontmatter>
    <booklists>
      <toc/>
      <figurelist/>
      <tablelist/>
    </booklists>
  </frontmatter>
  
  <chapter href="maps/c_getting_started.ditamap"/>
  <chapter href="maps/c_admin_guide.ditamap"/>
  <chapter href="maps/c_user_guide.ditamap"/>
  
  <backmatter>
    <appendix href="topics/c_glossary.dita"/>
    <appendix href="topics/c_faq.dita"/>
  </backmatter>
</bookmap>
```

## 6. Localization Architecture

### 6.1 Supported Languages

**Tier 1 (Full Support):**
- English (US)
- German (Germany)
- French (France)
- Spanish (Spain)
- Chinese (Simplified)

**Tier 2 (On-Demand):**
- Japanese
- Korean
- Portuguese (Brazil)
- Italian

### 6.2 Translation Workflow

```
English Master (Approved)
    ↓
Extract translatable strings (Automated)
    ↓
Upload to TMS (memoQ/Trados)
    ↓
Professional Translation (NOT machine)
    ↓
Native Speaker Review
    ↓
Translation Memory Updated
    ↓
Generate Multi-Language Output
    ↓
Publish Simultaneously
```

**SLA:** < 1 week for translation

## 7. Publishing Strategy

### 7.1 Multi-Channel Output

```
DITA Source
    ↓
DITA-OT Transformation
    ↓
├─→ HTML5 (Responsive Web)
├─→ PDF (Printable)
├─→ Mobile (App)
├─→ Help System (Context-sensitive)
└─→ Search Index (Elasticsearch)
```

### 7.2 Publishing SLA

- **English Publication:** < 1 hour after approval
- **Translated Publication:** < 24 hours after translation
- **Full Multi-Language:** < 48 hours

---

**Document ID:** ARCH-001  
**Next Review Date:** Q3 2026  
