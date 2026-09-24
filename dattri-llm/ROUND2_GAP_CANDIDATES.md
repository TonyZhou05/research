# Round-2 gap candidates — dattri-llm paper ↔ code

**Tip SHA:** `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb` (`origin/dev`, detached checkout `~/Desktop/dattri-llm`)
**Generated:** 2026-09-24 America/Toronto
**Restored:** 2026-09-24 ~04:00 ET (recovered after overwrite of 17-item thin report)
**Paper:** private `paper-round2.txt` / `paper.pdf` under `agent-context/Ship/dattri-llm-wrapup/` (not copied to hub)
**Prior:** round-1 `WRAPUP_GAP_CANDIDATES.md`; dual-LGTM C1–C5 (Y), C6 (N)
**Hard constraints:** no TRAIS-Lab issues/PRs; no public PDF

---

## Round-1 agreed items — status on a5c08fc

| id | title | status | evidence on tip |
|----|-------|--------|-----------------|
| C1 | README hero / on-the-fly → dattri_llm.task | **OPEN** | README.md:33 and :144 still `from dattri.task import AttributionTask`; hero loss_fn still `(params, batch)` + functional_call. examples/attribution/attribution_on_the_fly.py correctly uses dattri_llm.task. |
| C2 | Document pyproject extras beyond [transformers] | **PARTIAL** | README install documents `.[transformers]` and `.[attribution]` only. pyproject.toml also has `trl`, `olmo`, `dev`, `test` — not listed in the install block (examples/README does mention trl/ai2-olmo per-script). |
| C3 | Document HF Llama/Qwen RMSNorm recipe | **OPEN** | README only names RMSNorm in the layer-coverage bullet. Full recipe (layer_types + rms_norm_module_kwargs / variance_epsilon) lives in dattri_llm/gradient/hooks/config.py docstring and examples/projection/; not surfaced in root README. |
| C4 | Export OptimizerStateCallback (+ siblings) + capture contract | **DONE** | Commit a5c08fc: OptimizerStateCallback, ParameterSnapshotCallback, KroneckerCovarianceCallback eager-exported in dattri_llm/__init__.py; README 'Capture requirements' table documents LESS/AdamW/DVEmb/KFAC needs. |
| C5 | examples README barrier: on_the_fly torch-only | **PARTIAL** | examples/README.md still lists attribution_on_the_fly as needing `transformers`. Script is a torch-only MLP using dattri_llm.task (no transformers import). 74d5488 fixed install wording elsewhere but table row remains wrong. |
| C6 | LESS/AdamW/DVEmb/EKFAC smoke examples | **DECLINED** | Dual-declined (Opus effort veto). Still no examples/* for those methods; revisit only as optional short-fall. |

Recent commits checked: `74d5488` (example/install requirements), `a5c08fc` (export optimizer/snapshot/covariance callbacks + capture docs).

---

## Ranked round-2 candidates

Categories: `gap` | `inconsistency` | `short-fall` | `style` | `claim` | `repro`. Effort S/M/L. Status includes r1 linkage.

### R2-1 — C1 — README hero still imports dattri.task + functional_call
- **category:** `gap` · **effort:** S · **paper_risk:** none · **status:** `r1-agreed-open`
- **r1_overlap:** C1 / G1
- **evidence_paper:** §3.3 / Abstract: live capture from any `.backward()` including DDP/FSDP; closed-loop Attributor over live model.
- **evidence_code:** README.md:33,144 `from dattri.task import AttributionTask`; hero loss_fn(params, batch)+functional_call. Contrasts dattri_llm/task.py (model,batch) contract and examples/attribution/attribution_on_the_fly.py.
- **proposed_fix:** Replace both README snippets with `from dattri_llm import AttributionTask` (or `from dattri_llm.task import …`) and a `(model, batch) -> loss` that calls the live model; mention `AttributionTask.from_dattri` as the adapter for dattri-style losses.
- **why_effective:** Highest-traffic copy-paste path still contradicts the paper's distributed/live-hook story and the library's own task contract.

### R2-2 — C5 — examples README marks on_the_fly as needing transformers
- **category:** `inconsistency` · **effort:** S · **paper_risk:** none · **status:** `r1-agreed-open`
- **r1_overlap:** C5
- **evidence_paper:** N/A (docs). Paper 'few lines' framing assumes minimal deps for basic attribution.
- **evidence_code:** examples/README.md table: attribution_on_the_fly → `transformers`. Script uses only torch + dattri_llm MLP; CI still installs transformers before running it.
- **proposed_fix:** Change Extra requirements to `—` (torch-only). Optionally add a one-line note that live HF/LLM demos need `.[transformers]`.
- **why_effective:** False barrier; users think the smallest end-to-end demo needs HF when it does not.

### R2-3 — C2 — README install omits trl / olmo / dev extras
- **category:** `short-fall` · **effort:** S · **paper_risk:** none · **status:** `r1-agreed-open`
- **r1_overlap:** C2 / I4
- **evidence_paper:** Abstract + §3.3 claim Transformers, TRL, and OLMo integrations.
- **evidence_code:** pyproject.toml extras: attribution, transformers, trl, olmo, dev, test. README install block only shows transformers + attribution.
- **proposed_fix:** Extend install subsection with `.[trl]`, `.[olmo]`, `.[dev]` (and `.[test]`) one-liners pointing at examples/trainers/*.
- **why_effective:** Paper-named frameworks are installable but invisible from the README install story.

### R2-4 — C3 — HF LlamaRMSNorm recipe missing from root README
- **category:** `gap` · **effort:** S · **paper_risk:** low · **status:** `r1-agreed-open`
- **r1_overlap:** C3
- **evidence_paper:** Appendix A.1: normalization layers (LayerNorm, RMSNorm, …) are first-class factorized types.
- **evidence_code:** README only lists RMSNorm in Key Features. Recipe with layer_types + module_kwargs / rms_norm_module_kwargs is in hooks/config.py and examples/projection/README.md + gradient_projection.py — not linked from root.
- **proposed_fix:** Add a short 'Custom / HF norm layers' subsection (or link) showing LlamaRMSNorm → nn.RMSNorm declaration with rms_norm_module_kwargs(variance_epsilon=…).
- **why_effective:** Llama/Qwen users hit unrecognized RMSNorm classes; the fix exists but is buried.

### R2-5 — W1 — Abstract 'and and'
- **category:** `style` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** W1
- **evidence_paper:** Abstract: '…across four GPUs, and and delivers faster and more faithful attribution… at 7B scale.'
- **evidence_code:** N/A (paper-only).
- **proposed_fix:** Delete the duplicated 'and'.
- **why_effective:** Highest-visibility grammar glitch in the abstract.

### R2-6 — I1 — Curvature speedup 1.3–2.5× (abstract) vs 1.4–3.1× (body)
- **category:** `inconsistency` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** I1
- **evidence_paper:** Abstract: curvature 1.3–2.5×. Intro Efficiency / conclusion: 1.4–3.1× (§B.3). Appendix also cites projected K-/EK-FAC 1.6–2.5× and full-dim 1.2–1.6× / 1.4–2.1× vs specific libs.
- **evidence_code:** experiments/benchmark/{throughput,routing,benchmark}.py measure cells; do not encode abstract range.
- **proposed_fix:** Reconcile abstract to one audited table slice (or qualify 'vs fastest baseline on completed projected-curvature cells').
- **why_effective:** Reviewers read abstract first; mismatched × ranges look unreconciled.

### R2-7 — I2 — 'gradient similarity at parity' vs GradDot 2.7–3.4× / 1.4–8.9×
- **category:** `claim` · **effort:** S · **paper_risk:** low · **status:** `parked-elevated`
- **r1_overlap:** I2
- **evidence_paper:** Abstract: 'gradient similarity at parity.' §4: GradDot achieves 2.7–3.4× Kronfluence and 1.4–8.9× Bergson on completed scales.
- **evidence_code:** experiments/benchmark/throughput.py; README maps Grad-Dot → TracInAttributor.
- **proposed_fix:** Replace 'at parity' with the measured range, or define parity as a specific matched-hardware row and move the × gains into the same sentence.
- **why_effective:** Direct abstract↔results contradiction.

### R2-8 — W2 — 'introduct' + 'motivates' + latex 'Eq. equation N'
- **category:** `style` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** W2 (elevated with more typos)
- **evidence_paper:** 'we will introduct in §3.2'; Related Work 'building blocks motivates'; repeated 'Eq. equation 1/2/3/4' and 'Equation equation 1' (A.1); A.3 'moments the coming optimizer.step()'.
- **evidence_code:** N/A.
- **proposed_fix:** introduct→introduce; motivates→motivate; fix equation refs to 'Eq. (N)'; repair 'moments the coming' grammar.
- **why_effective:** Cheap camera-ready polish; latex doubling is systematic in the extract.

### R2-9 — I3 — GradDot paper name vs algorithm label 'TracIn'
- **category:** `inconsistency` · **effort:** S · **paper_risk:** low · **status:** `parked-elevated`
- **r1_overlap:** I3
- **evidence_paper:** Fig.1 lists TracIn · GradDot · GradCos; benchmarks name GradDot; Related Work distinguishes single-ckpt Grad-Dot from multi-ckpt TracIn.
- **evidence_code:** tracin.py: algorithm='GradCos' if normalized_grad else 'TracIn' — never 'GradDot'. README table: Grad-Dot / Grad-Cos | TracInAttributor.
- **proposed_fix:** Set algorithm='GradDot' for single-checkpoint raw dot (or document alias in score metadata + paper footnote).
- **why_effective:** Artifacts grepping `algorithm` disagree with paper method names.

### R2-10 — N1 — paper 'Attributor' vs code BaseAttributor
- **category:** `style` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** N1
- **evidence_paper:** Fig.1 caption: 'subclassing Attributor'; entry API 'Attributor vs HookManager'.
- **evidence_code:** Public ABC is BaseAttributor / BaseInnerProductAttributor; no class named Attributor. TrajectoryAttributor exists but is also unexported (see R2-18).
- **proposed_fix:** Paper: 'subclassing BaseAttributor' (or 'an *Attributor'). Optional README gloss: 'Attributor family'.
- **why_effective:** Removes 'where is Attributor?' friction after Fig.1.

### R2-11 — S1 — Fig.1 / §3.1 method strip omits shipped LESS & AdamW-influence
- **category:** `short-fall` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** S1
- **evidence_paper:** Fig.1: TracIn · GradDot · GradCos · K-FAC · EK-FAC · DVEmb. §3.1 closed-loop list matches. Body/A.3 and §4.2 evaluate LESS transform and AdamW-influence heavily.
- **evidence_code:** LESSAttributor, AdamWInfluenceAttributor exported + README Supported Algorithms + tests.
- **proposed_fix:** Add LESS · AdamW-influence to Fig.1 strip or a caption footnote 'also: LESS, AdamW-influence (A.3)'. Align §3.1 sentence.
- **why_effective:** Stops 'paper under-sells the library' / figure incomplete vs README.

### R2-12 — G2 — FLOP-aware routing weakly surfaced for users
- **category:** `gap` · **effort:** M · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** G2
- **evidence_paper:** Efficiency pillar + §3.2 + Appendix A.4 + B.4 routing ablation.
- **evidence_code:** Realized as maybe_use_materialized_gram/norm, should_materialize in gradient/ops/{dot,projection}.py. Root README mentions dynamic routing once; no user-facing 'force/inspect route' section. capture_style auto/factorized/materialized is in HookManagerConfig docstring only.
- **proposed_fix:** README subsection: cost-model symbols, HookManagerConfig(capture_style=...), how experiments/benchmark/routing.py pins routes; optional debug log of chosen mode.
- **why_effective:** Ties the paper's main systems claim to grep-able knobs reviewers can verify.

### R2-13 — S2 — No docs/ tree (Sphinx/MkDocs); ruff excludes docs/ that does not exist
- **category:** `short-fall` · **effort:** M · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** S2
- **evidence_paper:** Positions dattri-LLM as a library with HookManager, Attributor, callbacks, AttributionArguments, GradientStreamer, AttributionScore.
- **evidence_code:** Tree: README + examples/**/README + CLAUDE.md + rich docstrings. No docs/. pyproject.toml ruff.exclude lists 'docs/'.
- **proposed_fix:** Minimal MkDocs/Sphinx API index from docstrings, or expand README API index; remove stale docs/ exclude if deferred.
- **why_effective:** ICLR systems/library papers invite 'is this research code?' risk without a stable API page.

