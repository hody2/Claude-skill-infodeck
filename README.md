# claude-skill-infodeck

A personal Claude skill that turns technical documents into self-contained, navigable HTML explainer pages — vertical SVG-heavy layout, "how to read this chart" callouts, color-coded legends, and a top-nav for jumping between figures.

把技术文档转成自包含、可导航的 HTML 图解页面的 Claude skill —— 纵向 SVG 布局、"怎么看这张图"提示框、统一的色彩图例、顶部导航跳转。在监控指标方案上实战验证过。

---

## 这是什么

一份给 Claude 用的 skill 文件包。当我把技术方案/分析文档发给 Claude 并要求"生成图示 / 做一份图解版 / 可视化讲解"时，Claude 会自动按照这个 skill 的设计规范产出一份**自包含的 HTML 页面**（单文件，含内嵌 SVG），用于：

- 学习材料 / 个人理解辅助
- 组会汇报 / 投屏讲解
- 离线归档 / 二次编辑

**不是**：通用图表生成器、流程图工具、PPT 替代品。它是为"把复杂技术方案讲明白"这个特定场景设计的。

## 设计哲学（一句话）

> 信息密度高，但通俗易懂。每张图都要回答一个具体问题，不画装饰图。

详见 `skill/design-principles.md`。

## 这个 skill 怎么用

### 方式一：在 Claude.ai 项目中使用

1. 把本 repo 整个 clone 到本地
2. 在 Claude.ai 网页版创建一个项目（Project）
3. 把 `SKILL.md` 和 `skill/` 目录下所有文件传到项目的 Knowledge
4. 之后跟 Claude 对话时，让它"按 SKILL.md 的规范生成图解 HTML"

### 方式二：手动引用

在跟 Claude 对话时直接说："使用这套设计规范生成图示" 然后把 `SKILL.md` 内容贴给它。

## 包含什么

```
.
├── README.md                      ← 你正在看的这个
├── LICENSE                        ← MIT
├── SKILL.md                       ← Claude 读取的主文件（触发条件 + 整体流程）
└── skill/
    ├── design-principles.md       ← 设计哲学（5 条核心原则）
    ├── color-system.md            ← 视觉语言规范（配色 / 字号 / 间距）
    ├── layout-patterns.md         ← 7 种核心布局模式
    ├── svg-patterns.md            ← SVG 实战陷阱（含 <u> 标签坑）
    ├── templates/                 ← 7 个 SVG 图示模板
    │   ├── page-skeleton.html
    │   ├── three-layer.svg
    │   ├── decision-tree.svg
    │   ├── story-cards.svg
    │   ├── lifecycle-trace.svg
    │   ├── group-dashboard.svg
    │   ├── phased-rollout.svg
    │   └── pipeline-anchor.svg
    └── examples/
        └── monitoring-v3.html     ← 完整成品参考（监控指标方案 V3 图解版）
```

## 7 种核心图示

每种都有对应的 SVG 模板和真实案例：

| 图示类型 | 解决什么问题 | 模板 |
|---|---|---|
| 三层架构 | 多层级体系的整体观 | `three-layer.svg` |
| 决策树 | "怎么把候选项归类"的流程 | `decision-tree.svg` |
| 故事卡 | 多个并列概念的卡片化展示 | `story-cards.svg` |
| 心电图 / 生命周期 | 实体在时间维度上的状态变化 | `lifecycle-trace.svg` |
| 仪表盘分组 | 多组并列分类的指标 | `group-dashboard.svg` |
| 阶段甘特 | 分阶段实施的时间线 | `phased-rollout.svg` |
| 执行点锚定 | 抽象概念→具体落地点的映射 | `pipeline-anchor.svg` |

## 一个完整成品长什么样

`skill/examples/monitoring-v3.html` 是用这个 skill 做的真实案例：把一份 37KB 的监控指标方案转成 7 张图的图解版。打开浏览器直接看。

## 已沉淀的核心经验

- 「📖 怎么看这张图」黄色提示框是阅读入口
- 总体 → 决策框架 → 详细 → 关系映射，必须按这个叙述顺序
- 颜色编码必须在**第一张图**就建立，全篇坚持
- 实施时间线用「阶段一/二/三」，不用具体周数
- 决策树用「主干 + 旁路」比泳道图清晰
- 配套机制单独说，不要塞进决策树
- SVG `<text>` 里**绝对不能**有 HTML 标签（必须用 `<tspan>` 替代，否则后续 SVG 元素全部不渲染）
- 写完一定要做一次"section count balance"检查（开/闭标签数量平衡）

## License

MIT — 见 LICENSE 文件。
