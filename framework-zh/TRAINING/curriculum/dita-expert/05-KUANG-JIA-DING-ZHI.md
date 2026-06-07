---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# 框架自定义

## 制造业 DITA 深度定制与系统集成

**DITA 专家级培训 · 模块 5**

---

## 学习目标

完成本模块后，您将能够：

- 自定义制造业专用主题类型与领域模块
- 开发 DITA-OT 插件实现自定义处理逻辑
- 使用 XSL-FO 创建符合企业 VI 的 PDF 输出
- 配置新型输出格式 (JSON, 知识图谱, 移动端)
- 实现 DITA 与 MES/PLM/ERP 系统的双向集成
- 扩展主题方案 (Subject Scheme) 管控制造业元数据
- 调试和优化自定义插件性能

---

<!-- _class: divider -->

# 第一部分
## 自定义主题类型

---

## 主题类型设计方法论

在专用化现有类型之前，先回答三个问题：

```
问题 1: 信息目标是什么？
  → 教学 → concept   │  操作 → task   │  参考 → reference

问题 2: 现有类型能覆盖 80% 需求吗？
  → 能 → 使用现有类型 + 领域模块
  → 否 → 专用化新主题类型

问题 3: 新增结构是否有语义价值？
  → 是 → 谨慎专用化
  → 否 → 用 outputclass 属性代替
```

**牢记**：不是每个新想法都需要新主题类型。

---

## 制造业主题类型扩展谱系

```
                    topic
         ┌───────────┼───────────┐
      concept       task      reference
         │            │            │
    ┌────┴────┐  ┌────┴────┐  ┌───┴──────┐
    │         │  │         │  │          │
  故障    维护  机器      安全  物料      技术
  诊断    概念  操作      任务  参考      规格
   │         │  │         │  │          │
   │         │  │    ┌────┴──┴──┐    ┌──┴──┐
   │         │  │   CNC   机器人  │   BOM  图纸
   │         │  │  操作   操作    │  清单  参考
   │         │  │    │          │
   │         │  │    ├─ MC-5000 │
   │         │  │    ├─ DR-6000 │
   │         │  │    └─ DR-7000 │
```

原则：专用化深度不超过 3 层，避免过度碎片化。

---

## 创建自定义机器操作主题 (machine-task)

DTD shell 文件 `machine-task.dtd`:

```xml
<!-- ============================================================= -->
<!-- DTD: Machine Task Shell for 示例制造                       -->
<!-- Specialized from: OASIS DITA 1.3 Task                         -->
<!-- ============================================================= -->

<!-- 1. 引入基础 task 模块 -->
<!ENTITY % task-typemod
  PUBLIC "-//OASIS//ELEMENTS DITA 1.3 Task//EN"
         "task.mod"
>
%task-typemod;

<!-- 2. 替换 taskbody 为 machine-taskbody -->
<!ENTITY % taskbody.content
  "((%prereq;)?,
    (%context;)?,
    (%section; |
     %machine-step-group;)*,
    (%result;)?,
    (%example;)?,
    (%postreq;)?)"
>
```

---

## machine-step-group 元素定义

`machine-step-group.mod`:

```xml
<!-- ============================================================= -->
<!-- MODULE: Machine Step Group                                     -->
<!-- ============================================================= -->

<!ENTITY % machine-step-group.content
  "((%safety-interlock;)?,
    (%setup-tools;)?,
    (%machine-steps;),
    (%quality-check;)?,
    (%cleanup;)?)"
>

<!ENTITY % machine-step-group.attributes
  "%id-atts;
   %localization-atts;
   machine-mode (automatic|manual|semi-auto|maintenance) #REQUIRED
   spindle-speed CDATA #IMPLIED
   coolant (on|off|mist|through) #IMPLIED
   outputclass CDATA #IMPLIED"
>

<!ELEMENT machine-step-group %machine-step-group.content;>
<!ATTLIST machine-step-group %machine-step-group.attributes;>
```

---

## machine-steps 元素与安全联锁

```xml
<!-- 安全联锁检查（步骤前置条件） -->
<!ELEMENT safety-interlock
  ((%safety-interlock-item;)+)
>
<!ATTLIST safety-interlock
  %id-atts;
  interlock-status (pass|fail|bypass) #REQUIRED
>

<!ELEMENT safety-interlock-item
  (#PCDATA | %ph; | %term;)*
>

<!-- 机器操作步骤（每步包含 CNC 特有数据） -->
<!ELEMENT machine-step
  ((%cmd;), (%machine-action;), (%expected-result;)?,
   (%machine-data;)?)
>
<!ATTLIST machine-step
  %id-atts;
  g-code CDATA #IMPLIED
  m-code CDATA #IMPLIED
  cycle-time CDATA #IMPLIED
  tool-number NMTOKEN #IMPLIED
>
```

---

## 机器数据元素：整合 CNC 参数

```xml
<!-- 机器操作数据块 -->
<!ELEMENT machine-data
  ((%spindle-data;)?,
   (%feed-data;)?,
   (%coolant-data;)?,
   (%measurement;)?)
>

<!ELEMENT spindle-data (EMPTY)>
<!ATTLIST spindle-data
  speed CDATA #REQUIRED
  direction (cw|ccw) #REQUIRED
  unit (rpm|sfm) #REQUIRED
>

<!ELEMENT feed-data (EMPTY)>
<!ATTLIST feed-data
  rate CDATA #REQUIRED
  unit (mm_per_min|mm_per_rev|mm_per_tooth) #REQUIRED
  rapid CDATA #IMPLIED
>
```

---

## 使用 machine-task 的完整示例

<!-- _class: exercise -->

