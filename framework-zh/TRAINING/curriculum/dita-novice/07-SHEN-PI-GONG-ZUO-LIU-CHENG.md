---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# 审批工作流程
## 制造业文档的结构化评审与发布

**DITA 课程 | 制造业文档**

---

<!-- _class: divider -->

# 第一章：为什么需要审批

---

# 学习目标

完成本模块后，你将能够：

- 理解制造文档需要结构化审批的根本原因
- 掌握 10 步审批工作流的每个环节
- 熟悉编写、评审、修订、审批、发布五个阶段
- 独立操作基于 Git 的分支管理和 PR 评审流程
- 区分四个评审角色的职责和权限
- 使用标签和里程碑管理文档状态
- 追踪和处理评审评论
- 在模拟环境中完成完整的 PR 评审流程

---

# 为什么制造文档需要审批？

<!-- _class: keypoint -->

在制造业中，文档错误可能导致严重后果：

- **安全问题**：错误的操作步骤可能导致人员伤亡
- **设备损坏**：错误的维护程序可能损坏昂贵的 CNC 设备
- **产品质量**：不准确的规格参数会产生不合格零件
- **合规风险**：不符合 ISO 标准的文档会导致审计失败
- **法律风险**：安全文档的疏漏可能带来法律责任

> 一杯咖啡洒在键盘上不会导致飞机坠毁。但一份未经审核的 CNC 操作程序却可能导致机床撞刀、工件报废、甚至人员受伤。

---

# 审批=质量门控

| 行业 | 质量门控 | 文档审批类比 |
|------|---------|-------------|
| 制药 | 批次检验放行 | 文档技术审核 |
| 航空 | 适航认证 | 文档合规审核 |
| 汽车 | 质量检查点 (Quality Gate) | 文档评审里程碑 |
| **CNC 加工** | 首件检验 + 过程检验 | **同行评审 + 专家评审** |

> 正如 CNC 加工有首件检验和过程检验，文档也必须经过层层审核才能"放行"发布。

---

# 不审批的真实案例

**案例**：某机床厂操作手册中将主轴最高转速 8000 rpm 误写为 80000 rpm。

| 后果 | 严重程度 |
|------|---------|
| 操作工尝试输入 80000 rpm | 控制系统报错 |
| 如果控制系统未拦截 | 主轴超速运转 |
| 离心力导致刀具飞出 | **致命安全风险** |

**如果有审批流程**：第二个人在评审时会发现这个明显的数值错误。

---

<!-- _class: divider -->

# 第二章：10 步审批工作流概览

---

# 审批流程全景图

```
┌──────────────────────────────────────────────────────────────┐
│                    10 步审批工作流                            │
├────┬──────┬──────┬──────┬──────┬─────┬──────┬──────┬────────┤
│ 1  │  2   │  3   │  4   │  5   │  6  │  7   │  8   │ 9 → 10 │
│需求│ 编写 │自检  │同行  │专家  │修订 │质量  │批准  │发布+归档│
│分析│ 草稿 │      │评审  │评审  │     │审核  │      │        │
└────┴──────┴──────┴──────┴──────┴─────┴──────┴──────┴────────┘
```

---

# 10 步详解（步骤 1-5）

| 步骤 | 名称 | 负责人 | 产出物 |
|------|------|--------|--------|
| **1** | 需求分析 | 文档经理 + SME | 文档需求规格书 |
| **2** | 编写草稿 | 技术写作工程师 | `.dita` 主题文件（feature 分支） |
| **3** | 自检 | 作者本人 | 自检清单完成 |
| **4** | 同行评审 | 另一名技术写作工程师 | 评审意见（GitHub PR Review） |
| **5** | 专家评审 | 主题专家 (SME) | 技术准确性验证意见 |

---

# 10 步详解（步骤 6-10）

| 步骤 | 名称 | 负责人 | 产出物 |
|------|------|--------|--------|
| **6** | 修订 | 作者 | 根据反馈修改后的文件 |
| **7** | 质量审核 | 质量负责人 | 合规性和完整性确认 |
| **8** | 批准 | 文档经理/部门主管 | Approved 标签 |
| **9** | 发布 | CI/CD 管道 / 操作员 | 生成的 PDF/HTML5 交付物 |
| **10** | 归档 | 系统自动 | 发布版本标签 (Git Tag) |

---

# 五阶段模型

将 10 步归为五大阶段：

```
[ 编写阶段 ] → [ 评审阶段 ] → [ 修订阶段 ] → [ 审批阶段 ] → [ 发布阶段 ]
   步骤1-3         步骤4-5          步骤6         步骤7-8         步骤9-10
```

