---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# DITA 元数据基础
## 让文档可发现、可追溯、可管理

**DITA 课程 | 制造业文档**

---

<!-- _class: divider -->

# 第一章：什么是元数据

---

# 元数据的定义

元数据（Metadata）是"关于数据的数据"。

在 DITA 中，元数据描述了主题的：
- **作者身份**：谁写的、谁审核的
- **时间信息**：创建日期、修订日期
- **分类标签**：主题属于什么产品、什么类别
- **适用范围**：给谁看的、什么情况下适用
- **管理信息**：版本号、审批状态

---

<!-- _class: tip -->

# 制造业类比：工件流程卡

在 CNC 车间，每个工件都附带一张流程卡，上面记录了：
- 工件编号和名称
- 加工工序和操作员
- 检验结果和日期

DITA 的 prolog 元素就是主题的"流程卡"——记录了这个文档的生命周期信息。

---

# 元数据的价值

| 价值 | 说明 | 制造业场景 |
|------|------|-----------|
| 可发现性 | 搜索和过滤 | 按产品型号查找所有相关文档 |
| 可追溯性 | 谁写了什么 | 安全程序必须标注编写者 |
| 合规性 | 满足审计要求 | ISO 9001 要求文档受控 |
| 自动化 | 条件处理和发布 | 根据产品型号自动筛选内容 |
| 质量管理 | 跟踪审阅状态 | 文档是否经过技术审核 |

---

# DITA 元数据的位置

```xml
<topic id="topic_xxx">
  <title>主题标题</title>
  <prolog>           ← 元数据区
    <author/>
    <publisher/>
    <copyright/>
    <critdates/>
    <audience/>
    <prodinfo/>
    <othermeta/>
  </prolog>
  <body>             ← 正文内容
    ...
  </body>
</topic>
```

---

<!-- _class: divider -->

# 第二章：prolog 总览

---

# prolog 的子元素

```xml
<prolog>
  <author type="creator">创建者</author>
  <author type="contributor">贡献者</author>
  <publisher>出版信息</publisher>
  <copyright>版权声明</copyright>
  <critdates>关键日期</critdates>
  <permissions>权限信息</permissions>
  <audience type="maintainer">目标读者</audience>
  <prodinfo>产品信息</prodinfo>
  <othermeta name="status">自定义元数据</othermeta>
  <resourceid>资源标识符</resourceid>
</prolog>
```

---

# prolog 子元素一览

| 元素 | 用途 | 制造业示例 |
|------|------|-----------|
| author | 作者信息 | 编写工程师姓名 |
| publisher | 出版单位 | 制造工程部 |
| copyright | 版权声明 | (c) 2025 某公司 |
| critdates | 关键日期 | 创建/修订日期 |
| permissions | 权限 | 内部使用/保密 |
| audience | 目标读者 | 操作工/维修工 |
| prodinfo | 产品信息 | VMC850 系列 |
| othermeta | 自定义元数据 | 审批状态 |

---

<!-- _class: divider -->

# 第三章：author 元素

---

# author 元素详解

```xml
<author type="creator">
  <person>
    <name>张工</name>
    <email>zhang@manufacturing.com</email>
    <organization>制造工程部</organization>
  </person>
</author>
```

`type` 属性值：
- `creator`：原始作者
- `contributor`：内容贡献者
- `reviewer`：审核者

---

# author 的完整结构

```xml
<author type="creator">
  <person>
    <name>张三</name>
    <email>zhangsan@company.com</email>
    <organization>制造工程部</organization>
  </person>
</author>

<author type="reviewer">
  <person>
    <name>李四</name>
    <email>lisi@company.com</email>
    <organization>技术审核部</organization>
  </person>
</author>
```

---

# 制造业中的 author 最佳实践

- **creator**：实际编写文档的工程师
- **contributor**：提供技术内容的专家（如CNC程序员）
- **reviewer**：技术审核人（通常是资深工程师或主管）
- 多人协作时明确标注每个角色
- 保留审核记录满足 ISO 9001 文档控制要求

---

<!-- _class: divider -->

# 第四章：publisher 和 copyright

---

# publisher 元素

