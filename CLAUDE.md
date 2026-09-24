# CLAUDE.md — research hub (TonyZhou05/research)

Fleet clay for implementers. Keep under one page.

## Repo

- Hub stage checkout: `~/Documents/Tonys-agent-playground/hub-stage/`
- Remote: `TonyZhou05/research` → https://tonyzhou05.github.io/research/
- Publish pattern: edit here, commit, push (Pages from `main`)

## Roles (implementer ≠ reviewer on the same PR)

| Role | Who |
| --- | --- |
| Default implementer | Fable (Claude) — or Muse/DSH when Tongli asks |
| Blind dual reviewers | **Astra (Codex)** + **second seat** |
| Second seat | Fable **only if Fable did not implement**; else Kimi (or Muse when asked) |
| Merge | Tongli only — dual LGTM ≠ shipped |

Never forward one reviewer’s findings into the other kickoff.

## Rules

- No secrets in HTML, markdown, or commits.
- No force-push. Prefer branch + PR for coding / dual-LGTM pilots; small operational gap-truth HTML on `main` is OK when matching prior hub publish.
- Do not enable Field Brief / Jev / new Grok bots without Tongli ask.
- Never claim AUTH_OK without a live probe.

## Build / check

- Static HTML hub — open locally or push and check live URL.
- After harness edits: skim Gap register pills vs reality.