| 阶段 | 入口条件 | 出口标准 |
|------|---------|---------|
| 编写 | 需求明确 | 自检通过 |
| 评审 | 草稿完成 | 评审意见收集完毕 |
| 修订 | 收到评审反馈 | 所有意见已解决 |
| 审批 | 修订完成 | 获得正式批准 |
| 发布 | 已批准 | 产品交付且归档 |

---

# 审批流程的角色速查

| 角色 | 英文 | 职责 | MC-5000 团队 |
|------|------|------|-------------|
| 作者 | Author | 创建和修订主题内容 | 技术写作工程师 |
| 同行评审 | Peer Reviewer | 检查 DITA 规范、结构、可读性 | 另一名写作工程师 |
| 主题专家 | SME | 验证技术内容的准确性 | CNC 资深工程师 |
| 质量负责人 | Quality Lead | 确认合规性、完整性、一致性 | 质量部/文档经理 |

---

<!-- _class: divider -->

# 第三章：编写阶段 (步骤 1-3)

---

# 步骤 1：需求分析

在动笔之前，明确文档需求：

```
文档需求规格书（简要版）
──────────────────────────
产品：MC-5000 五轴联动加工中心
文档类型：用户操作手册
信息类型：Concept × 8 / Task × 12 / Reference × 4
目标读者：CNC 操作工（general 水平）
语言：zh-CN
交付格式：PDF + HTML5
交付日期：2026-03-15
审批要求：同行评审 + SME 技术审核 + 质量负责人最终批准
```

---

# 步骤 2：编写草稿

在专用的 feature 分支上创建主题文件：

```bash
# 创建功能分支
git checkout -b feature/dr5000-safety-chapter

# 复制模板
cp templates/zh-CN/concept/t1-concept-basic.dita \
   product/dr5000/zh-CN/concepts/c_dr5000_safety.dita

# 填充内容
# ... 在 oXygen 中编辑 c_dr5000_safety.dita ...

# 提交
git add product/dr5000/zh-CN/concepts/c_dr5000_safety.dita
git commit -m "feat: 添加 MC-5000 安全概念主题初稿"
git push origin feature/dr5000-safety-chapter
```

> 分支命名规范：`feature/{产品}-{模块名}` 或 `fix/{bug-id}-{简短描述}`

---

# 步骤 3：自检

在请求他人评审前，作者先自我检查：

| 检查项 | 通过标准 | 工具/方法 |
|--------|---------|-----------|
| XML 格式正规 | 所有标签正确闭合 | oXygen `Ctrl+Shift+V` |
| DTD 验证通过 | 无 DTD 错误 | oXygen 实时验证 |
| 必填元素完整 | title, shortdesc, id 已填写 | 目视检查 |
| 元数据 M1-M7 | 全部满足 | 对照元数据检查清单 |
| 拼写检查 | 无错别字 | oXygen 拼写检查 |
| 链接有效性 | href/conref 指向存在 | DITA Maps Manager 验证 |
| 命名规范 | 文件名和 id 符合规范 | 对照命名规范表 |
| 内容完整性 | 无 TODO 或 xxx 占位符 | 全文搜索 `xxx` / `TODO` |

---

# 自检操作命令

```bash
# 1. 搜索残留的占位符
grep -r "xxx\|TODO\|FIXME\|【填" product/dr5000/zh-CN/

# 2. 使用 XMLLint 批量验证
find product/dr5000/zh-CN/ -name "*.dita" \
  -exec xmllint --valid --noout {} \;

# 3. 使用 DITA-OT 验证
dita --input=ditamap/dr5000_manual.ditamap \
     --format=validate

# 4. 检查行尾空格和制表符
git diff --check
```

---

<!-- _class: divider -->

# 第四章：评审阶段 (步骤 4-5)

---

# 步骤 4：同行评审

创建 Pull Request 并请求同行评审：

```bash
# 1. 确保所有改动已提交并推送
git push origin feature/dr5000-safety-chapter

# 2. 在 GitHub/GitLab 上创建 PR
#    Base: main ← Compare: feature/dr5000-safety-chapter
#    标题: [MC-5000] 添加安全章节初稿
#    描述: 包含 3 个概念主题和 2 个任务主题，请 @reviewer 评审

# 3. 或使用 GitHub CLI
gh pr create \
  --title "[MC-5000] 添加安全章节初稿" \
  --body "包含 3 个概念主题和 2 个任务主题。请 @zhangsan 评审。" \
  --reviewer zhangsan \
  --base main
```

