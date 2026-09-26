# Blind dual-LGTM ROUND 4 (library readability) — dattri-llm
- **tip:** `7f967a591c4539d6c5181b7af2921919d719bbc5` (seats checked: opus `7f967a591c4539d6c5181b7af2921919d719bbc5`, astra `7f967a591c4539d6c5181b7af2921919d719bbc5`)
- **generated:** 2026-09-26T16:29-04:00 (America/Toronto)
- **seats:** Claude Opus via Claude Code CLI (print mode, dontAsk, Read/Grep/Glob) | Codex `gpt-6-astra` (read-only sandbox); launched independently from the same prompt file
- **gate per seat:** real AND industry_norm AND not_taste; AGREED = both overall Y
- cells: real/industry_norm/not_taste→overall

| id | sev | title | opus | astra | agreed |
|---|---|---|---|---|---|
| R4-1 | med | Lint toolchain is pinned only in CI: dev extra, Makefile and contributor docs use an unpinned ruff, and there is no pre-commit | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-2 | med | Google docstring sections are mostly missing on the public API, and the checks that would catch it are switched off | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-3 | low | Enumerated string options are typed as bare `str` instead of `Literal` aliases | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-4 | med | No PEP 561 py.typed marker, so the package's near-complete type hints are ignored downstream | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-5 | med | Lazy top-level exports are invisible to type checkers and IDEs (module __getattr__ -> object, no TYPE_CHECKING imports) | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-6 | med | tqdm is a hard runtime import but is not a declared dependency | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-7 | low | pyproject [project] metadata is minimal: no readme, authors, urls, classifiers or keywords | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-8 | low | Version string is duplicated between pyproject.toml and __init__.py | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-9 | low | No CONTRIBUTING.md or CHANGELOG.md; the developer workflow is documented only in the agent file CLAUDE.md | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-10 | med | Layer-filter argument is named `layer_name` (singular) in the attribution API but `layer_names` in the capture layer | Y/Y/Y→**Y** | Y/Y/N→**N** | N |
| R4-11 | low | DVEmb reimplements contextlib.nullcontext as a lowercase class with a naming-rule suppression | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-12 | low | Repeated magic-number defaults and unnamed epsilons in the attribution kernels | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-13 | low | Very large modules and functions, with the complexity rules disabled globally rather than per-file | Y/Y/Y→**Y** | Y/Y/N→**N** | N |
| R4-14 | low | User-facing module_kwargs builders (rms_norm_module_kwargs etc.) are not exported or listed by dattri_llm.utils | Y/Y/Y→**Y** | N/Y/Y→**N** | N |
| R4-15 | low | Internal helpers carry public names in modules that define no __all__ | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |
| R4-16 | low | Examples bypass the advertised top-level API: deep implementation imports and a sys.path hack in every script | Y/Y/Y→**Y** | Y/Y/Y→**Y** | **Y** |

## AGREED (13)

- **R4-1** — Lint toolchain is pinned only in CI: dev extra, Makefile and contributor docs use an unpinned ruff, and there is no pre-commit
- **R4-2** — Google docstring sections are mostly missing on the public API, and the checks that would catch it are switched off
- **R4-3** — Enumerated string options are typed as bare `str` instead of `Literal` aliases
- **R4-4** — No PEP 561 py.typed marker, so the package's near-complete type hints are ignored downstream
- **R4-5** — Lazy top-level exports are invisible to type checkers and IDEs (module __getattr__ -> object, no TYPE_CHECKING imports)
- **R4-6** — tqdm is a hard runtime import but is not a declared dependency
- **R4-7** — pyproject [project] metadata is minimal: no readme, authors, urls, classifiers or keywords
- **R4-8** — Version string is duplicated between pyproject.toml and __init__.py
- **R4-9** — No CONTRIBUTING.md or CHANGELOG.md; the developer workflow is documented only in the agent file CLAUDE.md
- **R4-11** — DVEmb reimplements contextlib.nullcontext as a lowercase class with a naming-rule suppression
- **R4-12** — Repeated magic-number defaults and unnamed epsilons in the attribution kernels
- **R4-15** — Internal helpers carry public names in modules that define no __all__
- **R4-16** — Examples bypass the advertised top-level API: deep implementation imports and a sys.path hack in every script

## Split (3)