```xml
<machine-task id="dr5000-face-mill" machine-mode="automatic">
  <title>MC-5000 面铣加工：AISI 4140 钢</title>
  <machine-taskbody>
    <safety-interlock interlock-status="pass">
      <safety-interlock-item>防护门闭合确认</safety-interlock-item>
      <safety-interlock-item>急停按钮复位确认</safety-interlock-item>
      <safety-interlock-item>冷却液液位 >= 最低标记</safety-interlock-item>
    </safety-interlock>
    <machine-step-group machine-mode="automatic"
                        spindle-speed="1200"
                        coolant="through">
      <machine-steps>
        <machine-step g-code="G54" tool-number="T01">
          <cmd>选定工件坐标系 G54</cmd>
          <machine-action>确认工件原点与程序设定一致</machine-action>
          <machine-data>
            <spindle-data speed="1200" direction="cw" unit="rpm"/>
            <feed-data rate="350" unit="mm_per_min"/>
          </machine-data>
        </machine-step>
      </machine-steps>
    </machine-step-group>
  </machine-taskbody>
</machine-task>
```

---

<!-- _class: keypoint -->

## 关键要点：自定义主题类型

- 先判断是否真的需要新类型（80/20法则）
- 专用化深度不超过 3 层
- CNC 特有数据 (G-code, M-code, 转速, 进给) 内嵌到元素属性
- 安全联锁作为步骤前置条件强制存在
- 每个新元素必须能安全降级到基础类型

---

<!-- _class: divider -->

# 第二部分
## DITA-OT 插件开发基础

---

## DITA-OT 插件架构

```
dita-ot/
  └── plugins/
        └── com.dingrui.mfg-custom/
              ├── plugin.xml          ← 插件描述与配置
              ├── integrator.xml      ← 集成点声明
              ├── xsl/               ← XSLT 处理逻辑
              │     ├── html5/
              │     │     └── dita2html5.xsl
              │     └── pdf/
              │           └── custom.xsl
              ├── cfg/               ← 配置目录
              │     └── fo/
              │           └── attrs/
              │                 └── custom.xsl
              ├── dtd/               ← DTD 专用化文件
              │     └── machine-task/
              │           ├── machine-task.dtd
              │           ├── machine-task.mod
              │           └── machine-task.ent
              └── build.xml          ← Ant 构建文件 (可选)
```

---

## plugin.xml 模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<plugin id="com.dingrui.mfg-custom">
  
  <require plugin="org.dita.html5"/>
  <require plugin="org.dita.pdf2"/>
  
  <feature extension="dita.conductor.transtype.check"
           value="mfg-html5"/>
  <feature extension="dita.conductor.transtype.check"
           value="mfg-pdf"/>
  
  <!-- HTML5 自定义输出 -->
  <feature extension="dita.xsl.html5"
           file="xsl/html5/dita2html5.xsl"/>
  
  <!-- PDF 自定义输出 -->
  <feature extension="dita.xsl.xslfo"
           file="xsl/pdf/custom.xsl"/>
  
  <!-- 自定义转换类型 -->
  <transtype name="mfg-html5" desc="示例制造业HTML5">
    <param name="include.rellinks" value="true"/>
  </transtype>
</plugin>
```

---

## 自定义 HTML5 处理：提取 CNC 参数表

`xsl/html5/dita2html5.xsl`:

```xml
<xsl:template match="*[contains(@class, ' machine-step-group ')]">
  <div class="machine-step-group">
    <!-- 提取 CNC 参数栏 -->
    <xsl:if test="@spindle-speed or @coolant">
      <div class="cnc-params">
        <xsl:if test="@machine-mode">
          <span class="mode-badge">
            <xsl:value-of select="@machine-mode"/>
          </span>
        </xsl:if>
        <xsl:if test="@spindle-speed">
          <span class="spindle">
            转速: <xsl:value-of select="@spindle-speed"/> RPM
          </span>
        </xsl:if>
        <xsl:if test="@coolant">
          <span class="coolant">
            冷却: <xsl:value-of select="@coolant"/>
          </span>
        </xsl:if>
      </div>
    </xsl:if>
    <xsl:apply-templates/>
  </div>
</xsl:template>
```

---

## 处理 spindle-data 元素

```xml
<xsl:template match="*[contains(@class, ' spindle-data ')]">
  <div class="spindle-data params-row">
    <xsl:if test="@speed and @unit">
      <span class="param">
        <span class="param-icon">⚙</span>
        <span class="param-label">主轴转速:</span>
        <span class="param-value">
          <xsl:value-of select="@speed"/>
          <xsl:text> </xsl:text>
          <xsl:value-of select="@unit"/>
        </span>
      </span>
    </xsl:if>
    <xsl:if test="@direction">
      <span class="param">
        <span class="param-label">方向:</span>
        <span class="param-value">
          <xsl:choose>
            <xsl:when test="@direction = 'cw'">顺时针</xsl:when>
            <xsl:otherwise>逆时针</xsl:otherwise>
          </xsl:choose>
        </span>
      </span>
    </xsl:if>
  </div>
</xsl:template>
```

---

## 插件安装与验证

```bash
# 1. 将插件放入 DITA-OT plugins 目录
cp -r com.dingrui.mfg-custom/ \
     $DITA_HOME/plugins/

# 2. 运行集成器
cd $DITA_HOME
bin/dita --install

# 3. 验证插件已加载
bin/dita --plugins | grep dingrui
# 输出: com.dingrui.mfg-custom [installed]

# 4. 测试构建
bin/dita -i content/dr5000.ditamap \
         -f mfg-html5 \
         -o output/mfg-html5

