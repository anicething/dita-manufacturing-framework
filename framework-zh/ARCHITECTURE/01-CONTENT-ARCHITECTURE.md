# 内容架构与设计

**Document ID:** ARCH-001  
**Version:** 1.1  
**Last Updated:** 2026-06-07  
**Status:** Approved

---

## 1. 信息架构概览

### 1.1 高层结构

```
企业文档门户
├── 共享/通用内容（占主题的30%）
│   ├── 公司信息
│   ├── 产品系列
│   ├── 安全与合规
│   ├── 环境标准
│   ├── 共享术语
│   └── 通用流程
│
├── 产品线（占主题的70%）
│   ├── 产品A 文档
│   │   ├── 入门指南
│   │   ├── 管理员指南
│   │   ├── 用户指南
│   │   ├── 技术参考
│   │   ├── API 文档
│   │   ├── 故障排查
│   │   └── 发布说明
│   ├── 产品B 文档
│   └── 产品C 文档
│
└── 支撑文档
    ├── 发布说明（全部产品）
    ├── 已知问题
    ├── 常见问题
    ├── 术语
    ├── 法律/合规
    └── 联系/支持
```

### 1.2 主题分布 — 扩展版 (v1.1)

**核心分布:** 40% 概念 / 40% 任务 / 20% 参考

DITA 1.3 定义了超过 25 种主题类型。本框架采用 **13 种类型**，
分属三个层级。详见 `04-EXTENDED-TOPIC-TYPES.md`。

```
层级一 — 核心类型（占全部内容的 80%）
────────────────────────────────────────────────────────────────
概念主题           320      解释功能、架构、概念
任务主题           320      分步操作流程、操作指南
参考主题           160      命令文档、API、配置

层级二 — 专门类型（占全部内容的 15%）
────────────────────────────────────────────────────────────────
故障排查            60      按报警代码进行问题诊断
术语条目            50      可复用、可翻译的术语定义
危害声明            30      用于 ISO 合规的结构化安全警告
任务需求            30      结构化前置条件（技能、工具、条件）

层级三 — 高级类型（占全部内容的 5%）
────────────────────────────────────────────────────────────────
Bookmap            10      正式出版结构（PDF/打印）
学习对象            10      在线学习模块
Subject Scheme       1      元数据受控值治理
─────────────────────────────────────────────────────────────────
总计              ~1,000    个主题（不含图与 Scheme）

按产品分布：
MC-5000 VMC:         350 个主题
MC-8000 5轴:         350 个主题
DR-2000 高速:        300 个主题
共享/通用:           200 个主题（跨所有产品复用）
```

## 2. 内容复用策略

### 2.1 复用模式

#### 模式 1：共享主题（占主题的 30%）

**用例：** 所有产品中完全相同的内容

```
共享主题：
├── 公司名称与Logo引用
├── 联系信息
├── 支持时间与流程
├── 法律免责声明
├── 保修信息
├── 安全警告
├── 环境合规
├── 认证信息
├── 术语术语
└── 产品系列概览
```

#### 模式 2：内容片段（占主题的 20%）

**用例：** 主题内的部分复用（步骤、警告、示例）

```
内容片段：
├── 常用任务步骤（设置、配置、安装）
├── 警告与注意（安全、数据丢失、安全）
├── 支持信息（联系、升级路径）
├── 示例与用例
└── 故障排查步骤（通用）
```

#### 模式 3：产品专属主题（占主题的 50%）

**用例：** 每个产品的独特内容

```
产品专属：
├── 产品概览
├── 功能文档
├── 配置指南（产品专属）
├── API 参考
├── 故障排查（产品专属）
└── 发布说明
```

### 2.2 内容归属

**共享内容：**
- 负责人：DITA 架构师
- 评审：DGC 每月
- 更新：按需
- 复用目标：跨产品 100%

**产品专属内容：**
- 负责人：产品文档工程师（每产品）
- 评审：产品经理
- 更新：按产品发布周期
- 复用目标：产品内部 35-40%

## 3. 元数据架构

