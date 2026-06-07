---
marp: true
theme: dita-training
class: invert
paginate: true
---

# DITA CI/CD 流水线

## 制造业文档持续集成与交付

**DITA 专家级培训 · 模块 2**

---

## 学习目标

完成本模块后，您将能够：

- 设计 DITA 文档的完整 CI/CD 流水线
- 配置 GitHub Actions 实现自动构建和发布
- 实现多层次文档验证流程
- 自定义 DITA-OT 插件适配制造业需求
- 排查 CI/CD 常见故障
- 集成 Slack/Teams 构建监控通知

---

## CI/CD 架构概览

```
触发器 → 作业 → 步骤 → 工件 → 部署
  ↓        ↓      ↓      ↓       ↓
PR/推送  验证/发布  执行任务  HTML/PDF  S3/CDN
```

| 组件 | 作用 | 制造业场景 |
|------|------|-----------|
| **触发器** | 何时运行 | PR 提交 → 验证；主分支合并 → 发布 |
| **作业** | 任务分组 | validate / publish / deploy |
| **步骤** | 原子操作 | DITA-OT build → 上传 S3 |
| **工件** | 构建产物 | PDF 手册、HTML5 帮助 |
| **缓存** | 加速构建 | DITA-OT 缓存、字体缓存 |

---

## 发布工作流概览 (publish.yml)

```
源代码推送 (main)
    │
    ▼
┌─────────────────┐
│ 检出代码 + LFS  │
└────────┬────────┘
         ▼
┌─────────────────┐
│ 安装 DITA-OT    │  ← 缓存层
└────────┬────────┘
         ▼
┌─────────────────────────────────┐
│       并行构建                    │
│  HTML5  │  PDF  │   Eclipse Help │
└────────┬────────────────────────┘
         ▼
┌─────────────────┐
│ 上传到 S3/Azure │
└────────┬────────┘
         ▼
┌─────────────────┐
│ Elasticsearch   │  ← 搜索索引
│ 索引更新        │
└─────────────────┘
```

---

## publish.yml：触发条件

```yaml
name: Publish Documentation

on:
  push:
    branches: [main, release/*]
    paths:
      - 'content/**/*.dita'
      - 'content/**/*.xml'
      - 'framework/**'
      - 'images/**'
  workflow_dispatch:
    inputs:
      product:
        description: 'Product to publish'
        required: true
        type: choice
        options:
          - all
          - cnc-5000
          - robot-arm-rx
          - conveyor-cx
      output_format:
        description: 'Output format'
        type: choice
        options:
          - all
          - html5
          - pdf
```

---

## publish.yml：环境和工作

```yaml
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        product: [cnc-5000, robot-arm-rx, conveyor-cx]
        format: [html5, pdf]
    steps:
      - uses: actions/checkout@v4
        with:
          lfs: true

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Cache DITA-OT
        uses: actions/cache@v4
        with:
          path: /opt/dita-ot
          key: dita-ot-4.1.2
```

---

## publish.yml：DITA-OT 安装与构建

```yaml
      - name: Download and Setup DITA-OT
        run: |
          wget -q https://github.com/.../dita-ot-4.1.2.zip
          unzip -q dita-ot-4.1.2.zip -d /opt/
          /opt/dita-ot-4.1.2/bin/dita --install

      - name: Build ${{ matrix.product }} - ${{ matrix.format }}
        run: |
          /opt/dita-ot-4.1.2/bin/dita \
            --input=content/${{ matrix.product }}/${{ matrix.product }}.ditamap \
            --format=${{ matrix.format }} \
            --output=output/${{ matrix.product }}/${{ matrix.format }} \
            --filter=profiles/${{ matrix.product }}.ditaval \
            --propertyfile=build.properties
```

---

## publish.yml：S3 上传与 ES 索引

```yaml
      - name: Upload to S3
        run: |
          aws s3 sync output/${{ matrix.product }}/${{ matrix.format }}/ \
            s3://docs.acme.com/${{ matrix.product }}/${{ matrix.format }}/latest/ \
            --delete --cache-control "max-age=3600"

      - name: Index to Elasticsearch
        if: matrix.format == 'html5'
        run: |
          node scripts/es-indexer.js \
            --source output/${{ matrix.product }}/html5/ \
            --index docs-${{ matrix.product }} \
            --elasticsearch-url ${{ secrets.ES_URL }}

      - name: Notify Slack
        if: always()
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "文档发布完成: ${{ matrix.product }} (${{ matrix.format }})
                       状态: ${{ job.status }}
                       链接: https://docs.acme.com/${{ matrix.product }}/latest/"
            }
```

