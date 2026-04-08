---
name: crispe-coach
description: This skill should be used when the user asks to "convert to CRISPE", "make this a CRISPE prompt", "rewrite as CRISPE", "improve this prompt", "structure this prompt", mentions "CRISPE framework", or wants to turn a plain description into a well-structured prompt using the CRISPE methodology (Context, Role, Instruction, Specification, Performance, Example).
version: 1.0.1
argument-hint: "[file-path] (optional) — path to a prompt file to load and optimize using the CRISPE framework"
---

# CRISPE Coach

Converts a human-language description or idea into a well-structured prompt using the **CRISPE framework**, then hands off to `crispe-execute` so the user can run it immediately.

## CRISPE Framework

The CRISPE framework breaks down prompt engineering into six essential elements:

| Component                | Description |
|--------------------------|-------------|
| **C** -- Context         | Set the scene: business background, audience, goals, constraints -- anything a consultant would need to know walking in cold. The richer the context, the less the model has to guess. |
| **R** -- Role            | Assign a precise persona. Specificity matters enormously: "senior data analyst with 15 years in healthcare" produces dramatically better output than "be an analyst." Include job title, years of experience, industry, and relevant specialisation. |
| **I** -- Instruction     | State the task clearly and explain *why* it matters. Give the model a mission with meaning: what to do, and the purpose behind it. |
| **S** -- Specification   | Define the output in concrete terms: format (bullet points, JSON, executive report), tone, length, structure. You decide the shape -- don't leave it to chance. |
| **P** -- Performance     | Describe what a great result looks like: KPIs, quality bars, pitfalls to avoid. Set the bar high and make it measurable. |
| **E** -- Example         | Give the model a pattern to follow -- a snippet of exemplary output, a reference style, or a "here's what good looks like" anchor. This is one of the highest-leverage additions to any prompt. |

## File Argument

If a file path is provided as an argument (e.g., `/crispe-coach path/to/prompt.md`):

1. **Load the file** -- Read the contents of the file at the given path.
2. **Use it as input** -- Treat the file contents as the raw prompt or description to be optimized, instead of waiting for the user to describe it in chat.
3. **Proceed normally** -- Apply all the steps below to convert and structure the loaded content into a CRISPE prompt.

## When This Skill Applies

Activate when the user:
- Provides a rough or informal description of what they want from an AI
- Asks to improve, structure, or formalize a prompt
- Mentions CRISPE or wants a "better prompt"
- Has a goal but hasn't articulated it clearly for AI consumption

## Instructions

1. **Understand the input** -- Read the user's raw description carefully. Identify the intent, domain, and any constraints mentioned.

2. **Infer missing components** -- If the user hasn't specified all six CRISPE components, make reasonable inferences based on context. Flag any assumptions you made.

3. **Draft the CRISPE prompt** -- Structure the output with clearly labeled sections for each component.

4. **Present the result** -- Show the structured CRISPE prompt in a readable format. Offer to refine any component if the user wants adjustments.

5. **Iterate if needed** -- Ask clarifying questions only when a key component is truly ambiguous and cannot be inferred.

6. **Hand off to crispe-execute** -- After presenting the CRISPE prompt, invoke the `crispe-execute` skill to ask the user whether to run it immediately.

## Output Format

Present the result as:

```
[Context]
<background and situation>

[Role]
<the persona the model should adopt>

[Instruction]
<the specific task and why it matters>

[Specification]
<output format, tone, length, structure>

[Performance]
<what success looks like -- quality bars, pitfalls to avoid>

[Example]
<a snippet of exemplary output or a reference style to follow>
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
