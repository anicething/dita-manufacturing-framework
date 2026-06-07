# DITA Manufacturing Framework / DITA 制造框架

**DITA Governance Framework for Manufacturing Enterprises**
**面向制造企业的 DITA 治理框架**

Version / 版本: v1.1 | Date / 日期: 2026-06-07
License / 许可: MIT

---

## Introduction / 简介

The DITA Manufacturing Framework is a complete governance and authoring framework for manufacturing enterprises adopting DITA (Darwin Information Typing Architecture). Designed for organizations with 50+ team members, multiple product lines, and multi-language requirements, it provides everything needed to launch and sustain a structured documentation practice -- from governance charters and content architecture to ready-to-use XML templates and CI/CD pipelines.

本框架为采用 DITA（Darwin Information Typing Architecture）的制造企业提供完整的治理与写作框架。面向 50 人以上团队、多产品线、多语言需求的组织，涵盖从治理章程、内容架构到即用 XML 模板和 CI/CD 流水线的全方位支持。

**Compliance Standards:** ISO 27001, ISO 9001, ISO 14001
**Technology Stack:** DITA 1.3, oXygen XML Editor, DITA-OT

---

## Project Structure / 项目结构

The framework is organized into two fully self-contained language versions. Each includes governance, training, examples, templates, and references specific to that language.

框架分为两个完全自包含的语言版本，每个版本包含对应的治理、培训、示例、模板和引用。

```
dita-manufacturing-framework/
├── framework-en/                   # English governance documents / 英文框架文档
│   ├── GOVERNANCE/                 # Governance charter, ownership, change authority
│   ├── ARCHITECTURE/               # Content architecture, metadata, topic types
│   ├── STYLE-GUIDE/                # Writing standards, glossary, quality checklist
│   ├── WORKFLOWS/                  # Approval workflows, Git strategy, review criteria
│   ├── TOOLS/                      # Tool setup, version control, CI/CD pipeline
│   ├── TRAINING/                   # Framework onboarding + DITA curriculum slides (novice/professional/expert)
│   ├── CI-CD/                      # CI/CD pipeline, DITA-OT config, publishing SLA
│   ├── PUBLISHING/                 # Cross-format consistency
│   ├── examples/                   # Examples: governance, maps, metadata, topics
│   ├── templates/                  # 12 DITA XML templates (Concept, Task, Reference, etc.)
│   ├── references/                 # Reusable DITA keys, variables, entities
│   ├── IMPLEMENTATION-GUIDE.md     # 6-month adoption roadmap
│   ├── RISK-CONTROL-STRATEGY.md    # Risk register and controls
│   ├── INDEX.md                    # Document registry
│   ├── README.md
│   └── CONTRIBUTING.md
├── framework-zh/                   # 简体中文框架文档 / Simplified Chinese framework
│   ├── GOVERNANCE/
│   ├── ARCHITECTURE/
│   ├── STYLE-GUIDE/
│   ├── WORKFLOWS/
│   ├── TOOLS/
│   ├── TRAINING/
│   ├── CI-CD/
│   ├── PUBLISHING/
│   ├── examples/                   # 示例：maps, topics
│   ├── templates/                  # 12 个 DITA XML 模板
│   ├── references/                 # Reusable DITA keys, variables, entities
│   ├── IMPLEMENTATION-GUIDE.md
│   ├── RISK-CONTROL-STRATEGY.md
│   ├── INDEX.md
│   ├── README.md
│   └── CONTRIBUTING.md
├── README.md                       # This file / 本文件
├── CONTRIBUTING.md
└── LICENSE
```

---

## Quick Navigation / 快速导航

### Two Language Versions / 双语言版本

