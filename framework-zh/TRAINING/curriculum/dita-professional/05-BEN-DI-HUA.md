---
marp: true
theme: dita-training
class: invert
paginate: true
---

# 本地化工作流

## DITA Professional — 模块 5

**多语言内容管理与翻译自动化**

---

## 学习目标

完成本模块后，您将能够：

- 理解制造业文档本地化的商业价值与行业要求
- 掌握 DITA 本地化标准工作流（提取-翻译-合并）
- 使用 XLIFF 格式作为翻译交换标准
- 创建并使用翻译记忆库 (TMX) 降低重复翻译成本
- 建立术语管理系统确保翻译一致性
- 了解框架 9 语言支持架构
- 配置自动化翻译管道并与供应商协作

---

## 为什么制造业需要本地化

| 驱动因素 | 具体表现 | 本地化需求 |
|----------|----------|------------|
| **全球市场** | MC-5000 出口欧美日韩 | 操作手册需 6+ 语言版本 |
| **法规遵从** | 欧盟机械指令 2006/42/EC | 必须提供目标国官方语言文档 |
| **安全生产** | 安全警告理解偏差导致事故 | ISO 12100 要求可理解的风险提示 |
| **客户满意** | 本地语言文档降低支持工单 | 减少 40% 售后技术支持请求 |
| **品牌形象** | 专业多语言文档 = 国际企业形象 | 竞争差异化和品牌溢价 |

---

## 示例制造的全球化布局

```
                    ┌──────────────────────────┐
                    │  MC-5000 CNC 本地化需求   │
                    ├──────────────────────────┤
                    │  出货区域  │  语言要求     │
                    ├────────────┼──────────────┤
                    │  中国大陆  │  简体中文     │
                    │  中国台湾  │  繁体中文     │
                    │  日本      │  日语         │
                    │  韩国      │  韩语         │
                    │  德国      │  德语         │
                    │  法国      │  法语         │
                    │  西班牙    │  西班牙语     │
                    │  美国      │  英语         │
                    │  巴西      │  葡萄牙语     │
                    └────────────┴──────────────┘
```

---

## DITA 本地化核心优势

| 传统本地化 | DITA 本地化 |
|------------|-------------|
| 全文翻译 Word/PDF 文档 | 主题级增量翻译 |
| 重复内容反复翻译 | Conref 引用仅译一次 |
| 格式调整耗费大量时间 | 内容与格式分离 |
| 文件锁限制并行工作 | 主题级并行翻译 |
| 翻译更新需整本对比 | 仅翻译变更主题 |
| 人工统计字数报价不透明 | XLIFF 自动提取字数统计 |

> DITA 主题化结构可将翻译成本降低 **35-50%**，翻译周期缩短 **40%**

---

## 本地化标准工作流

```
 ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
 │ 源语言   │────▶│ 提取    │────▶│ 翻译    │────▶│ 合并    │
 │ 创作     │     │ XLIFF   │     │ 供应商   │     │ 多语言   │
 └─────────┘     └─────────┘     └─────────┘     └─────────┘
                      │                               │
                      ▼                               ▼
               ┌─────────┐                    ┌─────────┐
               │ TMX/术语 │                    │ 质量审查  │
               │ 记忆库    │                    │ LQI 检查  │
               └─────────┘                    └─────────┘
```

**四阶段模型**：创作 → 提取 → 翻译 → 合并与发布

---

## 阶段一：源语言创作规范

在开始本地化之前，源语言内容必须满足以下要求：

| 规范项 | 要求 | 目的 |
|--------|------|------|
| 句子完整性 | 每个 `<p>` 为完整句子 | TM 匹配粒度 |
| 术语一致性 | 使用受控词汇表术语 | 提高 TM 命中率 |
| 无内嵌文本 | 避免在图中嵌入文字 | 减少图像再制作成本 |
| 文化中性 | 避免文化特定比喻/习语 | 降低翻译歧义 |
| 索引标记 | 提前标记 `<indexterm>` | 翻译后索引自动正确 |
| `xml:lang` 标记 | 源文件准确设定 `xml:lang="zh-CN"` | 识别源语言 |

---

## 阶段二：提取 XLIFF 文件

