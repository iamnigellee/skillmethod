---
name: skill-forge
description: >
  The Skill Forge — a battle-tested methodology for crafting Anthropic Skills
  that actually work. Guides through the full lifecycle: scope → description →
  instructions → testing → hardening. Enforces five core principles: Verifiable
  Perimeter, Attention-Trust Tradeoff, Reference File Paradox, Failure
  Disclosure Threshold, and Trigger-Scope Coupling Law.
  TRIGGER when user wants to create a skill, build a skill, design a skill,
  write a SKILL.md, make a new skill, start a skill project, skill development,
  help me build a Claude skill, skill forge.
  SKIP when user wants to use an existing skill (not create one).
  SKIP when user wants to edit code unrelated to skill creation.
  SKIP when user asks about Anthropic API or SDK usage (that is claude-api).
---

# Skill Forge

## Constraints

- Follow the forge sequence strictly: Scope → Description → Instructions → Test → Harden.
- NEVER skip the scope validation step. Poorly scoped skills are the #1 cause of failure.
- NEVER generate a SKILL.md in one shot. Each phase requires user confirmation.
- If the user's idea should NOT be a skill, say so. See "When NOT to Build" below.

## Phase 0: Intake Interview

Before anything else, understand what the user wants to build.

1. Ask: "What task do you want the skill to handle? Describe it as if explaining to a coworker."
2. Ask: "How often do you do this task? (daily / weekly / monthly)"
3. Ask: "What does Claude currently get wrong when you ask it to do this?"

If the user has done the task fewer than 3 times manually, CHECKPOINT:
"You may not understand this task well enough to encode it yet. Consider doing it manually a few more times first. Want to proceed anyway?"

## Phase 1: Scope Validation

### The Verb-Noun Test

The skill must complete this sentence: **"Help me [VERB] [NOUN]."**

If the user describes multiple verbs ("review code, write tests, and generate docs"), STOP:
"That sounds like 3 separate skills. Let's pick one to start with. Which is most painful?"

### Kill Criteria — When NOT to Build a Skill

Do NOT build a skill if ANY of these are true:

- **Base Claude already does it well.** Test by asking Claude without any skill. If it works 80%+ of the time, a skill adds overhead without value. Examples: "summarize this document", "write a Python function", "explain this code."
- **The output is purely conversational.** Skills produce artifacts (code, config, documents, structured output). If the output is just chat, use a system prompt instead.
- **It wraps a single command.** If the skill is "run `kubectl get pods` and explain it", just tell the user to run the command. A skill thinner than a function call is overhead.
- **The domain is too broad.** If the TRIGGER list would need 15+ verbs, it's too wide. Split it.

If any kill criteria match, output:
`[SKILL_FORGE: This should not be a skill. Reason: ___. Alternative: ___.]`

### Scope Output

Confirm with user:
```
Scope Lock:
  Verb-Noun: [verb] [noun]
  In scope: [3-5 bullet points]
  Out of scope: [3-5 bullet points]
  Escape hatches: [conditions where skill should bail out]
```

Do NOT proceed to Phase 2 until user confirms scope.

## Phase 2: Description Engineering

### Domain Width Assessment

Ask: "How unique is this domain? Could other skills also handle similar requests?"

Classify:
- **Narrow** (PDF, OCR, calendar): 8-10 trigger verbs, 2-3 SKIP entries
- **Medium** (API development, testing): 5-7 trigger verbs, 4-5 SKIP entries
- **Wide** (code review, writing): 4-5 trigger verbs + qualifiers, 5-7 SKIP entries

### Draft the Description

Generate a description following these rules:
- Max 1024 characters, third person
- TRIGGER scenarios: concrete verb phrases, each ≤ 6 words
- SKIP scenarios: name adjacent skills/domains explicitly
- The description must semantically align with the body — reading it should make the body's first 50 lines feel "expected"

### Priming Alignment Check

Before finalizing, ask yourself: "If Claude only reads this description, will it expect the instructions I'm about to write?" If not, revise.

### Description Output

Show the user the draft frontmatter:
```yaml
---
name: [lowercase-with-hyphens]
description: >
  [drafted description]
---
```

CHECKPOINT: "Does this description capture when the skill should and shouldn't activate?"

## Phase 3: Instruction Architecture

### Gather Before Generating

Before writing any instructions, determine:
1. What context must be acquired at runtime? (files to read, commands to run, state to check)
2. What decisions must the skill make? (type detection, format selection, tool choice)
3. What are the failure modes? (missing tools, unexpected input, scope violations)

### Degrees of Freedom Assessment

For each section of the skill, classify:

