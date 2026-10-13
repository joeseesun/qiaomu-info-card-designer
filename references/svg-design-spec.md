# SVG 图表设计规范

> 信息卡片中内联 SVG 图表的完整设计指南。目标：从「线框图」升级到「精品感」。

## 核心原则

1. **单一焦点**：整个图表只有一个视觉重心，其他元素全部服务于它
2. **克制美学**：少即是多，删除一切不传递信息的元素
3. **色彩继承**：SVG 颜色从卡片配色方案继承，不引入新色
4. **信息增量 ≥ 30%**：图表必须比纯文字多传递至少 30% 的信息，否则不加

## 网格与间距

**8px 基础网格**：所有尺寸对齐到 4 或 8 的倍数。

| 间距层级 | 值 | 使用场景 |
|---------|------|---------|
| xs | 4px | 紧密关联元素（标签与图形） |
| sm | 8px | 同组元素内部 |
| md | 16px | 同区域不同元素 |
| lg | 24px | 不同区域 |
| xl | 32px | 主要分隔 |

```
黄金比例参考：组间距 ≈ 组内距 × 1.618
例：组内 16px → 组间 26px → 区域间 42px
```

**viewBox 规范**：
- 宽度：`520`（匹配 600px 卡片 - 40px×2 padding）
- 高度：按内容决定，通常 160-280px
- 图表是配角，高度不超过卡片总高的 15%

## 排版层级

**字号比例**（从大到小 ≈ 1.5:1 递减）：

| 层级 | 字号 | 用途 | font-weight |
|------|------|------|-------------|
| 主标签 | 22-24px | 焦点元素文字 | 600 |
| 次标签 | 18-20px | 非焦点元素文字 | normal |
| 注释 | 13-14px | 右侧说明、连接文字 | normal |
| 微标注 | 11-12px | 底部结论、分类标签 | normal |

**字体**：统一 `-apple-system, 'Helvetica Neue', sans-serif`（SVG 中不用楷体/宋体，确保截图渲染稳定）

**文本对齐**：
```xml
<!-- 居中对齐（圆内、矩形内） -->
<text text-anchor="middle" dominant-baseline="middle">

<!-- 左对齐（列表式布局） -->
<text text-anchor="start">

<!-- 右对齐（右侧注释） -->
<text text-anchor="end">
```

**letter-spacing**：
- 大字（≥20px）：`0` 或 `-0.02em`
- 小字标签（≤13px）：`0.06em - 0.15em`（增加小字可读性）

## 颜色与透明度系统

### 透明度四级制

| 等级 | opacity / fill-opacity | 用途 |
|------|----------------------|------|
| Primary | 1.0 | 焦点元素（文字、高亮图形） |
| Secondary | 0.6-0.7 | 次要元素 |
| Tertiary | 0.3-0.4 | 辅助信息、连接线 |
| Background | 0.02-0.08 | 背景色块、装饰 |

### 从卡片继承配色

SVG 不能用 CSS 变量（`var(--xxx)` 在 SVG 属性中无效），必须写固定 hex 值。

**浅色卡片**（bg ≈ `#f5f3ed`）：
```
焦点文字：#0a0a0a
次要文字：#555
淡化文字：#999
图形描边：卡片 accent 色
背景色块：rgba(0,0,0,0.03)
```

**深色卡片**（bg ≈ `#0E0B06`）：
```
焦点文字：#EAD9B5 ~ #F2E8D0
次要文字：#9A8E7A
淡化文字：#5A4E3A
极淡文字：#3A3020（接近不可见，暗示「已排除」）
图形描边：accent 色的提亮版本
背景色块：rgba(234,217,181,0.02-0.07)
```

### 禁止事项

```xml
<!-- ❌ SVG 属性中不能用 CSS 变量 -->
<text fill="var(--accent)">

<!-- ❌ 用 opacity 叠加压暗颜色（深色背景上会变成黑色） -->
<text fill="#C49736" opacity="0.2">

<!-- ✅ 直接用目标颜色的固定 hex -->
<text fill="#5A4E3A">
```

