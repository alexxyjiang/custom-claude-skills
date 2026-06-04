---
name: crispe-system-architect
description: Expert program architecture analyzer that generates documentation for any code repository. Supports three output styles -- Choice 1: CONTRIBUTING.md + STRUCTURE.md (contribution guide alongside a separate structure reference), Choice 2: CLAUDE.md (single merged AI context file), Choice 3: STRUCTURE.md only (structure reference without a contribution guide). STRUCTURE.md, ARCHITECTURE.md, and OVERVIEW.md are treated as equivalent -- update the one that already exists, or create STRUCTURE.md if none is present. Trigger this skill proactively when the user requests a code change but no documentation file exists in the repo root. Also trigger explicitly when the user says "analyze this repo", "generate STRUCTURE.md", "generate CLAUDE.md", "generate CONTRIBUTING.md", "summarize the codebase", "document the project structure", "what is the architecture of this project", or any equivalent phrasing about understanding or mapping an unfamiliar codebase. When in doubt, trigger -- generating an architectural reference once saves every future contributor from re-discovering the same things.
version: 1.1.0
---

# CRISPE System Architect

When this skill is active, adopt the full CRISPE framing below to analyze the current repository and produce one of three documentation styles at the repo root -- the user chooses which: Choice 1 (CONTRIBUTING.md + STRUCTURE.md), Choice 2 (CLAUDE.md only), or Choice 3 (STRUCTURE.md only).

---

## [Context]

You are helping a developer work in an unfamiliar -- or partially familiar -- codebase. Before writing or modifying code, a clear architectural map prevents mistakes, enforces consistency, and dramatically shortens onboarding time. The artifact(s) you produce are meant to be committed to the repo and reused by every contributor going forward. They are living references, not one-off answers.

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

If this skill fired because the user asked for a code change and no documentation file (`STRUCTURE.md`, `ARCHITECTURE.md`, `OVERVIEW.md`, `CLAUDE.md`, `CONTRIBUTING.md`) exists in the repo root, pause before touching any code and ask exactly this:

> "I don't see any documentation (`STRUCTURE.md`, `CLAUDE.md`, `CONTRIBUTING.md`, or equivalent) in this repo. Would you like me to generate one first? If yes, which style?
> - **Choice 1: CONTRIBUTING.md + STRUCTURE.md** -- a contribution guide (PR process, code of conduct, pointer to the structure file) alongside a separate structure/architecture reference
> - **Choice 2: CLAUDE.md** -- a single Claude Code context file optimized for AI consumption (commands, key files, conventions, things to avoid)
> - **Choice 3: STRUCTURE.md only** -- structure and architecture reference without a separate contribution guide
> Or should we skip this and start on the change directly?"

If they choose a style, record the answer as `$DOC_CHOICE` (1, 2, or 3) and proceed with Steps 1 - 6. If they say skip, proceed with the code change instead.

### Step 1: Check for existing documentation and confirm choice

**1a. Scan** the repo root for `STRUCTURE.md`, `ARCHITECTURE.md`, `OVERVIEW.md`, `CLAUDE.md`, and `CONTRIBUTING.md`.

**1b. Resolve `$STRUCTURE_FILE`**: whichever of `STRUCTURE.md`, `ARCHITECTURE.md`, `OVERVIEW.md` already exists becomes `$STRUCTURE_FILE` (it will be updated). If none exists, `$STRUCTURE_FILE = STRUCTURE.md` (will be created).

**1c. Confirm `$DOC_CHOICE`** -- this must be confirmed before checking any existing files. If `$DOC_CHOICE` was already set in Step 0 (proactive path), skip to 1d. Otherwise, apply the table below exactly -- do not guess outside these cases:

| What the user said | `$DOC_CHOICE` |
|---|---|
| "generate CLAUDE.md" / "generate a CLAUDE.md" | 2 -- infer directly |
| Both CONTRIBUTING.md **and** a structure file named together (e.g., "generate CONTRIBUTING.md and STRUCTURE.md") | 1 -- infer directly |
| Anything else -- including "generate STRUCTURE.md" alone, "generate CONTRIBUTING.md" alone, "analyze this repo", "document the project", or any ambiguous phrasing | **Ask** |

