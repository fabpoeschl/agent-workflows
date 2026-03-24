# Rails-Specific Agent Configuration

Include this alongside other agent docs when working on Rails projects. For project-specific test, lint, and build commands, see `doc/agents/project.md`.

## Testing Rules
- Before defining a complex mock, check `test/support/` for existing stubs and helpers.
- Prefer factories over fixtures for test data setup, considering dependency chains.
- Add view tests for static content and simple interactions; use system tests for complex interactions.

## Code Review Extras
In addition to the general review checklist, check for:
- N+1 queries, missing `includes`/`preloads`
- Scope composability
- Potential locking or contention issues
- Postgres-specific optimizations or pitfalls
- Migration best practices (long-running migrations, data integrity, separate deploy for drops)
- Fat model/controller smell and service object quality

## Debugging CI Failures
- CI failures that don't reproduce locally are often seed/ordering dependent.
- Always reproduce with the exact seed: `bundle exec rails test path/to/test.rb --seed <seed>`
- Common sources of test pollution:
  - Class-level Mocha stubs or `any_instance` stubs leaking between tests
  - Flipper flags or feature toggles set in one test affecting another
  - Shared mutable state (class variables, global config, in-memory caches)
- Prefer HTTP stubs over class-level Mocha stubs for external service calls in integration paths.
