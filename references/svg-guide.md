# SVG 绘图指南 / SVG Authoring Guide

> 配合 `SKILL.md` 的硬性规范使用。本册解决"怎么画"，配色语义见 `colors.md`，画什么结构见 `arch-patterns.md`。

## 1. 画布与坐标系

### 骨架

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 920 620"
     role="img" aria-labelledby="title desc">
  <title id="title">图表标题</title>
  <desc id="desc">一句话描述图的内容与结构</desc>
  <style>/* 见 §6 */</style>
  <rect class="canvas" x="0" y="0" width="920" height="620"/>
  <!-- 内容 -->
</svg>
```

- `viewBox` 让图随容器缩放；文档嵌入场景不要写死 `width/height`
- 画布四周留 ≥40px 边距，标题区高约 48px
- 尺寸建议：流程 / 分层架构 920×620 起步，序列图按参与方数量定宽（每方 ≥180px）

### 网格与对齐

- 以 8px 为基准网格：所有坐标尽量落在 8 的倍数上
- 组件间距 ≥40px；同层组件**等宽等高**，中心 x 对齐
- 层带（band）布局：每层一个淡色底框，层间距 16~24px，层标签放层框左上角
- 多列布局中心点公式：`center_i = margin + col_w/2 + i * (col_w + gap)`

## 2. 基本形状与语义

### 服务组件（圆角矩形）

```xml
<g class="node compute">
  <rect x="120" y="312" width="200" height="72" rx="8"/>
  <text class="t-title" x="220" y="344">订单服务</text>
  <text class="t-sub"   x="220" y="366">下单 · 履约</text>
</g>
```

- `rx="8"` 统一圆角；两行文本时主标题 14px bold、副标题 12px
- 文本永远 `text-anchor="middle"`，x 取组件中心，避免手算左对齐

### 数据库（圆柱体）

```xml
<g class="node data">
  <path class="cyl-body" d="M 160 491 v 39 a 60 13 0 0 0 120 0 v -39"/>
  <ellipse class="cyl-top" cx="220" cy="491" rx="60" ry="13"/>
  <text class="t-title" x="220" y="521">订单库</text>
</g>
```

- 顶盖椭圆单独一层浅色，主体 path：下移 → 底弧 → 回上
- 占位约 120×66；标签放柱身垂直居中

### 外部系统（六边形）

```xml
<polygon class="node external" points="310,96 330,84 390,84 410,96 390,108 330,108"/>
```

### 边界 / 信任域（虚线组框）

```xml
<rect class="boundary" x="40" y="274" width="840" height="162" rx="10"/>
<text class="band-label" x="52" y="292">业务服务层 Services</text>
```

- 组框用中性灰 + dashed 描边，透明度低于内部组件，表示 VPC、集群、信任边界等

## 3. 连接线

### 箭头 marker（统一定义一次）

```xml
<defs>
  <marker id="arrow" markerWidth="10" markerHeight="8" refX="8" refY="4"
          orient="auto" markerUnits="userSpaceOnUse">
    <path d="M0,0 L8,4 L0,8 Z" fill="#475569"/>
  </marker>
  <marker id="arrow-open" markerWidth="10" markerHeight="8" refX="8" refY="4"
          orient="auto" markerUnits="userSpaceOnUse">
    <path d="M0,0 L8,4 L0,8 Z" fill="#FFFFFF" stroke="#475569" stroke-width="1"/>
  </marker>
</defs>
```

- 实心箭头 = 请求 / 调用；空心箭头 = 返回值（序列图返回线）
- 线终点停在目标边界外 2~4px，别让箭头压进填充区

### 三种路由

```xml
<!-- 直线：水平 / 垂直相邻 -->
<line class="conn" x1="190" y1="125" x2="226" y2="125"/>

<!-- 正交折线：跨层 / 绕行，肘部留在空白带 -->
<path class="conn" d="M 460 244 V 290 H 220 V 308"/>

<!-- 贝塞尔：无向关联或示意性流动 -->
<path class="conn" d="M 200 100 C 260 100, 260 160, 320 160"/>
```

- 折线拐点选在两组件之间的空白带（如层间距中点），不穿组件
- 多条线并行时留 12px 以上间距

### 线型语义

| 线型 | class | 含义 |
|---|---|---|
| 实线 | `conn` | 同步调用、强依赖 |
| 虚线 `stroke-dasharray="6 4"` | `conn-async` | 异步消息、事件、返回 |
| 点线 `stroke-dasharray="2 4"` | `conn-ctrl` | 控制流、配置下发 |

### 线标签

```xml
<g class="edge-label">
  <rect x="258" y="106" width="90" height="18" rx="3"/>
  <text x="303" y="119">publish</text>
