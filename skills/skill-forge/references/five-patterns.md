# Five Key Design Patterns

Every well-forged skill uses these five patterns.

## 1. Escape Hatch

Define explicit "when to bail out" conditions. Skills without escape hatches hallucinate through edge cases.

```markdown
If any of these are true, output [SKILL_CANNOT_PROCEED: reason]:
- [specific condition 1]
- [specific condition 2]
Suggest: "[alternative approach]"
```

## 2. Output Anchoring

Fix the output format with a template. Eliminates 80% of output variance.

```markdown
Response must begin with:
`[STATUS: ok|warning|error] ten-word summary`
```

Or for structured output:
```markdown
Output format:
type(scope): imperative subject under 72 chars
```

## 3. Context Acquisition

ALWAYS read before generating. Skills that skip this work on demos, fail on real projects.

```markdown
BEFORE generating any output:
1. Read [relevant project file] to determine [context]
2. Check for [existing patterns/config]
3. Identify [framework/toolchain]
```

## 4. Negative Examples First

Show what NOT to do. 3x more effective than positive examples. Use 2:1 negative-to-positive ratio.

```markdown
DO NOT:
- "Updated files" — too vague
- "Fixed stuff" — no specificity

DO:
- "fix(auth): prevent token expiry race during refresh"
```

## 5. Checkpoint

For multi-step work, pause and confirm. No checkpoints = users disable the skill.

```markdown
Before proceeding to step 3, confirm with user:
"I plan to modify these files: [list]. Proceed?"
```

## Quick Self-Test

For any skill you're building, check:
- Can it bail out gracefully? (Pattern 1)
- Is the output format locked down? (Pattern 2)
- Does it read context before acting? (Pattern 3)
- Does it show what NOT to do? (Pattern 4)
- Does it pause before irreversible actions? (Pattern 5)
