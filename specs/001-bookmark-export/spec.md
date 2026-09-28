# Feature Specification: Export bookmarks

**Feature Branch**: `001-bookmark-export`
**Status**: Draft
**Input**: "Let me download all my bookmarks as a CSV file"

> Shortened for the sample. Only the last section is new; the rest follows the Spec
> Kit spec template.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Take my bookmarks to another tool (Priority: P1)

A user moving to another tool downloads every bookmark they saved, in a format any
spreadsheet or importer can read.

**Acceptance Scenarios**:

1. **Given** a user with three bookmarks, **When** they call `GET /bookmarks/export`,
   **Then** they get a CSV file with a header row and three rows.
2. **Given** a user with no bookmarks, **When** they export, **Then** they get the
   header row only.

## Requirements *(mandatory)*

- **FR-001**: `GET /bookmarks/export` MUST return `text/csv` with the columns `url`,
  `title`, `createdAt`.
- **FR-002**: Rows MUST be in the order the bookmarks were saved, oldest first.
- **FR-003**: Export requests MUST be observable like every other request.

## Success Criteria *(mandatory)*

- **SC-001**: An operator can see how many exports ran, and how long they took, from
  the logs alone.

## Requires (engineering)

This feature does not explain how observability works. It depends on it:

```yaml
requires:
  - observability@1     # engineering/capabilities/observability/capability.yaml
```

`observability` is `planned`, not `active`. This feature cannot ship until the
`add-observability` System Change is verified.
