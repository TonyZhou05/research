# Round-3 paper↔code candidates — dattri-llm
> Hub copy — paper_says fields are loose paraphrases; no manuscript text is reproduced. Dual-LGTM results: `merged-dual-lgtm-round3.md`.

- **Tip:** `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb` (origin/dev) — **prev tip:** same; `git log a5c08fc..origin/dev` is empty (no new commits since R2)
- **Generated:** 2026-09-24T21:41-04:00 (America/Toronto)
- **Scope:** Paper-facing only: manuscript edits to match code at tip (claims, numbers, method names, API snippets, captions, repro/setup, dependency, hardware). Severity low/med. Deduped vs C1–C6 and R2-1…R2-28; priors included only with new evidence. No commits since R2 tip (git log a5c08fc..origin/dev empty).
- **Paper:** private (not on hub); locations cite section / page / paper margin line.

## Summary

| id | category | sev | title | paper loc | prior |
|---|---|---|---|---|---|
| R3-1 | number | low | B.2 EK-FAC query-chunk size on OLMo-3-7B: paper 8, launcher 4 | B.2 Fidelity Evaluation Details — 'Batch sizes' (p.19) | — |
| R3-2 | caption | med | Table 7 lists limiting resources for OLMo-3-7B runs the launcher never executes | B.2, Table 7 (and text l.993–994) (p.19) | — |
| R3-3 | claim | med | A.1/§6 'one implementation covers all layer types' overclaims for K-FAC/EK-FAC | A.1 (last sentences) and §6 Conclusion (p.13; 9) | — |
| R3-4 | claim | low | Abstract implies token-level attribution for trajectory-based methods; code supports it only for inner-product/curvature/LESS | Abstract (p.1) | — |
| R3-5 | api-usage | low | §3.3 says OLMo is instrumented by wrapping trainer.train(); both OLMo paths wrap trainer.fit() | §3.3 Compatibility (p.5) | R2-16 |
| R3-6 | dependency | low | Fig. 1 / §3.1 dependency tier: dattri is optional (projectors only) and Transformers is required by GradientStreamer | Figure 1 (bottom tier) and §3.1 Architecture Overview (p.2; 4) | R2-25 |
| R3-7 | hardware | low | Pythia/A40 experiments are labelled 'fp32' but every benchmark adapter enables TF32 matmuls | B.3 (setup), B.4, B.6, captions of Table 9/10 and Fig. 6 (p.19–22) | — |
| R3-8 | method-name | low | 'Pythia-0.5B' is the Pythia-410M checkpoint | B.3, B.4, B.5, B.6 (Tables 9–10, Fig. 6) (p.19–22) | — |
| R3-9 | repro-setup | low | B.1 says only Kronfluence runs unprojected; Bergson K-FAC/EK-FAC also keep full-dimensional factors | B.1 'Library configurations' (p.16–17) | — |
| R3-10 | caption | low | Benchmark time limits and dash semantics are not stated (timeouts reported as OOM; Table 6 query count) | §4.1 Setup; Fig. 2 caption; B.1 Table 6 caption (p.6–7; 18) | — |
| R3-11 | claim | low | A.3 optimizer-coverage claim — Rprop and ASGD are not supported | A.3 Optimizer-Preconditioned Capture (p.14) | — |
| R3-12 | api-usage | low | A.4 capture-time representation rule is opt-in and global, not automatic/per-layer | A.4 FLOP-Aware Routing — 'Decision' (p.15) | — |
| R3-13 | api-usage | low | A.5 lists type-based layer selection — code selects by name, regex, or REGISTER_ALL | A.5 Capture Mechanism — 'Hook families' (p.15) | — |
| R3-14 | claim | low | A.2 says materialized projection is the only reduction for normalization layers; code also allows the random mask | A.2 Dimension Reduction — 'Materialized projection' (p.14) | — |
| R3-15 | claim | low | A.2 seed-comparability claim omits the device caveat (CPU vs CUDA projectors differ; SJLT/GraSS CUDA-only) | A.2 — 'Random maps' (and §3.2 list of maps) (p.14; 4) | — |
| R3-16 | api-usage | low | Fig. 4 snippet's default HookManager hooks embeddings/LM head, unlike the B.7 protocol | §5 Figure 4 code; B.7 (p.8; 23) | — |
| R3-17 | claim | low | A.1 supported-layer list omits nn.Bilinear and does not note RMSNorm needs PyTorch ≥ 2.4 | A.1 Canonical Factorization (p.13) | — |