---

# 同行评审检查项

<!-- _class: tip -->

同行评审者关注以下方面：

| 维度 | 检查内容 |
|------|---------|
| **DITA 规范** | 信息类型正确、结构符合 DTD |
| **命名规范** | 文件名、id 符合项目规范 |
| **元数据完整** | M1-M7 全部满足 |
| **可读性** | 语言通顺、术语一致 |
| **结构合理** | 主题粒度和层级合理 |
| **模板正确** | 使用了正确的模板编号 |

> 同行评审**不负责**技术准确性——那是 SME 的职责。

---

# GitHub PR Review 操作

在 GitHub 上提交评审：

1. 打开 PR 页面 → **Files changed** 标签
2. 点击代码行号旁的 **+** 按钮添加评论
3. 选择评论类型：
   - **Comment**：一般性建议
   - **Approve**：通过评审
   - **Request changes**：需要修改后才能合并

```markdown
<!-- 评审评论示例 -->
在 c_dr5000_safety.dita 第 45 行：

建议将 `<note type="warning">` 改为 `<warning type="danger">`，
因为这里涉及高压电，属于致命危险级别。

另外，shortdesc 超过了两句话，建议精简。
```

---

# 步骤 5：专家评审 (SME Review)

SME 的评审重点：

| 维度 | 检查内容 | 示例 |
|------|---------|------|
| **技术准确性** | 参数、步骤、原理是否正确 | 主轴转速 8000 rpm 还是 12000 rpm？ |
| **安全合规** | 安全警告级别和内容是否正确 | 是否需要添加高压危险警告？ |
| **操作可行性** | 步骤顺序是否合理 | 是否应在更换刀具前先断开气源？ |
| **术语规范** | 行业术语是否准确 | "刀库" 还是 "刀具存储装置"？ |
| **缺失内容** | 是否遗漏了关键信息 | 是否缺少冷却液类型说明？ |

---

# SME 评审请求示例

通过 GitHub Issue 或直接沟通发起 SME 评审：

```markdown
## SME 评审请求

**主题**：MC-5000 主轴维护程序 (t_dr5000_spindle_maintenance.dita)
**作者**：王技术
**需要专家确认的内容**：

1. [ ] 拆卸步骤中，M12螺栓的预紧扭矩是否为 80 N·m？（第 12 步）
2. [ ] 千分表测量的径向跳动上限是否为 0.005mm？（第 18 步）
3. [ ] 是否需要在步骤 5 前增加"释放残余气压"的安全步骤？
4. [ ] 主轴润滑油规格：ISO VG 32 还是 ISO VG 46？

**评审截止日期**：2026-02-20
**SME**：@李专家 (主轴系统主管工程师)
```

---

<!-- _class: divider -->

# 第五章：修订阶段 (步骤 6)

---

# 步骤 6：根据反馈修订

处理评审意见的标准流程：

```
收到评审意见 → 分类处理 → 逐条修改 → 标记解决 → 请求重新评审
```

**意见分类**：

| 分类 | 处理方式 | 示例 |
|------|---------|------|
| **必须修改** | 立即修改并提交 | 错误的扭矩值 |
| **建议修改** | 评估后决定 | 用词优化建议 |
| **信息性** | 确认收到，不一定要改 | "FYI，下个版本可能会..." |
| **超出范围** | 记录到 Backlog | "建议添加第6章...（后续迭代）" |

---

# 修订时的 Git 操作

```bash
# 1. 在 feature 分支上直接修改
vim product/dr5000/zh-CN/concepts/c_dr5000_safety.dita

# 2. 提交修订
git add product/dr5000/zh-CN/concepts/c_dr5000_safety.dita
git commit -m "fix: 根据评审意见修改安全主题

- 将扭矩值从 80 N·m 修正为 120 N·m
- 新增'释放残余气压'安全步骤
- 修正警告级别: note/warning → warning/danger"

# 3. 推送更新
git push origin feature/dr5000-safety-chapter
```

> Git 自动将新的 commit 推送到同一个 PR，评审者可以看到增量变更。

---

# 评论追踪和解决

在 GitHub PR 中管理评审意见：

```bash
# 查看所有待解决的评审意见
gh pr view 42 --comments

# 在提交消息中引用 PR 评论
git commit -m "fix: 解决 PR #42 评论: 修正扭矩值为 120 N·m

Resolves: #42-discussion-r123456"
```

**工作建议**：
- 每条评审意见都需要**回复**（即使只是"已修改"或"不适用于..."）
- 修改完成后点击 **Resolve conversation**
- 所有意见解决后，请求评审者 **Re-review**

