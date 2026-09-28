---
name: speckit-system-change-classify
description: Classify a request as a Feature Change or a System Change, and list the
  engineering/ files to create or update
compatibility: Requires spec-kit project structure with .specify/ directory
metadata:
  author: Yuki Chiba
  source: extension:system-change
---

# System Change Classify Skill

# Classify a Change Request

Decide which route a request takes before anyone writes a spec or code:

- **Feature Change** — it changes what a user or API caller can observe. It goes
  through the normal Spec Kit path, starting with `/speckit-specify`.
- **System Change** — it changes what the system can do for many features, what it
  connects to, or where and how it runs. It goes through
  `/speckit-system-change-apply`.

The only output is `.specify/system-changes/<slug>/classification.md`. This command
never edits `engineering/`, `specs/`, or source code.

## User Input

```text
$ARGUMENTS
```

The input is a request in plain words (for example "set up Figma Code Connect" or
"we can't tell which requests are slow in production"), optionally with
`slug=<kebab-case>`.

## Slug

1. If the user gave `slug=<value>`, use it (lowercase, hyphens, digits only).
2. Otherwise, in an interactive session, suggest a 2–4 word kebab-case slug and ask.
3. In a non-interactive run, generate one. If `.specify/system-changes/<slug>/`
   already exists, add `-2`, `-3`, … Never overwrite an existing directory.

Set `CHANGE_DIR = .specify/system-changes/<slug>`.

## Read First

Read what already exists, so you update instead of duplicating:

- `.specify/memory/constitution.md`
- `engineering/decisions/*.md` — status and number of every ADR
- `engineering/capabilities/*/capability.yaml`
- `engineering/integrations/*/contract.yaml`
- `engineering/environments/*/environment.yaml`
- `engineering/standards/*.md`
- `specs/*/spec.md` — only the `Requires (engineering)` sections, to find features
  that depend on what this request touches

If `engineering/` does not exist, say so and treat every artifact as CREATE.

## Step 1 — Choose the route

| Route | Use it when |
|---|---|
| Feature Change | The goal is behaviour a user or caller can observe, and it needs no new or changed capability, integration or environment. |
| System Change | The goal is a shared ability, a connection to a tool or service, a dev/CI/production change, or a technical choice with lasting consequences. |
| Both | Split it. The System Change part comes first. The Feature Change part later references the capability with `requires: <id>@<version>`. |

If you cannot tell, stop and ask one question. Do not guess.

## Step 2 — Classify each piece of a System Change

Every piece gets exactly one kind:

| Kind | Test | Artifact |
|---|---|---|
| Decision | A choice between options with lasting consequences, or it conflicts with an accepted ADR or the constitution | `engineering/decisions/NNNN-<slug>.md` |
| Capability | A shared ability that features can depend on | `engineering/capabilities/<id>/capability.yaml` |
| Integration contract | A connection to an external tool or service, and its terms | `engineering/integrations/<id>/contract.yaml` |
| Environment | What must be true in development, CI or production | `engineering/environments/<id>/environment.yaml` |
| Standard | A rule every feature must follow | `engineering/standards/<topic>.md` |
| Enabling task | One-off setup work | listed in `classification.md` only |

## Step 3 — Is a decision required?

Answer **yes** if any of these is true:

- It adds a dependency, tool, service or vendor.
- There is more than one reasonable option.
- It changes or contradicts an accepted ADR or the constitution. The new ADR
  **supersedes** the old one; accepted ADRs are never edited.
- It is expensive to reverse.

Number a new ADR one higher than the highest existing number.

## Step 4 — Find the impact

For every capability, integration and environment id this request touches, find what
references it: `requires:` in specs, `capabilities:` and `integrations:` in
environments, `implemented_by:` in capabilities. List each as UPDATE (it must change)
or REVIEW (a human should check it still holds).

A version bump is needed only when an existing consumer's `requires: <id>@N` would
break.

## Step 5 — Write the classification

Create `CHANGE_DIR` and write `CHANGE_DIR/classification.md`:

```markdown
# Classification: <short title>

- **Slug**: <slug>
- **Date**: <YYYY-MM-DD>
- **Request**: <the request, quoted>
- **Route**: System Change | Feature Change | Both
- **Primary kind**: Decision | Capability | Integration contract | Environment | Standard
- **Decision required**: yes | no — <which rule in Step 3>
- **Feature spec required**: yes | no

## Why this route

<Two or three sentences. Name the test from Step 1 that decided it.>

## Artifacts

| Action | Kind | Path | Why |
|---|---|---|---|
| CREATE | Decision | engineering/decisions/NNNN-<slug>.md | <reason> |
| CREATE | Capability | engineering/capabilities/<id>/capability.yaml | <reason> |
| UPDATE | Environment | engineering/environments/<id>/environment.yaml | <reason> |
| REVIEW | Feature | specs/<NNN-name>/spec.md | requires <id>@<N> |

## Enabling tasks

- [ ] <one-off setup step>

## Verification

- <the check that proves the change works, e.g. a test or a command>

## Open questions

- [NEEDS CLARIFICATION: <question>]
```

For a Feature Change, keep the header, write `None` under Artifacts, and say which
existing capabilities the spec should list under `Requires (engineering)`.

## Report

Tell the user, each on its own line:

- `Route: <route>`
- `Slug: <slug>`
- The path to `classification.md`
- The next step:
  - System Change or Both → `/speckit-system-change-apply slug=<slug>`
  - Feature Change → `/speckit-specify <request>`

## Guardrails

- Only write inside `CHANGE_DIR`.
- Never invent the id or path of an existing artifact. If you think one exists but
  cannot find it, write it under Open questions.
- Never edit an accepted ADR. A change to one is a new, superseding ADR.
- Keep the classification short. It is a routing note, not a design document.
