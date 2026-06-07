---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# 使用 DITA 模板
## 快速构建制造业文档

**DITA 课程 | 制造业文档**

---

<!-- _class: divider -->

# 第一章：为什么需要模板？

---

# 模板的价值

- **一致性保证**：所有文档遵循相同结构
- **效率提升**：无需从空白开始，直接填充内容
- **合规保障**：内置行业标准和企业规范
- **降低门槛**：新人也能快速上手
- **质量可控**：预先定义好的结构减少错误

---

<!-- _class: tip -->

# 类比：CNC 加工中的 G 代码模板

在制造业中，CNC 操作员不会每次都从零编写 G 代码。他们使用经过验证的程序模板，只需修改参数即可。

DITA 模板就是"文档的 G 代码模板"——你改变的是内容，结构已被证明是可靠的。

---

# 模板 vs 从头编写

| 方面 | 使用模板 | 从头编写 |
|------|---------|---------|
| 时间 | 10-20 分钟 | 2-4 小时 |
| 一致性 | 高度统一 | 因人而异 |
| 错误率 | 低（已验证） | 高（漏字段） |
| 学习曲线 | 平缓 | 陡峭 |
| 协作友好 | 是 | 否 |

---

# DITA 模板体系的层次

```
templates/zh-CN/
├── concept/          ← 概念模板
│   ├── t1-concept-basic.dita
│   ├── t2-concept-extended.dita
│   └── t3-concept-troubleshooting.dita
├── task/             ← 任务模板
│   ├── t4-task-basic.dita
│   ├── t5-task-extended.dita
│   └── t6-task-troubleshooting.dita
├── reference/        ← 参考模板
│   ├── t7-reference-basic.dita
│   ├── t8-reference-extended.dita
│   └── t9-reference-specification.dita
└── bookmap/          ← 书册模板
    ├── t10-bookmap-product.dita
    ├── t11-bookmap-training.dita
    └── t12-bookmap-maintenance.dita
```

---

# 12 个模板编号体系

| 编号 | 模板名称 | 类型 |
|------|---------|------|
| T1 | concept-basic | 基础概念 |
| T2 | concept-extended | 扩展概念 |
| T3 | concept-troubleshooting | 故障排除概念 |
| T4 | task-basic | 基础任务 |
| T5 | task-extended | 扩展任务 |
| T6 | task-troubleshooting | 故障排除任务 |
| T7 | reference-basic | 基础参考 |
| T8 | reference-extended | 扩展参考 |
| T9 | reference-specification | 规格参数参考 |
| T10 | bookmap-product | 产品书册 |
| T11 | bookmap-training | 培训书册 |
| T12 | bookmap-maintenance | 维护书册 |

---

<!-- _class: divider -->

# 第二章：模板选择决策

---

<!-- _class: keypoint -->

# 核心问题：我该用哪个模板？

选择模板的第一步是确定你的内容属于哪种信息类型：

1. **解释概念和原理** → 概念模板 (T1-T3)
2. **指导操作步骤** → 任务模板 (T4-T6)
3. **提供参考数据** → 参考模板 (T7-T9)
4. **组织整本手册** → 书册模板 (T10-T12)

---

# 模板选择决策树

```
你要写什么？
├── 解释"是什么"/"为什么"
│   ├── 简单概念（1页内）→ T1 concept-basic
│   ├── 复杂概念（多节）→ T2 concept-extended
│   └── 故障排查思路   → T3 concept-troubleshooting
├── 指导"怎么做"
│   ├── 简单步骤（≤5步） → T4 task-basic
│   ├── 复杂步骤（多条件）→ T5 task-extended
│   └── 故障修复步骤     → T6 task-troubleshooting
├── 列出参数/规格
│   ├── 简单表格         → T7 reference-basic
│   ├── 复杂多表         → T8 reference-extended
│   └── 技术规格参数     → T9 reference-specification
└── 组织多主题成书
    ├── 产品手册         → T10 bookmap-product
    ├── 培训教材         → T11 bookmap-training
    └── 维修手册         → T12 bookmap-maintenance
```

---