- **R4-10** — Layer-filter argument is named `layer_name` (singular) in the attribution API but `layer_names` in the capture layer — opus=Y (base.py:165,360,392,1043, kronecker.py:911,1100,1280, streaming.py:122,1186 and score.py:81 all use singular `layer_name` for a list, and HookManager exposes both `layer_names` (manager.py:1631) and an identical `layer_name` (manager.py:1636) — an objective self-inconsistency, and the DeprecationWarning shim keeps the rename non-breaking.) | astra=N (The naming difference exists at dattri_llm/attribution/base.py:165 and gradient/hooks/hooks.py:180, but the former deliberately accepts either one name or a list; renaming this documented API remains a preference.)
- **R4-13** — Very large modules and functions, with the complexity rules disabled globally rather than per-file — opus=Y (Counts are exact (manager.py 1711, kronecker.py 1593, hooks.py:178 register_linear_io_hooks 242 lines) and pyproject.toml:73,93-95 disable C901/PLR0912/PLR0914/PLR0915 package-wide; scoping blanket ignores to the named kernel files via extend-per-file-ignores is normative ruff practice, and the suggested seam (nested _make_forward_hook at hooks.py:314) already exists — the module split is the weaker half but the item stands.) | astra=N (Size counts and pyproject.toml:73,93-95 suppressions are correct, but hooks.py:314 and :360 already isolate hooks as closures; scoped lint exceptions are useful, while the bundled structural splits remain design preferences.)
- **R4-14** — User-facing module_kwargs builders (rms_norm_module_kwargs etc.) are not exported or listed by dattri_llm.utils — opus=Y (utils/__init__.py:1-12,31-43 lists hashing/distributed/autograd/cache but omits utils.module entirely, so users deep-import (examples/projection/gradient_projection.py:17) a helper set module.py:10-18 tells them to use, and config.py:171-178 spells the raw dict instead; the builder count is 16 rather than the stated 18, which does not affect the finding.) | astra=N (dattri_llm/utils/module.py:78-439 defines 16 public builders, not the claimed 18; omission from utils/__init__.py:31 and the raw-dict example at gradient/hooks/config.py:175 are otherwise confirmed.)

## Both NO (0)


## Reasons (one line per seat)

- **R4-1** opus: lint.yml:35 pins ruff==0.15.20 but pyproject.toml:35-42 dev has no ruff and Makefile:11 is unpinned on a narrower path set; no .pre-commit-config.yaml; required-version + ruff-pre-commit are the documented ruff mechanisms, and CLAUDE.md:85-86's 'make check' claim is wrong.
  astra: .github/workflows/lint.yml:35 pins Ruff, while pyproject.toml:35 omits it and Makefile:11 uses different paths; synchronizing installation, version enforcement and commands improves reproducibility.
- **R4-2** opus: kronecker.py:227-246, dot.py:423-430 and adamw_influence.py:435-444 are prose-only on exported callables while pyproject.toml:77-78 ignores DOC201/DOC501 and .darglint:3 strictness=long runs only on PR comment; sections on the public API plus ruff pydoclint replacing unmaintained darglint is the repo's own stated Google convention.
  astra: Docstring counts reproduce; dattri_llm/gradient/ops/dot.py:429 and attribution/algorithm/adamw_influence.py:444 lack parameter/result sections, while pyproject.toml:77 disables relevant checks; documenting applicable public contracts is substantive.
- **R4-3** opus: base.py:710,800,1047 and config.py:309 type enumerated options as bare str while gradient.py:15-16,952 and kronecker.py:82 already use Literal aliases; PEP 586 Literal for closed string sets, consistent with in-repo precedent.
  astra: The 47 string-default parameters reproduce; dattri_llm/attribution/base.py:710 and gradient/hooks/config.py:309 accept finite modes as str; Literal aliases improve static validation without changing runtime behavior.
- **R4-4** opus: No py.typed tracked anywhere and pyproject.toml:44-45 ships no package-data, so PEP 561 forces downstream checkers to ignore the 426/426 annotated returns; an empty marker plus package-data is the standard fix.
  astra: No tracked py.typed exists, and pyproject.toml:44 only configures package discovery; the annotation totals reproduce, and shipping the marker is the standard PEP 561 opt-in.
- **R4-5** opus: __init__.py:42-57 lists 14 names only as strings and :95-99 resolves them via `__getattr__(name) -> object` (uncached), with F822 suppressed at :125-128; a TYPE_CHECKING import block is the documented PEP 562 lazy-export pattern and costs nothing at runtime.
  astra: dattri_llm/__init__.py:42 contains 14 lazy exports and :95 returns object without static imports; TYPE_CHECKING re-exports expose their actual types while preserving lazy runtime loading.
- **R4-6** opus: datasets.py:16 and trajectory.py:33 import tqdm at module level (pulled in transitively by streaming.py:49 and kronecker.py:61) while pyproject.toml:13-15 declares only torch>=2.1, so the advertised disk path works only by accident; declaring an imported runtime dep is basic PEP 621 hygiene.
  astra: dattri_llm/gradient/datasets.py:16 and attribution/algorithm/trajectory.py:33 import tqdm unconditionally, but pyproject.toml:13 declares only torch; declaring the direct dependency fixes an installation contract.
- **R4-7** opus: pyproject.toml:6-15 has no readme/authors/urls/classifiers/keywords, so a build has an empty long description; the additions are exactly what the Packaging User Guide prescribes and the advice to omit `License ::` is correct under PEP 639 given license = "MIT" at :10.
  astra: pyproject.toml:6 lacks the cited descriptive metadata, despite README.md:98 providing the repository URL; README and project links materially improve distribution discoverability and identification.
