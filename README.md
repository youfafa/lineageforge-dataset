# LineageForge Dataset

> Smart contract vulnerabilities derived from real-world attacks, with protected and vulnerable contracts, executable PoCs, and execution environments.

[Overview](#overview) · [Downloads](#downloads) · [Getting started](#getting-started) · [Dataset card](DATASET_CARD.md) · [Catalog](catalog.json)

| Dataset records | Incident-derived lineages | Recorded passing PoC executions |
| :---: | :---: | :---: |
| **767** | **7** | **1,534** |

## Overview

LineageForge is a framework for constructing verifiable smart contract vulnerability datasets at scale from vulnerabilities observed in real-world attacks. This release provides **767 dataset records** organized into seven lineages, each derived from a selected real-world incident.

Each dataset record contains a **protected contract S1**, its corresponding **vulnerable contract S2**, executable **proof-of-concept (PoC) attacks**, and the execution environment. S2 is derived from S1 through targeted code changes. The corresponding PoCs execute equivalent attacker actions under relevant preconditions to check whether the intended exploit succeeds in S2 and is blocked in S1.

| Contract | Role | Intended attack outcome |
| --- | --- | --- |
| **S1 — Protected contract** | Provides protection against the selected vulnerability | The intended exploit is blocked |
| **S2 — Vulnerable contract** | Is derived from S1 through targeted code changes | The intended exploit succeeds |

### How the records are constructed

1. **Prepare the lineage specification.** Incident evidence, local reference contracts, and their PoCs guide the requirements for discovery, permitted code changes, and expected attack outcomes.
2. **Discover compatible contracts.** Incident-Guided Discovery (IGD) identifies source-verified contracts with the required business logic and collects source evidence to guide construction.
3. **Construct and validate the contracts.** Evidence-Grounded Contract Construction (EGCC) guides large language models to construct S1 and S2, adapt their PoCs, and check the contracts and attack outcomes through compilation, execution, and source and PoC review.

The paper reports **797 accepted dataset records** from the construction campaign. This standardized public release contains **767 records**, indexed in [catalog.json](catalog.json).

## Downloads

Each ZIP contains one lineage. Download the archives you need together with [catalog.json](catalog.json), then extract them beside the catalog while retaining the directory structure.

| Lineage | Selected vulnerability | Records | Archive |
| --- | --- | ---: | --- |
| BCBC | Missing authorization for token minting or reward operations | 40 | [BCBC.zip](BCBC.zip) |
| ByToken | Unauthorized token burning | 52 | [ByToken.zip](ByToken.zip) |
| GPU | Balance inflation through self-transfer | 39 | [GPU.zip](GPU.zip) |
| Legendary | Signature bypass through zero-address recovery | 183 | [Legendary.zip](Legendary.zip) |
| MURE | Signature validation with an attacker-selected signer | 53 | [MURE.zip](MURE.zip) |
| ODOS | Side-effecting calls during signature validation | 344 | [ODOS.zip](ODOS.zip) |
| SorStaking | Repeated withdrawal of rewards that remain claimable | 56 | [SorStaking.zip](SorStaking.zip) |
| **Total** | **7 lineages** | **767** | |

### Inside a record

The `path` field in the catalog locates each record after extraction. Archive directories use the full lineage identifiers rather than the short names in the download table.

```text
<lineage>/<historical_quality>/<case_id>/
├── s1/                    Protected contract source files
├── s2/                    Vulnerable contract source files
├── poc/
│   ├── s1/                Protected-side PoC project and execution records
│   └── s2/                Vulnerable-side PoC project and execution records
├── components.json        Rewritten components and original S0 snapshots
├── manifest.json          File digests, test paths, and execution configuration
└── cleaning_report.json   Source preprocessing records
```

**S0** denotes the source contract used for construction. The `components.json` file stores original source component snapshots alongside the corresponding code in S1 and S2. The [dataset card](DATASET_CARD.md) describes source preprocessing and execution records.

## Getting started

1. **Select a record.** Use `lineage`, `case_id`, and `path` in [catalog.json](catalog.json) to find a case in an extracted archive.
2. **Inspect the contracts.** Compare `s1/` and `s2/`, using `components.json` to locate the rewritten components and their original source snapshots.
3. **Prepare the execution environment.** Install Foundry with the Forge version recorded in the case's `manifest.json`. Retain the PoC project's configuration and dependencies.
4. **Run both PoCs.** Enter `poc/s1/` and run the command stored in its `result.json` or on the first line of `forge.log`. Repeat with the command recorded in `poc/s2/`.

For example, run the following command from each PoC directory of the BCBC record `c_0015dd5ee1ea`.

```sh
forge test --root . --match-path test/PocOracle.t.sol -vvvv
```

**Both test runs are expected to pass.** The S2 test checks that the exploit succeeds, and the S1 test checks that it is blocked. The assertions and traces in `forge.log` show the behavior checked by each PoC.

### Execution records and quality labels

The archive includes **1,534 recorded passing PoC executions** across all 767 dataset records. Standardization regenerated 472 executions and retained 1,062 existing execution records from byte-identical packages.

The catalog records historical pipeline quality labels for **666 Strong**, **56 Acceptable**, and **45 Weak** records. These correspond to the directory names `strong_replay`, `acceptable_replay`, and `weak_replay`. Prior audit annotations for ten records are stored separately in `prior_audit_flag`.

## ODOS model inputs

`ODOS.zip` also provides source inputs for detection and training at `odos_isvalidsigimpl_erc6492_side_effect_call/masked_inputs/`. This view covers all **344 ODOS records** and replaces eligible occurrences of two diagnostic strings introduced during construction, `side effects disabled` and `side effect failed`, with `STR`. Its README documents the masking rule, and its manifest maps the files to their original counterparts.

Use the masked sources as model inputs and the original PoC projects for execution. The recorded execution results belong to the original projects. The paper's ODOS training experiment uses **298 dataset records** from a separately frozen pool with the same masking policy.

## Source and attribution

The source contract corpus comes from [Sourcify](https://docs.sourcify.dev/docs/repository/download-dataset/). LineageForge provides the constructed contracts, their corresponding PoCs, and dataset organization. Existing copyright and licence notices are retained in the source files. Licence declarations for the target source files are indexed in [catalog.json](catalog.json).
