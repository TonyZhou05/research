# Blind dual-LGTM ROUND 3 (paper-consistency) — dattri-llm
- **tip:** `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb` (seats checked: opus `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb`, astra `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb`)
- **generated:** 2026-09-24T21:53-04:00 (America/Toronto)
- **seats:** Claude Opus via Claude Code CLI (print mode, dontAsk, Read/Grep/Glob) | Codex `gpt-6-astra` (read-only sandbox); launched independently from the same prompt file
- **gate per seat:** effective AND paper_only AND low_med; AGREED = both overall Y
- cells: effective/paper_only/low_med→overall

| id | sev | title | opus | astra | agreed |
|---|---|---|---|---|---|
| R3-1 | low | B.2 EK-FAC query-chunk size on OLMo-3-7B: paper 8, launcher 4 | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-2 | med | Table 7 lists limiting resources for OLMo-3-7B runs the launcher never executes | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-3 | med | 'Every attribution method covers every supported layer type' overclaims for K-FAC/EK-FAC | Y/Y/Y→**Y** | N/Y/Y→**N** | N |
| R3-4 | low | Abstract implies token-level attribution for trajectory-based methods; code supports it only for inner-product/curvature/LESS | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-5 | low | §3.3 says OLMo is instrumented by wrapping trainer.train(); both OLMo paths wrap trainer.fit() | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-6 | low | Fig. 1 / §3.1 dependency tier: dattri is optional (projectors only) and Transformers is required by GradientStreamer | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-7 | low | Pythia/A40 experiments are labelled 'fp32' but every benchmark adapter enables TF32 matmuls | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-8 | low | 'Pythia-0.5B' is the Pythia-410M checkpoint | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-9 | low | B.1 says only Kronfluence runs unprojected; Bergson K-FAC/EK-FAC also keep full-dimensional factors | Y/Y/Y→**Y** | N/Y/Y→**N** | N |
| R3-10 | low | Benchmark time limits and dash semantics are not stated (timeouts reported as OOM; Table 6 query count) | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-11 | low | A.3 'every coordinate-wise optimizer in torch.optim' — Rprop and ASGD are not supported | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-12 | low | A.4 capture-time representation rule is opt-in and global, not automatic/per-layer | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-13 | low | A.5 layer selection 'by type' — code selects by explicit name, regex, or REGISTER_ALL | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-14 | low | A.2 says materialized projection is the only reduction for normalization layers; code also allows the random mask | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-15 | low | A.2 seed-comparability claim omits the device caveat (CPU vs CUDA projectors differ; SJLT/GraSS CUDA-only) | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R3-16 | low | Fig. 4 snippet's default HookManager hooks embeddings/LM head, unlike the B.7 protocol | Y/Y/Y→**Y** | N/Y/Y→**N** | N |
| R3-17 | low | A.1 supported-layer list omits nn.Bilinear and does not note RMSNorm needs PyTorch ≥ 2.4 | Y/Y/Y→**Y** | N/Y/Y→**N** | N |

## AGREED (13)

- **R3-1** — B.2 EK-FAC query-chunk size on OLMo-3-7B: paper 8, launcher 4
- **R3-2** — Table 7 lists limiting resources for OLMo-3-7B runs the launcher never executes
- **R3-4** — Abstract implies token-level attribution for trajectory-based methods; code supports it only for inner-product/curvature/LESS
- **R3-5** — §3.3 says OLMo is instrumented by wrapping trainer.train(); both OLMo paths wrap trainer.fit()
- **R3-6** — Fig. 1 / §3.1 dependency tier: dattri is optional (projectors only) and Transformers is required by GradientStreamer
- **R3-7** — Pythia/A40 experiments are labelled 'fp32' but every benchmark adapter enables TF32 matmuls
- **R3-8** — 'Pythia-0.5B' is the Pythia-410M checkpoint
- **R3-10** — Benchmark time limits and dash semantics are not stated (timeouts reported as OOM; Table 6 query count)
- **R3-11** — A.3 'every coordinate-wise optimizer in torch.optim' — Rprop and ASGD are not supported
- **R3-12** — A.4 capture-time representation rule is opt-in and global, not automatic/per-layer
- **R3-13** — A.5 layer selection 'by type' — code selects by explicit name, regex, or REGISTER_ALL
- **R3-14** — A.2 says materialized projection is the only reduction for normalization layers; code also allows the random mask
- **R3-15** — A.2 seed-comparability claim omits the device caveat (CPU vs CUDA projectors differ; SJLT/GraSS CUDA-only)

