# Research Strategies

Load this file when you need detailed guidance on how to research context before asking clarifying questions.

## Order of Operations

Always follow this sequence — stop as soon as you have enough context:

1. **Conversation history** — cheapest, often enough
2. **Workspace files** — read relevant files, check recent changes
3. **Codebase exploration** — grep for errors, patterns, TODOs
4. **Web research** — only when the question requires external knowledge

## Strategy by Vagueness Type

### "Fix the bug / error / problem"
1. Search conversation history for recent error mentions
2. Run: grep for `error`, `Error`, `exception`, `TODO`, `FIXME` in recent files
3. Check git log for recently changed files
4. Look for failing test output if available

**Goal**: Find the most likely candidate(s) so your question is "which of these two specific errors" not "which error?"

### "Add tests"
1. Check conversation history for what was just built
2. Look at the test directory structure — what framework is in use?
3. Find existing test files to match style and coverage patterns
4. Identify untested functions or modules

**Goal**: Know exactly which module/function needs tests and what testing patterns to follow.

### "Make it better / improve this"
1. Read the thing being improved (file, text, function)
2. Check conversation history for any stated goals or constraints
3. Identify specific weak points: performance bottlenecks, unclear logic, style inconsistencies

**Goal**: Replace "make it better" with a list of specific improvements to confirm.

### "Update the file / document"
1. Read the current file content
2. Check what's changed recently (git diff, conversation context)
3. Identify what sections are outdated vs. what should remain

**Goal**: Know which sections need updating and why.

### "Write the report / document"
1. Check conversation history for any stated audience, purpose, or format
2. Look for existing templates or similar documents
3. Identify what data or input is available

**Goal**: Know the audience, length, tone, and what content is available.

## Research Tools Available

| Tool | Best for |
|------|---------|
| Conversation history | Resolving pronouns ("the bug", "the file") |
| Read | Reading specific files mentioned or implied |
| Glob | Finding files by pattern (e.g., `**/*.test.ts`) |
| Grep | Finding errors, patterns, function definitions |
| Bash (git log/diff) | Understanding recent changes |
| WebSearch | External docs, best practices, current info |
| TodoWrite | Creating a visible research plan |

## Creating a Research Plan

For complex vague prompts, make your research plan visible with TodoWrite before starting:

```
Research plan:
1. Check conversation history for recent error mentions
2. Grep for TypeError in src/ directory
3. Check git log for files changed today
4. Read the most recently changed file
```

This helps the user understand what you're doing and why questions are grounded in real findings.

## Documenting Findings

Before formulating questions, summarize what you found in 1–3 sentences. This:
- Shows the user you did real research
- Grounds the question options in specifics
- Helps you avoid generic options like "Option A / Option B"

Example: "I found two likely candidates: a TypeError on line 89 of `src/api/users.ts` where `user` can be null, and a failing test in `test/auth.test.ts` for the token refresh flow."
