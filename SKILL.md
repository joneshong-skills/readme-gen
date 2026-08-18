---
name: readme-gen
description: "readme, gen, generate, create, write, project, documentation, 產生"
version: 0.2.0
tools: Read, Write, Glob, Grep, Bash, sandbox_execute
---

# README Generator

Generate professional README.md files by analyzing a repository's structure, dependencies, and existing documentation.

## Agent Delegation

Delegate codebase scanning to `explorer` agent, README writing to `writer` agent.

```
Main context (style decision, section selection, badge validation)
  └─ Task(subagent_type: explorer, prompt: "Scan the repo at [path]. Return: project type, key dependencies, entry points, existing docs list, CI config filenames. Output format: structured key-value list only.")
  └─ Task(subagent_type: writer, prompt: "Write a [minimal|standard|comprehensive] README.md for [project name]. Analysis: [explorer summary]. Use real code examples from the codebase. Return only the markdown content.")
```

## Workflow

### Phase 1: Project Analysis

**Preferred (Sandbox)**: Use `sandbox_execute` to batch the entire project scan in one call — detect project type, read manifest files, scan directory structure, check for existing docs, identify CI/CD, and sample key source files. Returns a structured project profile (~400 tokens) instead of 10+ individual Read/Glob calls loading raw file contents into context.

**Fallback**: Scan the repository step by step to understand what the project is and how it works.

1. **Detect project type** by checking for manifest files:
   - `package.json` -> Node.js / JavaScript / TypeScript
   - `Cargo.toml` -> Rust
   - `go.mod` -> Go
   - `pyproject.toml` / `setup.py` / `requirements.txt` -> Python
   - `Gemfile` -> Ruby
   - `pom.xml` / `build.gradle` -> Java / Kotlin
   - `*.csproj` / `*.sln` -> .NET / C#
   - `CMakeLists.txt` / `Makefile` -> C / C++
   - `pubspec.yaml` -> Dart / Flutter
   - `Package.swift` -> Swift

2. **Read manifest files** to extract: project name, version, description, license, dependencies, scripts, commands, entry points.

3. **Scan directory structure** (`ls`, Glob for `src/**`, `lib/**`, `cmd/**`, etc.) to understand architecture — identify entry points, modules, config files, test directories.

4. **Check for existing docs**: current README.md, CONTRIBUTING.md, LICENSE, docs/ folder, examples/ folder, CHANGELOG.md.

5. **Identify CI/CD**: `.github/workflows/`, `.gitlab-ci.yml`, `Dockerfile`, `docker-compose.yml`.

6. **Sample real code**: Read main entry point and 2-3 key source files to extract realistic usage examples and understand the public API.

### Phase 2: Determine Style

Ask the user or infer from project complexity:

| Style | Target Length | When to Use |
|---|---|---|
| **minimal** | ~50 lines | Small utilities, single-purpose tools, scripts |
| **standard** | ~150 lines | Libraries, CLI tools, typical open-source projects |
| **comprehensive** | ~300+ lines | Frameworks, platforms, projects with complex APIs |

Default to **standard** if not specified. If the project has fewer than 5 source files, lean toward minimal. If it has extensive API surface or multiple packages, lean toward comprehensive.

### Phase 3: Generate README

Write the README.md with these sections in order. Omit sections that do not apply.

#### Section Reference

1. **Title + Icon + Badges** (MANDATORY format)
   - H1 with project name
   - Centered icon: `<p align="center"><img src="..." alt="icon" width="200"/></p>`
   - Language switcher (if bilingual): `<p align="center"><a href="README.md">English</a> | <a href="README.zh.md">繁體中文</a></p>`
   - Centered badge row using HTML `<p align="center">` with `<a>` + `<img>` (NOT markdown `![]()`)
   - See Badge Patterns below for template

2. **Description**
   - One-line tagline (from manifest description or inferred)
   - 2-4 sentence overview explaining what it does and why it exists

3. **Features**
   - Bullet list of key capabilities derived from code analysis
   - Keep to 4-8 items; be specific, not generic

4. **Prerequisites + Installation**
   - Runtime requirements (Node >= 18, Rust stable, etc.)
   - Install command (`npm install`, `cargo add`, `pip install`, etc.)
   - For libraries: show dependency manager command
   - For applications: show clone + build steps

5. **Quick Start / Usage**
   - Minimal working code example pulled or adapted from actual source
   - For CLIs: show 2-3 common command invocations
   - For libraries: show import + basic usage
   - Use the project's actual module/package name

6. **Configuration**
   - Environment variables (scan for `process.env`, `os.Getenv`, `std::env`, etc.)
   - Config file format if applicable
   - Table format: Variable | Description | Default

7. **API Reference**
   - Only for libraries with public API surface
   - Document key exported functions/types with signatures
   - Link to full generated docs if available (rustdoc, typedoc, etc.)

8. **Architecture**
   - Brief description of how the project is structured
   - Suggest using `diagram-gen` skill to create a Mermaid architecture diagram
   - Example prompt: "Use diagram-gen to create an architecture diagram for this project"

9. **Contributing**
   - Reference CONTRIBUTING.md if it exists
   - Otherwise: fork, branch, PR workflow in 4-5 steps
   - Mention test commands from manifest (e.g., `npm test`, `cargo test`)

10. **License**
    - Detect from LICENSE file or manifest field
    - One-line statement with link to LICENSE file

