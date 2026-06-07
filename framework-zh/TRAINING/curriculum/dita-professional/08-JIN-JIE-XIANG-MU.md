---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# 模块 08：进阶实践项目
## DITA 专业级综合实训

**DITA 制造框架 | Professional Level Training**

---

## 学习目标

完成本项目后，你将能够：

- 独立规划一套完整的产品文档结构
- 综合运用所有已学的 DITA 进阶技能
- 创建包含多主题类型的完整文档集
- 实现内容复用和本地化准备
- 通过 GitHub PR 提交并完成评审

---

## 项目概览

<!-- _class: divider -->

---

## 项目简介

**场景：** 为示例制造 MC-5000 CNC 加工中心创建完整的产品文档集

**规模：**
| 组件 | 数量 | 主题类型 |
|------|------|----------|
| 概念主题 | 2 | safety-overview, system-architecture |
| 任务主题 | 3 | installation, daily-operation, maintenance |
| 参考主题 | 2 | technical-specs, parts-catalog |
| 故障排除 | 1 | alarm-codes |
| DITA Map | 1 | 含 keys 定义 |
| Bookmap | 1 | PDF 出版用 |

---

## 项目要求

### 必须包含

- M1-M7 完整元数据
- 基于 key 的内容引用（conkeyref）
- Subject scheme 分类标记
- Bookmap 结构
- 本地化准备标记（`@xml:lang`）

### 评分标准

| 维度 | 权重 | 要求 |
|------|------|------|
| 元数据完整性 | 15% | M1-M7 全部正确 |
| 内容结构 | 20% | 主题类型选择正确 |
| 复用策略 | 20% | conref/conkeyref 使用合理 |
| Map 组织 | 15% | 层级清晰，keys 完整 |
| Bookmap | 10% | 结构完整 |
| 质量 | 20% | 无 XML 错误，内容准确 |

---

## 第一步：规划主题结构

<!-- _class: divider -->

---

## 确定主题类型

分析 MC-5000 的内容需求：

| 内容需求 | 主题类型 | 理由 |
|----------|----------|------|
| 机床安全系统介绍 | Concept | 需要背景解释性内容 |
| 系统架构说明 | Concept | 概述性、原理性内容 |
| 安装步骤 | Task | 分步操作指南 |
| 日常操作 | Task | 操作流程 |
| 定期维护 | Task | 维护步骤 |
| 技术规格 | Reference | 查找型数据 |
| 备件目录 | Reference | 结构化的部件清单 |
| 报警代码 | Troubleshooting | 诊断和排障 |

---

## 第二步：编写概念主题

<!-- _class: divider -->

---

## 概念主题 1：安全系统

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE concept PUBLIC "-//OASIS//DTD DITA Concept//EN" "concept.dtd">
<concept id="dr5000-safety-concept">
  <title>MC-5000 安全系统概述</title>
  <shortdesc>MC-5000 加工中心的多层安全保护体系</shortdesc>
  <prolog>
    <metadata>
      <author>学员姓名</author>
      <critdates>
        <created date="2026-06-07"/>
      </critdates>
      <audience type="operator" job="primary"/>
      <category keyref="product-dr5000"/>
      <othermeta name="doc-type" value="overview"/>
    </metadata>
  </prolog>
  <conbody>
    <section id="safety-architecture">
      <title>安全系统架构</title>
      <p>MC-5000 采用三层安全架构...</p>
    </section>
  </conbody>
