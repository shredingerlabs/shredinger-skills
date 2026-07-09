---
name: verification-web
description: Defines what "verified" means for web/frontend work — typecheck, lint, build, tests, and confirming actual behavior in a running browser or dev server. Use this whenever a change touches a web frontend, a browser-facing UI, client-side JavaScript/TypeScript, or an API boundary consumed by a browser client (CORS, auth headers, request/response contracts). Trigger even for small UI tweaks — a passing unit test alone is not sufficient here.
---

# Web Verification

Success = typecheck + lint + build + relevant test suite all green, **and** the actual behavior
confirmed in a running browser/dev server (or Playwright) when UI or user-facing behavior changed.
A passing unit test is not proof a component renders or behaves correctly — it proves the test
passed.

## Requirements

- Run the project's typecheck, lint, build, and test commands. All must pass before calling
  anything "done."
- If the change affects anything rendered or interactive, actually load it — dev server or
  Playwright — rather than reasoning from the diff alone.
- Never leave secrets, keys, or tokens in client-side code, even temporarily for testing.
- State CORS/auth/data-boundary assumptions explicitly before touching any API integration point
  — don't assume the existing contract still holds after a change.
