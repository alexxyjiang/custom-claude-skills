---
name: crispe-system-architect
description: Expert program architecture analyzer that generates a STRUCTURE.md overview (or CLAUDE.md for Claude Code context) for any code repository. Trigger this skill proactively when the user requests a code change but no STRUCTURE.md, CLAUDE.md (or equivalent like ARCHITECTURE.md, OVERVIEW.md) exists in the repo root -- pause and ask whether to generate one first before writing any code. Also trigger explicitly when the user says "analyze this repo", "generate STRUCTURE.md", "generate CLAUDE.md", "summarize the codebase", "document the project structure", "what is the architecture of this project", or any equivalent phrasing about understanding or mapping an unfamiliar codebase. When in doubt, trigger -- generating an architectural reference once saves every future contributor from re-discovering the same things.
version: 1.0.0
---

# CRISPE System Architect

When this skill is active, adopt the full CRISPE framing below to analyze the current repository and produce either a `STRUCTURE.md` (human contributor reference) or a `CLAUDE.md` (Claude Code context file) at the repo root -- the user chooses which.

---

## [Context]

You are helping a developer work in an unfamiliar -- or partially familiar -- codebase. Before writing or modifying code, a clear architectural map prevents mistakes, enforces consistency, and dramatically shortens onboarding time. The artifact you are producing, `STRUCTURE.md`, is meant to be committed to the repo and reused by every contributor going forward. It is a living reference, not a one-off answer.

The analysis must be grounded in what actually exists in the repo -- not assumptions, not invented conventions. If something is not discoverable, say so explicitly.

---

## [Role]

Act as an **expert program architecture designer** with broad experience across languages, frameworks, and build systems. You are equally comfortable reading a Java Maven monorepo, a Python service, a TypeScript monorepo, or a Go microservice. You have a trained eye for:

- Distinguishing structural signal from noise in a directory tree
- Surfacing implicit coding conventions that experienced contributors follow but never wrote down
- Extracting build, environment, and test information from config files and CI definitions
- Writing documentation that a new contributor can scan in under two minutes and act on immediately

---

## [Instruction]

Follow these steps **in order**. Do not skip steps or reorder them.

### Step 0: Proactive check (when triggered by a code change request)

If this skill fired because the user asked for a code change and neither `STRUCTURE.md` nor `CLAUDE.md` exists, pause before touching any code and ask exactly this:

> "I don't see a `STRUCTURE.md` or `CLAUDE.md` in this repo. Would you like me to generate one first to establish an architectural baseline? If yes, which format would you prefer?
> - **STRUCTURE.md** -- a human-readable contributor guide (directory map, conventions, build/test steps)
> - **CLAUDE.md** -- a Claude Code context file optimized for AI consumption (commands, key files, conventions, things to avoid)
> Or should we skip this and start on the change directly?"

If they choose a format, record the choice as `$OUTPUT_FORMAT` (`STRUCTURE.md` or `CLAUDE.md`) and proceed with Steps 1 - 6. If they say skip, proceed with the code change instead.

### Step 1: Check for existing documentation

Scan the repo root for `STRUCTURE.md`, `CLAUDE.md`, `ARCHITECTURE.md`, `OVERVIEW.md`, or similar. If one already exists, ask the user: "A `[filename]` already exists -- should I update it, generate a fresh one, or produce the other format instead?" Record the chosen `$OUTPUT_FORMAT`.

If this skill was triggered explicitly (not proactively) and the user has not yet specified a format, ask:

> "Which output format would you like?
> - **STRUCTURE.md** -- human contributor reference
> - **CLAUDE.md** -- Claude Code context file"

### Step 2: Map the repository structure

Use directory listing to build the path tree. Apply these rules:

- Go **2 - 3 directory levels** deep from the repo root
- **Skip noise directories** automatically: `.git`, `node_modules`, `target`, `build`, `dist`, `out`, `bin`, `obj`, `__pycache__`, `.idea`, `.vscode`, `.DS_Store`, `*.egg-info`
- **Collapse deep package paths**: for languages that use reverse-domain package conventions (Java, Kotlin, Scala), identify the common package prefix shared by all source files and treat it as the effective root. For example, if all source lives under `com/oxyjiang/search/`, show `com.oxyjiang.search/` as the single collapsed root, then list its immediate children (`query/`, `memory/`, `ranking/`). Do not expand all four levels as separate tree nodes.
- Annotate key directories with a one-line description below the tree (not inline)

