# BOARD_PATCH_ROUND2 — dattri-llm hub-local board draft

- **updatedAt:** 2026-09-24 ~04:10 ET (`2026-09-24T04:10:00-04:00` America/Toronto)
- **tipSha:** `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb` (short `a5c08fc`)
- **draft only:** writes `issues/board.proposed.json` — does **not** overwrite live `issues/board.json`
- **reminder:** hub-local only — **no TRAIS-Lab GitHub issues**; do not publish/commit GH Pages unless files stay local drafts under hub-stage
- **untouched:** `ROUND2_GAP_CANDIDATES.*` (R2-1…R2-28 id scheme preserved)

## Dual-LGTM round-2 gate

- Seats: Claude Opus 5.5 | Codex gpt-6-astra
- AGREED (22 both-YES): R2-1, R2-3, R2-4, R2-5, R2-6, R2-7, R2-8, R2-9, R2-10, R2-11, R2-12, R2-13, R2-17, R2-18, R2-19, R2-20, R2-21, R2-24, R2-25, R2-26, R2-27, R2-28
- NOT dual-YES (6): R2-2, R2-14, R2-15, R2-16, R2-22, R2-23

## Counts

| metric | n |
|--------|---|
| Updated cards | 4 (H1–H4) |
| New cards | 4 (H5–H8) |
| Total openIssues in proposed board | 8 |
| AGREED R2 ids referenced | 22 / 22 |

## Updated cards

| id | title | labels | source R2 | notes |
|----|-------|--------|-----------|-------|
| H1 | Polish README and installation docs for the final public handoff | hub-only, wrap-up, agreed | R2-1, R2-3, R2-4 | = C1 OPEN / C2 PARTIAL / C3 OPEN |
| H2 | Run a release checklist against supported examples and trainer wrappers | hub-only, wrap-up, **agreed** (new) | R2-19, R2-24 | R2-19 elevates former C6 |
| H3 | Document live-path dependency honesty and keep examples maintainable | hub-only, wrap-up, agreed, **reversed-r1** | R2-25 (agreed); R2-2 cited as reverse | see H3/C5 reverse |
| H4 | Record final attribution-method and compatibility notes for archival use | hub-only, wrap-up, agreed | R2-18 | C4 DONE retained on tip |

## New cards

| id | title | labels | source R2 |
|----|-------|--------|-----------|
| H5 | Camera-ready paper copyedit and claim/number consistency | hub-only, wrap-up, agreed, paper | R2-5, R2-6, R2-7, R2-8, R2-27 |
| H6 | Align paper method naming and coverage with shipped attributors | hub-only, wrap-up, agreed, paper | R2-9, R2-10, R2-11, R2-26 |
| H7 | Surface FLOP-aware routing and a minimal API docs index | hub-only, wrap-up, agreed | R2-12, R2-13 |
| H8 | Align OLMo/fidelity/hardware notes and add a paper-table runbook | hub-only, wrap-up, agreed, repro | R2-17, R2-20, R2-21, R2-28 |

## H3 / C5 reverse (critical)

Round-1 dual-YES on **C5** framed `attribution_on_the_fly` as torch-only and labeled **H3 agreed** (“fix examples README so on_the_fly requires only Torch + dattri_llm”).

Round-2 **R2-2 is BOTH-NO** (Opus NO / Astra NO) at tip `a5c08fc` / `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb`:

- **Refutation:** `streaming.py` (~line 670) imports `transformers` unconditionally, so the live / on-the-fly path **does** hard-require transformers.
- **Board action:** H3 title + notes rewritten. Prior C5 “torch-only barrier” is **not** dual-agreed anymore. Label `reversed-r1` added. `agreed` on H3 now refers only to **R2-25** (document live GradientStreamer transformers hard-require; README Compatibility understates TRL).
- **Do not** leave H3 as “agreed” in the old C5 sense.

## Items NOT agreed (and why)

### Both-NO
| id | why |
|----|-----|
| R2-2 | Refutes C5; transformers hard-required via streaming.py:~670 |
| R2-16 | No OLMo `Trainer.train()` in tree — claim refuted |
| R2-22 | `dattri_llm/{algorithm,trainers}` are untracked `__pycache__` leftovers, not HEAD packages |

### Split (Opus YES / Astra NO)
| id | why |
|----|-----|
| R2-14 | Install extras already present; combining install commands not established |
| R2-15 | Product / distro / import naming triad is legitimate |
| R2-23 | `experiments/efficiency` (+ offload) untracked / absent from HEAD |

These six must **not** receive an `agreed` label on any card.

## AGREED R2 → card map

| R2 | card |
|----|------|
| R2-1 | H1 |
| R2-3 | H1 |
| R2-4 | H1 |
| R2-5 | H5 |
| R2-6 | H5 |
| R2-7 | H5 |
| R2-8 | H5 |
| R2-9 | H6 |
| R2-10 | H6 |
| R2-11 | H6 |
| R2-12 | H7 |
| R2-13 | H7 |
| R2-17 | H8 |
| R2-18 | H4 |
| R2-19 | H2 |
| R2-20 | H8 |
| R2-21 | H8 |
| R2-24 | H2 |
| R2-25 | H3 |
| R2-26 | H6 |
| R2-27 | H5 |
| R2-28 | H8 |

## Output paths

1. `/Users/tonyzhou/Documents/Tonys-agent-playground/hub-stage/dattri-llm/issues/board.proposed.json`
2. `/Users/tonyzhou/Documents/agent-context/Ship/dattri-llm-wrapup/BOARD_PATCH_ROUND2.md` (+ hub-stage copy)
3. `/Users/tonyzhou/Documents/agent-context/Ship/dattri-llm-wrapup/BOARD_PATCH_ROUND2.json` (+ hub-stage copy)

## Ambiguities / judgment calls

- H2 gained `agreed` because R2-19 (ex-C6) is now dual-YES; release-checklist title retained rather than splitting a separate “smoke examples” card.
- R2-18 folded into H4 (archival / public export surface) rather than a ninth card.
- Paper items split H5 (copyedit/claims) vs H6 (method naming/coverage) to keep paper vs library edits separable.
- Top-level `tipSha` added on proposed board (not present on R1 live board) so Manage Bot can pin a5c08fc without scraping notes.