Count: 17 (2 med, 15 low).

## Details

### R3-1 — B.2 EK-FAC query-chunk size on OLMo-3-7B: paper 8, launcher 4
- **category:** `number` · **severity:** low · **prior:** none
- **paper location:** B.2 Fidelity Evaluation Details — 'Batch sizes', p.19, paper l.997–1000 (extract L1627–1631)
- **paper says (paraphrase):** Reports the OLMo-3-7B query chunk for dattri-LLM's full-dim EK-FAC as 8.
- **code evidence:**
  - `experiments/fidelity/fidelity.py:63` — EKFAC_CHUNK = {gpt2:64, 0.5b:64, 1.5b:8, 3b:8, olmo2-1b:16, olmo3-7b:4}
  - `experiments/fidelity/fidelity.py:154-155` — olmo_attribution passes --query-chunk/--eval-batch EKFAC_CHUNK[scale] (=4 for olmo3-7b)
  - `experiments/fidelity/fidelity.py:60-62` — comment: chunk is the largest power of two that fits the budget (so 4 is the recorded value)
- **proposed paper edit:** Change the OLMo-3-7B dattri-LLM EK-FAC chunk from 8 to 4 ("64, 8, and 4 queries"), or re-run at 8 if 8 was actually used and update the launcher reference.

### R3-2 — Table 7 lists limiting resources for OLMo-3-7B runs the launcher never executes
- **category:** `caption` · **severity:** med · **prior:** none
- **paper location:** B.2, Table 7 (and text l.993–994), p.19, paper l.973–980 (extract L1594–1604)
- **paper says (paraphrase):** Table 7 names a binding resource for four OLMo-3-7B baseline/full configurations.
- **code evidence:**
  - `experiments/fidelity/fidelity.py:130-160` — olmo_attribution builds only masked AdamW-influence, dattri EK-FAC (k64/full), TrackStar; Bergson EK-FAC only 'if scale in BERGSON_CHUNK'
  - `experiments/fidelity/fidelity.py:64` — BERGSON_CHUNK has no 'olmo3-7b' key, so Bergson EK-FAC is never launched at 7B; no MAGIC/SOURCE/full-AdamW cell exists for OLMo
  - `experiments/fidelity/README.md:70-73` — on OLMo-3-7B the methods infeasible at Qwen2.5-1.5B/3B (MAGIC, SOURCE, full AdamW-influence, Bergson EK-FAC) 'are not run'; SOURCE/MAGIC cannot run under OLMo-core's trainer
- **proposed paper edit:** Mark the four OLMo-3-7B rows as 'not run (infeasible at a smaller scale; MAGIC/SOURCE own the training loop)' instead of naming a limiting resource, or split Table 7 into 'exceeded budget' vs 'not attempted'; align the Table 8 '–' footnote accordingly.

### R3-3 — A.1/§6 'one implementation covers all layer types' overclaims for K-FAC/EK-FAC
- **category:** `claim` · **severity:** med · **prior:** none
- **paper location:** A.1 (last sentences) and §6 Conclusion, p.13; 9, paper l.688–690 (extract L1173–1176); l.463–465 (extract L845–847)
- **paper says (paraphrase):** Claims one implementation per attribution method spans all supported layer families (A.1; echoed in §6).
- **code evidence:**
  - `dattri_llm/gradient/ops/types.py:80-90` — is_kfac_eligible: only linear and (transposed) conv; norm, embedding, param_grad not eligible
  - `dattri_llm/attribution/algorithm/kronecker.py:630-651` — kfac_layers skips non-eligible layer types
  - `dattri_llm/attribution/algorithm/kronecker.py:199,1035-1036` — non_kfac_strategy defaults to 'ignore' (norm layers skipped unless 'direct')
  - `dattri_llm/attribution/algorithm/kronecker.py:708-714` — embeddings are left ignored even under 'direct' (warning)
- **proposed paper edit:** Qualify: Kronecker-factored methods (K-FAC/EK-FAC) apply to linear and convolutional layers; normalization layers are optionally preconditioned by a dense empirical Fisher and embeddings are excluded. Keep the 'single implementation' claim for inner-product / projection-based methods.

