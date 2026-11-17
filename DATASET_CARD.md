# LineageForge Dataset Card

## Dataset

This release provides **1,043 dataset records** constructed by LineageForge from vulnerabilities observed in real-world attacks. The records are organized into seven lineages, each derived from a selected real-world incident.

**We will continue to update this dataset.**

Each dataset record contains a **protected contract S1**, its corresponding **vulnerable contract S2**, executable **proof-of-concept (PoC) attacks**, and the execution environment. S2 is derived from S1 through targeted code changes. The corresponding PoCs execute equivalent attacker actions under relevant preconditions to check whether the intended exploit succeeds in S2 and is blocked in S1.

| Lineage | Directory identifier | Strong | Acceptable | Weak | Records |
| --- | --- | ---: | ---: | ---: | ---: |
| BCBC | `bsc_bcbc_setreward_reward_authority` | 150 | 23 | 8 | **181** |
| ByToken | `bytoken_public_triggerautoburn_pool_reserve_shift` | 151 | 4 | 10 | **165** |
| GPU | `gpu_self_transfer_balance_inflation` | 50 | 0 | 0 | **50** |
| Legendary | `legendarymoneymonnft_ecrecover_zero_signature_bypass` | 151 | 17 | 15 | **183** |
| MURE | `muredistribution_erc1271_arbitrary_signer_bypass` | 50 | 1 | 2 | **53** |
| ODOS | `odos_isvalidsigimpl_erc6492_side_effect_call` | 298 | 30 | 16 | **344** |
| SorStaking | `sorstaking_withdraw_reward_not_consumed` | 51 | 9 | 7 | **67** |
| **Total** | **7 lineages** | **901** | **84** | **58** | **1,043** |

The paper reports **797 accepted dataset records** from the initial construction campaign (**767** in the initial standardized release). Following the **2026-11-16 to 2026-11-17** expansion and standardization campaign (resolving **27** multi-batch historical records across 16 BCBC and 11 GPU cases, expanding **BCBC** by 125 records to 150 Strong and **ByToken** by 113 records to 151 Strong, and replacing the initial **SorStaking** batch with a clean 67-record Strong-50 rerun containing 51 Strong), the complete release contains the **1,043 records** listed above. The [README](README.md) provides downloads, the directory layout, and a usage example.

### Archive checksums (SHA-256)

| Archive | Size (bytes) | SHA-256 |
| --- | ---: | --- |
| `BCBC.zip` | 4,948,554 | `8340459829ec8e8d67552c3d23a862f972e212e2bbc5ffb485c6c15d1ca91729` |
| `ByToken.zip` | 5,900,939 | `3d5f3b285a167d98aef0668cf8cde8605e2d01f194cab7eb4cbbaea1eabb2bb6` |
| `GPU.zip` | 883,287 | `a092ae8c2f34f5056bc6fb7248d39c848b3b9e8a9e0ce21fac78c7a7aedac47b` |
| `Legendary.zip` | 12,049,481 | `c0cdc83978379f0c4db91b3a292d21bc57fc3d14803a231ded5cf39013de64b0` |
| `MURE.zip` | 5,799,819 | `a4d25fe3d619894af1e063bfaca123b5607601a9c0718a3b494a665d787cfecb` |
| `ODOS.zip` | 31,718,139 | `4fc3bc5ddd5857941385fe852922c4a0ad70265769dbb4607937292c670600fd` |
| `SorStaking.zip` | 5,056,177 | `2c877547b5bcf3f33646b717f76178ca87fb3163e1a1005df531fa933c3bf4f6` |

## Source processing