```xml
<publisher>
  <name>某精密制造有限公司</name>
  <organization>
    <namedata>
      <orgname>制造工程部</orgname>
      <address>
        <locality>苏州</locality>
        <country>中国</country>
      </address>
    </namedata>
  </organization>
</publisher>
```

---

# copyright 元素

```xml
<copyright>
  <copyryear year="2025"/>
  <copyrholder>某精密制造有限公司</copyrholder>
</copyright>
```

版权声明要点：
- `year` 属性记录发布年份
- 多年度使用多个 `<copyryear>` 元素
- 敏感文档可附加使用限制

---

# 制造业版权示例

```xml
<copyright>
  <copyryear year="2024"/>
  <copyryear year="2025"/>
  <copyrholder>某精密制造有限公司</copyrholder>
</copyright>
```

对于供应商提供的设备文档：
```xml
<copyright>
  <copyryear year="2023"/>
  <copyrholder>FANUC Corporation</copyrholder>
</copyright>
```

---

<!-- _class: divider -->

# 第五章：critdates 关键日期

---

# critdates 元素

```xml
<critdates>
  <created date="2025-01-15"/>
  <revised modified="2025-03-20"/>
  <expires date="2027-01-15"/>
</critdates>
```

三个子元素：
- `created`：创建日期
- `revised`：最后修改日期
- `expires`：过期日期（可选）

---

# 日期的意义

| 日期类型 | 用途 | 重要性 |
|---------|------|--------|
| created | 追溯文档历史 | ISO 审计必需 |
| revised | 版本控制 | 确保用户使用最新版 |
| expires | 定期审核提醒 | 避免使用过时信息 |

---

# 制造业日期规范

```xml
<!-- CNC 安全程序 -->
<critdates>
  <created date="2025-06-01"/>
  <revised modified="2025-08-15"/>
  <!-- 安全规程每年复审 -->
  <expires date="2026-06-01"/>
</critdates>
```

安全规程必须定期复审——`expires` 日期强制执行这一要求。

---

<!-- _class: divider -->

# 第六章：audience 元素

---

# audience 元素

```xml
<audience type="maintainer" job="service-technician"
          experiencelevel="expert">
  <personname>机床维修技师</personname>
</audience>
```

属性：
- `type`：读者角色（user, purchaser, administrator, maintainer, programmer）
- `job`：具体职务
- `experiencelevel`：所需经验水平（novice, general, expert）
- `product`：关联产品
- `platform`：平台

---

# 制造业受众定义示例

```xml
<!-- 操作工 -->
<audience type="user" job="cnc-operator"
          experiencelevel="general">
  <personname>CNC 操作工</personname>
</audience>

<!-- 维修技师 -->
<audience type="maintainer" job="service-technician"
          experiencelevel="expert">
  <personname>设备维修技师</personname>
</audience>

<!-- 编程员 -->
<audience type="programmer" job="cnc-programmer"
          experiencelevel="expert">
  <personname>CNC 编程员</personname>
</audience>
```

---

# 受众与安全的关联

```xml
<audience type="user" job="cnc-operator"
          experiencelevel="general">
  <personname>CNC 操作工</personname>
</audience>
<audience type="maintainer" job="electrician"
          experiencelevel="expert">
  <personname>持证电工</personname>
  <!-- 电气维修文档必须注明 -->
  <!-- 只有持证电工可以操作 -->
</audience>
```

某些安全程序只能由特定资质的受众执行——在元数据中明确标注。

---

<!-- _class: divider -->

# 第七章：prodinfo 产品信息

---

# prodinfo 元素

```xml
<prodinfo>
  <prodname>VMC850 立式加工中心</prodname>
  <vrmlist>
    <vrm version="2025 R1" release="1.0"
         modification="A"/>
  </vrmlist>
  <brand>某精密制造</brand>
  <series>VMC 系列</series>
  <component>主轴</component>
  <feature>BT40 刀柄接口</feature>
</prodinfo>
```

---

# prodinfo 子元素说明

| 子元素 | 说明 | 示例 |
|--------|------|------|
| prodname | 产品名称 | VMC850 立式加工中心 |
| vrmlist/vrm | 版本发布信息 | 2025 R1 |
| brand | 品牌 | 某精密制造 |
| series | 产品系列 | VMC 系列 |
| component | 所属组件 | 主轴组件 |
| feature | 功能特性 | BT40 刀柄接口 |

