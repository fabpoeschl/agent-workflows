---
name: implement
description: >
  Turn an approved feature spec's failing tests green. Loads the spec YAML from
  $AGENTS_DIR/projects/<project>/specs/<feature>.yaml and the project context, confirms
  the new tests fail, implements one contract at a time with the minimum code required,
  verifies all tests pass and nothing regressed, then commits following the project's
  commit conventions. Does not push.
---

Follow the implementation process defined in `.agents/prompts/implement.md`.
