# AFBMS-ResUNet

Code for **AFBMS-ResUNet: Boundary-aware multiscale representation learning for
brain tumor segmentation in multimodal MRI**.

AFBMS-ResUNet combines local high-frequency feature fusion at four decoder stages
(LHFC), adaptive residual multiscale context at the bottleneck (AR-MSC V2), and
auxiliary boundary supervision (ABS). The model uses 24 base channels and a
boundary loss weight of 0.1. The auxiliary boundary head is used during training.
BraTS 2020 models predict WT, TC, and ET. UCSF-BMSR models predict a binary
brain-metastasis mask after retraining on that cohort.

## Data

| Dataset | Training | Validation | Held-out test | Split |
| --- | --- | --- | --- | --- |
| BraTS 2020 | 257 cases | 74 cases | 37 cases | Fixed split from `tumourCSV.csv`, `random_state=10` |
| UCSF-BMSR | 321 scans | 94 scans | 46 scans | Patient-grouped split generated during preparation |

The BraTS results use the project's held-out subset of the labeled challenge
training data. Download the MRI data from the
[BraTS 2020 Kaggle mirror](https://www.kaggle.com/datasets/awsaf49/brats2020-training-data)
and the [UCSF imaging portal](https://imagingdatasets.ucsf.edu/dataset/1), following
each provider's access conditions. UCSF-BMSR uses a noncommercial data license.

The `path` column in `tumourCSV.csv` contains the original AutoDL locations.
Remap those paths to your data while preserving case IDs and row order.
The prepared UCSF manifest records the patient groups and scan assignments.

## Installation

```bash
git clone https://github.com/mess-is-goat/AFBMS-ResUNet.git
cd AFBMS-ResUNet
python -m pip install -r requirements.txt
```

The original project environment uses Python 3.10 and PyTorch 2.1.2.
For an existing CUDA environment, retain a compatible PyTorch installation and
install the remaining dependencies listed in the requirements file.
Additional dependency files cover UCSF and the DoubleBlock-ViT architecture baseline.

## Training

The staged training protocol first trains a ResUNet BCE-Dice baseline and then
initializes Full from the matching seed's best baseline checkpoint. For seed 55,
replace the output paths and checkpoint filename in these commands.

```bash
python scripts/train_baseline_bcedice.py \
  --seed 55 --checkpoint_dir /path/to/seed55/baseline

python scripts/train_hf_concat_boundary.py \
  --seed 55 --fusion concat --boundary_weight 0.1 \
  --multiscale_context_v2 \
  --baseline_checkpoint /path/to/seed55/baseline/best_model_N.pth \
  --checkpoint_dir /path/to/seed55/full
```

Seeds 42, 55, and 123 support the repeated BraTS experiments. Each Full run
uses the matching seed's baseline checkpoint and validation-loss early stopping.

The [UCSF workflow](docs/UCSF_EXTERNAL_BASELINE_FULL.md) covers preprocessing,
patient grouping, Baseline/Full training, and evaluation after cohort-specific
retraining. Scripts expose path and checkpoint arguments through `--help`.

## Evaluation and baselines

[Evaluation instructions](docs/EVALUATION.md) list the region, boundary, lesion,
and residual-coefficient analyses. Preserve the metric profile and fixed test
cases when comparing results. The UCSF evaluator reports case-level overlap,
surface distance, and lesion-level detection and overlap.

| Baseline | Instructions |
| --- | --- |
| U-Net, ResUNet, Attention U-Net, and historical InstanceNorm U-Net | Model definitions in `models/`. The [ET lesion workflow](docs/ET_SMALL_LESION_FIVE_MODELS.md) documents the corresponding checkpoint inputs. |
| DoubleBlock-ViT | [Pinned source and training](docs/PAPER_BASELINES.md) |

The historical InstanceNorm U-Net is implemented by the existing `nnUNet3d`
class, which preserves the architecture and state dictionaries used for the
original comparison.

## Repository layout

- `models/`, `losses/`, `training/`, `data/` contain model and training components.
- `evaluation/` contains region, contour, and lesion metrics.
- `scripts/` contains training, evaluation, and prediction-analysis entry points.
- `docs/` contains experiment-specific commands and settings.
- `tests/` checks model connections, data splits, metric definitions, and resume behavior.
- `third_party/paper_baselines/` contains pinned baseline sources and their licenses.

## Tests

```bash
python -m pip install pytest
python -m pytest tests -q
```

## License

Original project code is distributed under the [MIT License](LICENSE).
The bundled DoubleBlock-ViT source retains its upstream MIT license. Its revision
and checksum are listed in
[third-party source details](third_party/paper_baselines/README.md).