---

# 产品信息与条件处理

```xml
<prodinfo>
  <prodname>VMC 系列立式加工中心</prodname>
  <vrmlist>
    <vrm version="Standard"/>
    <vrm version="High-Speed" modification="HS"/>
  </vrmlist>
</prodinfo>
```

通过 `vrmlist` 可以管理同一产品的多个版本变体。发布时可以根据版本号筛选相关内容。

---

<!-- _class: divider -->

# 第八章：othermeta 自定义元数据

---

# othermeta 元素

```xml
<othermeta name="status" content="draft"/>
<othermeta name="reviewer" content="李工"/>
<othermeta name="lang" content="zh-CN"/>
<othermeta name="doc-id" content="DOC-VMC850-2025-001"/>
<othermeta name="security" content="internal"/>
<othermeta name="department" content="制造工程部"/>
```

`name` 和 `content` 是键值对形式，可以定义任意元数据。

---

# 制造业 othermeta 常用字段

| name | content 示例 | 用途 |
|------|-------------|------|
| status | draft / review / approved | 文档审批状态 |
| doc-id | DOC-VMC850-2025-001 | 内部文档编号 |
| security | public / internal / confidential | 安全级别 |
| department | 制造工程部 | 负责部门 |
| equipment | VMC850 | 关联设备 |
| safety-level | general / warning / critical | 安全等级 |

---

# 安全文档的 othermeta 示例

```xml
<prolog>
  <othermeta name="status" content="approved"/>
  <othermeta name="doc-id" content="SAF-VMC850-001"/>
  <othermeta name="security" content="internal"/>
  <othermeta name="safety-level" content="critical"/>
  <othermeta name="review-cycle" content="annual"/>
  <othermeta name="compliance" content="GB/T 15706-2012"/>
  <othermeta name="compliance" content="ISO 12100:2010"/>
</prolog>
```

---

<!-- _class: divider -->

# 第九章：元数据验证规则 (M1-M7)

---

# M1-M7 验证规则概览

| 规则 | 名称 | 检查内容 |
|------|------|---------|
| M1 | 必填作者 | 至少有 creator 类型 author |
| M2 | 必填日期 | critdates 包含 created |
| M3 | 必填受众 | 至少定义一个 audience |
| M4 | 版权声明 | 包含 copyright 元素 |
| M5 | 文档编号 | othermeta 包含 doc-id |
| M6 | 状态标记 | othermeta 包含 status |
| M7 | 产品关联 | prodinfo 不空 |

---

# M1: 必填作者

**规则**：每个主题必须至少有一个 `type="creator"` 的 author。

```xml
<!-- 通过检查 -->
<prolog>
  <author type="creator">
    <person><name>张工</name></person>
  </author>
</prolog>

<!-- 不通过检查 — 缺少 creator -->
<prolog>
  <author type="contributor">
    <person><name>李工</name></person>
  </author>
</prolog>
```

---

# M2: 必填日期

**规则**：`critdates` 必须包含 `created` 子元素，日期格式为 YYYY-MM-DD。

```xml
<!-- 通过 -->
<critdates>
  <created date="2025-06-15"/>
</critdates>

<!-- 不通过 — 缺少 created -->
<critdates>
  <revised modified="2025-06-15"/>
</critdates>
```

---

# M3: 必填受众

**规则**：至少定义一个 `audience` 元素。

```xml
<!-- 通过 -->
<audience type="user" job="cnc-operator"
          experiencelevel="general"/>

<!-- 不通过 — 缺少 audience -->
<prolog>
  <!-- 其他元数据... -->
</prolog>
```

---

# M4: 版权声明

**规则**：必须包含 `copyright` 元素，且有 `copyryear` 和 `copyrholder`。

```xml
<!-- 通过 -->
<copyright>
  <copyryear year="2025"/>
  <copyrholder>某精密制造有限公司</copyrholder>
</copyright>

<!-- 不通过 — 缺少 copyrholder -->
<copyright>
  <copyryear year="2025"/>
</copyright>
```

---

# M5: 文档编号

**规则**：`othermeta` 中必须包含 `name="doc-id"` 的条目。