# 制造业场景：选择练习

**场景 A**：你需要写一份 CNC 主轴更换的步骤说明，共 12 步，其中 3 步有安全警告。

应该选哪个模板？

---

<!-- _class: note -->

# 场景 A 答案

应选 **T5 task-extended**：
- 是操作步骤 → task 类型
- 超过 5 步 → extended
- 有安全警告需要 special 元素 → extended 支持

---

# 制造业场景：选择练习 (2)

**场景 B**：你需要写一份 CNC 机床的技术参数表，包含工作台尺寸、主轴转速、刀库容量、冷却系统参数。

应该选哪个模板？

---

<!-- _class: note -->

# 场景 B 答案

应选 **T9 reference-specification**：
- 是数据和参数 → reference 类型
- 需要规范化的参数表格 → specification
- 包含多组技术数据 → T9 专为此设计

---

<!-- _class: divider -->

# 第三章：概念模板详解 (T1-T3)

---

# T1: concept-basic 结构

```xml
<concept id="concept_xxx">
  <title>概念标题</title>
  <shortdesc>一句话概述</shortdesc>
  <conbody>
    <p>核心解释段落</p>
    <p>补充说明段落</p>
    <note type="tip">使用提示</note>
  </conbody>
</concept>
```

适用场景：简单概念（1页内讲清楚）

---

# T1 制造业示例：什么是 CNC

```xml
<concept id="concept_cnc_overview">
  <title>CNC 加工中心概述</title>
  <shortdesc>CNC（计算机数控）加工中心
  是通过预编程计算机软件控制加工工具
  的自动化机床。</shortdesc>
  <conbody>
    <p>CNC 加工中心是现代制造业的核心设备。
    它通过 G 代码/M 代码指令精确控制
    刀具运动轨迹，实现高精度加工。</p>
    <note type="tip">常见的 CNC 系统包括
    FANUC、Siemens、Heidenhain。</note>
  </conbody>
</concept>
```

---

# T2: concept-extended 结构

```xml
<concept id="concept_xxx">
  <title>概念标题</title>
  <shortdesc>概述</shortdesc>
  <prolog><!-- 元数据 --></prolog>
  <conbody>
    <section><title>背景</title>...</section>
    <section><title>原理</title>...</section>
    <section><title>应用场景</title>...</section>
    <section><title>注意事项</title>...</section>
    <example>示例内容</example>
  </conbody>
  <related-links>...</related-links>
</concept>
```

适用场景：需要分节讲解的复杂概念

---

# T2 制造业示例：切削参数

```xml
<concept id="concept_cutting_params">
  <title>CNC 切削参数选择原理</title>
  <shortdesc>理解主轴转速、进给率和
  切削深度之间的数学关系。</shortdesc>
  <conbody>
    <section><title>基本公式</title>
      <p>Vc = π × D × n / 1000</p>
    </section>
    <section><title>材料影响</title>
      <p>不同材料的推荐切削速度...</p>
    </section>
    <section><title>刀具直径的影响</title>
      <p>直径越大，线速度越高...</p>
    </section>
  </conbody>
</concept>
```

---

# T3: concept-troubleshooting 结构

```xml
<concept id="concept_xxx">
  <title>故障排除概念</title>
  <shortdesc>概述</shortdesc>
  <conbody>
    <section><title>问题现象</title>...</section>
    <section><title>可能原因</title>...</section>
    <section><title>排查思路</title>...</section>
    <section><title>预防措施</title>...</section>
  </conbody>
</concept>
```

适用场景：描述某类问题的排查逻辑

---

# T3 制造业示例：主轴振动

```xml
<concept id="concept_spindle_vibration">
  <title>CNC 主轴异常振动分析</title>
  <shortdesc>主轴振动是加工精度下降
  的常见原因，需要系统分析。</shortdesc>
  <conbody>
    <section><title>典型症状</title>
      <ul><li>工件表面粗糙度不达标</li>
      <li>刀具寿命明显缩短</li></ul>
    </section>
    <section><title>常见原因</title>
      <ul><li>轴承磨损</li>
      <li>刀具动平衡不良</li></ul>
    </section>
  </conbody>
</concept>
```