**XLIFF**（XML Localization Interchange File Format）是翻译交换标准。

```bash
# 使用 DITA-OT 提取 XLIFF
dita -i manual-coolpro5000.ditamap \
     -f xliff \
     -Dtarget.lang=ja-JP \
     -Doutput.dir=localization/xliff/ \
     --filter=ditaval/product-coolpro5000.ditaval
```

提取后的文件结构：
```
localization/
  └── xliff/
      └── ja-JP/
          ├── concept-cooling-system.xliff
          ├── task-replace-filter.xliff
          └── reference-coolant-specs.xliff
```

---

## XLIFF 文件结构

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
  <file original="task-replace-filter.dita"
        source-language="zh-CN" target-language="ja-JP"
        datatype="x-dita">
    <header>
      <skl>
        <external-file href="task-replace-filter.dita"/>
      </skl>
      <phase-group>
        <phase phase-name="extraction" process-name="DITA-OT"/>
      </phase-group>
    </header>
    <body>
      <trans-unit id="title" resname="title">
        <source xml:lang="zh-CN">更换冷却液过滤器</source>
        <target xml:lang="ja-JP"></target>
      </trans-unit>
      <trans-unit id="step1" resname="step">
        <source xml:lang="zh-CN">关闭冷却泵电源</source>
        <target xml:lang="ja-JP"></target>
      </trans-unit>
    </body>
  </file>
</xliff>
```

---

## XLIFF trans-unit 元素详解

每个 `<trans-unit>` 代表一个翻译单元：

| 元素 | 说明 | 示例 |
|------|------|------|
| `@id` | 翻译单元唯一标识 | `title` / `step1` |
| `<source>` | 源语言文本 | `<source xml:lang="zh-CN">更换冷却液过滤器</source>` |
| `<target>` | 目标语言（翻译后填入） | `<target xml:lang="ja-JP">冷却液フィルターの交換</target>` |
| `<note>` | 翻译者注释 | `<note from="developer">技术术语，请保持一致性</note>` |
| `@state` | 翻译状态 | `new` / `translated` / `reviewed` / `final` |

---

## XLIFF 状态管理

```
       ┌──────┐
       │ new  │ ← 新提取，尚未翻译
       └──┬───┘
          │
          ▼
    ┌─────────────┐
    │ translated  │ ← 翻译完成，进入审查
    └──────┬──────┘
           │
           ▼
    ┌────────────┐
    │ reviewed   │ ← 审校完成，修改已标注
    └──────┬─────┘
           │
           ▼
    ┌────────────┐
    │ final      │ ← 终审通过，可合并
    └────────────┘
```

---

## 阶段三：翻译记忆库 (TMX)

**TMX**（Translation Memory eXchange）存储已翻译的句对，实现内容复用。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tmx version="1.4">
  <header creationtool="MemoQ" srclang="zh-CN"
          adminlang="en-US" datatype="x-dita"
          o-tmf="OmegaT" segtype="sentence">
  </header>
  <body>
    <tu>  <!-- Translation Unit — 翻译记忆单元 -->
      <tuv xml:lang="zh-CN">
        <seg>关闭冷却泵电源</seg>
      </tuv>
      <tuv xml:lang="en-US">
        <seg>Turn off the coolant pump power</seg>
      </tuv>
    </tu>
  </body>
</tmx>
```

---

## TMX 实践策略

| 策略 | 说明 | 预期收益 |
|------|------|----------|
| **共享记忆库** | 所有项目共用公司级 TMX | 跨产品复用 30-50% |
| **产品分类** | 按产品线划分子记忆库 | 提高匹配精度 |
| **定期更新** | 每次翻译完成反哺 TMX | 记忆库持续增长 |
| **模糊匹配** | 70-99% 相似度提示人工确认 | 进一步降低翻译量 |
| **100% 匹配** | 完全匹配自动填入，0 成本 | 重复内容零翻译费 |

> 框架目标：TM 命中率达到 **65%+**（首次项目后）

---

## TM 匹配类型

