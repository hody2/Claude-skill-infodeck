<div align="center">

<h1>📖 Claude Skill · Infodeck</h1>

<p><strong>把一份技术文档变成一份能投屏讲解的图解 HTML · 单文件 · 自包含 · 离线可看</strong></p>

<p>
  <a href="./README.md"><strong>中文</strong></a>
  ·
  <a href="./SKILL.md">SKILL.md</a>
  ·
  <a href="./skill/examples/monitoring-v3.html">在线示例</a>
</p>

<p>
  <a href="./LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
  <img src="https://img.shields.io/badge/Claude-Skill-D97757.svg?logo=anthropic&logoColor=white" alt="Claude Skill">
  <img src="https://img.shields.io/badge/output-Single%20HTML-orange.svg" alt="Single-file HTML">
  <img src="https://img.shields.io/badge/SVG-inline-purple.svg" alt="Inline SVG">
  <img src="https://img.shields.io/badge/templates-7-success.svg" alt="7 Templates">
</p>

<p>7 种图示模板 · 5 条设计原则 · 1474 行实战样例 · 在监控指标方案上跑过真活</p>

<pre>
┌─────────────────────────────────────────┐
│   📖 怎么看这张图：左→右是叙事顺序     │
├─────────────────────────────────────────┤
│  [架构]→[决策]→[细节]→[关系]→[落地]    │
│   ▼      ▼      ▼      ▼      ▼        │
│  🟥红   🟪紫   🟦蓝   ⚪灰   🟨黄      │
│  急诊   底座   趋势   弃用   提示       │
└─────────────────────────────────────────┘
</pre>

</div>

---

## 🤔 为什么做这个项目？

把一份技术方案讲明白，我不想再做下面这些事：

- 🎨 **每次重画轮子** — 同一种「三层架构」「决策树」「故事卡」，每个文档都要重新调一遍 SVG 坐标
- 🌫️ **配色失控** — 第一张图红色代表「急诊」，第三张图红色又变成「编号徽章」，读者直接懵
- 📑 **PPT 太重** — 只是想给组会发 5 分钟讲解，没必要做 30 页 deck
- 💥 **SVG 玄学坑** — `<text>` 里写了 `<u>` 标签，后面所有图直接不渲染（这种隐性 bug 肉眼看不出）
- 🤖 **AI 装饰画** — 让 Claude 直接画图常出现「20 个框 + 50 根线」的装饰图，回答不了任何具体问题

这个 skill 把这些问题一次性收敛，**让 Claude 按一套确定的规范产出图解 HTML**——而不是每次都瞎画一遍。

### 关键产物指标

| 指标 | 数值 | 说明 |
|---|---|---|
| **输出格式** | **单文件 HTML** | CSS / SVG 全内联，双击可看，离线归档 |
| **图示模板** | **7 种** | 覆盖架构 / 决策 / 卡片 / 时间线 / 关系映射 |
| **设计原则** | **5 条** | 信息密度 + 颜色统一 + 阅读入口 + 叙事顺序 + 完整性校验 |
| **质量门控** | **3 个 grep 检查** | section / svg 标签平衡 + SVG 文本反污染 |
| **实战样例** | **1474 行** | 一份监控指标方案的完整图解版 |

---

## ✨ 特性

- 🧱 **7 种核心图示模板** — 每种回答一类具体问题，不画装饰图
- 🎨 **统一视觉语言** — CSS 变量驱动配色，第一张图建立的色彩编码全篇坚持
- 📖 **每图必带「怎么看」提示框** — 黄色阅读入口把读图门槛从 30 秒降到 5 秒
- 🧭 **顶部 sticky 导航** — 多图之间一键跳转，长 deck 也不晕
- 🪞 **响应式 + 移动端友好** — 投屏 / 电脑 / 手机都能看
- 🛡️ **三重质量门控** — 标签平衡 + SVG 文本反污染 + viewBox 边界校验，交付前 grep 一次
- 💾 **离线自包含** — 单文件 HTML，无外链资源，可直接归档 / 二次编辑
- 🔁 **可复用经验沉淀** — `svg-patterns.md` 收录的坑（如 `<u>` 标签会废掉整张 SVG）都已写进规则

