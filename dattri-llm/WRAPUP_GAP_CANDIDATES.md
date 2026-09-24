# dattri-llm wrap-up gap candidates (paper ↔ `dev`)

**Scope:** ICLR 2027 PDF `hub-stage/dattri-llm/paper/paper.pdf` vs public `TRAIS-Lab/dattri-llm` branch **`dev`** (tree SHA tip at analysis: `f46496040d97…`; 166 blobs; no GitHub releases).  
**Method:** `pdftotext` on the PDF; GitHub contents/raw for `dev` (no full clone; no issues/PRs opened on TRAIS-Lab/dattri-llm).  
**Audience:** dual Opus/GPT review for camera-ready / wrap-up polish.  
**Hard constraint honored:** hub-local write only.

**Naming flags (read first):**
| Surface | Form |
|---|---|
| Paper title / product | `dattri-LLM` (74× in extract) |
| PyPI / repo folder | `dattri-llm` (`pyproject.toml` `name`) |
| Python package | `dattri_llm` |
| Abstract typo | **“and and”** (end of abstract) |
| Other extract typos | **“introduct”** (§3 lead-in); **“motivates”** after plural “building blocks” (Related Work) |

Abstract speed claim **1.3–2.5×** (curvature) conflicts with intro/conclusion **1.4–3.1×** and with GradDot throughput numbers that are **not** “at parity.”

---

## Shortlist (≤12), ranked

### 1. `W1` — style — Abstract “and and”
- **Evidence (paper):** Abstract closes: “…scales attribution to 110B-parameter models across four GPUs, **and and** delivers faster and more faithful attribution… at 7B scale.”
- **Evidence (code):** N/A (PDF-only).
- **Effort:** S · **Paper-risk:** none · **Why effective:** Highest-visibility grammar glitch; free win before dual review / camera-ready.

### 2. `I1` — inconsistency — Curvature speedup range: abstract vs body
- **Evidence (paper):** Abstract: curvature methods **1.3–2.5×** faster than fastest existing library. Intro Efficiency bullet / conclusion: **1.4–3.1×** over compared libraries (§B.3). Appendix also cites projected K-/EK-FAC **1.6–2.5×**, full-dim **1.2–1.6×** / **1.4–2.1×** vs specific libs — different slices.
- **Evidence (code):** `experiments/benchmark/` (`throughput.py`, `routing.py`, README) implements the measurement ladder; does not encode the abstract number.
- **Effort:** S · **Paper-risk:** none (reconcile numbers to one audited table) · **Why effective:** Reviewers read abstract first; mismatched × ranges look like unreconciled drafts.

### 3. `I2` — inconsistency — “Gradient similarity at parity” vs GradDot throughput gains
- **Evidence (paper):** Abstract: “gradient similarity at parity.” §4 / appendix: for **GradDot**, dattri-LLM achieves **2.7–3.4×** throughput of Kronfluence and **1.4–8.9×** of Bergson on completed scales (same extract).
- **Evidence (code):** Benchmarks under `experiments/benchmark/`; README algorithm table maps Grad-Dot → `TracInAttributor`.
- **Effort:** S · **Paper-risk:** low (clarify “parity” = same-hardware wall-clock in a specific row, or drop/replace abstract phrasing) · **Why effective:** Direct abstract↔results contradiction; easy for a careful reviewer to catch.

### 4. `I3` — inconsistency — Paper “GradDot / GradCos / TracIn” vs single `TracInAttributor` + labels
- **Evidence (paper):** Fig. 1 lists **TracIn · GradDot · GradCos** as peer methods. Related work: TracIn = multi-checkpoint accumulation; Grad-Dot / Grad-Cos = single checkpoint (Charpiat et al.). Benchmarks repeatedly name **GradDot**.
- **Evidence (`dev`):** `dattri_llm/attribution/algorithm/tracin.py` — one class `TracInAttributor`; `normalized_grad=True` → metric cosine and `self.algorithm = "GradCos"`; else **`self.algorithm = "TracIn"`** (never `"GradDot"`). README table: “Grad-Dot / Grad-Cos | `TracInAttributor` | cosine via `normalized_grad=True`”.
- **Effort:** S (paper wording or one-line algorithm label / alias) · **Paper-risk:** low · **Why effective:** Aligns API, score metadata, and paper method names so artifacts / tables don’t disagree when someone greps `algorithm`.

