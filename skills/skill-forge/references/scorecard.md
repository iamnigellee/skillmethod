# Skill Quality Scorecard

Rate each item pass/fail. A skill must pass all items in its maturity tier.

## L0 — Draft (minimum viable)

- [ ] Description ≤ 1024 chars, third person
- [ ] Name: lowercase + hyphens, ≤ 64 chars, no "anthropic"/"claude"
- [ ] TRIGGER list present with concrete scenarios
- [ ] SKIP list present covering adjacent domains
- [ ] Maps to exactly one verb-noun pair
- [ ] Body ≤ 400 lines (structured up to 600)
- [ ] 3 manual tests passed (golden path + edge case + non-trigger)

## L1 — Validated (shareable)

All L0 items, plus:
- [ ] Every instruction is trigger/check/action/branch
- [ ] Intent stated before procedure in each section
- [ ] Critical constraints in first 50 AND last 30 lines
- [ ] Escape hatches with SKILL_CANNOT_PROCEED
- [ ] Output anchored with code block template
- [ ] Negative examples present
- [ ] Checkpoints at key decisions
- [ ] 4+ eval cases in evals.json
- [ ] Trigger precision ≥ 95%, recall meets domain target
- [ ] Hard assertions pass at 100%
- [ ] Peer reviewed by one other person

## L2 — Blessed (enterprise-ready)

All L1 items, plus:
- [ ] Frontmatter includes version (semver), owner, review-by, compliance-tier
- [ ] Business case in frontmatter (2-3 sentences)
- [ ] 6-8 eval cases with adversarial tests
- [ ] Full 20-query trigger suite
- [ ] Baseline comparison win rate > 70%
- [ ] Tested on Haiku + Sonnet + Opus
- [ ] Security review passed (no eval/exec/sudo, no credential reads)
- [ ] Core workflow works without tools (multi-surface)
- [ ] Quarterly review scheduled

## 3-Minute Quick Assessment

For any skill, answer three questions:
1. Fuzzy near-miss query — does it trigger? (It shouldn't.)
2. What's the most dangerous thing this skill can do? Does the user know?
3. Run the same query twice — is the output structure consistent?
