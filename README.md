# Git Commit Format

用 Conventional Commits 格式生成 Git 提交信息的 Claude Code 插件。

## 安装

### 方式一：Claude Code 插件市场（推荐）

```bash
# 添加市场
claude plugins marketplace add KanoCifer/git-commit-format

# 安装插件
claude plugins install gcf@gcf
```

或在 claude 对话框里安装

```
/plugin marketplace add KanoCifer/git-commit-format
# 需要分两次输入
/plugin install gcf@gcf
```

### 方式二：npx skills（跨 Agent 安装）

支持 70+ 编码 Agent（Claude Code、Codex、Cursor 等）：

```bash

# 交互式安装
npx skills add KanoCifer/git-commit-format
```

### 方式三：其他 Agents

将下面的 prompt 发送给你的 agent：

```
帮我安装这个skill：https://github.com/KanoCifer/git-commit-format
```

## 使用

完成开发后手动调用

```
/gcf:gcf       # 仅生成 commit message 预览
/gcf:gcf c     # 生成并自动 git commit
/gcf:gcf p     # 生成、commit 并 git push
```

## 概述

基于 `git diff --cached` 的实际变更生成 Conventional Commits 格式的提交信息（英文 type + 中文说明），自动推断 scope，分支名不规范时给出提醒。

## 结构

```
git-commit-format/
├── .claude-plugin/
│   └── marketplace.json
├── skills/
│   └── gcf/
│       └── SKILL.md
├── README.md
└── LICENSE
```

## License

MIT
