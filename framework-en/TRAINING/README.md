# DITA Training / DITA 培训

This directory contains the complete **DITA Manufacturing Framework Training Curriculum** — a three-level professional training program for technical writers, engineers, and documentation specialists in the manufacturing industry.

此目录包含完整的 **DITA 制造框架培训课程** —— 面向制造业技术写作人员、工程师和文档专家的三级专业培训体系。

---

## Curriculum Overview / 课程概览

| Level / 级别 | Duration / 时长 | Modules / 模块 | Target Audience / 目标学员 |
|--------------|-----------------|----------------|---------------------------|
| **Novice / 初级** | 2 days (16 hrs) | 8 modules | New technical writers, engineers new to DITA |
| **Professional / 进阶** | 2 days (16 hrs) | 8 modules | Experienced writers, content architects |
| **Expert / 专家** | 2 days (16 hrs) | 6 modules | Senior architects, team leads, tool admins |
| **Total** | **6 days (48 hrs)** | **22 modules** | **Entire documentation team** |

---

## Directory Structure / 目录结构

```
TRAINING/
├── README.md                    ← This file / 本文件
├── dita-theme.css               ← Custom Marp presentation theme
├── 01-ONBOARDING-GUIDE.md       ← Framework onboarding (4-week program)
├── 02-QUICK-START.md            ← First-day quick start guide
├── 03-TRAINING-EXERCISES.md     ← Hands-on projects
├── 04-CERTIFICATION-CHECKLIST.md← Assessment criteria
│
├── curriculum/                  ← Full training curriculum
│   ├── dita-novice/             ←   Level 1: Novice (8 modules)
│   ├── dita-professional/       ←   Level 2: Professional (8 modules)
│   ├── dita-expert/             ←   Level 3: Expert (6 modules)
│   ├── framework/               ←   Framework-level modules
│   └── product/                 ←   Product-level modules
└── ...training modules
```

---

## How to View the Slides / 如何查看幻灯片

The slides use **Marp** (Markdown Presentation Ecosystem). Each `.md` file is a standalone slide deck.

### Option 1: VS Code with Marp Extension (Recommended)

1. Install the [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) extension
2. Open any `.md` file in `training/`
3. Click "Open Preview to the Side" or press `Ctrl+Shift+V`
4. The custom theme `dita-theme.css` is automatically applied

### Option 2: Marp CLI

```bash
npx @marp-team/marp-cli curriculum/dita-novice/01-DITA-FUNDAMENTALS.md
```

### Option 3: Export to PDF

```bash
npx @marp-team/marp-cli curriculum/dita-novice/01-DITA-FUNDAMENTALS.md --pdf
```

---

## Novice Course (Level 1) / 初级课程

**Duration:** 2 days (16 hours)
**Prerequisites:** None — this is an entry-level course.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | DITA Fundamentals | What is DITA, history, core principles, benefits |
| 02 | Three Core Topic Types | Concept, Task, Reference — when to use each |
| 03 | DITA XML Basics | XML syntax, elements, attributes, DTDs, well-formedness |
| 04 | Using Templates | 12 framework templates, how to choose and use them |
| 05 | Metadata Basics | Prolog, metadata elements, M1-M7 rules |
| 06 | DITA Map Basics | .ditamap structure, topicref, hierarchy, keys |
| 07 | Approval Workflow | 10-step approval process, Git, PR reviews |
| 08 | Hands-On Project | Capstone: create a complete small documentation set |

---

## Professional Course (Level 2) / 进阶课程

**Duration:** 2 days (16 hours)
**Prerequisites:** Novice course or equivalent DITA experience.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | Extended Topic Types | Troubleshooting, Glossary, Hazard, Task Reqs, Learning Object |
| 02 | Content Reuse | Conref, conkeyref, content referencing patterns |
| 03 | Subject Scheme | Controlled values, taxonomy management, Schematron |
| 04 | Bookmap & PDF | Bookmap structure, PDF publishing, DITA-OT Chemistry |
| 05 | Localization | Translation workflow, XLIFF, 9-language framework |
| 06 | Metadata Governance | M1-M7 rules, automated checks, quality metrics |
| 07 | Multilingual Publishing | Multi-language builds, CJK fonts, batch publishing |
| 08 | Professional Project | Capstone: full product documentation with localization |

---

## Expert Course (Level 3) / 专家课程

**Duration:** 2 days (16 hours)
**Prerequisites:** Professional course or equivalent experience.

| Module | Title | Description |
|--------|-------|-------------|
| 01 | DITA Specialization | Domain vs topic specialization, .mod/.ent/.dtd |
| 02 | CI/CD Pipeline | GitHub Actions, automated validation, publishing |
| 03 | Content Metrics & KPIs | Reuse ratio, coverage, readability, compliance scoring |
| 04 | Team Management | DITA CoE, ownership models, roles, budgeting |
| 05 | Framework Customization | Custom topic types, DITA-OT plugins, new output formats |
| 06 | Continuous Improvement | Maturity model, 12-month roadmap, AI/ML in docs |

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

**Version / 版本:** v1.0
**Last Updated / 最后更新:** 2026-06-07