- **R4-8** opus: pyproject.toml:8 and dattri_llm/__init__.py:38 both hardcode "0.1.0"; single-sourcing via importlib.metadata or setuptools dynamic version is the standard PyPA guidance.
  astra: pyproject.toml:8 and dattri_llm/__init__.py:38 independently hardcode 0.1.0; the proposed setuptools dynamic-version alternative removes a real synchronization obligation.
- **R4-9** opus: No CONTRIBUTING/CHANGELOG in the tracked root set and README.md has no Development section (headings 1,63,93,158,194,204,227,233), leaving CLAUDE.md:40-49,83-90 as the only contributor workflow — a discoverability gap for human contributors, not a style preference.
  astra: The tracked tree lacks contribution/release-history files; CLAUDE.md:42 and :88 hold the contributor commands and CI triggers, so linked contributor documentation and release notes improve accessibility.
- **R4-10** opus: base.py:165,360,392,1043, kronecker.py:911,1100,1280, streaming.py:122,1186 and score.py:81 all use singular `layer_name` for a list, and HookManager exposes both `layer_names` (manager.py:1631) and an identical `layer_name` (manager.py:1636) — an objective self-inconsistency, and the DeprecationWarning shim keeps the rename non-breaking.
  astra: The naming difference exists at dattri_llm/attribution/base.py:165 and gradient/hooks/hooks.py:180, but the former deliberately accepts either one name or a list; renaming this documented API remains a preference.
- **R4-11** opus: dvemb.py:121-126 hand-rolls a lowercase no-op context with `# noqa: N801` for its single use at :678 while streaming.py:1082 already calls contextlib.nullcontext(); deleting it is a strict simplification.
  astra: dattri_llm/attribution/algorithm/dvemb.py:121 implements a no-op context used only at :678; contextlib.nullcontext provides the same required behavior and removes redundant implementation.
- **R4-12** opus: 1e-3 repeated at kronecker.py:198,1004,1105,1276 and 4096 at :200,910,1006,1110,1279 are defaults that must stay in sync across signatures, plus unnamed epsilons at tracin.py:107,125 and optimizer.py:111-112; named module constants are standard and PLR2004's global ignore (pyproject.toml:92) is justified for shape literals, not hyperparameters.
  astra: Repeated defaults are confirmed at dattri_llm/attribution/algorithm/kronecker.py:198, :1004, :1105 and :1276; shared constants prevent drift, while tracin.py:107 and :125 share the same stabilization epsilon.
- **R4-13** opus: Counts are exact (manager.py 1711, kronecker.py 1593, hooks.py:178 register_linear_io_hooks 242 lines) and pyproject.toml:73,93-95 disable C901/PLR0912/PLR0914/PLR0915 package-wide; scoping blanket ignores to the named kernel files via extend-per-file-ignores is normative ruff practice, and the suggested seam (nested _make_forward_hook at hooks.py:314) already exists — the module split is the weaker half but the item stands.
  astra: Size counts and pyproject.toml:73,93-95 suppressions are correct, but hooks.py:314 and :360 already isolate hooks as closures; scoped lint exceptions are useful, while the bundled structural splits remain design preferences.
- **R4-14** opus: utils/__init__.py:1-12,31-43 lists hashing/distributed/autograd/cache but omits utils.module entirely, so users deep-import (examples/projection/gradient_projection.py:17) a helper set module.py:10-18 tells them to use, and config.py:171-178 spells the raw dict instead; the builder count is 16 rather than the stated 18, which does not affect the finding.
  astra: dattri_llm/utils/module.py:78-439 defines 16 public builders, not the claimed 18; omission from utils/__init__.py:31 and the raw-dict example at gradient/hooks/config.py:175 are otherwise confirmed.
- **R4-15** opus: streaming.py:1076 autocast_for and :1087 move_batch (used only at :824,1073,1290,1294) and datasets.py:29 identity_collate carry public names, and only the 8 __init__.py files define __all__; PEP 8's public/internal-interface rule and the repo's own _conv_module_kwargs precedent both back the underscore.
  astra: The 42-module and 49-definition counts reproduce; dattri_llm/gradient/streaming.py:1076,1087 and gradient/datasets.py:29 contain module-local helpers with public names; explicit boundaries follow PEP 8.
- **R4-16** opus: sys.path.insert appears in all 11 example scripts (attribution_from_disk.py:11, trl_grpo_trainer.py:31) alongside deep imports (:17,:21) of names README.md:105-150 advertises from the top level, and examples_test.yml:34 already pip-installs the package, so dropping the hack is safe and makes the examples regression-test the public surface.
  astra: All 11 scripts inject sys.path; examples/attribution/attribution_from_disk.py:11,17 bypasses the installed public surface documented at README.md:100,131; public imports make examples exercise the supported entry points.
