# DITA Training / DITA 培训

This directory contains the **DITA Manufacturing Framework Training Curriculum** — organized into four categories covering concept, framework, writing, and implementation topics.

此目录包含完整的 **DITA 制造框架培训课程** —— 分为概念、框架、写作和落地四大类别。

---

## Curriculum Overview / 课程概览

| 类别 | 模块数 | 侧重点 |
|------|--------|--------|
| **01-CONCEPT / 概念** | 7 个模块 | DITA 基础、主题类型、XML、Subject Scheme、专用化、信息架构 |
| **02-FRAMEWORK / 框架** | 3 个模块 | 框架概述、模板使用、元数据基础 |
| **03-WRITING / 写作** | 5 个模块 | Bookmap/PDF、Subject Scheme 深入、多语言发布、产品培训 |
| **04-IMPLEMENTATION / 落地** | 3 个模块 | CI/CD、审批流程、持续改进 |
| **总计** | **18 个模块** | **完整的 DITA 制造培训课程** |

---

## Directory Structure / 目录结构

```
TRAINING/
├── README.md                    ← 本文件
├── dita-theme.css               ← 自定义 Marp 幻灯片主题
├── 01-CONCEPT/                  ← 概念培训
│   ├── 01-DITA-JI-CHU.md
│   ├── 02-SAN-HE-XIN-LEI-XING.md
│   ├── 03-DITA-XML-JI-CHU.md
│   ├── 04-KUO-ZHAN-ZHU-TI-LEI-XING.md
│   ├── 05-SUBJECT-SCHEME.md
│   ├── 06-DITA-ZHUAN-YONG-HUA.md
│   └── 07-XIN-XI-JIA-GOU.md
├── 02-FRAMEWORK/                ← 框架培训
│   ├── 01-KUANG-JIA-GAI-SHU.md
│   ├── 02-SHI-YONG-MO-BAN.md
│   └── 03-YUAN-SHU-JU-JI-CHU.md
├── 03-WRITING/                  ← 写作与发布培训
│   ├── 01-SUBJECT-SCHEME-SHEN-RU.md
│   ├── 02-BOOKMAP-PDF.md
│   ├── 03-NEI-RONG-FU-YONG.md
│   ├── 04-DUO-YU-YAN-FA-BU.md
│   └── 05-CHAN-PIN-PEI-XUN.md
└── 04-IMPLEMENTATION/           ← 落地与工作流
    ├── 01-CI-CD-LIU-SHUI-XIAN.md
    ├── 02-SHEN-PI-LIU-CHENG.md
    └── 03-CHI-XU-GAI-JIN.md
```

---

## How to View the Slides / 如何查看幻灯片

幻灯片使用 **Marp**（Markdown 演示生态系统）。每个 `.md` 文件是一个独立的幻灯片集。

### 方式一：VS Code + Marp 扩展（推荐）

1. 安装 [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) 扩展
2. 打开 `TRAINING/` 中的任意 `.md` 文件
3. 点击"打开侧边预览"或按 `Ctrl+Shift+V`
4. 自定义主题 `dita-theme.css` 将自动应用

### 方式二：Marp CLI

```bash
npx @marp-team/marp-cli 01-CONCEPT/01-DITA-JI-CHU.md
```

### 方式三：导出为 PDF

```bash
npx @marp-team/marp-cli 01-CONCEPT/01-DITA-JI-CHU.md --pdf
```

---

## 01-CONCEPT：概念培训

深入的 DITA 概念培训，帮助建立扎实的基础。适合所有级别。

| 模块 | 标题 | 说明 |
|------|------|------|
| 01 | DITA 基础 | DITA 是什么、历史、核心原则、优势 |
| 02 | 三大核心主题类型 | 概念、任务、参考——何时使用每种类型 |
| 03 | DITA XML 基础 | XML 语法、元素、属性、DTD、格式良好 |
| 04 | 扩展主题类型 | 故障排查、术语、危害、任务前置条件、学习对象 |
| 05 | Subject Scheme | 受控值、分类法管理、Schematron |
| 06 | DITA 专用化 | 领域与主题专用化、.mod/.ent/.dtd |
| 07 | 信息架构 | IA 设计、粒度、内容-任务矩阵、复用模式 |

---

## 02-FRAMEWORK：框架培训

如何使用 DITA 制造框架进行日常编写。

| 模块 | 标题 | 说明 |
|------|------|------|
| 01 | 框架概述 | 12 个模板、主题类型、审批流程、CI/CD |
| 02 | 使用模板 | 复制→重命名→替换→验证 工作流 |
| 03 | 元数据基础 | Prolog、元数据元素、M1-M7 规则 |
| 04 | DITA Map 基础 | Ditamap 结构、地图类型、嵌套、keydef |
| 05 | 元数据治理 | 元数据策略、评审周期、合规性 |

---

## 03-WRITING：写作与发布

高级写作技巧和多渠道发布。

| 模块 | 标题 | 说明 |
|------|------|------|
| 01 | 内容复用 | Conref、conkeyref、内容引用模式 |
| 02 | Bookmap 与 PDF | Bookmap 结构、PDF 发布、DITA-OT Chemistry |
| 03 | 本地化 | 本地化策略、XLIFF、翻译工作流 |
| 04 | 多语言发布 | 多语言构建、CJK 字体、批量发布 |
| 05 | 产品培训 | 产品特定的文档模式 |

---

## 04-IMPLEMENTATION：落地与工作流

工具链搭建、自动化和持续改进。

| 模块 | 标题 | 说明 |
|------|------|------|
| 01 | 入职指南 | 新团队成员入职、工具设置、初始任务 |
| 02 | 审批流程 | 10 步审批流程、Git、PR 评审 |
| 03 | 实践项目 | 基础实践练习 |
| 04 | 进阶项目 | 中级实践场景 |
| 05 | CI/CD 流水线 | 自动化验证、发布流水线 |
| 06 | 指标与 KPI | 文档质量指标、度量 |
| 07 | 团队管理 | 团队结构、角色、职责 |
| 08 | 持续改进 | 成熟度模型、指标、KPI、路线图 |

---

## Using the Custom Theme / 使用自定义主题

The `dita-theme.css` provides professional manufacturing styling:

| CSS 类 | 效果 |
|--------|------|
| `<!-- _class: title -->` | 深蓝色渐变背景，白色文字 |
| `<!-- _class: divider -->` | 纯蓝背景，居中文字 |
| `<!-- _class: exercise -->` | 绿色顶部边框，h1 带 ⚡ 前缀 |
| `<!-- _class: keypoint -->` | 橙色边框，圆角 |
| `<!-- _class: note -->` | 黄色左边框，警告样式 |
| `<!-- _class: tip -->` | 蓝色左边框，信息样式 |
| `<!-- _class: columns -->` | Flexbox 双栏布局 |

---

## Related Links / 相关链接

- [Framework Documentation (English)](../../framework-en/README.md)
- [框架文档（简体中文）](../README.md)
- [DITA Examples](../examples/README.md)
- [DITA Templates](../templates/README.md)
- [Main Project README](../../README.md)

---

**Version / 版本:** v1.1
**Last Updated / 最后更新:** 2026-06-07
