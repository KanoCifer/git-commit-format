# Git Commit Format — Skill Pack

A Claude Code skill that generates Conventional Commits-format commit messages based on actual staged changes (`git diff --cached`).

## Installation

### Option 1: Claude Code Plugin Marketplace (Recommended)

```bash
# Add the marketplace
claude plugins marketplace add KanoCifer/git-commit-format

# Install the plugin
claude plugins install gcf@gcf
```

Or install directly from the Claude chat:

```
/plugin marketplace add KanoCifer/git-commit-format
# Requires two separate commands
/plugin install gcf@gcf
```

### Option 2: npx skills (Cross-Agent)

Works with 70+ coding agents (Claude Code, Codex, Cursor, etc.):

```bash
npx skills add KanoCifer/git-commit-format
```

### Option 3: Manual Prompt

Share the following prompt with your agent:

```
Install this skill for me: https://github.com/KanoCifer/git-commit-format
```

## Usage

After development, manually invoke:

```
/gcf:gcf       # Preview commit message only
/gcf:gcf c     # Generate and commit
/gcf:gcf p     # Generate, commit, and push
```

## How It Works

Based on `git diff --cached` of your actual staged changes, the skill:

1. Reads the current branch name
2. Analyzes staged file changes (diff stat + content)
3. Reviews the last 5 commits for style consistency
4. Generates a Conventional Commits message with English type + summary
5. Automatically infers the scope from the changed module
6. Warns if the branch name doesn't follow conventions

## Project Structure

```
git-commit-format/
├── .claude-plugin/
│   └── marketplace.json       # Plugin marketplace manifest
├── skills/
│   └── gcf/
│       └── SKILL.md           # Skill implementation
├── README.md                  # Chinese documentation
├── README_EN.md               # This file
└── LICENSE                    # MIT
```

## License

MIT
