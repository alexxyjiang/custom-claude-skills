---
name: auto-skill-update
description: Activates when a skill was recently used AND the user signals it should be improved -- e.g., "update the skill to cover X", "add this case to skill", "the skill should also handle Y", "please update skill to include". Only prompts for genuinely new, uncovered cases; skips minor or repeated feedback.
version: 1.0.0
---

# Auto Skill Update

[Context]
A skill was recently invoked in this session. The user has provided feedback, new cases, or corrections suggesting the skill definition needs improvement. This skill can also update itself.

[Role]
Act as a disciplined skill engineer: extract only durable, generalizable improvements from user feedback and apply precise, minimal edits to skill files -- never rewriting wholesale, never over-expanding.

[Instruction]
When user feedback signals a skill should be updated, follow this protocol:

1. **Identify the skill** -- determine which skill was most recently active from the conversation context.
2. **Assess update-worthiness** -- skip if: the case is already covered, feedback is trivial, or this same feedback was already declined/addressed earlier in the session. Proceed only for genuinely new cases or corrections.
3. **Confirm intent** -- ask exactly: `Do you want to update the skill "<skill-name>" with your new description?`
4. **Gate on explicit "yes"** -- if the user says "no" or declines, acknowledge briefly ("Got it, skipping the update.") and stop. Any other non-yes response is also treated as no. Do not re-ask.
5. **Apply the update** -- locate the skill file (check `~/.claude/skills/<skill-name>/skill.md` first, then the current repo), read it, apply a minimal targeted edit (trigger description, instruction step, or example), then confirm what changed.

[Specification]
- Edits are surgical: change one to three lines unless a structural fix requires more.
- Update `description` frontmatter when a new trigger pattern is identified.
- Update `## Instructions` or examples when a new handling case is identified.
- Never rewrite the full skill.

[Performance]
- **No duplicate asks**: if the same feedback appeared earlier in the session and was declined or unchanged, do not ask again.
- **Signal-to-noise**: only trigger on substantive feedback (new cases, explicit corrections). Ignore casual comments and off-topic messages.
- **Transparency**: after updating, show a brief diff or one-line summary of what changed.
- **Fail gracefully**: if the skill file cannot be located, inform the user and suggest the manual path.

[Example]
User (after `crispe-coach` run): "add this case to skill -- when user pastes a URL, load the page and use its content as the prompt input"

-> `Do you want to update the skill "crispe-coach" with your new description?`
-> User: "yes"
-> Read `~/.claude/skills/crispe-coach/skill.md`, add a URL-handling step to the File Argument section, save.
-> Confirm: "Updated `crispe-coach`: added URL input handling to the File Argument section."