```xml
<!-- 通过 -->
<othermeta name="doc-id" content="DOC-VMC850-2025-001"/>

<!-- 不通过 — 缺少 doc-id -->
<othermeta name="status" content="draft"/>
```

---

# M6: 状态标记

**规则**：`othermeta` 中必须包含 `name="status"` 的条目。

```xml
<othermeta name="status" content="draft"/>
```

合法值：
- `draft` — 草稿
- `review` — 审核中
- `approved` — 已批准
- `published` — 已发布
- `obsolete` — 已废弃

---

# M7: 产品关联

**规则**：`prodinfo` 必须非空，且至少包含 `prodname`。

```xml
<!-- 通过 -->
<prodinfo>
  <prodname>VMC850 立式加工中心</prodname>
</prodinfo>

<!-- 不通过 — prodinfo 为空 -->
<prodinfo></prodinfo>
```

---

# M1-M7 规则速查

| 规则 | 必填元素 | 最小要求 |
|------|---------|---------|
| M1 | author type="creator" | 1个 |
| M2 | critdates/created | 1个 |
| M3 | audience | 1个 |
| M4 | copyright + copyryear + copyrholder | 1组 |
| M5 | othermeta name="doc-id" | 1个 |
| M6 | othermeta name="status" | 1个 |
| M7 | prodinfo/prodname | 1个 |

---

<!-- _class: divider -->

# 第十章：概念主题的元数据示例

---

# 完整的概念主题 prolog

```xml
<concept id="concept_vmc850_safety">
  <title>VMC850 安全操作原则</title>
  <prolog>
    <author type="creator">
      <person><name>张安全</name>
        <email>zhanganquan@company.com</email>
        <organization>安全工程部</organization>
      </person>
    </author>
    <author type="reviewer">
      <person><name>李主管</name></person>
    </author>
    <publisher><name>安全工程部</name></publisher>
    <copyright>
      <copyryear year="2025"/>
      <copyrholder>某精密制造有限公司</copyrholder>
    </copyright>
    <critdates>
      <created date="2025-04-10"/>
      <revised modified="2025-06-01"/>
      <expires date="2026-04-10"/>
    </critdates>
    <audience type="user" job="cnc-operator"
              experiencelevel="general">
      <personname>CNC 操作工</personname>
    </audience>
    <prodinfo>
      <prodname>VMC850</prodname>
      <series>VMC</series>
    </prodinfo>
    <othermeta name="status" content="approved"/>
    <othermeta name="doc-id" content="SAF-VMC850-001"/>
    <othermeta name="safety-level" content="critical"/>
  </prolog>
  <conbody>...</conbody>
</concept>
```

---

# 安全检查清单（概念主题元数据）

- [ ] author type="creator" 有姓名和部门
- [ ] author type="reviewer" 已指定审核人
- [ ] copyright 包含年份和权利人
- [ ] created 日期格式正确
- [ ] audience 类型匹配内容
- [ ] prodinfo 关联正确产品
- [ ] status 是 approved
- [ ] safety-level 标记为 critical

---

<!-- _class: divider -->

# 第十一章：任务主题的元数据示例

---

# 完整的任务主题 prolog

```xml
<task id="task_vmc850_emergency_stop">
  <title>VMC850 紧急停机程序</title>
  <prolog>
    <author type="creator">
      <person><name>王操作</name>
        <organization>生产部</organization>
      </person>
    </author>
    <author type="reviewer">
      <person><name>赵安全</name>
        <organization>安全工程部</organization>
      </person>
    </author>
    <critdates>
      <created date="2025-05-20"/>
      <revised modified="2025-06-10"/>
      <expires date="2026-05-20"/>
    </critdates>
    <audience type="user" job="cnc-operator"
              experiencelevel="novice">
      <personname>所有操作人员</personname>
    </audience>
    <audience type="maintainer" job="shift-supervisor"
              experiencelevel="expert">
      <personname>值班主管</personname>
    </audience>
    <prodinfo>
      <prodname>VMC850</prodname>
    </prodinfo>
    <othermeta name="status" content="approved"/>
    <othermeta name="doc-id" content="TASK-VMC850-EM-STOP"/>
    <othermeta name="safety-level" content="critical"/>
    <othermeta name="practice-required" content="monthly"/>
  </prolog>
  <taskbody>...</taskbody>
</task>
```