---

<!-- _class: divider -->

# 第六章：审批阶段 (步骤 7-8)

---

# 步骤 7：质量审核

质量负责人进行最终合规检查：

| 检查维度 | 具体内容 | 依据 |
|---------|---------|------|
| 元数据完整性 | M1-M7 全部通过 | 项目元数据规范 |
| 模板使用 | 模板编号正确，结构完整 | 模板使用指南 |
| 命名规范 | 文件名、id、navtitle 符合规范 | 项目命名规范 |
| 导航图更新 | Map 文件已同步更新 | 产品文档结构 |
| DITA-OT 构建 | 无错误、无警告 | CI 构建日志 |
| 输出质量 | PDF/HTML5 渲染正确 | 输出预览 |

---

# 质量审核操作清单

```bash
# 1. 拉取 PR 分支到本地进行构建验证
git fetch origin pull/42/head:pr-42
git checkout pr-42

# 2. 构建 PDF 验证
dita --input=ditamap/dr5000_manual.ditamap \
     --format=pdf \
     --output=./build/pr-42/

# 3. 构建 HTML5 验证
dita --input=ditamap/dr5000_manual.ditamap \
     --format=html5 \
     --output=./build/pr-42/

# 4. 检查构建日志
cat ./build/pr-42/*.log | grep -i "error\|warning"

# 5. 打开生成的 PDF 和 HTML5，确认渲染正确
```

---

# 步骤 8：正式批准

满足以下条件后，文档经理批准合并：

**批准条件（全部满足）**：
- [ ] 同行评审通过（Approved）
- [ ] SME 技术评审通过
- [ ] 所有评审意见已解决（Resolved）
- [ ] 质量审核通过
- [ ] DITA-OT 构建成功（CI 绿色）
- [ ] 无合并冲突（Branch is up to date）

```bash
# 质量负责人批准 PR
gh pr review 42 --approve \
  --body "质量审核通过。M1-M7 完整，DITA-OT 构建无误，批准合并。"

# 合并到 main
gh pr merge 42 --squash --delete-branch
```

---

# 使用标签和里程碑管理状态

在 GitHub/GitLab 中使用标签追踪文档状态：

| 标签 | 颜色 | 含义 |
|------|------|------|
| `status:draft` | 灰色 | 正在编写 |
| `status:review` | 黄色 | 评审中 |
| `status:revision` | 橙色 | 修订中 |
| `status:approved` | 绿色 | 已批准 |
| `status:published` | 蓝色 | 已发布 |
| `type:concept` | 紫色 | 概念主题 |
| `type:task` | 紫色 | 任务主题 |
| `type:reference` | 紫色 | 参考主题 |
| `priority:urgent` | 红色 | 安全相关/紧急 |

---

# 里程碑管理

```bash
# 创建里程碑
gh api repos/:owner/:repo/milestones \
  -f title="MC-5000 操作手册 v1.0" \
  -f due_on="2026-03-15T00:00:00Z" \
  -f description="包含安全、操作、基础维护章节"

# 将 PR 关联到里程碑
gh pr edit 42 --milestone "MC-5000 操作手册 v1.0"

# 查看里程碑进度
gh milestone view "MC-5000 操作手册 v1.0"
```

---

<!-- _class: divider -->

# 第七章：发布阶段 (步骤 9-10)

---

# 步骤 9：发布

代码合并到 main 后，触发 CI/CD 自动构建和发布：

```yaml
# .github/workflows/publish.yml (示意)
name: Publish Documentation
on:
  push:
    branches: [main]
    paths:
      - 'product/dr5000/**/*.dita'
      - 'product/dr5000/**/*.ditamap'

jobs:
  build-and-publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build PDF
        run: |
          dita --input=ditamap/dr5000_manual.ditamap \
               --format=pdf \
               --output=./dist/pdf/
      - name: Build HTML5
        run: |
          dita --input=ditamap/dr5000_manual.ditamap \
               --format=html5 \
               --output=./dist/html5/
      - name: Publish to Portal
        run: ./scripts/publish.sh ./dist/
```

---

# 步骤 10：归档

使用 Git Tag 标记发布版本：

```bash
# 创建发布标签
git tag -a "v1.0.0-dr5000-manual" -m "MC-5000 操作手册 v1.0.0 正式发布"
git push origin "v1.0.0-dr5000-manual"

# 创建 GitHub Release
gh release create "v1.0.0-dr5000-manual" \
  --title "MC-5000 操作手册 v1.0.0" \
  --notes "首个正式发布版本。包含安全、操作、维护章节。" \
  ./dist/pdf/dr5000_manual.pdf \
  ./dist/html5/dr5000_manual.zip
```

