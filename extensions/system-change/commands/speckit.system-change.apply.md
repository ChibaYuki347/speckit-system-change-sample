---
description: "Create or update the engineering/ files listed in a System Change classification, and write its enabling tasks"
---

# Apply a System Change

Turn an approved classification into artifacts. This command writes the `engineering/`
files that describe the **current state** of the system, and a task list for the
one-off work. It does not change source code.

## User Input

```text
$ARGUMENTS
```

The input names the change: `slug=<slug>`. If it is missing and there is exactly one
directory under `.specify/system-changes/`, use it; otherwise ask.

Set `CHANGE_DIR = .specify/system-changes/<slug>`.

## Prerequisites

- `CHANGE_DIR/classification.md` must exist. If not, stop and point the user at
  `__SPECKIT_COMMAND_SYSTEM-CHANGE_CLASSIFY__`.
- If its Route is `Feature Change`, stop. Point the user at
  `__SPECKIT_COMMAND_SPECIFY__` instead.
- If it has open `[NEEDS CLARIFICATION]` items that affect an artifact, ask about
  them first.

## Templates

New files start from the templates installed with this extension:

| Kind | Template |
|---|---|
| Decision | `.specify/extensions/system-change/templates/adr-template.md` |
| Capability | `.specify/extensions/system-change/templates/capability-template.yaml` |
| Integration contract | `.specify/extensions/system-change/templates/contract-template.yaml` |
| Environment | `.specify/extensions/system-change/templates/environment-template.yaml` |

Follow the comments in each template, then delete the comments that only explain the
template.

## Execution

Work through the Artifacts table in this order, so each file can reference the ones
before it: Decision → Capability → Integration contract → Environment → Standard →
Feature REVIEW rows.

1. **CREATE** — copy the template to the path in the table and fill it in.
   - A new ADR gets `Status: proposed`. A human accepts it.
   - A new capability or contract gets `status: planned`. It becomes `active` only
     after its verification passes.
   - Cross-reference by id: a capability lists its `decisions` and
     `implemented_by`; a contract lists its `capabilities`; an environment lists the
     `capabilities` it must provide as `<id>@<version>`.
2. **UPDATE** — edit the file in place so it describes the new current state.
   - Bump `version` only if an existing consumer's `requires: <id>@N` would break.
   - Never edit an accepted ADR. Write a new ADR with `Supersedes: NNNN` and set only
     the old one's status line to `superseded by NNNN`.
3. **REVIEW** — do not edit feature specs. List them in the task list for a human.
4. Write `CHANGE_DIR/tasks.md`:

   ```markdown
   # Tasks: <short title>

   Change: <slug> — see classification.md

   ## Enabling tasks
   - [ ] T001 <one-off step, with the file or command it touches>

   ## Verification
   - [ ] V001 <check from the capability's verification list>

   ## Review
   - [ ] R001 Accept or reject ADR NNNN
   - [ ] R002 <feature spec to review>

   ## Done when
   - Verification passes, the capability and contract are set to `active`, and the
     ADR is accepted.
   ```

5. Append a `## Applied` section to `classification.md` listing every file you
   created or changed.

## Report

- The files created and updated.
- The path to `tasks.md`.
- What a human must decide next (usually: accept the ADR).

## Guardrails

- Write only under `engineering/` and `CHANGE_DIR`.
- Do not change source code, tests, CI files or feature specs in this command.
- Environments describe the desired state. Put setup steps in code (dev container,
  script, pipeline) and point to them; do not write a manual.
- Keep each YAML file short. If a field does not apply, delete it rather than
  writing "N/A".
