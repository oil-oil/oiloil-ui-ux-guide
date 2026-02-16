# oiloil-ui-ux-guide

一个面向现代简洁界面的 Codex Skill。  
它把 UI/UX 建议变成可执行改法，重点解决两类问题：

- 评审结论过于空泛，难落地到代码
- 页面提示越加越多，最后变成“提示墙”

## 适用场景

- 新功能上线前，先拿一套稳定规则做设计把关
- 已有页面体验不佳，需要按优先级做改造
- 设计评审时，希望输出能直接进入开发任务

## 这个 Skill 能做什么

这个 Skill 有两种模式：

- `guide`：给出简洁、可执行的“该做 / 不该做”规则
- `review`：评审现有界面并输出 `P0 / P1 / P2` 修复清单

核心能力包括：

- 任务优先：主任务与主操作在 3 秒内可识别
- 提示分层：只保留必要信息，减少长期平铺文案
- 状态闭环：覆盖加载 / 空 / 错误 / 成功 / 权限
- 诊断方法：区分执行鸿沟、评估鸿沟、Slip 与 Mistake
- 视觉规范：强调 CRAP（对比/重复/对齐/亲密性）与间距规整
- 图标规范：禁止 emoji 图标，要求统一图标集

## 跨工具支持（Codex / Claude Code / Cursor / Windsurf）

结论先说：目前没有一个被所有工具完全统一的“单文件标准”。  
实践里最稳的做法是：

- 用 `AGENTS.md` 作为跨工具通用指令入口（单一真源）
- 用各工具的适配文件做轻量桥接（例如 `CLAUDE.md`、`.cursor/rules/*.mdc`）
- 保留 `SKILL.md` 作为本 Skill 的行为定义

当前仓库已按这个方式配置。

## 各工具如何使用

- Codex：原生 Skill 工作流，读取 `SKILL.md` 与 `agents/openai.yaml`
- Claude Code：项目内读取 `CLAUDE.md`（已桥接到 `AGENTS.md` + `SKILL.md`）
- Cursor：可读取 `AGENTS.md`，并支持 `.cursor/rules/*.mdc`
- Windsurf：支持 `AGENTS.md` 作为项目级代理指令

## 用 `skills` CLI 一键安装到多个 Agent（推荐）

你可以直接使用 [vercel-labs/skills](https://github.com/vercel-labs/skills) 的 CLI，把这个 Skill 安装到多个 Agent。

### 先列出仓库里的可安装 Skill

```bash
npx skills add oil-oil/oiloil-ui-ux-guide --list
```

### 一次安装到多个 Agent

```bash
npx skills add oil-oil/oiloil-ui-ux-guide \
  -a codex \
  -a claude-code \
  -a cursor \
  -a windsurf
```

### 全局安装（跨项目可用）

```bash
npx skills add oil-oil/oiloil-ui-ux-guide \
  -g \
  -a codex \
  -a claude-code \
  -a cursor \
  -a windsurf
```

说明：

- `-a` 指定目标 Agent
- `-g` 安装到用户目录（全局）
- 如果你只想安装这个仓库内某个 Skill，可加 `--skill <name>`

## 安装与配置（Codex）

### 方式 A：通过 GitHub 安装（推荐）

在 Codex 环境执行：

```bash
scripts/install-skill-from-github.py --repo oil-oil/oiloil-ui-ux-guide --path .
```

### 方式 B：手动安装

将仓库复制到：

```bash
~/.codex/skills/oiloil-ui-ux-guide
```

建议目录结构：

```text
~/.codex/skills/oiloil-ui-ux-guide/
  AGENTS.md
  CLAUDE.md
  .cursor/rules/oiloil-ui-ux-guide.mdc
  agents/openai.yaml
  index.html
  skills/
    oiloil-ui-ux-guide/
      SKILL.md
      references/
```

安装后重启 Codex，使新 Skill 生效。

> 如果你已经使用上面的 `npx skills add` 安装到 Codex，可以跳过本节。

## 让不同 AI 正确触发这个 Skill

你可以用两种方式触发：

1. 显式点名 Skill
   - `请使用 $oiloil-ui-ux-guide 评审这个设置页。`
2. 直接描述匹配任务
   - “帮我评审这个仪表盘，按 P0/P1/P2 给修复建议。”
   - “给我这个创建流程的简洁 UX 规则，重点防止提示堆叠。”

建议在 Claude/Cursor/Windsurf 里也显式带上 `oiloil-ui-ux-guide`，这样触发更稳定。

## 推荐提示词模板

### 模板一：review（评审现有界面）

```text
请使用 $oiloil-ui-ux-guide 的 review 模式。
背景：Web 管理后台，目标用户为首次完成配置的新用户。
目标：提升首次配置完成率，减少误操作。
请输出：
1) 关键假设
2) P0/P1/P2 问题清单（附简短证据）
3) 每个问题的可执行修复方案（布局/组件/文案/状态）
4) 验收检查点
```

### 模板二：guide（先出规则再设计）

```text
请使用 $oiloil-ui-ux-guide 的 guide 模式。
页面类型：B 端设置页。
请输出简洁的“该做 / 不该做”规则，并覆盖：
- 主任务与主操作层级
- 提示分层
- 状态闭环
- 间距/重复/规整
要求：避免长段落，尽量用要点。
```

## 输出风格（预期结果）

- 结论短、重点清楚，不写空泛套话
- 每条建议都能落到界面与代码层面
- 优先级明确，便于排期执行
- 末尾有验收点，方便验证改造是否有效

## 仓库结构

- `AGENTS.md`：跨工具共享指令（推荐作为单一真源）
- `CLAUDE.md`：Claude Code 入口（桥接到共享指令）
- `.cursor/rules/oiloil-ui-ux-guide.mdc`：Cursor 规则入口（桥接到共享指令）
- `agents/openai.yaml`：Skill 展示信息与默认提示
- `index.html`：Skill 介绍与可视化示例页面
- `skills/oiloil-ui-ux-guide/SKILL.md`：Skill 主规则与工作流
- `skills/oiloil-ui-ux-guide/references/system-principles.md`：系统级指导原则
- `skills/oiloil-ui-ux-guide/references/interaction-psychology.md`：交互心理学（HCI 定律、认知偏差、交互节奏、注意力经济）
- `skills/oiloil-ui-ux-guide/references/design-psych.md`：设计心理学诊断词汇
- `skills/oiloil-ui-ux-guide/references/icons.md`：图标一致性与可理解性规则
- `skills/oiloil-ui-ux-guide/references/review-template.md`：标准评审输出模板
- `skills/oiloil-ui-ux-guide/references/checklists.md`：扩展检查清单

## 参考文档

- skills CLI（跨 Agent 分发）：<https://github.com/vercel-labs/skills>
- Claude Code 记忆机制（`CLAUDE.md`）：<https://docs.anthropic.com/en/docs/claude-code/memory>
- Cursor 规则与 `AGENTS.md`：<https://docs.cursor.com/context/rules-for-ai>
- Windsurf `AGENTS.md` 支持：<https://docs.windsurf.com/windsurf/cascade/memories>

## 许可证

本项目采用 Apache License 2.0，详见 `LICENSE.txt`。