---

# 版本标签规范

| 标签格式 | 含义 | 示例 |
|---------|------|------|
| `v{major}.{minor}.{patch}-{product}-{doc}` | 产品文档版本 | `v1.0.0-dr5000-manual` |
| `v{major}.{minor}.{patch}-{product}-{doc}-draft` | 草案版本 | `v0.9.0-dr5000-manual-draft` |
| `v{major}.{minor}.{patch}-{product}-{doc}-hotfix{N}` | 紧急修复 | `v1.0.1-dr5000-manual-hotfix1` |

```bash
# 查看所有已发布版本
git tag -l "*-dr5000-*"

# 查看两个版本之间的差异
git diff v1.0.0-dr5000-manual v1.0.1-dr5000-manual -- product/dr5000/
```

---

<!-- _class: divider -->

# 第八章：分支策略

---

# Git 分支模型

```
main ─────●────────●────────●────→ (受保护分支，只接受 PR)
           \       /         /
feature/●──●─●────●─────────┘
             \       (Squash Merge)
feature/●─────●─●
               \
fix/●────────────●
                    (Cherry-pick to main)
```

**分支类型**：

| 分支 | 命名 | 生命周期 | 用途 |
|------|------|---------|------|
| main | `main` | 永久 | 已发布的稳定版本 |
| feature | `feature/dr5000-{模块}` | 开发期间 | 新文档或大改 |
| fix | `fix/{bug-id}-{描述}` | 修复期间 | 内容修正 |
| release | `release/v{版本号}` | 发布准备期 | 冻结后最终测试 |

---

# 分支操作速查

```bash
# 从 main 创建 feature 分支
git checkout main
git pull origin main
git checkout -b feature/dr5000-operation-chapter

# 保持 feature 分支与 main 同步
git fetch origin main
git rebase origin/main

# 解决冲突后继续
git add . && git rebase --continue

# 合并到 main（通过 PR，不在本地直接合并）
# Pull Request: base=main ← compare=feature/dr5000-operation-chapter
```

---

<!-- _class: divider -->

# 第九章：评审角色职责矩阵

---

# 角色与权限矩阵

| 操作 | 作者 | 同行评审 | SME | 质量负责人 |
|------|------|---------|-----|-----------|
| 创建主题 | 可以 | - | - | - |
| 修改内容 | 可以 | - | 建议 | - |
| PR Review 评论 | - | 可以 | 可以 | 可以 |
| Approve 审批 | - | 可以 | 可以 | 可以 |
| Request Changes | - | 可以 | 可以 | 可以 |
| Merge 合并 | - | - | - | 可以 |
| 创建 Release | - | - | - | 可以 |
| 设置标签/里程碑 | 可以 | - | - | 可以 |

---

# 评审者的责任边界

<!-- _class: keypoint -->

```
同行评审者负责：结构、规范、可读性
SME 负责：          技术准确性、完整性、安全性
质量负责人负责：     合规性、一致性、最终质量

三者互补，不要越界。
```

> 同行评审者不要试图验证技术参数的准确性——那是 SME 的工作。SME 也不要纠结 DITA 的标签嵌套规则——那是同行评审的工作。

---

# 评审礼仪和最佳实践

| 原则 | 说明 |
|------|------|
| **对事不对人** | "这段描述不够清晰"而非"你写得不清楚" |
| **给出建议** | 不只是指出问题，还要给出修改建议 |
| **解释原因** | "建议改为...，因为..."而不仅仅是"改掉" |
| **区分优先级** | 标明哪些是必须修改的、哪些是建议性的 |
| **及时响应** | 24 小时内回应评审请求 |
| **感谢反馈** | 对评审意见表示感谢，即使是不同意见 |

---

<!-- _class: divider -->

# 第十章：实战：MC-5000 审批流程示例

---

# 场景设置

**场景**：MC-5000 项目需要为"五轴联动基本操作"创建文档。

**任务流**：

1. 技术写作工程师小王（作者）在 `feature/dr5000-5axis-operation` 分支上创建了 3 个主题
2. 小王完成自检后创建 PR #101
3. 小张（同行评审）检查 DITA 规范
4. 李工（SME，CNC 高级编程师）验证技术内容
5. 小王根据反馈修改
6. 赵经理（质量负责人）最终审核并批准
7. 文档自动发布并归档

---

# 场景：作者小王的自检