</concept>
```

---

## 概念主题 2：系统架构

第二个概念主题应覆盖：

- CNC 控制系统架构
- 主轴驱动系统
- 进给轴系统
- 冷却与润滑系统
- 各子系统的功能描述

> 请确保使用 `<section>` 合理组织内容结构

---

## 第三步：编写任务主题

<!-- _class: divider -->

---

## 任务主题 1：安装程序

任务主题必须包含：

| 元素 | 说明 |
|------|------|
| `<prereq>` | 安装前提条件（地基、电源、吊装设备） |
| `<steps>` | 分步安装说明 |
| `<stepresult>` | 每步的预期结果 |
| `<postreq>` | 安装后的验证步骤 |

---

## 任务主题 2：日常操作

```xml
<task id="dr5000-daily-operation-task">
  <title>MC-5000 日常操作程序</title>
  <taskbody>
    <prereq>
      <p>操作前请穿戴好 PPE（安全眼镜、安全鞋）</p>
    </prereq>
    <steps>
      <step>
        <cmd>打开主电源开关</cmd>
        <stepresult>控制面板指示灯亮起</stepresult>
      </step>
      <step>
        <cmd>执行归零操作（Home）</cmd>
      </step>
      <step>
        <cmd>加载加工程序</cmd>
      </step>
    </steps>
  </taskbody>
</task>
```

---

## 任务主题 3：定期维护

创建维护任务时需包含：

- 维护前的安全准备工作
- 操作人员资质要求（使用 taskreq）
- 分步维护指导
- 维护后的功能验证
- 维护记录填写

> 提示：可引用任务要求模板（T9）来定义维护前提条件

---

## 第四步：编写参考主题

<!-- _class: divider -->

---

## 参考主题 1：技术规格

```xml
<reference id="dr5000-specs-reference">
  <title>MC-5000 技术规格</title>
  <refbody>
    <properties>
      <propgroup name="主轴参数">
        <property>
          <propkey>主轴转速</propkey>
          <propvalue>12,000 RPM</propvalue>
        </property>
        <property>
          <propkey>额定功率</propkey>
          <propvalue>37 kW</propvalue>
        </property>
      </propgroup>
    </properties>
  </refbody>
</reference>
```

---

## 参考主题 2：备件目录

使用 `<simpletable>` 或 `<properties>` 结构：

| 备件编号 | 名称 | 规格 | 数量 |
|----------|------|------|------|
| SP-001 | 主轴轴承 | NSK 7020 | 1 |
| SP-002 | 皮带组 | HTD-8M | 2 |
| SP-003 | 过滤器 | 10μm | 4 |

---

## 第五步：故障排除主题

<!-- _class: divider -->

---

## 报警代码主题

```xml
<troubleshooting id="dr5000-alarm-700016">
  <title>报警 700016：主轴驱动过载</title>
  <troublebody>
    <troublesolution>
      <condition>主轴负载超过额定值 120%</condition>
      <cause>切削参数不当或主轴轴承磨损</cause>
      <remedy>
        <steps>
          <step><cmd>停止当前加工程序</cmd></step>
          <step><cmd>检查切削参数</cmd></step>
        </steps>
      </remedy>
    </troublesolution>
  </troublebody>
</troubleshooting>
```

---

## 第六步：创建 DITA Map

<!-- _class: divider -->

---

## 产品 DITA Map

```xml
<map id="dr5000-product-map" title="MC-5000 产品文档">
  <title>MC-5000 CNC 加工中心文档</title>
  <keydef keys="product-dr5000" href="dr5000-specs-reference.dita"/>
  <topicref href="dr5000-safety-concept.dita" navtitle="安全系统">
    <topicref href="dr5000-installation-task.dita" navtitle="安装"/>
  </topicref>
  <topicref href="dr5000-operation-concept.dita" navtitle="操作">
    <topicref href="dr5000-daily-operation-task.dita"/>
    <topicref href="dr5000-maintenance-task.dita"/>
  </topicref>
  <topicref href="dr5000-specs-reference.dita" navtitle="规格"/>
  <topicref href="dr5000-alarm-700016-ts.dita" navtitle="故障排除"/>
