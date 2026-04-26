# Agent Memory Router

[English](./README.md) | [简体中文](./README.zh-CN.md)

一组面向 Codex / Claude Code 风格工作流的轻量技能，用来把“任务路由”和“项目记忆结构”拆开处理。

这个仓库的目标不是做一个全自动记忆系统，而是提供一套：

- 可审计
- 可版本化
- 可预测
- 可渐进接入

的项目记忆与路由方案。

## 包含的技能

- [`rules-router`](./skills/rules-router/SKILL.md)
- [`project-memory-init`](./skills/project-memory-init/SKILL.md)

## 为什么做这两个技能

很多项目并不需要完整的项目记忆体系，但几乎所有项目都需要一个稳定的起手式，来决定：

- 现在该读什么上下文
- 什么可以先跳过
- 下一步应该交给哪个工作流
- 新的信息应该写回到哪里

这个仓库把问题拆成两层：

- `rules-router`
  - 在执行前做任务路由
  - 判断意图、复杂度、上下文预算、项目记忆是否 ready
  - 先路由到 capability，再映射到已安装 skill 或内置 fallback

- `project-memory-init`
  - 负责初始化、修复、恢复、审计项目记忆结构
  - 帮助区分任务状态、经验教训、长期知识和稳定规则
  - 不替代日常任务路由

## 核心设计原则

- 只加载支持下一步所需的最小上下文
- 不假设每个项目都需要 memory-aware routing
- 优先按 capability 路由，而不是写死 skill 名
- 首选 skill 未安装时，能够平滑退回 fallback
- 让 task state、lessons、knowledge、rules 保持分层

## 仓库结构

```text
agent-memory-router/
├── LICENSE
├── PUBLISHING.md
├── README.md
├── README.zh-CN.md
└── skills/
    ├── project-memory-init/
    │   └── SKILL.md
    └── rules-router/
        └── SKILL.md
```

## 两个技能如何协作

### `rules-router`

适合处理这些问题：

- 开始任务前，到底要读哪些规则或文档
- 当前任务是 coding、bugfix、review、docs 还是 brainstorming
- 项目记忆结构是否已经完整到可以进入 memory mode
- 一个纠正、经验、规则或长期事实该写回哪里
- 某个偏好 skill 没安装时，如何继续推进

它有两种模式：

- `generic mode`
  - 项目还没有建立预期记忆结构时使用
- `memory mode`
  - 只有当项目具备 `AGENTS.md`、`tasks/index.md`、`tasks/todo.md`、`tasks/lessons.md`、`tasks/knowledge.md` 时才启用

### `project-memory-init`

适合在“记忆结构本身就是问题”的场景使用：

- 第一次进入仓库
- 项目记忆缺失或不可靠
- 丢失上下文后恢复工作
- 修复 rules、task state、lessons、knowledge 混写的问题

它支持四种模式：

- `scan`
- `bootstrap`
- `resume`
- `repair`

## 安装方式

把两个 skill 目录复制到本地 skill 搜索路径即可。

常见位置例如：

- `~/.agents/skills/`
- `~/.codex/skills/`

示例：

```powershell
Copy-Item .\skills\rules-router "$HOME\.agents\skills\rules-router" -Recurse -Force
Copy-Item .\skills\project-memory-init "$HOME\.agents\skills\project-memory-init" -Recurse -Force
```

如果你的环境使用其他目录约定，把这两个子目录复制到对应位置即可。

## 推荐使用顺序

1. 进入一个新的或陌生的仓库
   - 先用 `rules-router`
   - 判断 `generic mode` 是否足够，或者是否需要手动运行 `project-memory-init`
2. 项目还没有建立记忆结构
   - 用 `project-memory-init` 的 `scan` 或 `bootstrap`
3. 项目结构已经稳定
   - 让 `rules-router` 接管日常路由
4. 结构开始漂移
   - 用 `project-memory-init` 的 `repair`

## 共享的写回分类

两个技能共享同一套写回分类：

- `session-only`
- `tasks/todo.md`
- `tasks/lessons.md`
- `tasks/knowledge.md`
- `AGENTS.md` 或稳定项目规则，例如 `docs/rules.md`

核心约定：

- 事实写进 `tasks/knowledge.md`
- 行为约束写进 `tasks/lessons.md` 或稳定规则
- always-on 的工作方式写进 `AGENTS.md` 或稳定规则
- 任务局部状态只写进 `tasks/todo.md`

## 适合谁

这个仓库更适合：

- 有明确协作规范的工程团队
- 长期维护组件规范、质量门禁或共享流程的项目
- 关心“为什么加载这个上下文”和“为什么写回到这里”的使用者

如果你更想要一个全自动、语义检索优先、尽量减少人工参与的记忆层，这个仓库并不是为那个方向优化的。

## 最值得反馈的点

真实使用中最有价值的反馈通常是：

- 路由是否太重
- 写回分类是否仍然容易混淆
- fallback 是否还不够顺手
- 哪些项目应该更久地停留在 `generic mode`
- lesson 升级为 rule 的标准是否过松或过严

## 许可证

本仓库使用 [MIT License](./LICENSE)。
