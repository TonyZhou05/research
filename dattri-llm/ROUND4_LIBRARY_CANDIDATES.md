# Round 4 — dattri-llm library readability / industry-formatting candidates

- Tip: `7f967a591c4539d6c5181b7af2921919d719bbc5` (dev) — prev tip `a5c08fc2d386c9f24f7b8df9600b789132bdd7eb`
- Generated: 2026-09-26T16:22:20-04:00
- Scope: `dattri_llm/`, repo root, examples, docs. Library readability only (not paper consistency). Deduped against R1 C1–C6, R2-1…28, R3-1…17, board H1–H9; overlaps are tagged.

## Commits since a5c08fc

- 7f967a5 Update tests (.github/workflows/examples_test.yml)
- 015bbd3 Update GRPO example (examples/README.md, examples/trainers/README.md, new examples/trainers/trl_grpo_trainer.py)

## Tooling run

- **ruff==0.15.20 (CI pin, .github/workflows/lint.yml) `ruff check .`** → All checks passed (0 errors)
- **ruff==0.15.20 `ruff format --check .`** → 108 files already formatted (0 would reformat)
- **ruff==0.15.20 `ruff check --isolated dattri_llm` (ruff defaults)** → All checks passed (0 errors)
- **ruff==0.16.9 (latest) `ruff check --statistics .` with repo config** → 115 errors: 61 RUF105 noqa-comments, 54 RUF201 rule-codes-in-selectors (preview meta-rules; no code findings)
- **ruff==0.16.9 `ruff format --check .`** → 2 would reformat (README.md, examples/projection/README.md markdown code blocks), 114 already formatted
- **python3 ast_audit.py (public, non-underscore symbols in dattri_llm/)** → 470 public symbols (44 classes, 426 functions/methods); docstring 469/470; params fully annotated 412/426 (the 14 gaps are all **proj_kwargs in ops/projection.py, ANN003 ignored); return annotated 426/426; Google 'Args:' present 97/297 functions with params; 'Returns:'/'Yields:' present where a value is returned 148/426 compliant (278 missing); 'Raises:' present 20/78 functions that raise; NumPy-style 0
- **python3 size stats** → 793 functions; 19 >100 lines, 1 >200 lines (register_linear_io_hooks, 242); 10 modules >900 lines (max gradient/hooks/manager.py 1711)
- **grep print/logging/warnings in dattri_llm** → 0 print(); 2 modules use logging (4 logger calls); 23 warnings.warn; raises: 171 ValueError, 26 RuntimeError, 21 KeyError, 18 TypeError, 16 NotImplementedError
- **find py.typed / CHANGELOG / CONTRIBUTING / .pre-commit-config.yaml / docs/** → none present

_Repo untouched: tracked-file archive of tip extracted on the box; ruff installed in a throwaway venv. The dirty Mac working tree (untracked experiments/, output/) was not used for counts._

## Summary

| id | theme | severity | effort | overlaps | title |
|---|---|---|---|---|---|
| R4-1 | lint-format | med | S | — | Lint toolchain is pinned only in CI: dev extra, Makefile and contributor docs use an unpinned ruff, and there is no pre-commit |
| R4-2 | docstrings-types | med | L | — | Google docstring sections are mostly missing on the public API, and the checks that would catch it are switched off |
| R4-3 | docstrings-types | low | M | — | Enumerated string options are typed as bare `str` instead of `Literal` aliases |
| R4-4 | docstrings-types | med | S | — | No PEP 561 py.typed marker, so the package's near-complete type hints are ignored downstream |
| R4-5 | api-surface | med | S | — | Lazy top-level exports are invisible to type checkers and IDEs (module __getattr__ -> object, no TYPE_CHECKING imports) |
| R4-6 | repo-hygiene | med | S | H3 | tqdm is a hard runtime import but is not a declared dependency |
| R4-7 | repo-hygiene | low | S | — | pyproject [project] metadata is minimal: no readme, authors, urls, classifiers or keywords |
| R4-8 | repo-hygiene | low | S | — | Version string is duplicated between pyproject.toml and __init__.py |
| R4-9 | repo-hygiene | low | S | — | No CONTRIBUTING.md or CHANGELOG.md; the developer workflow is documented only in the agent file CLAUDE.md |
| R4-10 | naming | med | M | — | Layer-filter argument is named `layer_name` (singular) in the attribution API but `layer_names` in the capture layer |
| R4-11 | structure-duplication | low | S | — | DVEmb reimplements contextlib.nullcontext as a lowercase class with a naming-rule suppression |
| R4-12 | structure-duplication | low | S | — | Repeated magic-number defaults and unnamed epsilons in the attribution kernels |
| R4-13 | structure-duplication | low | L | — | Very large modules and functions, with the complexity rules disabled globally rather than per-file |
| R4-14 | api-surface | low | S | R1-C3, R2-4, H1 | User-facing module_kwargs builders (rms_norm_module_kwargs etc.) are not exported or listed by dattri_llm.utils |
| R4-15 | api-surface | low | S | — | Internal helpers carry public names in modules that define no __all__ |
| R4-16 | api-surface | low | S | H3 | Examples bypass the advertised top-level API: deep implementation imports and a sys.path hack in every script |

## Candidates

### R4-1 — Lint toolchain is pinned only in CI: dev extra, Makefile and contributor docs use an unpinned ruff, and there is no pre-commit

- **theme:** lint-format · **severity:** med · **effort:** S · **overlaps:** none
- **evidence:**
  - .github/workflows/lint.yml:32-39 — installs ruff==0.15.20 with the comment that select=ALL + preview=true is version-sensitive, then runs `ruff check .` / `ruff format --check .`
  - pyproject.toml:35-42 — dev extra lists pytest/dattri/transformers/accelerate/trl/ai2-olmo only; no ruff (or darglint)
  - pyproject.toml:47-56 — [tool.ruff] has no required-version; lint uses preview=true, select=["ALL"]
  - Makefile:10-12 — `$(PYTHON) -m ruff check dattri_llm tests examples` (unpinned, and a narrower path set than CI's `.`)
  - CLAUDE.md:85-86 — says lint.yml runs `make check`, but lint.yml:36-39 runs ruff directly on `.`
  - tooling: latest ruff 0.16.9 on the same tree reports 115 errors (RUF105×61, RUF201×54) and 2 files to reformat, while CI's 0.15.20 is clean
  - repo root — no .pre-commit-config.yaml
- **fix:** Add ruff==0.15.20 to the dev extra (or a `lint` extra/dependency group), set `[tool.ruff] required-version = "==0.15.20"`, add a .pre-commit-config.yaml using astral-sh/ruff-pre-commit at rev v0.15.20 (ruff + ruff-format hooks), and make `make check` run the same `ruff check .` / `ruff format --check .` as CI (fix the CLAUDE.md sentence).

### R4-2 — Google docstring sections are mostly missing on the public API, and the checks that would catch it are switched off

- **theme:** docstrings-types · **severity:** med · **effort:** L · **overlaps:** none
- **evidence:**
  - tooling: Args: present on 97/297 public functions/methods with params; Returns:/Yields: missing on 278 value-returning public callables; Raises: present on 20/78 that raise
  - dattri_llm/attribution/algorithm/kronecker.py:227-246 — abstract public KroneckerAttributor.fit_factors(train_source, fisher_acc) -> dict: prose only, no Args/Returns
  - dattri_llm/gradient/ops/dot.py:423-430 — exported ops.dot(f1, f2, layer_type, include_bias) has a one-line docstring and no Args/Returns
  - dattri_llm/attribution/algorithm/adamw_influence.py:435-444 — public attribute(train_dataset, test_dataset, *, hook_config, verbose, **kwargs) documented by one line
  - pyproject.toml:77-78 — DOC201 (missing Returns) and DOC501 (missing Raises) ignored; pyproject.toml:130-131 google convention only enables D417 for Args sections that already exist
  - .darglint:3 — strictness = long (accepts docstrings without sections); darglint is run only by PR comment (.github/workflows/darglint-on-comment.yml:17,58, unpinned `pip install darglint`) or `make darglint` (Makefile:14-17, marked optional)
- **fix:** Add Args/Returns/Raises sections to every symbol reachable from dattri_llm.__all__ and the subpackage __all__ lists first (attributors' attribute/attribute_from_cache/cache, HookManager, GradientStorageManager, ops.*). Then remove DOC201/DOC501 from the global ignore, or keep them ignored only via per-file-ignores for private kernels. Replace the unmaintained darglint (.darglint, workflow, Makefile target) with ruff's pydoclint DOC rules so CI enforces it on every push.

### R4-3 — Enumerated string options are typed as bare `str` instead of `Literal` aliases

- **theme:** docstrings-types · **severity:** low · **effort:** M · **overlaps:** none
- **evidence:**
  - tooling: 47 public parameters typed `str = "<mode>"`: attribution_granularity×9, mode×9, loss_reduction×7, hessian_mode×4, propagation×3, capture_style×4, residency×2, …
  - dattri_llm/attribution/base.py:710,800,1047 — attribution_granularity: str = "instance"
  - dattri_llm/attribution/algorithm/adamw_influence.py:231 — loss_reduction: str = "mean"
  - dattri_llm/gradient/hooks/config.py:309 — capture_style: str = "factorized" (also hooks.py:186, gradient.py:558, ops/projection.py:886)
  - dattri_llm/gradient/gradient.py:15-16 — the codebase already defines Literal aliases (GradientRepresentation, Indexing), and gradient.py:685 uses Literal["batch", "token"]
- **fix:** Define Literal aliases next to GradientRepresentation (for example AttributionGranularity = Literal["instance", "token"], LossReduction, CaptureStyle, HessianMode, Propagation, CacheResidency) and use them in the public signatures, keeping the runtime validation. Type checkers and IDEs then show and enforce the allowed values.

### R4-4 — No PEP 561 py.typed marker, so the package's near-complete type hints are ignored downstream

- **theme:** docstrings-types · **severity:** med · **effort:** S · **overlaps:** none
- **evidence:**
  - tooling: public callables have fully annotated params 412/426 and annotated returns 426/426
  - dattri_llm/ — no py.typed file anywhere in the tree (find . -name py.typed → none)
  - pyproject.toml:44-45 — only [tool.setuptools.packages.find]; no package-data entry that would ship a marker
  - CLAUDE.md:55-56 — the project's stated style is 'type hints on every function'
- **fix:** Add an empty dattri_llm/py.typed and `[tool.setuptools.package-data] dattri_llm = ["py.typed"]` (plus the `Typing :: Typed` classifier, see R4-7), so mypy/pyright use the inline annotations for installed dattri-llm.

### R4-5 — Lazy top-level exports are invisible to type checkers and IDEs (module __getattr__ -> object, no TYPE_CHECKING imports)

- **theme:** api-surface · **severity:** med · **effort:** S · **overlaps:** none
- **evidence:**
  - dattri_llm/__init__.py:40-57 — 14 advertised names (all attributors, AttributionArguments, AttributionTask, AttributionScore, GradientStreamer, DiskGradientSource) exist only as strings in _LAZY_EXPORTS
  - dattri_llm/__init__.py:95-99 — `def __getattr__(name: str) -> object` resolves them at runtime, so static analysis types `from dattri_llm import TracInAttributor` as `object` or unresolved; the result is also never cached in globals()
  - README.md:35,131,145 — the documented entry point is exactly `from dattri_llm import AttributionArguments, TracInAttributor`
  - pyproject.toml:125-128 — F822 has to be suppressed because __all__ names are undefined at module level
- **fix:** Add an `if TYPE_CHECKING:` block in dattri_llm/__init__.py that imports the 14 lazy names from their modules (no runtime cost, which keeps the torch-only import). Optionally cache in __getattr__ (`value = getattr(import_module(module), name); globals()[name] = value`). This follows PEP 562 lazy-loading practice (for example scientific-python lazy_loader's stub approach).

### R4-6 — tqdm is a hard runtime import but is not a declared dependency

- **theme:** repo-hygiene · **severity:** med · **effort:** S · **overlaps:** H3
- **evidence:**
  - dattri_llm/gradient/datasets.py:16 — module-level `from tqdm.auto import tqdm`
  - dattri_llm/attribution/algorithm/trajectory.py:33 — module-level `from tqdm.auto import tqdm` (base of AdamW-influence and DVEmb)
  - dattri_llm/gradient/streaming.py:50 and attribution/algorithm/kronecker.py:61 — import dattri_llm.gradient.datasets, so every attributor import pulls in tqdm; streaming.py:1281 also imports it lazily
  - pyproject.toml:13-15 — dependencies = ["torch>=2.1"] only; tqdm is in no extra (lines 17-42) and is satisfied only transitively (for example via transformers)
- **fix:** Declare `tqdm>=4` in [project].dependencies (it is used on the disk-attribution path that the README presents as not needing transformers), or move the imports inside the verbose branches and add tqdm to the relevant extra.

### R4-7 — pyproject [project] metadata is minimal: no readme, authors, urls, classifiers or keywords

- **theme:** repo-hygiene · **severity:** low · **effort:** S · **overlaps:** none
- **evidence:**
  - pyproject.toml:6-15 — [project] has only name, version, description, license, license-files, requires-python, dependencies
  - no `readme = "README.md"`, so a built sdist/wheel has an empty long description
  - no [project.urls] (Homepage/Repository/Issues) although README.md:98 (git clone) points at github.com/TRAIS-Lab/dattri-llm
  - no classifiers (Python 3.10+ versions, Typing :: Typed, Topic :: Scientific/Engineering :: Artificial Intelligence), no authors/keywords
- **fix:** Add readme = "README.md", authors = [{name = "TRAIS Lab"}] (or the maintainers), keywords, [project.urls] Homepage/Repository/Issues, and classifiers for the supported Python versions, Development Status, Intended Audience :: Science/Research, Topic :: Scientific/Engineering :: Artificial Intelligence, and Typing :: Typed. Omit the License :: classifier, which PEP 639 deprecates when `license` is an SPDX string.

### R4-8 — Version string is duplicated between pyproject.toml and __init__.py

- **theme:** repo-hygiene · **severity:** low · **effort:** S · **overlaps:** none
- **evidence:**
  - pyproject.toml:8 — version = "0.1.0"
  - dattri_llm/__init__.py:38 — __version__ = "0.1.0" hardcoded separately
- **fix:** Keep one source of truth: either `__version__ = importlib.metadata.version("dattri-llm")` in __init__.py, or `dynamic = ["version"]` with `[tool.setuptools.dynamic] version = {attr = "dattri_llm.__version__"}`.

### R4-9 — No CONTRIBUTING.md or CHANGELOG.md; the developer workflow is documented only in the agent file CLAUDE.md

- **theme:** repo-hygiene · **severity:** low · **effort:** S · **overlaps:** none
- **evidence:**
  - repo root (tip 7f967a5) — no CONTRIBUTING*, CHANGELOG*, or HISTORY* files
  - CLAUDE.md:40-49 — the only description of `make check`, `make test`, the `pytest -m "not gpu"` marker and 'run make check before every commit'
  - CLAUDE.md:83-90 — the only description of CI and PR-comment triggers (`run gpu test`, `run expensive examples`, `run darglint`)
  - README.md headings (1,63,93,158,194,204,227,233) — no Development/Contributing section
- **fix:** Add CONTRIBUTING.md (dev install `pip install -e ".[dev]"`, `make check`, test markers, docstring/type-hint conventions, PR-comment CI triggers) with a one-line link from the README, and a Keep-a-Changelog CHANGELOG.md starting at 0.1.0 (including the new GRPO example).

### R4-10 — Layer-filter argument is named `layer_name` (singular) in the attribution API but `layer_names` in the capture layer

- **theme:** naming · **severity:** med · **effort:** M · **overlaps:** none
- **evidence:**
  - dattri_llm/attribution/base.py:165,360,392,1043 — `layer_name: str | list[str] | None` described as 'Restrict scoring to this subset of the stored layers'
  - dattri_llm/attribution/algorithm/kronecker.py:911,1100,1280 and gradient/streaming.py:122,1186 — same singular name for a list
  - dattri_llm/attribution/score.py:81 — AttributionScore.layer_name: list[str] | None
  - dattri_llm/gradient/hooks/hooks.py:180,814,899 — `layer_names: set[str] | None`; gradient/gradient.py:650 Gradient.select_layers(layer_names: Iterable[str])
- **fix:** Rename the plural-valued parameter and field to `layer_names` across the attribution and streaming API. Accept `layer_name=` for one release through a keyword shim that emits a DeprecationWarning (and a matching AttributionScore property alias).

### R4-11 — DVEmb reimplements contextlib.nullcontext as a lowercase class with a naming-rule suppression

- **theme:** structure-duplication · **severity:** low · **effort:** S · **overlaps:** none
- **evidence:**
  - dattri_llm/attribution/algorithm/dvemb.py:121-126 — `class _null_context:  # noqa: N801` with no-op __enter__/__exit__
  - dattri_llm/attribution/algorithm/dvemb.py:678 — only use: `else _null_context(),`
  - dattri_llm/gradient/streaming.py:1082 — the same package already uses `contextlib.nullcontext()`
- **fix:** Delete _null_context and use `contextlib.nullcontext()` at dvemb.py:678 (removing the noqa).

### R4-12 — Repeated magic-number defaults and unnamed epsilons in the attribution kernels

- **theme:** structure-duplication · **severity:** low · **effort:** S · **overlaps:** none
- **evidence:**
  - dattri_llm/attribution/algorithm/kronecker.py:198,1004,1105,1276 — default damping literal 1e-3 repeated four times
  - dattri_llm/attribution/algorithm/kronecker.py:200,910,1006,1110,1279 — direct_fim_max_params default 4096 repeated five times
  - dattri_llm/attribution/algorithm/tracin.py:107,125 — cosine denominator `+ 1e-8` duplicated inline; less.py:69 `clamp_min(1e-16)`
  - dattri_llm/gradient/ops/optimizer.py:111-112 — NAdam's 0.96 momentum-decay base inlined twice
  - pyproject.toml:92 — PLR2004 is ignored globally with the rationale 'tensor rank / dimension literals', but these are hyperparameters, not ranks
- **fix:** Introduce module constants (DEFAULT_DAMPING = 1e-3, DEFAULT_DIRECT_FIM_MAX_PARAMS = 4096 in kronecker.py; _COSINE_EPS in tracin.py; _NADAM_DECAY_BASE = 0.96 in ops/optimizer.py) and reference them in the signatures and bodies. That keeps the defaults in sync and self-documenting.

### R4-13 — Very large modules and functions, with the complexity rules disabled globally rather than per-file

- **theme:** structure-duplication · **severity:** low · **effort:** L · **overlaps:** none
- **evidence:**
  - tooling: 10 modules >900 lines — gradient/hooks/manager.py 1711, attribution/algorithm/kronecker.py 1593, gradient/callbacks/data_selection_callback.py 1348, gradient/streaming.py 1324, gradient/storage_manager.py 1290, gradient/gradient.py 1221, attribution/base.py 1115
  - dattri_llm/gradient/hooks/hooks.py:178 — register_linear_io_hooks is 242 lines; attribution/algorithm/dvemb.py:700 attribute_from_cache 198; attribution/utils.py:238 score_sources 197; attribution/algorithm/kronecker.py:1094 attribute_from_cache 169; gradient/streaming.py:441 __init__ 161; gradient/hooks/manager.py:197 HookManager.__init__ 150 (19 functions >100 lines in total)
  - pyproject.toml:73,93-95 — C901, PLR0912, PLR0914, PLR0915 ignored for the whole package (rationale: 'dispatch/assembly kernels')
- **fix:** Move the C901/PLR0912/PLR0914/PLR0915 ignores from the global list into extend-per-file-ignores for the specific kernel modules, so new code elsewhere is checked. Split the worst offenders along existing seams: register_linear_io_hooks into _make_forward_hook/_make_backward_hook module helpers, and HookManager's registration vs assembly halves into separate modules.

### R4-14 — User-facing module_kwargs builders (rms_norm_module_kwargs etc.) are not exported or listed by dattri_llm.utils

- **theme:** api-surface · **severity:** low · **effort:** S · **overlaps:** R1-C3, R2-4, H1
- **evidence:**
  - dattri_llm/utils/module.py:78-439 — 18 public keyword-only builders (linear_, embedding_, conv*_, layer_norm_, rms_norm_, group_norm_, instance_norm*_module_kwargs) with full Google docstrings
  - dattri_llm/utils/__init__.py:1-12 — package docstring lists hashing/distributed/autograd/cache but not utils.module; __init__.py:31-43 __all__ omits every builder
  - examples/projection/gradient_projection.py:17 and examples/projection/README.md:108 — users must deep-import `from dattri_llm.utils.module import rms_norm_module_kwargs`
  - dattri_llm/gradient/hooks/config.py:171-178 — the HookManagerConfig RMSNorm example spells the raw dict, while utils/module.py:10-18 recommends the builder
- **fix:** Re-export the builders from dattri_llm.utils (add them to __all__), list utils.module in the package docstring, and make the HookManagerConfig docstring example use rms_norm_module_kwargs(...) like module.py does.

### R4-15 — Internal helpers carry public names in modules that define no __all__

- **theme:** api-surface · **severity:** low · **effort:** S · **overlaps:** none
- **evidence:**
  - tooling: 0 of 42 non-__init__ modules define __all__; 49 module-level public defs are in no __all__
  - dattri_llm/gradient/streaming.py:1076 autocast_for and :1087 move_batch — used only inside streaming.py (824,1073,1290,1294), not exported, no underscore
  - dattri_llm/gradient/datasets.py:29 identity_collate — used only inside datasets.py
  - contrast: the same package does use underscores for internals (e.g. utils/module.py `_conv_module_kwargs`, `_instance_norm_module_kwargs`)
- **fix:** Prefix module-internal helpers with an underscore (_autocast_for, _move_batch, _identity_collate, …), or add a module-level __all__ to each implementation module listing its intended public names, so the private/public boundary is explicit (PEP 8 'Public and internal interfaces').

### R4-16 — Examples bypass the advertised top-level API: deep implementation imports and a sys.path hack in every script

- **theme:** api-surface · **severity:** low · **effort:** S · **overlaps:** H3
- **evidence:**
  - examples/attribution/attribution_from_disk.py:11 — `sys.path.insert(0, str(pathlib.Path(__file__).resolve().parents[2]))`; the same line is in all 11 example scripts (including the new examples/trainers/trl_grpo_trainer.py:31)
  - examples/attribution/attribution_from_disk.py:17,21 — `from dattri_llm.attribution.algorithm.tracin import TracInAttributor`, `from dattri_llm.gradient.storage_manager import GradientStorageManager`
  - examples/trainers/trl_grpo_trainer.py:44-46 — imports HookManager/HookManagerConfig/GradientStorageManager from submodules
  - README.md:35,111,131,145 and dattri_llm/__init__.py:3-8 — document `from dattri_llm import HookManager, HookManagerConfig, OffloadCallback, TracInAttributor, …`; README.md:97-101 installs with `pip install -e .`
- **fix:** Switch the examples to the public imports (`from dattri_llm import HookManager, HookManagerConfig, GradientStorageManager, TracInAttributor, …`) and drop the sys.path.insert lines, relying on the README's `pip install -e .`. The examples then show the supported API surface and fail loudly if an export regresses.

