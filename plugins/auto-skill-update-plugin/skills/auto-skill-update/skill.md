---
name: auto-skill-update
description: Trigger this skill proactively when the user signals any skill should be improved -- e.g., "update the skill to cover X", "add this case to skill", "the skill should also handle Y", "please update skill to include", "that should be in the skill", "fix the skill", "the skill missed this case", or any phrasing about correcting, extending, or improving a skill definition. Invoke immediately on these signals -- do NOT pre-assess worthiness before invoking; that assessment happens inside this skill.
version: 1.1.0
argument-hint: "[skill-name] (optional) -- name of the skill to update; inferred from context if omitted"
---

# Auto Skill Update

[Context]
The user has signaled that a skill definition needs improvement. This may follow a recent skill invocation in the current session, or the user may reference a skill by name without having just used it. The feedback may be a new trigger pattern, a missing handling case, a correction, or an example to add. This skill can also update itself.

[Role]
Act as a disciplined skill engineer: extract only durable, generalizable improvements from user feedback and apply precise, minimal edits to skill files -- never rewriting wholesale, never over-expanding.

[Instruction]
When invoked, follow this protocol in order:

1. **Identify the skill** -- determine which skill the feedback targets. Check conversation context for the most recently active skill, or the skill the user named explicitly.
2. **Assess update-worthiness** -- skip if: the case is already covered, the feedback is trivial or off-topic, or this same feedback was already declined/addressed earlier in the session. Proceed for genuinely new cases or corrections.
3. **Confirm intent** -- ask exactly: `Do you want to update the skill "<skill-name>" with your new description?`
4. **Gate on explicit "yes"** -- if the user says "no" or declines, acknowledge briefly ("Got it, skipping the update.") and stop. Any other non-yes response is treated as no. Do not re-ask.
5. **Apply the update** -- locate the skill file (check `~/.claude/skills/<skill-name>/skill.md` first, then the current repo under `plugins/`), read it, apply a minimal targeted edit (trigger description, instruction step, or example), then confirm what changed. If both copies exist, update both to keep them in sync.

[Specification]
- Edits are surgical: change one to three lines unless a structural fix requires more.
- Update `description` frontmatter when a new trigger pattern is identified.
- Update `[Instruction]` or `[Example]` sections when a new handling case is identified.
- Never rewrite the full skill.
- When both `~/.claude/skills/<skill-name>/skill.md` and a plugin repo copy exist, update both files.

[Performance]
- **Invoke eagerly, assess inside**: do not pre-filter before invoking -- invoke on any improvement signal and run the worthiness assessment in Step 2.
- **No duplicate asks**: if the same feedback appeared earlier in the session and was declined or unchanged, do not ask again.
- **Transparency**: after updating, show a brief diff or one-line summary of what changed.
- **Fail gracefully**: if the skill file cannot be located, inform the user and suggest the manual path (`~/.claude/skills/<skill-name>/skill.md`).

[Example]
User (after `crispe-coach` run): "add this case to skill -- when user pastes a URL, load the page and use its content as the prompt input"

-> `Do you want to update the skill "crispe-coach" with your new description?`
-> User: "yes"
-> Read `~/.claude/skills/crispe-coach/skill.md`, add a URL-handling step to the File Argument section, save.
-> Confirm: "Updated `crispe-coach`: added URL input handling to the File Argument section."