# 5. 使用调试模式排查问题
bin/dita -i content/dr5000.ditamap \
         -f mfg-html5 \
         --debug \
         -o output/debug/
```

---

## 调试常见插件错误

| 错误信息 | 可能原因 | 解决方案 |
|---------|---------|---------|
| `plugin.xml not found` | 插件目录结构不正确 | 确认 plugin.xml 在插件根目录下 |
| `Extension point not recognized` | extension ID 拼写错误 | 检查 `dita.conductor.transtype.check` 拼写 |
| `XSLT compile error` | XSL 语法错误 | 用 oXygen 单独验证 XSL 文件 |
| `No pipeline for transtype` | 未声明 transtype | 在 plugin.xml 中注册 `<transtype>` |
| `File not found: *.mod` | DTD 集成路径错误 | 确认相对路径或使用 PUBLIC ID |

---

<!-- _class: exercise -->

## 练习五：开发安全警告高亮插件

**任务**：为示例 HTML5 输出创建一个插件，实现对 `<safety-interlock>` 元素的特殊渲染。

**需求**：
1. 安全联锁状态为 `pass` 时显示绿色边框 + "已通过"
2. 状态为 `fail` 时显示红色边框 + "未通过，禁止操作"
3. 状态为 `bypass` 时显示黄色边框 + "已旁路（需审批）"
4. 三个状态使用不同的 CSS class: `interlock-pass`, `interlock-fail`, `interlock-bypass`

请写出核心 XSLT 模板和对应 CSS。

---

<!-- _class: keypoint -->

## 关键要点：插件开发

- 插件是 DITA-OT 扩展的标准方式，每个插件独立目录
- plugin.xml 声明依赖、转换类型、扩展点
- XSLT 是与 DITA-OT 集成的主要编程语言
- 每次修改插件后必须重跑 `dita --install`
- `--debug` 模式输出详细日志辅助排查

---

<!-- _class: divider -->

# 第三部分
## 自定义 PDF 输出

---

## XSL-FO PDF 管道概览

```
DITA Map → DITA-OT 预处理 → 合并为单 XML → XSLT 转为 XSL-FO → FOP/AH 渲染 → PDF
```

关键文件结构：

```
cfg/fo/
  ├── attrs/
  │     └── custom.xsl          ← 属性集（字体、颜色、间距）
  ├── layout-masters.xsl        ← 页面版式（页眉、页脚、页码）
  ├── static-content.xsl        ← 静态内容（封面、目录、索引）
  └── i18n/
        └── zh_CN.xml           ← 中文国际化字符串
```

---

## 自定义页面版式

`layout-masters.xsl`:

```xml
<xsl:template name="createDefaultLayoutMasters">
  
  <!-- 页面尺寸：A4 -->
  <fo:simple-page-master master-name="body-first"
                          page-width="210mm" page-height="297mm"
                          margin-top="20mm" margin-bottom="20mm"
                          margin-left="25mm" margin-right="20mm">
    <fo:region-body margin-top="15mm" margin-bottom="20mm"/>
    <fo:region-before extent="15mm"/>
    <fo:region-after extent="20mm"/>
  </fo:simple-page-master>
  
  <!-- 奇偶页不同 -->
  <fo:page-sequence-master master-name="body-sequence">
    <fo:repeatable-page-master-alternatives>
      <fo:conditional-page-master-reference 
        master-reference="body-odd"
        odd-or-even="odd"/>
      <fo:conditional-page-master-reference 
        master-reference="body-even"
        odd-or-even="even"/>
    </fo:repeatable-page-master-alternatives>
  </fo:page-sequence-master>
  
</xsl:template>
```

---

## 页眉与页脚定制

```xml
<!-- 奇数页页眉：右侧显示文档标题 -->
<fo:static-content flow-name="odd-header">
  <fo:block text-align="right" font-size="8pt" color="#666">
    <fo:retrieve-marker retrieve-class-name="current-heading"/>
  </fo:block>
  <fo:block text-align="right">
    <fo:leader leader-pattern="rule" leader-length="100%"
               color="#003D7A" rule-thickness="0.5pt"/>
  </fo:block>
</fo:static-content>

<!-- 页脚：左侧公司名 + 右侧页码 -->
<fo:static-content flow-name="odd-footer">
  <fo:block font-size="7pt" color="#999">
    <fo:inline text-align="left">
      示例制造 · MC-5000 CNC · 机密
    </fo:inline>
    <fo:inline text-align="right">
      页码 <fo:page-number/> / <fo:page-number-citation ref-id="last-page"/>
    </fo:inline>
  </fo:block>
</fo:static-content>
```

---

## 品牌属性集：colors + fonts

```xml
<!-- 品牌色定义 -->
<xsl:attribute-set name="brand.color.primary">
  <xsl:attribute name="color">#003D7A</xsl:attribute>
</xsl:attribute-set>

<xsl:attribute-set name="brand.color.accent">
  <xsl:attribute name="color">#E87722</xsl:attribute>
</xsl:attribute-set>

<!-- 中文正文字体栈 -->
<xsl:attribute-set name="body.font.cn">
  <xsl:attribute name="font-family">
    "Noto Sans SC", "Microsoft YaHei", "SimHei", sans-serif
  </xsl:attribute>
  <xsl:attribute name="font-size">10pt</xsl:attribute>
  <xsl:attribute name="line-height">1.6</xsl:attribute>
</xsl:attribute-set>

<!-- 代码块 -->
<xsl:attribute-set name="code.block">
  <xsl:attribute name="font-family">
    "Noto Sans Mono", "Courier New", monospace
  </xsl:attribute>
  <xsl:attribute name="font-size">8pt</xsl:attribute>
  <xsl:attribute name="background-color">#F5F5F5</xsl:attribute>
  <xsl:attribute name="padding">8pt</xsl:attribute>
