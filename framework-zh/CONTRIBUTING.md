# 贡献指南

**适用于:** DITA 制造框架  
**语言:** 简体中文 (zh-CN)  
**最后更新:** 2026-06-06

---

## 欢迎

感谢您有意为 DITA 制造框架做出贡献。本框架是制造企业技术文档的基础——您的贡献有助于确保所有产品的文档质量、一致性以及合规性。

本指南描述了如何为框架贡献内容、治理文档、翻译以及工具改进。

---

## 1. 行为准则

所有贡献者应：

- 尊重且专业地进行沟通
- 提供建设性的、具体的反馈——避免模糊的反对意见
- 遵循既定的治理与审查工作流
- 在有损贡献者之间寻求共识的意图下解决分歧
- 将合规与安全置于便利之前

---

## 2. 贡献类别

### 2.1 核心框架贡献

这些是提升框架本身的变更：

| 领域 | 内容 | 负责人 |
|------|------|--------|
| Governance（治理） | 治理章程、所有权矩阵、权限矩阵 | DGC |
| Style Guide（样式指南） | 写作规则、格式标准、术语 | DITA 架构师 |
| Templates（模板） | DITA 主题模板、映射/图模板、元数据骨架 | DITA 架构师 |
| Specializations（专用化） | DTD/XSD Schema、领域定义、约束 | DITA 架构师 |
| Tooling（工具） | 发布脚本、CI/CD 流水线、转换 XSLT | IT/基础设施主管 |
| Translations（翻译） | 本框架文档的本地化版本 | 本地化经理 |

### 2.2 产品内容贡献

针对特定产品线的文档贡献通过 [治理章程中的内容所有权矩阵](./GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md) 进行管理。产品写作师应遵循其产品线的审批工作流。

### 2.3 翻译贡献

框架官方翻译（zh-CN、ja-JP、ko-KR、de-DE、fr-FR、es-ES、pt-BR、ar-SA）由本地化经理协调。非授权语言的贡献者应首先联系本地化经理。

---

## 3. 贡献流程

### 3.1 针对治理与框架文档

```
1. 通过查看现有治理（GOV-001、GOV-002、GOV-003）确定待变更内容
2. 创建一个 Git 分支：feature/gov-<描述>-<姓名首字母>
3. 在分支中做出变更
4. 对照审查标准进行审查（见下文第 5 节）
5. 提交一个拉取请求 (PR)，其中包含：
   - 变更的清晰描述
   - 变更理由
   - 对受影响文档/角色的引用
   - 风险评估
6. 根据变更权限矩阵获取批准
7. 一旦批准，合并至 main 分支并关闭 PR
```

### 3.2 针对翻译

```
1. 向本地化经理请求翻译任务分配
2. 使用英语源文件为权威参考
3. 遵循项目术语表以保持术语一致性
4. 保留所有 DITA 标签、属性以及元数据结构
5. 将翻译后的文件提交到适当的语言目录
6. 使用语言与治理标签提交 PR
7. 在合并前获得本地化经理批准
```

### 3.3 针对模板与专用化

```
1. 与 DITA 架构师讨论提议的变更
2. 在分支中开发 Schema/DTD/XSD/模板变更
3. 在至少两个产品线的样本内容中测试变更
4. 若适用，更新元数据 Schema
5. 为架构审查提交 PR
6. DITA 架构师在迁移到正式使用前批准 1 周宽限期
7. 通知所有写作师新模板/专用化的可用性
```

---

## 4. 分支策略与 Git 规范

### 分支命名

```
feature/gov-<描述>       — 治理文档变更
feature/content-<描述>    — 产品内容变更
feature/template-<描述>   — 模板或专用化变更
feature/tooling-<描述>    — 工具与流水线变更
fix/<描述>                — 缺陷修复
translation/<lang>-<描述> — 翻译贡献
```

### 提交信息

- 每行限 72 个字符
- 使用祈使语气（"Add safety topic" 而非 "Added safety topic"）
- 引用文档编号：`[GOV-001] Update review frequency section`
- 引用变更请求：`[CR-2026-001] Add new calibration topic`

### PR 要求

- PR 标题清晰描述变更内容
- PR 描述包括：内容、理由、受影响方、风险
- 所有审查意见必须在合并前解决
- 要求至少一名审批人（根据变更权限矩阵）

---

## 5. 审查标准

贡献将根据以下标准进行评估：

### 治理文档
- 事实准确性与完整性
- 与现有治理结构的一致性
- 对工作流与 SLA 的影响
- 跨产品线和角色的公平性

### 样式指南与模板
- 清晰度与可用性
- 与现有标准的向后兼容性
- 对现有内容的迁移影响
- 是否符合行业最佳实践

### 翻译
- 术语准确性（对照项目术语表）
- 语言流畅性与自然性
- 对原文含义的忠实度
- 正确的 DITA 标签与元数据保留
- 文化适配性（在适当情况下进行区域适配）

### 工具与流水线
- 稳定性和错误处理
- 对现有工作流的性能影响
- 安全性与访问控制
- 文档与可维护性

---

## 6. 文件组织

