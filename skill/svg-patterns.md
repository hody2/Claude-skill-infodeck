# SVG 实战陷阱

⚠️ **每次激活 skill 时都要读这份**。

这里收录的都是**实战中真实踩过的坑**，不是理论上的"最佳实践"。每条都有"症状 / 原因 / 修复 / 预防"四段。

---

## 陷阱 1：SVG `<text>` 里写 HTML 标签

### 症状
图示渲染了一半，从某个位置开始**整段消失**（不是被遮挡，是完全不渲染）。例如设计了 B1-B5 共 5 组卡片，浏览器只显示 B1、B2、B3。

### 原因
SVG 是 XML 严格模式，`<text>` 元素里不允许嵌入 HTML 内联标签：
- `<u>` 下划线
- `<b>` 粗体
- `<i>` 斜体
- `<strong>` 强调
- `<em>` 着重

遇到任何一个，浏览器解析就**立刻中止**整个 SVG，导致**该标签之后**的所有 SVG 元素全部消失。

### 修复（用 `<tspan>` 替代）

```xml
<!-- ❌ 错误（会破坏渲染） -->
<text>用<u>异构</u> LLM 评判</text>
<text>必须用<b>外部信道</b>报警</text>

<!-- ✅ 正确 -->
<text>用<tspan text-decoration="underline">异构</tspan> LLM 评判</text>
<text>必须用<tspan font-weight="700">外部信道</tspan>报警</text>
```

### 预防
生成完 HTML 后必须跑这条 grep：

```bash
grep -n '<u>\|<b>\|<i>\|<strong>\|<em>' output.html | grep -v "foreignObject"
```

**应该为空**。如果有命中，要么改成 `<tspan>`，要么把这段文字放到 `<foreignObject>` 里（foreignObject 里是真 HTML，可以用任何标签）。

---

## 陷阱 2：viewBox 高度不够，下方内容被裁掉

### 症状
图示底部的内容（caption、conclusion、最后一组卡片）显示不全。

### 原因
SVG 的 viewBox 定义了"可视区域"，超出 viewBox 的内容会被裁掉。手写坐标时容易低估总高度。

### 修复
生成前先算总高度：
- 列出所有 `<g transform="translate(x, y)">` 的 y 坐标
- 找最大 y 坐标 + 该 group 内容的最大延伸（如 rect 的 height）
- viewBox 高度 = 最大值 + 至少 30px 余量

### 预防
养成习惯：每次写完 SVG 在心里过一遍"最底下那个元素的最大 y 坐标是多少"。如果不确定，就把 viewBox 高度多设 50px。

---

## 陷阱 3：foreignObject 里的 HTML 不继承外面的 CSS

### 症状
在 `<foreignObject>` 里写的 `<div>` 字体跟外面 SVG 文字风格不一致，间距奇怪。

### 原因
foreignObject 内部是一个独立的 HTML 子树，**不会自动继承外部 CSS**。

### 修复
在 foreignObject 内部的 div 上**显式声明**字体样式：

```xml
<foreignObject x="10" y="42" width="212" height="120">
  <div xmlns="http://www.w3.org/1999/xhtml" 
       style="font-size:11px; line-height:1.45; font-family:sans-serif; color:#1a1a1a;">
    内容
  </div>
</foreignObject>
```

注意：
- 必须有 `xmlns="http://www.w3.org/1999/xhtml"`，否则部分浏览器不渲染
- 用 inline style，不要依赖外部 CSS

### 预防
所有 foreignObject 内的 div 都用统一的 style 字符串（包含 font-size / line-height / font-family / color 四个属性）。

---

## 陷阱 4：marker（箭头标记）共享 ID 失效

### 症状
第二张图开始，箭头消失（变成裸线条）。

### 原因
两张 SVG 都定义了 `<marker id="arrow">`，浏览器只识别第一个。第二个 SVG 引用 `url(#arrow)` 时找的是第一张图的定义，但作用域不通，导致渲染失败。

### 修复
每张 SVG 的 marker id 加唯一后缀：