</xsl:attribute-set>
```

---

## 安全警告的 PDF 样式差异化

```xml
<xsl:template match="*[contains(@class, ' safety-interlock ')]">
  <xsl:choose>
    <xsl:when test="@interlock-status = 'pass'">
      <fo:block background-color="#E8F5E9" 
                border-left="4pt solid #4CAF50"
                padding="8pt" margin="6pt 0">
        <fo:inline font-weight="bold" color="#2E7D32">
          安全联锁: 已通过 ✓
        </fo:inline>
        <xsl:apply-templates/>
      </fo:block>
    </xsl:when>
    <xsl:when test="@interlock-status = 'fail'">
      <fo:block background-color="#FFEBEE" 
                border-left="4pt solid #F44336"
                padding="8pt" margin="6pt 0">
        <fo:inline font-weight="bold" color="#C62828">
          安全联锁: 未通过 ✗ — 禁止操作
        </fo:inline>
        <xsl:apply-templates/>
      </fo:block>
    </xsl:when>
  </xsl:choose>
</xsl:template>
```

---

## 自定义封面

```xml
<xsl:template name="createFrontCover">
  <fo:page-sequence master-reference="cover-page">
    <fo:flow flow-name="xsl-region-body">
      
      <!-- 公司 Logo -->
      <fo:block text-align="center" space-before="40mm">
        <fo:external-graphic src="images/dingrui-logo.png"
                             content-width="60mm"/>
      </fo:block>
      
      <!-- 文档标题 -->
      <fo:block text-align="center" font-size="24pt" 
                font-weight="bold" space-before="20mm"
                color="#003D7A">
        <xsl:value-of select="/map/title"/>
      </fo:block>
      
      <!-- 文档编号 & 版本 -->
      <fo:block text-align="center" font-size="11pt"
                space-before="10mm" color="#666">
        文档编号: <xsl:value-of select="/map/data/@doc-number"/>
        版本: <xsl:value-of select="/map/data/@version"/>
      </fo:block>
      
      <!-- 安全等级标注 -->
      <fo:block text-align="center" font-size="9pt"
                space-before="15mm" color="#E87722">
        机密 — 仅供示例制造内部使用
      </fo:block>
      
    </fo:flow>
  </fo:page-sequence>
</xsl:template>
```

---

## 处理 CNC 参数表

```xml
<xsl:template match="*[contains(@class, ' spindle-data ')]">
  <fo:table width="100%" table-layout="fixed"
            border="1pt solid #CCC" margin="8pt 0">
    <fo:table-column column-width="25%"/>
    <fo:table-column column-width="25%"/>
    <fo:table-column column-width="25%"/>
    <fo:table-column column-width="25%"/>
    <fo:table-header>
      <fo:table-row background-color="#003D7A" color="white">
        <fo:table-cell><fo:block>主轴转速</fo:block></fo:table-cell>
        <fo:table-cell><fo:block>旋转方向</fo:block></fo:table-cell>
        <fo:table-cell><fo:block>进给速率</fo:block></fo:table-cell>
        <fo:table-cell><fo:block>冷却模式</fo:block></fo:table-cell>
      </fo:table-row>
    </fo:table-header>
    <fo:table-body>
      <fo:table-row>
        <fo:table-cell>
          <fo:block><xsl:value-of select="concat(@speed, ' ', @unit)"/></fo:block>
        </fo:table-cell>
        <fo:table-cell>
          <fo:block><xsl:value-of select="@direction"/></fo:block>
        </fo:table-cell>
        <fo:table-cell>
          <fo:block><xsl:value-of select="../feed-data/@rate"/></fo:block>
        </fo:table-cell>
        <fo:table-cell>
          <fo:block><xsl:value-of select="../coolant-data/@mode"/></fo:block>
        </fo:table-cell>
      </fo:table-row>
    </fo:table-body>
  </fo:table>
</xsl:template>
```

---

<!-- _class: exercise -->

## 练习六：制造作业指导书 PDF 模板

**任务**：为示例生产线设计一份 A5 尺寸的作业指导书 (Work Instruction) PDF 模板。

**需求**：
1. A5 横版 (210mm x 148mm)，适合工位摆放
2. 仅包含: 标题、工件图、步骤、安全警告、检验要求
3. 安全警告使用红色警告框
4. 每个步骤左侧显示步骤编号（圆形标记）
5. 底部显示版本号和生效日期

请写出 `layout-masters.xsl` 的页面版式配置和关键模板。

---

<!-- _class: keypoint -->

## 关键要点：PDF 自定义

- XSL-FO 是 DITA 到 PDF 的标准中间格式
- 页面版式、页眉页脚、属性集分别管理
- 安全信息根据 @interlock-status 呈现不同样式
- 品牌色 (#003D7A 蓝, #E87722 橙) 贯穿始终
- 中文字体需要商业许可，预算中已包含
- 表格最适合呈现 CNC 参数

---

<!-- _class: divider -->

# 第四部分
## 新型输出格式

---

## 多格式输出矩阵

```
                     DITA 源文件
                          │
         ┌────────────────┼────────────────┐
         │                │                │
    ┌────┴────┐     ┌─────┴─────┐    ┌─────┴─────┐
    │   传统   │     │   结构化   │    │   新兴     │
    ├─────────┤     ├───────────┤    ├───────────┤
    │ HTML5   │     │ JSON      │    │ 知识图谱   │
    │ PDF     │     │ API Docs  │    │ 移动端 H5  │
    │ Eclipse │     │ Markdown  │    │ AR 叠加层   │
    └─────────┘     └───────────┘    └───────────┘