---

## 验证工作流 (validate.yml)

```yaml
name: Validate Documentation

on:
  pull_request:
    paths:
      - 'content/**'
      - 'framework/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: XML Validation
        run: find content/ -name "*.dita" -exec xmllint --noout --dtdvalid {} \;
      - name: Metadata Check (M1-M7)
        run: node scripts/validate-metadata.js
      - name: Spell Check
        run: node scripts/spell-check.js
      - name: Link Check
        run: node scripts/check-links.js
      - name: Subject Scheme Validation
        run: node scripts/validate-subject-scheme.js
```

---

## 自定义验证脚本：M1-M7 元数据强制

```javascript
// scripts/validate-metadata.js
const REQUIRED_METADATA = {
  task: ['M1-product', 'M2-version', 'M3-audience', 
         'M4-safety-level', 'M5-owner', 'M6-review-date', 'M7-status'],
  concept: ['M1-product', 'M2-version', 'M3-audience', 'M5-owner'],
  reference: ['M1-product', 'M2-version', 'M5-owner', 'M6-review-date']
};

function validateMetadata(xmlContent, topicType) {
  const required = REQUIRED_METADATA[topicType] || REQUIRED_METADATA.task;
  const missing = [];
  for (const field of required) {
    if (!xmlContent.includes(`name="${field}"`)) {
      missing.push(field);
    }
  }
  return missing;
}
```

---

## 自定义验证脚本：@class 属性验证

```javascript
// scripts/validate-class-attr.js
function validateClassAttribute(element) {
  const classAttr = element.getAttribute('class');
  if (!classAttr) {
    // 只有基础 DITA 元素允许缺少 class
    const baseElements = ['topic', 'concept', 'task', 'reference',
                          'map', 'title', 'shortdesc', 'body'];
    if (!baseElements.includes(element.tagName)) {
      return { valid: false, error: 'Missing @class on specialized element' };
    }
    return { valid: true };
  }

  // 验证第一个 token 是 "-" 还是 "+"
  const tokens = classAttr.trim().split(/\s+/);
  if (!['-', '+'].includes(tokens[0])) {
    return { valid: false, error: `@class must start with '-' or '+'` };
  }

  // 验证层级链
  for (let i = 1; i < tokens.length; i++) {
    if (!tokens[i].includes('/')) {
      return { valid: false, error: `Invalid class token: ${tokens[i]}` };
    }
  }
  return { valid: true };
}
```

---

## 自定义验证脚本：Conref 解析检查

```javascript
// scripts/check-conrefs.js
function validateConref(xmlContent, filePath) {
  const conrefPattern = /conref="([^"]+)"/g;
  let match;
  const errors = [];

  while ((match = conrefPattern.exec(xmlContent)) !== null) {
    const conref = match[1];
    const [targetFile, targetId] = conref.split('#');

    if (!fs.existsSync(path.resolve(path.dirname(filePath), targetFile))) {
      errors.push(`Conref target file not found: ${targetFile}`);
      continue;
    }

    if (targetId) {
      const targetContent = fs.readFileSync(
        path.resolve(path.dirname(filePath), targetFile), 'utf8'
      );
      if (!targetContent.includes(`id="${targetId}"`)) {
        errors.push(`Conref target ID not found: ${conref}`);
      }
    }
  }
  return errors;
}
```

---

## 自定义验证脚本：Xref 链接完整性

```javascript
// scripts/check-xrefs.js
function validateXrefsInMap(ditamapPath) {
  const mapContent = fs.readFileSync(ditamapPath, 'utf8');
  const hrefPattern = /href="([^"]+)"/g;
  const errors = [];

  let match;
  while ((match = hrefPattern.exec(mapContent)) !== null) {
    const href = match[1];
    // 跳过外部 URL
    if (href.startsWith('http://') || href.startsWith('https://')) continue;
    // 跳过 scope="external"
    const context = mapContent.substring(
      Math.max(0, match.index - 50), match.index
    );
    if (context.includes('scope="external"')) continue;

    const resolvedPath = path.resolve(path.dirname(ditamapPath), href);
    if (!fs.existsSync(resolvedPath)) {
      errors.push(`Broken link in ${ditamapPath}: ${href}`);
    }
  }
  return errors;
}
```

