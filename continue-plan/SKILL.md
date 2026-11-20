---
name: continue-plan
description: Resume one implementation slice from an existing repository plan, or create a sliced plan for a specification when explicitly asked. Use when asked to continue a plan, start its next slice, finish an outstanding slice closeout, or split a specification into a plan.
---

# Continue a Plan

Read [the shared workflow](references/workflow.md). Resolve links inside this skill, such as `references/`, against this skill's directory. Resolve relative links inside a project document, such as a plan's specification link, against that document's directory. Resolve every other project path, including user-supplied plan paths, against the repository root.

## Create a plan

Only when the user explicitly asks to plan a specification, follow the workflow's "Planning (Only When Requested)" section and stop there. Do not start implementation unless the user also asked for it.

## Locate the plan

Otherwise locate the plan in the current repository:

1. Use an explicit plan path first. Report a missing path rather than silently choosing another plan.
2. Otherwise discover plans in the documentation locations named by project instructions, falling back to `Docs/` or `docs/`. Match `*-plan.md` case-insensitively, including files directly in those directories and subdirectories, and deduplicate paths that resolve to the same file. Filter by any supplied filename fragment. Translations such as `*-plan.zh-CN.md` are not candidates; read and maintain them when the plan or project instructions require it. Exclude discovered plans whose header declares `Status: done`; if every match is excluded, report the completed plans that matched instead of reporting no plan. An explicit plan path is never excluded.
3. Read candidate slice status and any outstanding handoff. Accept existing layouts and missing optional fields; require enough information to identify scope, dependencies and acceptance criteria. Report unusable plans separately from completed ones.
4. Inspect relevant working-tree changes and slice history before declaring a plan complete. Legacy `done` entries may still have outstanding closeout. If several plans are actionable, show their next slice or closeout and ask which to use. If none is actionable, distinguish complete, unusable and absent plans, then stop.

Follow the workflow for the selected plan. Continue only one slice or its outstanding closeout; this request does not authorize a new plan, subsequent slices, commits or publication beyond existing user authorization.
