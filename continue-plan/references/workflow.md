# Sliced Implementation Workflow

Use this workflow for a specification delivered in reviewable slices. User instructions take precedence; applicable project instructions define required reading, confirmation gates, verification, translation and delivery requirements. This workflow supplies the common execution steps. Read linked project rules before editing; resolve consequential conflicts instead of silently choosing a different contract.

## Plan and State

The specification owns behavior; the plan owns slice scope, dependencies, status and decisions. Git and the working tree provide evidence, not proof that acceptance criteria pass. Each session focuses on one slice; an unfinished slice may span sessions.

Use `pending`, `in progress` and `done`. Start work as `in progress`. Set `done` only after acceptance criteria, required checks and review pass and the agreed delivery is ready to be completed. If the project requires a commit per slice, keep verified work `in progress` while awaiting commit authorization; set `done` in the plan as part of the authorized commit with the implementation. If committing fails, restore `in progress` and record the outstanding closeout. A commitless delivery is complete only when project or user instructions allow it. Never infer publication permission from a completed status.

Maintain these records where needed; accept equivalent existing fields without bulk migration:

- Decisions: decisions and reasons; approved behavior or contract changes also belong in the owning specification.
- Verification: actual commands, results, review outcomes and unverified scope with reasons. Separate historical evidence from checks run now. Re-run affected checks after subsequent changes.
- Resume: remaining work, blockers and the next concrete action or command. Clear when closeout is complete.
- Carry-over: constraints or work for later slices, distinct from this slice's remaining work.

Recheck relevant Preflight Findings and Carry-over against actual code. Write plans in English and keep a Simplified Chinese translation beside each new plan, named `<plan>.zh-CN.md`. The English plan is authoritative; whenever a plan with a translation changes, including status, decisions, verification and resume records, update the translation in the same change with equivalent content. Keep status values, slice tags, identifiers, paths, commands and Given/When/Then keywords in English in the translation. Maintain other translations where project or document instructions require them.

## Resume One Slice

1. **Reorient.** Confirm repository and branch. Read applicable instructions, the plan, referenced specifications, staged and unstaged diffs, relevant untracked files and slice history. Use `git log --oneline --fixed-strings --grep "<slice tag>"` when a tag exists. An unborn repository or uncommitted `in progress` slice is not itself a discrepancy. Preserve unrelated changes. Resolve earlier outstanding closeout before selecting the first unfinished slice; verify its dependencies. Ask when conflicting scope, unexplained state or unmet dependencies prevent safe continuation.
2. **Define success.** State the selected slice's objective, assumptions, exclusions, affected files/contracts, acceptance criteria and verification approach. Proceed within existing authorization; wait only for unresolved decisions or an explicit project confirmation gate. Inspect actual code, toolchain, scripts and configuration rather than inheriting another project's commands. Use bounded read-only exploration when useful.
3. **Implement.** Mark the slice `in progress`. Follow project TDD requirements for behavior changes, one observable criterion at a time. Use risk-appropriate document, configuration or visual checks for other work. Record decisions as they are made. When requirements change, identify affected current, later and previously completed slices; resolve behavioral/data/compatibility decisions, then synchronize the specification, dependencies and plan. Reopen affected completed work when necessary; preserve its historical results.
4. **Verify and review.** Run checks required by the project and affected contracts. Follow project independent-review thresholds; give reviewers requirements, exact diff scope and actual verification results. Fix actionable findings and rerun affected checks. If a prerequisite is unavailable, continue independent useful work, but keep unmet acceptance or mandatory gates unfinished. Record blocker, impact, unverified scope and next action; never substitute unit/simulated evidence for required browser, database or external verification.
5. **Deliver or hand off.** Inspect the final diff, update required user/contract documentation, and record results and remaining work. Follow the completion report required by the project. Commit only with explicit user authorization; existing authorization remains valid. Stage explicit paths belonging to this slice, including its plan and required translations, and preserve unrelated staged changes. For overlapping staged work, isolate the slice before committing. Use the plan's slice tag in the subject when specified. Apply the completion rule above; otherwise keep `in progress` and write Resume. Stop without starting the next slice.

Before an interruption or a long-session handoff, persist decisions, actual check results, remaining work, blockers and the next action. A fresh session must be able to resume from repository artifacts without conversation history.

## Planning (Only When Requested)

Use this section only when the user explicitly asks for a plan. Read the specification and resolve open behavior, compatibility and data decisions. Split by observable, independently verifiable outcomes. Technical prerequisites are valid slices when they name the dependency they unblock, independent verification and rollback. Avoid empty layers or placeholder infrastructure.

Put the plan next to its specification. Use a `-Plan.md` suffix, preserving existing project conventions. Adjust relative links to the plan's actual directory. Record scope, references, dependencies and acceptance criteria per slice; keep workflow steps here rather than copying them into plans. Specify whether delivery requires a commit, following project rules. Use a short slice tag for commit discovery; do not record a commit's own hash in its contents.

```markdown
# <Title> — Implementation Plan

Spec: [<Title>](<spec-file>.md)
Slice tag: `<tag>`
Delivery: <project-required completion and commit requirements>

## How to Continue

Use the shared continue-plan skill; resume one slice or outstanding closeout under project instructions.

## Slices

| # | Scope | Status | Depends on |
|---|-------|--------|------------|
| 1 | <Observable outcome> | pending | None |

### Slice 1 — <Title>

- Scope / exclusions:
- Spec refs:
- Depends on:
- Acceptance criteria: Given ..., when ..., then ...
- Decisions:
- Verification:
- Resume:
- Carry-over:
```

Create the plan's Chinese translation as described in "Plan and State", opening with a note that links the English plan and states that it is authoritative.

Present the plan for review, commit only when authorized, and stop before implementation unless the user requested it too.

## Parallel Work

Prefer sequential slices. Use parallel sessions only for independent slices in separate worktrees with independent implementation files. Assign one integrator for shared plan/status updates; preserve each slice's evidence when merging. Parallel execution never waives dependencies, verification or authorization.
