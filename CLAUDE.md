# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个 Claude Code 技能定义仓库，用于开发和发布 Claude Code 技能。每个技能是一个独立目录，包含 `SKILL.md` 文件。

## 目录规范

- 技能放在 `<skill-name>/SKILL.md`，如 `git-commit-format/SKILL.md`
- SKILL.md 使用 YAML frontmatter：`name`（技能名）、`description`（触发条件和使用说明）、可选的 `argument-hint`
- 技能描述用中文撰写

## 提交规范

使用 Conventional Commits 格式，英文 type + 中文说明：

```
<type>(<scope>): <简短中文描述>
```

常用 type：`feat` | `fix` | `refactor` | `docs` | `chore` | `style`
Scope 为技能名称，如 `git-commit-format`