---

## DITA-OT 自定义：PDF 插件 (XSL-FO)

```xml
<!-- plugin.xml -->
<plugin id="com.acme.pdf.cnc-manual">
  <require plugin="org.dita.pdf2"/>
  <feature extension="dita.xsl.xslfo"
           file="xsl/custom.xsl"/>
  <feature extension="dita.conductor.transtype.check"
           value="cnc-pdf"/>
</plugin>
```

```xml
<!-- xsl/custom.xsl -->
<xsl:template match="*[contains(@class,' mfg-d/safetyInterlock ')]">
  <fo:block font-weight="bold" color="red"
            border="2pt solid red" padding="8pt"
            background-color="#fff0f0">
    <xsl:text>安全联锁: </xsl:text>
    <xsl:apply-templates/>
  </fo:block>
</xsl:template>
```

---

## DITA-OT 自定义：PDF 插件 (CSS 分页媒体)

```css
/* css/print.css —— 现代替代方案 */
@page {
  size: A4;
  margin: 2cm;
  @top-right {
    content: "ACME CNC 5000";
    font-size: 8pt;
    color: #666;
  }
  @bottom-center {
    content: counter(page);
  }
}

@page safety-page {
  background-color: #fff0f0;
  @top-right {
    content: "SAFETY NOTICE";
    color: red;
    font-weight: bold;
  }
}

*[class~="mfg-d/safetyInterlock"] {
  page: safety-page;
  border: 3pt solid red;
  padding: 12pt;
  font-weight: bold;
  color: #cc0000;
}
```

---

## DITA-OT 自定义：HTML5 变换扩展

```xml
<!-- xsl/dita2html5-custom.xsl -->
<xsl:template match="*[contains(@class,' mfg-d/spindleCheck ')]">
  <div class="spindle-check">
    <xsl:attribute name="data-rpm" select="@rpm"/>
    <xsl:attribute name="data-temp-max" select="@temperature-max"/>
    <xsl:attribute name="data-vibration-limit" select="@vibration-limit"/>

    <div class="spindle-check-header">
      <span class="icon">&#x2699;</span>
      <span class="label">主轴检查</span>
    </div>
    <div class="spindle-check-readings">
      <span class="rpm">RPM: <xsl:value-of select="@rpm"/></span>
      <span class="temp">温度上限: <xsl:value-of select="@temperature-max"/>°C</span>
    </div>
    <div class="spindle-check-notes">
      <xsl:apply-templates/>
    </div>
  </div>
</xsl:template>
```

---

## Ant 构建文件自定义

```xml
<!-- build-cnc-manual.xml -->
<project name="CNC Manual Build" default="build-all">
  <property name="dita.dir" value="/opt/dita-ot-4.1.2"/>
  <property name="input.dir" value="${basedir}/content/cnc-5000"/>
  <property name="output.dir" value="${basedir}/output/cnc-5000"/>

  <target name="build-all" depends="build-html,build-pdf,build-eclipse"/>

  <target name="build-html">
    <ant antfile="${dita.dir}/build.xml">
      <property name="args.input" 
                value="${input.dir}/cnc-5000-maintenance.ditamap"/>
      <property name="transtype" value="html5"/>
      <property name="output.dir" value="${output.dir}/html5"/>
      <property name="args.filter" 
                value="${input.dir}/profiles/operator.ditaval"/>
    </ant>
  </target>

  <target name="build-pdf">
    <ant antfile="${dita.dir}/build.xml">
      <property name="args.input"
                value="${input.dir}/cnc-5000-maintenance.ditamap"/>
      <property name="transtype" value="cnc-pdf"/>
      <property name="output.dir" value="${output.dir}/pdf"/>
    </ant>
  </target>
</project>
```

---

## 部署目标配置

```yaml
# deployment-config.yml
environments:
  dev:
    s3_bucket: docs-dev.acme.com
    cloudfront_id: E1ABC123
    elasticsearch: https://es-dev.acme.com:9200

  staging:
    s3_bucket: docs-staging.acme.com
    cloudfront_id: E2DEF456
    elasticsearch: https://es-staging.acme.com:9200
    approval_required: true

  production:
    s3_bucket: docs.acme.com
    cloudfront_id: E3GHI789
    elasticsearch: https://es-prod.acme.com:9200
    approval_required: true
    protect_from_delete: true

products:
  cnc-5000:
    ditamap: content/cnc-5000/cnc-5000-maintenance.ditamap
    portal_url: https://docs.acme.com/cnc-5000/latest/
  robot-arm-rx:
    ditamap: content/robot-arm-rx/robot-arm-rx-maintenance.ditamap
    portal_url: https://docs.acme.com/robot-arm-rx/latest/
```

