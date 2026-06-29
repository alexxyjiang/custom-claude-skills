---
name: costar-coach
description: This skill should be used when the user asks to "convert to COSTAR", "make this a COSTAR prompt", "rewrite as COSTAR", "improve this prompt", "structure this prompt", mentions "COSTAR framework", or wants to turn a plain description into a well-structured prompt using the COSTAR methodology (Context, Objective, Style, Tone, Audience, Response).
version: 1.2.0
argument-hint: "[file-path] (optional) -- path to a prompt file to load and optimize using the COSTAR framework"
---

# COSTAR Coach

Converts a human-language description or idea into a well-structured prompt using the **COSTAR framework**, then offers to execute it immediately so the user doesn't have to copy and re-paste it themselves.

## COSTAR Framework

The COSTAR framework breaks down prompt engineering into six essential elements:

| Component             | Description |
|-----------------------|-------------|
| **C** -- Context      | Background information that helps the AI understand the situation. Give your model the background information it needs. This helps ground the AI in the specific situation you're dealing with, reducing irrelevant outputs. For example, telling the model you're working with healthcare data sets different parameters than if you're analyzing social media posts. |
| **O** -- Objective    | The specific task or goal you want the AI to accomplish. Tell the model exactly what you want it to accomplish. Clear objectives prevent the model from wandering off-task. This could be "summarize this research paper" or "find inconsistencies in this code." |
| **S** -- Style        | Specify how you want the information presented. This might be "technical and detailed" or "simple and concise" depending on your needs. The style instruction shapes how information is organized and delivered, especially the writing style, format, or approach to use. |
| **T** -- Tone         | Set the emotional quality of the response. Whether you need formal, friendly, cautious, or enthusiastic responses, explicitly stating the tone helps ensure the output feels right for your use case. (e.g., professional, friendly, direct) |
| **A** -- Audience     | Identify who will be reading or using this output. The model can tailor vocabulary, complexity, and examples based on whether the audience is technical developers, business executives, or the general public. |
| **R** -- Response     | The expected format or structure of the output, whether that's paragraphs of text, JSON, CSV, or something else. This is particularly important when integrating AI outputs into existing systems or workflows. Specifying the response format helps ensure the output is immediately usable and reduces the need for additional parsing or formatting steps. |

## File Argument

If a file path is provided as an argument (e.g., `/costar-coach path/to/prompt.md`):

1. **Load the file** -- Read the contents of the file at the given path.
2. **Use it as input** -- Treat the file contents as the raw prompt or description to be optimized, instead of waiting for the user to describe it in chat.
3. **Proceed normally** -- Apply all the steps below to convert and structure the loaded content into a COSTAR prompt.

## When This Skill Applies

Activate when the user:
- Provides a rough or informal description of what they want from an AI
- Asks to improve, structure, or formalize a prompt
- Mentions COSTAR or wants a "better prompt"
- Has a goal but hasn't articulated it clearly for AI consumption

## Coaching Style

- **Patient** -- take time to fully understand the user's goal before structuring the prompt; don't rush to produce a draft if something is unclear.
- **Ask, don't guess** -- when a component is missing or ambiguous, ask the user a focused question rather than making assumptions. One clear question beats a flagged guess.

## Instructions

1. **Understand the input** -- Read the user's raw description carefully. Identify the intent, domain, and any constraints mentioned.

2. **Clarify missing components** -- If the user hasn't specified all six COSTAR components, ask a focused question to fill the gap rather than guessing. Ask one question at a time; don't bombard the user.

3. **Draft the COSTAR prompt** -- Structure the output with clearly labeled sections for each component. Always embed these behavioral constraints in the draft: the model should be patient and work through issues without rushing; and when something is unclear, it should ask one focused question rather than guess. Place patience in **[Tone]** and the ask-don't-guess rule in **[Style]**, unless the user's input provides a more natural home for them.

4. **Present the result** -- Show the structured COSTAR prompt in a readable format. Offer to refine any component if the user wants adjustments.

5. **Iterate patiently** -- Continue refining through questions until the prompt accurately reflects what the user wants. Never assume you have everything you need.

6. **Offer to execute** -- After presenting the COSTAR prompt, append this confirmation:

   > **Ready to run this prompt?**
   > - **yes** -- execute it now
   > - **no** -- keep it to copy and use manually
   > - **yes, but... ** -- make a change first, then run it

7. **Handle the response:**

   - **Yes / Y** -- Take the full assembled COSTAR prompt and treat it as your new instruction. Execute the task it describes immediately, producing the output the COSTAR prompt specifies. Produce the output directly -- no preamble, no re-display of the COSTAR structure.

   - **Yes, but [modification]** -- Apply the requested change to the relevant COSTAR component, show the updated assembled prompt briefly, then execute it.

   - **No / N** -- Acknowledge briefly: *"Got it -- the prompt is ready above whenever you need it."*

   - **Anything else / ambiguous** -- Re-ask with the three options above.

## Output Format

Present the result as:

```
[Context]
<background and situation>

[Objective]
<the specific task>

[Style]
<writing style or format guidance>

[Tone]
<desired tone>

[Audience]
<intended reader/user>

[Response]
<expected output format>
```

### Assembled prompt (always include)

The assembled prompt is a single cohesive block the user can copy directly.

```
<all six components woven into a single, ready-to-use prompt block>
```

## Example

**Input:**
> Write something to explain machine learning to my manager

**Output:**
```
[Context]
You are assisting a technical professional who needs to communicate complex AI/ML concepts to a non-technical business audience.

[Objective]
Explain what machine learning is, how it works at a high level, and why it matters for the business -- without using jargon.

[Style]
Clear, concise prose with an analogy or two to ground abstract concepts. Use short paragraphs. When something is unclear, ask one focused question rather than guess.

[Tone]
Professional yet approachable. Confident but not condescending. Patient -- work through the reader's confusion step by step without rushing.

[Audience]
A business manager with no technical background in AI or software development.

[Response]
A 200 - 300 words explanation suitable for an email or brief slide note.
```

Then append:

> **Ready to run this prompt?**
> - **yes** -- execute it now
> - **no** -- keep it to copy and use manually
> - **yes, but... ** -- make a change first, then run it

**If the user says "yes":** Produce the 200 -- 300 word machine learning explanation immediately -- don't repeat the COSTAR breakdown.

**If the user says "yes, but make it shorter":** Update [Response] to target ~100 words, show the updated assembled prompt, then write the shorter explanation.

**If the user says "no":** *"Got it -- the prompt is ready above whenever you need it."*
