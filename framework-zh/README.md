# DITA 制造框架 — 中文版

**组织:** 制造企业 (Manufacturing Enterprise)
**版本:** v1.1
**日期:** 2026-06-07
**语言:** 简体中文 (zh-CN)

---

## 概述

本目录包含 **DITA 制造框架 v1.1** 的中文翻译文档。该框架为大型制造企业（50 人以上团队、多产品线、多语言支持）提供完整的 DITA 文档治理方案，涵盖治理结构、内容架构、写作规范、工作流程、工具配置、培训体系和持续集成/部署流水线。

英文源文件位于 `framework-en/` 目录。本目录 (`framework-zh/`) 提供对应的简体中文翻译，作为 Tier-1 语言支持的一部分。

**合规标准:** ISO 27001、ISO 9001、ISO 14001（中文版对应 GB/T 标准）

---

## 目录结构

| 模块 | 目录 | 说明 |
|------|------|------|
| **治理** | `GOVERNANCE/` | 治理章程、内容所有权矩阵、变更权限矩阵 |
| **架构** | `ARCHITECTURE/` | 内容架构设计、元数据模式、主题模板规范、扩展主题类型指南、Subject Scheme 指南、变量管理策略 |
| **落地指南** | `/IMPLEMENTATION-GUIDE.md` | 6 个月分阶段实施路线图，含团队配置、风险管理、KPI 仪表盘 |
| **风格指南** | `STYLE-GUIDE/` | 写作原则与规范、写作风格与技巧指南、术语表、质量检查清单 |
| **工作流** | `WORKFLOWS/` | 审批流程（含 SLA）、文档成套完整性/失效控制/状态变更控制、复用率计算与优化、共享库治理流程 |
| **工具** | `TOOLS/` | oXygen XML Editor、DITA-OT、TMS、版本控制配置与 CI/CD 工具链 |
| **培训** | `TRAINING/` | 入职指南（4 周培训计划）、快速上手、练习项目、认证清单 |
| **CI/CD** | `CI-CD/` | CI/CD 流水线自动化发布、DITA-OT 转换配置、发布 SLA |
| **发布控制** | `PUBLISHING/` | 跨格式输出一致性（HTML5/PDF/移动端）写作规则、CSS 策略、自动化测试 |

---

## 使用说明

### 适用读者

- **领导层/治理委员会**: 阅读 `GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md`（治理章程）
- **技术写作团队**: 从 `STYLE-GUIDE/01-STYLE-GUIDE.md`（风格指南）和 `WORKFLOWS/`（工作流）开始
- **IT/基础设施团队**: 参考 `TOOLS/`（工具配置）和 `CI-CD/`（发布流水线）
- **产品经理**: 查看 `ARCHITECTURE/01-CONTENT-ARCHITECTURE.md`（内容架构）和 `GOVERNANCE/`（内容所有权）

### 翻译说明

- 中文版自英文源语言翻译，每个中文 DITA 主题的 `xml:lang` 属性设为 `"zh-CN"`
- 技术标准已转换为对应的 GB/T 国家标准（如 ISO 13849-1 -> GB/T 16855.1）
- 术语采用中国大陆制造业通用表达

### 文件命名约定

中文翻译文件与英文源文件保持相同的目录结构和文件名，仅路径从 `framework-en/` 变为 `framework-zh/`。例如:

- English: `framework-en/GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md`
- Chinese: `framework-zh/GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md`

---

## 版本信息

| 字段 | 值 |
|------|-----|
| **版本** | v1.1 |
| **发布日期** | 2026-06-07 |
| **源语言版本** | framework-en/ (v1.1) |
| **翻译完成度** | 进行中 |
| **适用标准** | GB/T 16855.1、GB/T 19001 (ISO 9001)、GB/T 24001 (ISO 14001) |

---

## 相关链接

- [English Framework (framework-en/)](../framework-en/README.md)
- [项目顶层 README](../README.md)
- [DITA 示例（中文版）](examples/README.md)
- [DITA 模板参考](templates/README.md)

---

**最后更新:** 2026-06-07
**下次评审:** Q3 2026
