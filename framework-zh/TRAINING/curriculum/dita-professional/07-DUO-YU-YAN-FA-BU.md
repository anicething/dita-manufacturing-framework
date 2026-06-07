---
marp: true
theme: dita-training
class: invert
paginate: true
---

# 多语言发布

## DITA Professional — 模块 7

**跨语言构建策略与 CJK 字体管理**

---

## 学习目标

完成本模块后，您将能够：

- 设计并实施多语言并行构建策略
- 在 DITA-OT 中配置 CJK（中日韩）字体映射
- 创建语言特定的 CSS/XSL 样式表
- 编写批处理发布脚本实现一键多语言输出
- 集成 CI/CD 实现自动多语言发布
- 管理 `@xml:lang` 属性实现正确的语言切换
- 实施语言回退机制确保内容不缺失

---

## 多语言发布的挑战

| 挑战 | 中文 | 日文 | 韩文 | 英文 |
|------|------|------|------|------|
| **字体** | 宋体/黑体 | MS Mincho | Malgun Gothic | Arial/Times |
| **文本方向** | 横排/竖排 | 横排/竖排 | 横排 | 横排 |
| **断字规则** | 无断字 | 无断字 | 无断字 | 音节断字 |
| **标点挤压** | 是 (CJK) | 是 (CJK) | 是 (CJK) | 否 |
| **对齐方式** | 两端均等 | 两端均等 | 两端均等 | 左对齐 |
| **页码格式** | 中文数字 | 阿拉伯/汉字 | 阿拉伯 | 阿拉伯 |
| **索引排序** | 拼音/笔画 | 五十音 | 谚文 | 字母 |

---

## 多语言发布路径矩阵

```
源语言: zh-CN
 ─────────────────────────────────────────────────
 目标语言   │ HTML5  │  PDF   │  EPUB  │  Help
 ───────────┼────────┼────────┼────────┼────────
 en-US      │   ✓    │   ✓    │   ✓    │   ✓
 zh-TW      │   ✓    │   ✓    │   ✓    │   ✓
 ja-JP      │   ✓    │   ✓    │   ✓    │   —
 ko-KR      │   ✓    │   ✓    │   ✓    │   —
 de-DE      │   ✓    │   ✓    │   —    │   —
 fr-FR      │   ✓    │   ✓    │   —    │   —
 es-ES      │   ✓    │   ✓    │   —    │   —
 pt-BR      │   ✓    │   ✓    │   —    │   —
 ───────────┴────────┴────────┴────────┴────────
 总计: 8 × 3 = 24 个输出组合（需自动化）
```

---

## 构建策略一：串行构建

**最简单但最慢**：按语言逐个构建。

```bash
#!/bin/bash
# 串行构建 — 适用于小项目或调试阶段

TARGETS=("en-US" "ja-JP" "ko-KR" "de-DE" "fr-FR" "es-ES" "pt-BR" "zh-TW")
FORMATS=("html5" "pdf2")

for lang in "${TARGETS[@]}"; do
  for fmt in "${FORMATS[@]}"; do
    echo "=== Building $lang / $fmt ==="
    dita -i language/$lang/maps/manual-dr5000.ditamap \
         -f $fmt \
         -Dargs.default.language=$lang \
         -Doutput.dir=output/$lang/$fmt/ \
         --filter=ditaval/product-dr5000.ditaval
  done
done
```

> 耗时估算：8 语言 × 2 格式 = 16 次构建，每次 ~2 分钟，总耗时 ~32 分钟

---

## 构建策略二：并行构建

**高性能方案**：利用多核 CPU 同时构建。

