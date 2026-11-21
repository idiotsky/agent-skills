# NuGetStrongName Skill — Implementation Plan

Status: pending
Slice tag: `nuget-strongname`
Delivery: Each slice requires acceptance checks, independent review, and an explicitly authorized commit containing the Skill and both plan translations. Verified work awaiting commit stays `in progress`. This plan does not authorize implementation, commits, or publication.

## Goal and How to Continue

Create a reusable `nuget-strongname` Skill that drives the `NuGetStrongName.Tool` CLI for arbitrary .NET repositories. The CLI, its behavior, and its releases are owned by [idiotsky/NuGetStrongName](https://github.com/idiotsky/NuGetStrongName) (`Docs/NuGetStrongName-Plan.md`); this plan owns only the Skill. Use the shared `continue-plan` skill, resuming one slice or its closeout at a time. Use `pending`, `in progress`, and `done`; record actual commands, results, blockers, and next actions. The English plan is authoritative; maintain its Chinese translation in the same change.

## Slices

| # | Outcome | Status | Depends on |
|---|---------|--------|------------|
| 1 | Generic Skill with pinned, hash-verified tool download, verified on Windows and Linux | pending | NuGetStrongName Slice 1 and its first GitHub Release |

### Slice 1 — Create the Generic `nuget-strongname` Skill

Scope and distribution:
- After the CLI is accepted and a GitHub Release exists, use `skill-creator` to create `nuget-strongname/` in this repository and follow `writing-for-agents` for English instructions. This repository is also the installed skill root, so no separate source copy or synchronization step exists. Do not duplicate download/signing/packaging logic from the CLI.
- Record the pinned tool version and its SHA-256 in the Skill's own files. Do not commit the tool `.nupkg`, keys, credentials, or third-party input packages to this repository.
- On first use, download `NuGetStrongName.Tool.<version>.nupkg` from the pinned `v<version>` GitHub Release into a writable per-version cache, verify its SHA-256 against the value recorded in the Skill (never only against a manifest fetched from the same release), then install that exact version from the cached local feed into an isolated `--tool-path` and invoke it. Reuse a cached, hash-verified copy on later runs. Do not install globally, modify the consumer checkout, or download a different version. A hash mismatch or failed download is a hard failure with clear diagnostics, never a fallback to another source.
- Use plain `https` download when the release is publicly reachable; when it requires authentication, use `gh release download` with the user's existing GitHub sign-in. Never embed tokens in the Skill.
- Check .NET SDK/runtime prerequisites before installing. Use platform-neutral instructions and detect Windows/Linux; PowerShell is not required and no Windows-specific path is hardcoded. The same directory works under any environment's actual skill root.

Required behavior:
- Define the Skill as a reusable NuGet strong-name workflow for arbitrary .NET repositories, without requiring particular project names, layout, target framework, or feeds.
- Document actual CLI invocation, package ID/explicit version and local input modes, dependency mappings, JSON results/exit codes, verification, and failure diagnosis.
- Use the consumer repository's effective NuGet configuration, source mapping, and authentication. Take an explicit user version, or query and freeze a stable version when latest is requested. Respect existing package management, including central versions and project-level PackageReference, without assuming fixed file locations.
- Use existing project/user conventions for output identities and dependency mappings; `-signed` is an example, not a universal naming requirement. Default to checking and producing packages; modify project references only when requested, with minimal changes and consumer verification.
- Do not hardcode .NET 11, package lists, user directories, repository names, or machine-specific paths beyond the pinned release coordinates. Reusability does not require speculative CLI features, plugin frameworks, or abstraction layers.
- Do not hardcode a key filename or output directory. When signing is needed, search the target repository for usable strong-name private keys, using its evaluated signing configuration to identify the intended key. Validate usability without exposing key material. If no usable key exists, report it and ask the user to supply one; if multiple candidates remain ambiguous, ask which to use. Do not generate a key or silently choose a different identity. Pass the selected key explicitly with CLI `--key-file`.
- Resolve output from the user's current instruction or an explicit repository setting; if neither supplies a directory, ask the user. Pass it explicitly with `--output`; known consumer output locations are task inputs. The already-strong-named path needs neither key selection nor an output-directory prompt.
- Document the `already-strong-named` success result: use the reported original package identity directly, skip signed-package generation, and verify the consumer after changing from a custom signed version.
- Do not automatically publish packages, bypass licensing checks, or equate a successful CLI exit alone with artifact verification.

Acceptance criteria:
- Skill validation passes. Following the Skill completes a real package download/sign/pack/verify flow plus a failure scenario with nonzero exit and useful diagnostics.
- Verify key discovery, missing/ambiguous-key prompts, explicit output selection, and an already-signed result without unnecessary prompts; no fixed key/output path appears in Skill instructions.
- Verify first-run download and hash check, cached reuse without re-download, and hard failure on a tampered or mismatched package, on Windows and Linux.
- Exercise the Skill in a second minimal .NET repository with a different directory/signing configuration and confirm no unrelated references or global configuration change.
- Retiring consumers' legacy tools is outside this plan.

Resume: Waiting for NuGetStrongName Slice 1 acceptance and its first GitHub Release; no Skill created.

## Verification and Handoff Record

No Skill exists; no downloads, tests, commits, or releases have run. Next action: begin Slice 1 after the CLI's first release.