```

示例重点投入：**JSON API**、**知识图谱**、**移动端 H5**。

---

## JSON 输出插件实现

制造业场景：将 DITA 内容输出为 JSON 供 MES 系统和车间终端消费。

```xml
<!-- plugin.xml 中注册 JSON 转换类型 -->
<transtype name="mfg-json" desc="示例制造业JSON输出">
  <param name="output-format" value="json"/>
  <param name="include-properties" value="true"/>
  <param name="pretty-print" value="true"/>
</transtype>

<feature extension="dita.xsl.mfg-json"
         file="xsl/json/dita2json.xsl"/>
```

---

## dita2json.xsl 核心逻辑

```xml
<xsl:template match="/">
  <xsl:text>[</xsl:text>
  <xsl:for-each select="//*[contains(@class, ' topic/topic ')]">
    <xsl:if test="position() > 1">,</xsl:if>
    {
      "id": "<xsl:value-of select="@id"/>",
      "title": "<xsl:value-of select="*[contains(@class, ' topic/title ')]"/>",
      "type": "<xsl:value-of 
        select="substring-after(@class, '/')"/>",
      "shortdesc": "<xsl:value-of 
        select="*[contains(@class, ' topic/shortdesc ')]"/>",
      "cnc_params": {
        <xsl:if test="//*[contains(@class,' spindle-data ')]/@speed">
          "spindle_speed": <xsl:value-of 
            select="//*[contains(@class,' spindle-data ')]/@speed"/>,
        </xsl:if>
        <xsl:if test="//*[contains(@class,' spindle-data ')]/@unit">
          "spindle_unit": "<xsl:value-of 
            select="//*[contains(@class,' spindle-data ')]/@unit"/>"
        </xsl:if>
      }
    }
  </xsl:for-each>
  <xsl:text>]</xsl:text>
</xsl:template>
```

---

## JSON 输出示例

```json
[
  {
    "id": "dr5000-face-mill",
    "title": "MC-5000 面铣加工：AISI 4140 钢",
    "type": "machine-task",
    "shortdesc": "使用面铣刀对 AISI 4140 钢板进行粗加工",
    "cnc_params": {
      "spindle_speed": 1200,
      "spindle_unit": "rpm",
      "feed_rate": 350,
      "feed_unit": "mm_per_min",
      "coolant_mode": "through",
      "g_code": "G54",
      "tool_number": "T01"
    },
    "safety_interlocks": [
      {"item": "防护门闭合确认", "status": "pass"},
      {"item": "急停按钮复位确认", "status": "pass"},
      {"item": "冷却液液位 >= 最低标记", "status": "pass"}
    ]
  }
]
```

MES 系统可直接解析该 JSON，将 CNC 参数加载到机床控制器。

---

## 知识图谱输出：DITA → RDF/OWL

制造业知识图谱将 DITA 主题关系转化为语义网络：

```
主题 A (故障诊断: 主轴异响)
  │
  ├── related-link → 主题 B (维护步骤: 更换主轴轴承)
  │                          │
  │                    machine-data
  │                          │
  │                    ┌─────┴─────┐
  │                    │           │
  │               spindle-data  tool-data
  │               speed: 800    T02: 轴承拉马
  │                    │
  └── related-link ────┘
  
  → RDF 三元组:
  <主轴异响> dita:causedBy <轴承磨损>
  <更换主轴轴承> dita:requiresTool <T02>
  <T02> dita:crossReference <备件目录>
```

---

## 移动端输出：车间 H5 终端

```html
<!-- 为车间平板优化的 HTML 模板 -->
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <style>
    body { font-family: "Noto Sans SC"; font-size: 16px; }
    .step-card { 
      border-left: 4px solid #003D7A;
      margin: 12px 0; padding: 16px;
      background: #FAFAFA; border-radius: 8px;
    }
    .safety-alert {
      background: #FFEBEE; border-left: 4px solid #F44336;
      margin: 16px 0; padding: 16px; border-radius: 8px;
    }
    /* 大触控目标 */
    .nav-button { 
      min-height: 48px; min-width: 48px;
      padding: 12px 24px; font-size: 18px;
    }
  </style>
</head>
```

---

## 输出格式对比与选择指南

| 输出格式 | 优势 | 劣势 | 示例适用场景 |
|---------|------|------|------------|
| **HTML5** | 交互丰富、搜索友好 | 离线支持较弱 | 桌面端帮助中心 |
| **PDF** | 精确排版、离线可用 | 无交互、文件大 | 出厂随附手册、合规归档 |
| **JSON** | 结构化、可编程 | 不适合人类阅读 | MES 集成、车间终端 |
| **知识图谱** | 语义推理、关联发现 | 维护成本高 | 故障诊断辅助、备件关联 |
| **移动 H5** | 车间友好、大触控 | 需要网络 | 工位终端、AR 辅助 |

---

<!-- _class: exercise -->

## 练习七：设计 MES 数据接口

**任务**：设计从 DITA 到 MES 系统的 JSON 数据合约。

**MES 需求**：
1. 当操作工在 MES 终端扫描工件二维码时，自动加载对应 CNC 程序参数
2. 显示安全联锁检查清单，操作工逐项确认
3. 显示当前工序的刀具清单 (Tool Number + Tool Description)
4. 记录每道工序的实际加工时间

请定义 JSON Schema，包含所有必需的字段和类型。

---

<!-- _class: keypoint -->

## 关键要点：新型输出格式

- JSON 输出是 MES/PLM 集成的桥梁格式
- 知识图谱支持故障诊断推理和备件关联
- 移动端 H5 需要考虑车间环境：大触控、防油污、离线支持
- 每种输出格式都有其最佳场景，不可一统

---

<!-- _class: divider -->

# 第五部分
## MES/PLM/ERP 系统集成

---

## 制造业系统集成全景

```
                    ┌─────────────┐
                    │   DITA CCMS  │
                    │  (内容源)    │
                    └──────┬──────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
    ┌─────┴─────┐   ┌──────┴──────┐   ┌─────┴─────┐
    │    MES    │   │    PLM      │   │    ERP    │
    │ 制造执行   │   │ 产品生命周期 │   │ 企业资源   │
    ├───────────┤   ├─────────────┤   ├───────────┤
    │ · 工单     │   │ · BOM       │   │ · 物料号   │
    │ · 工艺路线 │   │ · 工程变更   │   │ · 成本核算  │
    │ · 机台参数 │   │ · 设计图纸   │   │ · 采购     │
    └─────┬─────┘   └──────┬──────┘   └─────┬─────┘
          │                │                │
          └────────────────┼────────────────┘
                           │
                    ┌──────┴──────┐
                    │   ESB / API  │
                    │  集成总线     │
                    └─────────────┘