---

# 概念模板对比

| 特性 | T1 basic | T2 extended | T3 troubleshooting |
|------|---------|-------------|-------------------|
| section | 不支持 | 支持（4节） | 支持（4节） |
| example | 不支持 | 支持 | 不支持 |
| prolog | 可选 | 推荐 | 推荐 |
| related-links | 可选 | 推荐 | 推荐 |
| 内容长度 | 1屏 | 3-5屏 | 2-4屏 |

---

<!-- _class: divider -->

# 第四章：任务模板详解 (T4-T6)

---

# T4: task-basic 结构

```xml
<task id="task_xxx">
  <title>任务标题</title>
  <shortdesc>一句话说明</shortdesc>
  <taskbody>
    <prereq>前置条件</prereq>
    <context>背景说明</context>
    <steps>
      <step><cmd>步骤1</cmd></step>
      <step><cmd>步骤2</cmd></step>
    </steps>
    <result>预期结果</result>
    <postreq>后续操作</postreq>
  </taskbody>
</task>
```

适用场景：5步以内的简单操作

---

# T4 制造业示例：更换刀具

```xml
<task id="task_tool_change">
  <title>更换 CNC 铣刀</title>
  <shortdesc>安全更换加工中心铣刀
  的标准操作程序。</shortdesc>
  <taskbody>
    <prereq>机床已停止，主轴已归位。
    佩戴防护手套。</prereq>
    <context>当刀具磨损或需要更换
    不同规格刀具时执行此操作。</context>
    <steps>
      <step><cmd>按下急停按钮确认安全</cmd></step>
      <step><cmd>使用扳手松开刀柄</cmd></step>
      <step><cmd>取出旧刀具，放入刀架</cmd></step>
      <step><cmd>装入新刀具并拧紧</cmd></step>
      <step><cmd>释放急停，执行刀具校准</cmd></step>
    </steps>
    <result>新刀具安装完毕，可正常加工。</result>
  </taskbody>
</task>
```

---

# T5: task-extended 结构

```xml
<task id="task_xxx">
  <title>任务标题</title>
  <shortdesc>概述</shortdesc>
  <prolog><!-- 元数据 --></prolog>
  <taskbody>
    <prereq>前置条件</prereq>
    <context>背景</context>
    <steps>
      <step><cmd>步骤</cmd>
        <info>补充信息</info>
        <stepresult>预期结果</stepresult>
      </step>
      <step importance="urgent">
        <cmd>安全关键步骤</cmd>
        <warning type="danger">安全警告</warning>
      </step>
    </steps>
    <example>示例</example>
    <result>最终结果</result>
    <postreq>后续</postreq>
  </taskbody>
</task>
```

适用场景：超过5步或有安全警告的复杂操作

---

# T5 制造业示例：主轴更换

```xml
<task id="task_spindle_replacement">
  <title>更换 CNC 加工中心主轴</title>
  <taskbody>
    <prereq>切断总电源，挂"禁止合闸"牌。
    准备扭力扳手、千分表、吊具。</prereq>
    <steps>
      <step><cmd>断开主轴冷却管路</cmd>
        <info>使用容器接住残留冷却液</info>
      </step>
      <step importance="urgent">
        <cmd>拆卸主轴固定螺栓（共8颗M12）</cmd>
        <warning type="danger">主轴重约45kg，
        必须使用吊具。禁止人力搬运！</warning>
      </step>
      <step><cmd>安装新主轴并预紧螺栓</cmd>
        <info>预紧扭矩：80 N·m</info>
      </step>
      <step><cmd>使用扭力扳手终紧至120 N·m</cmd></step>
      <step><cmd>安装千分表检查径向跳动</cmd>
        <stepresult>跳动 ≤ 0.005mm 为合格</stepresult>
      </step>
    </steps>
  </taskbody>
</task>
```

---

# T6: task-troubleshooting 结构

