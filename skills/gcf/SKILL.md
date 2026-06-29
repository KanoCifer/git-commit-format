---
name: gcf
description: Generate concise, accurate commit messages using Conventional Commits format.
argument-hint: [v(view)|c(commit)|p(push)]
allowed-tools: Bash(git *), Bash(gh *), AskUserQuestion
disable-model-invocation: true
---

## 流程

读取当前分支、暂存区 diff与最近5条提交历史作为生成依据。暂存区为空先 `git add -A`(仅跟踪文件)。

## 行为模式

- `c`/`--commit`:生成 message → 直接 `git commit`(跳过确认)
- `p`/`--push`:生成 message → `git commit` → `git push`(跳过确认)
- 默认模式(空/`v`):展示 preview 后用 `AskUserQuestion` 询问:提交 / 提交并推送 / 取消。

## Commit Message

基于暂存区 diff 实际内容生成。标题已说明清楚则跳过正文。

**标题**:`<type>(<scope>): <imperative summary>`,≤50 chars(hard cap 72),全小写,不加句点。

- Type:`feat`/`fix`/`refactor`/`perf`/`docs`/`test`/`chore`/`build`/`ci`/`style`/`revert`
- Scope:单模块用目录名(`api`、`frontend`、`config`),多模块省略

**正文**(仅必要时:breaking change、非显而易见的 why、`Closes #42`):每行 wrap 72,与标题空一行。

**禁止**:非祈使语气(`added`/`adds`)、AI 署名、emoji、`As requested by` 来源标记。

## 输出格式

严格按此输出,禁止额外信息:

```
提交预览(Committing to <branch>)

提交信息:
<type>(<scope>): <summary>

  - <body line>

变更文件:
• <relative-path>    (+N/-N)
```
