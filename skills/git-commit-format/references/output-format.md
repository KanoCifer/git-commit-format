# Output Format Reference

Preview 输出的严格格式约束。所有预览必须遵循此规范，不得添加额外内容。

## Preview 结构

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

## 字段规则

### 提交信息（标题行）

- 必填，始终展示
- 格式：`<type>(<scope>): <imperative summary>`
- scope 仅在单模块变更时出现，多模块省略括号

### 正文（body）

- 仅在标题行无法完整表达变更意图时展示
- 展示条件：涉及 breaking change、非显而易见的 why、关联 issue
- 每行以 ` -` 开头（两空格缩进 + 连字符 + 空格）
- 正文与标题之间空一行
- 正文行数 ≤ 3 行

### 变更文件

- 必填，始终展示
- 每行格式：`• <relative-path>    (+N/-N)`
- 文件路径使用相对路径
- 文件列表按 `git diff --cached --stat` 原始顺序
- 文件数 > 15 时，展示前 15 个 + `... 及 N 个其他文件`

## 禁止输出

以下内容不得出现在 preview 中：

- 开头引导语：`已为您生成`、`以下是`、`根据 diff`、`我为您`
- 结尾确认语：`是否确认提交？`、`请确认以上信息`、`需要修改吗？`
- 解释说明：`本次提交的目的是`、`主要改动包括`
- 模型署名：`Generated with`、`Co-authored-by`
- emoji（除非项目惯例要求）
- 重复展示标题行（preview 中只出现一次）

## 模式差异

| 模式                 | Preview 后行为                                      |
| -------------------- | --------------------------------------------------- |
| 仅预览（v/view）     | 使用 AskUserQuestion 询问：提交 / 提交并推送 / 取消 |
| 自动提交（c/commit） | 直接执行 git commit，不再询问                       |
| 提交并推送（p/push） | 直接执行 git commit + git push，不再询问            |
