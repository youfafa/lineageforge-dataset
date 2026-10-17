# LineageForge Dataset

LineageForge is a dataset of **767 smart contract pairs across seven incident-derived vulnerability lineages**. Each pair contains a protected version **S1**, a vulnerable version **S2**, and corresponding proof-of-concept (PoC) projects with execution records. S1 and S2 form the protected and vulnerable counterparts for the selected mechanism.

## Downloads

Each ZIP contains one lineage. Extract the archives you need into the same directory as `catalog.json`.

| Lineage | Contract pairs | Archive |
| --- | ---: | --- |
| BCBC | 40 | [BCBC.zip](BCBC.zip) |
| ByToken | 52 | [ByToken.zip](ByToken.zip) |
| GPU | 39 | [GPU.zip](GPU.zip) |
| Legendary | 183 | [Legendary.zip](Legendary.zip) |
| MURE | 53 | [MURE.zip](MURE.zip) |
| ODOS | 344 | [ODOS.zip](ODOS.zip) |
| SorStaking | 56 | [SorStaking.zip](SorStaking.zip) |
| **Total** | **767** | |

## Contents and use

Original cases use the layout `<lineage>/<historical_quality>/<case_id>/`.

- `s1/` and `s2/` contain the paired contract projects.
- `poc/s1/` and `poc/s2/` contain the corresponding PoC projects, configuration and execution records.
- `components.json` identifies rewritten components and includes S0 component snapshots.
- `manifest.json` and `cleaning_report.json` record file provenance and preprocessing.

To reproduce a recorded test, install Foundry and enter the relevant `poc/s1/` or `poc/s2/` directory. Run the command recorded in its `result.json` or the first line of `forge.log`. Use the package's configuration and the Forge version recorded in its case manifest.

[DATASET_CARD.md](DATASET_CARD.md) describes preprocessing and execution records. [catalog.json](catalog.json) indexes the 767 cases by lineage, case ID and directory, with historical pipeline labels and existing audit annotations.

## ODOS masked inputs

`ODOS.zip` also includes `odos_isvalidsigimpl_erc6492_side_effect_call/masked_inputs/`. This optional model-input view contains all 344 ODOS pairs and replaces two verified, newly introduced diagnostic strings with `STR`. Its README and manifest describe the rule and map each file to its original counterpart.

Use the masked view for detection and training inputs, and the original projects for PoC execution. Both views share the same case IDs and represent the same 344 pairs.

## Source and attribution

The source contract corpus comes from [Sourcify](https://docs.sourcify.dev/docs/repository/download-dataset/). LineageForge provides the constructed contract variants, paired PoCs and dataset organization. Existing copyright and licence notices are retained in the source files.