| Section / 部分 | English / 英文 | Chinese / 中文 | Description / 说明 |
|----------------|----------------|----------------|---------------------|
| **Framework / 框架** | [framework-en/](framework-en/) | [framework-zh/](framework-zh/) | Core governance, architecture, and templates (language-specific) |
| **Templates / 模板** | Included in each framework directory — see `framework-en/templates/` and `framework-zh/templates/` | | 12 ready-to-use DITA XML templates (per language) |
| **Examples / 示例** | Included in each framework directory — see `framework-en/examples/` and `framework-zh/examples/` | | Worked examples using Example Manufacturing scenarios |

---

## Framework Module Details / 框架模块详情

| Module / 模块 | English | Chinese | Content / 内容 |
|---------------|---------|---------|-----------------|
| **Governance / 治理** | [GOVERNANCE/](framework-en/GOVERNANCE/) | [GOVERNANCE/](framework-zh/GOVERNANCE/) | Charter, content ownership, change authority |
| **Architecture / 架构** | [ARCHITECTURE/](framework-en/ARCHITECTURE/) | [ARCHITECTURE/](framework-zh/ARCHITECTURE/) | Content design, metadata schema, topic types, variable management |
| **Implementation / 落地指南** | [IMPLEMENTATION-GUIDE.md](framework-en/IMPLEMENTATION-GUIDE.md) | [IMPLEMENTATION-GUIDE.md](framework-zh/IMPLEMENTATION-GUIDE.md) | 6-month adoption roadmap, team roles, risk register, KPIs |
| **Style Guide / 风格指南** | [STYLE-GUIDE/](framework-en/STYLE-GUIDE/) | [STYLE-GUIDE/](framework-zh/STYLE-GUIDE/) | Writing standards, glossary, quality checks |
| **Workflows / 工作流** | [WORKFLOWS/](framework-en/WORKFLOWS/) | [WORKFLOWS/](framework-zh/WORKFLOWS/) | Approval process, Git branching, reviews |
| **Tools / 工具** | [TOOLS/](framework-en/TOOLS/) | [TOOLS/](framework-zh/TOOLS/) | oXygen, DITA-OT, TMS, version control setup |
| **Training / 培训** | [TRAINING/](framework-en/TRAINING/) | [TRAINING/](framework-zh/TRAINING/) | Onboarding program, exercises, certification |
| **CI/CD** | [CI-CD/](framework-en/CI-CD/) | [CI-CD/](framework-zh/CI-CD/) | CI/CD pipeline, DITA-OT, publishing SLA |
| **Publishing / 发布控制** | [PUBLISHING/](framework-en/PUBLISHING/) | [PUBLISHING/](framework-zh/PUBLISHING/) | Cross-format consistency (HTML5/PDF/Mobile), CSS strategy, testing |

---

## Key Features / 核心特性

- **Complete Governance Framework** -- committee structure, ownership matrix, change authority, ISO compliance built-in
- **Content Architecture** -- topic distribution (40% Concept / 40% Task / 20% Reference), 35-40% reuse target
- **Manufacturing-Specific** -- safety and compliance focus, multi-product coordination, environmental management
- **Multi-Language Support** -- Tier-1 (EN, DE, FR, ES, zh-CN) + Tier-2 (JA, KO, PT-BR, IT)
- **Team Coordination** -- 10-step approval workflow with SLAs, 5 review stages
- **Automation** -- CI/CD pipeline, multi-channel publishing (HTML5, PDF, Mobile)
- **12 DITA Templates** -- Concept, Task, Reference, Troubleshooting, Glossary, Hazard Statement, Task Requirements, Learning Object, Ditamap, Bookmap, Subject Scheme, and Metadata

---

## Getting Started / 入门指南

### For Leadership / 领导层

1. Review the [Governance Charter](framework-en/GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md) / 阅读[治理章程](framework-zh/GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md)
2. Understand the [Content Ownership Matrix](framework-en/GOVERNANCE/02-CONTENT-OWNERSHIP-MATRIX.md) / 了解[内容所有权矩阵](framework-zh/GOVERNANCE/02-CONTENT-OWNERSHIP-MATRIX.md)
3. Approve the governance framework and schedule monthly DGC meetings

