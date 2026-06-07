# Variable Management — Key-Based Content Reuse Strategy

## 1. Purpose

This document defines the strategy for managing **variable content** (company names, product names, legal statements, version numbers) across all DITA documentation. The goal is to ensure that a single change propagates to all topics without manual search-and-replace.

## 2. The Problem

Hard-coded variable content creates maintenance nightmares:

```
<p>Welcome to DingRui Precision Machinery DR-5000 documentation.</p>
```

If the company name changes, every topic containing it must be edited. In a 10,000-topic manufacturing documentation set, this is a disaster.

## 3. The Solution: Three-Layer Variable Strategy

DITA provides three complementary mechanisms for variable management. Use them in order of preference:

| Priority | Mechanism | When to Use | Change Impact |
|----------|-----------|-------------|---------------|
| **1** | Keys + keyref | Content within topic body, prolog metadata | 1 change in ditamap |
| **2** | Conkeyref (content referencing) | Longer reusable text blocks (legal, copyright) | 1 change in source topic |
| **3** | XML Entities (`.ent`) | DTD-level macros, attribute defaults | 1 change in .ent file |

---

## 4. Mechanism 1: Keys + keyref (Recommended Primary)

### 4.1 Defining Keys in the Framework-Level Map

Create a centralized key definition map:

```xml
<!-- references/framework-keys.ditamap -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map id="framework-keys">
  <title>Framework Global Key Definitions</title>

  <!-- === COMPANY === -->
  <keydef keys="company-name">
    <topicmeta>
      <keywords><keyword>ExampleCorp Manufacturing Co., Ltd.</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="company-abbr">
    <topicmeta>
      <keywords><keyword>ECM</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="company-short">
    <topicmeta>
      <keywords><keyword>ExampleCorp</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="company-address">
    <topicmeta>
      <keywords><keyword>No. 123, Innovation Road, Science Park, Hsinchu, Taiwan</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- === PRODUCTS === -->
  <keydef keys="product-line">
    <topicmeta>
      <keywords><keyword>MC Series Machining Centers</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="product-flagship">
    <topicmeta>
      <keywords><keyword>MC-5000 5-Axis Machining Center</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- === STANDARDS === -->
  <keydef keys="standard-iso12100">
    <topicmeta>
      <keywords><keyword>ISO 12100:2010</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="standard-iso9001">
    <topicmeta>
      <keywords><keyword>ISO 9001:2015</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="standard-iso27001">
    <topicmeta>
      <keywords><keyword>ISO 27001:2022</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- === LEGAL === -->
  <keydef keys="copyright-year">
    <topicmeta>
      <keywords><keyword>2026</keyword></keywords>
    </topicmeta>
  </keydef>
  <keydef keys="doc-version">
    <topicmeta>
      <keywords><keyword>v1.1</keyword></keywords>
    </topicmeta>
  </keydef>
</map>
```

### 4.2 Referencing Keys in Topic Content

Within any topic body:

```xml
<p>Welcome to the <keyword keyref="company-name"/> documentation
for the <keyword keyref="product-flagship"/>.</p>
```

Output:
> Welcome to the ExampleCorp Manufacturing Co., Ltd. documentation for the MC-5000 5-Axis Machining Center.

### 4.3 Referencing Keys in Metadata (Prolog)

```xml
<prolog>
  <metadata>
    <publisher>
      <keyword keyref="company-name"/>
    </publisher>
    <copyright>
      <copyryear year="2026"/>
      <copyrholder>
        <keyword keyref="company-name"/>
      </copyrholder>
    </copyright>
  </metadata>
</prolog>
```

### 4.4 Key Scoping and Override

Keys follow DITA's scoping rules. A product-level map can override framework keys:

```xml
<!-- products/mc5000/mc5000-map.ditamap -->
<map id="mc5000-map">
  <!-- Include framework keys -->
  <topicref href="../references/framework-keys.ditamap"
            processing-role="resource-only"/>

  <!-- Override product-specific keys -->
  <keydef keys="product-flagship">
    <topicmeta>
      <keywords><keyword>MC-5000 5-Axis Machining Center</keyword></keywords>
    </topicmeta>
  </keydef>

  <!-- Topic references -->
  <topicref href="topics/overview.dita"/>
</map>
```

---

## 5. Mechanism 2: Conkeyref (Content Referencing)

Use conkeyref when the reusable content is longer than a keyword (paragraphs, multi-line legal text, boilerplate sections).

### 5.1 Defining Reusable Content

```xml
<!-- references/legal-statements.dita -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE reference PUBLIC "-//OASIS//DTD DITA Reference//EN" "reference.dtd">
<reference id="legal-statements">
  <title>Legal Statements</title>
  <refbody>
    <section id="copyright">
      <title>Copyright Notice</title>
      <p id="copyright-en">Copyright © 2026 ExampleCorp Manufacturing Co., Ltd.
All rights reserved. No part of this document may be reproduced
without prior written permission.</p>
      <p id="copyright-zh">版权所有 © 2026 示例制造有限公司。保留所有权利。</p>
    </section>
    <section id="disclaimer">
      <title>Disclaimer</title>
      <p id="disclaimer-en">The information in this document is subject to change
without notice. ExampleCorp makes no warranty of any kind regarding
the accuracy of this information.</p>
    </section>
    <section id="trademark">
      <title>Trademark Notice</title>
      <p id="trademark-en">ExampleCorp and the ExampleCorp logo are trademarks
of ExampleCorp Manufacturing Co., Ltd.</p>
    </section>
  </refbody>
</reference>
```

