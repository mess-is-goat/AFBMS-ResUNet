# Evaluation

The evaluators report region overlap, contour localization, and lesion detection
on the study's fixed held-out cases. Use the same case lists, scoring space,
probability threshold, and metric definitions for every comparison.

## Entry points

| Analysis | Script |
| --- | --- |
| BraTS staged configuration comparison | `scripts/eval_key_comparison.py` |
| UCSF-BMSR Baseline and Full | `scripts/eval_ucsf_baseline_full.py` |
| Training-set lesion size thresholds | `scripts/derive_train_lesion_strata.py` |
| ET lesion size analysis | `scripts/eval_et_lesion_stratified.py` |
| WT lesion size analysis | `scripts/eval_wt_lesion_stratified.py` |
| Five-model small and medium ET comparison | `scripts/eval_et_small_lesion_five_models.py` |
| Residual coefficient sensitivity | `scripts/eval_alpha_sensitivity.py` |
| Representative prediction figures | `scripts/select_boundary_typical_cases.py` |
| Prediction analysis across random seeds | `scripts/run_multiseed_typical_case_analysis.py` |

For the staged comparison, use the checkpoint directories produced by
`scripts/run_seed_stability.py`. Run the evaluator once per seed (42, 55, and 123):

```bash
python scripts/eval_key_comparison.py --seed 55 \
  --stability-root /path/to/stability --threshold 0.33 --no-cache
```

## Lesion matching

`evaluation/wt_lesion_stratified.py` implements 26-connected components,
one-to-one matching, lesion recall, and lesion Dice. Size thresholds are fitted
on training cases and then fixed for evaluation. GT-anchored lesion Dice assigns
zero to each missed GT lesion. Matched lesion Dice averages detected matches.
Lesion F1 uses the total TP, FP, and FN counts over the evaluation set.

The standard ET analysis uses a probability threshold of 0.33, a minimum component
size of 10 voxels, and the fixed training-derived small and medium intervals.
See [ET lesion evaluation](ET_SMALL_LESION_FIVE_MODELS.md) for the exact intervals
and checkpoint overrides.

See [UCSF training and evaluation](UCSF_EXTERNAL_BASELINE_FULL.md) for the
preparation, training, and evaluation commands for the second cohort.