### R2-14 — I4 — Install story vs 'few lines' hero (extras + wrong task)
- **category:** `inconsistency` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** I4 (+C1)
- **evidence_paper:** 'few lines' / practical library framing (Abstract, §3.4, §5).
- **evidence_code:** Hard dep only torch; hero needs transformers + (as written) dattri AttributionTask. Install shows pip install -e . then immediately a snippet that needs extras and currently wrong import.
- **proposed_fix:** Hero install one-liner `pip install -e ".[transformers,attribution]"` OR rewrite hero to torch-only MLP path matching attribution_on_the_fly.
- **why_effective:** Makes the abstract claim true on a clean machine.

### R2-15 — N2 — naming triad dattri-LLM / dattri-llm / dattri_llm
- **category:** `style` · **effort:** S · **paper_risk:** none · **status:** `parked-elevated`
- **r1_overlap:** N2
- **evidence_paper:** Product name dattri-LLM almost everywhere; rare dattri-llm / dattri_llm.
- **evidence_code:** Engineering names consistent (dattri-llm, dattri_llm); README title dattri-llm.
- **proposed_fix:** Camera-ready first-mention footnote: dattri-LLM (`pip install dattri-llm`, `import dattri_llm`).
- **why_effective:** Prevents naming bikesheds in review.