### 5. `N1` — style / naming — “subclassing Attributor” vs `BaseAttributor`
- **Evidence (paper):** Fig. 1 caption: “subclassing **Attributor** adds an attribution method”; entry API described as **Attributor** vs **HookManager**.
- **Evidence (`dev`):** Public ABC is `BaseAttributor` / `BaseInnerProductAttributor`; concrete `*Attributor` classes. Top-level `__init__.py` exports those names — no class named `Attributor`.
- **Effort:** S · **Paper-risk:** none · **Why effective:** One-token paper fix (or README “Attributor family”) removes “where is Attributor?” friction for readers who open the repo after the figure.

### 6. `G1` — gap / short-form — README quickstart uses `dattri.task` + `functional_call`, not `dattri_llm.task`
- **Evidence (paper):** Compatibility story is live `.backward()` / trainer loops; setup snippet in §5 uses `from dattri_llm import DataSelectionCallback`.
- **Evidence (`dev`):** Root `README.md` hero example: `from dattri.task import AttributionTask` and `torch.func.functional_call` loss. Package ships `dattri_llm.task.AttributionTask` with **`(model, batch)`** live-model loss (required for DDP/FSDP hooks); `examples/attribution/attribution_on_the_fly.py` correctly uses `dattri_llm.task`. `AttributionTask.from_dattri` exists as adapter.
- **Effort:** S · **Paper-risk:** none (docs only) · **Why effective:** First-copy README path contradicts the paper’s distributed/live-hook claim and the library’s own task contract; high leverage for “reproducible few lines.”

### 7. `W2` — style — “introduct” + subject–verb “motivates”
- **Evidence (paper):** “…representation that we will **introduct** in §3.2…”; Related Work: “whose shared building blocks **motivates** the design…”
- **Evidence (code):** N/A.
- **Effort:** S · **Paper-risk:** none · **Why effective:** Cheap polish; signals care before rebuttal/camera-ready.

### 8. `S1` — short-fall — Fig. 1 method strip omits shipped LESS / AdamW-influence
- **Evidence (paper):** Fig. 1 methods: TracIn · GradDot · GradCos · K-FAC · EK-FAC · DVEmb (+ Offload / DataSelection callbacks). Body discusses LESS and AdamW-influence as supported transforms / methods.
- **Evidence (`dev`):** `LESSAttributor`, `AdamWInfluenceAttributor` exported from package and listed in README “Supported Algorithms”; tests `tests/attribution/test_less.py`, `test_adamw_influence.py`.
- **Effort:** S · **Paper-risk:** none (figure or caption footnote) · **Why effective:** Stops “paper under-sells the library” / “figure incomplete vs README” comments; optional if space-constrained (mark as optional).

### 9. `G2` — gap — FLOP-aware routing is core claim but weakly surfaced for users
- **Evidence (paper):** Efficiency pillar: exact factorized + materialized forms; **FLOP-aware cost model** per layer/op; Appendix B.4 routing ablation.
- **Evidence (`dev`):** Realized as `maybe_use_materialized_gram` / `maybe_use_materialized_norm` / `should_materialize` in `dattri_llm/gradient/ops/dot.py` (explicit flop inequalities); attributors call `_route_test_rep`. Root README mentions dynamic routing once; no dedicated user-facing “how to inspect / force route” section; no `docs/` tree on `dev`.
- **Effort:** S–M (short README subsection or paper appendix pointer to function names) · **Paper-risk:** none · **Why effective:** Ties the paper’s main systems claim to grep-able symbols reviewers/users can verify.