</map>
```

---

## 实现内容复用

<!-- _class: divider -->

---

## 复用策略

| 复用方式 | 示例 | 用途 |
|----------|------|------|
| conref | `<p conref="shared/safety.dita#shared/lockout-tagout"/>` | 通用安全警告 |
| conkeyref | `<p conkeyref="product-dr5000/shortdesc"/>` | 产品名称引用 |
| keyref | `<keyword keyref="company-name"/>` | 公司名称 |

在项目中使用 conkeyref 引用至少一个通用内容片段。

---

## 第七步：创建 Bookmap

<!-- _class: divider -->

---

## 服务手册 Bookmap

```xml
<bookmap id="dr5000-service-manual">
  <booktitle>
    <mainbooktitle>MC-5000 维修手册</mainbooktitle>
  </booktitle>
  <frontmatter>
    <booklist type="toc"/>
    <preface>
      <topicref href="dr5000-safety-concept.dita"/>
    </preface>
  </frontmatter>
  <chapter navtitle="安装">
    <topicref href="dr5000-installation-task.dita"/>
  </chapter>
  <chapter navtitle="维护">
    <topicref href="dr5000-maintenance-task.dita"/>
  </chapter>
  <appendices>
    <topicref href="dr5000-specs-reference.dita"/>
  </appendices>
</bookmap>
```

---

## 第八步：本地化准备

<!-- _class: divider -->

---

## 本地化就绪检查

- [ ] 所有文本内容使用 `@xml:lang="zh-CN"`
- [ ] 代码和文件路径不翻译
- [ ] 图片中的文字单独提取
- [ ] 使用 `<indexterm>` 标记索引条目
- [ ] 添加翻译注释（`<note type="other" othertype="l10n">`）

---

## 第九步：提交与评审

<!-- _class: divider -->

---

## GitHub PR 提交流程

1. 在 feature 分支上创建文档
2. 运行 XML 验证工具
3. 创建 Pull Request
4. 请求同行评审
5. 根据反馈修改
6. 审批通过后合并

---

## 评审检查清单

**内容评审：**
- 主题类型选择是否恰当？
- 步骤是否完整且可操作？
- 内容是否准确无误？

**技术评审：**
- XML 是否结构良好？
- metadata 是否完整（M1-M7）？
- key 引用是否正确？
- conref 路径是否有效？

---

## 评分标准详解

| 维度 | > 90% | > 75% | > 60% |
|------|-------|-------|-------|
| 元数据 | 全部 M1-M7 完整 | 少数遗漏 | 基本框架 |
| 内容质量 | 专业、准确、完整 | 偶有小错误 | 核心内容完整 |
| 复用 | conkeyref 有效使用 | conref 使用 | 基本引用 |
| Map/Bookmap | 结构完美 | 结构合理 | 结构存在 |
| XML 合规 | 完全合规 | 少量警告 | 无严重错误 |

---

## 项目提交材料清单

最终提交应包含：

- [ ] 8 个 DITA 主题文件（2 概念 + 3 任务 + 2 参考 + 1 排障）
- [ ] 1 个 DITA Map 文件
- [ ] 1 个 Bookmap 文件
- [ ] 1 个 Subject Scheme 文件（可选加分）
- [ ] 所有文件通过 XML 验证
- [ ] GitHub PR 链接

**截止时间：** 请与讲师确认具体日期

---

## 课程总结

<!-- _class: divider -->

---

## Professional 级回顾

通过这 8 个模块，你已掌握：

| 模块 | 核心技能 |
|------|----------|
| M01 | 扩展主题类型（排障、术语、安全等） |
| M02 | 内容复用（conref, conkeyref） |
| M03 | Subject Scheme 与受控词表 |
| M04 | Bookmap 与 PDF 发布 |
| M05 | 本地化工作流 |
| M06 | 元数据治理（M1-M7） |
| M07 | 多语言发布工程 |
| M08 | **综合实践项目** ✅ |

---

## 下一步：Expert 级

完成本项目后，你已具备 DITA 进阶能力。

接下来可以进入 **Expert（专家级）** 课程学习：
- DITA 专用化（Specialization）
- CI/CD 流水线
- 内容指标与 KPI
- 团队管理与 CoE
- 框架定制
- 持续改进路线图

---

<!-- _class: title -->

# 祝项目顺利！
## 实践是掌握 DITA 的最佳途径
