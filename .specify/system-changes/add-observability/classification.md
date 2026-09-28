# Classification: Make requests observable

- **Slug**: add-observability
- **Date**: 2026-09-28
- **Request**: "We can't tell which requests are slow or failing in production. Add observability."
- **Route**: System Change
- **Primary kind**: Capability
- **Decision required**: yes — the obvious option (an OpenTelemetry SDK) adds a dependency, and ADR 0001 says that needs an ADR
- **Feature spec required**: no

## Why this route

No user sees a different response. What changes is what the system can do for every
route — let operators see requests — and what production must collect. That is the
System Change test.

## Artifacts

| Action | Kind | Path | Why |
|---|---|---|---|
| CREATE | Decision | engineering/decisions/0002-log-requests-as-json-lines.md | SDK vs built-ins; must respect ADR 0001 |
| CREATE | Capability | engineering/capabilities/observability/capability.yaml | New shared ability; features will `require` it |
| CREATE | Integration contract | engineering/integrations/log-collector/contract.yaml | The event shape becomes a promise to the log collector |
| UPDATE | Environment | engineering/environments/production/environment.yaml | Production must collect stdout. Additive, so no version bump |
| CREATE | Standard | engineering/standards/logging.md | Every feature that logs must follow the same rules |
| REVIEW | Feature | specs/001-bookmark-export/spec.md | FR-003 needs `observability@1` |

## Enabling tasks

- [ ] Add `src/request-events.js` with a default stdout sink
- [ ] Accept `emit` in `createServer`

## Verification

- `npm test` — one event per request, exact key set, no query string in the line

## Open questions

- [NEEDS CLARIFICATION: how long does the platform keep stdout? `environment.yaml` assumes 30 days]

## Applied

- engineering/decisions/0002-log-requests-as-json-lines.md (created)
- engineering/capabilities/observability/capability.yaml (created)
- engineering/integrations/log-collector/contract.yaml (created)
- engineering/environments/production/environment.yaml (updated)
- engineering/standards/logging.md (created)
