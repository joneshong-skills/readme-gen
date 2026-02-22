[English](README.md) | [繁體中文](README.zh.md)

# readme-gen

Generate professional README.md files by analyzing a repository's structure and content.

## 說明

README Generator scans a repository's files, dependencies, and existing documentation to produce a well-structured, accurate README — delegating scanning to an explorer agent and writing to a writer agent.

## 功能特色

- Analyzes repository structure, code, and dependencies automatically
- Generates badges, installation steps, usage examples, and API docs
- Supports multiple project types (Node.js, Python, Go, etc.)
- Bilingual output (English + Traditional Chinese) available
- Updates existing READMEs to stay current with code changes
- Uses `explorer` + `writer` agent delegation for efficiency

## 使用方式

透過以下觸發語句呼叫 Claude Code 來使用此技能：

- "generate a README"
- "create a README"
- "write project documentation"
- "産生 README"
- "建立 README"

## 相關技能

- [`doc-coauthoring`](https://github.com/joneshong-skills/doc-coauthoring)
- [`skill-publisher`](https://github.com/joneshong-skills/skill-publisher)
- [`changelog-gen`](https://github.com/joneshong-skills/changelog-gen)

## 安裝

將技能目錄複製到 Claude Code 技能資料夾：

```
cp -r readme-gen ~/.claude/skills/
```

放置在 `~/.claude/skills/` 的技能會被 Claude Code 自動發現，無需額外註冊。