### For Technical Writers / 技术写作人员

1. Study the [Style Guide](framework-en/STYLE-GUIDE/01-STYLE-GUIDE.md) / 学习[风格指南](framework-zh/STYLE-GUIDE/01-STYLE-GUIDE.md)
2. Understand the [Approval Workflow](framework-en/WORKFLOWS/01-APPROVAL-WORKFLOW.md) / 了解[审批流程](framework-zh/WORKFLOWS/01-APPROVAL-WORKFLOW.md)
3. Review [Topic Templates](framework-en/ARCHITECTURE/03-TOPIC-TEMPLATES.md) / 查看[主题模板](framework-zh/ARCHITECTURE/03-TOPIC-TEMPLATES.md)
4. Begin the 4-week training program / 开始 4 周培训计划

### For IT/Infrastructure / IT 基础设施团队

1. Follow the [Tool Setup Guide](framework-en/TOOLS/01-TOOL-SETUP.md) / 按[工具设置指南](framework-zh/TOOLS/01-TOOL-SETUP.md)操作
2. Implement the [CI/CD Pipeline](framework-en/CI-CD/01-CICD-PIPELINE.md) / 实施 [CI/CD 流水线](framework-zh/CI-CD/01-CICD-PIPELINE.md)

### For Product Managers / 产品经理

1. Review [Content Architecture](framework-en/ARCHITECTURE/01-CONTENT-ARCHITECTURE.md) / 查看[内容架构](framework-zh/ARCHITECTURE/01-CONTENT-ARCHITECTURE.md)
2. Understand governance and content ownership / 了解治理和内容所有权
3. Plan product documentation schedule and participate in approval workflow

---

## Worked Examples / 示例文档

Examples, templates, and references are distributed alongside the framework documents — each language version has its own copies within `framework-en/` and `framework-zh/`.

示例文档、模板和参考资料跟随框架文档分发，中英文各自独立存放在 `framework-en/` 和 `framework-zh/` 中。

- **English examples:** [framework-en/examples/README.md](framework-en/examples/README.md) — complete, real-world DITA documentation for Example Manufacturing Co., Ltd.
- **English templates:** [framework-en/templates/README.md](framework-en/templates/README.md) — 12 ready-to-use DITA XML templates with English placeholder content
- **English references:** `framework-en/references/` — reusable keys, variables, and entities
- **Chinese examples:** [framework-zh/examples/README.md](framework-zh/examples/README.md) — 中文示例文档
- **Chinese templates:** [framework-zh/templates/README.md](framework-zh/templates/README.md) — 12 个中文 DITA XML 模板
- **Chinese references:** `framework-zh/references/` — 可复用的键、变量和实体

---

## How to Use This Framework / 如何使用本框架

1. **Governance Committee**: Start with `framework-en/GOVERNANCE/` to understand roles and responsibilities
2. **Content Architects**: Review `framework-en/ARCHITECTURE/` for information design and reuse strategy
3. **Technical Writers**: Begin with `framework-en/STYLE-GUIDE/` and `framework-en/templates/`, then study `framework-en/examples/`
4. **IT/DevOps**: Configure tools via `framework-en/TOOLS/` and set up CI/CD from `framework-en/CI-CD/`
5. **Chinese-language teams**: Use `framework-zh/` for Chinese-specific guidance

All framework documents are available in both English (source) and Simplified Chinese.

所有框架文档均提供英文（源语言）和简体中文版本。

---

## Contributing / 参与贡献

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on proposing changes, review processes, and documentation standards.

---

## License / 许可

MIT License -- see [LICENSE](LICENSE) for details.

---

**Last Updated / 最后更新:** 2026-06-07
**Next Review / 下次评审:** Q3 2026
**Version / 版本:** 1.1
