# Claude Prompt Improver Skill

**Auto-detect vague prompts and ask targeted clarifying questions — before Claude executes in the wrong direction.**

Works on **Claude.ai web** and **Cowork** (no CLI, no hooks, no Python required).

---

## Why This Exists

When you type something like *"fix the bug"* or *"write the report"*, Claude has to guess what you mean. Most of the time it guesses wrong, you correct it, it tries again — and you've wasted 3 turns that could have been 1.

This skill intercepts vague requests **before** Claude acts on them. It researches available context (your files, conversation history, codebase), then asks the minimum number of targeted questions needed to proceed correctly. Clear prompts pass straight through with zero friction.

---

## Demo

```
You:     "fix the bug"

Claude:  Which issue should I fix?
         ○ TypeError in src/api/users.ts:89 — crashes on login when user is null
         ○ Failing token refresh test in test/auth.test.ts

You:     [selects first option]

Claude:  [fixes the null reference — correct on the first try]
```

```
You:     "Refactor getUserById in src/api/users.ts to use async/await instead of promise chains"

Claude:  [proceeds immediately — prompt is already specific, no questions asked]
```

---

## Features

- **Auto-evaluates every request** when activated via Project Instructions or CLAUDE.md
- **Zero overhead for clear prompts** — the majority of requests pass straight through
- **Context-grounded questions** — options come from real findings, not generic placeholders
- **1–6 targeted questions** scaled to actual complexity (not a questionnaire)
- **`*` bypass prefix** — skip evaluation for any single message
- **Works on Claude.ai web and Cowork** — no CLI, no hooks, no scripts

---

## Installation

### Claude.ai Web

1. Download `prompt-improver.skill` from the Releases tab
2. Go to **claude.ai → Projects → your project → Skills** → upload the file
3. Add to your **Project Instructions**:
   ```
   Always apply the prompt-improver skill before executing any request.
   ```

That's it. Every message in that project is now evaluated automatically.

### Cowork

1. Download `prompt-improver.skill` from the Releases tab
2. Open Cowork → **Settings → Skills** → upload the file
3. Add to your workspace `CLAUDE.md`:
   ```
   Always apply the prompt-improver skill before executing any request.
   ```

---

## How It Works

Before acting on any request, Claude checks:

1. Does the prompt have a **specific target**? (which file, feature, topic, document?)
2. Is the **action** clear? (what exactly should happen?)
3. Is there **enough context** to avoid guessing? (or does conversation history fill the gap?)

If yes → proceed immediately.

If no → research available context first (files, codebase, uploads, conversation history), then ask the minimum questions needed. Questions are always grounded in what Claude actually finds — never generic.

---

## Bypass

Prefix any message with `*` to skip evaluation entirely:

```
* just scaffold something quick, I'll refine it later
```

---

## Skill Architecture

```
prompt-improver/
├── SKILL.md                          # Core evaluation workflow
└── references/
    ├── research-strategies.md        # How to gather context by vagueness type
    └── question-patterns.md          # Question templates and good/bad option examples
```

Uses progressive disclosure — reference files load on demand, keeping per-prompt overhead minimal.

---

## Compared to the Claude Code Hook Version

| Feature | This skill | Claude Code hook |
|---|---|---|
| Works on Claude.ai web | ✅ | ❌ |
| Works on Cowork | ✅ | ❌ |
| Works in Claude Code CLI | ✅ (manual) | ✅ (automatic) |
| Requires terminal / Python | ❌ | ✅ |
| One-line setup | ✅ | ❌ |

The [claude-code-prompt-improver](https://github.com/severity1/claude-code-prompt-improver) by severity1 uses a `UserPromptSubmit` hook for CLI interception. This skill achieves the same outcome through Claude's native skill system — no scripts required.

---

## Keywords

`claude skill` · `claude.ai skill` · `prompt improvement` · `vague prompt detection` · `clarifying questions` · `claude cowork` · `claude prompt optimizer` · `AI prompt clarification` · `claude project instructions` · `prompt engineering`

---

## License

MIT — free to use, modify, and share.
