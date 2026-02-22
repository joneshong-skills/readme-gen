[English](README.md) | [繁體中文](README.zh.md)

# readme-gen

Generate professional README.md files by analyzing a repository's structure and content.

## Description

README Generator scans a repository's files, dependencies, and existing documentation to produce a well-structured, accurate README — delegating scanning to an explorer agent and writing to a writer agent.

## Features

- Analyzes repository structure, code, and dependencies automatically
- Generates badges, installation steps, usage examples, and API docs
- Supports multiple project types (Node.js, Python, Go, etc.)
- Bilingual output (English + Traditional Chinese) available
- Updates existing READMEs to stay current with code changes
- Uses `explorer` + `writer` agent delegation for efficiency

## Usage

Invoke by asking Claude Code with trigger phrases such as:

- "generate a README"
- "create a README"
- "write project documentation"
- "産生 README"
- "建立 README"

## Related Skills

- [`doc-coauthoring`](https://github.com/joneshong-skills/doc-coauthoring)
- [`skill-publisher`](https://github.com/joneshong-skills/skill-publisher)
- [`changelog-gen`](https://github.com/joneshong-skills/changelog-gen)

## Install

Copy the skill directory into your Claude Code skills folder:

```
cp -r readme-gen ~/.claude/skills/
```

Skills placed in `~/.claude/skills/` are auto-discovered by Claude Code. No additional registration is needed.