The source contracts come from the [Sourcify verified contract corpus](https://docs.sourcify.dev/docs/repository/download-dataset/). LineageForge identifies compatible contracts, constructs S1 and S2, and adapts the corresponding PoCs.

The [incident references](README.md#incident-references) list the source incident contracts and attack transactions for all seven lineages.

The executable archive uses the `comments_only_v2` policy (`model_calls = 0`). Across **41,436** Solidity file instances, **890,605** ordinary and NatSpec comments are replaced with whitespace while preserving line breaks and character positions. Existing copyright and SPDX licence notices, **8,860** `@solidity` compiler directive comments (such as `memory-safe-assembly`), **281,902** string tokens, identifiers, conditions, and assertions are retained unchanged (`all_non_comment_tokens_equal = true`). Each case's `cleaning_report.json` records file-level digests and comment counts.

## PoC execution records

The archive includes **2,086 recorded passing PoC executions** (`2,639` passed test occurrences, zero skipped or failed tests) across all **1,043** dataset records. Standardization regenerated **1,116** executions across **558** packages under `comments_only_v2` and retained **970** existing execution records from **485** byte-identical packages. Each PoC project (`poc/s1/` and `poc/s2/`) includes `result.json` and `forge.log`. The case's `manifest.json` records the Forge version, test path, and SHA-256 digests of all package files.

To reproduce a recorded execution, use the Forge version specified in `manifest.json` and retain the project's configuration and dependencies. Run the command stored in `result.json` or on the first line of `forge.log` from the corresponding `poc/s1/` or `poc/s2/` directory. Both test runs are expected to pass: the S2 test checks that the exploit succeeds, and the S1 test checks that it is blocked.

## Labels and catalog

The catalog records historical pipeline quality labels for **901 Strong**, **84 Acceptable**, and **58 Weak** records, corresponding to the directory names `strong_replay`, `acceptable_replay`, and `weak_replay`.

[catalog.json](catalog.json) (`schema_version = incident_inspired_archive_catalog_v1`) contains one entry per dataset record, indexing its lineage, case ID, relative path, package SHA-256 digests, historical quality label, execution provenance (`fresh_v2_execution` or `byte_identical_reuse`), target licence declarations, and cleaning statistics. Benchmark qualification annotations partition the archive into `candidate_requires_semantic_review` (**900** unflagged Strong records), `conditional_supplemental_archive` (**136** unflagged Acceptable/Weak records), and `prior_audit_hold` (**7** records carrying a `prior_audit_flag`).

## De-labeled model inputs

Every lineage archive provides de-labeled target contract files for vulnerability detection and model training at `<lineage>/masked_inputs/` under the `rewrite_diagnostics_v2` policy, covering all **1,043** pairs (**2,086** target Solidity files).

1. **Component-scoped diagnostic masking.** Using `components.json`, the transformation inspects bare `require(cond, "...")` and `revert("...")` statements inside verified rewritten components. Static string literals absent from the original S0 component snapshot are replaced with `"STR"`. All S0 strings, EIP-712 domain parameters, selectors, and non-diagnostic literals remain unchanged.
2. **Comment-erasure whitespace normalization.** Trailing spaces left behind by fixed-width comment blanking are stripped and runs of consecutive blank lines are capped at one, verified to preserve 100% of Solidity code tokens.

| Lineage | Pairs | Target files | Diagnostic-changed files | Replacements (`S1` / `S2`) | Whitespace-normalized files |
| --- | ---: | ---: | ---: | ---: | ---: |
| BCBC | 181 | 362 | 0 | 0 (`0` / `0`) | 360 |
| ByToken | 165 | 330 | 14 | 14 (`14` / `0`) | 328 |
| GPU | 50 | 100 | 9 | 11 (`6` / `5`) | 98 |
| Legendary | 183 | 366 | 202 | 327 (`244` / `83`) | 350 |
| MURE | 53 | 106 | 2 | 2 (`1` / `1`) | 102 |
| ODOS | 344 | 688 | 666 | 776 (`365` / `411`) | 670 |
| SorStaking | 67 | 134 | 0 | 0 (`0` / `0`) | 130 |
| **Total** | **1,043** | **2,086** | **893** | **1,130** (`630` / `500`) | **2,038** |

Use `<lineage>/masked_inputs/` as model inputs and the original case projects for compilation and PoC execution. Each lineage's `masked_inputs/manifest.json` maps every masked file to its original source file and records SHA-256 digests, `target_label` (`0` for `S1`, `1` for `S2`), and exact replacement spans. The paper's ODOS training experiment uses **298 dataset records** from a separately frozen pool under the diagnostic-masking policy.

## Token usage and cost

The initial construction campaign primarily used **DeepSeek V4 Flash 0423 (April version)**, with additional DeepSeek V4 Pro calls. The **2026-11-16–17** expansion runs (BCBC Strong-150, ByToken Strong-150, and SorStaking Strong-50) used **DeepSeek (`deepseek-v4-flash`)**, with token and request counts aggregated from retained completed API traces and API cost reported from daily billing.

| Metric | Initial campaign | Expansion (2026-11-16–17) | Cumulative total |
| --- | ---: | ---: | ---: |
| Tokens | 380,471,095 | 43,768,290 | **424,239,385** |
| API requests | 19,465 | 2,356 | **21,821** |
| Reported API cost | ¥239.82 CNY | ¥64.73 CNY | **¥304.55 CNY** |

Expansion breakdown:
- **By lineage (retained completed API traces)**: BCBC Strong-150 (`14,368,896` tokens, `942` requests), ByToken Strong-150 (`16,421,506` tokens, `838` requests), and SorStaking Strong-50 (`12,977,888` tokens, `576` requests).
- **By billing date**: `2026-11-16` (**¥26.05 CNY**) and `2026-11-17` (**¥38.68 CNY**), totaling **¥64.73 CNY**.

We will update these usage and cost figures alongside future dataset updates.

## File metadata and licensing

**S0** denotes the source contract used for construction. The `components.json` file stores original source component snapshots alongside the corresponding code in S1 and S2. The `manifest.json` file records the files and execution configuration of each dataset record, and `cleaning_report.json` documents comment processing.

Existing copyright and licence notices are retained in the distributed source files. Licence declarations for the target source files are indexed in [catalog.json](catalog.json) (`95` records have target files without an explicit SPDX licence identifier, and `88` records include `UNLICENSED` declarations).
