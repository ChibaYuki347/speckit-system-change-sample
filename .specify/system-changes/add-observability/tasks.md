# Tasks: Make requests observable

Change: add-observability — see classification.md

## Enabling tasks

- [ ] T001 Add `src/request-events.js`: build the event from the matched route, status and `durationMs`; default sink writes one JSON line to stdout
- [ ] T002 Accept `{ emit }` in `createServer`; call it once per request after the response is sent, and never let a sink error affect the response

## Verification

- [ ] V001 `npm test` — request-events suite: one event per request, exact key set, no query string in the serialised line

## Review

- [ ] R001 Accept or reject ADR 0002
- [ ] R002 Confirm stdout retention with the platform team (`environments/production`)
- [ ] R003 Check `specs/001-bookmark-export` FR-003 against the contract

## Done when

- Verification passes, `observability` and `log-collector` are set to `active`, and
  ADR 0002 is accepted.