</g>
```

- 白底小矩形托底 + 居中文本，压在线的中点上方，避免文字与线打架
- 动词或消息名（`publish`、`SELECT`、`consume`），不写整句

## 4. 文本

```xml
<text class="t-title" x="220" y="344">订单服务</text>

<text class="t-body" x="220" y="100">
  <tspan x="220" dy="0">第一行</tspan>
  <tspan x="220" dy="18">第二行</tspan>
</text>
```

- 层级：图标题 18px bold → 组件主标题 14px bold → 副标题 / 线标签 12px
- 中文宽度估算：约等于字号（14px 字号 ≈ 14px/字）；判断文本是否放得下先估宽
- `dominant-baseline` 在部分渲染器表现不一，垂直定位直接用 `y` 微调
- **不要用 `<foreignObject>`**：部分查看器和导出工具不支持，纯 `<text>` + `<tspan>` 最稳

## 5. 样式与复用

### CSS 类集中定义

```xml
<style>
  .compute { fill:#DBEAFE; stroke:#2563EB; stroke-width:1.5; }
  .data    { fill:#DCFCE7; stroke:#16A34A; stroke-width:1.5; }
  .infra   { fill:#F3F4F6; stroke:#4B5563; stroke-width:1.5; }
  .t-title { font: bold 14px system-ui, -apple-system, "PingFang SC",
             "Microsoft YaHei", sans-serif; fill:#1E293B; text-anchor:middle; }
  .t-sub   { font: 12px system-ui, sans-serif; fill:#64748B; text-anchor:middle; }
  .conn    { stroke:#64748B; stroke-width:1.8; fill:none;
             marker-end:url(#arrow); }
</style>
```

- `<font shorthand>` 或 `font-family/font-size` 分写均可，全图统一即可
- 复用组件用 `<defs><g id="...">` + `<use href="#id" x=".." y=".."/>`

### 渐变与阴影（克制）

```xml
<defs>
  <filter id="soft" x="-20%" y="-20%" width="140%" height="140%">
    <feDropShadow dx="1.5" dy="1.5" stdDeviation="1.5" flood-opacity="0.15"/>
  </filter>
</defs>
```

- 技术文档图默认**不用**渐变和阴影；仅演示场景给关键节点加 `filter="url(#soft)"`
- 3D / 拟物效果不要主动使用

## 6. 常用小图标

```xml
<!-- 云 / Cloud -->
<path d="M50 58 q10 -16 28 -8 q12 -14 28 -2 q16 2 14 16 q10 8 -4 12 l-60 0
         q-14 -2 -6 -18z" class="icon"/>

<!-- 锁 / Security -->
<rect x="40" y="52" width="36" height="26" rx="4" class="icon"/>
<path d="M48 52 v-8 a10 10 0 0 1 20 0 v8" fill="none" class="icon-line"/>

<!-- 服务器 / Server -->
<rect x="40" y="34" width="56" height="64" rx="4" class="icon"/>
<circle cx="50" cy="48" r="2.5"/><circle cx="50" cy="66" r="2.5"/>

<!-- 用户 / User -->
<circle cx="60" cy="44" r="9" class="icon"/>
<path d="M44 76 a16 14 0 0 1 32 0" class="icon"/>
```

图标是辅助：同一张图里要么都用小图标，要么都不用，不要混搭。

## 7. 图例（Legend）

图中语义种类 >3 时必须带图例：

```xml
<g id="legend">
  <rect class="swatch compute" x="40" y="592" width="14" height="14"/>
  <text class="legend-text" x="60" y="603">计算 / API</text>
  <!-- 横向重复：色块 + 文本为一组，组间距 120~150px；第二行放线型示例 -->
  <line class="conn" x1="40" y1="624" x2="80" y2="624"/>
  <text class="legend-text" x="88" y="628">同步</text>
</g>
```

## 8. 导出与互转

- **PNG**：`rsvg-convert -w 1600 in.svg -o out.png`，或 `inkscape in.svg -w 1600 -o out.png`；macOS 临时可用 `qlmanage -t -s 1600 in.svg`
- **Mermaid**：用户只要文本版时，按图结构重写为 `flowchart` / `sequenceDiagram` / `stateDiagram-v2`，不要试图机器逐元素转换
- SVG → PNG 前确认无 `<foreignObject>`（本指南已禁用）

## 9. 交付前自检清单

- [ ] `viewBox`、`<title>`、`<desc>`、`role="img"` 齐全
- [ ] 打开文件目测：无文本截断 / 重叠，连线不穿组件
- [ ] 同层组件等宽等高、间距一致，坐标落在 8px 网格
- [ ] 箭头方向正确，返回线用空心箭头
- [ ] 语义色符合 `colors.md`，图例齐全
- [ ] 文件为单个自包含 SVG（无外链图片、无脚本）