```xml
<task id="task_troubleshoot_xxx">
  <title>故障修复步骤</title>
  <taskbody>
    <prereq>相关背景知识</prereq>
    <context>故障环境</context>
    <steps>
      <step>
        <cmd>检查项</cmd>
        <choices>多种可能</choices>
      </step>
    </steps>
  </taskbody>
</task>
```

适用场景：需要分叉判断的故障修复步骤

---

# step 元素的关键属性

| 属性 | 值 | 用途 | 示例 |
|------|-----|------|------|
| importance | optional | 可选步骤 | 可跳过 |
| importance | required | 必需步骤 | 默认 |
| importance | urgent | 安全关键 | 必须执行 |
| props | 条件属性 | 产品筛选 | 仅A型 |

---

# 警告声明类型

```xml
<!-- 危险警告 -->
<warning type="danger">可能导致死亡或重伤</warning>

<!-- 重要警告 -->
<warning type="warning">可能导致设备损坏</warning>

<!-- 注意事项 -->
<note type="caution">可能导致加工误差</note>

<!-- 使用技巧 -->
<note type="tip">提高效率的建议</note>

<!-- 补充信息 -->
<note type="info">背景信息</note>
```

---

<!-- _class: divider -->

# 第五章：参考模板详解 (T7-T9)

---

# T7: reference-basic 结构

```xml
<reference id="reference_xxx">
  <title>参考标题</title>
  <shortdesc>概述</shortdesc>
  <refbody>
    <section><title>项目1</title>...</section>
    <section><title>项目2</title>...</section>
    <table>...</table>
  </refbody>
</reference>
```

适用场景：简单的参考数据，1-2个表格

---

# T7 制造业示例：刀具清单

```xml
<reference id="ref_tool_inventory">
  <title>CNC 铣床标准刀具清单</title>
  <refbody>
    <table>
      <title>常用铣刀规格</title>
      <tgroup cols="4">
        <colspec colname="c1"/>
        <thead>
          <row>
            <entry>刀具类型</entry>
            <entry>直径(mm)</entry>
            <entry>材料</entry>
            <entry>用途</entry>
          </row>
        </thead>
        <tbody>
          <row>
            <entry>立铣刀</entry>
            <entry>10</entry>
            <entry>硬质合金</entry>
            <entry>粗加工</entry>
          </row>
        </tbody>
      </tgroup>
    </table>
  </refbody>
</reference>
```

---

# T8: reference-extended 结构

```xml
<reference id="reference_xxx">
  <title>参考标题</title>
  <prolog><!-- 元数据 --></prolog>
  <refbody>
    <section>多节组织</section>
    <table>多个表格</table>
    <properties>属性列表</properties>
    <example>示例</example>
  </refbody>
</reference>
```

适用场景：多表格、多章节的复杂参考

---

# T9: reference-specification 结构

```xml
<reference id="ref_spec_xxx">
  <title>规格参数表</title>
  <refbody>
    <section><title>基本参数</title>
      <table>...</table>
    </section>
    <section><title>性能参数</title>
      <table>...</table>
    </section>
    <section><title>环境要求</title>
      <table>...</table>
    </section>
  </refbody>
</reference>
```

适用场景：设备技术规格、产品参数表

---

# T9 制造业示例：CNC 规格

```xml
<reference id="ref_spec_vmc850">
  <title>VMC850 立式加工中心技术规格</title>
  <refbody>
    <section><title>工作台参数</title>
      <table><tgroup cols="2">
        <row><entry>工作台尺寸</entry>
        <entry>1000×500 mm</entry></row>
        <row><entry>最大承重</entry>
        <entry>600 kg</entry></row>
      </tgroup></table>
    </section>
    <section><title>行程参数</title>
      <table><tgroup cols="2">
        <row><entry>X轴行程</entry>
        <entry>850 mm</entry></row>
        <row><entry>Y轴行程</entry>
        <entry>520 mm</entry></row>
        <row><entry>Z轴行程</entry>
        <entry>550 mm</entry></row>
      </tgroup></table>
    </section>
  </refbody>
</reference>
```

---

# 参考模板对比

