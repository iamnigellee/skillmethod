# Attention Curve Placement Guide

LLM attention follows a U-shaped curve across instruction length.

## Zone Map

| Zone | Lines | Attention | Place Here |
|------|-------|-----------|------------|
| **Hot Start** | 1-50 | Highest | Critical constraints, quick-start workflow, fail-closed defaults |
| **Active Middle** | 51-200 | Medium | Decision trees, branching logic, numbered procedures |
| **Cold Zone** | 200-350 | Lowest | Reference tables, lookup data, examples (retrieved on-demand) |
| **Hot Close** | 350-end | Rising | Repeated constraints, edge cases, escape hatches, "when NOT to use" |

## Key Rules

1. Any constraint with serious consequences if violated → appears in BOTH Hot Start AND Hot Close.
2. Each section opens with 1-2 imperative sentences (command-shaped text gets priority).
3. Headers (##) create attention anchors — use one every 40-60 lines.
4. No single section exceeds 60 lines.

## Format Effects on Compliance

| Format | Impact | Best For |
|--------|--------|----------|
| Numbered lists | **High** | Sequential procedures |
| Code blocks | **High** | Exact output templates |
| Headers (##) | **High** | Section boundaries / anchors |
| **Bold** | Medium | Key terms within paragraphs |
| Bullet lists | Medium | Unordered rule sets |
| Tables | Low | Reference data (NOT behavioral instructions) |