```
┌─────────────────┬─────────┬──────────┬────────────┐
│ 匹配类型         │ 相似度   │ 翻译费用  │ 处理方式    │
├─────────────────┼─────────┼──────────┼────────────┤
│ 上下文匹配       │ 101%    │ 0%       │ 自动填充    │
│ 完全匹配 (Exact) │ 100%    │ 0-5%     │ 自动填充    │
│ 模糊匹配 (Fuzzy) │ 85-99%  │ 30-50%   │ 人工修改    │
│ 模糊匹配         │ 70-84%  │ 60-70%   │ 人工修改    │
│ 无匹配 (No Match)│ <70%    │ 100%     │ 从头翻译    │
└─────────────────┴─────────┴──────────┴────────────┘
```

---

## 术语管理

**术语管理系统**（Terminology Management System, TMS）确保关键术语翻译一致。

```
术语库文件：glossary/TB-MFG-2025.tbx

术语条目结构：
┌─────────────────────────────────────────┐
│ termEntry                                │
│   ├── langSet xml:lang="zh-CN"           │
│   │   └── term: 冷却泵                    │
│   ├── langSet xml:lang="en-US"           │
│   │   └── term: Coolant Pump             │
│   ├── langSet xml:lang="ja-JP"           │
│   │   └── term: 冷却液ポンプ              │
│   └── descrip: 机床冷却液循环动力装置      │
└─────────────────────────────────────────┘
```

---

## TBX 术语库示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<martif type="TBX" xml:lang="zh-CN">
  <martifHeader>
    <fileDesc>
      <titleStmt><title>示例制造 — 制造术语库 v2.3</title></titleStmt>
      <sourceDesc><p>示例技术文档部</p></sourceDesc>
    </fileDesc>
  </martifHeader>
  <text>
    <body>
      <termEntry id="TE-001">
        <langSet xml:lang="zh-CN">
          <ntig><termGrp><term>主轴</term></termGrp></ntig>
        </langSet>
        <langSet xml:lang="en-US">
          <ntig><termGrp><term>Spindle</term></termGrp></ntig>
        </langSet>
        <langSet xml:lang="ja-JP">
          <ntig><termGrp><term>スピンドル</term></termGrp></ntig>
        </langSet>
      </termEntry>
    </body>
  </text>
</martif>
```

---

## 框架 9 语言架构

```
language/
├── zh-CN/          # 简体中文（源语言）
│   ├── topics/
│   └── maps/
├── zh-TW/          # 繁体中文
├── en-US/          # 英语（美国）
├── ja-JP/          # 日语
├── ko-KR/          # 韩语
├── de-DE/          # 德语
├── fr-FR/          # 法语
├── es-ES/          # 西班牙语
└── pt-BR/          # 葡萄牙语（巴西）
```

每种语言独立目录，结构镜像源语言，通过 `xml:lang` 属性区分。

---

## 语言目录映射策略

```xml
<!-- ditamap 中的语言感知引用 -->
<map xml:lang="zh-CN">
  <title>MC-5000 操作与维护手册</title>
  <topicref href="topics/concept-machine-overview.dita"/>
  <topicref href="topics/task-startup.dita"/>
</map>
```

翻译后的目录结构：

```
en-US/
├── topics/
│   ├── concept-machine-overview.dita    ← 翻译版
│   └── task-startup.dita                ← 翻译版
└── maps/
    └── manual-dr5000.ditamap            ← navtitle 本地化