| 特性 | T7 basic | T8 extended | T9 specification |
|------|---------|-------------|-----------------|
| 章节数 | 1-2节 | 3-6节 | 按参数类型分 |
| 表格数 | 1-2个 | 多个 | 多个参数表 |
| 元数据 | 可选 | 推荐 | 推荐 |
| 适用 | 简单参考 | 复杂参考 | 规格参数 |

---

<!-- _class: divider -->

# 第六章：书册模板 (T10-T12)

---

# T10: bookmap-product 用途

适用于产品随附的用户手册：
- 安全须知
- 技术规格
- 操作说明
- 维护保养
- 故障排除

---

# T11: bookmap-training 用途

适用于培训教材：
- 课程目标
- 理论知识（概念主题）
- 实操练习（任务主题）
- 参考资料（参考主题）
- 考核评估

---

# T12: bookmap-maintenance 用途

适用于设备维修手册：
- 维护周期表
- 检修步骤
- 零件清单
- 润滑图表
- 电气原理图

---

# bookmap 结构示意

```xml
<bookmap id="bookmap_xxx">
  <booktitle>
    <mainbooktitle>手册标题</mainbooktitle>
  </booktitle>
  <frontmatter>
    <notices/><bookabstract/>
    <chapter>安全须知</chapter>
  </frontmatter>
  <chapter href="concept_overview.dita"/>
  <chapter href="task_operation.dita"/>
  <chapter href="reference_spec.dita"/>
  <chapter href="task_maintenance.dita"/>
  <appendix href="ref_troubleshoot.dita"/>
</bookmap>
```

---

<!-- _class: divider -->

# 第七章：从模板目录复制

---

# 复制模板的操作

在命令行中从 `templates/zh-CN/` 复制模板：

```bash
# 进入你的工作分支
cd dita-manufacturing-framework

# 复制概念模板
cp templates/zh-CN/concept/t1-concept-basic.dita \
   product/cnc-vmc850/zh-CN/concepts/

# 复制任务模板
cp templates/zh-CN/task/t4-task-basic.dita \
   product/cnc-vmc850/zh-CN/tasks/

# 复制参考模板
cp templates/zh-CN/reference/t7-reference-basic.dita \
   product/cnc-vmc850/zh-CN/reference/
```

---

# 复制后重命名

```bash
# 复制并重命名
cp templates/zh-CN/concept/t1-concept-basic.dita \
   product/cnc-vmc850/zh-CN/concepts/c_vmc850_overview.dita

# 批量复制任务模板
for tpl in t4 t5 t6; do
  cp templates/zh-CN/task/${tpl}-*.dita \
     product/cnc-vmc850/zh-CN/tasks/
done
```

---

# 模板复制检查清单

- [ ] 确认目标目录存在
- [ ] 选择正确的模板编号
- [ ] 复制到正确的信息类型目录
- [ ] 文件重命名遵循命名规范
- [ ] 更新文件中的 id 属性
- [ ] 确认 XML DOCTYPE 声明正确

---

<!-- _class: divider -->

# 第八章：模板填充详解

---

# 填充 T1 模板的步骤

1. 打开 `t1-concept-basic.dita`
2. 修改 `id` 属性：`concept_xxx` → `concept_your_topic`
3. 填写 `<title>`：你的概念标题
4. 填写 `<shortdesc>`：一句话概述
5. 在 `<conbody>` 的 `<p>` 中填写详细解释
6. 添加 `<note>` 元素（如需要）
7. 保存并验证

---

# 填充 T4 模板的步骤

1. 打开 `t4-task-basic.dita`
2. 修改 `id`：`task_xxx` → `task_your_procedure`
3. 填写 `<prereq>`：需要准备的工具/条件
4. 填写 `<context>`：什么时候执行这个任务
5. 编写 `<steps>`：每步一个 `<step><cmd>...</cmd></step>`
6. 填写 `<result>`：完成后的状态
7. 添加安全警告（如需要）
8. 保存并验证

---

# T4 模板逐元素填充

