# Infodeck Skill

把技术文档转成自包含、可导航的 HTML 图解页面。

---

## When to use this skill

激活这个 skill，当用户的请求满足以下任一条件：

1. **明确请求图解**：用户说"生成图示 / 做一份图解版 / 可视化讲解 / 画出来 / 图示版 / 做一份学习材料"等
2. **要把技术文档变成讲解材料**：用户上传或粘贴了一份复杂的技术方案，并要求"做一份给组会用的 / 学习用的 / 组内分享的"图示
3. **多概念并列展示**：用户的内容包含三个以上并列的概念、分类、流程阶段或对比项，并希望"看图比看字快"

**不激活**的场景：
- 用户只想要一张图（用 SVG/HTML widget 直接出即可，不需要整套 deck）
- 用户要求生成 PPT/PDF/Word（用对应的文档 skill）
- 用户的内容是单一流程图、单一架构图（用 mermaid 就够了）

---

## 触发后的工作流程

### Step 1: 理解输入文档

读完全部输入后，先回答以下三个问题（在心里完成，不需要输出给用户）：

1. **核心叙事顺序是什么？** 通常应该是 "总体架构 → 决策/分类框架 → 具体细节 → 关系映射"
2. **能识别出多少种图示类型？** 对照 `skill/layout-patterns.md` 中的 7 种模式
3. **颜色编码怎么定？** 如果文档有 A/B/C 类、第一层/第二层/第三层、急诊/常规/趋势 这样的分类轴，必须在第一张图就用颜色固定下来

### Step 2: 规划图示清单

输出 4-8 张图，每张图必须**回答一个具体问题**，避免"装饰性图示"。典型清单：

| 序号 | 图示 | 回答的问题 |
|---|---|---|
| 01 | 总体架构 | 这个方案分几层？每层做什么？ |
| 02 | 决策框架 / 分类树 | 怎么决定某个东西归到哪一类？ |
| 03-04 | 详细卡片 | 每一类里具体有哪些条目？各自解决什么？ |
| 05 | 关系视图 | 这些条目之间的依赖/触发关系？ |
| 06 | 实施时间线 | 怎么分阶段落地？ |
| 07 | 真实场景锚定 | 这套抽象的方案对应到具体系统的哪里？ |

### Step 3: 加载相关参考资料

在生成代码之前，读取以下文件（按需）：

- 总是读：`skill/design-principles.md`（5 条贯穿始终的原则）
- 总是读：`skill/color-system.md`（CSS 变量 / 配色 / 字号）
- 总是读：`skill/svg-patterns.md`（**必读，包含 `<u>` 标签等致命陷阱**）
- 按图示类型读：`skill/layout-patterns.md` 对应章节
- 找模板参考：`skill/templates/` 下对应的 SVG 文件
- 看完整案例：`skill/examples/monitoring-v3.html`

### Step 4: 生成 HTML 文件

按 `skill/templates/page-skeleton.html` 作为整页骨架。每张图嵌入为内联 SVG（不外链）。

### Step 5: 验证

生成完成后**必须**执行以下检查（用 bash 工具）：

```bash
# Check 1: section 标签开/闭平衡
grep -c "<section" output.html
grep -c "</section>" output.html
# 两个数字必须相等

# Check 2: SVG 文本里没有非法 HTML 标签
grep -n '<u>\|<b>\|<i>\|<strong>' output.html | grep -v "foreignObject"
# 应该为空（HTML 标签只能出现在 foreignObject 里）

# Check 3: 每张图的 viewBox 高度足够容纳内容
# 人工检查每个 <svg viewBox=...> 的 height 是否 >= 内部元素的最大 y 坐标
```

---

## 核心交付物

一个**单文件 HTML**，特征：

- 自包含（CSS/SVG 全内联，可离线打开）
- 顶部 sticky 导航栏（跳转到各 figure）
- 每张图配 **"📖 怎么看这张图"** 黄色提示框
- 每张图下方配 caption（关键洞察 / 记忆要点）
- 移动端友好（响应式 width: 100%）

---

## 不允许做的事

- **不允许**让 AI 写"装饰图"（每张图必须回答一个具体问题）
- **不允许**用 emoji 当主要视觉元素（emoji 可以作为辅助，但不能撑场面）
- **不允许**在 SVG `<text>` 元素里用 HTML 标签（`<u>` `<b>` `<i>` 等会破坏渲染，必须用 `<tspan>`）
- **不允许**在时间线上用"第 1 周 / 第 2 周"等具体周数（用"阶段一/二/三"）
- **不允许**输出半完成品就交付（必须跑完 Step 5 验证）
- **不允许**把"配套机制"和"主流程"混在同一张图（容易把读者搞混）

---

## 内部文件索引

| 文件 | 内容 | 何时读 |
|---|---|---|
| `skill/design-principles.md` | 5 条贯穿原则 | 每次激活时 |
| `skill/color-system.md` | CSS 变量 / 配色 / 字号 / 间距 | 每次激活时 |
| `skill/layout-patterns.md` | 7 种核心布局模式 | 决定用哪种图时 |
| `skill/svg-patterns.md` | SVG 实战陷阱 | 每次激活时（必读） |
| `skill/templates/page-skeleton.html` | 整页 HTML 骨架 | 起手时 |
| `skill/templates/three-layer.svg` | 三层架构图模板 | 画总体架构时 |
| `skill/templates/decision-tree.svg` | 决策树模板 | 画归类流程时 |
| `skill/templates/story-cards.svg` | N×N 卡片网格模板 | 画并列概念时 |
| `skill/templates/lifecycle-trace.svg` | 实体生命周期追踪模板 | 画时间维度状态变化时 |
| `skill/templates/group-dashboard.svg` | 多组分类仪表盘模板 | 画并列分组时 |
| `skill/templates/phased-rollout.svg` | 阶段甘特图模板 | 画实施时间线时 |
| `skill/templates/pipeline-anchor.svg` | 抽象→具体执行点映射模板 | 画落地形态时 |
| `skill/examples/monitoring-v3.html` | 完整成品参考 | 不确定整体观感时打开看 |