### R2-16 — OLMo integration: paper/§3 says trainer.train() vs fidelity fit()
- **category:** `inconsistency` · **effort:** S · **paper_risk:** low · **status:** `new`
- **r1_overlap:** none (new)
- **evidence_paper:** §3.3: wrapping trainer.train() with hm.collect() for HF/TRL/OLMo. §4.2 / B.2: OLMo-3-7B capture by wrapping fit().
- **evidence_code:** examples/trainers/olmo_trainer.py uses ai2-olmo Trainer; experiments/fidelity uses OLMo-core fit(). Two different OLMo stacks.
- **proposed_fix:** Paper: distinguish ai2-olmo Trainer.train vs OLMo-core Trainer.fit; README/examples clarify which extra covers which demo.
- **why_effective:** Reviewers reproducing §4.2 will look for train() and hit the wrong package API.

### R2-17 — olmo extra pins ai2-olmo but fidelity needs ai2-olmo-core 2.6.0
- **category:** `repro` · **effort:** M · **paper_risk:** med · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** §4.2 / B.2 claim unmodified OLMo-core trainer for OLMo-3-7B fidelity.
- **evidence_code:** pyproject.toml olmo/dev → `ai2-olmo`. experiments/fidelity/README.md: need ai2-olmo-core (2.6.0). examples/trainers/olmo_trainer.py imports olmo.* from ai2-olmo.
- **proposed_fix:** Split extras (olmo vs olmo-core) or document both; pin versions used in paper tables; add to repro appendix.
- **why_effective:** Paper's headline 7B fidelity path is not what `pip install -e ".[olmo]"` installs.