## 效果工具箱

### 渐变（Gradient）

**线性渐变 — 时间线/进度条**：
```xml
<defs>
  <linearGradient id="timelineGrad" x1="0" y1="0" x2="0" y2="1">
    <stop offset="0%" stop-color="#3A3020"/>
    <stop offset="80%" stop-color="#C49736"/>
  </linearGradient>
</defs>
<line stroke="url(#timelineGrad)" stroke-width="2"/>
```

**径向渐变 — 光晕/焦点**：
```xml
<defs>
  <radialGradient id="glow" cx="50%" cy="50%" r="50%">
    <stop offset="0%" stop-color="#C49736" stop-opacity="0.25"/>
    <stop offset="40%" stop-color="#C49736" stop-opacity="0.08"/>
    <stop offset="100%" stop-color="#C49736" stop-opacity="0"/>
  </radialGradient>
</defs>
<circle fill="url(#glow)"/>  <!-- 在实心元素下方叠加，产生柔和光晕 -->
```

### 发光滤镜（Glow Filter）

**柔和发光 — 焦点元素专用**：
```xml
<defs>
  <filter id="softGlow" x="-150%" y="-150%" width="400%" height="400%">
    <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur"/>
    <feMerge>
      <feMergeNode in="blur"/>
      <feMergeNode in="SourceGraphic"/>
    </feMerge>
  </filter>
</defs>
<circle r="8" fill="#C49736" filter="url(#softGlow)"/>
```

**使用原则**：
- 一张图表中最多 **1 个** 元素使用 glow filter
- `stdDeviation` 控制模糊半径：4-6 柔和，8-10 醒目
- filter 的 x/y/width/height 要足够大（-150%, 400%），否则发光被裁切

### 投影（深色背景不推荐）

深色背景上 `feDropShadow` 效果极弱，优先用发光替代。浅色卡片可用：
```xml
<filter id="shadow">
  <feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#000" flood-opacity="0.12"/>
</filter>
```

## 布局模板

### 模板 1：时间线排除法

适用：排除/筛选/过滤逻辑（A 被排除，B 被排除，C 存活）

**结构**：
```
左侧时间线（渐变竖线）
├── ×  Row 1: 被排除项（极淡）    右侧注释
├── ×  Row 2: 被排除项（极淡）    右侧注释
├── ●  Row 3: 存活项（高亮+发光）  右侧注释
↓
结论文字
```

**关键参数**：
- 时间线 x=32，竖线 stroke-width=2
- 排除标记：circle r=8 + 两条交叉线（stroke-width=1.5, linecap=round）
- 存活标记：circle r=8, fill=accent, filter=softGlow
- 行背景：rect 宽 456（520-56-8）, 高 48, rx=8
- 排除行：fill rgba(…,0.02), 文字 fill=#3A3020
- 存活行：fill rgba(accent,0.07), stroke=accent 0.8px, 文字 fill=#EAD9B5
- 行间距：64px（含 48px 行高 + 16px gap）
- 时间线到结论：箭头（line + polygon）

