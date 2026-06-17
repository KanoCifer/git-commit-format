---
name: gcf
description: >
  Generate concise, accurate Git commit messages.This skill should be used when the user modifies code in a git workflow, ready to commit, or says "commit", "generate commit message", "write commit", "commit message", "提交", "commit", "生成提交信息"
argument-hint: [v(view)|c(commit)|p(push)]
allowed-tools: Bash(git *), Bash(gh *), AskUserQuestion
---

## Git 上下文

```
git branch --show-current
git remote -v
git status --short
git diff --cached --stat
git diff --cached | head -500
git log --oneline -10
```

若「暂存区 diff」为空,执行 `git add .`(仅跟踪文件)后重读。

## 行为模式

- `c` / `commit` / `--commit`:生成 message → 直接 `git commit`(跳过确认)
- `p` / `push` / `--push`:检查 CI → 生成 message → `git commit` → `git push`(跳过确认)
- 空 / `v` / `view`:仅生成 preview 展示,不自动执行

分支名含中文、空格或明显临时命名时,给一行提醒。

## 生成 Commit Message

基于「暂存区 diff」实际内容生成。

### 标题(必须)

```
<type>(<scope>): <imperative summary>
```

**Type 速查**

| Type       | 场景                       |
| ---------- | -------------------------- |
| `feat`     | 新增能力                   |
| `fix`      | 问题修复                   |
| `refactor` | 结构优化,不改外部行为      |
| `perf`     | 性能优化                   |
| `docs`     | 文档改动                   |
| `test`     | 测试新增或调整             |
| `chore`    | 依赖、脚本、配置、工程事务 |
| `build`    | 构建系统改动               |
| `ci`       | CI/CD 改动                 |
| `style`    | 仅格式/排版,不改逻辑       |
| `revert`   | 回滚                       |

**Scope**:单模块用模块目录名(`api`、`frontend`、`backend`、`db`、`config`、`deps`),多模块省略。

### 正文(仅在必要时)

标题已能说明时跳过。仅在 breaking change、非显而易见的 why、关联 issue(`Closes #42`)时加正文,每行 wrap at 72 chars,正文和标题空一行。

### 规则

- 祈使语气:`add`、`fix`、`remove`,不要 `added`、`adds`、`adding`
- 尽可能 ≤50 chars,hard cap 72
- 不加句点,全小写
- 禁用词:`This commit does`、`I`、`we`、`now`、`currently`
- 禁止 AI 署名、emoji、`As requested by` 之类来源标记

## 执行

- **默认模式**:展示 preview 后用 `AskUserQuestion` 询问:提交 / 提交并推送 / 取消
- **`--commit`**:直接 `git commit -m "..."`(有正文用 heredoc),跳过询问,然后 `git status` 确认
- **`--push`**:直接执行,跳过询问:
  1. `git rev-parse --abbrev-ref --symbolic-full-name @{u}` 检查远程跟踪
  2. 有远程且有 `gh`:`gh run view --branch <branch> --limit 1 --json status,conclusion`,CI 失败暂停警告
  3. `git commit` → `git push`

## Preview 格式

严格按此输出,不添加模板外任何文本(无开头引导语、结尾确认语、解释、署名、emoji):

```
提交预览(Committing to <branch>)

提交信息:
<type>(<scope>): <summary>

  - <body line 1>
  - <body line 2>

变更文件:
• <relative-path>    (+N/-N)
• <relative-path>    (+N/-N)
```

- **正文**:仅在 breaking change、非显而易见的 why、关联 issue 时展示,每行 `  -` 开头,与标题空一行,≤3 行
- **变更文件**:按 `git diff --cached --stat` 原序,>15 个时前 15 + `... 及 N 个其他文件`