---

# 关键点

<!-- _class: keypoint -->

任务主题的元数据特点：
- 通常面向 **novice/general** 水平读者
- 安全关键任务必须标注 `safety-level: critical`
- 多读者时可定义多个 `audience`
- `practice-required` 自定义字段记录演练频率

---

<!-- _class: divider -->

# 第十二章：参考主题的元数据示例

---

# 完整的参考主题 prolog

```xml
<reference id="ref_vmc850_tech_specs">
  <title>VMC850 技术规格参数</title>
  <prolog>
    <author type="creator">
      <person><name>钱设计</name>
        <organization>研发部</organization>
      </person>
    </author>
    <author type="contributor">
      <person><name>孙测试</name>
        <organization>质量部</organization>
      </person>
    </author>
    <copyright>
      <copyryear year="2025"/>
      <copyrholder>某精密制造有限公司</copyrholder>
    </copyright>
    <critdates>
      <created date="2025-03-01"/>
      <revised modified="2025-07-15"/>
    </critdates>
    <audience type="user" job="sales-engineer"
              experiencelevel="general">
      <personname>销售工程师</personname>
    </audience>
    <audience type="user" job="cnc-programmer"
              experiencelevel="expert">
      <personname>CNC 编程员</personname>
    </audience>
    <prodinfo>
      <prodname>VMC850</prodname>
      <vrmlist>
        <vrm version="Standard"/>
        <vrm version="HS" modification="高速型"/>
      </vrmlist>
    </prodinfo>
    <othermeta name="status" content="published"/>
    <othermeta name="doc-id" content="REF-VMC850-SPEC-001"/>
    <othermeta name="data-source" content="研发部测试报告"/>
  </prolog>
  <refbody>...</refbody>
</reference>
```

---

# 参考主题元数据特点

- 通常与测试报告、设计文档关联
- `contributor` 标注数据提供者
- `vrmlist` 区分产品版本
- `data-source` 追溯数据来源
- 面向多种受众（销售、编程、维护）

---

<!-- _class: divider -->

# 第十三章：元数据模板化

---

# 在不同模板中使用元数据

各模板都已预置 prolog 骨架：

- **T1 concept-basic**：基础 prolog（author, copyright, critdates）
- **T2 concept-extended**：完整 prolog（含 audience, prodinfo）
- **T4 task-basic**：基础 prolog
- **T5 task-extended**：完整 prolog + othermeta
- **T7 reference-basic**：基础 prolog
- **T9 reference-specification**：完整 prolog + vrmlist

---

# 复制模板后填充元数据

复制模板后按此顺序填充：

1. 填写 author（creator + reviewer）
2. 填写 copyright（年份 + 公司）
3. 填写 critdates（创建日期）
4. 确认 audience 与内容匹配
5. 填写 prodinfo（产品名称）
6. 添加 othermeta（doc-id, status）
7. 检查 M1-M7 是否全部满足

---

# 元数据填充顺序图

```
author → copyright → critdates → audience
    ↓
prodinfo → othermeta → 验证M1-M7
```

---

<!-- _class: divider -->

# 第十四章：元数据与搜索/导航

---

# 元数据驱动的搜索

DITA 发布系统可利用元数据建立搜索索引：

| 元数据字段 | 搜索用途 |
|-----------|---------|
| prodname | 按产品搜索 |
| audience/@type | 按角色筛选 |
| othermeta status | 只搜索已发布内容 |
| othermeta doc-id | 精确查找 |
| author/person/name | 按作者搜索 |

---

# 元数据过滤示例

在发布系统中可实现：

```
只显示 VMC850 相关的文档
只显示状态为 "approved" 的文档
只显示面向操作工的文档
只显示张三编写的文档
```

---

# 制造业搜索场景

**场景**：维修技师需要查找 VMC850 主轴相关的已审核维修程序。

查询条件：
- `prodname = VMC850`
- `component = 主轴`
- `audience/type = maintainer`
- `status = approved`

这些条件都在元数据中定义。

---

<!-- _class: divider -->

# 第十五章：元数据常见问题

