# Question Patterns

Load this file when you need templates and examples for formulating good clarifying questions.

## AskUserQuestion Format

```
question: "Which [thing] are you referring to?"
header: "Target" (max 12 chars)
multiSelect: false
options:
  - label: "Specific name/location"
    description: "What this means and any relevant trade-off"
  - label: "Other specific option"
    description: "What this means"
```

## Question Templates by Type

### Identifying the target
```
question: "Which [file/function/component/document] should I work on?"
header: "Target"
options: [actual files/functions found in research]
```

### Choosing an approach
```
question: "How should I approach this?"
header: "Approach"
options: [concrete methods with trade-offs, e.g., "Rewrite from scratch (cleaner but more risk)" vs "Refactor incrementally (safer, preserves history)"]
```

### Defining scope
```
question: "How much of the codebase should this cover?"
header: "Scope"
options: ["Just [specific module]", "[Specific module] + related tests", "All instances across the project"]
```

### Clarifying validation
```
question: "How should I verify this is working?"
header: "Validation"
options: ["Run existing tests", "Add new unit tests", "Manual verification only"]
```

### Specifying format/output
```
question: "What format should the output be in?"
header: "Format"
options: [formats that make sense given what was found]
```

## Good vs. Bad Options

**Bad** (generic, not grounded):
- "Fix the main error"
- "Use a different approach"
- "Something else"
- "Option A" / "Option B"

**Good** (specific, from research):
- "Fix null reference in src/api/users.ts:89"
- "Use async/await to match the rest of the codebase"
- "Add validation before the database call in processPayment()"

## Scenario Examples

### "Fix the bug" (found 2 candidates)
```
question: "Which issue should I fix?"
header: "Bug target"
options:
  - label: "Null ref in users.ts:89"
    description: "TypeError when user object is undefined — crashes on login"
  - label: "Auth token expiry (test/auth.test.ts)"
    description: "Token refresh not handled — users get logged out unexpectedly"
```

### "Add tests" (found untested module)
```
question: "Which functions need test coverage?"
header: "Test scope"
options:
  - label: "processPayment() only"
    description: "The function changed in the last commit — highest priority"
  - label: "Full payments module"
    description: "processPayment + validateCard + handleRefund — full coverage"
```

### "Write the report" (audience unclear)
```
question: "Who is this report for?"
header: "Audience"
options:
  - label: "Engineering team"
    description: "Technical details, code metrics, implementation notes"
  - label: "Executives / stakeholders"
    description: "High-level summary, business impact, recommendations only"
```

### "Make it faster" (found two bottlenecks)
```
question: "Which performance issue should I tackle first?"
header: "Bottleneck"
options:
  - label: "Database query (loadUserDashboard)"
    description: "N+1 query pattern — 47 queries per page load observed"
  - label: "Image rendering pipeline"
    description: "No lazy loading — all 120+ images load on initial page render"
```

## How Many Questions?

| Situation | Questions |
|-----------|-----------|
| Single ambiguous reference ("the file", "the bug") | 1 |
| Unclear target + unclear approach | 2 |
| New feature with scope, method, and validation unknowns | 3–4 |
| Major system change with architecture decisions | 5–6 |

**Never ask more than 6.** If you have more than 6 genuine unknowns, prioritize the ones that would most change what you do. The others can often be inferred from the answers you get.