---

## 构建状态通知：Slack/Teams 集成

```yaml
      - name: Build Status to Slack
        if: always()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          channel-id: 'docs-build'
          payload: |
            {
              "attachments": [{
                "color": "${{ job.status == 'success' && 'good' || 'danger' }}",
                "fields": [
                  {"title": "产品", "value": "${{ matrix.product }}", "short": true},
                  {"title": "格式", "value": "${{ matrix.format }}", "short": true},
                  {"title": "构建ID", "value": "${{ github.run_id }}", "short": true},
                  {"title": "状态", "value": "${{ job.status }}", "short": true},
                  {"title": "URL", "value": "https://docs.acme.com/${{ matrix.product }}/latest/"}
                ],
                "footer": "DITA CI/CD Pipeline"
              }]
            }
```

---

## 常见 CI/CD 故障排查

<!-- _class: exercise -->

| 问题 | 症状 | 根因 | 解决方案 |
|------|------|------|---------|
| **DTD 解析错误** | `Element 'xxx' is not declared` | catalog.xml 路径错误 | 检查 PUBLIC ID 映射 |
| **缺少图片** | 构建成功但图片不显示 | LFS 未拉取 | `git lfs pull` 或 `with: lfs: true` |
| **内存溢出** | `java.lang.OutOfMemoryError` | 大文档集内存不足 | `-Xmx4g` JVM 参数 |
| **PDF 超时** | 构建超 30 分钟 | XSL-FO 处理慢 | 拆分 ditamap 并行构建 |
| **Conref 断裂** | 构建警告但无错误 | 目标文件移动但未更新引用 | 运行 `check-conrefs.js` |
| **ES 索引失败** | HTML5 部署但搜索不可用 | ES 连接超时 | 添加重试逻辑 |

---

## DITA-OT 故障排查：JVM 内存配置

```bash
# bin/dita 脚本中设置 JVM 参数
JAVA_OPTS="-Xmx4096m -XX:+UseG1GC -XX:MaxGCPauseMillis=200"

# 或在 Ant 调用中设置
ant -f build.xml \
  -DmaxJavaMemory=4096m \
  -Dtranstype=pdf \
  -Dargs.input=content/cnc-5000/cnc-5000-maintenance.ditamap
```

```yaml
# GitHub Actions 中
- name: Build with custom JVM settings
  run: |
    export ANT_OPTS="-Xmx4096m -XX:+UseG1GC"
    /opt/dita-ot-4.1.2/bin/dita \
      --input=content/${{ matrix.product }}/${{ matrix.product }}.ditamap \
      --format=pdf \
      -DmaxJavaMemory=4096m
```

---

## 缓存策略优化

```yaml
      - name: Cache DITA-OT
        uses: actions/cache@v4
        with:
          path: |
            /opt/dita-ot-4.1.2
            ~/.dita-ot
          key: dita-ot-${{ runner.os }}-4.1.2

      - name: Cache Dependencies
        uses: actions/cache@v4
        with:
          path: |
            node_modules
            ~/.fonts
          key: deps-${{ runner.os }}-${{ hashFiles('package-lock.json') }}

      - name: Cache Build Output (Incremental)
        uses: actions/cache@v4
        with:
          path: output/.cache
          key: build-cache-${{ matrix.product }}-${{ github.sha }}
          restore-keys: |
            build-cache-${{ matrix.product }}-
```

---

## 关键要点

<!-- _class: key-takeaways -->

- CI/CD 流水线分为**验证**（PR）和**发布**（主分支）两个阶段
- 验证层包括：XML 验证、元数据检查、拼写检查、链接检查、Subject Scheme 验证
- 元数据强制脚本确保所有主题遵循 M1-M7 规范
- Conref 和 xref 链接完整性检查是防止断裂引用的关键
- DITA-OT 自定义通过插件架构实现，核心是 plugin.xml + XSL/CSS 覆盖
- 大文档集需要 JVM 内存调优和缓存策略
- Slack/Teams 集成提供构建状态实时可见性
- 多产品并行发布使用 matrix strategy 实现
