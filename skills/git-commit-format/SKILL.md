---
name: git-commit-format
description: >
  Generate concise, accurate Git commit messages using Conventional Commits format (English type + Chinese description).
  Use when the user modifies code in a git workflow, after git add, ready to commit, or says "写 commit", "generate commit message", "write commit", "commit message".
argument-hint: [v(view)|c(commit)|p(push)]
context: fork
model: haiku
shell: bash
license: MIT
metadata:
  author: Kuroome
  version: 1.0.0
---

根据 `$ARGUMENTS` 的值决定行为模式：

- `$ARGUMENTS` 为 `c`、`commit`、`--commit`：生成 message → 询问确认 → 执行 `git commit`
- `$ARGUMENTS` 为 `p`、`push`、`--push`：检查 CI → 生成 message → 确认 → commit → `git push`
- `$ARGUMENTS` 为空、`v` 或 `view`：仅生成 message 展示预览，不自动执行

## Step 1：收集 Git 上下文

检查暂存区状态。若 `git diff --cached` 无内容，先执行 `!git add .`。然后收集以下信息：

```!
git branch --show-current
git remote -v
git status
git diff --cached --stat
git diff --cached
git log --oneline -10
```

## Step 2：分支名审查

展示当前分支名。如果分支名含中文、空格或明显不规范的临时命名，给出一行提醒。

## Step 3：生成 Commit Message

基于 `git diff --cached` 的实际 diff 内容生成。

### 标题行（必须）

```
<type>(<scope>): <imperative summary>
```

**type** — 从 `feat`、`fix`、`refactor`、`perf`、`docs`、`test`、`chore`、`build`、`ci`、`style`、`revert` 中选择。完整 type 定义和场景说明见 `references/commit-types.md`。

**scope** — 从项目结构和变更位置推断。单模块变更用模块目录名（如 `api`、`frontend`、`backend`、`db`、`config`、`deps`），多模块变更可省略 scope。

**规则：**

- 祈使语气：`add`、`fix`、`remove` — 不要 `added`、`adds`、`adding`
- 尽可能 ≤50 chars，hard cap 72
- 不加句点，全小写（跟随项目惯例）

### 正文（仅在必要时添加）

标题已能说明时跳过正文。仅在以下情况加正文：

- 非显而易见的 _why_（"为什么要这么做"）
- Breaking changes、migration notes
- 关联 issue（`Closes #42`、`Refs #17`）
- 通过 `- ` 前缀添加额外信息

正文每行 wrap at 72 chars，正文和标题之间空一行。

### 以下内容永不出现

- "This commit does X"、"I"、"we"、"now"、"currently" — diff 本身就是说明
- "As requested by..." — 改用 Co-authored-by trailer
- "Generated with Claude Code" 或任何 AI 归属
- Emoji（除非项目惯例要求）

## Step 4：执行

- **默认模式**：展示后等待确认/修改，不做提交，使用 AskUserQuestion 工具询问是否提交或提交并推送
- **`--commit`/自动提交**：执行 `git commit -m "..."`（有正文用 heredoc），然后 `git status` 确认
- **`--push`/提交并推送**：
  1. 检查是否有远程跟踪分支：`git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null`
  2. 如果有远程且安装了 `gh` CLI，检查 CI 状态：`gh run view --branch <branch> --limit 1 --json status,conclusion`
  3. CI 失败时暂停并警告
  4. `git commit` → `git push`

## Step 5：展示 Preview

执行后，严格按此结构展示, 仅输出Preview内容：

```
提交预览（Committing to <分支名>）

提交信息：
feat(api): add pagination support for book list

  - 具体实现了什么功能
  - 做了哪些重要改动

变更文件：
• src/file_a.ts    (+10/-2)
• src/file_b.ts    (+5/-0)
```

如为`仅预览模式`，输出后使用`AskUserQuestion`询问用户是否提交/推送

## 参考

完整的 type 定义、scope 推断规则和更多示例见 `references/commit-types.md`。