```bash
#!/bin/bash
# 并行构建 — 适用于生产环境

TARGETS=("en-US" "ja-JP" "ko-KR" "de-DE" "fr-FR" "es-ES" "pt-BR" "zh-TW")

MAX_PARALLEL=4  # 根据 CPU 核心数调整
CURRENT=0

for lang in "${TARGETS[@]}"; do
  {
    echo "Building $lang..."
    dita -i language/$lang/maps/manual-dr5000.ditamap \
         -f pdf2 \
         -Dargs.default.language=$lang \
         -Doutput.dir=output/$lang/pdf/ \
         --filter=ditaval/product-dr5000.ditaval \
         > logs/build-$lang.log 2>&1
    echo "[DONE] $lang"
  } &

  CURRENT=$((CURRENT + 1))
  if [ $CURRENT -ge $MAX_PARALLEL ]; then
    wait -n   # 等待任一个完成
    CURRENT=$((CURRENT - 1))
  fi
done
wait
echo "=== 全部语言构建完成 ==="
```

---

## 构建策略三：增量构建

**仅构建有变更的语言/主题**，适合频繁迭代。

```bash
#!/bin/bash
# 增量构建 — 对比 Git diff 确定变更范围

CHANGED_LANGS=()

# 检测哪些语言目录有变更
for lang_dir in language/*/; do
  lang=$(basename "$lang_dir")
  if [ "$lang" = "zh-CN" ]; then
    continue  # 跳过源语言
  fi
  # 检查自上次构建标签以来的变更
  if ! git diff --quiet build-$(date +%Y-%m-%d) -- "$lang_dir"; then
    CHANGED_LANGS+=("$lang")
  fi
done

echo "需要重建的语言: ${CHANGED_LANGS[*]}"

for lang in "${CHANGED_LANGS[@]}"; do
  dita -i language/$lang/maps/manual-dr5000.ditamap \
       -f html5 -Doutput.dir=output/$lang/html5/
done

# 创建新构建标签
git tag -f "build-$(date +%Y-%m-%d)"
```

---

## DITA-OT CJK 字体配置概述

DITA-OT PDF 输出的 CJK 字体配置涉及两个层面：

```
┌─────────────────────────────────────────────┐
│  层面 1: 字体注册                            │
│  ┌─────────────────────────────────────┐    │
│  │ 告诉 DITA-OT 字体文件在哪里            │    │
│  │ → fop.xconf / chemistry-fonts.xml    │    │
│  └─────────────────────────────────────┘    │
│                    ↓                         │
│  层面 2: 字体映射                            │
│  ┌─────────────────────────────────────┐    │
│  │ 告诉 DITA-OT 什么逻辑字体 = 什么物理字体 │    │
│  │ → font-mappings.xml                 │    │
│  └─────────────────────────────────────┘    │
│                    ↓                         │
│  层面 3: 字体选择逻辑                         │
│  ┌─────────────────────────────────────┐    │
│  │ 根据 @xml:lang 自动选择对应字体        │    │
│  │ → custom.xsl / i18n-config.xml       │    │
│  └─────────────────────────────────────┘    │
└─────────────────────────────────────────────┘
```

---

## 层面一：FOP 字体注册

```xml
<!-- cfg/fop/fop.xconf -->
<fop version="1.0">
  <renderers>
    <renderer mime="application/pdf">
      <fonts>

        <!-- 中文字体 -->
        <font kerning="yes" embed-url="fonts/NotoSansSC-Regular.otf"
              embedding-mode="subset">
          <font-triplet name="Noto Sans SC" style="normal" weight="normal"/>
        </font>
        <font kerning="yes" embed-url="fonts/NotoSansSC-Bold.otf"
              embedding-mode="subset">
          <font-triplet name="Noto Sans SC" style="normal" weight="bold"/>
        </font>

        <!-- 日文字体 -->
        <font kerning="yes" embed-url="fonts/NotoSansJP-Regular.otf"
              embedding-mode="subset">
          <font-triplet name="Noto Sans JP" style="normal" weight="normal"/>
        </font>

```

---

## 层面一：FOP 字体注册（续）

