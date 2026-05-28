---
name: git-commit-format
description: >
  Generate concise, accurate Git commit messages using Conventional Commits format (English type + Chinese description).
  Use when the user modifies code in a git workflow, after git add, ready to commit, or says "写 commit", "generate commit message", "write commit", "commit message".
argument-hint: [v(view)|c(commit)|p(push)]
context: fork
license: MIT
model: inherit
metadata:
  author: Kuroome
  version: 1.0.0
---

根据 `$ARGUMENTS` 的值决定行为模式：

- `$ARGUMENTS` 为 `c`、`commit`、`--commit`：生成 message → 询问确认 → 执行 `git commit`
- `$ARGUMENTS` 为 `p`、`push`、`--push`：检查 CI → 生成 message → 确认 → commit → `git push`
- `$ARGUMENTS` 为空、`v` 或 `view`：仅生成 message 展示预览，不自动执行

## Step 1：收集 Git 上下文

检查暂存区状态。若 `!git diff --cached` 无内容，先执行 `!git add .`。然后收集以下信息：

```!
git branch --show-current
git remote -v
git status
git diff --cached --stat
git diff --cached
git log --oneline -10
```

## Step 2：分支名审查

如果分支名含中文、空格或明显不规范的临时命名，给出一行提醒。

## Step 3：生成 Commit Message

基于 `!git diff --cached` 的实际 diff 内容生成。

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

- 祈使语气：`add`、`fix`、`remove` — 不要 `added`、`adds`、`adding`
- 尽可能 ≤50 chars，hard cap 72
- 不加句点，全小写（跟随项目惯例）
- 不要使用emoji，除非说明
- "This commit does"、"I"、"we"、"now"、"currently"
- "As requested by..." — 改用 Co-authored-by trailer
- "Generated with Claude Code" 或任何 AI 归属
- Emoji（除非项目惯例要求）

## Step 4：执行

- **默认模式**：展示后等待确认/修改，不做提交，使用`AskUserQuestion`工具询问是否提交或提交并推送
- **`--commit`/自动提交**：执行 `git commit -m "..."`（有正文用 heredoc），然后 `git status` 确认
- **`--push`/提交并推送**：
  1. 检查是否有远程跟踪分支：`git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null`
  2. 如果有远程且安装了 `gh` CLI，检查 CI 状态：`gh run view --branch <branch> --limit 1 --json status,conclusion`
  3. CI 失败时暂停并警告
  4. `git commit` → `git push`

## Step 5：展示 Preview

将 Step 1 收集的 git 上下文（branch、diff_stat、diff、log、mode）交给 `agents/formatter.md` 生成 preview，严格按 `references/output-format.md` 格式输出，不得添加模板外的任何文本。

## 参考

- `references/commit-types.md` — 完整的 type 定义、scope 推断规则和更多示例
- `references/output-format.md` — Preview 输出的严格格式约束
- `agents/formatter.md` — Formatter agent，负责生成 commit message 并格式化 preview