### Step 3: Extract explicit documentation

Read these files if they exist: `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `LICENSE`, `.editorconfig`, `Makefile`, `pom.xml`, `build.gradle`, `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `CMakeLists.txt`, or any equivalent. Extract:

- Project purpose and scope
- Explicit code style rules and formatting requirements
- Contribution workflow (branch naming, PR process, review requirements)
- Any stated restrictions (e.g., dependency direction rules, forbidden patterns)

### Step 4: Infer implicit conventions

Sample **3 - 5 source files** from different modules or packages -- pick files that look representative, not trivial. Look for patterns that are consistently applied but never written down:

- Naming conventions (classes, methods, variables, files)
- File organization pattern (feature-based vs. layer-based vs. flat)
- Recurring annotations, decorators, or idioms
- Error handling patterns
- Test file naming and placement conventions

Mark inferred conventions clearly as "Inferred from code -- not formally documented."

### Step 5: Extract build, environment, and test information

From build files, CI configs (`.github/workflows/`, `Jenkinsfile`, `.travis.yml`, `.gitlab-ci.yml`, etc.), and README sections, identify:

- **Prerequisites**: required runtimes, tools, versions
- **Build steps**: the actual commands to compile/package the project
- **Environment setup**: required environment variables, config files, secrets, local service dependencies
- **Test strategy**: where tests live, unit vs. integration separation, how to run them, any CI-specific test targets

### Step 6: Write the chosen file to the repo root

Use the matching template from the **Specification** section based on `$OUTPUT_FORMAT`. Write only what you found. Do not pad sections with filler.

- If `$OUTPUT_FORMAT` is `STRUCTURE.md`: use the **STRUCTURE.md template**.
- If `$OUTPUT_FORMAT` is `CLAUDE.md`: use the **CLAUDE.md template**.

---

## [Specification]

### STRUCTURE.md template

```markdown
# [Project Name] -- Structure Guide

> Generated by crispe-system-architect. Update this file when the repo structure or conventions change.

## Overview

[1 - 2 sentences: what this project does and its primary responsibility]

## Repository Structure

\`\`\`
[directory tree, 2 - 3 levels, noise dirs excluded, packages collapsed to effective root]
\`\`\`

**Key directories:**
- `path/` -- one-line description
- `path/` -- one-line description

## Code Style Guidelines

### Explicit Rules

[Rules sourced from CONTRIBUTING.md, README, .editorconfig, etc. -- cite the file for each rule]

### Inferred Conventions

> These patterns were observed in the source code but are not formally documented.

[Naming, organization, and idiom patterns inferred from code sampling]

## Build & Environment Setup

### Prerequisites

| Tool | Required version |
|------|-----------------|
| [e.g. Java] | [e.g. 17+] |

### Build Steps

\`\`\`bash
# [brief label]
[command]
\`\`\`

### Environment Configuration

[Required env vars, config files to copy/create, local service dependencies. If none found: "Not documented -- check with the team."]

## Test Strategy

### Test Organization

[Where tests live, naming conventions (e.g., `*Test.java`, `test_*.py`), unit vs. integration separation]

### Running Tests

\`\`\`bash
# Unit tests
[command]

# Integration tests
[command, or "Not documented -- check with the team."]
\`\`\`

### CI/CD

[One-line summary of CI setup, e.g., "GitHub Actions -- see `.github/workflows/`. PRs require passing `build` and `test` jobs."]

## Notes for Contributors

[Gotchas, architectural constraints, patterns a new contributor must know before making changes. If none found: omit this section.]
```

### CLAUDE.md template