| 元素 | 必填 | 填写要点 |
|------|------|---------|
| `id` | 是 | 遵循命名规范：task_动词_名词 |
| `title` | 是 | 动宾结构：更换XX、校准XX |
| `shortdesc` | 是 | 最多2句话，概述目的 |
| `prereq` | 推荐 | 工具、权限、安全装备 |
| `context` | 可选 | 使用时机和背景 |
| `steps/step/cmd` | 是 | 每步一个动作 |
| `result` | 推荐 | 完成后的可验证状态 |

---

# 制造业 T4 填空示例

```xml
<task id="task_calibrate_probe">
  <title>校准 【填：设备名称】 测头</title>
  <shortdesc>【填：校准的目的】</shortdesc>
  <taskbody>
    <prereq>【填：需要的工具和条件】</prereq>
    <context>【填：何时需要校准】</context>
    <steps>
      <step><cmd>【填：第1步操作】</cmd></step>
      <step><cmd>【填：第2步操作】</cmd></step>
      <!-- 添加更多步骤 -->
    </steps>
    <result>【填：校准完成的标准】</result>
  </taskbody>
</task>
```

---

# 填充 T2 模板的关键点

- `<section>` 的 `<title>` 是必填的
- 每个 `<section>` 至少包含一个 `<p>`
- `<example>` 放在最后
- `<related-links>` 可以链接到相关的任务主题

---

# 填充 T9 模板的关键点

- `<table>` 需要 `<tgroup cols="N">` 指定列数
- `<colspec>` 定义每列属性
- `<thead>` 用于表头，`<tbody>` 用于数据
- 每个 `<section>` 对应一类参数
- 数值型参数标注单位

---

<!-- _class: divider -->

# 第九章：模板与命名规范

---

# 文件命名规范

| 信息类型 | 前缀 | 示例 |
|---------|------|------|
| 概念 | `c_` | `c_vmc850_overview.dita` |
| 任务 | `t_` | `t_tool_change.dita` |
| 参考 | `r_` | `r_vmc850_spec.dita` |
| 故障排除概念 | `c_tr_` | `c_tr_spindle_vibration.dita` |
| 故障排除任务 | `t_tr_` | `t_tr_spindle_repair.dita` |

---

# ID 命名规范

```
concept_产品代号_主题关键词
task_产品代号_动作_对象
reference_产品代号_内容类型
```

示例：
- `concept_vmc850_cutting_theory`
- `task_vmc850_replace_spindle`
- `reference_vmc850_tech_specs`

---

# 目录结构规范

```
product/cnc-vmc850/zh-CN/
├── concepts/        ← 概念主题
│   ├── c_vmc850_overview.dita
│   └── c_vmc850_safety.dita
├── tasks/           ← 任务主题
│   ├── t_vmc850_startup.dita
│   └── t_vmc850_shutdown.dita
├── reference/       ← 参考主题
│   └── r_vmc850_specs.dita
├── images/          ← 图片资源
└── ditamap/         ← DITA Map
    └── vmc850_user_manual.ditamap
```

---

<!-- _class: divider -->

# 第十章：模板高级用法

---

# 在模板中使用条件属性

```xml
<step props="model(A)">
  <cmd>针对A型设备的步骤</cmd>
</step>
<step props="model(B)">
  <cmd>针对B型设备的步骤</cmd>
</step>
```

这样同一个任务模板可以适配不同型号的 CNC 设备。

---

# 模板中的内容引用

```xml
<!-- 在任务模板中引用警告声明 -->
<step>
  <cmd>执行高压测试</cmd>
  <warning conref="shared/warnings.dita#warnings/high_voltage"/>
</step>
```

集中管理的警告声明，一处更新，全局生效。

---

# 模板中的变量占位符

使用 DITA 的 `keyword` 和 `keydef` 实现变量：

```xml
<!-- 在 ditamap 中定义 -->
<keydef keys="product_name">
  <topicmeta>
    <keywords>
      <keyword>VMC850 立式加工中心</keyword>
    </keywords>
  </topicmeta>
</keydef>

<!-- 在主题中使用 -->
<p>本手册适用于 <keyword keyref="product_name"/>。</p>
```

---

# 创建自己的模板变体

在基础模板上添加企业特定元素：

