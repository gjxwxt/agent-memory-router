# Publishing Guide

这个仓库适合以“纯技能仓库”的方式发布到 GitHub，不依赖额外脚本、数据库或 hooks。

## 发布前检查

发布前建议确认下面几项：

1. 两个技能的 `SKILL.md` 都能独立阅读，不依赖你本地私有路径中的额外文档
2. `rules-router` 和 `project-memory-init` 的职责边界清楚，没有互相抢活
3. 仓库级 `README.md` 已说明安装方式、使用顺序、设计边界
4. 如果准备开源，确认是否要补充 `LICENSE`
5. 如果将来准备持续维护，确认仓库名、简介、标签是否清晰

## 推荐仓库名

可选名称例如：

- `codex-memory-routing-skills`
- `project-memory-skills`
- `codex-rules-router`

当前本地目录使用的是：

`F:\F-code\infor\codex-memory-routing-skills`

## 本地初始化 Git

在仓库目录执行：

```powershell
cd F:\F-code\infor\codex-memory-routing-skills
git init
git add .
git commit -m "feat: add rules-router and project-memory-init skills"
```

## 在 GitHub 创建仓库

1. 打开 GitHub，新建一个空仓库
2. 仓库名建议与本地目录保持一致
3. 是否公开按你的需求决定
4. 如果本地已经有 `README.md`，创建远端仓库时不要再勾选初始化 README

## 连接远端并推送

创建好远端后执行：

```powershell
cd F:\F-code\infor\codex-memory-routing-skills
git remote add origin <your-github-repo-url>
git branch -M main
git push -u origin main
```

示例远端地址：

- `https://github.com/<your-name>/codex-memory-routing-skills.git`
- `git@github.com:<your-name>/codex-memory-routing-skills.git`

## 首次发布后建议补充

如果你准备把它做成长期维护项目，建议后续再补这些内容：

- `LICENSE`
- Release tag
- 示例 issue 模板
- 变更记录
- 更贴近真实使用的 eval prompts

## 版本建议

如果你准备快速收集反馈，可以先从：

`v0.1.0`

开始，强调这是可用但仍在迭代中的第一版。

## 维护建议

发布后最值得持续观察的是：

- `rules-router` 的 generic mode 是否足够轻
- memory-ready 的判定是否过严或过松
- capability -> skill -> fallback 的实际命中是否顺手
- `lessons` 的 type 和升级规则是否真的能减轻记忆膨胀

## 一句话发布策略

先把它作为“可用的工作流草案”发出去，比等到它变成“完美系统”更容易得到高质量反馈。