### R2-18 — TrajectoryAttributor / ReplayGradientSource / TrajectorySnapshots not public exports
- **category:** `gap` · **effort:** S · **paper_risk:** none · **status:** `new`
- **r1_overlap:** none (related to N1)
- **evidence_paper:** §3.4 extensibility: subclass Attributor over uniform gradient stream; trajectory methods (DVEmb, AdamW-influence) central in §4.2.
- **evidence_code:** trajectory.py defines TrajectoryAttributor used by AdamW/DVEmb; ReplayGradientSource in streaming.py; neither in dattri_llm/__init__.py _LAZY_EXPORTS / __all__. Extenders must deep-import.
- **proposed_fix:** Export TrajectoryAttributor (+ optionally ReplayGradientSource, TrajectorySnapshots) from package root with docstring pointers.
- **why_effective:** Matches paper extension-point story for trajectory methods beyond copying LESS/AdamW.

### R2-19 — No runnable examples for LESS / AdamW-influence / DVEmb / EK-FAC
- **category:** `short-fall` · **effort:** M · **paper_risk:** low · **status:** `r1-declined-revisit`
- **r1_overlap:** C6 (r1-declined-revisit)
- **evidence_paper:** §4.2 / Fig.3 center AdamW-influence & EK-FAC; A.3 LESS; methods are first-class in design.
- **evidence_code:** examples/ only TracIn (+ token GradDot/KFAC). Tests exist (test_less, test_adamw_influence, test_dvemb, test_kronecker) but no examples/* scripts. Dual declined C6 as not-small.
- **proposed_fix:** Add compact CPU smokes derived from tests (or one multi-method script) behind examples/attribution/; keep CI optional if heavy.
- **why_effective:** Paper's fidelity heroes are invisible in examples/; raises 'research-only method' risk despite shipped APIs.

### R2-20 — Fidelity model ladder: paper §4.2 trio vs experiments broader ladder
- **category:** `repro` · **effort:** M · **paper_risk:** med · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** §4.2 Setup: GPT-2, Qwen2.5-1.5B, OLMo-3-7B. B.2 repeats the trio.
- **evidence_code:** experiments/fidelity/README.md evaluates also Qwen2.5-0.5B/3B and OLMo-2-1B; resource table mentions those scales. Main-text figures may omit them.
- **proposed_fix:** Either promote appendix table for extra scales or trim experiment README to paper trio; explicitly mark appendix-only scales.
- **why_effective:** Repro readers chasing every README model will not find matching paper claims/figures.

### R2-21 — B.6 invasive-capture hardware: paper L40S vs experiment README A40
- **category:** `repro` · **effort:** S · **paper_risk:** low · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** Table 10 (B.6): Pythia-0.5B on one L40S (48 GB).
- **evidence_code:** experiments/capture/README.md: one A40; shares benchmark workload. experiments/efficiency/ untracked local tree.
- **proposed_fix:** Align paper hardware string with the launcher that produced Table 10, or note both; ensure capture results provenance path is documented.
- **why_effective:** Small but checkable reproducibility mismatch on a dedicated ablation.

### R2-22 — Stale empty packages: dattri_llm/{algorithm,trainers} and gradient/trainers
- **category:** `gap` · **effort:** S · **paper_risk:** none · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** Fig.1 architecture shows clean gradient/attribution tiers.
- **evidence_code:** Three directories contain only __pycache__ (old trainers/algorithm layouts); no .py sources. setuptools include=dattri_llm* may still package them.
- **proposed_fix:** Delete empty dirs / pycache leftovers; confirm packages.find does not ship hollow namespaces.
- **why_effective:** Confuses contributors grepping trainers next to examples/trainers; looks like incomplete move.

### R2-23 — experiments/efficiency (+ offload) absent from experiments README / mostly untracked
- **category:** `repro` · **effort:** M · **paper_risk:** low · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** Efficiency claims cite benchmark/routing/throughput; capture ablation in B.6.
- **evidence_code:** experiments/README indexes benchmark/, fidelity/, capture/ only. experiments/efficiency/ is untracked (??); experiments/offload/ has results.jsonl, no README.
- **proposed_fix:** Either add to index with purpose/protocol or exclude from release tree; do not leave orphan result dumps unexplained.
- **why_effective:** Repro hygiene: shadowed experiment dirs invite divergent numbers.

### R2-24 — token_attribution example not in CI (fast or expensive)
- **category:** `gap` · **effort:** S · **paper_risk:** none · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** Abstract + §3.2 claim token-level attribution; B.5 documents examples.
- **evidence_code:** examples/attribution/token_attribution.py exists; .github/workflows/examples_test.yml and expensive-examples omit it (only gpt2_data_selection + olmo on expensive).
- **proposed_fix:** Add to expensive-examples (downloads gpt2) or a tiny synthetic token smoke in fast CI.
- **why_effective:** Paper feature with an example but no automated guardrail.

### R2-25 — Live GradientStreamer hard-requires transformers; README Compatibility understates TRL
- **category:** `inconsistency` · **effort:** S · **paper_risk:** low · **status:** `new`
- **r1_overlap:** partial I4
- **evidence_paper:** Abstract/§3.3 list Transformers, TRL, OLMo equally. Closed-loop attribute() is a primary entry.
- **evidence_code:** streaming.py imports transformers (set_seed, get_scheduler, Trainer-like step). README Key Features Compatibility bullet lists Hugging Face Trainer and OLMo — omits TRL (TRL appears later under Frameworks). Torch-only disk path works; live LLM path does not.
- **proposed_fix:** Document clearly: capture core = torch; live streamer = transformers extra; TRL/OLMo in Compatibility bullets. Consider lazy import errors with install hint.
- **why_effective:** Aligns dependency story with paper's three-framework claim and avoids silent ImportError on `pip install -e .`.

### R2-26 — §3.1 Attributor method list under-claims vs README / evaluations
- **category:** `claim` · **effort:** S · **paper_risk:** low · **status:** `new`
- **r1_overlap:** S1 (related)
- **evidence_paper:** §3.1: Attributor directly returns scores for TracIn, gradient dot/cosine, K-FAC and EK-FAC, and data-value embeddings — no LESS/AdamW.
- **evidence_code:** Seven attributors exported; §4.2 evaluates AdamW-influence as a dattri-LLM method.
- **proposed_fix:** Extend §3.1 list or add 'plus optimizer-aware methods (LESS, AdamW-influence; A.3)'.
- **why_effective:** Design section should match what §4 claims the library implements.

### R2-27 — Invasive capture naming: paper 'invasive linear io' vs code invasive_linear_io
- **category:** `style` · **effort:** S · **paper_risk:** none · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** B.6: optional capture mode (invasive linear io).
- **evidence_code:** Hook family string `invasive_linear_io` in hooks + experiments/capture.
- **proposed_fix:** Use monospace `invasive_linear_io` in paper; cross-link experiments/capture/README.md.
- **why_effective:** Lets readers grep the exact API token from the appendix.

### R2-28 — End-to-end paper-table reproduction runbook not centralized
- **category:** `repro` · **effort:** L · **paper_risk:** med · **status:** `new`
- **r1_overlap:** none
- **evidence_paper:** Reproducibility paragraph points to Appendix B.1–B.7 for protocols.
- **evidence_code:** Strong per-dir READMEs (benchmark, fidelity, capture) + pinned baselines in versions.py (bergson 0.26.1, kronfluence 1.0.1, logix 0.1.1). No single top-level mapping Fig.X / Table.Y → exact launcher flags + expected jsonl. Modal launchers exist but are tribal knowledge.
- **proposed_fix:** Add experiments/PAPER_FIGURES.md (or appendix mirror) mapping each main figure/table to command, hardware, seed, and result artifact path.
- **why_effective:** Converts good local READMEs into reviewer-usable camera-ready repro.

---

## Explicitly checked — still aligned

| Area | Verdict on a5c08fc |
|------|--------------------|
| HookManager + `with hm.collect()` / any `.backward()` | Present; examples + tests. |
| OffloadCallback / DataSelectionCallback / HookManagerCallback | Present; Fig.1 names match. |
| C4 callback exports + capture-requirements table | **DONE** (`a5c08fc`). |
| DDP / FSDP capture & data selection | Tests under `tests/gradient/test_data_selection_{ddp,fsdp}.py`, live sharded, optimizer FSDP. |
| Factorized Gradient + FLOP routing implementation | Present (`ops.dot` / `ops.projection`); surfacing is G2/R2-12, not absence. |
| TracIn / KFAC / EKFAC / DVEmb / LESS / AdamW APIs | Exported; unit tests exist. |
| Token-level attribution | `examples/attribution/token_attribution.py` + `test_token_granularity.py` (CI coverage thin — R2-24). |
| HF / TRL / OLMo example trainers | `examples/trainers/{transformers,trl,olmo}_trainer.py` + CI (olmo expensive/non-blocking). |
| Scaling harness to 110B | `experiments/benchmark/scaling.py` + throughput; not a library API gap. |
| Baseline version pins | `experiments/benchmark/utils/versions.py` (bergson 0.26.1, kronfluence 1.0.1, logix 0.1.1). |

---

## Rejected / out-of-scope

- **RJ-1:** C4 already DONE on a5c08fc — do not re-open as new gap.
- **RJ-2:** C6 dual-declined — only listed as optional R2-19 revisit.
- **RJ-3:** Core HookManager / DDP / FSDP / factorized ops / token granularity — still aligned.
- **RJ-4:** Opening TRAIS-Lab GitHub issues/PRs — hard out of scope.
- **RJ-5:** Publishing paper.pdf to public hub — hard out of scope; private ship paths only.
- **RJ-6:** Implementing missing TDA methods named only as related work (TrackStar, SOURCE, MAGIC, TRAK) — paper compares against them, does not claim to ship them.

---

## Suggested priority bands

1. **Ship/docs blockers (r1 open):** R2-1 (C1), R2-2 (C5), R2-3 (C2), R2-4 (C3), R2-14 (I4).
2. **Abstract/camera-ready claim integrity:** R2-5…R2-8, R2-7, R2-6, R2-11, R2-26.
3. **API/naming clarity:** R2-9, R2-10, R2-15, R2-18, R2-27.
4. **Repro / experiments alignment:** R2-16, R2-17, R2-20, R2-21, R2-23, R2-28.
5. **Optional depth:** R2-12, R2-13, R2-19, R2-22, R2-24, R2-25.

---

## Provenance

| Item | Value |
|------|-------|
| Code tip | `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb` on Mac `/Users/tonyzhou/Desktop/dattri-llm` |
| Paper extract | `…/dattri-llm-wrapup/paper-round2.txt` (+ private PDF sibling) |
| Round-1 | box `/workspace/research/dattri-llm/WRAPUP_GAP_CANDIDATES.md` + Mac hub-stage copy |
| Dual LGTM | `…/merged-dual-lgtm.md` |
| Hub board note | H1/H3/H4 agreed with C1–C5 @ ~2026-09-24 03:15 ET |
| Analyst | Dattri Research round-2 paper↔code (restored after overwrite) |

