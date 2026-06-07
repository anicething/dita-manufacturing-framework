---
marp: true
theme: dita-training
class: invert
paginate: true
---

# Subject Scheme — 受控词汇治理

## DITA Professional — 模块 3

**DITA 的受控词汇定义与验证机制**

---

## 学习目标

完成本模块后，您将能够：

- 理解 Subject Scheme 在 DITA 生态中的角色
- 创建 subjectdef 定义受控值
- 使用 enumerationdef 绑定属性到值域
- 在 oXygen XML Editor 中配置 Subject Scheme
- 实现 Git pre-commit hook 自动验证
- 扩展 Subject Scheme 添加新属性或新值

---

## 什么是 Subject Scheme

Subject Scheme 是 DITA 提供的**受控词汇定义机制**，解决以下问题：

| 问题 | 无 Subject Scheme | 有 Subject Scheme |
|------|-------------------|-------------------|
| 拼写变体 | "CoolPro5000" / "coolpro5000" / "CoolPro 5000" | 仅允许 "coolpro5000" |
| 未定义值 | `@audience="anyone"` | oXygen 下拉选择仅合法值 |
| 废弃值 | `@product="coolpro3000"` 仍在使用 | 移除后验证报错 |
| 一致性 | 不同作者用不同术语 | 统一受控词汇表 |

---

## Subject Scheme 结构

```
subjectScheme
  ├── subjectdef (值定义)
  │   ├── @keys — 键标识符
  │   ├── subjectdef (子值/层级)
  │   └── ...
  └── enumerationdef (属性绑定)
      ├── attributedef — 绑定到哪个属性
      └── subjectdef (引用已定义的值)
```

两个核心组件：
- **subjectdef**：定义合法的值（可嵌套形成层级）
- **enumerationdef**：将值集合绑定到具体的 DITA 属性

---

## 框架的 6 个受控属性集

| 属性 | 值数量 | 示例值 |
|------|--------|--------|
| `audience` | 6 | operator, technician, engineer, manager, trainer, inspector |
| `content-type` | 8 | concept, task, reference, troubleshooting, glossary, hazard, requirements, learning |
| `document-status` | 5 | draft, review, approved, published, deprecated |
| `product` | 4 | coolpro5000, spindlext300, hydramax200, controlplus |
| `hazard-severity` | 4 | danger, warning, caution, notice |
| `skill-level` | 5 | level-0, level-1, level-2, level-3, level-4 |

---

## Subject Scheme 完整示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<subjectScheme>
  <!-- === audience 属性值域 === -->
  <subjectdef keys="audience-values" navtitle="受众类型">
    <subjectdef keys="aud-operator" navtitle="操作员">
      <subjectdef keys="aud-op-novice" navtitle="新手操作员"/>
      <subjectdef keys="aud-op-senior" navtitle="高级操作员"/>
    </subjectdef>
    <subjectdef keys="aud-technician" navtitle="维修技术员"/>
    <subjectdef keys="aud-engineer" navtitle="工程师"/>
    <subjectdef keys="aud-manager" navtitle="管理人员"/>
    <subjectdef keys="aud-trainer" navtitle="培训师"/>
    <subjectdef keys="aud-inspector" navtitle="质检员"/>
  </subjectdef>
```

---

## Subject Scheme 完整示例（续）

```xml
  <!-- === 绑定 audience 属性 === -->
  <enumerationdef>
    <attributedef name="audience"/>
    <subjectdef keyref="aud-operator"/>
    <subjectdef keyref="aud-technician"/>
    <subjectdef keyref="aud-engineer"/>
    <subjectdef keyref="aud-manager"/>
    <subjectdef keyref="aud-trainer"/>
    <subjectdef keyref="aud-inspector"/>
  </enumerationdef>

  <!-- === product 属性值域 === -->
  <subjectdef keys="product-values" navtitle="产品线">
    <subjectdef keys="prod-coolpro5000" navtitle="CoolPro 5000"/>
    <subjectdef keys="prod-spindlext300" navtitle="SpindleXT 300"/>
    <subjectdef keys="prod-hydramax200" navtitle="HydraMax 200"/>
    <subjectdef keys="prod-controlplus" navtitle="ControlPlus"/>
  </subjectdef>
```

---

## Subject Scheme 完整示例（续二）

```xml
  <!-- === 绑定 product 属性 === -->
  <enumerationdef>
    <attributedef name="product"/>
    <subjectdef keyref="prod-coolpro5000"/>
    <subjectdef keyref="prod-spindlext300"/>
    <subjectdef keyref="prod-hydramax200"/>
    <subjectdef keyref="prod-controlplus"/>
  </enumerationdef>

  <!-- === 绑定 document-status 属性 === -->
  <enumerationdef>
    <attributedef name="document-status"/>
    <subjectdef keyref="status-draft"/>
    <subjectdef keyref="status-review"/>
    <subjectdef keyref="status-approved"/>
    <subjectdef keyref="status-published"/>
    <subjectdef keyref="status-deprecated"/>
  </enumerationdef>
