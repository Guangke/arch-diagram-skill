# arch-diagram-skill

**arch-diagram** 是一个架构图绘制 Agent Skill：把自然语言需求变成一张单文件、可手改、语义配色的 **SVG 架构图**。适用于 Claude Code、OpenAI Codex CLI 及任何兼容 [Agent Skills](https://agentskills.io/) 开放格式（`SKILL.md`）的 Agent。

**arch-diagram** is an Agent Skill that turns plain-language requirements into a single-file, hand-editable, semantically colored **SVG architecture diagram**. Works with Claude Code, OpenAI Codex CLI, and any agent that supports the open [Agent Skills](https://agentskills.io/) format (`SKILL.md`).

## 功能 / What it does

- **五类图表**：系统架构（微服务 / 云原生 / K8s）、数据与存储（DFD / ER / ETL 管道）、网络与基础设施（拓扑 / VPC / CDN）、行为与交互（序列 / 状态 / 活动图）、概念与组织结构
- **完整工作流**：需求澄清 → 信息收集 → 结构设计 → 布局草图 → SVG 生成 → 自检迭代
- **语义配色系统**：蓝 = 计算、绿 = 数据、橙 = 外部、灰 = 基础设施、红 = 安全、紫 = 可观测；亮 / 暗两套主题
- **SVG 规范**：响应式 `viewBox`、可访问性（`title` / `desc` / `role`）、CSS 类集中样式、8px 网格对齐、线型语义（实线同步 / 虚线异步 / 点线控制流）
- **13 种架构模式速查**：分层微服务、事件驱动、CQRS、K8s、服务网格、ETL、Lambda、缓存、VPC、CDN、混合云、OAuth 等
- Five diagram families, a full authoring workflow, a semantic color system, SVG hard rules, and a 13-pattern architecture cheat-sheet.

## 安装 / Install

仓库根目录就是 skill 本体，克隆到对应 Agent 的全局 skills 目录即可（多出的 README / LICENSE 不影响加载）：

The repo root **is** the skill — clone it into your agent's global skills directory (the extra README / LICENSE files don't affect loading):

### Claude Code

```bash
git clone https://github.com/Guangke/arch-diagram-skill.git ~/.claude/skills/arch-diagram
```

### Codex CLI

```bash
git clone https://github.com/Guangke/arch-diagram-skill.git ~/.codex/skills/arch-diagram
```

### 项目级 / Per-project

```bash
# Claude Code
git clone https://github.com/Guangke/arch-diagram-skill.git .claude/skills/arch-diagram
# Codex CLI
git clone https://github.com/Guangke/arch-diagram-skill.git .codex/skills/arch-diagram
```

安装后重启会话（Claude Code 重开会话；Codex 重新启动）即可被发现。更新：进入目录 `git pull`。

Restart the session after installing. To update, `git pull` inside the directory.

## 使用 / Usage

自然语言直接触发，例如：

Just ask in natural language:

- "画一张电商微服务架构图，包含网关、订单、支付和各自的数据库"
- "把这段部署描述画成 VPC 网络拓扑：公有子网放 ALB，私有子网放应用和 RDS"
- "画用户登录的时序图，走 OAuth 授权码流程"
- "Draw the data pipeline from Kafka ingestion to the warehouse serving layer"
- "Convert this service list into an architecture diagram as SVG"

输出：单个自包含 `.svg` 文件（需要时指导 Agent 转 PNG 或 Mermaid，方法内置在 `references/svg-guide.md`）。

Output: one self-contained `.svg` file (PNG / Mermaid conversion guidance is built into `references/svg-guide.md`).

## 目录结构 / Structure

```
arch-diagram-skill/
├── SKILL.md                        # skill 入口：图表路由、工作流、硬性规范
├── references/
│   ├── svg-guide.md                # SVG 技法：形状、连线、文本、复用、图标、导出
│   ├── colors.md                   # 语义色板（亮 / 暗）、对比度、线型语义
│   └── arch-patterns.md            # 13 种常见架构模式速查
├── templates/
│   ├── microservices.svg           # 分层微服务架构模板
│   ├── sequence.svg                # 序列图模板
│   └── dataflow.svg                # 数据管道模板
└── examples/
    └── event-driven.svg            # 事件驱动架构完整示例
```

## 自定义 / Customize

- 换配色：改 `references/colors.md` 的色值表即可，模板与规范按类名引用
- 加模式：在 `references/arch-patterns.md` 追加"组件清单 + 连接关系 + 布局要点"三段式条目
- Rebrand colors by editing `references/colors.md`; add patterns to `references/arch-patterns.md`.

## License

[MIT](LICENSE)