```bash
# 小王的自检操作
$ git checkout feature/dr5000-5axis-operation

# 验证 XML
$ find product/dr5000/zh-CN/ -name "c_5axis*.dita" \
  -exec xmllint --valid --noout {} \;
# 输出：(无错误)

# 检查占位符
$ grep -r "xxx\|TODO" product/dr5000/zh-CN/
# 输出：(无匹配)

# 构建测试
$ dita --input=ditamap/dr5000_manual.ditamap --format=pdf
# Build successful.

# 创建 PR
$ git push origin feature/dr5000-5axis-operation
$ gh pr create --title "[MC-5000] 添加五轴联动操作章节" \
    --reviewer zhang-peer,li-sme
```

---

# 场景：同行评审小张的检查

```markdown
## 同行评审意见（小张）

### c_dr5000_5axis_overview.dita

1. **[必须修改]** 第 23 行：`<shortdesc>` 超过 2 句话，
   建议缩减为一句概述。

2. **[建议修改]** 第 45 行：`<section>` 标题"工作原理"
   建议改为"五轴联动运动学原理"，更准确。

3. **[必须修改]** 元数据缺少 `othermeta name="status"`，
   M6 规则未满足。请添加 `content="review"`。

### t_dr5000_5axis_setup.dita

4. **[必须修改]** 步骤 3 中 `importance` 未设置，
   这个步骤涉及刀具碰撞风险，应设置为 `importance="urgent"`。

5. **[通过]** 步骤结构清晰，前置条件完整。
   整体任务粒度合理，T5 模板使用正确。
```

---

# 场景：SME 李工的审查

```markdown
## SME 技术审查意见（李工）

### t_dr5000_5axis_setup.dita

1. **[必须修改]** 第 12 步：G 代码 `G68.2` 的格式有误。
   正确格式应为 `G68.2 X0 Y0 Z0 I0 J1 K0 R45.0`，
   而不是文档中写的 `G68.2 X0 Y0 Z0 I0 J0 K0`。

2. **[必须修改]** 第 8 步：五轴联动前必须确认
   A 轴和 C 轴的软限位参数已正确设定。
   如未设定，可能导致转台与刀具碰撞。这是严重安全问题！

3. **[建议修改]** 第 5 步：建议补充说明，
   工件坐标系 G54 的 Z 轴零点应在工件顶面而非底面。
   这与三轴加工的习惯不同，新操作工容易出错。

4. **[通过]** 其余 G 代码示例和技术参数准确无误。
```

---

# 场景：小王的修订和最终审批

```bash
# 小王处理反馈
$ vim product/dr5000/zh-CN/concepts/c_dr5000_5axis_overview.dita
$ vim product/dr5000/zh-CN/tasks/t_dr5000_5axis_setup.dita

$ git add .
$ git commit -m "fix: 根据 PR #101 评审意见修订

同行评审 (小张):
- 精简 shortdesc (c_5axis_overview)
- 完善 section 标题
- 补充 M6: status=review
- 设置步骤3 importance=urgent

SME 审查 (李工):
- 修正 G68.2 指令格式
- 新增 A/C 轴软限位检查步骤
- 补充工件坐标系零点位置说明"

$ git push origin feature/dr5000-5axis-operation

# 赵经理审批
$ gh pr review 101 --approve --body "所有评审意见已解决。质量审核通过。批准合并。"
$ gh pr merge 101 --squash --delete-branch
```

---

<!-- _class: divider -->

# 第十一章：常见问题与解决方案

---

# 问题 1：评审周期过长

**症状**：PR 创建后 5 天仍无评审响应。

**解决方案**：
- 设置评审 SLA（如 48 小时内必须响应）
- 使用 GitHub 的 **Request review** 功能明确指派
- 在团队群中每日提醒未处理的 PR
- 对于紧急的安全文档，使用 `priority:urgent` 标签

```bash
# 查看所有待评审的 PR
gh pr list --label "status:review"

# 为紧急文档添加 urgent 标签
gh pr edit 101 --add-label "priority:urgent"
```

---

# 问题 2：评审意见冲突

**症状**：同行评审说"改 A"，SME 说"保持 A"。

**解决方案**：
- 在 PR 评论中发起讨论
- 必要时召集相关方快速会议
- **SME 的意见在技术问题上优先**
- **同行评审的意见在规范问题上优先**
- 记录最终决策在 PR 评论中

---

# 问题 3：PR 包含过多变更

**症状**：一个 PR 包含 20 个主题文件，涉及 3 个不同章节，难以评审。

