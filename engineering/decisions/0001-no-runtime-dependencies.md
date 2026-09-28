# 0001 — No runtime dependencies

- **Status:** accepted
- **Date:** 2026-06-01
- **Affects:** environment `production`, all code

## Context

The Bookmarks API is small. Anyone should be able to run it with nothing but
Node.js 22 or later. Every runtime dependency is supply-chain risk and upgrade work
that a service this size does not need.

## Options considered

1. **Add packages when they help** — faster to write some things, but the
   dependency tree soon outgrows the service.
2. **No runtime dependencies; use Node.js built-ins** — `node:http`, `node:test`,
   `node:crypto`. More code in a few places.
3. **Do nothing** — every contributor decides for themselves.

## Decision

Option 2. The service has no runtime dependencies. Adding one needs a new ADR that
supersedes this one.

## Consequences

- `npm install` is not needed to run the service.
- Some features take more code, because there is no library to lean on.
- Any tool or SDK proposal starts as a Decision, not as code.