When asking:

> "Which documentation style would you like?
> - **Choice 1: CONTRIBUTING.md + STRUCTURE.md** -- contribution guide + structure reference (split into two files)
> - **Choice 2: CLAUDE.md** -- merged Claude Code context file
> - **Choice 3: STRUCTURE.md only** -- structure reference, no contribution guide"

**1d. Check existing files** (only after `$DOC_CHOICE` is confirmed):
- If `$DOC_CHOICE` is 1 and `CONTRIBUTING.md` already exists: ask "A `CONTRIBUTING.md` already exists -- should I update it or regenerate from the template?"
- If `$DOC_CHOICE` is 2 and `CLAUDE.md` already exists: ask "A `CLAUDE.md` already exists -- should I update it or regenerate from scratch?"
- If `$DOC_CHOICE` is 1 or 3 and `$STRUCTURE_FILE` already exists: it will be updated in place (no need to ask -- the user's choice already implies updating the structure reference).

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

### Step 6: Write the chosen file(s) to the repo root

Use the matching template(s) from the **Specification** section based on `$DOC_CHOICE`. Write only what you found. Do not pad sections with filler.

- If `$DOC_CHOICE` is 1: generate both `CONTRIBUTING.md` (using the **CONTRIBUTING.md template**) and `$STRUCTURE_FILE` (using the **STRUCTURE.md template**). Inside `CONTRIBUTING.md`, the `Code Structure` section must link to `$STRUCTURE_FILE` by its actual filename -- not a hardcoded `STRUCTURE.md` string.
- If `$DOC_CHOICE` is 2: generate `CLAUDE.md` (using the **CLAUDE.md template**).
- If `$DOC_CHOICE` is 3: generate `$STRUCTURE_FILE` only (using the **STRUCTURE.md template**).

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

### CONTRIBUTING.md template

```markdown
# Contributing to [Project Name]

When contributing to this repository, please first discuss the change you wish to make via issue,
email, or any other method with the owners of this repository before making a change.

Please note we have a code of conduct -- follow it in all your interactions with the project.

## Code Structure

For a detailed map of the repository layout, modules, naming conventions, and build/test setup,
see [`$STRUCTURE_FILE`](./$STRUCTURE_FILE).

## Pull Request Process

1. Ensure any install or build dependencies are removed before the end of the layer when doing a
   build.
2. Update the README.md with details of changes to the interface, including new environment
   variables, exposed ports, useful file locations, and container parameters.
3. Increase the version numbers in any example files and the README.md to the version that this
   Pull Request represents. The versioning scheme we use is [SemVer](http://semver.org/).
4. You may merge the Pull Request once you have the sign-off of two other developers, or if you
   do not have permission to do that, request the second reviewer to merge it for you.

## Code of Conduct

### Our Pledge

In the interest of fostering an open and welcoming environment, we as contributors and maintainers
pledge to make participation in our project and community a harassment-free experience for everyone,
regardless of age, body size, disability, ethnicity, gender identity and expression, level of
experience, nationality, personal appearance, race, religion, or sexual identity and orientation.

### Our Standards

Examples of behavior that contributes to creating a positive environment include:

* Using welcoming and inclusive language
* Being respectful of differing viewpoints and experiences
* Gracefully accepting constructive criticism
* Focusing on what is best for the community
* Showing empathy towards other community members

Examples of unacceptable behavior by participants include:

* The use of sexualized language or imagery and unwelcome sexual attention or advances
* Trolling, insulting/derogatory comments, and personal or political attacks
* Public or private harassment
* Publishing others' private information, such as a physical or electronic address, without
  explicit permission
* Other conduct which could reasonably be considered inappropriate in a professional setting

### Our Responsibilities

Project maintainers are responsible for clarifying the standards of acceptable behavior and are
expected to take appropriate and fair corrective action in response to any instances of unacceptable
behavior.

Project maintainers have the right and responsibility to remove, edit, or reject comments, commits,
code, wiki edits, issues, and other contributions that are not aligned to this Code of Conduct, or
to ban temporarily or permanently any contributor for behaviors they deem inappropriate, threatening,
offensive, or harmful.

### Scope

This Code of Conduct applies both within project spaces and in public spaces when an individual is
representing the project or its community. Examples of representing a project or community include
using an official project e-mail address, posting via an official social media account, or acting as
an appointed representative at an online or offline event.

### Enforcement

Instances of abusive, harassing, or otherwise unacceptable behavior may be reported by contacting
the project team at [INSERT EMAIL ADDRESS]. All complaints will be reviewed and investigated and
will result in a response that is deemed necessary and appropriate to the circumstances. The project
team is obligated to maintain confidentiality with regard to the reporter of an incident.

Project maintainers who do not follow or enforce the Code of Conduct in good faith may face
temporary or permanent repercussions as determined by other members of the project's leadership.

### Attribution

This Code of Conduct is adapted from the [Contributor Covenant][homepage], version 1.4,
available at [http://contributor-covenant.org/version/1/4][version].

[homepage]: http://contributor-covenant.org
[version]: http://contributor-covenant.org/version/1/4/
```

### Rules for writing the file(s)

- **Only write what you verified.** Never invent build commands, infer test patterns from a single file, or speculate about environment requirements.
- **Collapsed packages**: always use dot notation for the collapsed prefix (`com.oxyjiang.search/`), not path separators.
- **Cite sources**: for explicit rules, always name the file they came from.
- **Flag unknowns**: if a section has no discoverable content, write `Not documented -- check with the team.` Do not omit the section header.
- **Scannable in 2 minutes**: keep it tight. This is a reference, not an essay.
- **CLAUDE.md vs STRUCTURE.md**: `CLAUDE.md` is terse and action-oriented (commands first, bullet lists, things-to-avoid). `STRUCTURE.md` is prose-friendly with full tables and CI details. Do not mix the two formats.
- **CONTRIBUTING.md structure file link**: the `Code Structure` section must reference `$STRUCTURE_FILE` by its actual resolved filename (`STRUCTURE.md`, `ARCHITECTURE.md`, or `OVERVIEW.md`) -- never hardcode `STRUCTURE.md` if the repo uses a different name.
- **CONTRIBUTING.md is a template, not analysis**: its Code of Conduct and PR Process sections are largely standard. Fill in `[Project Name]` and `[INSERT EMAIL ADDRESS]` from what is discoverable (README, git config, package metadata); leave the placeholders intact if nothing is found. Do not fabricate project-specific PR rules beyond what is documented.

---

## [Performance]

- **Accurate over complete**: a shorter, correct file is more valuable than a long one that contains guesses.
- **One bundled question**: bundle the style choice with any other uncertainties (e.g., two competing build systems, ambiguous primary language) into a single question -- not a sequence of questions mid-generation.
- **Single-pass generation**: produce the full file (or both files for Choice 1) in one shot. Do not ask for approval section by section.
- **Proactive but not pushy**: when detecting the absence of any documentation before a code change, ask once (including the three-choice selection) and respect the answer immediately.
- **Respect the format**: `CLAUDE.md` must stay terse and command-first; `STRUCTURE.md` may be richer prose; `CONTRIBUTING.md` follows the standard template -- never mix these formats or merge them together unless the user explicitly chose Choice 2.

---

## [Example]

### Trigger routing

**Safe to infer (no question needed):**
- User: "generate a CLAUDE.md" -> `$DOC_CHOICE = 2`. Run Steps 2-5, write `CLAUDE.md`.
- User: "generate CONTRIBUTING.md and STRUCTURE.md" -> `$DOC_CHOICE = 1`. Run Steps 2-5, write both.

**Must ask (ambiguous -- do not guess):**
- User: "generate STRUCTURE.md" -> ambiguous; Choice 1 also produces `STRUCTURE.md`. Ask the three-choice question.
- User: "generate ARCHITECTURE.md" -> same ambiguity. Ask.
- User: "generate CONTRIBUTING.md" -> no standalone choice for CONTRIBUTING.md only; could be Choice 1. Ask.
- User: "analyze this repo" / "document the project" / "summarize the codebase" -> no file named. Ask.

**Proactive detection:**
- User: "add a new REST endpoint for search suggestions"
- Check for docs: nothing found -> ask:
  > "I don't see any documentation in this repo. Would you like me to generate one first?
  > - Choice 1: CONTRIBUTING.md + STRUCTURE.md
  > - Choice 2: CLAUDE.md
  > - Choice 3: STRUCTURE.md only
  > Or skip and start on the endpoint?"

---

### Choice 1 compact example -- CONTRIBUTING.md + STRUCTURE.md

**CONTRIBUTING.md** (abbreviated):
```markdown
# Contributing to my-search-service

When contributing to this repository, please first discuss the change you wish to make via issue
or email with the owners before making a change.

## Code Structure

For the full module map, naming conventions, and build/test setup, see
[STRUCTURE.md](./STRUCTURE.md).

## Pull Request Process

1. Remove build artifacts before committing.
2. Update README.md for any interface changes.
3. Bump the version in SemVer format.
4. Get sign-off from two developers before merging.

## Code of Conduct
[... standard CoC sections ...]
```

**STRUCTURE.md** (abbreviated):
```markdown
# my-search-service -- Structure Guide

> Generated by crispe-system-architect.

## Overview

Provides query, ranking, and memory retrieval for the search platform.

## Repository Structure

\`\`\`
src/main/java/
+-- com.oxyjiang.search/
    +-- query/
    +-- ranking/
    +-- memory/
src/test/java/
pom.xml
\`\`\`

## Build & Environment Setup
...
```

---

### Choice 2 compact example -- CLAUDE.md only

**CLAUDE.md** (abbreviated):
```markdown
# my-search-service

> Generated by crispe-system-architect.

## Project overview

Provides query, ranking, and memory retrieval for the search platform.

## Common commands

\`\`\`bash
# Build
mvn package -DskipTests

# Run unit tests
mvn test

# Run integration tests
mvn verify -Pintegration
\`\`\`

## Key files and directories

| Path | Purpose |
|------|---------|
| `src/main/java/com.oxyjiang.search/query/` | Query parsing and execution |
| `src/main/java/com.oxyjiang.search/ranking/` | Ranking algorithms |
| `pom.xml` | Maven build descriptor |

## Architecture notes

- Modules must not import across sibling packages without going through the `api/` facade.
- Entry point: `SearchApplication.java` (Spring Boot).

## Coding conventions

- Explicit (pom.xml): Java 17, Checkstyle enforced on build.
- Inferred from code: service classes suffixed `*Service`, repository classes suffixed `*Repository`.

## Things to avoid

- Do not add direct calls from `ranking/` into `memory/` -- route via `query/`.
```

---

### Choice 3 compact example -- STRUCTURE.md only

**STRUCTURE.md** (abbreviated):
```markdown
# my-search-service -- Structure Guide

> Generated by crispe-system-architect. No CONTRIBUTING.md generated (Choice 3).

## Overview

Provides query, ranking, and memory retrieval for the search platform.

## Repository Structure

\`\`\`
src/main/java/
+-- com.oxyjiang.search/
    +-- query/
    +-- ranking/
    +-- memory/
pom.xml
\`\`\`

## Code Style Guidelines

### Explicit Rules
- Java 17 (source: `pom.xml`)
- Checkstyle profile `google` (source: `pom.xml`)

### Inferred Conventions
> Inferred from code -- not formally documented.
- Service classes suffixed `*Service`; repository classes suffixed `*Repository`

## Build & Environment Setup
...
```

---

### Collapsed package tree example (Java)

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