**解决方案**：
- 控制 PR 粒度：**一个 PR = 一个逻辑工作单元**
- 理想 PR 大小：3-8 个新主题，或 1 个完整的小章节
- 超过 10 个文件时考虑拆分
- 使用子 Map 拆分大型变更

---

# 问题 4：合并冲突

**症状**：两个 feature 分支修改了同一个 Map 文件。

```bash
# 解决步骤
git checkout feature/dr5000-5axis-operation
git fetch origin main
git rebase origin/main

# 如果发生冲突
# 1. 编辑冲突文件
# 2. git add <resolved-files>
# 3. git rebase --continue
# 4. git push --force-with-lease
```

> 使用 `--force-with-lease` 而非 `--force`，更安全。

---

<!-- _class: divider -->

# 第十二章：动手练习

---

<!-- _class: exercise -->

# 练习 1：识别评审角色

根据以下场景，判断应该由哪个角色来审查：

1. 检查 DITA 文件是否符合 DTD 规范
2. 验证 CNC 主轴转速参数是否正确
3. 确认元数据 M1-M7 全部满足
4. 检查安全警告级别是否合理
5. 判断文档的语言风格是否一致
6. 确认 G 代码示例的语法是否正确

---

<!-- _class: exercise -->

# 练习 2：模拟 PR 创建

任务：为 MC-5000 创建一个模拟 Pull Request。

在本地完成以下操作：
1. 从 main 创建 `feature/dr5000-coolant-system` 分支
2. 创建一个概念主题 `c_dr5000_coolant.dita`
3. 提交并推送
4. 编写 PR 描述（使用 Markdown 模板）
5. 添加评审者并设置标签

使用 GitHub CLI (`gh`) 或手动编写 PR 描述。

---

<!-- _class: exercise -->

# 练习 3：编写评审意见

针对下面的主题片段，以同行评审的身份写出评审意见：

```xml
<task id="task_dr5000_coolant_change">
  <title>更换冷却液</title>
  <taskbody>
    <steps>
      <step><cmd>打开排放阀</cmd></step>
      <step><cmd>添加新冷却液</cmd></step>
    </steps>
  </taskbody>
</task>
```

提示：检查必填元素、安全事项、步骤完整性。

---

<!-- _class: exercise -->

# 练习 4：修订和解决意见

模拟收到 SME 的以下意见后的修订操作：

> "排放冷却液前必须先关闭冷却泵并冷却至 40°C 以下。冷却液在高温时打开排放阀可能导致烫伤。"

1. 你会如何修改 `t_dr5000_coolant_change.dita`？
2. 你会添加什么安全警告元素？
3. 修订后的 commit message 怎么写？

---

# 练习 1 参考答案

| 场景 | 负责角色 |
|------|---------|
| 检查 DTD 规范 | 同行评审（或自动验证） |
| 验证主轴转速参数 | SME（CNC 工程师） |
| 确认 M1-M7 | 同行评审 / 质量负责人 |
| 检查安全警告级别 | SME + 同行评审 |
| 判断语言风格 | 同行评审 |
| 确认 G 代码语法 | SME（CNC 编程师） |

---

# 练习 2 参考步骤

```bash
# 1. 创建分支
git checkout main && git pull origin main
git checkout -b feature/dr5000-coolant-system

# 2. 创建概念主题（使用模板）
cp templates/zh-CN/concept/t1-concept-basic.dita \
   product/dr5000/zh-CN/concepts/c_dr5000_coolant.dita

# 编辑 c_dr5000_coolant.dita...

# 3. 提交
git add product/dr5000/zh-CN/concepts/c_dr5000_coolant.dita
git commit -m "feat: 添加 MC-5000 冷却系统概述概念主题"
git push origin feature/dr5000-coolant-system

# 4. 创建 PR
gh pr create \
  --title "[MC-5000] 添加冷却系统概述" \
  --body "$(cat <<'EOF'
## 变更内容
- 新增概念主题：MC-5000 冷却系统概述 (T1 模板)
- 元数据满足 M1-M7
- XML 格式正规，DTD 验证通过

## 需要评审的内容
- [ ] DITA 结构是否符合规范
- [ ] 技术术语是否准确（请 SME 确认）
- [ ] 安全相关内容是否充分

## 评审者
- 同行评审：@zhang-peer
- SME 审查：@li-cooling-expert
EOF
  )" \
  --reviewer zhang-peer,li-cooling-expert \
  --label "status:review,type:concept"
```

---

# 练习 3 参考评审意见