```xml
        <!-- 韩文字体 -->
        <font kerning="yes" embed-url="fonts/NotoSansKR-Regular.otf"
              embedding-mode="subset">
          <font-triplet name="Noto Sans KR" style="normal" weight="normal"/>
        </font>

        <!-- 等宽代码字体 -->
        <font kerning="yes" embed-url="fonts/SourceCodePro-Regular.otf"
              embedding-mode="subset">
          <font-triplet name="Source Code Pro" style="normal"
                        weight="normal"/>
        </font>

        <!-- 通用后备 — 覆盖所有 Unicode -->
        <font kerning="yes" embed-url="fonts/NotoSansCJKsc-Regular.otf"
              embedding-mode="subset">
          <font-triplet name="Noto Sans CJK SC" style="normal"
                        weight="normal"/>
        </font>

      </fonts>
    </renderer>
  </renderers>
</fop>
```

---

## 层面二：字体映射文件

```xml
<!-- cfg/font-mappings.xml -->
<font-mappings>

  <!-- zh-CN 字体重映射 -->
  <font-group xml:lang="zh-CN" label="简体中文">
    <font name="Noto Sans SC">
      <weight value="normal">Noto Sans SC</weight>
      <weight value="bold">Noto Sans SC Bold</weight>
    </font>
    <font name="Noto Serif SC" face="Noto Serif CJK SC"/>
  </font-group>

  <!-- ja-JP 字体重映射 -->
  <font-group xml:lang="ja-JP" label="日本語">
    <font name="Noto Sans JP">
      <weight value="normal">Noto Sans JP</weight>
      <weight value="bold">Noto Sans JP Bold</weight>
    </font>
    <font name="Noto Serif JP" face="Noto Serif CJK JP"/>
  </font-group>

```

---

## 层面二：字体映射文件（续）

```xml
  <!-- ko-KR 字体重映射 -->
  <font-group xml:lang="ko-KR" label="한국어">
    <font name="Noto Sans KR">
      <weight value="normal">Noto Sans KR</weight>
      <weight value="bold">Noto Sans KR Bold</weight>
    </font>
  </font-group>

  <!-- en-US / 西欧语言 -->
  <font-group xml:lang="en-US" label="English">
    <font name="Noto Sans">
      <weight value="normal">Noto Sans</weight>
      <weight value="bold">Noto Sans Bold</weight>
    </font>
    <font name="Noto Serif" face="Noto Serif"/>
  </font-group>

  <!-- 默认回退 -->
  <font-group xml:lang="*" label="Fallback">
    <font name="Noto Sans CJK SC"/>
  </font-group>

</font-mappings>
```

---

## CJK 常用字体选型指南

| 语言 | 推荐无衬线体 | 推荐衬线体 | 等宽字体 | 开源替代 |
|------|-------------|-----------|----------|----------|
| **zh-CN** | Noto Sans SC | Noto Serif SC | Source Code Pro | 思源黑体/宋体 |
| **zh-TW** | Noto Sans TC | Noto Serif TC | Source Code Pro | 思源黑體/宋體 |
| **ja-JP** | Noto Sans JP | Noto Serif JP | Source Han Code JP | 源ノ角ゴシック |
| **ko-KR** | Noto Sans KR | Noto Serif KR | D2Coding | 本고딕 |
| **en-US** | Noto Sans | Noto Serif | Source Code Pro | Roboto, Open Sans |
| **de-DE** | Noto Sans | Noto Serif | Source Code Pro | 同上 |
| **fr-FR** | Noto Sans | Noto Serif | Source Code Pro | 同上 |
| **es-ES** | Noto Sans | Noto Serif | Source Code Pro | 同上 |
| **pt-BR** | Noto Sans | Noto Serif | Source Code Pro | 同上 |

> 推荐使用 Google Noto 系列，统一风格、覆盖广泛、免费商用

---

## 层面三：语言感知的字体自动选择

```xml
<!-- cfg/custom.xsl — 根据 @xml:lang 选择字体 -->
<xsl:stylesheet version="2.0"
  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

  <!-- 默认字体 -->
  <xsl:variable name="body-font-family">Noto Sans</xsl:variable>

  <!-- 标题字体按语言 -->
  <xsl:template name="select-title-font">
    <xsl:param name="lang"/>
    <xsl:choose>
      <xsl:when test="$lang = 'zh-CN'">Noto Sans SC Bold</xsl:when>
      <xsl:when test="$lang = 'ja-JP'">Noto Sans JP Bold</xsl:when>
      <xsl:when test="$lang = 'ko-KR'">Noto Sans KR Bold</xsl:when>
      <xsl:otherwise>Noto Sans Bold</xsl:otherwise>
    </xsl:choose>
  </xsl:template>

</xsl:stylesheet>
```

