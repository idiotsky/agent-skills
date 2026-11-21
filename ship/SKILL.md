---
name: ship
description: Deliver approved GitHub changes to main, master, or develop with a direct push when unprotected or a PR, required checks, and squash merge when protected. Use for the full shipping workflow; commit-only and push-only requests retain their narrower scope.
---

# Ship

Deliver the authorized change on GitHub while keeping the target integration branch linear. Supported targets are `main`, `master`, and `develop`. Check remote protection first: use direct delivery only when the target is confirmed unprotected; otherwise use the PR route. Follow repository instructions for validation, documentation, and communication language.

## Authorization

An explicit request to run `$ship` (Codex), `/ship` (Claude Code), or the full shipping workflow authorizes committing the agreed changes and either pushing directly to the confirmed-unprotected target or pushing a task branch, creating/updating its PR, and squash merging after required gates pass. Continue without repeated confirmation within that scope. Automatic skill discovery grants no authorization: preserve narrower requests such as commit-only, push-only, or draft-PR-only.

Creating or editing this skill does not authorize shipping the current repository. Respect user constraints, tool permissions, and repository rules. Never bypass protection, approve for a human, force-push the target, or change repository settings as part of shipping.

## 1. Establish scope

Inspect applicable AGENTS.md and CLAUDE.md files, current branch, staged/unstaged diffs, outgoing commits, remotes, existing PRs, and required owning documents. Identify intended files, existing user changes, destination repository, and PR base. Resolve the established integration branch as `base`: only `main`, `master`, and `develop` are supported. If the target is ambiguous or outside this set, stop and ask before publishing. Use this verified `base` throughout preparation, protection checks, publication, merge verification, and local synchronization.

Briefly state the scope, branch, and checks. Ask only when ownership, behavior, destination, or conflicting requirements remain ambiguous. Discover available GitHub connectors first; use them when sufficient, otherwise use authenticated GitHub CLI and Git. Discover actual test and build commands from the repository.

Proceed when every intended file and outgoing commit belongs to the authorized task and the destination is known.

## 2. Choose the route from GitHub protection

Resolve the target GitHub host/repository from the actual push remote and upstream; do not assume origin. This skill currently supports GitHub only. Confirm the verified `base` exists. Branch names alone do not determine protection; query GitHub for the selected target.

