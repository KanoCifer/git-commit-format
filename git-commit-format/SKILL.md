---
name: git-commit-format
description: >
  用 Conventional Commits 格式生成简洁精确的 Git 提交信息
  （英文类型 + 中文说明）。用户在 git 工作流中修改代码后、
  做了 git add 后、准备提交时、说"写 commit"触发。
argument-hint: "[v(view)|c(commit)|p(push)]"
---

根据用户输入检测行为模式：

- 用户说`--commit`或`--c`或`自动提交`：生成 message → 询问确认 → 自动 `git commit`
- 用户说`--push`或`--p`或`提交并推送`：检查 CI → 生成 message → 确认 → commit → `git push`
- 其他情况（包括只说"写 commit"）：仅生成 message 展示给用户预览，不自动执行

## Step 1：收集 Git 上下文

先收集暂存区更改，若无暂存区更改，默认执行`git add .`，然后收集以下信息：

```bash
git branch --show-current
git remote -v
git status
git diff --cached --stat
git diff --cached
git log --oneline -10
git diff --stat
```

## Step 2：分支名审查

告知用户当前分支名。如果分支名含中文、空格或明显不规范的临时命名，给出一行提醒。

## Step 3：生成 Commit Message

基于 `git diff --cached` 的实际 diff 内容生成。

### 标题行（必须）

```
<type>(<scope>): <imperative summary>
```

**type** — `feat` | `fix` | `refactor` | `perf` | `docs` | `test` | `chore` | `build` | `ci` | `style` | `revert`

**scope** — 从项目结构和变更位置推断，如 `api`、`frontend`、`react-app`、`backend`、`db`、`config`、`deps`。多模块变更时可不写 scope。

**规则：**

- 祈使语气：`add`、`fix`、`remove` — 不要 `added`、`adds`、`adding`
- 尽可能 ≤50 chars，hard cap 72
- 不加句点，全小写（跟随项目惯例）

### 正文（仅在必要时添加）

标题已能说明时跳过正文。仅在以下情况加正文：

- 非显而易见的 _why_（"为什么要这么做"）
- Breaking changes、migration notes
- 关联 issue（`Closes #42`、`Refs #17`）

正文每行 wrap at 72 chars，正文和标题之间空一行。

### 以下内容永不出现

- "This commit does X"、"I"、"we"、"now"、"currently" — diff 本身就是说明
- "As requested by..." — 改用 Co-authored-by trailer
- "Generated with Claude Code" 或任何 AI 归属
- Emoji（除非项目惯例要求）

## Step 4：执行

- **默认模式**：展示后等待用户确认/修改，不做提交，使用AskUserQuestion工具询问用户是否提交或提交并推送
- **`--commit`/自动提交**：执行 `git commit -m "..."`（有正文用 heredoc），然后 `git status` 确认
- **`--push`/提交并推送**：
  1. 先检查是否有远程跟踪分支：`git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null`
  2. 如果有远程且安装了 `gh` CLI，检查 CI 状态：`gh run view --branch <branch> --limit 1 --json status,conclusion`
  3. CI 失败时暂停并警告用户
  4. `git commit` → `git push`

## Step 5：展示 Preview

执行后，按此结构展示给用户：

```
📋 提交预览（Committing to <分支名>）

变更文件：
• src/file_a.ts    (+10/-2)
• src/file_b.ts    (+5/-0)

提交信息：
feat(api): add pagination support for book list
  - 具体实现了什么功能
  - 做了哪些重要改动
```

## 中文 type 速查

| Type       | 场景                       |
| ---------- | -------------------------- |
| `feat`     | 新增能力                   |
| `fix`      | 问题修复                   |
| `refactor` | 结构优化，不改外部行为     |
| `perf`     | 性能优化                   |
| `docs`     | 文档改动                   |
| `test`     | 测试新增或调整             |
| `chore`    | 依赖、脚本、配置、工程事务 |
| `build`    | 构建系统改动               |
| `ci`       | CI/CD 改动                 |
| `style`    | 仅格式/排版，不改逻辑      |
| `revert`   | 回滚                       |