---

# 问题 1：忘记更新 status

```xml
<!-- 已发布的文档仍标记为 draft -->
<othermeta name="status" content="draft"/>  <!-- 错误！ -->
```

**解决**：发布前务必更新 status 为 `approved` 或 `published`。

---

# 问题 2：audience 太笼统

```xml
<!-- 不精确 -->
<audience type="user" experiencelevel="general"/>

<!-- 精确 -->
<audience type="maintainer" job="service-technician"
          experiencelevel="expert">
  <personname>设备维修技师（需持电工证）</personname>
</audience>
```

---

# 问题 3：缺少 expires 日期

安全文档和操作规程应设置到期日：

```xml
<!-- 安全规程缺少到期日 -->
<critdates>
  <created date="2025-01-01"/>
</critdates>

<!-- 正确 -->
<critdates>
  <created date="2025-01-01"/>
  <expires date="2026-01-01"/>
</critdates>
```

ISO 9001 要求文档定期复审。

---

# 问题 4：doc-id 格式不一致

```xml
<!-- 格式不统一 -->
<othermeta name="doc-id" content="DOC001"/>
<othermeta name="doc-id" content="VMC850-safety-guide"/>
<othermeta name="doc-id" content="task_2025_06"/>
```

**解决**：制定统一的编号规范

```
DOC-{产品}-{年份}-{序号}
TASK-{产品}-{关键词}
SAF-{产品}-{序号}
```

---

<!-- _class: divider -->

# 第十六章：制造业元数据最佳实践

---

# 安全文档专属元数据

```xml
<othermeta name="safety-level" content="critical"/>
<othermeta name="compliance" content="GB/T 15706-2012"/>
<othermeta name="compliance" content="ISO 12100:2010"/>
<othermeta name="competency" content="certified-electrician"/>
<othermeta name="review-cycle" content="annual"/>
<othermeta name="hazard-type" content="high-voltage"/>
```

---

# 操作程序专属元数据

```xml
<othermeta name="procedure-type" content="standard-operating"/>
<othermeta name="estimated-time" content="15min"/>
<othermeta name="tools-required" content="torque-wrench, dial-gauge"/>
<othermeta name="ppe-required" content="safety-glasses, gloves"/>
<othermeta name="practice-frequency" content="monthly"/>
```

---

# 元数据完整性检查清单

- [ ] M1: 有 creator 类型 author
- [ ] M2: 有 created 日期
- [ ] M3: 至少一个 audience
- [ ] M4: 有完整 copyright
- [ ] M5: 有 doc-id
- [ ] M6: 有 status
- [ ] M7: prodinfo 非空
- [ ] 安全文档: safety-level 已设置
- [ ] expires 日期已设置（安全/程序文档）

---

<!-- _class: divider -->

# 第十七章：动手练习

---

<!-- _class: exercise -->

# 练习 1：补全元数据

给以下不完整的 prolog 补充缺失的元数据：

```xml
<prolog>
  <author type="creator">
    <person><name>刘工</name></person>
  </author>
  <critdates>
    <created date="2025-08-01"/>
  </critdates>
  <!-- 请补充：copyright, audience, prodinfo, othermeta -->
</prolog>
```

---

<!-- _class: exercise -->

# 练习 2：安全文档元数据

为"VMC850 高压电气维护安全程序"编写完整的 prolog。

要求：
- 作者是电气工程师
- 审核人是安全主管
- 受众是持证电工
- 标注安全等级为 critical
- 标注相关合规标准
- 满足 M1-M7 全部规则

---

<!-- _class: exercise -->

# 练习 3：元数据验证

检查以下 prolog，找出所有不符合 M1-M7 的地方：

```xml
<prolog>
  <author type="contributor">
    <person><name>张三</name></person>
  </author>
  <copyright>
    <copyryear year="2025"/>
  </copyright>
  <critdates>
    <revised modified="2025-06-01"/>
  </critdates>
</prolog>
```

---

<!-- _class: exercise -->

# 练习 4：多受众文档

为一份同时面向操作工和维修技师的主题编写 prolog。

要求：
- 定义两个 audience 元素
- 操作工: type=user, job=cnc-operator, experiencelevel=general
- 维修技师: type=maintainer, job=service-technician, experiencelevel=expert
- 满足 M1-M7

