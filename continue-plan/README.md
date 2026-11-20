# continue-plan

Shared agent skill for delivering a specification in reviewable slices: it resumes one slice from a repository plan, or creates a sliced plan when explicitly asked. `SKILL.md` is the entry point and [references/workflow.md](references/workflow.md) is the authoritative workflow. This README is for people; agents do not need it.

## Use

- Codex and Claude Code: `/continue-plan [plan path or filename fragment]`. Codex also accepts `$continue-plan`.
- Either agent also picks the skill up from requests such as "continue the multi-tab plan".

Project rules (plan location, translations, commit requirements, quality gates) stay in each repository's `AGENTS.md`; this skill does not replace them.

## Install

Codex reads the skill from `~/.agents/skills/continue-plan/`. Keep this folder there on every machine.

Claude Code only reads `~/.claude/skills`, so link the same folder there:

```powershell
New-Item -ItemType Junction -Path "$HOME\.claude\skills\continue-plan" -Target "$HOME\.agents\skills\continue-plan"
```

```sh
ln -s ~/.agents/skills/continue-plan ~/.claude/skills/continue-plan
```

Do not also keep a `continue-plan` skill in a repository's `.claude/skills` or `.agents/skills`; duplicate names make agents choose between two copies.

## Maintain

`~/.agents/skills` is a git repository; edit the skill here and commit the change. Keep the `description` in `SKILL.md` accurate, because both agents use it to decide when to load the skill.