| Operation | Freedom | Style |
|-----------|---------|-------|
| Deterministic (exact format, API call) | Low | Step-by-step procedure |
| Constrained creative (tests, configs) | Medium | Goal + boundaries |
| Open creative (docs, refactoring) | High | Intent + examples |

### Write the Body Using the Attention Curve

```
Lines 1-50:   Critical constraints + quick-start workflow
Lines 51-200: Decision trees + branching logic (numbered lists)
Lines 200-350: Reference tables + examples (low-attention zone, retrieval-friendly)
Lines 350-end: Repeat critical constraints + edge cases + escape hatches
```

### Mandatory Sections Checklist

Every skill body MUST include:
- [ ] Critical Constraints (what NEVER to do) — first 50 lines AND last 30 lines
- [ ] Context Acquisition steps (what to read/check before acting)
- [ ] Output format template in a code block (Output Anchoring)
- [ ] Decision tree for the primary branching logic (numbered, max 3 levels)
- [ ] Negative examples (what NOT to do, 2:1 ratio vs positive)
- [ ] Escape hatch conditions with `[SKILL_CANNOT_PROCEED: reason]`
- [ ] Checkpoint conditions (when to ask user before proceeding)

### File Size Rules

- Target: under 400 lines
- If approaching 400: extract reference tables to `references/` files
- Hard ceiling: 600 lines (mandatory split above this)
- Each section: max 60 lines, with a `##` header every 40-60 lines

### Anti-Rot Rules

Apply these three rules to every instruction:
1. **Point, don't snapshot.** Write "match patterns in `src/tests/`" not a pasted template.
2. **Encode principles, not versions.** Write "use the project's linter" not "run eslint --rule X."
3. **Separate stable from volatile.** Workflow in SKILL.md, tool versions in project config.

### Instruction Output

Generate the complete SKILL.md body. Show it to the user.

CHECKPOINT: "Review the instructions. Anything missing or wrong?"

## Phase 4: Testing

### Tier 1 Quick Validation (always do this)

Create and run 3 manual tests:
1. **Golden path**: the most common use case
2. **Edge case**: an unusual but valid input
3. **Non-trigger**: a query that should NOT activate the skill

For each test, define hard assertions:
- Output contains expected format?
- No forbidden actions executed?
- Escape hatch fires when it should?

### Tier 2 Production Validation (if skill will be shared)

Guide user to create `evals/evals.json` with:
- 4-5 eval cases (2 golden + 1-2 edge + 1 adversarial)
- 10 trigger/non-trigger queries (5+5, include 2 near-misses in the non-trigger set)

Target metrics:
- Trigger precision ≥ 95%
- Hard assertion pass rate: 100%
- Baseline comparison: skill output beats no-skill on at least 1 golden path

## Phase 5: Hardening

### Security Review

Check the skill for:
- [ ] No `eval()`/`exec()` on dynamic input in scripts
- [ ] No reads outside project scope (no ~/.ssh, ~/.aws, .env)
- [ ] No `sudo` or system-level writes
- [ ] Guardrails are structural (regex/format), not aspirational ("be careful")
- [ ] Critical constraints appear in both first 50 and last 30 lines

### Multi-Surface Check

Verify the core workflow works without tools:
- "If I paste this SKILL.md into a Claude.ai conversation with no tools, does the core task still complete?"
- If not, add a "When Tools Are Available" enhancement section with capability detection.

### Quality Scorecard

Run the final checklist:
- [ ] Description ≤ 1024 chars with TRIGGER + SKIP
- [ ] Maps to one verb-noun pair
- [ ] Body ≤ 400 lines (600 hard max with structure)
- [ ] Every instruction is trigger/check/action/branch
- [ ] Intent before procedure in each section
- [ ] No cross-skill dependencies
- [ ] Escape hatches defined with SKILL_CANNOT_PROCEED
- [ ] Output anchored with code block template
- [ ] Negative examples present (2:1 ratio)
- [ ] Checkpoints at key decision points
- [ ] Constraints repeated first 50 + last 30 lines

### Maturity Classification

Based on what was completed:
- **L0 Draft**: Has description + body, 3 manual tests passed
- **L1 Validated**: 4+ evals, trigger precision ≥ 95%, peer reviewed
- **L2 Blessed**: Full enterprise checklist, versioned, quarterly review scheduled

## Final Reminders

- Follow the forge sequence: Scope → Description → Instructions → Test → Harden.
- NEVER skip scope validation. NEVER generate SKILL.md in one shot.
- If it shouldn't be a skill, say so immediately.
- Each phase ends with a user CHECKPOINT before proceeding.
- The goal is a skill that is invisible when working and honest when failing.
