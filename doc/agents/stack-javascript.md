# JavaScript/TypeScript Agent Configuration

Include this alongside other agent docs when working on JavaScript/TypeScript projects. For project-specific test, lint, and build commands, see `doc/agents/project.md`.

## Testing Rules
- Use `msw` (Mock Service Worker) or similar for API mocking when available.
- Avoid snapshot tests for dynamic content; prefer explicit assertions.
- For React/Vue components, prefer testing library queries (`getByRole`, `getByText`) over test IDs or CSS selectors.

## Code Review Extras
In addition to the general review checklist, check for:
- Bundle size impact of new dependencies
- Unnecessary re-renders in component-based frameworks
- Missing error boundaries or loading states
- Unhandled promise rejections and missing error handling in async code
- Memory leaks from event listeners, subscriptions, or timers not cleaned up
- Accessibility (semantic HTML, ARIA attributes, keyboard navigation)