### R3-4 — Abstract implies token-level attribution for trajectory-based methods; code supports it only for inner-product/curvature/LESS
- **category:** `claim` · **severity:** low · **prior:** none
- **paper location:** Abstract, p.1, paper l.019–022 (extract L30–33)
- **paper says (paraphrase):** Abstract implies per-token scoring is available for all three method families, trajectory ones included.
- **code evidence:**
  - `dattri_llm/attribution/base.py:790-801` — attribution_granularity ('instance'|'token') on BaseInnerProductAttributor.attribute
  - `dattri_llm/attribution/algorithm/kronecker.py:1003 and less.py:219` — K-FAC/EK-FAC and LESS expose attribution_granularity
  - `dattri_llm/attribution/algorithm/dvemb.py:592-607` — DVEmbAttributor.attribute has no granularity parameter
  - `dattri_llm/attribution/algorithm/adamw_influence.py:435-470` — AdamWInfluenceAttributor attribute/attribute_from_cache have no granularity option (no 'granularity'/'token' anywhere in trajectory.py, dvemb.py, adamw_influence.py)
- **proposed paper edit:** Scope the token-level clause to gradient-similarity and curvature-based methods (e.g., '…and trajectory-based methods, with gradient-similarity and curvature scores available per example or per token'), consistent with the hedge already in B.5.