Read the branch protection indicator with `GET /repos/{owner}/{repo}/branches/{base}` and all pages of active applicable rules with `GET /repos/{owner}/{repo}/rules/branches/{base}`. The rules endpoint includes inherited organization rules. Consult classic protection details when needed to understand required gates. Reference: [GitHub branches](https://docs.github.com/en/rest/branches/branches) and [applicable branch rules](https://docs.github.com/en/rest/repos/rules#get-rules-for-a-branch).

- Protected indicator true or any active applicable rule: use the PR route below, even if the current account has bypass privileges. A fully locked branch may also prevent PR merges; report that blocker instead of changing settings.
- Successful authoritative responses showing protected false and no active applicable rules: use direct delivery to `base`.
- Missing permissions, failed requests, ambiguous results, or an unsupported API: report the uncertainty and stop before publishing. A 403/404 is not proof that `base` is unprotected. Use an equivalent authoritative GitHub view if available.

Recheck protection immediately before a direct push. If protection appears, preserve the task commits on a typed branch and switch to the PR route. Never probe protection by attempting a push.

## 3. Prepare and validate

For the PR route, name new branches `<type>/<short-kebab-description>`: `feat/task-timer`, `fix/token-refresh`, `docs/continue-plan-commands`, `chore/update-dependencies`, or `refactor/session-service`. Prefer the change type over a creator prefix such as codex/ or claude/. Explicit user branch conventions override defaults.

Reuse an existing suitable task branch and PR; preserve published names unless renaming is requested. For the PR route from `base`, create the task branch before committing or pushing. For direct delivery, prepare the local `base` without discarding work and synchronize it with the remote using `--ff-only`; inspect any existing outgoing commits. If task work already lives on another branch, transfer only its authorized changes through a fast-forward or reviewed cherry-pick, without a merge commit or implicit history rewrite. Inspect any existing local commits before carrying them into the PR.

Fetch the base and review the entire outgoing diff and commit range, including earlier commits, for either route. Ensure direct delivery adds no merge commits to `base`. Preserve unrelated changes and staged content; use an isolated worktree when needed. Never implicitly discard, stash, amend, or include unrelated work.

Run impact-based repository checks and review the final diff. Documentation-only changes need relevant link, consistency, encoding, and diff checks. Follow repository test-first requirements for fixes introduced during shipping. Update required plan/closeout records to reflect actual validation and commit state.

Stage only explicit authorized paths or hunks. Proceed when applicable checks pass and the staged diff contains exactly the intended change. Unavailable required checks remain blockers.

## 4. Commit and publish

Use Conventional Commit subjects describing the actual diff. Keep each body paragraph on one line; omit AI signatures unless requested. Preserve existing commits unless rewriting is authorized.

For direct delivery, recheck protection, commit on `base`, and push `base` normally without force. Verify the intended commits are reachable from the remote `base`. If the remote advanced, fetch and reconcile without a merge commit or unauthorized rewrite, then repeat affected checks. If protection rejects the push, inspect current rules and switch to a typed task branch and the PR route. Other rejections require diagnosing their actual cause. After a successful direct push, monitor any triggered CI for the pushed SHA; report failures and repair within scope using the same validation and two-attempt limit as below. Skip PR creation and squash merge, then perform the final synchronization/report step.

For the PR route, push the task branch with an upstream. On rejection, inspect the cause; avoid blind retries or force-pushes.

Create a PR against the verified base or update the existing PR for this branch. Use English titles and descriptions unless directed otherwise, follow the repository template, and use an available PR-writing skill when applicable. Describe the problem, resulting behavior, scope, and actual validation. For multiline CLI text, use a temporary body file with `--body-file`.

Proceed when the remote contains the intended commits and the correct PR exists. Record the PR URL and latest head SHA.

## 5. PR route: wait and resolve gates

Monitor checks and review/mergeability state in the current chat with bounded waits. Do not create an automation, scheduled task, or recurring loop. When the host provides its own PR/CI monitoring tools, use them instead of polling. Report meaningful changes and provide concise progress updates during long waits.

Evaluate required checks, human approvals, blocking conversations, and conflicts against the latest head. Pending, missing, cancelled, or failed required checks do not pass. Distinguish optional skipped jobs from required gates.

Fix failures attributable to this change within authorized scope, run appropriate local checks, commit and push, then verify the new head from the beginning. Assess actionable automated feedback with judgment. Stop after two repair attempts fail on the same cause and report evidence and the next action instead of repeating ineffective mutations.

If updating from the base is required, prefer merging the base into the task branch when repository rules permit; squash merge still keeps `base` linear. Rebase a published branch only with authorization to rewrite it, using a lease-protected push. Re-run affected checks after changes.

Stop with the PR link and exact next action when a human decision/review, unavailable permission, external outage, or scope conflict blocks progress. Resume when the condition changes. Do not claim completion while required gates remain unsatisfied.

## 6. PR route: squash merge

Immediately before merging, re-read the head SHA, base, check results, reviews, and mergeability. If the head changed, validate the new head first. Use expected-head matching when supported by the tool.

Squash merge using a Conventional Commit title and accurate body, producing one commit on `base` per PR. If squash is unavailable, report the blocker; do not substitute a merge commit or change settings.

Verify the PR is actually merged and its merge commit is reachable from the remote base. A successful merge request or enabled auto-merge alone is not completion.

## 7. Synchronize and report

Fetch, switch back to `base` when local work can be preserved, and synchronize using `--ff-only`.

Squash merging can leave local `base` containing the original task commits and therefore diverged. Preserve those commits on the task or a recovery branch and report the condition; do not reset `base` without explicit authorization. Distinguish successful remote merge from incomplete local synchronization.

Clean up only the merged task branch after checking for unique unmerged work, active worktrees, and other PRs or collaborators using it. Squash merging may prevent Git from recognizing equivalent commits; retain the local branch rather than forcing deletion without authorization.

Report the selected route, protection evidence, pushed commit or PR URL and squash commit, checks, and local branch/worktree state, including remaining cleanup. For direct delivery, PR and merge steps are not applicable. Claim remote success only after verifying the pushed commits or actual merge.