### 3.1 必需元数据（全部主题）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE topic PUBLIC "-//OASIS//DTD DITA Topic//EN" "topic.dtd">
<topic id="example-topic">
  <title>Example Topic Title</title>
  <prolog>
    <metadata>
      <!-- 文档标识 -->
      <data name="docid" value="DOC-PROD-001-v2.1"/>
      <data name="internal-id" value="example-topic"/>

      <!-- 分类 -->
      <data name="product" value="Product A"/>
      <data name="product-version" value="2.1+"/>
      <data name="audience" value="admin|user|developer|executive"/>
      <data name="topic-type" value="concept|task|reference"/>

      <!-- 合规与状态 -->
      <data name="status" value="approved"/>
      <data name="security-level" value="public|internal|confidential"/>
      <data name="iso-relevant" value="yes|no"/>

      <!-- 评审与维护 -->
      <data name="created-date" value="2026-01-15"/>
      <data name="revised-date" value="2026-06-06"/>
      <data name="author" value="John Smith"/>
      <data name="reviewed-by" value="Jane Doe"/>
      <data name="next-review-date" value="2026-09-06"/>
      <data name="review-frequency" value="quarterly|biannual|annual"/>

      <!-- 本地化 -->
      <data name="translatable" value="yes|no"/>
      <data name="translation-status" value="original|translated"/>
      <data name="language" value="en-US"/>
    </metadata>
  </prolog>

  <body>
    <!-- 主题内容 -->
  </body>
</topic>
```

### 3.2 元数据验证规则

| 字段 | 必需 | 有效值 | 示例 |
|-------|----------|--------------|----------|
| docid | 是 | `DOC-PROD-NNN-vX.Y` | DOC-PROD-001-v1.0 |
| product | 是 | 产品代码 | PROD-A, PROD-B |
| audience | 是 | admin, user, developer, executive | admin |
| status | 是 | draft, review, approved, obsolete | approved |
| created-date | 是 | YYYY-MM-DD | 2026-01-15 |
| reviewed-by | 是 | 人员姓名 | Jane Doe |
| security-level | 是 | public, internal, confidential | internal |
| translatable | 是 | yes, no | yes |

## 4. 主题类型规格

### 4.1 概念主题

**目的：** 解释「是什么？」和「为什么？」

**结构：**
```
标题
├─ 简短描述（2-3 句）
├─ 目的/背景
├─ 关键概念
├─ 图示/插图
├─ 示例
└─ 相关主题
```

**篇幅：** 1-3 页  
**受众：** 所有人  
**可复用性：** 高  

### 4.2 任务主题

**目的：** 分步骤的「操作方法」流程

**结构：**
```
标题（动作动词 + 宾语）
├─ 简短描述
├─ 目的
├─ 前置条件
├─ 步骤（编号）
├─ 预期结果
├─ 提示/故障排查
└─ 相关主题
```

**篇幅：** 1-5 页  
**受众：** 实操人员  
**可复用性：** 非常高  

### 4.3 参考主题

**目的：** 查阅信息（字典风格）

**结构：**
```
标题
├─ 简短描述
├─ 目的
├─ 参考数据（表格、列表、规格）
├─ 示例
└─ 相关参考资料
```

**篇幅：** 1-3 页  
**受众：** 实操人员  
**可复用性：** 非常高  

### 4.4 故障排查主题（v1.1 新增）

**目的：** 根据症状诊断问题 — 每个主题对应一个报警代码

**结构：**
```
标题（报警代码 + 症状）
├─ 简短描述
├─ 可观察状况（操作员看到的现象 + 阈值）
├─ 原因 1（最可能，含百分比频率）
│   └─ 解决措施 1..N（解决步骤）
├─ 原因 2（次可能）
│   └─ 解决措施 1..N
└─ 原因 N（罕见）
    └─ 解决措施 1..N