```markdown
## 同行评审意见

### t_dr5000_coolant_change.dita

1. **[必须修改] 缺少必填元素**：
   - 缺少 `<shortdesc>`：所有任务主题必须有简短描述
   - 缺少 `<prereq>`：更换冷却液需要什么工具和准备？
   - 缺少 `<result>`：更换完成后应该是什么状态？

2. **[必须修改] 步骤不完整**：
   - 缺少"排出旧冷却液"和"清洗冷却液箱"的步骤
   - 只说了打开排放阀和添加新液，中间步骤缺失

3. **[必须修改] 安全隐患**：
   - 未标注冷却液的处理注意事项
   - 建议在步骤 1 前增加安全警告

4. **[必须修改] 元数据缺失**：
   - 缺少 prolog 和 M1-M7 必填元数据
```

---

# 练习 4 参考修订

修订后的主题关键部分：

```xml
<task id="task_dr5000_coolant_change">
  <title>更换 MC-5000 冷却液</title>
  <shortdesc>安全更换 CNC 冷却系统的冷却液。</shortdesc>
  <taskbody>
    <prereq>冷却泵已停止运转。冷却液温度已降至 40°C 以下。
    准备新冷却液、接液容器和防护手套。</prereq>
    <steps>
      <step importance="urgent">
        <cmd>确认冷却液温度低于 40°C，冷却泵已断电。</cmd>
        <warning type="danger">高温冷却液可能导致严重烫伤。
        严禁在冷却液高于 40°C 时打开排放阀！</warning>
      </step>
      <step><cmd>将接液容器放在排放阀下方。</cmd></step>
      <step><cmd>缓慢打开排放阀，排空旧冷却液。</cmd></step>
      <step><cmd>清洗冷却液箱内部。</cmd></step>
      <step><cmd>关闭排放阀，注入新冷却液至标准液位。</cmd></step>
    </steps>
    <result>冷却液更换完成，液位正常，可启动冷却泵。</result>
  </taskbody>
</task>
```

修订的 commit message：

```
fix: 安全增强 - 冷却液更换程序添加高温警告

根据李专家 (SME) 反馈：
- 新增步骤：确认温度低于 40°C 且泵已断电
- 新增 warning type="danger" 高温烫伤警告
- 补充缺失步骤：排放废液、清洗液箱
- 补全 shortdesc、prereq、result 等必填元素
```

---

<!-- _class: divider -->

# 第十三章：总结与回顾

---

<!-- _class: keypoint -->

# 模块要点回顾

| # | 要点 |
|---|------|
| 1 | 制造文档审批是**安全保障**，不只是流程形式 |
| 2 | 10 步工作流 = 需求(1) + 编写(2-3) + 评审(4-5) + 修订(6) + 审批(7-8) + 发布(9-10) |
| 3 | Git 分支策略：feature 分支 → PR → 评审 → Squash Merge → main |
| 4 | 四个角色：**作者**(编写) / **同行评审**(规范) / **SME**(技术) / **质量负责人**(合规) |
| 5 | 评审意见必须**分类**(必须/建议/信息性)并**逐条回应** |
| 6 | 发布 = CI/CD 自动构建 + Git Tag 版本归档 |

---

# 审批流程完整工作流

```
需求分析 → 创建 feature 分支
              ↓
使用模板创建主题、填写内容
              ↓
自检：(XML验证 + 元数据 M1-M7 + 占位符搜索 + 构建测试)
              ↓
创建 PR → 请求同行评审 + SME 审查
              ↓
分类处理评审意见 → 修改代码 → 逐条标记解决
              ↓
质量审核：构建验证 + 合规检查 + 输出预览
              ↓
批准合并 → CI/CD 自动发布 → Git Tag 归档
```

---

# 审批流程速查卡

```bash
# 开始工作
git checkout -b feature/dr5000-{模块}

# 自检
grep -r "xxx\|TODO" product/dr5000/ && xmllint --valid *.dita

# 提交 PR
gh pr create --title "[MC-5000] ..." --reviewer reviewer1,sme1

# 修订
git commit -m "fix: 根据 PR #N 反馈..." && git push

# 批准
gh pr review N --approve && gh pr merge N --squash

# 发布
git tag -a "v1.0.0-dr5000-{doc}" && git push --tags
```

---

# 下节预告

下一课是初级课程的最后一课——**动手实践项目**，你将把你所学的一切付诸实践，为 MC-5000 创建一个完整的文档集，从零到发布。

---

<!-- _class: title -->

# 谢谢
## 下一课：动手实践项目

**DITA 课程 | 制造业文档 | 示例制造**
