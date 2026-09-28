# engineering/ — the current state of the system

`specs/` describes **changes**, one feature at a time. This folder describes what is
**true now**. Features point here with `requires: <id>@<version>`; they don't repeat
it.

| Folder | Answers | Lifetime | How it changes |
|---|---|---|---|
| `decisions/` | **Why** did we choose this? | Permanent | Add a new ADR. Supersede; never rewrite an accepted one |
| `capabilities/` | **What** can the system do? | Long | Edit in place to stay true |
| `integrations/` | What does it connect to, on what terms? | Long | Edit in place; a breaking change is a new version |
| `environments/` | **Where** does it run, and what must be true there? | Medium | Edit in place; steps live in code, not here |
| `standards/` | What rule does every feature follow? | Months to years | Edit in place |

## What changed → where to update

| What changed | Update |
|---|---|
| What users or callers need | `specs/NNN-*/spec.md` |
| How we'll build this feature | `specs/NNN-*/plan.md` |
| Work was added or split | `specs/NNN-*/tasks.md` |
| We chose between technical options | a new ADR in `decisions/` |
| The system gained or lost a shared ability | `capabilities/<id>/capability.yaml` |
| A connection to an external tool or service changed | `integrations/<id>/contract.yaml` |
| Dev, CI or production changed | `environments/<id>/environment.yaml` |
| A rule now applies to every feature | `standards/` |
| A rule became non-negotiable | `.specify/memory/constitution.md` |

Update the file that owns the fact, then fix whatever now disagrees with it.

## Worked example in this folder

The `add-observability` System Change
([classification](../.specify/system-changes/add-observability/classification.md),
[tasks](../.specify/system-changes/add-observability/tasks.md)) produced:

- `decisions/0002-log-requests-as-json-lines.md`
- `capabilities/observability/capability.yaml`
- `integrations/log-collector/contract.yaml`
- `environments/production/environment.yaml` (updated)
- `standards/logging.md`

`decisions/0001-no-runtime-dependencies.md` was already accepted. ADR 0002 has to
respect it, which is why the change needed a Decision before any code.
