---
name: promptify
description: Optimize prompts for clarity and effectiveness. Use when user says "improve this prompt", "optimize my prompt", "make this clearer", or provides vague/unstructured prompts. Intelligently dispatches subagents for codebase research, clarifying questions, or web search as needed.
---

# Prompt Optimizer

Transform prompts into clear, effective ones. Model-agnostic.

## Modifiers (parse from ARGUMENTS)

- **+ask** → Force clarifying questions
- **+deep** → Force codebase exploration
- **+web** → Force web search

No modifiers? Auto-detect what's needed.

## Auto-Detection Triggers

| Trigger | Signals |
|---------|---------|
| **Codebase Research** | "this project", "our API", specific files/functions, "integrate", "extend", "refactor" |
| **Clarification** | Ambiguous ("make it better"), multiple interpretations, missing constraints, vague pronouns |
| **Web Research** | "best practices", "latest", external APIs/libraries, framework patterns, year references |

## Subagent Dispatch

When research or clarification is needed (via modifiers or auto-detection):

1. Announce which subagent tasks are needed and why
2. Launch subagents **in parallel** using the Agent tool, providing the relevant instructions from the sections below
3. Synthesize all findings
4. Optimize the prompt with gathered context

When multiple subagent tasks are needed, always launch them simultaneously in a single response to maximize efficiency.

---

## Codebase Research Instructions

When codebase research is triggered (+deep or auto-detected), spawn a subagent with these instructions:

> Explore the codebase to gather relevant context for the prompt being optimized.
>
> **Exploration Process:**
> 1. **Identify Scope** - Parse domain terms from the prompt; identify likely file patterns (e.g., "auth" → `**/auth*`, `**/login*`)
> 2. **Structure Discovery** - Use Glob and LS to understand project structure, naming conventions, module boundaries
> 3. **Pattern Analysis** - Use Grep and Read to find existing implementations, API patterns, error handling approaches, testing patterns
> 4. **Convention Check** - Look for README.md, CONTRIBUTING.md, CLAUDE.md, style guides, .editorconfig, package.json scripts, Makefile
>
> **Output format:**
> ```
> ## Codebase Context
> **Project Type:** [framework/language/stack]
> **Relevant Files:**
> - `path/to/file` - [brief description]
> **Existing Patterns:**
> - [Pattern]: [how it's done in this codebase]
> **Conventions:**
> - [Naming, structure, or style conventions discovered]
> **Key Constraints:**
> - [Any limitations or requirements found in docs/config]
> ```
>
> Only include context directly relevant to the prompt. Prefer specific examples over general descriptions. Keep output concise.

## Clarification Instructions

When clarification is triggered (+ask or auto-detected), use the **AskUserQuestion** tool directly (no subagent needed) with 1-3 targeted questions.

**Ask ONLY what's truly unclear.** Skip questions with obvious answers from context.

### By Prompt Type

- **Coding**: Language/framework? Error handling requirements? Performance constraints? Testing expectations?
- **Writing**: Audience? Tone (formal/casual/technical)? Length constraints? Purpose (inform/persuade/entertain)?
- **Analysis**: Criteria that matter most? Depth (overview vs detailed)? Comparison baseline?
- **Design**: Problem being solved? Users? Constraints (technical, time, budget)?
- **General**: What will this output be used for? Hard constraints? What does success look like?

### Question Quality

**Good questions:** Specific, actionable, have meaningful different answers, directly affect the output, can't be inferred from context.

**Bad questions (avoid):** Already answered in context, obvious from the prompt, too broad ("Tell me more"), leading or assumptive.

After receiving answers, derive specific requirements from the clarifications.

## Web Research Instructions

When web research is triggered (+web or auto-detected), spawn a subagent with these instructions:

> Search the web for current best practices, documentation, and modern approaches relevant to the prompt.
>
> **Research Process:**
> 1. **Identify Search Topics** - Parse technologies/frameworks, patterns needed, external services/APIs, domain-specific standards
> 2. **Execute Searches** - Use WebSearch for "[technology] best practices [current year]", "[framework] [pattern] recommended approach", "[API/service] documentation"
> 3. **Validate & Synthesize** - Prioritize official documentation, cross-reference multiple sources, note version-specific information, flag conflicting advice
>
> **Output format:**
> ```
> ## Web Research Results
> **Topic:** [what was researched]
> **Best Practices Found:**
> 1. [Practice] - Source: [URL/description]
> **Relevant Documentation:**
> - [Doc]: [key takeaway]
> **Current Standards:**
> - [Standard or pattern with context]
> **Version Notes:**
> - [Any version-specific considerations]
> ```
>
> Prioritize recent sources. Cite official docs over blog posts. Note deprecated patterns to avoid. Keep findings actionable and relevant. Don't over-research.

---

## Core Contract (every prompt needs all four)

| Element | If Missing |
|---------|------------|
| **Role** | Add persona with expertise |
| **Task** | Make action specific |
| **Constraints** | Infer from context |
| **Output** | Specify format/structure |

## Process

1. **If image**: Analyze, incorporate context
2. **Detect type**: coding/writing/analysis/creative/data
3. **Convert output→process**: "Write X" → "Analyze → Plan → Implement → Validate"
4. **Strip fluff**: "please", "I want you to", filler, apologies
5. **Apply contract**: Verify all 4 elements
6. **Add structure**: XML tags for complex prompts (`<context>`, `<task>`, `<format>`)

## Type Focus

- **Coding**: Specs, edge cases, framework
- **Writing**: Tone, audience, length
- **Analysis**: Criteria, depth
- **Creative**: Constraints, novelty
- **Data**: I/O format, edge cases

## Techniques (apply as needed)

- Replace vague words with criteria
- Add "Do NOT..." constraints
- Add role/persona for expertise tasks
- "Think step by step" for reasoning

## Output

1. Optimized prompt in code block
2. `echo 'PROMPT' | pbcopy`
3. 2-3 sentence explanation