### R3-5 — §3.3 says OLMo is instrumented by wrapping trainer.train(); both OLMo paths wrap trainer.fit()
- **category:** `api-usage` · **severity:** low · **prior:** R2-16 (new evidence: R2-16 proposed an ai2-olmo train() vs OLMo-core fit() split and was refuted; re-verified that BOTH OLMo stacks call fit() — olmo_trainer.py:130, olmocore.py:445 — so the paper's generic train() wording is the actual defect, as the R2 Astra seat noted)
- **paper location:** §3.3 Compatibility, p.5, paper l.247–248 (extract L379–382)
- **paper says (paraphrase):** §3.3 describes OLMo instrumentation as wrapping train() (while §4.2/B.2 use fit()).
- **code evidence:**
  - `examples/trainers/olmo_trainer.py:3 and :126-130` — OLMo example wraps trainer.fit() in collector.collect()
  - `experiments/fidelity/utils/olmocore.py:41 and :444-445` — OLMo-core path: trainer.fit() inside hooks.collect()
  - `examples/trainers/transformers_trainer.py:3,121-129` — train() is correct for HF (and TRL)
  - `paper §4.2 l.318 / B.2 l.964 already say fit() for OLMo` — internal inconsistency with §3.3
- **proposed paper edit:** Write 'wrapping the trainer's entry point (trainer.train() for Hugging Face/TRL, trainer.fit() for OLMo) with with hm.collect()'.

### R3-6 — Fig. 1 / §3.1 dependency tier: dattri is optional (projectors only) and Transformers is required by GradientStreamer
- **category:** `dependency` · **severity:** low · **prior:** R2-25 (new evidence: R2-25 targeted the README; the manuscript's own Fig. 1/§3.1 dependency tier makes the same omission — pyproject.toml:13-23, projection.py:139, streaming.py:670)
- **paper location:** Figure 1 (bottom tier) and §3.1 Architecture Overview, p.2; 4, paper l.064 fig tier (extract L101–103); l.164–166 (extract L253–256)
- **paper says (paraphrase):** Figure 1 / §3.1 show PyTorch and dattri as the only bottom-tier dependencies.
- **code evidence:**
  - `pyproject.toml:13-15` — the only hard dependency is torch>=2.1
  - `pyproject.toml:21-23` — dattri is in the optional 'attribution' extra
  - `dattri_llm/gradient/ops/projection.py:139` — the only dattri import in the package (random_project, lazily)
  - `dattri_llm/__init__.py:10-16` — docstring: 'transformers for the live streamer, dattri for its random projectors' are optional/lazy
  - `dattri_llm/gradient/streaming.py:670,934` — GradientStreamer imports transformers (set_seed/enable_full_determinism, get_scheduler); used by attribute() via attribution/base.py:38,295
- **proposed paper edit:** In Fig. 1 and §3.1, show Hugging Face Transformers as a dependency of the live GradientStreamer (end-to-end Attributor path) and mark dattri as an optional backend used only for random projectors (capture core needs PyTorch only).

### R3-7 — Pythia/A40 experiments are labelled 'fp32' but every benchmark adapter enables TF32 matmuls
- **category:** `hardware` · **severity:** low · **prior:** none
- **paper location:** B.3 (setup), B.4, B.6, captions of Table 9/10 and Fig. 6, p.19–22, paper l.1019 (extract L1655); l.1097 (L1797); l.1102; l.1169 (L1886); l.1186 (L1907)
- **paper says (paraphrase):** Pythia/A40 ablations described simply as fp32.
- **code evidence:**
  - `experiments/benchmark/README.md:28` — 'All adapters enable TF32 matmuls.'
  - `experiments/benchmark/utils/adapters/run_ours.py:53, run_bergson.py:65, run_kronfluence.py:43, run_logix.py:66` — torch.backends.cuda.matmul.allow_tf32 = True
  - `experiments/capture/utils/adapters/run_ours.py:49 and experiments/capture/README.md:19-20` — 'fp32 with TF32 matmuls'
  - `experiments/fidelity/utils/settings.py:140-141` — only the fidelity study turns TF32 off
- **proposed paper edit:** Say 'fp32 weights with TF32 matmuls enabled (all libraries)' in B.3/B.4/B.6 and the Table 9/10 and Fig. 6 captions, to distinguish from the TF32-off fidelity protocol.

### R3-8 — 'Pythia-0.5B' is the Pythia-410M checkpoint
- **category:** `method-name` · **severity:** low · **prior:** none
- **paper location:** B.3, B.4, B.5, B.6 (Tables 9–10, Fig. 6), p.19–22, paper l.1017 (extract L1653); l.1041; l.1096; l.1118; l.1169
- **paper says (paraphrase):** Supplementary experiments name the model Pythia-0.5B.
- **code evidence:**
  - `experiments/benchmark/utils/models.py:24` — pythia '0.5b' -> ('EleutherAI/pythia-410m', 0.41)
  - `experiments/benchmark/README.md:15-17` — '0.5b is Pythia-410M in the Pythia family'
  - `experiments/benchmark/benchmark.py:3,33` — Pythia-0.5B label with family='pythia' (resolves to pythia-410m)
- **proposed paper edit:** Name the checkpoint as released: 'Pythia-410M (EleutherAI/pythia-410m)', optionally noting it is the 0.5B-class scale.

### R3-9 — B.1 says only Kronfluence runs unprojected; Bergson K-FAC/EK-FAC also keep full-dimensional factors
- **category:** `repro-setup` · **severity:** low · **prior:** none
- **paper location:** B.1 'Library configurations', p.16–17, paper l.863 & l.901 (extract L1420–1421, L1480)
- **paper says (paraphrase):** B.1 names only Kronfluence as running without k=64 projection.
- **code evidence:**
  - `experiments/benchmark/throughput.py:44-57` — PROJECTION: bergson kfac 'full-dim factors, rank-64 query', bergson ekfac 'full-dim', logix 'LoRA-64', kronfluence 'full-dim'
  - `experiments/benchmark/scaling.py:47-49` — Bergson K-FAC/EK-FAC keep full-dimension factors (7B–32B run sharded)
  - `experiments/benchmark/benchmark.py:26-30` — Bergson EK-FAC accepts no projection (NOT_EXPRESSIBLE)
- **proposed paper edit:** Extend the sentence: 'Kronfluence runs without projection, and Bergson's K-FAC (full-dimensional factors, rank-64 query) and EK-FAC (full-dimensional) cannot apply it; LogIX uses its LoGra rank-64 projection.'

### R3-10 — Benchmark time limits and dash semantics are not stated (timeouts reported as OOM; Table 6 query count)
- **category:** `caption` · **severity:** low · **prior:** none
- **paper location:** §4.1 Setup; Fig. 2 caption; B.1 Table 6 caption, p.6–7; 18, paper l.283–285 (extract L438–440); l.347–352; l.919–922 (extract L1512–1515)
- **paper says (paraphrase):** Limits left unspecified; Table 6 dashes described as OOM and workload as one query.
- **code evidence:**
  - `experiments/benchmark/throughput.py:131` — TIME_LIMIT_S = 3600 (cells cut off and recorded as timeout)
  - `experiments/benchmark/scaling.py:52-55,61-63` — Kronfluence scaling cells cut at time_limit_s=1800 and recorded as 'timeout'
  - `experiments/benchmark/scaling.py:65-69` — Bergson 72B/110B GradDot scaling cells are given n_test=4 (one query per device), not one
  - `experiments/benchmark/README.md: scaling paragraph` — 'a Kronfluence cell is cut off after 1800 s and recorded as timeout'
- **proposed paper edit:** State the limits (3600 s per throughput cell; 1800 s for Kronfluence scaling cells), make the Table 6 / Fig. 2 dash mean 'out of memory or time limit', and note the four-query (one per device) sharded GradDot cells at 72B/110B.

### R3-11 — A.3 optimizer-coverage claim — Rprop and ASGD are not supported
- **category:** `claim` · **severity:** low · **prior:** none
- **paper location:** A.3 Optimizer-Preconditioned Capture, p.14, paper l.740 (extract L1245)
- **paper says (paraphrase):** A.3 claims all coordinate-wise torch.optim optimizers are handled by one kernel.
- **code evidence:**
  - `dattri_llm/gradient/ops/optimizer.py:31-42` — OPTIMIZER_STATE_KEYS: SGD, Adam, AdamW, Adamax, NAdam, RAdam, RMSprop, Adagrad, Adadelta, Lion only
  - `dattri_llm/gradient/optimizer_state.py:75-88` — any other optimizer raises NotImplementedError (e.g. torch.optim.Rprop, torch.optim.ASGD, both coordinate-wise)
- **proposed paper edit:** Replace with the explicit list ('SGD, Adam/AdamW, Adamax, NAdam, RAdam, RMSprop, Adagrad, Adadelta, and Lion') or 'most coordinate-wise torch.optim optimizers'.

### R3-12 — A.4 capture-time representation rule is opt-in and global, not automatic/per-layer
- **category:** `api-usage` · **severity:** low · **prior:** none
- **paper location:** A.4 FLOP-Aware Routing — 'Decision', p.15, paper l.775–777 (extract L1299–1301)
- **paper says (paraphrase):** A.4 describes the persisted capture representation as rule-driven and configurable per layer.
- **code evidence:**
  - `dattri_llm/gradient/hooks/config.py:309` — capture_style: str = 'factorized' (default keeps factors regardless of cost)
  - `dattri_llm/gradient/hooks/config.py:180-191` — only capture_style='auto' applies the should_materialize cost rule
  - `dattri_llm/gradient/hooks/config.py:400-408` — capture_style validated as one string for the whole manager (no per-layer mapping)
- **proposed paper edit:** Say the storage rule is applied when capture_style='auto' (a single manager-level setting whose decision is made per layer and micro-batch); the default keeps factors.

### R3-13 — A.5 lists type-based layer selection — code selects by name, regex, or REGISTER_ALL
- **category:** `api-usage` · **severity:** low · **prior:** none
- **paper location:** A.5 Capture Mechanism — 'Hook families', p.15, paper l.787–789 (extract L1314–1317)
- **paper says (paraphrase):** A.5 lists layer-type-based selection among the three selection mechanisms.
- **code evidence:**
  - `dattri_llm/gradient/hooks/config.py:66-71` — Selector = REGISTER_ALL | list[str] (regex) | None
  - `dattri_llm/gradient/hooks/config.py:86-117` — hook_types explicit name->family assignment plus regex/REGISTER_ALL add-ons
  - `dattri_llm/gradient/hooks/config.py:132-143` — layer_types only relabels a layer's type and is 'orthogonal to which layers get hooked'
- **proposed paper edit:** Write 'by explicit per-layer assignment, by regular expression over module names, or by an all-eligible marker (REGISTER_ALL); eligibility itself is decided by layer type'.

### R3-14 — A.2 says materialized projection is the only reduction for normalization layers; code also allows the random mask
- **category:** `claim` · **severity:** low · **prior:** none
- **paper location:** A.2 Dimension Reduction — 'Materialized projection', p.14, paper l.710–711 (extract L1208–1210)
- **paper says (paraphrase):** A.2 presents materialized projection as the sole reduction for norm layers.
- **code evidence:**
  - `dattri_llm/gradient/hooks/config.py:240-243` — 'Norm layers must use "dense" or "mask"'
  - `dattri_llm/gradient/ops/projection.py:12-14,50` — 'mask' is a separate projection style gathered from factors
- **proposed paper edit:** Change to 'Materialized projection or the random mask (below) are the options for normalization layers'.

### R3-15 — A.2 seed-comparability claim omits the device caveat (CPU vs CUDA projectors differ; SJLT/GraSS CUDA-only)
- **category:** `claim` · **severity:** low · **prior:** none
- **paper location:** A.2 — 'Random maps' (and §3.2 list of maps), p.14; 4, paper l.714–721 (extract L1212–1223); l.209–211
- **paper says (paraphrase):** A.2 asserts seed-based comparability of projections with no device qualifier.
- **code evidence:**
  - `dattri_llm/gradient/hooks/config.py:250-256` — dattri's CPU and CUDA projectors do not produce the same projection for the same proj_seed; 'sjlt'/'grass' are CUDA-only; use one device consistently
  - `dattri_llm/gradient/ops/projection.py:139` — projections come from dattri.func.projection.random_project
- **proposed paper edit:** Add 'on the same device type' to the comparability sentence and note that SJLT and GraSS maps are CUDA-only.

### R3-16 — Fig. 4 snippet's default HookManager hooks embeddings/LM head, unlike the B.7 protocol
- **category:** `api-usage` · **severity:** low · **prior:** none
- **paper location:** §5 Figure 4 code; B.7, p.8; 23, paper l.407–416 (extract L763–779); l.1226–1228 (extract L1968–1971)
- **paper says (paraphrase):** Fig. 4 snippet constructs the manager with no layer config; B.7 restricts to attention/MLP linears.
- **code evidence:**
  - `dattri_llm/gradient/hooks/config.py:128-130` — default config: linear_io on every linear-IO-capable layer, param_grad fallback for the rest
  - `dattri_llm/gradient/hooks/hooks.py:78-97` — linear-IO-capable types include nn.Embedding and nn.Linear (so embed_tokens and lm_head are hooked by default)
  - `dattri_llm/gradient/callbacks/data_selection_callback.py:339-343,364` — Layer-Wise requires selection_kwargs['layer_wise']=True (default False)
- **proposed paper edit:** Add a HookManagerConfig(linear_io=[attention/MLP regex]) argument to the Figure 4 snippet (or a caption note that the B.7 layer selection is omitted), and mention layer_wise=True for the Layer-Wise variant.

### R3-17 — A.1 supported-layer list omits nn.Bilinear and does not note RMSNorm needs PyTorch ≥ 2.4
- **category:** `claim` · **severity:** low · **prior:** none
- **paper location:** A.1 Canonical Factorization, p.13, paper l.685–688 (extract L1167–1172)
- **paper says (paraphrase):** A.1 enumerates supported module types (no Bilinear; RMSNorm unconditional).
- **code evidence:**
  - `dattri_llm/gradient/ops/types.py:14-21` — LINEAR_TYPES also include nn.Bilinear and nn.NonDynamicallyQuantizableLinear
  - `dattri_llm/gradient/hooks/hooks.py:87,95-97` — nn.Bilinear hooked; nn.RMSNorm only added 'if hasattr(nn, "RMSNorm")' (PyTorch 2.4+)
  - `pyproject.toml:14` — torch>=2.1 is the declared floor
- **proposed paper edit:** Add nn.Bilinear (and MultiheadAttention's out-projection) to the list and footnote that native nn.RMSNorm support requires PyTorch ≥ 2.4 (older/custom RMSNorm via per-layer overrides).

## Checked and aligned (not listed)

- Table 4 per-device batch sizes match `throughput.py` BATCHES for every library/method/scale.
- Fig. 4 API names (`DataSelectionCallback(model, target='val_loader', val_loader=, val_loss_fn=, selection_kwargs={threshold, threshold_mode:'bottom_fraction'})`, `HookManager(model, callbacks=[...])`) match `data_selection_callback.py:320-332` and exports.
- Fig. 1 `attr.attribute(train_ds, test_ds)` matches `BaseAttributor.attribute` (base.py:136).
- B.2 fidelity protocol (512×128 WikiText-2 blocks, batch 32, 16 AdamW steps, β/ε/wd, 1e-5 peak with 10% warmup, TF32 off, 50 TSLOO, EK-FAC damping 0.1×mean eigenvalue, TrackStar dim 64, SOURCE 4×4 with 2 ckpts, Bergson chunks 16/1/N/A) matches `settings.py`, `fidelity.py`, `ekfac.py:53`, `bergson.py:63`, README.
- B.1 workload (512 samples, 1 query ≤32B / 4 at 72B-110B, 2 warm-up steps, FSDP vs LogIX DDP, attention+MLP linears) matches `throughput.py`.
- B.4 routing (T 32–2048, batch 8, 128 steps after 8 warm-up) and B.6 invasive-capture assignment match benchmark README.
- Table 3 checkpoints/params match `utils/models.py`.

## Not re-raised (no new evidence)

R2-6/R2-7 (abstract speedups), R2-9 (GradDot label), R2-10 (Attributor), R2-11/R2-26 (LESS/AdamW coverage), R2-17 (OLMo extra), R2-20 (fidelity ladder), R2-21 (L40S vs A40 — capture.py:24,106 already cited by R2 Astra), R2-27 (invasive_linear_io), R2-28 (runbook). Paper-internal typos (H5) not repeated.