---

## 语言特定样式 — CSS 方案

```css
/* css/lang-zh-CN.css — 简体中文样式 */
:root {
  --body-font: "Noto Sans SC", sans-serif;
  --heading-font: "Noto Sans SC Bold", sans-serif;
  --code-font: "Source Code Pro", monospace;
  --body-size: 10.5pt;
  --line-height: 1.8;
  --text-align: justify;
  --paragraph-indent: 1.5em;           /* CJK 段落缩进 */
  --page-size: A4;
  --writing-mode: horizontal-tb;
}
```

```css
/* css/lang-ja-JP.css — 日文样式 */
:root {
  --body-font: "Noto Sans JP", sans-serif;
  --heading-font: "Noto Sans JP Bold", sans-serif;
  --body-size: 10pt;                    /* 日文通常更小 */
  --line-height: 1.75;
  --text-align: justify;
  --paragraph-indent: 1em;             /* 日文常用 1 字符缩进 */
  --page-size: A4;
}
```

---

## 语言特定样式 — 西文 CSS

```css
/* css/lang-en-US.css — 英文样式 */
:root {
  --body-font: "Noto Sans", sans-serif;
  --heading-font: "Noto Sans Bold", sans-serif;
  --code-font: "Source Code Pro", monospace;
  --body-size: 10pt;
  --line-height: 1.4;
  --text-align: left;                  /* 左对齐，非两端 */
  --paragraph-indent: 0;              /* 无缩进 */
  --paragraph-spacing: 6pt;           /* 段间距替代缩进 */
  --page-size: Letter;                /* 北美 Letter 尺寸 */
  --hyphens: auto;                    /* 英文断字 */
}
```

> 西文与 CJK 排版差异显著：对齐方式、段落标记方式、断字规则完全不同

---

## CSS 选择器与 lang 属性联动

```css
/* 通用规则 */
body {
  font-family: var(--body-font);
  font-size: var(--body-size);
  line-height: var(--line-height);
  text-align: var(--text-align);
}

/* 语言特定微调 */
:root[lang="zh-CN"] .title {
  letter-spacing: 0.05em;    /* 中文标题加大字间距 */
}

:root[lang="ja-JP"] .title {
  letter-spacing: 0.02em;
}

:root[lang="en-US"] .title {
  text-transform: uppercase; /* 英文标题全大写 */
  letter-spacing: 0.1em;
}

:root[lang="de-DE"] .note {
  word-wrap: break-word;     /* 德语长复合词自动换行 */
}
```

---

## 批处理发布脚本

```bash
#!/bin/bash
# scripts/publish-all-languages.sh
# 一键发布全部语言的 HTML + PDF

set -euo pipefail

PROJECT="manual-dr5000"
FORMATS=("html5" "pdf2")
LANGUAGES=("zh-CN" "en-US" "ja-JP" "ko-KR" "de-DE"
           "fr-FR" "es-ES" "pt-BR" "zh-TW")

TIMESTAMP=$(date +%Y%m%d-%H%M%S)
OUTPUT_ROOT="output/$TIMESTAMP"

echo "=== DITA 多语言发布管道 ==="
echo "项目: $PROJECT"
echo "输出: $OUTPUT_ROOT"
echo "============================"

```

---

## 批处理发布脚本（续）