---

# 练习 1 参考答案

```xml
<prolog>
  <author type="creator">
    <person><name>刘工</name></person>
  </author>
  <author type="reviewer">
    <person><name>主管</name></person>
  </author>
  <copyright>
    <copyryear year="2025"/>
    <copyrholder>某精密制造有限公司</copyrholder>
  </copyright>
  <critdates>
    <created date="2025-08-01"/>
  </critdates>
  <audience type="user" job="cnc-operator"
            experiencelevel="general"/>
  <prodinfo>
    <prodname>VMC850</prodname>
  </prodinfo>
  <othermeta name="status" content="draft"/>
  <othermeta name="doc-id" content="DOC-VMC850-2025-008"/>
</prolog>
```

---

# 练习 2 参考答案

```xml
<prolog>
  <author type="creator">
    <person><name>陈电气</name>
      <organization>电气工程部</organization>
    </person>
  </author>
  <author type="reviewer">
    <person><name>赵安全</name>
      <organization>安全工程部</organization>
    </person>
  </author>
  <copyright>
    <copyryear year="2025"/>
    <copyrholder>某精密制造有限公司</copyrholder>
  </copyright>
  <critdates>
    <created date="2025-08-15"/>
    <expires date="2026-08-15"/>
  </critdates>
  <audience type="maintainer" job="electrician"
            experiencelevel="expert">
    <personname>持证电工</personname>
  </audience>
  <prodinfo>
    <prodname>VMC850</prodname>
    <component>电气系统</component>
  </prodinfo>
  <othermeta name="status" content="review"/>
  <othermeta name="doc-id" content="SAF-VMC850-ELEC-001"/>
  <othermeta name="safety-level" content="critical"/>
  <othermeta name="compliance" content="GB/T 5226.1-2019"/>
  <othermeta name="competency" content="certified-electrician"/>
</prolog>
```

---

# 练习 3 参考答案

不符合项：

1. **M1 不满足**：author type="contributor"，缺少 type="creator"
2. **M4 不满足**：copyright 缺少 `<copyrholder>`
3. **M2 不满足**：critdates 缺少 `<created>`，只有 `<revised>`
4. **M3 不满足**：缺少 audience
5. **M5 不满足**：缺少 othermeta doc-id
6. **M6 不满足**：缺少 othermeta status
7. **M7 不满足**：缺少 prodinfo

---

# 练习 4 参考答案

```xml
<prolog>
  <author type="creator">
    <person><name>王工</name></person>
  </author>
  <copyright>
    <copyryear year="2025"/>
    <copyrholder>某精密制造有限公司</copyrholder>
  </copyright>
  <critdates>
    <created date="2025-08-20"/>
  </critdates>
  <audience type="user" job="cnc-operator"
            experiencelevel="general">
    <personname>CNC 操作工</personname>
  </audience>
  <audience type="maintainer" job="service-technician"
            experiencelevel="expert">
    <personname>设备维修技师</personname>
  </audience>
  <prodinfo>
    <prodname>VMC850</prodname>
  </prodinfo>
  <othermeta name="status" content="draft"/>
  <othermeta name="doc-id" content="DOC-VMC850-2025-009"/>
</prolog>
```

---

<!-- _class: divider -->

# 第十八章：总结

---

<!-- _class: keypoint -->

# 本章要点

1. prolog 是 DITA 的元数据容器，记录文档生命周期信息
2. M1-M7 是元数据质量的最低验证标准
3. author, copyright, critdates, audience, prodinfo, othermeta 六要素
4. 安全文档需要额外的安全相关元数据
5. 元数据驱动搜索、过滤和条件发布
6. 始终保持 status 和 doc-id 的更新

---

# 元数据工作流

```
创建主题 → 填充 prolog → 保存
    ↓
运行 M1-M7 检查 → 修正问题
    ↓
提交审核 → 更新 status → 发布
```

---

# 下节预告

下一课我们将学习 DITA Map 的基础知识——如何将多个主题组织成完整的文档结构。

---

<!-- _class: title -->

# 谢谢
## 下一课：DITA Map 基础

**DITA 课程 | 制造业文档**
