# 元数据架构与验证

**Document ID:** ARCH-002  
**Version:** 1.1  
**Last Updated:** 2026-06-07  
**Status:** Approved

> **v1.1 更新：** 为全部 13 种扩展主题类型添加了元数据字段。
> 类型描述详见 `04-EXTENDED-TOPIC-TYPES.md`。

---

## 1. 必需元数据字段

每个主题，无论类型如何，都必须在 `<prolog>` 中包含以下字段：

### 1.1 通用字段（全部主题类型）

| 字段 | 元素 | 必需 | 示例 |
|-------|---------|----------|---------|
| 主题 ID | `@id`（根元素上） | 是 | `alarm-700016-troubleshooting` |
| 语言 | `@xml:lang` | 是 | `en-US` |
| 受众 | `<audience>` | 是 | `type="maintenance"` |
| 内容类型 | `<othermeta name="content-type">` | 是 | `troubleshooting` |
| 产品 | `<prodname>` | 是 | `MC-5000 VMC` |
| 版本 | `<vrm>` | 是 | `version="2.1"` |
| 文档状态 | `<othermeta name="document-status">` | 是 | `approved` |
| 负责部门 | `<othermeta name="owner-department">` | 是 | `Safety Engineering` |

### 1.2 类型专属必需字段

| 主题类型 | 额外必需字段 |
|------------|--------------------------|
| **troubleshooting** | `alarm-code`, `severity`, `estimated-repair-time` |
| **glossary-entry** | `glossary-domain`（[safety|technical|commercial|legal]），`translatable="yes"` |
| **hazard-statement** | `hazard-type`, `severity`（[danger|warning|caution|notice]），`iso-reference` |
| **task-requirements** | `estimated-time`, `required-skill-level`（使用 Subject Scheme） |
| **learning-object** | `duration`, `module-type`, `certification-relevant` |
| **concept** | 通用字段之外无需额外字段 |
| **task** | `estimated-time`, `required-skill-level` |
| **reference** | 通用字段之外无需额外字段 |

### 1.3 示例：故障排查元数据

```xml
<prolog>
  <metadata>
    <audience type="maintenance" job="technician" experiencelevel="experienced"/>
    <category>Troubleshooting</category>
    <prodinfo>
      <prodname>MC-5000 VMC</prodname>
      <vrmlist>
        <vrm version="2.1" release="A" modification="3"/>
      </vrmlist>
    </prodinfo>
    <othermeta name="content-type" content="troubleshooting"/>
    <othermeta name="alarm-code" content="700016"/>
    <othermeta name="severity" content="warning"/>
    <othermeta name="estimated-repair-time" content="15-60 minutes"/>
    <othermeta name="document-status" content="approved"/>
    <othermeta name="owner-department" content="Field Service"/>
  </metadata>
</prolog>
```

### 1.4 示例：术语条目元数据

```xml
<prolog>
  <metadata>
    <audience type="all"/>
    <category>Glossary</category>
    <othermeta name="content-type" content="glossary-entry"/>
    <othermeta name="glossary-domain" content="safety"/>
    <othermeta name="translatable" content="yes"/>
    <othermeta name="document-status" content="published"/>
    <othermeta name="iso-reference" content="ISO 14118"/>
  </metadata>
</prolog>
```

### 1.5 示例：危害声明元数据

```xml
<prolog>
  <metadata>
    <audience type="operator" job="all" experiencelevel="all"/>
    <category>Safety</category>
    <prodinfo>
      <prodname>MC-5000 VMC</prodname>
    </prodinfo>
    <othermeta name="content-type" content="hazard-statement"/>
    <othermeta name="hazard-type" content="mechanical"/>
    <othermeta name="severity" content="caution"/>
    <othermeta name="iso-reference" content="ISO 12100"/>
  </metadata>
</prolog>
```

## 2. 受控值

所有元数据值均由 **Subject Scheme** 管控
（参见 `05-SUBJECT-SCHEME-GUIDE.md`）。权威列表
定义在 `templates/12-SUBJECT-SCHEME-TEMPLATE.ditamap` 中。

### 2.1 受众值

```
operator       — 机器操作员
maintenance    — 维修技术员
engineer       — 设计/应用工程师
field-service  — 现场服务技术员
trainer        — 培训师
all            — 全部受众
```

### 2.2 内容类型值

```
concept              — 概念主题
task                 — 任务主题
reference            — 参考主题
troubleshooting      — 故障排查主题
glossary-entry       — 术语条目
hazard-statement     — 危害声明
task-requirements    — 结构化任务前置条件
learning-object      — 学习模块
```

### 2.3 文档状态值

```
draft       — 正在编写，尚未提交评审
review      — 处于审批工作流中
approved    — 已获得技术和编辑批准
published   — 已在 CI/CD 构建中发布
archived    — 已替代或从活跃使用中移除
```

### 2.4 危害严重性值

```
danger      — 将导致死亡或严重伤害
warning     — 可能导致死亡或严重伤害
caution     — 可能导致轻微或中度伤害
notice      — 仅造成财产损失
```

## 3. 元数据验证规则

| 规则 | 描述 | 执行方式 |
|------|-------------|-------------|
| **M1** | 所有通用字段必须存在 | Git pre-commit 钩子 |
| **M2** | `content-type` 与 DOCTYPE 匹配 | CI/CD 构建检查 |
| **M3** | 所有值来源于 Subject Scheme | oXygen 验证 |
| **M4** | `document-status` 与分支一致 | Git 工作流 |
| **M5** | `product` 与文件位置匹配 | 目录约定 |
| **M6** | `xml:lang` 与仓库路径匹配 | 目录约定 |
| **M7** | 无孤立主题（没有外部交叉引用的主题） | 每季度审计 |

---

## 4. 元数据模板

可复用的元数据块提供于 `templates/04-METADATA-TEMPLATE.xml`。

---

**Document ID:** ARCH-002  
**相关文档：**
- `04-EXTENDED-TOPIC-TYPES.md` — 主题类型定义
- `05-SUBJECT-SCHEME-GUIDE.md` — 受控值治理
- `templates/12-SUBJECT-SCHEME-TEMPLATE.ditamap` — Subject Scheme 文件

**下次评审日期：** 2026 年 Q4