```bash
for lang in "${LANGUAGES[@]}"; do
  MAP_FILE="language/$lang/maps/${PROJECT}.ditamap"

  if [ ! -f "$MAP_FILE" ]; then
    echo "[SKIP] $lang — Map 文件不存在: $MAP_FILE"
    continue
  fi

  for fmt in "${FORMATS[@]}"; do
    OUT_DIR="$OUTPUT_ROOT/$lang/$fmt"
    mkdir -p "$OUT_DIR"

    echo "[BUILD] $lang → $fmt"

    dita -i "$MAP_FILE" \
         -f "$fmt" \
         -Dargs.default.language="$lang" \
         -Dargs.css="css/lang-${lang}.css" \
         -Dargs.copycss=yes \
         --filter="ditaval/product-dr5000.ditaval" \
         -Doutput.dir="$OUT_DIR" \
         > "logs/build-${lang}-${fmt}.log" 2>&1

    echo "  ✓ $lang/$fmt 完成"
  done
done

echo "=== 发布完成: $OUTPUT_ROOT ==="
```

---

## CI/CD 集成：GitHub Actions

```yaml
# .github/workflows/publish-multilingual.yml
name: Multilingual Publish

on:
  push:
    branches: [main]
    paths:
      - 'language/**/*.dita'
      - 'language/**/*.ditamap'
      - 'css/lang-*.css'

jobs:
  publish-all:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        language: [zh-CN, en-US, ja-JP, ko-KR, de-DE,
                   fr-FR, es-ES, pt-BR, zh-TW]
        format: [html5, pdf2]
      fail-fast: false
```

---

## CI/CD 集成（续）

```yaml
    steps:
      - uses: actions/checkout@v4

      - name: Setup DITA-OT
        uses: dita-ot/setup-action@v1
        with:
          version: '4.2'

      - name: Build ${{ matrix.language }}/${{ matrix.format }}
        run: |
          MAP="language/${{ matrix.language }}/maps/manual-dr5000.ditamap"
          OUT="output/${{ matrix.language }}/${{ matrix.format }}"

          dita -i "$MAP" \
               -f "${{ matrix.format }}" \
               -Dargs.default.language="${{ matrix.language }}" \
               -Dargs.css="css/lang-${{ matrix.language }}.css" \
               --filter="ditaval/product-dr5000.ditaval" \
               -Doutput.dir="$OUT"

      - name: Upload Artifact
        uses: actions/upload-artifact@v4
        with:
          name: ${{ matrix.language }}-${{ matrix.format }}
          path: output/${{ matrix.language }}/${{ matrix.format }}/
```

---

## `@xml:lang` 属性管理规范

```xml
<!-- 1. 主题文件级 -->
<concept id="machine-overview" xml:lang="zh-CN">

<!-- 2. Map 文件级 -->
<map xml:lang="zh-CN">

<!-- 3. 内嵌语言切换（引用外来术语） -->
<p>根据
  <ph xml:lang="de-DE">Maschinenrichtlinie 2006/42/EG</ph>
  （欧盟机械指令）的要求...</p>

<!-- 4. 双语对照（术语定义） -->
<p>主轴 (<ph xml:lang="en-US">Spindle</ph>) 冷却系统...</p>
```

**规则**：
- 顶级元素 (`<topic>`, `<map>`) 必须声明 `@xml:lang`
- 内嵌外来语使用 `@xml:lang` 标记，便于 TTS/朗读正确发音
- 不可在同一段落中混用 `<ph>` 之外的 `@xml:lang` 切换

---

## 语言回退机制

当目标语言没有某个主题的翻译版本时，回退到更高优先级的语言：

```
回退链 (Fallback Chain):

de-DE → en-US → zh-CN (源语言)
  ↓        ↓        ↓
 德语    英语    简体中文
 (翻译版) (翻译版) (始终存在)

优先级：目标语言 > 英语 > 源语言(zh-CN)
```

```
示例：
  请求 de-DE 版本的 concept-coolant-system.dita
  → 检查 de-DE 目录：不存在
  → 回退到 en-US 目录：存在 ✓ → 使用 en-US 版本
  → 若 en-US 也不存在 → 使用 zh-CN 源语言
```

---

## 语言回退实现