### Badge Patterns

**MANDATORY: Use HTML `<p align="center">` format, NOT markdown `![]()` syntax.**

Template (standard format):
```html
# project-name

<p align="center">
  <img src="docs/icon.png" alt="project-name icon" width="200"/>
</p>

<p align="center">
  <strong><a href="README.md">English</a></strong>
  &nbsp;|&nbsp;
  <a href="README.zh.md">繁體中文</a>
</p>

<p align="center">
  <!-- Version / Registry (pick one) -->
  <a href="https://www.npmjs.com/package/PACKAGE">
    <img alt="npm version" src="https://img.shields.io/npm/v/PACKAGE?style=flat-square">
  </a>
  <a href="https://pypi.org/project/PACKAGE/">
    <img alt="PyPI version" src="https://badge.fury.io/py/PACKAGE.svg">
  </a>
  <!-- Language -->
  <a href="https://www.python.org/">
    <img alt="Python 3.12+" src="https://img.shields.io/badge/python-3.12%2B-blue?style=flat-square">
  </a>
  <!-- License -->
  <a href="https://opensource.org/licenses/MIT">
    <img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-green?style=flat-square">
  </a>
  <!-- Stars -->
  <a href="https://github.com/OWNER/REPO/stargazers">
    <img alt="Stars" src="https://img.shields.io/github/stars/OWNER/REPO?style=flat-square">
  </a>
  <!-- DeepWiki (AUTO-ADD for all public repos) -->
  <a href="https://deepwiki.com/OWNER/REPO">
    <img alt="Ask DeepWiki" src="https://deepwiki.com/badge.svg">
  </a>
</p>
```

Common badge sources:
- **Version**: shields.io (`npm/v`, `crates/v`, `pypi/v`) or badge.fury.io
- **CI**: `github/actions/workflow/status/OWNER/REPO/WORKFLOW.yml?branch=main`
- **License**: `badge/license-MIT-green` (match actual license)
- **Language**: `badge/python-3.12%2B-blue`, `badge/TypeScript-5.0-blue`, `badge/Rust-stable-orange`
- **Stars**: `github/stars/OWNER/REPO`
- **DeepWiki**: `https://deepwiki.com/badge.svg` → link to `https://deepwiki.com/OWNER/REPO`

Rules:
- Only include badges that are verifiable — do not guess at CI workflow filenames without checking `.github/workflows/`
- **DeepWiki badge**: Auto-include for ALL public repos (operonlab/* and JonesHong/*)
- Use `style=flat-square` for consistency (except DeepWiki which has its own SVG)
- Images/screenshots in body: use `<p align="center"><img width="..." /></p>`, NOT markdown `![]()`

### Tech Stack Detection Heuristics

After identifying the project type, adjust content accordingly:

- **Node.js**: Check for `tsconfig.json` (TypeScript), framework (`next`, `express`, `fastify` in deps), bundler (`vite`, `webpack`, `esbuild`), test runner (`jest`, `vitest`, `mocha`).
- **Rust**: Check for `[[bin]]` vs `[lib]` in Cargo.toml, workspace members, feature flags.
- **Python**: Check for `[tool.poetry]`, `[build-system]`, framework (`fastapi`, `flask`, `django`), type checking (`mypy`, `pyright`).
- **Go**: Check for `cmd/` directory (CLI), `internal/` (library pattern), `go generate` usage.
- **Monorepo**: Check for `workspaces` in package.json, `pnpm-workspace.yaml`, `Cargo.toml` with `[workspace]`, `lerna.json`.

## Key Principles

- **Use real code**: Extract actual imports, function names, and patterns from the codebase. Never invent fictional API examples.
- **Match the project's voice**: If the existing README or comments are casual, stay casual. If formal, stay formal.
- **Proportional depth**: A 200-line CLI tool does not need a 300-line README. Scale sections to match project complexity.
- **Verify before badge**: Only add badges for services actually configured in the repo.
- **Working examples**: Every code block should be copy-pasteable and functional.
- **No empty sections**: If a section has nothing meaningful to say, omit it entirely.
- **Suggest diagrams**: For projects with non-trivial architecture, recommend using the `diagram-gen` skill to produce an architecture diagram to embed in the README.

## Sandbox Optimization

Phase 1 (Project Analysis) benefits significantly from sandbox execution:

- **Project scanning**: Walk directory tree, detect project type from manifest files, extract dependencies, find CI configs, and sample key source files — all in one `sandbox_execute` call. Returns structured project profile (~400 tokens) vs 10+ Read/Glob calls (~3,000+ tokens).
- **Tech stack detection**: Batch-check for all manifest files and framework indicators in one pass.

Principle: **Project structure scanning → sandbox; README writing + style decisions → LLM.**

## Continuous Improvement

This skill evolves with each use. After every invocation:

1. **Reflect** — Identify what worked, what caused friction, and any unexpected issues
2. **Record** — Append a concise lesson to `lessons.md` in this skill's directory
3. **Refine** — When a pattern recurs (2+ times), update SKILL.md directly

### lessons.md Entry Format

```
### YYYY-MM-DD — Brief title
- **Friction**: What went wrong or was suboptimal
- **Fix**: How it was resolved
- **Rule**: Generalizable takeaway for future invocations
```

Accumulated lessons signal when to run `/skill-optimizer` for a deeper structural review.