```xml
<!-- 第一张图 -->
<marker id="arrow1" ...>...</marker>
<line marker-end="url(#arrow1)" />

<!-- 第二张图 -->
<marker id="arrow2" ...>...</marker>
<line marker-end="url(#arrow2)" />
```

### 预防
约定：每张图的 marker id 用 `arrow{图号}` 命名（arrow1、arrow2、arrow3...）。

---

## 陷阱 5：text-anchor 和 x 坐标错配

### 症状
文字位置看起来"差了一点点"，怎么调 x 都不对。

### 原因
`text-anchor` 有三种值：
- `start`（默认）：x 是文字**左端**
- `middle`：x 是文字**中点**
- `end`：x 是文字**右端**

混用时容易算错。

### 修复
形成肌肉记忆：
- 卡片标题、列表项 → `start`（左对齐），x = 卡片左边距
- 居中标题、徽章文字 → `middle`，x = 卡片宽度的一半
- 右侧数字、右上角标签 → `end`，x = 卡片右边距

### 预防
不要在同一个视觉单元里混用 anchor。一个 group 内统一用 start 或 middle。

---

## 陷阱 6：CDATA 段处理特殊字符

### 症状
在 SVG 文字里写代码示例（含 `<`、`>`、`&` 等），渲染时变成 HTML 实体显示。

### 原因
SVG 是 XML，`<` `>` `&` 是保留字符。

### 修复
要么转义：
```xml
<text>condition &amp;&amp; action</text>
<text>x &lt; 100</text>
```

要么用 foreignObject：
```xml
<foreignObject>
  <div xmlns="http://www.w3.org/1999/xhtml">
    <code>condition && action</code>
  </div>
</foreignObject>
```

代码块强烈推荐用 foreignObject + `<pre><code>`，不要硬塞 `<text>`。

### 预防
写代码示例之前先问自己："这段会不会有 `<` `>` `&` `"`？" 如果有，自动用 foreignObject。

---

## 陷阱 7：嵌套 transform 累加坐标系

### 症状
一个 group 内的元素，明明 y 坐标算对了，渲染出来还是位置不对。

### 原因
父级 `<g transform="translate(20, 100)">` 的偏移会**累加**到子元素的坐标上。子元素的 `y=30` 在屏幕上实际是 `y=130`。

### 修复
子元素坐标是**相对于父 group** 的，不是绝对坐标。设计布局时分两层：
- 父 group 的 transform：定义"这个区块从屏幕的哪里开始"
- 子元素坐标：定义"在这个区块内部的相对位置"

### 预防
养成习惯：每个有 `<g transform>` 的 group，内部元素的坐标都从 0,0 开始重新计算，不要混用绝对/相对坐标。

---

## 验证脚本（每次交付前必跑）

```bash
# 1. section 开闭平衡
SECTIONS_OPEN=$(grep -c "<section" output.html)
SECTIONS_CLOSE=$(grep -c "</section>" output.html)
echo "section: open=$SECTIONS_OPEN, close=$SECTIONS_CLOSE"
# 必须相等

# 2. svg 开闭平衡
SVG_OPEN=$(grep -c "<svg" output.html)
SVG_CLOSE=$(grep -c "</svg>" output.html)
echo "svg: open=$SVG_OPEN, close=$SVG_CLOSE"
# 必须相等

# 3. SVG <text> 里没有 HTML 标签
HTML_IN_SVG=$(grep -n '<u>\|<b>\|<i>\|<strong>\|<em>' output.html | grep -v "foreignObject" | wc -l)
echo "HTML tags in SVG: $HTML_IN_SVG"
# 必须 = 0

# 4. foreignObject 必须有 xmlns
MISSING_XMLNS=$(grep -c "<foreignObject" output.html)
HAS_XMLNS=$(grep -c 'xmlns="http://www.w3.org/1999/xhtml"' output.html)
echo "foreignObject: $MISSING_XMLNS, xmlns declarations: $HAS_XMLNS"
# 大致应该匹配
```

任何一项不通过，**不能交付**，必须先修复。
