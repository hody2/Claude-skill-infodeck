# Color & Typography System

视觉语言规范。所有 infodeck 都使用这套系统，保证风格一致。

---

## CSS 变量（必须放在 `<style>` 顶部）

```css
:root {
  --bg: #fafaf7;           /* 页面背景，淡黄米色，比纯白柔和 */
  --ink: #1a1a1a;          /* 主文字色，接近黑但不死黑 */
  --ink-soft: #555;        /* 次要文字色 */
  --line: #2a2a2a;         /* 描边色，比 ink 稍深 */
  --paper: #ffffff;        /* 卡片底色，纯白与 bg 形成层次 */

  /* 语义色 */
  --accent-red: #c0392b;     /* 急诊 / 紧急 / A 类 */
  --accent-amber: #d68910;   /* 提示框 / 警告 / 阶段二 */
  --accent-green: #229954;   /* 成功 / 趋势 / 第三层 */
  --accent-blue: #2874a6;    /* 信息 / 仪表盘 / B 类 */
  --accent-purple: #7d3c98;  /* 中间层 / 心电图 / 数据底座 */
  --accent-gray: #888;       /* 丢弃 / 待处理 */

  --shadow: 4px 4px 0 #1a1a1a;     /* 卡片阴影，硬边风格 */
}
```

---

## 语义色对照表（保持全文统一）

| 语义 | 颜色 | CSS 变量 | 典型场景 |
|---|---|---|---|
| 紧急 / 阻塞 / A 类 / 第一层 | 红 #c0392b | `--accent-red` | 急诊警报、错误、违例 |
| 中间层 / 数据底座 / B 类 / 第二层 | 紫 #7d3c98 | `--accent-purple` | 心电图、日志层、追踪机制 |
| 趋势 / 非阻塞 / C 类 / 第三层 | 蓝 #2874a6 / 绿 #229954 | `--accent-blue` / `--accent-green` | 仪表盘、趋势观察、成本效率 |
| 提示 / 阅读引导 / 阶段二 | 黄 #d68910 | `--accent-amber` | "怎么看这张图"框、警示 |
| 丢弃 / 不重要 | 灰 #888 | `--accent-gray` | 决策树的"丢弃"出口 |

**铁律**：建立完颜色编码，全篇所有图必须遵守。读者从第一张图建立的色彩直觉，必须在最后一张图依然有效。

---

## 字号体系

| 用途 | 字号 | font-weight |
|---|---|---|
| 页面主标题 (h1) | 36px | 700 |
| section 标题 (h2) | 28px | 700 |
| section 编号（巨大透明描边数字） | 56px | 900，`-webkit-text-stroke: 2px var(--line); color: transparent;` |
| 图前的 lead | 15px | 400 |
| 黄色 reading 框 | 14px | 400 |
| caption | 14px | 400，`color: var(--ink-soft)` |
| SVG 内主标题文字 | 18px | 700 |
| SVG 内次标题 | 14-16px | 700 |
| SVG 内说明文字 | 11-13px | 400 |
| SVG 内 pill 标签 | 11px | 700 |

---

## 间距体系

| 用途 | 数值 |
|---|---|
| 页面最大宽度 | 1100px |
| section 之间 | margin: 80px 0 |
| figure 内 padding | 32px |
| figure 之间的间距 | margin: 24px 0 |
| reading 框 padding | 16px 20px |
| caption padding | 12px 16px |

---

## 视觉风格关键词

- **硬边风格**（hard shadow，没有 box-shadow blur）
- **3px 描边**主卡片，2px 描边次要元素
- **米色背景**（不用纯白，减少疲劳）
- **大量留白**（信息密度高但不挤）
- **巨型透明描边数字**作为 section 编号（视觉锚点）

---

## 不允许的样式

- ❌ 不允许使用 `box-shadow: 0 4px 12px rgba(...)` 这种模糊阴影（破坏硬边风格）
- ❌ 不允许使用 gradient 背景（除非有强烈语义理由）
- ❌ 不允许在 SVG 里写裸的颜色值（必须用 CSS 变量名，便于全局调整）
- ❌ 不允许字号小于 10px（移动端读不清）
- ❌ 不允许超过 6 种颜色同时出现在一张图里
