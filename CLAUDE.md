# CLAUDE.md — research hub (TonyZhou05/research)

Fleet clay for implementers (Fable / Claude). Keep under one page.

## Repo

- Hub stage checkout: `~/Documents/Tonys-agent-playground/hub-stage/`
- Remote: `TonyZhou05/research` → https://tonyzhou05.github.io/research/
- Publish pattern: edit here, commit, push (Pages from `main`)

## Rules

- **Implementer ≠ reviewer** on the same PR.
- Blind dual LGTM: Astra (Codex) + Fable (Claude) review independently; never forward one review into the other kickoff.
- **Tongli-only merge.** Dual LGTM ≠ shipped.
- No secrets in HTML, markdown, or commits.
- No force-push. Prefer branch + PR for coding / dual-LGTM pilots; small operational gap-truth HTML on `main` is OK when matching prior hub publish.

## Build / test

- Static HTML hub — open locally or push and check live URL.
- After harness edits: skim Gap register pills vs reality.

## Things agents get wrong

- Claiming AUTH_OK without a live probe.
- Treating dual LGTM as merge permission.
- Enabling Field Brief / Jev / new Grok bots without Tongli ask.