### 10. `S2` — short-fall — No Sphinx/MkDocs `docs/`; paper readers land on README only
- **Evidence (paper):** Positions dattri-LLM as a library with entry APIs (HookManager, Attributor, callbacks, AttributionArguments, GradientStreamer, AttributionScore).
- **Evidence (`dev`):** Tree has `README.md`, `examples/**/README.md`, `CLAUDE.md`, rich docstrings — **no `docs/`**. Examples cover hooks, attribution (incl. token), trainers (Transformers / TRL / OLMo), benchmarks. Tests are broad (hooks, DDP/FSDP data selection, ops, attributors).
- **Effort:** M (if full docs) or S (expand README API index only) · **Paper-risk:** none · **Why effective:** For an ICLR systems/library paper, a thin but stable API page reduces “is this research code?” risk; not blocking if README is tightened (see G1).

### 11. `I4` — inconsistency — Install story vs “few lines” attribution demo
- **Evidence (paper):** “few lines” / practical library framing; methods need scoring stack.
- **Evidence (`dev`):** `pyproject.toml`: hard dep **only `torch`**; `dattri` behind optional `[attribution]`. README install shows `pip install -e .` then immediately `TracInAttributor` / `dattri.task` usage that needs the attribution extra (and currently the wrong task import — G1).
- **Effort:** S · **Paper-risk:** none · **Why effective:** One install line (`pip install -e ".[attribution]"` or document lazy extras) makes the abstract claim true on a clean machine.

### 12. `N2` — style — Product spelling triad `dattri-LLM` / `dattri-llm` / `dattri_llm`
- **Evidence (paper):** Marketing name **dattri-LLM** almost everywhere; rare `dattri-llm` / one `dattri_llm` in setup prose.
- **Evidence (`dev`):** Consistent engineering names (`dattri-llm`, `dattri_llm`); README title `dattri-llm`.
- **Effort:** S · **Paper-risk:** none · **Why effective:** Camera-ready footnote or consistent first-mention (“dattri-LLM (`pip install dattri-llm`, `import dattri_llm`)”) prevents naming bikesheds in review.

---

## Explicitly checked — aligned / not shortlisted

| Claim / area | Verdict on `dev` |
|---|---|
| HookManager + `with hm.collect():` / any `.backward()` | Present: `dattri_llm/gradient/hooks/manager.py`; examples + tests. |
| HookManagerCallback / OffloadCallback / DataSelectionCallback | Present; paper Fig. 1 names match code. |
| DDP / FSDP | Tests: `test_data_selection_ddp/fsdp`, `test_live_sharded`, `test_optimizer_state_fsdp`, etc. |
| HF Transformers / TRL / OLMo | `examples/trainers/{transformers,trl,olmo}_trainer.py` + CI workflows. |
| Factorized gradients + routing | `Factorized` / `Gradient` + ops cost heuristics (G2 is surfacing, not absence). |
| TracIn / K-FAC / EK-FAC / DVEmb | Classes + tests; README table. |
| Token-level attribution | `examples/attribution/token_attribution.py`, `tests/attribution/test_token_granularity.py`. |
| Scales to 110B / 4 GPUs | Experiment harness `experiments/benchmark/scaling.py` + `throughput.py` (Qwen ladder incl. 110B); not a library API gap. |
| Package layout `gradient/` + `attribution/` + `utils/` | Matches paper Fig. 1 tiers and README Architecture section. |

---

## Suggested review order for dual models

1. Fix **W1, I1, I2, W2** in the PDF (abstract integrity).  
2. Decide **I3 / N1** paper-vs-API naming policy (one sentence in §3 + optional `algorithm="GradDot"` when single-ckpt dot).  
3. Repo wrap-up: **G1 + I4** README install/quickstart; optional **G2** routing subsection; optional **S1/S2/N2**.

---

## Provenance

| Item | Value |
|---|---|
| Paper | `hub-stage/dattri-llm/paper/paper.pdf` (ICLR 2027 under review extract) |
| Code ref | `TRAIS-Lab/dattri-llm@dev` via API/raw (no TRAIS issues/PRs) |
| Hub write | this file (+ optional JSON sibling) |
| Date | 2026-09-24 (America/Toronto) |
