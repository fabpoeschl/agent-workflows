# Rails-Specific Agent Configuration

Include this alongside other agent docs when working on Rails projects.

## Running Tests
```
PARALLEL_WORKERS=1 bundle exec rails test path/to/test.rb
PARALLEL_WORKERS=1 bundle exec rails test path/to/test.rb:<line_number>
```

## Linting
```
bundle exec rubocop path/to/changed_file.rb
```
Follow the project's `.rubocop.yml` for Ruby style and `.eslintrc` for JavaScript style.

## API Documentation
If specs in `spec/integration` were added or changed, run `bundle exec rswag` to update swagger docs.

## Testing Rules
- Do not mock internal application code; only mock external services (HTTP boundaries).
- Before defining a complex mock, check `test/support/` for existing stubs and helpers.
- Prefer factories over fixtures for test data setup, considering dependency chains.
- Add view tests for static content and simple interactions; use system tests for complex interactions.

## Plan Mode Triggers
In addition to the general triggers, use plan mode when:
- Database schema or migration changes are needed
- Timescale hypertable modifications are needed
- Ingest pipeline changes are needed
- Caching layer changes are needed

## Commit Messages
Use conventional commits with optional ticket number:
- `feat: NT-XXXXX <description>`
- `fix: NT-XXXXX <description>`
- `test: NT-XXXXX <description>`
- `chore: NT-XXXXX <description>`

The ticket number may be omitted if not provided.

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
- Suspect test pollution: class-level Mocha stubs, `any_instance` stubs, Flipper flags, or shared mutable state.
- Prefer HTTP stubs over class-level Mocha stubs for external service calls in integration paths.
