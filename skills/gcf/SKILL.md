---
name: gcf
description: Generate concise, accurate commit messages using Conventional Commits format. Use when the user invokes `/gcf`.
argument-hint: [v(view)|c(commit)|p(push)]
allowed-tools: Bash(git *), Bash(gh *), AskUserQuestion
disable-model-invocation: true
context: fork
---

## 流程

1. 读取 `git branch --show-current` 获取分支名
2. 读取 `git diff --cached --stat` 和 `git diff --cached` 获取暂存区变更
3. 读取 `git log -5 --oneline` 获取最近5条提交历史作为风格参考
4. 如果暂存区为空(无输出):尝试 `git add -A`(仅已跟踪文件),再次检查;仍为空则告知用户无变更并退出

## 行为模式

- `c`/`--commit`:生成 message → 直接 `git commit`(跳过确认)
- `p`/`--push`:生成 message → `git commit` → `git push`(跳过确认)
- 默认模式(空/`v`):展示 preview 后用 `AskUserQuestion` 询问:提交 / 提交并推送 / 取消

## Commit Message

基于暂存区 diff 实际内容生成。标题已说明清楚则跳过正文。

**标题**: `<type>(<scope>): <imperative summary>`,≤50 chars(hard cap 72),全小写,不加句点

- Type: `feat`/`fix`/`refactor`/`perf`/`docs`/`test`/`chore`/`build`/`ci`/`style`/`revert`
- Scope: 单模块用目录名(`api`、`frontend`、`config`);多模块或不确定时省略

**正文**(仅必要时: breaking change、非显而易见的 why、`Closes #42`):每行 wrap 72,与标题空一行

**禁止**: 非祈使语气(`added`/`adds`)、AI 署名、emoji、`As requested by` 来源标记

## 输出格式

严格按此输出,禁止额外信息:

```
提交预览 (Committing to <branch>)

提交信息:
<type>(<scope>): <summary>

  <body line>

变更文件:
• <relative-path>    (+N/-N)
```

## 示例

输入: 暂存区有 `src/auth/login.py` 新增 JWT 验证函数

```
提交预览 (Committing to main)

提交信息:
feat(auth): add JWT verification to login flow

  Validates token signature and expiry before allowing
  access to protected routes.

变更文件:
• src/auth/login.py    (+42/-0)
```