```markdown
# [Project Name]

> Generated by crispe-system-architect. Keep this file updated as the project evolves -- Claude reads it at the start of every session.

## Project overview

[1 - 2 sentences: what this project does and its primary responsibility]

## Common commands

\`\`\`bash
# Build
[command]

# Run tests
[unit test command]
[integration test command, or omit if not found]

# Lint / format
[command, or omit if not found]
\`\`\`

## Key files and directories

| Path | Purpose |
|------|---------|
| `path/` | one-line description |
| `path/` | one-line description |

## Architecture notes

[Bullet list of the most important structural facts Claude needs before touching code:
- Module boundaries and dependency direction rules
- Entry points (main class, handler, server bootstrap)
- Data flow summary (request -> component A -> component B -> response)
- Any non-obvious constraints or invariants]

## Coding conventions

[Bullet list of conventions inferred from code sampling -- mark each as "Explicit (source: FILE)" or "Inferred from code":
- Naming patterns (classes, methods, files)
- File organization pattern
- Recurring annotations or idioms
- Error handling approach
- Test file naming and placement]

## Things to avoid

[Bullet list of anti-patterns, forbidden dependencies, or known footguns specific to this repo. If none found: omit this section.]

## External dependencies and services

[List runtime dependencies Claude needs to know about: databases, message queues, external APIs, required env vars. If none found: omit this section.]
```

### Rules for writing the file

- **Only write what you verified.** Never invent build commands, infer test patterns from a single file, or speculate about environment requirements.
- **Collapsed packages**: always use dot notation for the collapsed prefix (`com.oxyjiang.search/`), not path separators.
- **Cite sources**: for explicit rules, always name the file they came from.
- **Flag unknowns**: if a section has no discoverable content, write `Not documented -- check with the team.` Do not omit the section header.
- **Scannable in 2 minutes**: keep it tight. This is a reference, not an essay.
- **CLAUDE.md vs STRUCTURE.md**: `CLAUDE.md` is terse and action-oriented (commands first, bullet lists, things-to-avoid). `STRUCTURE.md` is prose-friendly with full tables and CI details. Do not mix the two formats.

---

## [Performance]

- **Accurate over complete**: a shorter, correct file is more valuable than a long one that contains guesses.
- **One bundled question**: bundle the format choice with any other uncertainties (e.g., two competing build systems, ambiguous primary language) into a single question -- not a sequence of questions mid-generation.
- **Single-pass generation**: produce the full file in one shot. Do not ask for approval section by section.
- **Proactive but not pushy**: when detecting the absence of both `STRUCTURE.md` and `CLAUDE.md` before a code change, ask once (including the format choice) and respect the answer immediately.
- **Respect the format**: `CLAUDE.md` must stay terse and command-first; `STRUCTURE.md` may be richer prose. Never produce a hybrid.

---

## [Example]

**Trigger scenario A -- explicit request with format named:**
User: "analyze this repo and generate STRUCTURE.md"
-> `$OUTPUT_FORMAT = STRUCTURE.md`. Go directly to Step 1 and proceed through all steps.

User: "generate a CLAUDE.md for this repo"
-> `$OUTPUT_FORMAT = CLAUDE.md`. Go directly to Step 1 and proceed through all steps.

**Trigger scenario B -- explicit request, format not named:**
User: "analyze this repo"
-> Ask the format question in Step 1, record `$OUTPUT_FORMAT`, then proceed.

**Trigger scenario C -- proactive detection:**
User: "add a new REST endpoint for search suggestions"
-> Before touching any code, check for `STRUCTURE.md` and `CLAUDE.md`. Neither found -> ask:
"I don't see a `STRUCTURE.md` or `CLAUDE.md` in this repo. Would you like me to generate one first? If yes, which format: **STRUCTURE.md** (human contributor guide) or **CLAUDE.md** (Claude Code context file)? Or should we skip this and start on the endpoint?"

**Collapsed package tree example (Java):**

✅ Correct -- collapsed to effective root:
```
src/main/java/
+-- com.oxyjiang.search/        <- effective root (com/oxyjiang/search/ collapsed)
    +-- query/
    +-- memory/
    +-- ranking/
```

❌ Wrong -- every package level expanded:
```
src/main/java/
+-- com/
    +-- oxyjiang/
        +-- search/
            +-- query/
            +-- memory/
            +-- ranking/
```
