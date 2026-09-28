# 0002 — Log requests as JSON lines on stdout, with no telemetry SDK

- **Status:** proposed
- **Date:** 2026-09-28
- **Affects:** capability `observability`, integration `log-collector`, environment `production`

## Context

Operators cannot tell which requests are slow or failing in production. The service
writes nothing when it answers a request.

[ADR 0001](0001-no-runtime-dependencies.md) constrains the answer: no runtime
dependencies, and adding one needs an ADR.

## Options considered

1. **OpenTelemetry SDK** — traces and metrics with standard exporters. Adds a
   dependency tree larger than the service, against ADR 0001.
2. **One JSON line per request on stdout, using Node.js built-ins** — whatever runs
   the process already collects stdout. No traces.
3. **Do nothing** — operators keep asking developers what happened.

## Decision

Option 2. The server writes one `http.request` event per request, after the response
is sent, through a sink passed to `createServer({ emit })`. The default sink writes
the event as one JSON line to stdout. The event carries the route template, never
the raw path, the query string or a body.

No telemetry SDK. ADR 0001 stands unchanged.

## Consequences

- Operators can count slow and failing requests per route without new
  infrastructure in this repository.
- The event shape is now a contract with the log collector
  (`engineering/integrations/log-collector/contract.yaml`). Renaming a field is a
  breaking change.
- There are no distributed traces. If we need them, that is a new ADR that
  supersedes this one and makes the case against ADR 0001.
