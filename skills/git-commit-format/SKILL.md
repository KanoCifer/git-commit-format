---
name: git-commit-format
description: >
  Generate concise, accurate Git commit messages using Conventional Commits format.
  Use when the user modifies code in a git workflow, after git add, ready to commit, or says "写 commit", "generate commit message", "write commit", "commit message", "提交代码", "commit", "生成提交信息", "帮我提交".
argument-hint: [v(view)|c(commit)|p(push)]
allowed-tools: Bash(git *), Bash(gh *), AskUserQuestion
---

## Git 上下文（自动注入）

- 当前分支：!`git branch --show-current`
- 远程仓库：!`git remote -v`
- 工作区状态：!`git status --short`
- 暂存区统计：!`git diff --cached --stat`
- 暂存区 diff（前 500 行）：!`git diff --cached | head -500`
- 近期提交：!`git log --oneline -10`

## 前置检查

若上方「暂存区 diff」为空，执行 `git add .`（仅跟踪文件）后重新读取 `git diff --cached`。

## 行为模式

根据 `$ARGUMENTS` 决定：

- `c` / `commit` / `--commit`：生成 message → 直接执行 `git commit`（跳过确认）
- `p` / `push` / `--push`：检查 CI → 生成 message → 直接执行 commit → `git push`（跳过确认）
- 空 / `v` / `view`：仅生成 message 展示预览，不自动执行

## 分支名审查

如果分支名含中文、空格或明显不规范的临时命名，给出一行提醒。

## 生成 Commit Message

基于上方「暂存区 diff」的实际内容生成。

### 标题行（必须）

```
<type>(<scope>): <imperative summary>
```

**type** — 从 `feat`、`fix`、`refactor`、`perf`、`docs`、`test`、`chore`、`build`、`ci`、`style`、`revert` 中选择。完整 type 定义和场景说明见 `references/commit-types.md`。

**scope** — 从项目结构和变更位置推断。单模块变更用模块目录名（如 `api`、`frontend`、`backend`、`db`、`config`、`deps`），多模块变更可省略 scope。

### 正文（仅在必要时添加）

标题已能说明时跳过正文。仅在以下情况加正文：

- 非显而易见的 _why_（"为什么要这么做"）
- Breaking changes、migration notes
- 关联 issue（`Closes #42`、`Refs #17`）

正文每行 wrap at 72 chars，正文和标题之间空一行。

## 规则

### 必须遵守

- 祈使语气：`add`、`fix`、`remove` — 不要 `added`、`adds`、`adding`
- 尽可能 ≤50 chars，hard cap 72
- 不加句点，全小写（跟随项目惯例）

### 禁止内容

- 禁止词语：`"This commit does"`、`"I"`、`"we"`、`"now"`、`"currently"`
- 禁止 AI 归属：`"Generated with Claude Code"` 或任何 AI 署名
- 禁止来源标记：`"As requested by..."` — 如需归属改用 Co-authored-by trailer
- 禁止 Emoji（除非项目惯例要求）

## 执行

- **默认模式**：展示预览后使用 `AskUserQuestion` 工具询问是否确认提交或提交并推送
- **`--commit`/自动提交**：生成 message 后直接执行 `git commit -m "..."`（有正文用 heredoc），跳过询问确认，然后 `git status` 确认
- **`--push`/提交并推送**：生成 message 后直接执行以下步骤，跳过询问确认：
  1. 检查是否有远程跟踪分支：`git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null`
  2. 如果有远程且安装了 `gh` CLI，检查 CI 状态：`gh run view --branch <branch> --limit 1 --json status,conclusion`
  3. CI 失败时暂停并警告
  4. `git commit` → `git push`

## 展示 Preview

严格按以下格式输出，不得添加模板外的任何文本：

```
提交预览（Committing to <branch>）

提交信息：
<type>(<scope>): <summary>

  - <body line 1>
  - <body line 2>

变更文件：
• <file>    (+N/-N)
• <file>    (+N/-N)
```

**格式规则：**

- **提交信息**：必填，格式 `<type>(<scope>): <imperative summary>`，scope 仅单模块时出现
- **正文**：仅在标题无法完整表达时展示（breaking change、非显而易见的 why、关联 issue）。每行以 `  -` 开头（两空格 + 连字符 + 空格），正文与标题空一行，≤3 行
- **变更文件**：必填，格式 `• <relative-path>    (+N/-N)`，文件数 >15 时展示前 15 个 + `... 及 N 个其他文件`

**禁止输出：** 开头引导语（`已为您生成`、`以下是`）、结尾确认语（`是否确认提交？`）、解释说明、模型署名、emoji

## 参考

- `references/commit-types.md` — 完整的 type 定义、scope 推断规则和更多示例
- `references/output-format.md` — Preview 输出的严格格式约束