```bash
#!/bin/bash
# scripts/resolve-language-fallback.sh

resolve_topic() {
  local topic_path="$1"   # e.g., "topics/concept-coolant.dita"
  local target_lang="$2"  # e.g., "de-DE"

  # 优先级 1: 目标语言
  if [ -f "language/$target_lang/$topic_path" ]; then
    echo "language/$target_lang/$topic_path"
    return
  fi

  # 优先级 2: 英语 (中介语言)
  if [ -f "language/en-US/$topic_path" ]; then
    echo "language/en-US/$topic_path"
    return
  fi

  # 优先级 3: 源语言 (zh-CN)
  if [ -f "language/zh-CN/$topic_path" ]; then
    echo "language/zh-CN/$topic_path"
    return
  fi

  echo ""  # 全部缺失
}
```

---

## Map 中的回退引用

```xml
<!-- language/de-DE/maps/manual-dr5000.ditamap -->
<map xml:lang="de-DE">
  <title>MC-5000 Betriebs- und Wartungshandbuch</title>

  <!-- de-DE 有翻译的章节 -->
  <topicref href="../topics/concept-machine-overview.dita"/>
  <topicref href="../topics/task-startup.dita"/>

  <!-- de-DE 无翻译，回退到 en-US -->
  <topicref href="../../en-US/topics/reference-torque-table.dita"
            scope="peer">
    <topicmeta>
      <navtitle>Anzugsdrehmomente (EN)</navtitle>
    </topicmeta>
  </topicref>

  <!-- en-US 也无，回退到 zh-CN 源 -->
  <topicref href="../../zh-CN/topics/appendix-parts-list.dita"
            scope="peer">
    <topicmeta>
      <navtitle>Ersatzteilliste (ZH)</navtitle>
    </topicmeta>
  </topicref>
</map>
```

> `scope="peer"` 表示引用同级语言目录中的主题，生成工具需特殊处理。

---

## 发布后验证清单

每个语言/格式的输出必须经过验证：

```bash
#!/bin/bash
# scripts/validate-publish-output.sh

LANG="$1"
FMT="$2"
OUT_DIR="output/$LANG/$FMT"

# HTML 验证
if [ "$FMT" = "html5" ]; then
  # 检查所有链接有效性
  linkchecker --no-status "$OUT_DIR/index.html"

  # 验证 HTML5 合规
  html5validator --root "$OUT_DIR"
fi

# PDF 验证
if [ "$FMT" = "pdf2" ]; then
  # 检查 PDF 可打开且有内容
  pdfinfo "$OUT_DIR"/*.pdf | grep "Pages"

  # 验证字体嵌入
  pdffonts "$OUT_DIR"/*.pdf | grep "Noto Sans"
fi

# 通用检查：文件大小合理性
for f in "$OUT_DIR"/*; do
  SIZE=$(stat -c%s "$f")
  if [ "$SIZE" -lt 10240 ]; then  # < 10KB 可能有问题
    echo "WARNING: $f 文件大小异常 ($SIZE bytes)"
  fi
done
```

---

## 多语言输出目录结构

```
output/
├── 20250607-143000/              ← 构建时间戳
│   ├── zh-CN/
│   │   ├── html5/
│   │   │   ├── index.html
│   │   │   └── topics/
│   │   └── pdf2/
│   │       └── MC-5000-手册-zh-CN.pdf
│   ├── en-US/
│   │   ├── html5/
│   │   │   └── index.html
│   │   └── pdf2/
│   │       └── MC-5000-Manual-en-US.pdf
│   ├── ja-JP/
│   │   ├── html5/
│   │   └── pdf2/
│   │       └── MC-5000-マニュアル-ja-JP.pdf
│   └── ...
└── latest/ → 20250607-143000/    ← 符号链接指向最新构建
```

---

## 发布索引页生成

```bash
#!/bin/bash
# scripts/generate-index.sh — 生成多语言索引导航页

OUTPUT="$1"
INDEX="$OUTPUT/index.html"

cat > "$INDEX" << 'HTML'
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>MC-5000 技术文档 — 多语言索引导航</title>
  <style>
    body { font-family: "Noto Sans SC", sans-serif; max-width: 900px; margin: 0 auto; }
    table { border-collapse: collapse; width: 100%; }
    th, td { border: 1px solid #ccc; padding: 12px; text-align: center; }
    .ok { color: green; } .na { color: gray; }
  </style>
</head>
```