```xml
<!-- 企业扩展：添加部门签名块 -->
<task id="task_xxx">
  ...
  <!-- 在 </taskbody> 之后添加 -->
  <taskbody>
    <!-- 标准内容 -->
  </taskbody>
  <!-- 企业扩展 -->
  <data name="dept_approval" value="制造工程部"/>
</task>
```

---

<!-- _class: divider -->

# 第十一章：常见错误与避免

---

# 错误 1：模板选错类型

**错误**：把操作步骤写成概念主题

```xml
<!-- 错误：任务内容写在 concept 里 -->
<concept>
  <conbody>
    <p>第一步打开电源
    第二步按下启动按钮...</p>
  </conbody>
</concept>
```

**纠正**：改用 T4/T5 task 模板

---

# 错误 2：忽略警告级别

**错误**：所有警告都用同一个级别

```xml
<!-- 错误：高压和常规提示用同一级别 -->
<warning type="note">接触高压电会死亡</warning>
```

**纠正**：

```xml
<warning type="danger">接触高压电可能导致死亡或重伤</warning>
<note type="tip">定期检查电源线绝缘层</note>
```

---

# 错误 3：保留占位符

**错误**：填充后仍保留模板的 `xxx` 占位符

```xml
<!-- 错误 -->
<title>关于xxx的概述</title>
```

**纠正**：搜索并替换所有 `xxx` 标记

---

# 错误 4：表格列数不匹配

**错误**：

```xml
<tgroup cols="4">  <!-- 声明4列 -->
  <row>
    <entry>参数</entry>
    <entry>数值</entry>  <!-- 只有2列，少了2列 -->
  </row>
</tgroup>
```

**纠正**：确保 `cols` 的值与实际 `<entry>` 数量一致

---

<!-- _class: divider -->

# 第十二章：模板验证

---

# 验证模板填充是否正确

使用 XMLLint 验证：

```bash
# 验证单个文件
xmllint --valid --noout my_topic.dita

# 批量验证目录
find product/ -name "*.dita" -exec xmllint --valid --noout {} \;
```

---

# DITA 验证检查点

| 检查项 | 说明 |
|--------|------|
| XML 良构性 | 标签正确闭合 |
| DOCTYPE 声明 | 指向正确的 DTD |
| 必填元素 | title, id 等 |
| ID 唯一性 | 全局不重复 |
| 引用有效性 | conref/href 指向存在 |

---

<!-- _class: keypoint -->

# 模板使用黄金法则

1. **先选类型**，后选模板
2. **复制模板**，不要直接修改
3. **填充内容**，删除占位符
4. **验证结构**，确保 XML 正确
5. **检查警告**，级别正确
6. **确认命名**，遵循规范

---

<!-- _class: divider -->

# 第十三章：动手练习

---

<!-- _class: exercise -->

# 练习 1：模板选择

根据以下场景选择合适的模板：

1. 写一篇关于 CNC 闭环控制原理的文章（3页，有公式）
2. 写一个机床每日开机检查清单（8步）
3. 提供 VMC850 的电气参数（电压、功率、频率）
4. 写一个更换冷却液的操作（4步）
5. 组织 VMC850 的产品使用手册

---

<!-- _class: exercise -->

# 练习 2：填充 T4 模板

任务：编写"CNC 机床开机预热程序"

要求：
- 使用 `t4-task-basic.dita` 模板
- 至少 5 个步骤
- 包含前置条件
- 包含一条安全提示

在 `product/cnc-vmc850/zh-CN/tasks/` 下创建文件。

---

<!-- _class: exercise -->

# 练习 3：填充 T9 模板

任务：编写"VMC850 主轴参数表"

要求：
- 使用 `t9-reference-specification.dita` 模板
- 至少包含：主轴转速、锥孔规格、电机功率
- 使用表格组织数据
- 标注所有单位

---

<!-- _class: exercise -->

# 练习 4：创建 bookmap

任务：为 VMC850 创建产品使用手册 bookmap

要求：
- 使用 `t10-bookmap-product.dita` 模板
- 包含：安全须知、规格参数、操作说明、维护保养
- 使用 topicref 引用现有主题
- 正确设置 navtitle

