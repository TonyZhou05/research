# REVIEW.md — blind dual-LGTM checklist

Identical reviewer pass for every PR. Reviewers: **Astra = Codex**, **Fable = Claude**.
Each reviewer sees **PR + intent/spec/plan only** — never the other reviewer’s notes.

## Role check

- [ ] I am **not** the implementer on this PR
- [ ] I have not read the other reviewer’s findings

## Against plan / acceptance

- [ ] Diff matches the stated plan / Ship note (no scope creep)
- [ ] Observable acceptance criteria hold (links, pills, paths)
- [ ] No secrets, tokens, Keychain material, or private credentials in the diff
- [ ] No force-push / history rewrite instructions
- [ ] Tests or smoke notes present when claimed

## Severity

- **P0 / P1** — block dual LGTM; request fix pass on same branch
- **P2** — note for fix pass; do not silently rewrite history

## Verdict

- [ ] **LGTM** (agent-complete only — Tongli still merges)
- [ ] **Request changes** (list P0/P1)

## Output

File a short note under vault `Ship/builds/` (or PR comment) with: reviewer seat, PR URL, verdict, findings. No secrets.
