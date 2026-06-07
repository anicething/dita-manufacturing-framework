# DITA Templates / DITA 模板 -- v1.1

This directory contains DITA XML templates for English documentation. All 12 templates are available directly at this level. Chinese equivalent templates are in `../../framework-zh/templates/`.

本目录包含英文 DITA XML 模板。12 个模板均直接在此目录下。中文对应模板位于 `../../framework-zh/templates/`。

---

## Structure / 目录结构

```
templates/
├── 01-CONCEPT-TOPIC-TEMPLATE.dita        (Concept / 概念)
├── 02-TASK-TOPIC-TEMPLATE.dita           (Task / 任务)
├── 03-REFERENCE-TOPIC-TEMPLATE.dita      (Reference / 参考)
├── 04-METADATA-TEMPLATE.xml              (Metadata / 元数据)
├── 05-DITAMAP-TEMPLATE.ditamap           (Ditamap / 导航图)
├── 06-TROUBLESHOOTING-TEMPLATE.dita      (Troubleshooting / 故障排除)
├── 07-GLOSSARY-ENTRY-TEMPLATE.dita       (Glossary Entry / 术语条目)
├── 08-HAZARD-STATEMENT-TEMPLATE.dita     (Hazard Statement / 危险声明)
├── 09-TASK-REQUIREMENTS-TEMPLATE.dita    (Task Requirements / 任务前提)
├── 10-LEARNING-OBJECT-TEMPLATE.dita      (Learning Object / 学习对象)
├── 11-BOOKMAP-TEMPLATE.ditamap           (Bookmap / 书图)
└── 12-SUBJECT-SCHEME-TEMPLATE.ditamap    (Subject Scheme / 主题方案)
```

Chinese templates are in `../../framework-zh/templates/` with identical filenames.

## Template Index / 模板索引

### Core Types / 核心类型 (v1.0 -- 5 templates / 5 个模板)

| # | File / 文件 | Type / 类型 | Use For / 用途 |
|---|-------------|-------------|----------------|
| T1 | `01-CONCEPT-TOPIC-TEMPLATE.dita` | Concept / 概念 | Explanatory content (what/why) / 说明性内容（是什么/为什么） |
| T2 | `02-TASK-TOPIC-TEMPLATE.dita` | Task / 任务 | Step-by-step procedures / 分步操作流程 |
| T3 | `03-REFERENCE-TOPIC-TEMPLATE.dita` | Reference / 参考 | Specifications and data / 技术规格与数据 |
| T4 | `04-METADATA-TEMPLATE.xml` | Metadata / 元数据 | Boilerplate metadata block / 标准元数据块 |
| T5 | `05-DITAMAP-TEMPLATE.ditamap` | Ditamap / 导航图 | Web navigation structure / Web 导航结构 |

### Extended Types / 扩展类型 (v1.1 addition / v1.1 新增 -- 7 templates / 7 个模板)

| # | File / 文件 | Type / 类型 | Use For / 用途 |
|---|-------------|-------------|----------------|
| T6 | `06-TROUBLESHOOTING-TEMPLATE.dita` | Troubleshooting / 故障排除 | Problem diagnosis by alarm code / 按报警代码诊断问题 |
| T7 | `07-GLOSSARY-ENTRY-TEMPLATE.dita` | Glossary Entry / 术语条目 | Reusable term definitions / 可复用术语定义 |
| T8 | `08-HAZARD-STATEMENT-TEMPLATE.dita` | Hazard Statement / 危险声明 | Structured safety warnings / 结构化安全警告 |
| T9 | `09-TASK-REQUIREMENTS-TEMPLATE.dita` | Task Requirements / 任务前提 | Structured prerequisites / 结构化前置条件 |
| T10 | `10-LEARNING-OBJECT-TEMPLATE.dita` | Learning Object / 学习对象 | E-learning modules / 在线培训模块 |
| T11 | `11-BOOKMAP-TEMPLATE.ditamap` | Bookmap / 书图 | Print/PDF publication structure / 印刷/PDF 发布结构 |
| T12 | `12-SUBJECT-SCHEME-TEMPLATE.ditamap` | Subject Scheme / 主题方案 | Metadata controlled values / 元数据受控值 |

