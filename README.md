# Codex Memory Routing Skills

一组面向 Codex / Claude Code 工作流的轻量技能，目标不是做“全自动黑盒记忆”，而是提供一套可审计、可版本化、可回退的项目记忆与任务路由机制。

当前仓库包含两个技能：

- [`rules-router`](./skills/rules-router/SKILL.md)
- [`project-memory-init`](./skills/project-memory-init/SKILL.md)

## 为什么做这两个技能

很多项目并不需要复杂的项目记忆结构，但几乎所有项目都需要“开始做事前先判断读什么、跳过什么、下一步交给谁”。

这套技能把问题拆成两层：

- `rules-router`
  - 负责任务起手式路由
  - 判断复杂度、意图、上下文预算、是否启用项目记忆模式
  - 优先按 capability 路由，再解析成已安装 skill 或内置 fallback

- `project-memory-init`
  - 负责项目记忆结构本身
  - 处理初始化、修复、恢复、结构审计
  - 不替代普通任务路由

这套设计和自动记忆方案不是同一个方向。它更强调：

- 可控
- 可解释
- 可回溯
- 可以按项目决定是否启用

## 设计原则

- 默认最小上下文，不预加载全部文档
- 没有项目记忆结构时，只启用 generic routing
- capability 优先，skill 名其次
- 缺少 skill 时不阻塞任务，退回 built-in fallback
- task state、lessons、knowledge、rules 明确分层

## 仓库结构

```text
codex-memory-routing-skills/
├── README.md
├── PUBLISHING.md
└── skills/
    ├── project-memory-init/
    │   └── SKILL.md
    └── rules-router/
        └── SKILL.md
```

## 技能关系

### `rules-router`

适合这些场景：

- 进入任务前，不确定该加载哪些规则或项目文档
- 需要判断是 coding、bugfix、review、docs 还是 brainstorming
- 需要判断项目是否已经初始化记忆结构
- 用户说“记住这个”“以后按这个来”，需要判断写回位置
- 某个偏好 skill 没安装，仍要继续完成路由

它有两种模式：

- `generic mode`
  - 项目尚未建立记忆分层时使用
- `memory mode`
  - 只有在 `AGENTS.md`、`tasks/index.md`、`tasks/todo.md`、`tasks/lessons.md`、`tasks/knowledge.md` 都齐备时才启用

### `project-memory-init`

适合这些场景：

- 第一次进入一个仓库
- 项目记忆结构缺失或混乱
- 长时间后回到项目，需要恢复上下文
- 想把经验、任务状态、长期知识分层清楚

它包含四种模式：

- `scan`
- `bootstrap`
- `resume`
- `repair`

## 安装方式

把 `skills/` 下的技能目录复制到你的 skill 搜索路径中即可。常见位置例如：

- `~/.agents/skills/`
- `~/.codex/skills/`

示例：

```powershell
Copy-Item .\skills\rules-router "$HOME\.agents\skills\rules-router" -Recurse -Force
Copy-Item .\skills\project-memory-init "$HOME\.agents\skills\project-memory-init" -Recurse -Force
```

如果你使用的是其他 skill 目录约定，把两个子目录放到对应位置即可。

## 推荐使用顺序

1. 新项目或陌生仓库：
   先用 `rules-router` 判断是否只需 generic routing，或者是否要手动运行 `project-memory-init`
2. 项目尚未初始化记忆结构：
   使用 `project-memory-init` 的 `scan` 或 `bootstrap`
3. 项目结构已经稳定：
   由 `rules-router` 接管后续日常路由
4. 项目中途出现规则冲突、知识和任务状态混写：
   使用 `project-memory-init` 的 `repair`

## 写回分层约定

两个技能共享同一套写回分类：

- `session-only`
- `tasks/todo.md`
- `tasks/lessons.md`
- `tasks/knowledge.md`
- `AGENTS.md` 或稳定项目规则，如 `docs/rules.md`

核心原则：

- 事实进 `tasks/knowledge.md`
- 行为约束进 `tasks/lessons.md` 或稳定规则
- 始终生效的工作方式进 `AGENTS.md` 或稳定规则
- 任务状态只进 `tasks/todo.md`

## 适合谁

这套技能更适合：

- 有明确协作规范的工程团队
- 需要长期维护项目规则、组件规范、质量门禁的仓库
- 更看重“为什么读这些上下文”和“为什么写到这里”的工作流

如果你追求的是全自动、无感知、语义检索优先的记忆系统，这套技能可能不是最省事的选择。

## 反馈与迭代

这两个技能是按真实开发使用场景设计的，最有价值的反馈通常来自：

- 哪些路由判断太重了
- 哪些写回分类还容易混淆
- 哪些 fallback 规则不够顺手
- 哪些项目其实不需要启用 memory mode

欢迎基于实际使用继续迭代。