### 5.2 Binding in the Ditamap

```xml
<keydef keys="legal" href="../references/legal-statements.dita"/>
```

### 5.3 Referencing from a Topic

```xml
<conbody>
  <section>
    <title>Copyright</title>
    <p conkeyref="legal/copyright-en"/>
  </section>
</conbody>
```

---

## 6. Mechanism 3: XML Entities (Legacy / DTD-Level)

Entities are the oldest mechanism and still useful for attribute values or DITA-OT configuration.

### 6.1 Entity Definition File

```xml
<!-- references/variables.ent -->
<!ENTITY company-name    "ExampleCorp Manufacturing Co., Ltd.">
<!ENTITY company-abbr    "ECM">
<!ENTITY product-flagship "MC-5000 5-Axis Machining Center">
<!ENTITY copyright-year  "2026">
<!ENTITY doc-version     "v1.1">
```

### 6.2 Using Entities in a Topic

```xml
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd"
  [<!ENTITY % vars SYSTEM "../references/variables.ent">
   %vars;
  ]>
<concept id="overview">
  <title>&product-flagship; Overview</title>
  <conbody>
    <p>Welcome to &company-name; documentation.</p>
  </conbody>
</concept>
```

> **Limitations:** Entities do not support DITA's key scoping. If you override a key at the product level, topics opened via that map will use the override. Entities always resolve to their definition regardless of context.

---

## 7. Variable Change Process

When a variable changes (e.g., company rename, product rebranding, standard update), follow this process:

### 7.1 Change Scenarios

| Scenario | What to Change | Effort |
|----------|---------------|--------|
| Company name change | Update key definitions in `framework-keys.ditamap` | **5 minutes** |
| Product rebranding | Update key definitions; optionally override at product level | **15 minutes** |
| Copyright year update | Update `copyright-year` key value | **1 minute** |
| Legal disclaimer update | Edit `legal-statements.dita` source | **30 minutes** |
| ISO standard reference update | Update key value or conref source | **10 minutes** |
| Version number bump | Update `doc-version` key value | **1 minute** |

### 7.2 Change Procedure

```
1. Identify the variable(s) needing change
2. Locate the defining source:
   - Key value → edit framework-keys.ditamap
   - Conref block → edit the source DITA topic
   - Entity → edit variables.ent
3. Make the change in ONE location
4. Verify with a test build
5. Publish all affected documentation
6. Update the change log
```

---

## 8. Directory Structure for Variables

```
references/
├── framework-keys.ditamap        ← Centralized key definitions (all products)
├── legal-statements.dita         ← Copyright, disclaimer, trademark (EN)
├── variables.ent                 ← Legacy entity definitions
├── en/
│   ├── company-variables.dita    ← Company information topics (EN)
│   └── standard-refs.dita        ← ISO/standards reference (EN)
└── zh-CN/
    ├── company-variables.dita    ← Company information topics (zh-CN)
    └── standard-refs.dita        ← ISO/standards reference (zh-CN)
```

---

## 9. Integration with Templates

Every DITA template SHOULD include key-based references instead of hard-coded placeholders:

| Template Element | Use Key/Reference | Instead Of |
|-----------------|-------------------|------------|
| Publisher name | `<keyword keyref="company-name"/>` | `ExampleCorp` |
| Product name | `<keyword keyref="product-flagship"/>` | `MC-5000` |
| Copyright notice | `<p conkeyref="legal/copyright-en"/>` | Hard-coded text |
| ISO standard | `<keyword keyref="standard-iso12100"/>` | `ISO 12100:2010` |
| Document version | `<keyword keyref="doc-version"/>` | `v1.1` |

---

## 10. Governance Rules

| Rule | Description | Enforcement |
|------|-------------|-------------|
| M-V1 | No hard-coded company names in topic body or title | Schematron check |
| M-V2 | All product names must use keyref | Code review |
| M-V3 | Legal text must use conkeyref to central source | Schematron check |
| M-V4 | Framework keys must not be overridden with null values | CI/CD validation |
| M-V5 | New variables must be documented in this file | Architecture review |

---

## 11. Migration: Converting Hard-Coded Topics

To migrate existing topics to key-based references:

1. Run a grep to find hard-coded company/product names
2. For each occurrence, replace with `<keyword keyref="xxx"/>`
3. Add the framework-keys ditamap to the product map
4. Rebuild and verify all outputs
5. Remove obsolete stale topic variants

**Automated migration script (bash):**
```bash
#!/bin/bash
# Replace hard-coded company name with keyref
find topics/ -name "*.dita" -exec sed -i \
  's/ExampleCorp Manufacturing Co\., Ltd\./<keyword keyref="company-name"\/>/g' {} \;
```

---

## Related Documents

- [01-CONTENT-ARCHITECTURE.md](01-CONTENT-ARCHITECTURE.md) — Overall architecture
- [02-METADATA-SCHEMA.md](02-METADATA-SCHEMA.md) — M1-M7 metadata rules
- [05-SUBJECT-SCHEME-GUIDE.md](05-SUBJECT-SCHEME-GUIDE.md) — Controlled values
- [Templates README](../templates/README.md) — Using variables in templates