```xml
<!-- 完整示例骨架 -->
<svg viewBox="0 0 520 232" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="tl" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#3A3020"/>
      <stop offset="80%" stop-color="#C49736"/>
    </linearGradient>
    <filter id="glow" x="-150%" y="-150%" width="400%" height="400%">
      <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="b"/>
      <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
    </filter>
    <radialGradient id="rg" cx="50%" cy="50%" r="60%">
      <stop offset="0%" stop-color="{accent}" stop-opacity="0.06"/>
      <stop offset="100%" stop-color="{accent}" stop-opacity="0"/>
    </radialGradient>
  </defs>

  <!-- 时间线 -->
  <line x1="32" y1="16" x2="32" y2="176" stroke="url(#tl)" stroke-width="2" stroke-linecap="round"/>

  <!-- Row: 排除项 (y 中心=32, 96) -->
  <circle cx="32" cy="32" r="8" fill="none" stroke="#3A3020" stroke-width="1.2"/>
  <line x1="27" y1="27" x2="37" y2="37" stroke="#5A4E3A" stroke-width="1.5" stroke-linecap="round"/>
  <line x1="37" y1="27" x2="27" y2="37" stroke="#5A4E3A" stroke-width="1.5" stroke-linecap="round"/>
  <rect x="56" y="8" width="456" height="48" rx="8" fill="rgba(234,217,181,0.02)"/>
  <text x="80" y="37" font-size="20" fill="#3A3020">{排除项名}</text>
  <text x="488" y="37" text-anchor="end" font-size="13" fill="#3A3020">{排除原因}</text>

  <!-- Row: 存活项 (y 中心=160) -->
  <circle cx="32" cy="160" r="8" fill="{accent}" filter="url(#glow)"/>
  <rect x="56" y="136" width="456" height="48" rx="8" fill="rgba({accent-rgb},0.07)" stroke="{accent-dim}" stroke-width="0.8"/>
  <rect x="56" y="136" width="456" height="48" rx="8" fill="url(#rg)"/>
  <text x="80" y="165" font-size="22" fill="{text-bright}" font-weight="600">{存活项名}</text>
  <text x="488" y="165" text-anchor="end" font-size="13" fill="{accent}">{结论标签}</text>

  <!-- 箭头 + 结论 -->
  <line x1="32" y1="184" x2="32" y2="206" stroke="{accent-dim}" stroke-width="1.5" stroke-linecap="round"/>
  <polygon points="28,206 32,214 36,206" fill="{accent-dim}"/>
  <text x="40" y="228" font-size="13" fill="{accent-dim}">{结论}</text>
</svg>
```

### 模板 2：因果流程链

适用：A → B → C → D 的因果关系

**结构**：
```
[概念A] ──→ [概念B] ──→ [概念C] ──→ [结论D]
  注释        注释        注释       （高亮）
```

**关键参数**：
- viewBox 宽 520, 高 120-160
- 节点：rect 宽 110-120, 高 44-48, rx=8
- 节点间箭头：line + polygon, stroke-width=1.5
- 普通节点：fill rgba(accent,0.06), stroke accent-dim
- 焦点节点（最后一个）：fill rgba(accent,0.12), stroke accent, 文字加粗
- 节点间距相等，水平居中排列

```xml
<!-- 4 节点水平流程 -->
<svg viewBox="0 0 520 120" xmlns="http://www.w3.org/2000/svg">
  <!-- 节点 1: x=8 -->
  <rect x="8" y="24" width="108" height="44" rx="8"
        fill="rgba({accent-rgb},0.06)" stroke="{accent-dim}" stroke-width="1"/>
  <text x="62" y="50" text-anchor="middle" font-size="15" fill="{text}">{A}</text>

  <!-- 箭头 1→2 -->
  <line x1="118" y1="46" x2="136" y2="46" stroke="{accent-dim}" stroke-width="1.5"/>
  <polygon points="136,42 144,46 136,50" fill="{accent-dim}"/>

  <!-- 节点 2: x=146 -->
  <rect x="146" y="24" width="108" height="44" rx="8"
        fill="rgba({accent-rgb},0.06)" stroke="{accent-dim}" stroke-width="1"/>
  <text x="200" y="50" text-anchor="middle" font-size="15" fill="{text}">{B}</text>

  <!-- ...更多节点... -->

  <!-- 底部注释 (可选) -->
  <text x="62" y="88" text-anchor="middle" font-size="11" fill="{muted}">{注释A}</text>
</svg>
```

### 模板 3：对比/并列

适用：两个概念的对比、Before/After

**结构**：
```
┌─────────────┐    ┌─────────────┐
│   概念 A     │ vs │   概念 B     │
│   描述...    │    │   描述...    │
└─────────────┘    └─────────────┘
```

