---
name: prompt-improver
description: >
  Evaluates every user request before executing it and asks targeted clarifying questions when a prompt is vague, unclear, or missing key context. Should be applied to ALL requests automatically — if this skill is active, check every new user message before acting. Triggers on any task request: coding, writing, editing, research, file work, or anything else. The goal is to catch ambiguous requests before executing in the wrong direction. If the request is already clear and specific, proceed immediately with zero overhead.
---

# Prompt Improver

> **One-time setup for automatic use** — After installing this skill, add the following line to your **Project Instructions** (Settings → Projects → your project → Instructions):
>
> ```
> Always apply the prompt-improver skill before executing any request.
> ```
>
> That's it. Every message in this project will then be automatically evaluated for clarity before Claude acts on it.

---

## What This Skill Does

Before executing any request, quickly check: *is there enough information here to act correctly?*

- **Clear prompt** → Proceed immediately, no questions asked
- **Vague prompt** → Research available context, ask 1–6 targeted questions, then execute

The goal is to prevent wasted effort — getting a task right the first time beats executing in the wrong direction and backtracking.

---

## Step 1: Evaluate the Prompt

A prompt is **clear enough** if it has:
- A specific target (which file, feature, topic, document, or thing?)
- A clear action (what should happen to it?)
- Enough context to avoid guessing (or earlier conversation fills the gap)

A prompt is **vague** when executing it would require a coin-flip guess — e.g., *"fix the bug"*, *"make it better"*, *"write the report"*, *"update the file"*, *"add tests"*.

**Always check conversation history first.** If earlier messages make the target clear, the prompt is no longer vague — proceed immediately.

**When in doubt, proceed.** Only ask when ambiguity would genuinely cause a wrong or wasted outcome.

**User bypass**: A message starting with `*` skips evaluation entirely. Execute as-is.

---

## Step 2: Research Before Asking (vague prompts only)

Never ask questions based on assumptions. Ground every question in what you actually find. Before formulating questions:

1. **Check conversation history** — often resolves the ambiguity entirely
2. **Look at available files and context** — workspace files, uploaded documents, code
3. **Search for specifics** — error messages, recent changes, failing tests, TODO comments
4. **Note what you found** — your question options should reference real things ("the error in auth.py:89" not "some error")

Use TodoWrite to make your research steps visible before starting.

See [references/research-strategies.md](references/research-strategies.md) for detailed strategies by vagueness type.

---

## Step 3: Ask Targeted Questions

Based on research, formulate 1–6 questions using the `AskUserQuestion` tool. Scale to actual complexity:
- **1–2**: Simple ambiguity (which file? which approach?)
- **3–4**: Moderate complexity (scope + method + validation)
- **5–6**: Complex new feature with multiple genuine decision points

**Question format:**
```
question: Specific question ending with ?
header: Short label (max 12 chars)
multiSelect: false (or true if choices aren't mutually exclusive)
options: 2–4 concrete choices found during research
  label: Concise choice (1–5 words, from actual context)
  description: Brief context or trade-off note
```

Every option should be a real, specific thing from your research — not "Option A / Option B" or "Some other approach".

**Keep it concise — no preamble.** Do NOT open with a multi-sentence explanation of why the prompt is vague. Just ask the question directly. If a brief one-liner helps orient the user, keep it to one short sentence at most.

See [references/question-patterns.md](references/question-patterns.md) for templates and examples.

---

## Step 4: Execute with Full Context

After receiving answers, proceed using:
- The original intent of the request
- The clarification answers
- Everything found during research

Execute as if the request had been fully specified from the start.

---

## Examples

**Vague → Questions → Execution**

User: `"fix the error"`

Research finds: TypeError in `src/api/users.ts:89` (null user object) and a failing test in `test/auth.test.ts` (token refresh).

Question asked:
> Which issue should I fix?
> - TypeError in src/api/users.ts:89 (crashes on login when user is null)
> - Failing token refresh test in test/auth.test.ts

User selects first → Claude fixes the null reference.

---

**Clear → Immediate execution**

User: `"Refactor getUserById in src/api/users.ts to use async/await instead of promise chains"`

Specific target ✓, clear action ✓, success criteria ✓ → Proceed immediately, no questions.

---

**Bypass**

User: `"* just write placeholder tests, don't worry about specifics"`

`*` prefix → skip evaluation, execute directly.

---

## Core Principles

1. **Assume clarity by default** — most prompts pass through without questions
2. **Research before asking** — questions from real context are far more useful than generic ones
3. **Stay lean** — max 6 questions, one decision point per question
4. **Trust conversation history** — always check it before exploring anything else
5. **Zero overhead for clear prompts** — the majority of work should proceed immediately

---

## Reference Files

Only load these when you need detailed guidance:

- **[references/research-strategies.md](references/research-strategies.md)** — how to research context from files, codebases, conversation history, and the web
- **[references/question-patterns.md](references/question-patterns.md)** — question templates and good/bad option examples by scenario type
