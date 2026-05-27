# Git Commit Format

用 Conventional Commits 格式生成 Git 提交信息的 Claude Code 插件。

## 安装

```bash
# 添加市场
claude plugins marketplace add KanoCifer/git-commit-format

# 安装插件
claude plugins install git-commit-format@git-commit-format
```

或在claude对话框里安装

```
/plugin marketplace add KanoCifer/git-commit-format
/plugin install git-commit-format@git-commit-format
```

### 其他安装方式（其他agents）

将prompt发送给你的agent

```
帮我安装这个skill：https://github.com/KanoCifer/git-commit-format
```

## 使用

完成开发后

```
/git-commit-format:git-commit-format       # 仅生成 commit message 预览
/git-commit-format:git-commit-format c     # 生成并自动 git commit
/git-commit-format:git-commit-format p     # 生成、commit 并 git push
```

或自动触发：在 git 工作流中修改代码后说"写 commit"。

## 概述

基于 `git diff --cached` 的实际变更生成 Conventional Commits 格式的提交信息（英文 type + 中文说明），自动推断 scope，分支名不规范时给出提醒。

## 结构

```
git-commit-format/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── git-commit-format/
│       ├── SKILL.md
│       └── references/
│           └── commit-types.md
├── README.md
└── LICENSE
```

## License

MIT
