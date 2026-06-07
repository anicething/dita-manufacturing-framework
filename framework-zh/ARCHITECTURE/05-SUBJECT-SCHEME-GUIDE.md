# Subject Scheme — 元数据治理指南

**Document ID:** ARCH-005  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved

---

## 1. 什么是 Subject Scheme？

Subject Scheme 是一种 DITA 特殊化，用于为元数据属性定义**受控词汇表**。与让作者输入自由文本值（如 `audience="operator"`，可能被写成 "Operator"、"OPERATOR"、"operators" 等）不同，Subject Scheme 强制执行一个预定义列表：

- 作者在 oXygen 中看到一个下拉菜单 → 选择 "operator"
- 验证检查每个主题是否符合该方案
- 报告可以一致地聚合

## 2. 为什么我们的框架需要它

| 不使用 Subject Scheme | 使用 Subject Scheme |
|------------------------|---------------------|
| 一个主题中 `audience="operator"`，另一个中 `audience="Operator"` | 两者都根据同一控制列表进行验证 |
| 无法按产品以编程方式过滤主题 | CI/CD 可以检查："所有 MC-5000 主题" |
| 作者随意创建新的元数据字段 | 仅允许定义的字段和值 |
| 元数据无自动化质量检查 | Pre-commit 钩子可以根据方案验证 |

## 3. 受控值 — 完整参考

### 3.1 `audience`

| Key | 显示名称 | 描述 |
|-----|-------------|-------------|
| `operator` | Operator（操作员） | 机器操作员 — 需要「如何运行」内容 |
| `maintenance` | Maintenance Technician（维修技术员） | 维修和服务内容 |
| `engineer` | Engineer（工程师） | 设计、应用、安装工程 |
| `field-service` | Field Service（现场服务） | 现场调试和维修 |
| `trainer` | Trainer（培训师） | 进行面授课程的培训师 |
| `all` | All Audiences（全部受众） | 与所有人相关的内容 |

### 3.2 `content-type`

| Key | 显示名称 | DTD |
|-----|-------------|-----|
| `concept` | Concept（概念） | `concept.dtd` |
| `task` | Task（任务） | `task.dtd` |
| `reference` | Reference（参考） | `reference.dtd` |
| `troubleshooting` | Troubleshooting（故障排查） | `troubleshooting.dtd` |
| `glossary-entry` | Glossary Entry（术语条目） | `glossentry.dtd` |
| `hazard-statement` | Hazard Statement（危害声明） | `reference.dtd` + hi-d domain |
| `task-requirements` | Task Requirements（任务需求） | `task.dtd` + pr-d domain |
| `learning-object` | Learning Object（学习对象） | `learningObject.dtd` |

### 3.3 `document-status`

| Key | 描述 |
|-----|-------------|
| `draft` | 正在编写 — 尚未提交评审 |
| `review` | 已提交 — 处于审批工作流中 |
| `approved` | 已获得技术和编辑批准 |
| `published` | 已在最新 CI/CD 构建中发布 |
| `archived` | 已替代或从活跃使用中移除 |

### 3.4 `product`

| Key | 显示名称 |
|-----|-------------|
| `MC-5000` | MC-5000 VMC |
| `MC-8000` | MC-8000 5-Axis（5轴） |
| `DR-2000` | DR-2000 High-Speed（高速） |
| `common` | Shared / Common（共享/通用） |

### 3.5 `hazard-severity`

| Key | 显示名称 | 含义 |
|-----|-------------|---------|
| `danger` | Danger（危险） | 将导致死亡或严重伤害 |
| `warning` | Warning（警告） | 可能导致死亡或严重伤害 |
| `caution` | Caution（注意） | 可能导致轻微或中度伤害 |
| `notice` | Notice（提示） | 仅造成财产损失 |

### 3.6 `skill-level`

| Key | 显示名称 |
|-----|-------------|
| `level-1` | Level 1 — Trainee（学员，仅限监督操作） |
| `level-2` | Level 2 — Junior Technician（初级技术员） |
| `level-3` | Level 3 — Experienced Technician（经验丰富的技术员） |
| `level-4` | Level 4 — Senior Technician（高级技术员） |
| `level-5` | Level 5 — Master Technician / Engineer（大师级技术员/工程师） |

## 4. 在 oXygen XML 编辑器中的实施

### 4.1 设置

1. 将 Scheme 文件保存到受版本控制的位置：
   `04-SUBJECT-SCHEME/METADATA-CONTROLLED-VALUES.ditamap`

2. 在 oXygen 中：**Options → Preferences → DITA → Subject Scheme**
   添加 Scheme 文件路径。

3. 现在您可以：
   - 编辑 `.dita` 文件时，看到 `audience`、`product` 等字段的下拉列表
   - 运行 **DITA → Validate → Validate with Subject Scheme** 检查合规性
   - 生成按主题过滤的内容引用

### 4.2 Git 钩子集成

添加 pre-commit 钩子以验证 Subject Scheme 合规性：

```bash
#!/bin/bash
# .git/hooks/pre-commit — DITA metadata validation
for file in $(git diff --cached --name-only --diff-filter=ACM | grep '\.dita$'); do
  if ! dita validate --subject-scheme=subject-scheme.ditamap "$file"; then
    echo "ERROR: $file failed subject scheme validation"
    exit 1
  fi
done
```

## 5. 扩展 Subject Scheme

要添加新的受控值（例如，新产品线）：

```xml
<subjectdef keys="DR-9000">
  <topicmeta><navtitle>DR-9000 Heavy-Duty VMC</navtitle></topicmeta>
</subjectdef>
```

将其插入到 `product` 的 `<hasInstance>` 块下，然后重新运行验证。无需更改单个主题 — Scheme 是唯一的事实来源。

## 6. 与元数据架构的关系

Subject Scheme 与 `02-METADATA-SCHEMA.md` 中定义的元数据架构**协同工作，而非替代**：

| 关注点 | 由谁处理 |
|---------|-----------|
| 哪些字段是必需的？ | 元数据架构 |
| 允许哪些值？ | Subject Scheme |
| 主题是否有效？ | Subject Scheme + 质量检查清单 |
| 在 oXygen 中如何显示？ | Subject Scheme + oXygen 配置 |

---

**文件：** `templates/12-SUBJECT-SCHEME-TEMPLATE.ditamap`  
**下次评审日期：** 2026 年 Q4
