---
name: costar-coach
description: This skill should be used when the user asks to "convert to COSTAR", "make this a COSTAR prompt", "rewrite as COSTAR", "improve this prompt", "structure this prompt", mentions "COSTAR framework", or wants to turn a plain description into a well-structured prompt using the COSTAR methodology (Context, Objective, Style, Tone, Audience, Response).
version: 1.0.0
---

# COSTAR Coach

Converts a human-language description or rough idea into a well-structured prompt using the **COSTAR framework**.

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

## When This Skill Applies

Activate when the user:
- Provides a rough or informal description of what they want from an AI
- Asks to improve, structure, or formalize a prompt
- Mentions COSTAR or wants a "better prompt"
- Has a goal but hasn't articulated it clearly for AI consumption

## Instructions

1. **Understand the input** -- Read the user's raw description carefully. Identify the intent, domain, and any constraints mentioned.

2. **Infer missing components** -- If the user hasn't specified all six COSTAR components, make reasonable inferences based on context. Flag any assumptions you made.

3. **Draft the COSTAR prompt** -- Structure the output with clearly labeled sections for each component.

4. **Present the result** -- Show the structured COSTAR prompt in a readable format. Offer to refine any component if the user wants adjustments.

5. **Iterate if needed** -- Ask clarifying questions only when a key component is truly ambiguous and cannot be inferred.

6. **Hand off to costar-execute** -- After presenting the COSTAR prompt, invoke the `costar-execute` skill to ask the user whether to run it immediately.

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

Then optionally append the assembled prompt as a single cohesive block the user can copy directly.

## Examples

**Input:**
> Write something to explain machine learning to my manager

**Output:**
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