**关键参数**：
- 两列各 240px 宽，中间 40px 间距
- 每列：rect 240×80-120, rx=8
- 左列：淡化处理（Before/旧/弱）
- 右列：高亮处理（After/新/强）
- 中间分隔：文字 "→" 或 "vs"，font-size 16, fill muted

### 模板 4：环形/引力场

适用：核心-外围关系、引力场隐喻

**结构**：
```
        ○ 外围概念（淡）
  ───○───
 /    ●    \     ● = 核心（发光）
  ───○───
        ○ 外围概念（淡）
```

**注意**：轨道型图表容易显得空洞。仅在内容本身就是「核心-外围」关系时使用，否则优先用模板 1 或 2。

**关键参数**：
- 同心圆轨道：stroke-width 0.5-0.8, opacity 0.08-0.15
- 中心圆：实心 fill + radialGradient 光晕 + softGlow filter
- 外围元素：极淡文字（fill 接近背景色），暗示「远离/不重要」
- 轨道不用 stroke-dasharray（实线比虚线更精致）

## 深色背景专用规则

1. **对比度底线**：
   - 大字（≥24px）：对比度 ≥ 3:1（WCAG AA 大字标准）
   - 正文/注释（<24px）：对比度 ≥ 4.5:1（WCAG AA 标准）

2. **glow 优于 shadow**：深色背景上投影不可见，用发光滤镜

3. **线条用低 opacity 而非深色**：
   ```xml
   <!-- ✅ 深色背景上的细线 -->
   <line stroke="#C49736" stroke-width="0.5" opacity="0.1"/>

   <!-- ❌ 太暗看不见 -->
   <line stroke="#1A1A1A" stroke-width="1"/>
   ```

4. **背景色块用极低透明度**：`rgba(亮色, 0.02-0.07)`，不要用 `rgba(0,0,0,x)`

## 质量检查清单

生成 SVG 后，逐条检查：

- [ ] 所有尺寸对齐 4/8px 网格
- [ ] 颜色不超过 5 个（含黑白）
- [ ] 最小文字 ≥ 11px
- [ ] 只有 1 个视觉焦点（高亮/发光元素）
- [ ] 没有装饰性元素（装饰线、阴影、渐变仅服务于信息传递）
- [ ] 图表高度不超过卡片总高 15%
- [ ] 深色卡片：焦点元素对比度 ≥ 3:1
- [ ] hex 颜色恰好 6 位
- [ ] 未使用 `var(--xxx)`（SVG 属性不解析 CSS 变量）
- [ ] 未使用 `opacity` 叠加方式压暗颜色

## 反模式

| 业余做法 | 专业替代 |
|---------|---------|
| 方框 + 粗箭头 + 密密麻麻的文字 | 大文字 + 留白 + 单色调 |
| 每个元素都加边框 | 只给焦点元素加描边，其余用色块区分 |
| 虚线轨道 + 散乱的浮动文字 | 实线轨道（极淡）+ 对齐的标签 |
| 5 种以上颜色「好看」 | 2-3 个色阶 + 透明度层级 |
| 对称的装饰性圆环 | 不对称布局，焦点偏移 |
| 给背景加网格/点阵装饰 | 纯净背景，信息区自带层次 |
| 线条粗细不一（1px, 2px, 3px 混用） | 统一 stroke-width（0.8 or 1.5），仅焦点可加粗 |

## 线条落在实处（借鉴 wheresryan22/anatomy，MIT）

- 每条线的两端都要落在东西上：一个形状的边、另一条线，或一个端点圆点（r 2–3）。悬在半空的线头是未完成，不是“留白”。
- 不画引线、气泡标注和指向空白的箭头；需要说明时把文字放在图形外侧对齐的位置。
- 纹理（刻度、分隔线、网格）要么从边到边，要么渐隐到 0，不在面中间突然停止。
- 截图后 4× 放大检查线端和交界处。
