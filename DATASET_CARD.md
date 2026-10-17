# LineageForge Dataset Card

## Dataset

LineageForge contains 767 constructed contract pairs across seven vulnerability lineages derived from public security incidents. Each record includes S1 and S2 source projects, paired PoC projects, configuration files and execution records. S0 component snapshots in `components.json` show the source components used during construction.

| Lineage | Pairs |
| --- | ---: |
| BCBC | 40 |
| ByToken | 52 |
| GPU | 39 |
| Legendary | 183 |
| MURE | 53 |
| ODOS | 344 |
| SorStaking | 56 |
| **Total** | **767** |

## Source processing

Contract substrates come from the [Sourcify verified contract corpus](https://docs.sourcify.dev/docs/repository/download-dataset/). LineageForge constructs the protected and vulnerable variants and their corresponding PoCs.

The executable archive uses the `comments_only_v2` policy. Ordinary Solidity comments are replaced with whitespace, preserving line breaks and character positions. Copyright and licence notices, compiler directives, strings, identifiers, conditions and assertions are retained. The cleaning reports record the transformations for each case.

## PoC execution records

All 767 pairs have execution records for both sides, covering 1,534 passing side executions. Standardization regenerated 472 side executions and retained 1,062 execution records from byte-identical packages. Each PoC project includes `result.json` and `forge.log`. Its case manifest records the Forge version, test path and file digests.

For reproduction, run the recorded command from the relevant `poc/s1/` or `poc/s2/` directory using the package configuration.

## Labels and catalog

S1 is the protected counterpart for the selected mechanism, and S2 is the vulnerable counterpart. The historical pipeline grades comprise 666 strong, 56 acceptable and 45 weak records. The existing directory names retain these grades.

`catalog.json` contains one record per pair. It stores the lineage, case ID, relative path, historical grade, execution provenance and file metadata. Ten records carry prior audit annotations concerning attack conditions, setup or mechanism checks. These annotations are retained in `prior_audit_flag` alongside the corresponding case IDs.

## ODOS masked inputs

The ODOS archive includes an alternative model-input view of its 344 pairs. The `rewrite_diagnostics_v1` policy checks component snapshots and replaces newly introduced `require` or `revert` diagnostic literals equal to `side effects disabled` or `side effect failed` with `STR`.

This transformation changes 592 locations across 586 target files. Another 102 target files remain unchanged, giving 688 files in total. The masked-input manifest maps every file to its original counterpart. Original projects provide the executable PoCs, while masked sources provide detection and training inputs. The paper's RQ3 ODOS experiment uses a separately frozen pool of 298 pairs with the same masking policy.

## File metadata

`manifest.json` records the files and execution configuration of each case. `components.json` maps the rewritten components across S0, S1 and S2. `cleaning_report.json` records comment processing. Source-level copyright and licence declarations are preserved in the distributed files and indexed in the catalog.