```

> 文件路径保持不变，通过语言目录切换内容

---

## 与翻译供应商协作

```
┌──────────────┐         ┌──────────────────┐
│  示例技术部   │◄───────▶│  翻译供应商 (LSP)  │
│              │         │                  │
│ 1. 生成XLIFF  │────────▶│ 2. 导入 CAT 工具  │
│ 4. 合并审校    │◄────────│ 3. 翻译+QA       │
│ 5. 输出/DITA-OT│         │                  │
└──────────────┘         └──────────────────┘
```

**交付包内容**：
```
delivery-2025-06-07/
├── source/            # 源 XLIFF 文件
├── translated/        # 翻译后 XLIFF (target 已填充)
├── tmx/               # 本次翻译产出的 TMX 更新
├── query-log.xlsx     # 翻译过程中提出的问题
└── quality-report.xlsx  # LQI 评分报告
```

---

## 翻译质量评估 (LQI)

**LQI**（Localization Quality Index）评估维度：

| 维度 | 权重 | 检查内容 |
|------|------|----------|
| **准确性** | 30% | 术语正确、无漏译、无多译 |
| **语言质量** | 25% | 语法、拼写、表达自然度 |
| **风格一致** | 15% | 与术语库和风格指南一致 |
| **国家适应性** | 10% | 日期格式、单位、文化适配 |
| **技术验证** | 20% | XML 结构完整性、属性保留 |

扣分标准：Critical (10 分) > Major (5 分) > Minor (1 分)

---

## 自动化翻译管道

```
Git Push (zh-CN 源文件变更)
        │
        ▼
  CI/CD Pipeline
        │
        ├── Step 1: 检测变更主题
        ├── Step 2: 提取 XLIFF (仅变更主题)
        ├── Step 3: 与 TMX 预翻译（100% 匹配自动填充）
        ├── Step 4: 打包发送至翻译管理系统 (TMS)
        ├── Step 5: 人工翻译 + QA
        ├── Step 6: 合并翻译后 XLIFF
        ├── Step 7: DITA-OT 验证目标语言结构
        └── Step 8: 构建多语言输出
```

---

## 自动化管道脚本

```bash
#!/bin/bash
# scripts/auto-localization.sh

SOURCE_LANG="zh-CN"
TARGET_LANGS=("en-US" "ja-JP" "ko-KR" "de-DE")
CHANGED_FILES=$(git diff --name-only HEAD~1 -- '*.dita')

echo "变更文件: $CHANGED_FILES"

for lang in "${TARGET_LANGS[@]}"; do
  echo "=== 处理目标语言: $lang ==="

  # 提取 XLIFF（仅变更的主题）
  dita -i manual-dr5000.ditamap \
       -f xliff \
       -Dtarget.lang=$lang \
       -Doutput.dir=localization/xliff/$lang/

  # 使用 TMX 预翻译
  omegaT-preprocess --tmx=tm/shared-memory.tmx \
                    --xliff=localization/xliff/$lang/

  # 验证 XLIFF 完整性
  xliff-validator localization/xliff/$lang/
done

echo "管道执行完毕"
```

---

## CAT 工具集成矩阵

| CAT 工具 | XLIFF 导入 | TMX 支持 | TBX 术语 | DITA 感知 | 推荐度 |
|----------|------------|----------|----------|-----------|--------|
| **MemoQ** | 原生 | 原生 | 原生 | 需插件 | 企业首选 |
| **Trados Studio** | 原生 | 原生 | 原生 | 需配置 | 市场主流 |
| **OmegaT** | 原生 | 原生 | 原生 | Okapi 插件 | 开源方案 |
| **Phrase (Memsource)** | 原生 | 原生 | 原生 | 原生 | 云端协作 |
| **Smartcat** | 原生 | 原生 | 原生 | 有限 | 快速上手 |

---

## DITA-OT 本地化参数

```bash
# 英文输出
dita -i manual-dr5000.ditamap \
     -f html5 \
     --filter=ditaval/product-dr5000.ditaval \
     -Dargs.default.language=en-US \
     -Doutput.dir=output/en-US/html/

# 日文输出
dita -i manual-dr5000.ditamap \
     -f pdf2 \
     --filter=ditaval/product-dr5000.ditaval \
     -Dargs.default.language=ja-JP \
     -Dargs.bfo.font.mappings=fonts/bfo-ja-JP.xml \
     -Doutput.dir=output/ja-JP/pdf/
```

**关键参数**：`-Dargs.default.language` 指定目标语言，影响排序、断字、字体选择。

---

## 翻译状态追踪

```xml
<!-- 在 ditamap 中使用 @translate 控制翻译范围 -->
<topicref href="topics/concept-machine-overview.dita"
          translate="yes"/>               <!-- 需要翻译 -->

<topicref href="shared/company-info.dita"
          translate="no"/>                <!-- 不需要翻译（法律固定文本） -->

<topicref href="warnings/hazard-flying-chips.dita"
          translate="yes"
          xml:lang="zh-CN"/>              <!-- 源语言标记 -->
