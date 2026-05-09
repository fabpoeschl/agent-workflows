---
name: review
description: >
  Tiered code review of a git diff against the project's constraints. Reads the diff
  and full affected files, then organizes findings into three tiers: must-fix (bugs,
  security, data loss), should-fix (ignored constraints, performance, readability), and
  nits/praise. Ends with a verdict: approve, request-changes, or comment-only.
  Read-only — produces no commits or file changes.
---

Follow the review workflow in `.agents/workflows/review.md`.