```

DITA 内容供应链：从设计到制造的全链路内容管理。

---

## MES 集成：从工单到操作指导

```
MES 触发                      DITA 响应
  │                              │
  ├─ 工单下达 (Work Order)       │
  │   → "MC-5000, 面铣, QTY 200" │
  │                              ├─ 查询 machine-task 主题
  │                              │  过滤: product=MC-5000
  │                              │       operation=face-mill
  │                              │
  ├─ 请求操作指导                 │
  │                              ├─ DITA-OT JSON 输出
  │                              │  → 返回 CNC 参数 + 安全联锁
  │                              │
  └─ 加载至 MES 终端 ◄────────────┘
```

---

## MES 集成的 API 架构

```yaml
# DITA Content API (RESTful)
GET /api/v1/operations/{product}/{operation}
  Parameters:
    product: MC-5000
    operation: face-mill
    lang: zh-CN
  Response:
    200 OK
    Content-Type: application/json
    {
      "operation_id": "dr5000-face-mill",
      "title": "MC-5000 面铣加工：AISI 4140 钢",
      "safety_interlocks": [...],
      "cnc_params": {...},
      "tool_list": [
        {"number": "T01", "desc": "面铣刀 D80"},
        {"number": "T02", "desc": "倒角刀 D10"}
      ],
      "quality_checks": [...],
      "estimated_cycle_time": "4min 30s"
    }
```

---

## PLM 集成：工程变更驱动文档更新

```
工程师修改 CAD 模型 (PLM)
    │
    ├─ 发出 ECO (Engineering Change Order)
    │     "MC-5000 主轴轴承规格变更: 6205 → 6206"
    │
    ├─ PLM Webhook → DITA CCMS
    │     POST /api/v1/eco-webhook
    │     { "eco_id": "ECO-2025-081",
    │       "affected_parts": ["SP-6205"],
    │       "change_desc": "主轴轴承 6205 → 6206" }
    │
    ├─ DITA CCMS
    │     ├─ 搜索引用 SP-6205 的主题
    │     │   → maintenance/dr5000-spindle.dita
    │     │   → parts-catalog/bearings.dita
    │     │   → bom/dr5000-mechanical.dita
    │     │
    │     ├─ 创建文档变更任务 (DCO)
    │     └─ 分配给技术写作者
    │
    └─ 写作者收到通知 → 更新 → 审核 → 发布
```

---

## ERP 集成：物料主数据同步

```xml
<!-- DITA 主题中引用 ERP 物料号 -->
<reference id="part-sp6206" class="- topic/reference ">
  <title>主轴轴承</title>
  <refbody>
    <section>
      <table>
        <tgroup cols="2">
          <colspec colname="c1"/>
          <colspec colname="c2"/>
          <thead>
            <row><entry>属性</entry><entry>值</entry></row>
          </thead>
          <tbody>
            <row>
              <entry>物料号 (ERP)</entry>
              <entry><data name="erp_code" value="SP-6206"/></entry>
            </row>
            <row>
              <entry>供应商</entry>
              <entry><data name="supplier" value="NSK"/></entry>
            </row>
            <row>
              <entry>规格</entry>
              <entry><data name="spec" value="30x62x16"/></entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
  </refbody>
</reference>
```

ERP 物料号变更 → 自动触发 DITA 内容审查。

---

## 集成中间件：Apache Camel 路由示例

```java
// 示例集成总线 — 连接 PLM/MES/ERP 与 DITA CCMS
from("webhook:eco-received")
    .log("收到工程变更单: ${header.ecoId}")
    .unmarshal().json(JsonLibrary.Jackson, EcoChangeOrder.class)
    .process(exchange -> {
        EcoChangeOrder eco = exchange.getIn().getBody(EcoChangeOrder.class);
        // 在 DITA 内容库中搜索受影响的主题
        List<String> affectedTopics = 
            ditaSearchService.findByPartNumber(eco.getAffectedParts());
        exchange.setProperty("affectedTopics", affectedTopics);
    })
    .to("direct:create-dco")  // 创建文档变更单
    .to("direct:notify-writers") // 通知写作者
    .to("direct:update-dashboard"); // 更新 KPI 仪表盘