```
framework-en/                      # English framework (source language)
├── GOVERNANCE/                    # 治理章程、所有权矩阵、变更权限矩阵
├── ARCHITECTURE/                  # 内容架构、元数据模式、主题类型、变量管理
├── STYLE-GUIDE/                   # 写作标准、术语表、质量检查清单
├── WORKFLOWS/                     # 审批流程、文档成套完整性、复用率计算、共享库
├── TOOLS/                         # 工具设置指南
├── TRAINING/                      # 入职指南、快速入门、培训练习、认证清单
├── CI-CD/                         # CI/CD 流水线、DITA-OT 配置、发布 SLA
├── PUBLISHING/                    # 跨格式一致性
├── IMPLEMENTATION-GUIDE.md        # 6 个月采用路线图
├── RISK-CONTROL-STRATEGY.md       # 风险登记与管控措施
├── INDEX.md                       # 文档注册表
├── README.md
└── CONTRIBUTING.md

framework-zh/                      # 简体中文框架（镜像结构）
├── GOVERNANCE/                    # 与 framework-en/ 保持相同目录结构和文件名
├── ARCHITECTURE/
├── STYLE-GUIDE/
├── WORKFLOWS/
├── TOOLS/
├── TRAINING/
├── CI-CD/
├── PUBLISHING/
├── IMPLEMENTATION-GUIDE.md
├── RISK-CONTROL-STRATEGY.md
├── INDEX.md
├── README.md
└── CONTRIBUTING.md
```

- **framework-en/** 是权威源。所有其他语言为翻译版本。
- 当出现差异时，以 framework-en/ 版本为准。
- 对 framework-en/ 源文件的所有变更将触发受影响的翻译文件的更新请求。

---

## 7. 术语表与术语一致性

所有贡献者在起草或翻译时必须使用经批准的项目术语表。主要术语包括：

| 英文 | 简体中文 | 备注 |
|------|----------|------|
| DITA (Darwin Information Typing Architecture) | DITA（达尔文信息分类架构） | 首字母缩写保持英文 |
| Governance Charter | 治理章程 | 官方治理文档 |
| Content Ownership Matrix | 内容所有权矩阵 | 所有权分配文档 |
| Change Authority Matrix | 变更权限矩阵 | 变更批准层级文档 |
| Single Source of Truth | 单一真实数据源 | 核心 DITA 原则 |
| Topic | 主题 | DITA 基本内容单元 |
| Map | 映射/图/映射图 | DITA 内容组织方式 |
| Specialization | 专用化 | DITA Schema 扩展机制 |
| Domain | 内容域 | DITA 领域词汇定义 |
| Conref | 内容引用 (conref) | DITA 内容复用属性 |
| SME (Subject Matter Expert) | 主题专家 (SME) | 首次使用时保留英文缩写 |
| SLA (Service Level Agreement) | 服务水平协议 (SLA) | 首次使用时保留英文缩写 |
| KPI (Key Performance Indicator) | 关键绩效指标 (KPI) | 首次使用时保留英文缩写 |
| Metadata | 元数据 | 标准术语 |
| Taxonomy | 分类法 | 信息架构术语 |
| Styling | 样式 | 格式化与呈现 |

如需新增术语或就现有术语提出异议，请联系元数据管理师。

---

## 8. 版本控制与发布

### 框架版本控制
- 框架遵循语义化版本控制 (SemVer)：`MAJOR.MINOR.PATCH`
- **MAJOR** — 治理或架构发生重大变更
- **MINOR** — 新增模板、新增专用化、新增文档章节
- **PATCH** — 拼写修正、格式调整、小幅度澄清

### 发布周期
- 月度补丁版本（拼写错误修正、小修复）
- 季度次要版本（新增模板、章节新增）
- 年度主要版本（与年度治理审查保持一致）

### 翻译版本控制
- 翻译紧随其 en-US 源版本之后
- 翻译文件名与 en-US 源文件名保持一致
- 翻译后框架文档的预期滞后时间为 < 2 周

---

## 9. 沟通渠道

| 目的 | 渠道 | 受众 |
|------|------|------|
| 治理讨论与批准 | DGC 会议（每月） | DGC 全体成员 |
| 架构与标准讨论 | 架构团队 Slack：#dita-architecture | 架构师、资深写作师 |
| 写作师支持与问题 | 写作师 Slack：#dita-writers | 所有写作师 |
| 工具与流水线问题 | IT 支持工单系统 | IT/基础设施主管 |
| 翻译协调 | 本地化 Slack：#l10n-dita | 本地化经理、翻译团队 |
| 会议之外的一般性问题 | 电子邮件至 dita-governance@company.com | DGC 主席 |

---

## 10. 入门指南

首次贡献者应按以下步骤操作：

1. 阅读 [治理章程 (GOV-001)](./GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md)
2. 阅读与本框架相关的[内容所有权矩阵 (GOV-002)](./GOVERNANCE/02-CONTENT-OWNERSHIP-MATRIX.md)
3. 阅读[变更权限矩阵 (GOV-003)](./GOVERNANCE/03-CHANGE-AUTHORITY-MATRIX.md)
4. 在此仓库上配置好 Git 及适当的访问权限
5. 加入 #dita-writers Slack 频道
6. 认领一个标记为 `good first issue` 的议题，或联系您的产品经理请求分配任务
7. 参加下一个排期的 DGC 会议（观察员身份），了解治理的实际运作方式

---

## 11. 许可证

对本框架的贡献采用与框架本体相同的许可证发布。请参阅仓库根目录下的 [LICENSE](../LICENSE) 文件。

---

**本贡献指南如有疑问，请联系 DGC 主席。**
