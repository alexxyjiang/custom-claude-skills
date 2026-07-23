---
name: prompt-convert-trigger
description: Automatically detects tasks that would benefit from structured prompt engineering and converts them into COSTAR or CRISPE prompts. Triggers proactively when the user's request involves complexity, vagueness, multi-step work, or spans multiple concerns. Make sure to use this skill whenever you notice the user's request would benefit from structured planning — don't wait for them to explicitly ask. Also applies to Chinese-language requests showing the same signals: multi-goal phrasing ("修复X同时让Y也能工作"), uncertainty markers ("我不确定怎么", "可能是", "我觉得"), vague action verbs ("改进", "优化", "整理", "搞定"), or requests spanning multiple files/systems.
version: 1.2.0
argument-hint: "[framework] (optional) -- force a specific framework (costar|crispe); auto-detected from request signals if omitted"
---

# Prompt Convert Trigger

## Context

You are operating inside a Claude Code session where the user is about to give you a task. Before diving in, you have the opportunity to detect whether the request would benefit from structured prompt engineering — and if so, recommend a framework and delegate to the appropriate coach skill.

Complex or vague tasks frequently underperform because requirements are incomplete, success criteria are unclear, or the scope isn't fully surfaced. Structured frameworks like COSTAR and CRISPE solve this by forcing requirements into explicit, reviewable sections before work begins.

## Objective

Proactively scan every incoming user request for signals that structured planning would improve outcomes. Detect which framework fits best, default to COSTAR when ambiguous, and ask the user for confirmation before delegating.

Your detection must be lightweight and non-disruptive: one brief recommendation, then immediate hand-off.

## Style

Concise and advisory. Two to three sentences maximum per recommendation. Do not lecture — one clear signal and one clear recommendation is enough.

## Tone

Helpful and proactive, but never pushy. If the user declines or says "just do it", respect that immediately and proceed with the task as-is.

## Audience

The Claude agent reading this skill definition. These are internal instructions for how Claude should behave, not content for the end user.

## Response

### Framework Selection

**Default to COSTAR** when the task type is ambiguous or doesn't clearly fit CRISPE signals.

#### Trigger COSTAR when the request involves:
- UI, UX, or user-facing features
- Product features with user interaction (forms, flows, dashboards)
- Documentation, writing, or content generation
- Design decisions, architecture, or creative direction
- Refactoring or "clean up" without a specific technical target
- Any request with a stakeholder, user impact, or release signal ("users see", "for the release")
- Vague improvement verbs with no technical specificity: *improve*, *make it better*, *clean up*, *polish*

#### Trigger CRISPE when the request involves:
- PySpark, Spark SQL, or data pipeline work
- Backend API development or database queries
- Data transformations, ETL, or HDFS/warehouse operations
- Infrastructure, DevOps, or CI/CD tasks
- Technical investigation or root-cause debugging
- System integration or inter-service wiring
- Vague verbs pointing at a technical domain: *investigate*, *debug*, *profile*, *optimize throughput*

### General Trigger Signals (apply to both frameworks)

**Strong signals** — trigger on any one of these alone:

1. **Multi-goal phrasing**: Two or more distinct outcomes — e.g., "fix X and also make Y work"
2. **3+ bullets or numbered items**: User enumerates the task in three or more points
3. **Spans 2+ files, modules, or systems**: Task crosses a file or service boundary
4. **Research + change combo**: Requires both investigation AND a code or config change

**Soft signals** — consider triggering when these appear, especially in combination:

5. **Uncertain framing**: "I think", "not sure how to", "the issue might be", "ideally", "somehow"
6. **Vague action verbs with no clear spec**: *improve*, *refactor*, *set up*, *handle*, *make it work*
7. **Stakeholder or impact signal**: "users see", "for the release", "the team needs by Thursday"

### Execution Flow

1. Detect any signal from the lists above
2. Select framework: COSTAR or CRISPE — default to COSTAR when unsure
3. Present a brief recommendation:
   ```
   This looks like it could benefit from [COSTAR/CRISPE] — it involves [one-line reason].
   Want me to structure it first, or should I just dive in?
   ```
4. On user agreement → invoke the chosen coach skill (`/costar-coach` or `/crispe-coach`)
5. Hand off completely — your role ends here

### Examples

| Request | Signal | Framework |
|---|---|---|
| "Add auth to our React app with JWT and role-based permissions" | Multi-goal + multi-file + user-facing | COSTAR |
| "Refactor the payment module to be more maintainable" | Vague verb, no spec | COSTAR (default) |
| "We need this ready for Thursday's release" | Stakeholder/timeline signal | COSTAR (default) |
| "Write a PySpark job that reads HDFS, filters, joins, outputs Parquet" | Data pipeline, multi-step | CRISPE |
| "Investigate the API latency spike and fix it" | Technical investigation + change | CRISPE |
| "I think the issue might be in the auth middleware" | Uncertain framing + technical domain | CRISPE |

### Chinese Trigger Signals (中文触发信号)

Apply the same detection logic to Chinese-language requests. Key signals:

**Strong signals (任一即触发):**
- Multi-goal phrasing: "修复X同时让Y也能工作" / "既要...又要..."
- 3+ enumerated items in Chinese
- Spans multiple files or systems (cross-service)

**Soft signals (组合出现时考虑触发):**
- Uncertainty markers: "我不太确定", "可能是", "我觉得", "大概是", "不知道怎么"
- Vague action verbs: "改进", "优化", "整理", "搞定", "弄一下", "处理"
- Stakeholder or deadline signals: "用户看到", "下周要上线", "团队需要"

**Framework selection for Chinese requests:**
- Default to COSTAR for UI/UX, writing, product features, design ("做一个...", "设计一个...")
- Default to CRISPE for data pipelines, backend APIs, infrastructure, debugging ("调查...", "修复...", "优化查询...")

**Should NOT trigger on Chinese requests:**
- "修复第42行的拼写错误" — single, fully specified
- "跑一下测试" — mechanical, no ambiguity
- "哪些文件处理认证？" — exploratory question, no task

### Should NOT Trigger

| Request | Reason |
|---|---|
| "Fix the typo on line 42 in README.md" | Single, trivial, fully specified |
| "Add a console.log to debug this function" | One-line, no ambiguity |
| "What files handle authentication?" | Exploratory question, no task |
| "Run the tests" | Mechanical, nothing to clarify |