## Split (4)

- **R3-3** — opus=Y astra=N
- **R3-9** — opus=Y astra=N
- **R3-16** — opus=Y astra=N
- **R3-17** — opus=Y astra=N

## Both NO (0)


## Reasons (one line per seat)

- **R3-1** opus: fidelity.py:63 EKFAC_CHUNK['olmo3-7b']='4' vs paper l.999-1000 '64, 8, and 8'; number-only manuscript fix
  astra: Paper l.999 specifies 8; experiments/fidelity/fidelity.py:63,154 specifies 4 for OLMo-3-7B; the manuscript-only replacement fixes it.
- **R3-2** opus: olmo_attribution (fidelity.py:134-160) + README:70-73 'are not run' vs Table 7 l.976-980 naming a limiting resource; also conflicts with paper l.993-994
  astra: Paper Table 7 assigns limiting resources to unattempted OLMo cells; experiments/fidelity/README.md:70-73 explicitly says they are not run, matching fidelity.py:134-161.
- **R3-3** opus: types.py:80-90 restricts K-FAC to linear/conv, kronecker.py:199/708-714 ignore norms+embeddings, so A.1 l.688-690 and §6 l.463-465 overclaim
  astra: Paper l.688-690 overclaims, but the proposed blanket embedding exclusion is also wrong: projection.py:456 supports embedding factors, hooks/manager.py:1062 relabels them as linear, and algorithm/kronecker.py:669 admits materialized layers to dense Fisher.
- **R3-4** opus: attribution_granularity absent from dvemb.py/adamw_influence.py/trajectory.py; abstract l.021-022 extends token level to trajectory methods
  astra: Abstract l.020-022 extends token granularity across method families; attribution/base.py:800 exposes it, whereas algorithm/dvemb.py:592-607 and adamw_influence.py:452-468 do not.
- **R3-5** opus: olmo_trainer.py:130 and olmocore.py:445 both wrap fit(); §3.3 l.248 says train() for OLMo, contradicting paper l.318/l.964
  astra: Paper l.247-248 prescribes train() for OLMo; examples/trainers/olmo_trainer.py:130 and experiments/fidelity/utils/olmocore.py:445 both call fit().
- **R3-6** opus: pyproject.toml:13-24 (torch-only hard dep, dattri optional) + streaming.py:670 transformers import in GradientStreamer.__enter__ vs Fig.1 tier / §3.1 l.164-166
  astra: Paper l.164-166 and Figure 1 omit the live-streamer dependency distinction: pyproject.toml:13-27 makes extras optional, projection.py:139 lazily imports dattri, and streaming.py:670 requires Transformers.
- **R3-7** opus: all benchmark/capture adapters set allow_tf32=True (run_ours.py:53 etc.); only fidelity settings.py:140-141 disables it, so B.3/B.4/B.6 'fp32' is imprecise
  astra: Paper l.1019,1097,1169,1186 omits enabled TF32; benchmark adapters run_ours.py:53, run_bergson.py:65, run_kronfluence.py:43, run_logix.py:66 and capture adapter run_ours.py:49 enable it.
- **R3-8** opus: models.py:24 maps pythia '0.5b' to EleutherAI/pythia-410m (0.41B); paper l.1017 etc. call it Pythia-0.5B
  astra: Paper l.1017 and subsequent supplementary labels say Pythia-0.5B; experiments/benchmark/utils/models.py:24 resolves that scale to EleutherAI/pythia-410m.
