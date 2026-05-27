# Commit Type Reference

## Type 速查

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

## Scope 推断规则

从项目结构和变更路径推断 scope：

- 单模块变更 → 用模块目录名（如 `api`、`frontend`、`backend`、`db`、`config`、`deps`）
- 多模块变更 → 可省略 scope
- 跨模块关联变更 → 选主要影响的模块

## 完整示例

### 单行标题（常规变更）

```
feat(api): add pagination support for book list
fix(auth): resolve token refresh race condition
refactor(db): extract connection pool to shared module
chore(deps): bump axios to 1.6.0
```

### 带正文（重大变更 / 需要解释 why）

```
feat(api): add rate limiting to public endpoints

Requests exceeding 100/min per IP receive 429 responses.
This protects the upstream billing service from being overwhelmed
during traffic spikes.

BREAKING CHANGE: unauthenticated requests now share a global rate
limit of 100/min. Previously there was no limit.
```

### 正文中包含 issue 引用

```
fix(frontend): correct date picker timezone offset

The date picker was using local timezone instead of UTC,
causing off-by-one errors for users in negative UTC offsets.

Closes #342
```
