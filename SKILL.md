---
name: arch-diagram
description: >-
  Draws professional technical diagrams as clean, hand-editable, semantically
  colored SVG files. Covers system / microservice / cloud-native architecture,
  network topology (VPC, CDN, hybrid cloud), data flow and ETL / lineage,
  sequence / state / activity diagrams, ER diagrams, and org or concept maps,
  following a requirements-analysis-to-iteration workflow. Use when the user
  asks to draw, design, or visualize system architecture, infrastructure,
  workflows, or data pipelines, or to turn a text description into a diagram.
  关键词：架构图、微服务、云原生、网络拓扑、数据流图、流程图、时序图、序列图、状态机、ER 图、组织结构图、系统设计。
license: MIT
metadata:
  version: 1.0.0
---

# Arch Diagram / 架构图绘制专家

把自然语言需求变成一张**单文件、可手改、语义配色**的 SVG 架构图。默认交付 `.svg`；需要 PNG 或 Mermaid 时参考 `references/svg-guide.md` 的导出章节。

## 图表类型路由 / Diagram Router

| 类型 | 适用场景 |
|---|---|
| 系统架构 | 微服务 / 单体 / 分布式、服务依赖与通信、云原生与 K8s |
| 数据与存储 | 数据流图（DFD）、ER 图、ETL/ELT 管道、缓存架构、数据血缘 |
| 网络与基础设施 | 网络拓扑、VPC / 子网 / 安全组、CDN、混合云 |
| 行为与交互 | 序列图、状态机、活动图 / 业务流程图 |
| 概念与组织 | 概念关系图、组织结构图、思维导图 |

## 工作流 / Workflow

1. **澄清需求**：用途（文档 / 演示 / 评审）、受众（技术 / 管理层）、详细程度。信息不足先问，不要猜。
2. **收集信息**：列出组件清单、连接关系、数据流方向、边界（信任域 / 网络域）。若图需反映真实代码，先读仓库证据再落笔。
3. **结构设计**：选图表类型 → 定布局（分层 / 分列 / 环形）→ 划视觉层次，主链路突出、次要元素弱化。常见结构查 `references/arch-patterns.md`。
4. **布局草图**：先用文本列出节点、连线和分组，按网格规划坐标（8px 基准，同层等宽等高、中心对齐）。
5. **生成 SVG**：按下方硬性规范输出完整 SVG，写入文件，命名 `<topic>-<type>.svg`（如 `checkout-architecture.svg`）。
6. **自检与迭代**：对照验收清单逐项检查；用户反馈后只调整需要改的部分，不重画整图。

## SVG 硬性规范 / Hard Rules

- 根元素带 `xmlns` 与 `viewBox`（响应式缩放）；不写死 `width/height`，除非用户要求固定尺寸
- `<title>` 与 `<desc>` 必填（可访问性）；根元素加 `role="img" aria-labelledby="title desc"`
- 文本不小于 12px；字体栈含中文回退：`system-ui, -apple-system, "PingFang SC", "Microsoft YaHei", sans-serif`
- 样式集中在 `<style>` 的 CSS 类中，同类组件复用同一类名，不用散落的内联属性
- 组件间距 ≥40px，同层组件等宽等高；整图按 8px 网格对齐
- 连接线用 `<defs><marker>` 定义箭头；线型语义：同步调用 = 实线，异步 / 事件 = 虚线，控制流 = 点线；线标签放在线段中点上方
- 一张图主色不超过 6 种色相；配色按语义取色，查 `references/colors.md`

## 语义配色速查 / Semantic Colors

| 语义 | 代表组件 | 色系 |
|---|---|---|
| 计算 | API、微服务、函数 | 蓝 |
| 数据 | 数据库、缓存、数据湖 | 绿 |
| 外部 | 第三方 API、SaaS | 橙 |
| 基础设施 | 网络、网关、消息队列 | 灰 |
| 安全 | 鉴权、防火墙、加密 | 红 |
| 可观测 | 日志、监控、告警 | 紫 |

## 交付与验收 / Deliverable & Checklist

输出单个 SVG 文件，交付前逐项自检：

- [ ] 所有文本完整显示，无截断、无相互重叠
- [ ] 箭头方向与数据流 / 调用方向一致，连线不穿过组件主体
- [ ] 同层组件对齐，间距一致
- [ ] 图中标出图例（语义色 + 线型）；图中语义种类 ≤3 时可省略
- [ ] `<title>` / `<desc>` 存在且与内容一致

## 资源文件 / Resources（按需加载，不要一次全读）

| 文件 | 内容 |
|---|---|
| `references/svg-guide.md` | SVG 技法全册：形状、连线、文本、复用、小图标、PNG 导出 |
| `references/colors.md` | 语义色板（亮 / 暗主题）、对比度规则、线型语义 |
| `references/arch-patterns.md` | 13 种常见架构模式：组件清单 + 连接关系 + 布局要点 |
| `templates/microservices.svg` | 分层微服务架构模板（四层带 + 图例） |
| `templates/sequence.svg` | 序列图模板（生命线 + 激活条 + 消息） |
| `templates/dataflow.svg` | 数据管道模板（主流水线 + 可观测旁路） |
| `examples/event-driven.svg` | 事件驱动架构完整示例 |
