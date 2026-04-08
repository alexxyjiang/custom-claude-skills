---
name: crispe-execute
description: Triggers immediately after `crispe-coach` finishes presenting a CRISPE-structured prompt. Ask the user whether to execute it now. If yes, use the assembled CRISPE prompt as the actual instruction and carry out the task directly. If no, leave the prompt for the user to copy. Always invoke this skill at the end of every `crispe-coach` run -- do not skip it.
version: 1.0.1
---

# CRISPE Execute

This skill closes the loop after `crispe-coach`. Once a CRISPE prompt has been built and shown, offer to execute it immediately -- so the user doesn't have to copy and re-paste it themselves.

## When This Skill Applies

Invoke this skill at the end of every `crispe-coach` response, after the CRISPE prompt has been presented to the user.

## Instructions

1. **Identify the CRISPE prompt** -- Locate the structured CRISPE prompt that was just generated in the current conversation. This is the block with labeled `[Context]`, `[Role]`, `[Instruction]`, `[Specification]`, `[Performance]`, and `[Example]` sections.

2. **Ask the user** -- After presenting the CRISPE prompt, append this confirmation:

   > **Ready to run this prompt?**
   > - **yes** -- execute it now
   > - **no** -- keep it to copy and use manually
   > - **yes, but…** -- make a change first, then run it

3. **Handle the response:**

   - **Yes / Y** -- Take the full assembled CRISPE prompt and treat it as your new instruction. Adopt the Role, follow the Instruction and Specification, meet the Performance criteria, and use the Example as a style anchor. Produce the output directly -- no preamble, no re-display of the CRISPE structure.

   - **Yes, but [modification]** -- Apply the requested change to the relevant CRISPE component, show the updated assembled prompt briefly, then execute it.

   - **No / N** -- Acknowledge briefly: *"Got it -- the prompt is ready above whenever you need it."*

   - **Anything else / ambiguous** -- Re-ask with the three options above.

## Why This Matters

`crispe-coach` structures the user's intent; `crispe-execute` lets them act on it without any extra steps. Skipping this step means the user has to manually copy the prompt and re-submit it -- friction that breaks the flow.

## Example

After `crispe-coach` outputs:

```
[Context]
You are assisting a technical professional who needs to communicate complex AI/ML concepts to a non-technical business audience.

[Role]
Act as a senior technology communicator with 10+ years of experience translating engineering concepts for executive stakeholders.

[Instruction]
Explain what machine learning is, how it works at a high level, and why it matters for the business -- without using jargon.

[Specification]
Clear, concise prose. Use one or two grounding analogies. Short paragraphs. Avoid acronyms. Suitable for an email or a single slide note. 200-300 words.

[Performance]
A non-technical reader should finish this and be able to explain the concept in their own words. No sentence should require a CS background to parse.

[Example]
"Think of machine learning like teaching a dog new tricks -- except instead of treats, the model learns from thousands of examples until it can recognise patterns on its own. Once trained, it can spot those patterns in data it has never seen before."
```

Append:

> **Ready to run this prompt?**
> - **yes** -- execute it now
> - **no** -- keep it to copy and use manually
> - **yes, but…** -- make a change first, then run it

**If the user says "yes":** Produce the 200 –300 words machine learning explanation immediately -- don't repeat the CRISPE breakdown.

**If the user says "yes, but make it shorter":** Update [Specification] to target ~100 words, show the updated assembled prompt, then write the shorter explanation.

**If the user says "no":** *"Got it -- the prompt is ready above whenever you need it."*
