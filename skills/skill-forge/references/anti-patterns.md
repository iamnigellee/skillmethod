# Anti-Pattern Quick Reference

When reviewing a skill draft, check for these common mistakes.

## Description Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|---|---|---|
| Kitchen-sink description | Triggers on everything, conflicts with other skills | Split into separate verb-noun skills |
| Negation-only description | "Don't use for non-CSV files" — no positive signal | List positive trigger scenarios first |
| Routing logic in body | "When to use" section in body, not description | Move ALL routing info to description |

## Instruction Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|---|---|---|
| Hedge instructions | "You might want to consider..." | "Always check." Be imperative. |
| Contradictory instructions | Two rules that conflict | Explicit priority with IF/THEN |
| Personality descriptions | "You are a helpful coding expert" | Delete. Skills are procedures, not personas. |
| Unanchored always/never | "Always write tests" (even for one-liners?) | Scope it: "When generating modules with 2+ exports, include tests." |
| Over-prescriptive steps | Enumerating steps Claude already knows | Name the action + success criteria only |

## Architecture Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|---|---|---|
| Documentation graveyard | README.md, CHANGELOG.md alongside SKILL.md | Skills are for AI, not human browsing. Remove extras. |
| Deep reference nesting | SKILL.md → ref.md → sub-ref.md | Max one level deep from SKILL.md |
| Freedom mismatch | Scripts for creative tasks OR prose for deterministic ops | Match freedom level to operation brittleness |

## Skills That Should NOT Exist

| Type | Why | Alternative |
|---|---|---|
| Generic summarizer | Base Claude already excels | Just ask Claude directly |
| Basic code generator | Core LLM competency | Only skill-ify for internal frameworks |
| Chat/conversation skill | Open-ended = system prompt | Use a system prompt instead |
| Swiss army knife ("DevOps Helper") | Too broad, mediocre at everything | Split into specific verb-noun skills |
| Single-command wrapper | Thinner than a function call | Tell user to run the command |
