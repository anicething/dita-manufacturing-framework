# DITA Training / DITA 培训

This directory contains the **DITA Manufacturing Framework Training Curriculum** — organized into four categories covering concept, framework, writing, and implementation topics.

此目录包含 **DITA 制造框架培训课程** —— 分为概念、框架、写作和落地四大类别。

---

## Curriculum Overview / 课程概览

| Category / 类别 | Modules / 模块数 | Focus / 侧重点 |
|-----------------|-----------------|----------------|
| **01-CONCEPT / 概念** | 7 modules | DITA fundamentals, topic types, XML, Subject Scheme, specialization, IA |
| **02-FRAMEWORK / 框架** | 3 modules | Framework overview, templates, metadata basics |
| **03-WRITING / 写作** | 5 modules | Bookmap/PDF, subject scheme, multilingual, product training |
| **04-IMPLEMENTATION / 落地** | 3 modules | CI/CD, approval workflow, continuous improvement |
| **Total** | **18 modules** | **Complete DITA manufacturing curriculum** |

---

## Directory Structure / 目录结构

```
TRAINING/
├── README.md                    ← This file / 本文件
├── dita-theme.css               ← Custom Marp presentation theme
├── 01-CONCEPT/                  ← Deep concept training
│   ├── 01-DITA-FUNDAMENTALS.md
│   ├── 02-THREE-CORE-TOPICS.md
│   ├── 03-DITA-XML-BASICS.md
│   ├── 04-EXTENDED-TOPIC-TYPES.md
│   ├── 05-SUBJECT-SCHEME.md
│   ├── 06-DITA-SPECIALIZATION.md
│   └── 07-INFORMATION-ARCHITECTURE.md
├── 02-FRAMEWORK/                ← Framework training
│   ├── 01-FRAMEWORK-OVERVIEW.md
│   ├── 02-USING-TEMPLATES.md
│   └── 03-METADATA-BASICS.md
├── 03-WRITING/                  ← Writing & publishing training
│   ├── 01-SUBJECT-SCHEME-DEEP.md
│   ├── 02-BOOKMAP-PDF.md
│   ├── 03-CONTENT-REUSE.md
│   ├── 04-MULTILINGUAL-PUBLISHING.md
│   └── 05-PRODUCT-TRAINING.md
└── 04-IMPLEMENTATION/           ← Implementation & workflows
    ├── 01-CICD-PIPELINE.md
    ├── 02-APPROVAL-WORKFLOW.md
    └── 03-CONTINUOUS-IMPROVEMENT.md
```

---

## How to View the Slides / 如何查看幻灯片

The slides use **Marp** (Markdown Presentation Ecosystem). Each `.md` file is a standalone slide deck.

### Option 1: VS Code with Marp Extension (Recommended)

1. Install the [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) extension
2. Open any `.md` file in `TRAINING/`
3. Click "Open Preview to the Side" or press `Ctrl+Shift+V`
4. The custom theme `dita-theme.css` is automatically applied

### Option 2: Marp CLI

```bash
npx @marp-team/marp-cli 01-CONCEPT/01-DITA-FUNDAMENTALS.md
```

### Option 3: Export to PDF

```bash
npx @marp-team/marp-cli 01-CONCEPT/01-DITA-FUNDAMENTALS.md --pdf
```

---

## 01-CONCEPT: Concept Training / 概念培训

Deep DITA concepts for building a strong foundation. Suitable for all levels.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | DITA Fundamentals | What is DITA, history, core principles, benefits |
| 02 | Three Core Topic Types | Concept, Task, Reference — when to use each |
| 03 | DITA XML Basics | XML syntax, elements, attributes, DTDs, well-formedness |
| 04 | Extended Topic Types | Troubleshooting, Glossary, Hazard, Task Reqs, Learning Object |
| 05 | Subject Scheme | Controlled values, taxonomy management, Schematron |
| 06 | DITA Specialization | Domain vs topic specialization, .mod/.ent/.dtd |
| 07 | Information Architecture | IA design, granularity, content-task matrix, reuse patterns |

---

## 02-FRAMEWORK: Framework Training / 框架培训

How to use the DITA Manufacturing Framework day-to-day.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | Framework Overview | 12 templates, topic types, approval workflow, CI/CD |
| 02 | Using Templates | Copy → Rename → Replace → Validate workflow |
| 03 | Metadata Basics | Prolog, metadata elements, M1-M7 rules |
| 04 | DITA Map Basics | Ditamap structure, map types, nesting, keydef |
| 05 | Metadata Governance | Metadata policies, review cycles, compliance |

---

## 03-WRITING: Writing & Publishing / 写作与发布

Advanced writing techniques and multi-channel publishing.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | Content Reuse | Conref, conkeyref, content referencing patterns |
| 02 | Bookmap & PDF | Bookmap structure, PDF publishing, DITA-OT Chemistry |
| 03 | Localization | Localization strategy, XLIFF, translation workflow |
| 04 | Multilingual Publishing | Multi-language builds, CJK fonts, batch publishing |
| 05 | Product Training | Product-specific documentation patterns |

---

## 04-IMPLEMENTATION: Implementation & Workflows / 落地与工作流

Setting up the toolchain, automation, and continuous improvement.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | Onboarding Guide | New team member onboarding, tool setup, first tasks |
| 02 | Approval Workflow | 10-step approval process, Git, PR reviews |
| 03 | Hands-On: Novice | Guided exercises for beginners |
| 04 | Hands-On: Professional | Intermediate exercises and scenarios |
| 05 | CI/CD Pipeline | Automated validation, publishing pipeline |
| 06 | Metrics & KPIs | Documentation quality metrics, measurement |
| 07 | Team Management | Team structure, roles, responsibilities |
| 08 | Continuous Improvement | Maturity model, metrics, KPIs, roadmap |

---

## Using the Custom Theme / 使用自定义主题

The `dita-theme.css` provides professional manufacturing styling:

| CSS Class / 样式类 | Appearance / 外观 |
|--------------------|--------------------|
| `<!-- _class: title -->` | Dark blue gradient background, white text |
| `<!-- _class: divider -->` | Solid blue background, centered text |
| `<!-- _class: exercise -->` | Green top border, ⚡ prefix on h1 |
| `<!-- _class: keypoint -->` | Orange border, rounded corners |
| `<!-- _class: note -->` | Yellow left border, warning-style |
| `<!-- _class: tip -->` | Blue left border, info-style |
| `<!-- _class: columns -->` | Flexbox two-column layout |

---

## Related Links / 相关链接

- [Framework Documentation (English)](../README.md)
- [框架文档（简体中文）](../../framework-zh/README.md)
- [DITA Examples](../examples/README.md)
- [DITA Templates](../templates/README.md)
- [Main Project README](../../README.md)

---

**Version / 版本:** v1.1
**Last Updated / 最后更新:** 2026-06-07
