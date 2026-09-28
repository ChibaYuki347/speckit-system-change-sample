# Logging

Rules every feature follows when it writes an event.

- **Log the route template, never the raw path or query string.** `/bookmarks/:id`,
  not `/bookmarks/42?token=…`. A saved link can carry a token.
- **Never log a request or response body.** Bookmark URLs and titles stay out of
  the logs.
- **One JSON object per line, on stdout.** No SDK, no second log format (ADR 0002).
- **Name events `<area>.<fact>`, lowercase.** For example `http.request`.
- **A logging failure never fails a request.**
- **A new event is a contract change.** Add it to
  [`integrations/log-collector/contract.yaml`](../integrations/log-collector/contract.yaml)
  in the same pull request.