</subjectScheme>
```

---

## oXygen 集成配置

**步骤 1**：在 oXygen 项目文件中配置：

```xml
<!-- project.xpr -->
<project>
  <meta>
    <subjectScheme>
      <scheme href="schemes/subject-scheme.subjectscheme"/>
    </subjectScheme>
  </meta>
</project>
```

**步骤 2**：重启 oXygen，效果如下：

- `@audience` 编辑时显示下拉菜单，仅列出合法值
- 输入非法值时红色下划线提示
- 自动补全功能仅显示 Subject Scheme 定义的值

---

## Git Pre-commit Hook 验证

```bash
#!/bin/bash
# .git/hooks/pre-commit

SCHEME_FILE="schemes/subject-scheme.subjectscheme"
STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.dita$')

for file in $STAGED_FILES; do
  # 提取所有 @product 值
  PROD_VALS=$(grep -oP 'product="([^"]*)"' "$file" | cut -d'"' -f2 | sort -u)

  # 提取受控值
  SCHEME_VALS=$(grep -oP 'keys="prod-([^"]*)"' "$SCHEME_FILE" \
    | sed 's/keys="prod-//' | sed 's/"//' | sort -u)

  # 验证
  for val in $PROD_VALS; do
    if ! echo "$SCHEME_VALS" | grep -qxF "$val"; then
      echo "ERROR: $file 中 product='$val' 不在 Subject Scheme 合法值中"
      exit 1
    fi
  done
done

echo "Subject Scheme 验证通过"
```

---

## 扩展 Subject Scheme

**场景 1：添加新的 product 值**

```xml
<!-- 在 schemes/subject-scheme.subjectscheme 中添加 -->
<subjectdef keys="prod-lasercut800" navtitle="LaserCut 800"/>

<enumerationdef>
  <attributedef name="product"/>
  <!-- 已有值... -->
  <subjectdef keyref="prod-lasercut800"/>  <!-- 新值 -->
</enumerationdef>
```

**场景 2：添加全新的属性**

```xml
<subjectdef keys="machine-type-values">
  <subjectdef keys="mt-milling" navtitle="铣床"/>
  <subjectdef keys="mt-turning" navtitle="车床"/>
  <subjectdef keys="mt-grinding" navtitle="磨床"/>
</subjectdef>
<enumerationdef>
  <attributedef name="machine-type"/>
  <subjectdef keyref="mt-milling"/>
  <subjectdef keyref="mt-turning"/>
  <subjectdef keyref="mt-grinding"/>
</enumerationdef>
```

---

## Subject Scheme 更新工作流

```
需求变更（新增产品/受众/属性）
        │
        ▼
更新 .subjectscheme 文件
        │
        ▼
提交 PR，在描述中列出变更
        │
        ▼
架构师 Review（确保值命名规范）
        │
        ▼
合并到 main 分支
        │
        ▼
团队拉取最新 schemes/
        │
        ▼
oXygen 自动获取新值列表
```

---

<!-- _class: exercise -->

## 练习：在 oXygen 中配置 Subject Scheme

**任务**：

1. 打开 `schemes/subject-scheme.subjectscheme` 文件
2. 为 `skill-level` 属性添加一个新值 `level-5`（专家级）
3. 在 `enumerationdef` 中绑定该新值
4. 将 Subject Scheme 关联到 oXygen 项目（`project.xpr`）
5. 创建一个测试概念主题，在 `@audience` 属性上验证下拉选择功能
6. 尝试输入一个非法值，观察 oXygen 的验证提示

**验证**：oXygen 属性编辑器中 `skill-level` 应显示 6 个选项（0-5）

---

## 关键要点

- Subject Scheme 是 DITA 的**唯一受控词汇机制**，避免拼写变体和非法值
- 框架定义**6 个受控属性集**：audience, content-type, document-status, product, hazard-severity, skill-level
- **subjectdef** 定义值（可嵌套），**enumerationdef** 绑定属性
- oXygen 集成后提供**下拉选择**和**实时验证**
- Git pre-commit hook 提供**提交时自动验证**，拦截非法值
- 扩展时遵循**先添加定义、再绑定、最后 Review** 的流程

---

## 下一步

模块 4：**Bookmap 与 PDF 输出**

学习使用 Bookmap 组织出版物并通过 DITA-OT 生成专业 PDF 文档