---

## 发布索引页生成（续）

```bash
cat >> "$INDEX" << 'HTML'
<body>
  <h1>MC-5000 CNC 技术文档 — 多语言版本</h1>
  <table>
    <tr><th>语言</th><th>HTML</th><th>PDF</th><th>构建时间</th></tr>
HTML

for lang_dir in "$OUTPUT"/*/; do
  lang=$(basename "$lang_dir")
  [ "$lang" = "index.html" ] && continue

  HTML_OK=$( [ -f "$lang_dir/html5/index.html" ] && echo '<span class="ok">✓</span>' || echo '<span class="na">—</span>' )
  PDF_OK=$( ls "$lang_dir/pdf2/"*.pdf 2>/dev/null && echo '<span class="ok">✓</span>' || echo '<span class="na">—</span>' )
  TIME=$(stat -c %y "$lang_dir" 2>/dev/null | cut -d. -f1)

  echo "<tr><td>$lang</td><td>$HTML_OK</td><td>$PDF_OK</td><td>$TIME</td></tr>" >> "$INDEX"
done

echo "</table></body></html>" >> "$INDEX"

echo "索引导航页已生成: $INDEX"
```

---

## 排错指南：CJK 字体常见问题

| 现象 | 原因 | 解决方法 |
|------|------|----------|
| PDF 中中文字符显示为 `#` | FOP 未注册中文字体 | 在 `fop.xconf` 中添加 `<font>` 注册 |
| 日文 PDF 汉字显示为中文变体 | 未区分 CJK 字体 | 使用 `@xml:lang` + `font-group` 区分 |
| 韩文 PDF 显示方块 (tofu) | 字体缺少韩文 glyph | 使用 Noto Sans KR 替代 Noto Sans CJK SC |
| PDF 文件过大 (>50 MB) | 字体完全嵌入 | 使用 `embedding-mode="subset"` |
| 中文标点挤压失效 | 排版引擎不支持 CJK | 使用支持 CJK 的 FOP/Chemistry 版本 |
| HTML 中文乱码 | 缺少 `<meta charset="UTF-8">` | DITA-OT args 中指定 `args.charset=UTF-8` |

---

<!-- _class: exercise -->

## 练习：为一套文档配置日文和英文发布

**任务**：

1. 创建 `language/en-US/` 和 `language/ja-JP/` 目录，各放 2 个已翻译的主题（可从练习目录复制）
2. 为 `ja-JP` 创建 `fop.xconf` 字体注册文件，注册 Noto Sans JP
3. 创建 `css/lang-ja-JP.css` 日文样式表，设置合适的行间距和字体
4. 编写批处理脚本 `publish-multi.sh`，并行构建 en-US 和 ja-JP 的 HTML5 和 PDF 输出
5. 运行脚本并检查输出目录结构
6. 实现语言回退：在日文 map 中引用一个日文不存在的主题，回退到英文版

**验证**：输出目录应包含 2 语言 × 2 格式 = 4 个输出目录，日文 PDF 无豆腐块

---

## 关键要点

- **三种构建策略**：串行（调试）→ 并行（生产）→ 增量（频繁迭代）
- CJK 字体配置三个层面：**注册** (`fop.xconf`) → **映射** (`font-mappings.xml`) → **选择** (`custom.xsl` + `@xml:lang`)
- 推荐使用 **Google Noto 系列**，统一风格、覆盖全面、免费商用
- 西文与 CJK 排版**差异显著**：对齐方式、段落标记、断字规则均需独立 CSS
- `@xml:lang` 在主题/Map/内嵌三个层级正确标记
- **语言回退链**：目标语言 → en-US → zh-CN，确保无主题缺失
- CI/CD 使用 `matrix` 策略并行构建，构建产物上传为 Artifact

---

## 下一步

模块 8：**专业级实践项目**

综合应用所有模块知识，为示例制造 MC-5000 创建完整的中英文文档集