```

---

## 集成安全与权限模型

| 系统 | 访问方向 | DITA 暴露的数据 | 安全措施 |
|------|---------|---------------|---------|
| MES | 只读 | 操作指导、CNC 参数、安全联锁 | API Key + HTTPS | 车间终端不可修改 |
| PLM | 读写 (Webhook) | BOM 引用、规格参数 | OAuth 2.0 Client Credentials | ECO 可触发内容审查 |
| ERP | 只读 | 物料主数据引用 | IP 白名单 + TLS | 物料号单向同步 |
| DITA CCMS | 本地 | 全部 | RBAC 角色权限 | 写作者按产品授权 |

---

<!-- _class: exercise -->

## 练习八：ERP 物料号变更的文档影响分析

**场景**：示例 ERP 系统中，物料号 SP-6206 将升级为 SP-6206-V2（供应商从 NSK 切换为 SKF，价格变化但规格不变）。

**问题**：
1. 哪些 DITA 主题需要更新？（列出搜索策略）
2. 文档变更的优先级是什么？（哪个先更新，哪个后更新？）
3. 设计一个自动化脚本，定期检查 ERP 物料号与 DITA 中引用的一致性。
4. 翻译版本如何处理？（已译成英语、西班牙语）

---

<!-- _class: keypoint -->

## 关键要点：系统集成

- MES 集成通过 REST API 按需提供 JSON 格式操作指导
- PLM 工程变更通过 Webhook 自动触发文档审查
- ERP 物料号变更影响全链路文档，需建立引用追踪
- 集成中间件 (Apache Camel) 编排跨系统工作流
- 安全模型按系统类型分级：MES 只读，PLM 可写

---

<!-- _class: divider -->

# 第六部分
## 扩展主题方案

---

## Subject Scheme 回顾与应用场景

Subject Scheme 是 DITA 的**受控词汇管理机制**，用于定义和管理分类与条件属性。

在制造业中用于管控：

| 应用场景 | 管控对象 | 示例 |
|---------|---------|------|
| **产品线** | props/prod 属性 | MC-5000, DR-6000, DR-7000 |
| **受众** | props/audience 属性 | 操作工, 维护工程师, 质量检验员 |
| **安全等级** | props/otherprops | 通用, 受限, 机密 |
| **文档类型** | 分类法 | 操作手册, 维护手册, 备件目录 |
| **区域** | 自定义属性 | 中国, 东南亚, 欧洲, 美洲 |

---

## 示例产品线 Subject Scheme

```xml
<subjectScheme>
  <subjectdef keys="products" navtitle="产品线">
    <subjectdef keys="cnc" navtitle="CNC 加工中心">
      <subjectdef keys="dr-5000" navtitle="MC-5000 精密CNC">
        <subjectdef keys="dr5000-base" navtitle="基础型"/>
        <subjectdef keys="dr5000-pro" navtitle="专业型"/>
        <subjectdef keys="dr5000-5axis" navtitle="五轴型"/>
      </subjectdef>
      <subjectdef keys="dr-6000" navtitle="DR-6000 大型CNC"/>
      <subjectdef keys="dr-7000" navtitle="DR-7000 高速CNC"/>
    </subjectdef>
    <subjectdef keys="robot" navtitle="工业机器人">
      <subjectdef keys="rx-200" navtitle="RX-200 六轴"/>
      <subjectdef keys="rx-500" navtitle="RX-500 重载"/>
    </subjectdef>
    <subjectdef keys="injection" navtitle="注塑机"/>
  </subjectdef>
</subjectScheme>
```

---

## 安全等级分类方案

```xml
<subjectScheme>
  <subjectdef keys="safety-classification" navtitle="安全等级">
    <subjectdef keys="safety-general" navtitle="通用安全">
      <subjectdef keys="general-ppe" navtitle="PPE 要求"/>
      <subjectdef keys="general-housekeeping" navtitle="厂房整理"/>
    </subjectdef>
    <subjectdef keys="safety-machine" navtitle="机器安全">
      <subjectdef keys="machine-interlock" navtitle="安全联锁"/>
      <subjectdef keys="machine-guard" navtitle="防护装置"/>
      <subjectdef keys="machine-eStop" navtitle="急停系统"/>
    </subjectdef>
    <subjectdef keys="safety-process" navtitle="过程安全">
      <subjectdef keys="process-coolant" navtitle="冷却液安全"/>
      <subjectdef keys="process-chip" navtitle="切屑处理"/>
      <subjectdef keys="process-fire" navtitle="防火安全"/>
    </subjectdef>
  </subjectdef>
</subjectScheme>
```

---

## 枚举值绑定与约束

```xml
<!-- 将产品属性绑定到 Subject Scheme -->
<enumerationdef>
  <attributedef name="product"/>
  <subjectdef keyref="products"/>
</enumerationdef>

<!-- 将安全分类绑定到 otherprops -->
<enumerationdef>
  <attributedef name="otherprops"/>
  <subjectdef keyref="safety-classification"/>
</enumerationdef>

<!-- 在 ditaval 中使用 -->
<val>
  <prop att="product" action="exclude"/>
  <prop att="product" val="dr-5000" action="include"/>
  <prop att="product" val="dr-6000" action="exclude"/>
</val>
```

工具验证：oXygen 自动检查 `@product` 属性值是否在 subjectScheme 中定义。

---

## 主题方案驱动的内容过滤

```
同一个 source DITA，根据产品线生成不同输出:

┌─────────────────────────────────────────────┐
│           machine-task.dita                  │
│  包含 MC-5000, DR-6000, DR-7000 条件内容     │
└──────────┬──────────┬──────────┬────────────┘
           │          │          │
    ┌──────┴──┐  ┌────┴────┐  ┌──┴──────┐
    │ ditaval │  │ ditaval  │  │ ditaval │
    │ MC-5000 │  │ DR-6000 │  │ DR-7000 │
    └──────┬──┘  └────┬────┘  └──┬──────┘
           │          │          │
    ┌──────┴──┐  ┌────┴────┐  ┌──┴──────┐
    │ MC-5000 │  │ DR-6000 │  │ DR-7000 │
    │ 操 作   │  │ 操 作   │  │ 操 作   │
    │ 手 册   │  │ 手 册   │  │ 手 册   │
    └─────────┘  └─────────┘  └─────────┘
```

---

## 自定义分类属性

```xml
<!-- 在 domain .ent 文件中声明自定义属性 -->
<!ENTITY % manufacturing-props-d-dec
  PUBLIC "-//DINGRUI//ENTITIES DITA Manufacturing Props//EN"
         "manufacturing-props-domain.ent"
>
%manufacturing-props-d-dec;

<!-- 在 domain .mod 中定义新属性 -->
<!ATTLIST topic
  machine-type (mill|lathe|drill|grind|edm) #IMPLIED
  material-class (steel|aluminum|titanium|plastic|composite) #IMPLIED
  tolerance-grade (it6|it7|it8|it9|it10) #IMPLIED
>

<!-- 在 subjectScheme 中约束枚举值 -->
<subjectScheme>
  <enumerationdef>
    <attributedef name="machine-type"/>
    <subjectdef keyref="machine-types"/>
  </enumerationdef>
</subjectScheme>
```

---

<!-- _class: exercise -->

## 练习九：设计审核工作流的分类方案

**任务**：示例需要管控每个 DITA 主题的审核状态。

设计一个 Subject Scheme，定义以下分类：
1. **审核状态**: 草稿、同行审核中、SME 审核中、合规审核中、已批准、已废弃
2. **优先级**: 紧急（24h）、高（3天）、中（1周）、低（2周）
3. **翻译状态**: 待翻译、翻译中、翻译审核中、已翻译

并为每个分类配置枚举绑定和 ditaval 示例。

---

<!-- _class: keypoint -->

## 关键要点：主题方案扩展

- Subject Scheme 是分类管理的核心机制
- 产品线、安全等级、受众、审核状态全部用 Subject Scheme 管理
- 枚举绑定确保属性值在受控词汇范围内
- ditaval 过滤实现"一套源文件，多产品输出"
- 自定义属性通过 domain module 扩展

---

<!-- _class: divider -->

# 第七部分
## 总结与行动建议

---

## 模块总结

| 部分 | 核心内容 | 关键输出 |
|------|---------|---------|
| 自定义主题类型 | 制造业 machine-task 主题设计与 DTD/Mod 实现 | machine-task.dtd + .mod + .ent |
| 插件开发 | DITA-OT 插件架构与 HTML5/PDF 自定义处理 | mfg-custom 插件 + XSLT 模板 |
| PDF 自定义 | XSL-FO 页面版式、品牌属性集、安全样式 | 封面 + 页眉页脚 + CNC 参数表 |
| 新型输出格式 | JSON API / 知识图谱 / 移动端 H5 | MES 可消费的 JSON 合约 |
| 系统集成 | MES/PLM/ERP 双向集成与自动化触发 | API 架构 + Webhook 设计 |
| 主题方案 | 受控词汇管理、枚举绑定、多产品过滤 | Subject Scheme + ditaval |

---

## 示例框架自定义 6 个月路线图

```
第 1-2 月: 主题类型专用化
  ✅ machine-task DTD/Mod 设计与实现
  ✅ 安全联锁元素集成
  ✅ CNC 参数专属元素定义
  ✅ 验证：从 topic 安全降级

第 3-4 月: 插件开发与 PDF
  ✅ mfg-custom DITA-OT 插件发布 v1.0
  ✅ HTML5 自定义输出上线
  ✅ PDF 品牌模板 (封面 + 版式) 上线
  ✅ 中文字体与配色方案确认

第 5-6 月: 集成与优化
  ✅ MES JSON API 接口上线
  ✅ PLM ECO Webhook 集成
  ✅ Subject Scheme 发布 v1.0
  ✅ 集成测试：端到端文档流水线
```

---

<!-- _class: exercise -->

## 综合练习：DR-6000 新产品的文档框架搭建

**场景**：示例将发布新产品 DR-6000 大型 CNC。你需要在一周内为其准备好 DITA 文档框架。

**任务清单**：
1. 更新 Subject Scheme，新增 DR-6000 产品线及其子型号
2. 评估 MC-5000 的 machine-task 是否可直接复用（至少列出 3 个差异点）
3. 创建 DR-6000 专属的 ditaval 过滤文件
4. 定义 DR-6000 特有的大工件加工安全联锁元素
5. 配置 CI/CD 流水线支持 DR-6000 构建目标
6. 设计 MES 接口如何区分 MC-5000 和 DR-6000 工艺参数

请给出每项任务的具体实施方案和关键代码片段。

---

## 推荐工具与参考

| 资源 | 类型 | 说明 |
|------|------|------|
| DITA-OT 官方文档 | 在线 | dita-ot.org/dev |
| "DITA for Print" by Leigh White | 图书 | PDF 自定义深度指南 |
| XSL-FO 规范 (W3C) | 标准 | w3.org/TR/xsl11 |
| oXygen XML Editor | 工具 | 最佳 DITA 创作与调试环境 |
| Apache Camel | 工具 | 制造业集成中间件 |
| Antenna House Formatter | 工具 | 商业级 PDF 渲染引擎 |

---

## 谢谢

**DITA 专家级培训 · 模块 5**
**框架自定义与系统集成**

*示例制造 MC-5000 CNC 文档中心*
*如有疑问请联系 DITA CoE*