---

## Template Quick Reference / 模板快速参考

| Template / 模板 | Use For / 用途 | Avg. Size / 平均篇幅 | Est. Time / 预计时间 | Reuse Target / 复用目标 |
|-----------------|----------------|----------------------|---------------------|------------------------|
| Concept / 概念 | Explanations / 说明 | 1-3 pgs | 2-4 hrs | 40% |
| Task / 任务 | Procedures / 流程 | 1-5 pgs | 2-6 hrs | 50% |
| Reference / 参考 | Specifications / 规格 | 1-3 pgs | 1-3 hrs | 80% |
| Troubleshooting / 故障排除 | Problem diagnosis / 问题诊断 | 1-3 pgs | 2-4 hrs | 30% |
| Glossary Entry / 术语条目 | Term definitions / 术语定义 | 1 para / 1 段 | 30 min | 100% |
| Hazard Statement / 危险声明 | Safety warnings / 安全警告 | 1-2 pgs | 2-3 hrs | 70% |
| Task Requirements / 任务前提 | Prerequisites / 前提条件 | 1-2 pgs | 1-2 hrs | 40% |
| Learning Object / 学习对象 | Training content / 培训内容 | 5-10 pgs | 8-16 hrs | 20% |

---

## How to Use / 使用说明

### Quick Start / 快速开始

1. Copy the template file to your project / 将模板文件复制到你的项目
2. Replace `"TEMPLATE"` in the `@id` with your topic ID / 将 `@id` 中的 `"TEMPLATE"` 替换为你的主题 ID
3. Replace all bracketed `[placeholders]` with your content / 将所有方括号 `[占位符]` 替换为实际内容
4. Validate XML against its DTD / 对照 DTD 验证 XML
5. Submit for review per the approval workflow / 按照审批流程提交评审

### Example / 示例

```bash
# Copy troubleshooting template / 复制故障排除模板
cp 06-TROUBLESHOOTING-TEMPLATE.dita ../MC-5000/topics/ALARM-700016-TROUBLESHOOTING.dita

# Edit in oXygen / 在 oXygen 中编辑
oXygen ../MC-5000/topics/ALARM-700016-TROUBLESHOOTING.dita

# Add content, validate, then submit / 添加内容、验证后提交
git add ../MC-5000/topics/ALARM-700016-TROUBLESHOOTING.dita
git commit -m "Add: Alarm 700016 troubleshooting topic / 添加报警 700016 故障排除主题"
```

### How to Use / 使用说明

**English authors:** Use templates from `templates/` — they contain English placeholder text and comments.

**中文作者:** 使用 `../../framework-zh/templates/` 中的模板 — 它们包含中文占位内容和注释。

Both template sets are structurally identical. Set `xml:lang` appropriately in the output.

---

## Design Reference / 设计参考

See `ARCHITECTURE/04-EXTENDED-TOPIC-TYPES.md` for the rationale behind each type and the decision tree for choosing the correct one.

关于各类型的原理说明以及选择正确类型的决策树，参见 `../../framework-zh/ARCHITECTURE/04-EXTENDED-TOPIC-TYPES.md`。

---

## All Templates Include / 所有模板包含

- Proper DITA DOCTYPE and structure / 正确的 DITA DOCTYPE 和结构
- Required metadata fields in `<prolog>` / `<prolog>` 中的必需元数据字段
- XML comments with authoring guidance / 含写作指导的 XML 注释
- Manufacturing-specific placeholder content / 制造行业特定的占位内容
- ISO compliance fields where applicable / 适用的 ISO 合规字段

---

## Related Links / 相关链接

- [Framework (English)](../../README.md)
- [Framework (Chinese)](../../framework-zh/README.md)
- [Examples (English)](../examples/README.md)
- [Examples (Chinese)](../../framework-zh/examples/README.md)
- [Top-Level README](../../README.md)

---

**Version / 版本:** v1.1
**Last Updated / 最后更新:** 2026-06-07
