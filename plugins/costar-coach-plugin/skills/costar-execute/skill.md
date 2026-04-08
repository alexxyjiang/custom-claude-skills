---
name: costar-execute
description: Triggers immediately after `costar-coach` finishes generating a COSTAR-structured prompt. Ask the user whether to execute the prompt now. If yes, use the COSTAR prompt as the actual instruction and carry out the task. If no, leave the prompt for the user to copy and use manually. Always invoke this skill at the end of every `costar-coach` run -- do not skip it.
version: 1.0.1
---

# COSTAR Execute

This skill closes the loop after `costar-coach`. Once a COSTAR prompt has been built and shown, offer to execute it immediately -- so the user doesn't have to copy and re-paste it themselves.

## When This Skill Applies

Invoke this skill at the end of every `costar-coach` response, after the COSTAR prompt has been presented to the user.

## Instructions

1. **Identify the COSTAR prompt** -- Locate the structured COSTAR prompt that was just generated in the current conversation. This is the block with labeled `[Context]`, `[Objective]`, `[Style]`, `[Tone]`, `[Audience]`, and `[Response]` sections.

2. **Ask the user** -- After presenting the COSTAR prompt, append this confirmation:

   > **Ready to run this prompt?**
   > - **yes** -- execute it now
   > - **no** -- keep it to copy and use manually
   > - **yes, but…** -- make a change first, then run it

3. **Handle the response:**

   - **Yes / Y** -- Take the full assembled COSTAR prompt and treat it as your new instruction. Execute the task it describes immediately, producing the output the COSTAR prompt specifies. Produce the output directly -- no preamble, no re-display of the COSTAR structure.

   - **Yes, but [modification]** -- Apply the requested change to the relevant COSTAR component, show the updated assembled prompt briefly, then execute it.

   - **No / N** -- Acknowledge briefly: *"Got it -- the prompt is ready above whenever you need it."*

   - **Anything else / ambiguous** -- Re-ask with the three options above.

## Why This Matters

`costar-coach` structures the user's intent; `costar-execute` lets them act on it without any extra steps. Skipping this step means the user has to manually copy the prompt and re-submit it -- friction that breaks the flow.

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
> - **yes** -- execute it now
> - **no** -- keep it to copy and use manually
> - **yes, but…** -- make a change first, then run it

**If the user says "yes":** Produce the 200 –300 words machine learning explanation immediately -- don't repeat the COSTAR breakdown.

**If the user says "yes, but make it shorter":** Update [Response] to target ~100 words, show the updated assembled prompt, then write the shorter explanation.

**If the user says "no":** *"Got it -- the prompt is ready above whenever you need it."*