---

## 🚀 快速开始

### 方式一：Claude.ai 项目知识库（推荐）

```bash
# 1. 克隆本仓库
git clone https://github.com/hody2/Claude-skill-infodeck.git
cd Claude-skill-infodeck
```

```
2. 在 Claude.ai 网页版「Projects」中新建一个项目
3. 把 SKILL.md 和整个 skill/ 目录上传到该项目的 Knowledge
4. 之后在该项目里发任何技术文档，对 Claude 说：
   "按 SKILL.md 的规范生成图解 HTML"
```

### 方式二：Claude Code / Codex skill 形式

```bash
# 把 skill/ 目录链接到 Claude Code 的 plugin 目录
ln -s "$(pwd)/skill" ~/.claude/plugins/infodeck
# 重启 Claude Code 即可触发：
#   "把这份文档做成图解" / "做一份图示版"
```

### 方式三：手动上下文注入

```
跟 Claude 直接说："使用以下设计规范生成图示" 然后把 SKILL.md 内容贴给它。
适合一次性使用，不需要长期挂在项目里。
```

---

## 🎨 7 种核心图示

每种图示**只回答一类问题**，绝不混用。所有模板在 `skill/templates/`，照搬即可。

| 图示类型 | 回答什么问题 | 模板文件 | 典型场景 |
|---|---|---|---|
| 🏛️ **三层架构** | 这个方案分几层？每层做什么？ | `three-layer.svg` | 监控分层 / 推荐系统分层 / 服务分层 |
| 🌲 **决策树** | 这个东西该归到哪一类？ | `decision-tree.svg` | 告警分级 / 策略选择 / 风控路由 |
| 🗂️ **故事卡** | 多个并列概念分别是什么？ | `story-cards.svg` | N 个 feature / 角色 / 模块卡片化 |
| 💓 **心电图 / 生命周期** | 实体在时间维度上的状态怎么变？ | `lifecycle-trace.svg` | 订单状态 / 用户旅程 / 任务生命周期 |
| 🎛️ **仪表盘分组** | 多组并列分类下各自的指标？ | `group-dashboard.svg` | KPI 对比 / 多机房状态 / A/B 实验 |
| 📅 **阶段甘特** | 怎么分阶段落地？ | `phased-rollout.svg` | 实施路线图 / 迁移计划 / 灰度策略 |
| ⚓ **执行点锚定** | 抽象方案对应到具体系统的哪里？ | `pipeline-anchor.svg` | 抽象架构 → 真实模块名映射 |

---

## 📖 设计哲学

一句话：**信息密度高，但每张图都有「怎么读」入口**。

详细 5 条原则在 [`skill/design-principles.md`](./skill/design-principles.md)：

1. **每张图回答一个具体问题** — 装饰图不要画，标题用陈述句或问句
2. **颜色编码全篇统一** — 第一张图就建立映射，之后绝不偏移
3. **每张图前置黄色阅读提示框** — 把读图门槛从 30 秒降到 5 秒
4. **叙事顺序固定**：总体 → 决策框架 → 细节 → 关系 → 落地
5. **写完必须 grep 验证** — 标签平衡 + SVG 文本反污染检查

---

## 🛡️ 内置质量门控

每次 Claude 生成完 HTML，会**强制**跑这三道 grep 检查再交付：

```bash
# Check 1 — section 标签开闭平衡
grep -c "<section" output.html        # 必须 ==
grep -c "</section>" output.html      # 必须 ==

# Check 2 — SVG <text> 里禁止出现 HTML 标签
grep -n '<u>\|<b>\|<i>\|<strong>' output.html | grep -v "foreignObject"
# 必须为空（出现就会让后续 SVG 全部不渲染，是真实踩过的坑）

# Check 3 — 每个 <svg viewBox> 高度足够
# 人工 / 视觉检查：viewBox 的 height ≥ 内部元素最大 y 坐标
```

