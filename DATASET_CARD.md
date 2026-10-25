# LineageForge Dataset Card

## Dataset

This release provides **767 dataset records** constructed by LineageForge from vulnerabilities observed in real-world attacks. The records are organized into seven lineages, each derived from a selected real-world incident.

**We will continue to update this dataset.**

Each dataset record contains a **protected contract S1**, its corresponding **vulnerable contract S2**, executable **proof-of-concept (PoC) attacks**, and the execution environment. S2 is derived from S1 through targeted code changes. The corresponding PoCs execute equivalent attacker actions under relevant preconditions to check whether the intended exploit succeeds in S2 and is blocked in S1.

| Lineage | Records |
| --- | ---: |
| BCBC | 40 |
| ByToken | 52 |
| GPU | 39 |
| Legendary | 183 |
| MURE | 53 |
| ODOS | 344 |
| SorStaking | 56 |
| **Total** | **767** |

The paper reports **797 accepted dataset records** from the construction campaign. This standardized public release contains the 767 records listed above. The [README](README.md) provides downloads, the directory layout, and a usage example.

## Source processing

The source contracts come from the [Sourcify verified contract corpus](https://docs.sourcify.dev/docs/repository/download-dataset/). LineageForge identifies compatible contracts, constructs S1 and S2, and adapts the corresponding PoCs.

The [incident references](README.md#incident-references) list the source incident contracts and attack transactions for all seven lineages.

The executable archive uses the `comments_only_v2` policy. Ordinary Solidity comments are replaced with whitespace, preserving line breaks and character positions. Copyright and licence notices, compiler directives, strings, identifiers, conditions and assertions are retained. The cleaning reports record the transformations for each case.

## PoC execution records

The archive includes **1,534 recorded passing PoC executions** across all 767 dataset records. Standardization regenerated 472 executions and retained 1,062 existing execution records from byte-identical packages. Each PoC project includes `result.json` and `forge.log`. The case's `manifest.json` records the Forge version, test path, and file digests.

To reproduce a recorded execution, use the Forge version specified in `manifest.json` and retain the project's configuration and dependencies. Run the command stored in `result.json` or on the first line of `forge.log` from the corresponding `poc/s1/` or `poc/s2/` directory. Both test runs are expected to pass. The S2 test checks that the exploit succeeds, and the S1 test checks that it is blocked.

## Labels and catalog

The catalog records historical pipeline quality labels for **666 Strong**, **56 Acceptable**, and **45 Weak** records. These correspond to the directory names `strong_replay`, `acceptable_replay`, and `weak_replay`.

[catalog.json](catalog.json) contains one entry for each dataset record, including its lineage, case ID, relative path, historical quality label, execution provenance, and file metadata. Prior audit annotations for ten records are stored separately in `prior_audit_flag`.

## ODOS model inputs

`ODOS.zip` provides source inputs for detection and training at `odos_isvalidsigimpl_erc6492_side_effect_call/masked_inputs/`. This view covers all **344 ODOS records**. The `rewrite_diagnostics_v1` policy checks component snapshots to identify newly introduced `require` or `revert` diagnostic literals within verified rewritten components. Eligible literals equal to `side effects disabled` or `side effect failed` are replaced with `STR`.

This transformation changes 592 locations across 586 target files. Another 102 target files remain unchanged, giving 688 files in total. The masked-input manifest maps every masked source file to its original source file.

Use the masked sources as model inputs and the original PoC projects for execution. The recorded execution results belong to the original projects. The paper's ODOS training experiment uses **298 dataset records** from a separately frozen pool with the same masking policy.

## Token usage and cost

This construction campaign primarily used **DeepSeek V4 Flash 0423 (April version)**, with additional DeepSeek V4 Pro calls. The table reports total API usage and cost for the campaign.

| Metric | Total |
| --- | ---: |
| Tokens | **380,471,095** |
| API requests | **19,465** |
| Reported API cost | **¥239.82 CNY** |

We will update these usage and cost figures alongside future dataset updates.

## File metadata

**S0** denotes the source contract used for construction. The `components.json` file stores original source component snapshots alongside the corresponding code in S1 and S2. The `manifest.json` file records the files and execution configuration of each dataset record, and `cleaning_report.json` documents comment processing.

Existing copyright and licence notices are retained in the distributed source files. Licence declarations for the target source files are indexed in [catalog.json](catalog.json).
