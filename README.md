# Claude Prompt Improver Skill

A skill that catches vague prompts **before** Claude acts on them — researches your context, asks 1–6 targeted questions, then executes correctly on the first try.

Works on **Claude.ai web** and **Cowork**. No CLI, no Python, no hooks. One-line setup.

---

## The Problem

You type `"fix the bug"`. Claude picks one. Wrong one. You clarify. It tries again. Three turns wasted.

Or you say `"write the report"` and get a generic five-paragraph essay when you needed a two-slide exec summary.

Most AI mistakes aren't intelligence failures — they're **missing context** failures. Claude is capable of doing the right thing; it just didn't know what the right thing was.

---

## What It Does

```
You:     "fix the bug"

Claude:  Which issue should I fix?
         ○ TypeError in src/api/users.ts:89 — crashes on login when user is null
         ○ Failing token refresh test in test/auth.test.ts

You:     [selects first option]

Claude:  [fixes the exact right thing, first try]
```

```
You:     "Refactor getUserById in src/api/users.ts to use async/await"

Claude:  [proceeds immediately — prompt is already specific, no questions asked]
```

Before acting on any request, Claude evaluates three things:

1. **Specific target** — which file, feature, topic, or thing?
2. **Clear action** — what exactly should happen to it?
3. **Sufficient context** — enough to avoid guessing?

All three present → proceeds immediately, zero overhead.
Missing any → researches your actual context first, then asks the minimum questions needed.

**Result:** Better outcomes on the first try, without back-and-forth.

---

## How It Works

```
User request
     │
     ▼
┌─────────────────────────────────┐
│  EVALUATE                       │
│  Is this clear enough to act?   │
│  Check conversation history     │
└──────────┬──────────────────────┘
           │
    ┌──────┴──────┐
    │             │
  Clear         Vague
    │             │
    ▼             ▼
Proceed      RESEARCH
immediately  • Files & workspace
(0 overhead) • Conversation history
             • Codebase patterns
             • Error logs / tests
                  │
                  ▼
             ASK (1–6 questions)
             Grounded in real findings
             Not generic placeholders
                  │
                  ▼
             EXECUTE with full context
```

Questions are always grounded in what Claude actually finds. If it discovers two specific error candidates in your code, it asks which of those two — not "which error did you mean?"

---

## Installation

### Claude.ai Web

1. Download `prompt-improver.skill` from the [Releases tab](../../releases)
2. Go to **claude.ai → Projects → your project → Skills** → upload the file
3. Add this single line to your **Project Instructions**:

```
Always apply the prompt-improver skill before executing any request.
```

Done. Every message in that project is now evaluated automatically before Claude acts.

### Cowork

1. Download `prompt-improver.skill` from the [Releases tab](../../releases)
2. Open Cowork → **Settings → Skills** → upload the file
3. Add this line to your workspace `CLAUDE.md`:

```
Always apply the prompt-improver skill before executing any request.
```

---

## Usage

**Vague prompt — gets clarified:**
```
"fix the bug"          → Claude researches, asks which one
"add tests"            → Claude asks which module, which framework
"write the report"     → Claude asks audience, length, format
"make it better"       → Claude identifies specific weak points first
"update the file"      → Claude reads it, asks what's outdated
```

**Clear prompt — proceeds immediately:**
```
"Refactor getUserById in src/api/users.ts to async/await"  → executes directly
"Write a 2-page exec summary of Q3 revenue for the board"  → executes directly
"Fix the null reference on line 89 of users.ts"            → executes directly
```

**Bypass prefix `*` — skip evaluation entirely:**
```
"* just scaffold something quick, I'll clean it up later"  → skips, executes as-is
```

---

## Design Philosophy

**Rarely intervene.** The majority of prompts pass through without a single question. The bar for asking is: would executing now lead to a genuinely wrong or wasted outcome?

**Research before asking.** Claude checks your actual files, conversation history, and context before forming questions. Options in the clarification UI come from real findings — not generic placeholders.

**Trust conversation history.** If earlier messages establish what "the bug" or "the file" refers to, the prompt is no longer vague. No questions asked.

**Stay lean.** Maximum 6 questions, one decision point per question. Enough for complex scenarios, still focused.

**Transparent.** When Claude does ask, it's clear why — the question itself shows you what it found and what it needs to know.

---

## Architecture

```
prompt-improver/
├── SKILL.md                         # Core evaluation + workflow (always loaded)
└── references/
    ├── research-strategies.md       # Context-gathering by vagueness type (on-demand)
    └── question-patterns.md         # Question templates + examples (on-demand)
```

Uses progressive disclosure — reference files only load when needed. Clear prompts never touch them. This keeps per-prompt overhead minimal.

**For vague prompts:**
- Skill loads (~170 lines)
- Claude builds a research plan, gathers context
- Asks 1–6 grounded questions
- Executes with full context

**For clear prompts:**
- Evaluation happens inline
- Skill body doesn't load
- Zero extra overhead

---

## Compared to the Claude Code Hook Version

The [claude-code-prompt-improver](https://github.com/severity1/claude-code-prompt-improver) by severity1 is excellent — it uses a `UserPromptSubmit` hook to intercept prompts at the CLI level. This skill achieves the same goal through Claude's native skill system, making it available to anyone without a terminal.

| | This skill | Claude Code hook |
|---|---|---|
| Claude.ai web | ✅ | ❌ |
| Cowork | ✅ | ❌ |
| Claude Code CLI | ✅ (manual invoke) | ✅ (automatic) |
| Requires Python / terminal | ❌ | ✅ |
| Setup | One line | Script + config |

---

## FAQ

**Will it slow me down?**
Only when it asks questions — which is rare. Clear, specific prompts proceed instantly. The time saved from fewer correction cycles easily outweighs the occasional question.

**Will I get bombarded with questions?**
No. It only intervenes when ambiguity would genuinely cause a wrong outcome. Most prompts pass through untouched.

**What if I want to skip it for one message?**
Prefix with `*`: `"* just do this quickly, don't overthink it"`

**Does it use conversation history?**
Yes — always checks it first. If earlier context resolves the ambiguity, it proceeds immediately without asking anything.

**Can I use it outside a project?**
Yes — invoke it manually at any time: `"Use the prompt-improver skill to clarify: add authentication"`

---

## License

MIT — free to use, modify, and share.