> **为什么这条很重要**：实战中 SVG 里写了一个 `<u>异构</u>`，整张图只渲染前 3 个分块，后 2 个完全消失。SVG 严格模式不允许 HTML 标签，遇到就中止解析。肉眼看代码看不出来——只能 grep。

---

## 📁 项目结构

```
Claude-skill-infodeck/
├── README.md                          ← 你正在看这个
├── LICENSE                            ← MIT
├── SKILL.md                           ← Claude 主入口（触发条件 + 工作流）
└── skill/
    ├── design-principles.md           ← 5 条贯穿原则
    ├── color-system.md                ← CSS 变量 / 配色 / 字号 / 间距
    ├── layout-patterns.md             ← 7 种布局模式详解
    ├── svg-patterns.md                ← SVG 实战陷阱（必读，含 <u> 标签坑）
    ├── templates/                     ← 7 个 SVG 图示模板 + 1 个 HTML 骨架
    │   ├── page-skeleton.html
    │   ├── three-layer.svg
    │   ├── decision-tree.svg
    │   ├── story-cards.svg
    │   ├── lifecycle-trace.svg
    │   ├── group-dashboard.svg
    │   ├── phased-rollout.svg
    │   └── pipeline-anchor.svg
    └── examples/
        └── monitoring-v3.html         ← 完整成品（1474 行，监控指标方案图解版）
```

---

## 🖼️ 完整成品长什么样

[`skill/examples/monitoring-v3.html`](./skill/examples/monitoring-v3.html) 是用本 skill 做的真实案例：

> **输入**：一份 37 KB 的监控指标方案纯文字稿
> **输出**：7 张图、1474 行 HTML、自包含可投屏
> **耗时**：Claude 一次性生成完，跑完 grep 校验直接交付

打开浏览器双击即可看。

---

## 🧠 沉淀的实战经验

这些是踩过坑之后写进 skill 的硬规则：

- 「📖 怎么看这张图」**必须**做成黄色提示框，作为阅读入口
- 叙事顺序**必须**是「总体 → 决策框架 → 详细 → 关系映射」，反过来会让读者迷路
- 颜色编码**必须**在第一张图就建立，全篇坚持，不允许中途切换语义
- 实施时间线**用「阶段一/二/三」**，不用「第 1 周 / 第 2 周」（周数会过时）
- 决策树**用「主干 + 旁路」**比泳道图清晰
- 配套机制**单独说**，不要塞进决策树
- SVG `<text>` 里**绝对不能**有 HTML 标签（必须用 `<tspan>` 替代）
- 写完**一定**跑一次 section count balance 检查

---

## ❓ FAQ

<details>
<summary><strong>这跟 mermaid / draw.io / PPT 有什么区别？</strong></summary>

- **mermaid**：适合单一流程图 / 架构图，多图组合 + 阅读引导 + 配色统一不擅长
- **draw.io / Figma**：手工拖拽，不能让 AI 一次性产出
- **PPT**：太重，做 5 分钟讲解不划算，且无法离线归档为单文件 HTML
- **本 skill**：专门解决「把一份技术方案讲明白」这一类场景，AI 按规范产出，配色/叙事/校验全自动

</details>

<details>
<summary><strong>能不能让 Claude 自动改样式 / 加图？</strong></summary>

可以。生成完 HTML 后直接对 Claude 说「把第 3 张图改成 group-dashboard 模板」「再加一张关系映射图」即可。skill 是规范层，不锁死具体图。

</details>

<details>
<summary><strong>不用 Claude.ai 项目，能在 Claude Code / Codex 里用吗？</strong></summary>

可以。把 `skill/` 目录软链到 `~/.claude/plugins/infodeck`（或对应 Codex skill 目录），重启即可触发。

</details>

<details>
<summary><strong>输出的 HTML 能再编辑吗？</strong></summary>

完全可以。单文件 HTML，CSS 在 `<style>` 标签内，SVG 是内联的——拿任何编辑器打开改坐标 / 配色 / 文案都行。

</details>

---

## License

MIT — 见 [LICENSE](./LICENSE)。
