# Description Engineering Patterns

## Template

```yaml
---
name: [lowercase-hyphens-only]
description: >
  [What it does — one sentence, third person].
  TRIGGER when [verb phrase 1], [verb phrase 2], [verb phrase 3],
  [verb phrase 4], [verb phrase 5].
  SKIP when [adjacent domain 1].
  SKIP when [adjacent domain 2].
---
```

## Good Example — Narrow Domain

```yaml
description: >
  Handles all PDF file operations including reading, extracting text
  and tables, merging multiple PDFs, splitting documents, rotating
  pages, adding watermarks, creating new PDFs, filling forms,
  encrypting, decrypting, and OCR on scanned documents.
  TRIGGER when user mentions PDF files, scanned documents, or form filling.
  SKIP when user asks about document writing or content generation.
  SKIP when user wants image editing unrelated to scans.
```

Why good: 10 trigger verbs (safe because "PDF" is unambiguous), explicit SKIP naming boundaries.

## Bad Example — Too Broad

```yaml
description: >
  Handles all code tasks: reviewing, writing, debugging, refactoring,
  testing, documenting, deploying, optimizing, architecting, migrating.
  TRIGGER when user mentions any programming language.
```

Why bad: Triggers on everything, no SKIP, conflicts with every dev skill.

## Bad Example — Negation Only

```yaml
description: Don't use this for non-CSV files.
```

Why bad: No positive signal. Skill never activates.

## Domain Width Guide

| Width | Trigger Verbs | SKIP Entries | Pushiness |
|-------|:---:|:---:|---|
| Narrow (PDF, OCR) | 8-10 | 2-3 | Aggressive |
| Medium (API dev) | 5-7 | 4-5 | Moderate with qualifiers |
| Wide (code review) | 4-5 | 5-7 | Restrained, heavy SKIP |

## Priming Rule

The description primes how Claude interprets the body. If description says "security" but body is about "formatting", compliance drops 15-30%. Ensure semantic alignment.