---

# 练习 1 参考答案

| 场景 | 正确模板 |
|------|---------|
| CNC 闭环控制原理（3页） | T2 concept-extended |
| 每日开机检查（8步） | T5 task-extended |
| 电气参数 | T9 reference-specification |
| 更换冷却液（4步） | T4 task-basic |
| 产品使用手册 | T10 bookmap-product |

---

# 练习 2 参考示例

```xml
<task id="task_vmc850_warmup">
  <title>VMC850 开机预热程序</title>
  <taskbody>
    <prereq>已检查润滑油位和气压正常</prereq>
    <steps>
      <step><cmd>合上总电源开关</cmd></step>
      <step><cmd>按下控制面板"启动"按钮</cmd></step>
      <step><cmd>等待系统自检完成（约30秒）</cmd></step>
      <step><cmd>执行主轴预热程序 O9001</cmd>
        <info>主轴以500rpm运转5分钟，逐步升速到3000rpm运转5分钟</info>
      </step>
      <step importance="urgent">
        <cmd>检查各轴回零是否正常</cmd>
        <warning type="danger">如有异常噪音立即按急停</warning>
      </step>
    </steps>
  </taskbody>
</task>
```

---

# 练习 3 参考示例

```xml
<reference id="ref_vmc850_spindle_spec">
  <title>VMC850 主轴技术参数</title>
  <refbody>
    <section><title>主轴基本参数</title>
      <table><tgroup cols="2">
        <row><entry>主轴锥孔</entry>
        <entry>BT40</entry></row>
        <row><entry>最高转速</entry>
        <entry>8000 rpm</entry></row>
        <row><entry>主轴电机功率</entry>
        <entry>7.5/11 kW（连续/30min）</entry></row>
      </tgroup></table>
    </section>
  </refbody>
</reference>
```

---

# 练习 4 参考示例

```xml
<bookmap id="bookmap_vmc850_manual">
  <booktitle>
    <mainbooktitle>VMC850 立式加工中心使用手册</mainbooktitle>
  </booktitle>
  <frontmatter>
    <chapter><topicref href="../concepts/c_vmc850_safety.dita"/></chapter>
  </frontmatter>
  <chapter navtitle="产品概述">
    <topicref href="../concepts/c_vmc850_overview.dita"/>
  </chapter>
  <chapter navtitle="技术规格">
    <topicref href="../reference/r_vmc850_specs.dita"/>
  </chapter>
  <chapter navtitle="操作指南">
    <topicref href="../tasks/t_vmc850_startup.dita"/>
    <topicref href="../tasks/t_vmc850_shutdown.dita"/>
  </chapter>
  <chapter navtitle="维护保养">
    <topicref href="../tasks/t_vmc850_maintenance.dita"/>
  </chapter>
</bookmap>
```

---

<!-- _class: divider -->

# 第十四章：总结与最佳实践

---

<!-- _class: keypoint -->

# 本章要点回顾

1. T1-T12 模板覆盖概念、任务、参考、书册四种信息类型
2. 选择模板的第一步：确定信息类型
3. 模板提供了结构，你来填充内容
4. 始终复制模板而非直接修改原始文件
5. 填充后必须验证 XML 结构和完整性

---

# 模板使用完整工作流

```
确定信息类型 → 选择模板编号 → 复制模板文件
→ 重命名 → 填充内容 → 添加警告/注释
→ 检查命名规范 → 验证XML → 提交审查
```

---

# 学习资源

- `templates/zh-CN/` 目录下的 12 个模板源文件
- DITA 1.3 规范：`docs.oasis-open.org/dita/`
- 企业内部 DITA 编写规范文档
- 已完成的产品主题作为参考示例

---

# 结束提问

1. 你当前要写的文档属于哪种信息类型？
2. 你选择的模板编号是哪个？
3. 模板中哪些部分是必填的？
4. 如何验证模板填充后的 XML 是否正确？

---

<!-- _class: title -->

# 谢谢
## 下一课：元数据基础

**DITA 课程 | 制造业文档**
