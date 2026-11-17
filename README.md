# LineageForge Dataset

> Smart contract vulnerabilities derived from real-world attacks, with protected and vulnerable contracts, executable PoCs, execution environments, and de-labeled model inputs.

[Overview](#overview) · [Downloads](#downloads) · [Getting started](#getting-started) · [De-labeled model inputs](#de-labeled-model-inputs) · [Token usage and cost](#token-usage-and-cost) · [Incident references](#incident-references) · [Dataset card](DATASET_CARD.md) · [Catalog](catalog.json)

| Dataset records | Quality breakdown (Strong / Acceptable / Weak) | Incident-derived lineages | Recorded passing PoC executions |
| :---: | :---: | :---: | :---: |
| **1,043** | **901 / 84 / 58** | **7** | **2,086** |

**We will continue to update this dataset.**

## Overview

LineageForge is a framework for constructing verifiable smart contract vulnerability datasets at scale from vulnerabilities observed in real-world attacks. This release provides **1,043 dataset records** (**901 Strong**, **84 Acceptable**, and **58 Weak**) organized into seven lineages, each derived from a selected real-world incident.

Each dataset record contains a **protected contract S1**, its corresponding **vulnerable contract S2**, executable **proof-of-concept (PoC) attacks**, and the execution environment. S2 is derived from S1 through targeted code changes. The corresponding PoCs execute equivalent attacker actions under relevant preconditions to check whether the intended exploit succeeds in S2 and is blocked in S1.

| Contract | Role | Intended attack outcome |
| --- | --- | --- |
| **S1 — Protected contract** | Provides protection against the selected vulnerability | The intended exploit is blocked |
| **S2 — Vulnerable contract** | Is derived from S1 through targeted code changes | The intended exploit succeeds |

### How the records are constructed

1. **Prepare the lineage specification.** Incident evidence, local reference contracts, and their PoCs guide the requirements for discovery, permitted code changes, and expected attack outcomes.
2. **Discover compatible contracts.** Incident-Guided Discovery (IGD) identifies source-verified contracts with the required business logic and collects source evidence to guide construction.
3. **Construct and validate the contracts.** Evidence-Grounded Contract Construction (EGCC) guides large language models to construct S1 and S2, adapt their PoCs, and check the contracts and attack outcomes through compilation, execution, and source and PoC review.

The paper reports **797 accepted dataset records** from the initial construction campaign (**767** in the initial standardized release). Following the **2026-11-16 to 2026-11-17** expansion and standardization campaign (resolving **27** multi-batch historical records across 16 BCBC and 11 GPU cases, expanding **BCBC** by 125 records to 150 Strong and **ByToken** by 113 records to 151 Strong, and replacing the initial **SorStaking** batch with a clean 67-record Strong-50 rerun containing 51 Strong), the complete release contains **1,043 records**, indexed in [catalog.json](catalog.json).

## Downloads

Each ZIP archive contains one lineage, including both its executable case packages and its de-labeled `masked_inputs/` view. Download the archives you need together with [catalog.json](catalog.json), then extract them beside the catalog while retaining the directory structure.

| Lineage | Selected vulnerability | Strong | Acceptable | Weak | Records | Size | Archive |
| --- | --- | ---: | ---: | ---: | ---: | ---: | --- |
| BCBC | Missing authorization for token minting or reward operations | 150 | 23 | 8 | **181** | 4.72 MiB | [BCBC.zip](BCBC.zip) |
| ByToken | Unauthorized token burning or reserve maintenance | 151 | 4 | 10 | **165** | 5.63 MiB | [ByToken.zip](ByToken.zip) |
| GPU | Balance inflation through self-transfer | 50 | 0 | 0 | **50** | 0.84 MiB | [GPU.zip](GPU.zip) |
| Legendary | Signature bypass through zero-address recovery | 151 | 17 | 15 | **183** | 11.49 MiB | [Legendary.zip](Legendary.zip) |
| MURE | Signature validation with an attacker-selected signer | 50 | 1 | 2 | **53** | 5.53 MiB | [MURE.zip](MURE.zip) |
| ODOS | Side-effecting calls during signature validation | 298 | 30 | 16 | **344** | 30.25 MiB | [ODOS.zip](ODOS.zip) |
| SorStaking | Repeated withdrawal of rewards that remain claimable | 51 | 9 | 7 | **67** | 4.82 MiB | [SorStaking.zip](SorStaking.zip) |
| **Total** | **7 lineages** | **901** | **84** | **58** | **1,043** | **63.28 MiB** | |

### Inside an archive

The `path` field in [catalog.json](catalog.json) locates each record after extraction. Archive directories use the full lineage identifiers rather than the short names in the download table:

- `BCBC` → `bsc_bcbc_setreward_reward_authority`
- `ByToken` → `bytoken_public_triggerautoburn_pool_reserve_shift`
- `GPU` → `gpu_self_transfer_balance_inflation`
- `Legendary` → `legendarymoneymonnft_ecrecover_zero_signature_bypass`
- `MURE` → `muredistribution_erc1271_arbitrary_signer_bypass`
- `ODOS` → `odos_isvalidsigimpl_erc6492_side_effect_call`
- `SorStaking` → `sorstaking_withdraw_reward_not_consumed`

```text
<lineage>/
├── <historical_quality>/<case_id>/
│   ├── s1/                    Protected contract source files
│   ├── s2/                    Vulnerable contract source files
│   ├── poc/
│   │   ├── s1/                Protected-side PoC project and execution records
│   │   └── s2/                Vulnerable-side PoC project and execution records
│   ├── components.json        Rewritten components and original S0 snapshots
│   ├── manifest.json          File digests, test paths, and execution configuration
│   └── cleaning_report.json   Source preprocessing records
└── masked_inputs/             De-labeled target contracts for detection and training
    ├── <historical_quality>/<case_id>/{s1,s2}/...
    ├── manifest.json          File mapping, target labels, and replacement records
    └── README.md              Lineage-specific masking summary
```

**S0** denotes the source contract used for construction. The `components.json` file stores original source component snapshots alongside the corresponding code in S1 and S2. The [dataset card](DATASET_CARD.md) describes source preprocessing, de-labeling rules, and execution records.

## Getting started

1. **Select a record.** Use `lineage`, `case_id`, and `path` in [catalog.json](catalog.json) to locate a case in an extracted archive.
2. **Inspect the contracts.** Compare `s1/` and `s2/`, using `components.json` to inspect the rewritten components and their original S0 snapshots.
3. **Prepare the execution environment.** Install Foundry with the Forge version recorded in the case's `manifest.json`. Retain the PoC project's configuration and dependencies.
4. **Run both PoCs.** Enter `poc/s1/` and run the command stored in its `result.json` or on the first line of `forge.log`. Repeat with the command recorded in `poc/s2/`.

For example, run the following command from each PoC directory of the BCBC record `c_0015dd5ee1ea`:

```sh
forge test --root . --match-path test/PocOracle.t.sol -vvvv
```

**Both test runs are expected to pass.** The S2 test checks that the exploit succeeds, and the S1 test checks that it is blocked. The assertions and traces in `forge.log` show the behavior checked by each PoC.

### Execution records and quality labels

The archive includes **2,086 recorded passing PoC executions** (`2,639` passed test assertions, zero skipped or failed tests) across all **1,043** dataset records. Standardization under `comments_only_v2` regenerated **1,116** executions across **558** packages and retained **970** existing execution records from **485** byte-identical packages.

The catalog records historical pipeline quality labels for **901 Strong**, **84 Acceptable**, and **58 Weak** records, corresponding to the directory names `strong_replay`, `acceptable_replay`, and `weak_replay`. Prior audit annotations for seven records are stored separately in `prior_audit_flag`.

## De-labeled model inputs

Every lineage archive provides a unified model-input view at `<lineage>/masked_inputs/` covering all **1,043** contract pairs (**2,086** target Solidity files) under the `rewrite_diagnostics_v2` policy:

1. **Component-scoped diagnostic masking.** Within verified rewritten components (`components.json`), any static string literal argument to a bare `require(cond, "...")` or `revert("...")` statement absent from the original S0 component snapshot is replaced with `"STR"` (**1,130** replacements across **893** files). Original S0 protocol strings, EIP-712 domain constants, selectors, and non-diagnostic literals remain untouched.
2. **Comment-erasure whitespace normalization.** Trailing spaces left by fixed-width comment blanking are stripped and consecutive blank lines are capped at one (**2,038** of **2,086** files normalized), with token-level verification that every Solidity code token remains identical.

Use `<lineage>/masked_inputs/` as model inputs for vulnerability detection evaluation and fine-tuning, and use the original `<lineage>/<historical_quality>/<case_id>/` projects for compilation and PoC execution. Each lineage's `masked_inputs/manifest.json` maps every masked file to its original source file and records `target_label` (`0` for protected `S1`, `1` for vulnerable `S2`). The paper's ODOS training experiment uses **298 dataset records** from a separately frozen pool under the diagnostic-masking policy.

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

## Incident references

The following incidents provide the vulnerability sources for the seven lineages. Each row links to the verified incident contract and the attack transaction referenced in the paper.

| Lineage | Chain | Incident entry | Incident contract | Attack evidence |
| --- | --- | --- | --- | --- |
| BCBC | BSC | `setReward` | [Contract source](https://bscscan.com/address/0xbCbCb0e7E28414e084c4a40C1cCC30B75629a7DE#code) | [Attack transaction](https://app.blocksec.com/explorer/tx/bsc/0xe15d6f7fa891c2626819209edf2d5ded6948310eaada067b400062aa022ce718) |
| ByToken | BSC | `triggerAutoBurn` | [Contract source](https://bscscan.com/address/0x6f50cffEcd4e00EcF7E442774C08c089450B62Ca#code) | [Attack transaction](https://bscscan.com/tx/0xe31c681eee764fb94b1b6bda3bbb0e4f25acb129c19040b9f58ad30541980979) |
| GPU | BSC | `transfer` | [Contract source](https://bscscan.com/address/0xf51CBf9F8E089Ca48e454EB79731037a405972ce#code) | [Attack transaction](https://app.blocksec.com/explorer/tx/bsc/0x2c0ada695a507d7a03f4f308f545c7db4847b2b2c82de79e702d655d8c95dadb) |
| Legendary | BSC | `cliamRewred` | [Contract source](https://bscscan.com/address/0x92D60629FF5d53a0098B51E9b1D59546D1D8e5B6#code) | [Attack transaction](https://bscscan.com/tx/0x15c835c070672948b3487d35254bce96831bec9f5212f78b04e683fed74bf4a2) |
| MURE | Ethereum | `distribute` | [Contract source](https://etherscan.io/address/0x365083717eFB17F3895290BA38f20F568C7A4D8a#code) | [Attack transaction](https://etherscan.io/tx/0xb83040361a0ec72fa2d06ad69493226518a5f8b5d96c19b400626248f9c5b798) |
| ODOS | Base | `isValidSigImpl` | [Contract source](https://basescan.org/address/0xB6333E994Fd02a9255E794C177EfBDEB1FE779C7#code) | [Attack transaction](https://basescan.org/tx/0xd10faa5b33ddb501b1dc6430896c966048271f2510ff9ed681dd6d510c5df9f6) |
| SorStaking | Ethereum | `withdraw` | [Contract source](https://etherscan.io/address/0x5d16b8Ba2a9a4ECA6126635a6FFbF05b52727d50#code) | [Attack transaction](https://app.blocksec.com/explorer/tx/eth/0x6439d63cc57fb68a32ea8ffd8f02496e8abad67292be94904c0b47a4d14ce90d) |

## Source and attribution

The source contract corpus comes from [Sourcify](https://docs.sourcify.dev/docs/repository/download-dataset/). LineageForge provides the constructed contracts, their corresponding PoCs, de-labeled model inputs, and dataset organization. Existing copyright and licence notices are retained in the source files. Licence declarations for the target source files are indexed in [catalog.json](catalog.json).