- **R3-9** opus: throughput.py:49-50 and benchmark.py:29-30 show Bergson K-FAC/EK-FAC unprojected, so B.1 l.901 singling out Kronfluence is incomplete
  astra: Paper l.863/901 already says projection is requested where supported, not that only Kronfluence is unprojected; l.293-294 explicitly identifies Bergson's full-dimensional curvature factors, consistent with throughput.py:49-50.
- **R3-10** opus: throughput.py:131 (3600s), scaling.py:55/63 (1800s timeout status) and scaling.py:68-69 (n_test=4) are unstated in §4.1 l.284, Fig.2 and Table 6 caption l.919-922
  astra: Paper l.349-352/919-922 gives OOM-only failure semantics; throughput.py:131 sets 3600 s, scaling.py:55,63 sets 1800 s, and scaling.py:65-69 gives Bergson's large sharded GradDot cells four queries.
- **R3-11** opus: optimizer.py:32-43 lists 10 families, optimizer_state.py:84-88 raises for Rprop/ASGD; A.3 l.740 claims every coordinate-wise torch.optim optimizer
  astra: Paper l.740 claims every coordinate-wise optimizer; gradient/ops/optimizer.py:32-43 lists supported families without Rprop/ASGD, and gradient/optimizer_state.py:81-88 rejects unmatched families.
- **R3-12** opus: config.py:309 default 'factorized', :180-191 rule only under 'auto', :399-408 one manager-level string; A.4 l.775-777 says persisted form follows the rule and is a per-layer setting
  astra: Paper l.775-777 presents storage routing as automatic and configurable per layer; hooks/config.py:188-195,309,400-408 establishes opt-in auto routing under one manager-level string, defaulting to factorized.
- **R3-13** opus: config.py:86-117 selects by explicit name map/regex/REGISTER_ALL and :132-143 layer_types only relabels; no type selector as A.5 l.787 states
  astra: Paper l.787-789 lists selection by type; hooks/config.py:67-71,88-109 implements name assignments, regex and REGISTER_ALL, while type overrides explicitly do not select layers at :140-143.
- **R3-14** opus: config.py:242-243 'Norm layers must use "dense" or "mask"' and projection.py:12-14 treat mask as a distinct style; A.2 l.710 says materialized projection is the only option
  astra: Paper l.710 says materialized projection is the only normalization-layer option; hooks/config.py:240-243 permits masks, implemented directly in gradient/ops/projection.py:738-743.
- **R3-15** opus: config.py:250-256 warns CPU/CUDA projectors differ for the same proj_seed and sjlt/grass are CUDA-only; A.2 l.720-721 and §3.2 l.209-211 omit both caveats
  astra: Paper l.714-721 omits device restrictions; hooks/config.py:252-256 explicitly documents differing CPU/CUDA maps at equal seeds and CUDA-only SJLT/GraSS.
- **R3-16** opus: default config + hooks.py:78-96 hook nn.Embedding and lm_head, unlike B.7 l.1226-1227; data_selection_callback.py:364 layer_wise defaults False, absent from Fig.4
  astra: Paper l.420 explicitly calls Figure 4 the core integration, with experimental selectors specified in B.7 l.1226-1228; the valid defaults in hooks/config.py:128-130 and data_selection_callback.py:364 do not contradict that illustrative scope.
- **R3-17** opus: types.py:17 / hooks.py:87 support nn.Bilinear and hooks.py:94-96 gates nn.RMSNorm on torch>=2.4 while pyproject.toml:14 allows 2.1; A.1 l.687-688 lists neither
  astra: The RMSNorm caveat is supported, but the proposed support-list expansion is unsafe: hooks/config.py:654-658 says MultiheadAttention.out_proj bypasses module hooks, and hooks/hooks.py:321 captures only Bilinear's first input.
