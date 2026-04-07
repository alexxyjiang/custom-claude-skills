---
name: costar-execute
description: Triggers immediately after `costar-coach` finishes generating a COSTAR-structured prompt. Ask the user whether to execute the prompt now. If yes, use the COSTAR prompt as the actual instruction and carry out the task. If no, leave the prompt for the user to copy and use manually. Always invoke this skill at the end of every `costar-coach` run -- do not skip it.
version: 1.0.0
---

# COSTAR Execute

This skill runs as the natural follow-up to `costar-coach`. After a COSTAR prompt has been constructed and shown to the user, offer to execute it immediately.

## When This Skill Applies

Invoke this skill at the end of every `costar-coach` response, after the COSTAR prompt has been presented to the user.

## Instructions

1. **Identify the COSTAR prompt** -- Locate the structured COSTAR prompt that was just generated in the current conversation. This is the block with labeled `[Context]`, `[Objective]`, `[Style]`, `[Tone]`, `[Audience]`, and `[Response]` sections.

2. **Ask the user** -- After presenting the COSTAR prompt, append this confirmation:

   > **Ready to run this prompt?**
   > - Type **yes** (or **y**) to execute it now.
   > - Type **no** (or **n**) to keep it for manual use.

3. **Handle the response:**

   - **Yes / Y** -- Take the full assembled COSTAR prompt and treat it as your new instruction. Execute the task it describes immediately, producing the output the COSTAR prompt specifies. Do not re-show the COSTAR structure; just do the work and return the result.

   - **No / N** -- Acknowledge briefly. Let the user know the prompt is ready to copy above. Do not execute anything.

   - **Anything else / ambiguous** -- Gently re-ask with the two options.

## Why This Matters

`costar-coach` helps the user *structure* their intent; `costar-execute` closes the loop by letting them act on it immediately. Without this step, the user has to manually copy the prompt and re-submit it -- extra friction that breaks the flow.

## Example

After `costar-coach` outputs:

```
[Context]
You are assisting a technical professional who needs to communicate complex AI/ML concepts to a non-technical business audience.

[Objective]
Explain what machine learning is, how it works at a high level, and why it matters for the business -- without using jargon.

[Style]
Clear, concise prose with an analogy or two to ground abstract concepts. Use short paragraphs.

[Tone]
Professional yet approachable. Confident but not condescending.

[Audience]
A business manager with no technical background in AI or software development.

[Response]
A 200 - 300 words explanation suitable for an email or brief slide note.
```

Append:

> **Ready to run this prompt?**
> - Type **yes** to execute it now.
> - Type **no** to keep it for manual use.

If the user says **yes**, produce the 200 –300 words machine learning explanation immediately -- don't repeat the COSTAR breakdown.

If the user says **no**, respond with something like: "Got it -- the prompt is ready above whenever you need it."
