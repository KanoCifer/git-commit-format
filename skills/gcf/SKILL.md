---
name: gcf
description: Generate concise, accurate commit messages using Conventional Commits format. Use when the user invokes `/gcf`.
argument-hint: [v(view)|c(commit)|p(push)]
allowed-tools: Bash(git *), AskUserQuestion
---

## 流程

1. **理解变更**：从对话上下文或暂存区中了解本次变更的内容和意图
2. 如果信息不足（用户只说"/gcf"但没描述改了什么），`git add -A` 并检查暂存区修改来生成。
3. 生成 Conventional Commits 格式的提交信息
4. 按行为模式处理后续操作

## 行为模式

- `c`/`--commit`：生成 message → 执行 `git commit`
- `p`/`--push`：生成 message → `git commit` → `git push`
- 默认模式（空/`v`）：展示 preview 后用 `AskUserQuestion` 询问：提交 / 提交并推送 / 取消

## Commit Message

根据用户描述的变更内容生成。标题已说明清楚则跳过正文。

**标题**：`<type>(<scope>): <imperative summary>`，≤50 chars（hard cap 72），全小写，不加句点

- Type：`feat` / `fix` / `refactor` / `perf` / `docs` / `test` / `chore` / `build` / `ci` / `style` / `revert`
- Scope：单模块用目录名（`api`、`frontend`、`config`）；多模块或不确定时省略

**正文**（仅必要时：breaking change、非显而易见的 why、`Closes #42`）：每行 wrap 72，与标题空一行

**禁止**：非祈使语气（`added`/`adds`）、AI 署名、emoji、`As requested by` 来源标记

## 输出格式

严格按此输出，禁止额外信息：

```
提交预览 (Committing to <branch>)

提交信息:
<type>(<scope>): <summary>

  <body line>

变更文件:
• <relative-path>    (+N/-N)
```

## 示例

用户描述：/gcf

```
提交预览 (Committing to main)

提交信息:
feat(auth): add JWT verification to login flow

  Validates token signature and expiry before allowing
  access to protected routes.

变更文件:
• src/auth/login.py    (+42/-0)
```
