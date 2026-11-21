# ship

Shared agent skill for delivering approved changes on GitHub. Supported target integration branches are `main`, `master`, and `develop`. It checks the selected target's protection on GitHub: a confirmed-unprotected target gets a direct push; a protected target goes through a typed task branch, a PR, required checks, and a squash merge. If protection cannot be determined, it stops before publishing. `SKILL.md` is the entry point and the authoritative workflow. This README is for people; agents do not need it.

## Use

- Codex: `$ship`.
- Claude Code: `/ship`.
- An explicit request for the full shipping workflow also works. Being picked up automatically from a narrower request (commit only, push only, draft PR only) does not authorize the full workflow.

Project rules (validation commands, documentation, communication language, branch conventions) stay in each repository's `AGENTS.md` or `CLAUDE.md`; this skill does not replace them.

## Install

Codex reads the skill from `~/.agents/skills/ship/`. Keep this folder there on every machine.

Claude Code only reads `~/.claude/skills`, so link the same folder there:

```powershell
New-Item -ItemType Junction -Path "$HOME\.claude\skills\ship" -Target "$HOME\.agents\skills\ship"
```

```sh
ln -s ~/.agents/skills/ship ~/.claude/skills/ship
```

Do not also keep a `ship` skill in a repository's `.claude/skills` or `.agents/skills`; duplicate names make agents choose between two copies.

## Maintain

`~/.agents/skills` is a git repository; edit the skill here and commit the change. Keep the `description` in `SKILL.md` accurate, because both agents use it to decide when to load the skill.