```

**篇幅：** 1-3 页  
**受众：** 维修技术员  
**可复用性：** 中等（可在使用相同组件的产品线之间部分复用）  
**模板：** `templates/06-TROUBLESHOOTING-TEMPLATE.dita`  
**示例：** `examples/topics/ALARM-700016-TROUBLESHOOTING.dita`

### 4.5 术语条目（v1.1 新增）

**目的：** 将术语定义为独立、可复用、可翻译的主题

**结构：**
```
术语
├─ 定义（1-2 句）
├─ 替代形式（缩略词、缩写、表面形式）
└─ 相关术语（交叉引用网络）
```

**篇幅：** 1 段  
**受众：** 全部  
**可复用性：** 非常高（跨所有主题 100% 复用）  
**模板：** `templates/07-GLOSSARY-ENTRY-TEMPLATE.dita`  
**示例：** `examples/topics/GLOSS-LOTO.dita`

### 4.6 危害声明（v1.1 新增）

**目的：** 用于 ISO 合规审计的结构化、机器可读安全警告

**结构：**
```
危害名称
├─ 安全警告（危害类型、后果、避免措施）
├─ 危害描述（何时、何地、如何发生）
├─ 风险评估（严重性 × 概率表）
├─ 所需控制措施（工程 + 个人防护装备）
└─ 适用标准
```

**篇幅：** 1-2 页  
**受众：** 操作员、维修人员、安全审计员  
**可复用性：** 高（跨所有使用相同子系统的产品共享）  
**模板：** `templates/08-HAZARD-STATEMENT-TEMPLATE.dita`  
**示例：** `examples/topics/HAZARD-FLYING-CHIPS.dita`

## 5. 图结构组织

### 5.1 主图结构

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE map PUBLIC "-//OASIS//DTD DITA Map//EN" "map.dtd">
<map>
  <title>Product A Documentation</title>
  <topicmeta>
    <author>Tech Comm Team</author>
    <publisher>Manufacturing Inc.</publisher>
    <created date="2026-01-15"/>
  </topicmeta>

  <topicref href="maps/c_getting_started.ditamap" navtitle="Getting Started"/>
  <topicref href="maps/c_admin_guide.ditamap" navtitle="Administrator Guide"/>
  <topicref href="maps/c_user_guide.ditamap" navtitle="User Guide"/>
  <topicref href="maps/c_reference.ditamap" navtitle="Technical Reference"/>
  <topicref href="maps/c_troubleshooting.ditamap" navtitle="Troubleshooting"/>
  <topicref href="topics/c_release_notes.dita" navtitle="Release Notes"/>
</map>
```

### 5.2 用于打印/PDF的 Bookmap

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE bookmap PUBLIC "-//OASIS//DTD DITA BookMap//EN" "bookmap.dtd">
<bookmap>
  <booktitle>
    <mainbooktitle>Product A User Guide</mainbooktitle>
    <booktitlealt>Version 2.1</booktitlealt>
  </booktitle>

  <bookmeta>
    <author>Technical Communication Team</author>
    <copyrfirst>
      <year>2026</year>
    </copyrfirst>
    <critdates>
      <created date="2026-01-15"/>
      <revised date="2026-06-06"/>
    </critdates>
  </bookmeta>

  <frontmatter>
    <booklists>
      <toc/>
      <figurelist/>
      <tablelist/>
    </booklists>
  </frontmatter>

  <chapter href="maps/c_getting_started.ditamap"/>
  <chapter href="maps/c_admin_guide.ditamap"/>
  <chapter href="maps/c_user_guide.ditamap"/>

  <backmatter>
    <appendix href="topics/c_glossary.dita"/>
    <appendix href="topics/c_faq.dita"/>
  </backmatter>
</bookmap>
```

## 6. 本地化架构

### 6.1 支持的语言

**层级一（完全支持）：**
- 英语（美国）
- 德语（德国）
- 法语（法国）
- 西班牙语（西班牙）
- 中文（简体）

**层级二（按需支持）：**
- 日语
- 韩语
- 葡萄牙语（巴西）
- 意大利语

### 6.2 翻译工作流

```
英文母版（已批准）
    ↓
提取可翻译字符串（自动化）
    ↓
上传至翻译管理系统（memoQ/Trados）
    ↓
专业翻译（非机器翻译）
    ↓
母语审校
    ↓
翻译记忆库更新
    ↓
生成多语言输出
    ↓
同步发布
```

**SLA：** 翻译周期小于 1 周

## 7. 发布策略

### 7.1 多渠道输出

```
DITA 源文件
    ↓
DITA-OT 转换
    ↓
├─→ HTML5（响应式 Web）
├─→ PDF（可打印）
├─→ 移动端（App）
├─→ 帮助系统（上下文相关）
└─→ 搜索索引（Elasticsearch）
```

### 7.2 发布 SLA

- **英文发布：** 批准后 < 1 小时
- **翻译后发布：** 翻译完成后 < 24 小时
- **完整多语言发布：** < 48 小时

---

**Document ID:** ARCH-001  
**相关文档：**
- `04-EXTENDED-TOPIC-TYPES.md` — 全部 13+ 采纳类型的完整目录
- `05-SUBJECT-SCHEME-GUIDE.md` — 元数据受控值治理

**下次评审日期：** 2026 年 Q4