```

框架建议在 top-level ditamap 中使用 `processing-role="resource-only"` 标记无需翻译的共享内容。

---

## 语言特定注意事项

| 语言 | 文本膨胀率 | 特殊要求 |
|------|------------|----------|
| **en-US** | -15% (比中文) | 被动语态偏好，冠词系统 |
| **ja-JP** | +20% | 三方角（敬语等级），标点差异 |
| **ko-KR** | +10% | 敬语体系，空格规则 |
| **de-DE** | +25% | 复合名词极长（影响 PDF 排版） |
| **fr-FR** | +20% | 空格与标点特殊规则（espace insécable） |
| **es-ES** | +20% | 性别一致（动词/形容词） |
| **pt-BR** | +15% | 正式/非正式 "你" 选择 |

> 排版设计需为膨胀率预留 **30%** 空间余量

---

## 文本膨胀率对 PDF 排版的影响

```
中文源 (100%)：
│■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■│

德语翻译 (125%)：
│■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■ ■│
                                              ↑ 溢出风险

对策：
- 表格列宽使用相对值（proportional-column-width）
- 避免硬编码绝对列宽
- 为德语/法语创建独立 CSS，允许更大字体行间距
```

---

## 翻译循环与版本管理

```
版本追踪矩阵：

源文件版本     翻译版本        状态        操作
v1.0.zh-CN    v1.0.en-US     ✓ 同步      无需
v1.1.zh-CN    v1.0.en-US     ⚠ 过期      增量更新
v2.0.zh-CN    v1.0.en-US     ✗ 大幅落后  重新提取

实现方式：
- DITA-OT 对比两张 map 的 topicref 列表
- 仅提取 hash 值变化的主题 XLIFF
- 生成增量 XLIFF 包（仅含变更的 trans-unit）
```

---

<!-- _class: keypoint -->

## 关键设计决策：集中式 vs 分散式

```
集中式 TMX                        分散式 TMX
┌─────────────────┐            ┌─────────────────┐
│ 公司级单一 TMX   │            │ 各产品线独立 TMX │
│ 跨产品复用率高    │            │ 匹配精度高       │
│ 噪音/误匹配风险  │            │ 复用范围受限     │
│ 维护简单         │            │ 维护复杂         │
└─────────────────┘            └─────────────────┘

示例选择：混合模式
  → 公司级核心 TMX（安全术语、公司介绍、通用警告）
  → 产品级 TMX（MC-5000 专有、SpindleXT300 专有）
  → 翻译时两者同时查询，优先级：产品级 > 公司级
```

---

<!-- _class: exercise -->

## 练习：为 MC-5000 创建日文翻译包

**任务**：

1. 从现有主题中选择 3 个文件（1 个 concept、1 个 task、1 个 reference）
2. 使用 DITA-OT 提取日文 (ja-JP) XLIFF
3. 打开一个 XLIFF 文件，找到对应标题的 `<trans-unit>`
4. 在 `<target>` 中填入模拟日文翻译（可使用假名占位）
5. 在共享目录 `shared/` 中创建一个 TBX 术语条目：**主轴/Spindle/スピンドル**
6. 在 Map 文件中为新主题添加 `translate="yes"` 属性

**验证**：XLIFF 中所有 `<trans-unit>` 的 `@state` 应为 `translated`

---

## 关键要点

- DITA 本地化通过 **主题粒度增量翻译** 降低 35-50% 成本
- **XLIFF** 是标准翻译交换格式，支持状态追踪和往返合并
- **TMX** 记忆库核心指标：首次项目后达到 **65%+** 命中率
- **TBX 术语库** 确保关键术语在所有目标语言中翻译一致
- 框架支持 **9 种语言**，通过并行目录结构管理
- 自动化管道实现：Git Push → CI/CD 提取 → TM 预翻 → 供应商 → 合并
- 翻译质量通过 **LQI 评分** 量化，Critical/Major/Minor 三级扣分

---

## 下一步

模块 6：**元数据治理**

学习 M1-M7 元数据规则体系，使用 Schematron 实现自动化验证，确保文档质量指标可量化、可追踪
