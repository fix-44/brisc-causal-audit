# Causal occlusion audit of brain-tumour MRI classifiers (BRISC 2025)

Code and result tables for the paper:

> **Octavian.** *Do brain-tumour MRI classifiers look at the tumour? A validated causal occlusion audit on BRISC 2025.*
> Manuscript under review.

Convolutional classifiers reach ~98–99% test accuracy on the BRISC 2025 four-class brain MRI dataset
(no tumour, glioma, meningioma, pituitary). This repository tests whether those predictions actually depend on the
tumour. It contains:

- a **dataset audit** of BRISC 2025: image-source confounds, a metadata-only classifier, pixel-confirmed duplicates
  between the official train and test splits, and linear probes showing how much class information lies outside the lesion;
- an **area-matched three-arm causal occlusion audit** with shape-matched null distributions, run with five explanation
  maps (spectral, spectral subspace, CAM, Eigen-CAM, Integrated Gradients) and two map-free arms (background, oracle);
- a **synthetic ground-truth validation** of the audit with three shortcut types (full corner marker, partial corner
  marker, arrow inside the head), each with three seeds and clean negative controls;
- the audit of nine clean models (ResNet50, DenseNet121, EfficientNet-B0 × 3 seeds) with each of the five maps, plus
  sensitivity analyses over occlusion budget, fill method and duplicate removal.

## Repository structure

```
brisc-causal-audit/
├── brisc_audit.ipynb          # the complete pipeline (Kaggle notebook); produces every table and figure
├── requirements.txt           # Python dependencies
├── LICENSE                    # MIT licence (code)
├── README.md
└── results/                   # outputs of the run reported in the paper
    ├── config.json            # all parameters of that run
    ├── split.json             # train / validation / test file lists
    ├── tables/                # CSV tables (T0–T8, per-image properties, all audit statistics)
    └── figures/               # PDF figures
```

## Data

The BRISC 2025 images are **not** redistributed here. Download the dataset from its official sources and cite it:

- Kaggle: https://www.kaggle.com/datasets/briscdataset/brisc2025 (the run reported here used Kaggle dataset version ID 13613832)
- Paper: *BRISC: Annotated Dataset for Brain Tumor Segmentation and Classification*, arXiv:2506.14318

`results/split.json` lists the exact training, validation and test files used.

## Reproducing the results

The notebook is designed for Kaggle:

1. Create a new Kaggle notebook and import `brisc_audit.ipynb`.
2. Add the BRISC 2025 dataset as input (the path is detected automatically).
3. Settings: **Accelerator → GPU T4**, **Internet → On** (ImageNet weights are downloaded).
4. Optional: set `QUICK_TEST = True` for a ~20-minute pipeline check on a small subset.
5. Set `QUICK_TEST = False` and run **Save Version → Save & Run All**. A full run takes about 4.5 hours on one T4 GPU.

Outputs are written to `/kaggle/working/brisc_audit/` and archived as `brisc_audit_results.zip`.
To run elsewhere, set `BASE_DIR` and `OUT_DIR` in the first code cell and install `requirements.txt`.

Seeds are fixed and deterministic algorithms are enabled where available; small numerical differences can still
occur across GPU types and library versions.

## Result tables

| File | Content |
|---|---|
| `T0a_source_properties.csv` | image-source properties per class (original size, file mode, background) |
| `T0b_metadata_probe.csv` | metadata-only random forest (never sees brain pixels) |
| `T0c_duplicate_pairs.csv` | all duplicate candidates with pixel differences; `confirmed = True` are duplicates |
| `T0d_duplicates_by_class.csv` | duplicates removed per class in the deduplicated test set |
| `per_image_properties.csv` | per-image properties and duplicate status |
| `T1_accuracy.csv` | test accuracy of clean models (all / deduplicated), mean ± SD over seeds |
| `T2_spectral.csv` | spectral estimates and fidelity controls |
| `T3_verdicts_primary.csv` | primary audit verdicts (deduplicated test set, 2× budget, nconv fill) |
| `T3_verdicts_dedup_b1.csv`, `T3_verdicts_all_b2.csv` | sensitivity analyses |
| `T3b_effect_sizes.csv`, `T3c_background_arm.csv` | effect sizes per arm; background arm |
| `T4_synthetic_detail.csv`, `T4_synthetic_detection.csv`, `T4c_synthetic_effects.csv` | synthetic ground-truth validation |
| `T5_fill_sensitivity.csv` | verdicts under three fill methods |
| `T6_behavior.csv` | behaviour of shortcut and clean models on clean / marked test sets |
| `T7_tumour_erasure.csv` | correct predictions retained after erasing the tumour |
| `T8_fingerprint_probes.csv` | class information outside the lesion (linear probes on frozen features) |
| `all_audit_summaries.csv` | complete statistics of every audit (supplementary) |

## Citation

If you use this code or the duplicate list, please cite the paper (citation will be added on publication) and the
BRISC 2025 dataset.

## Licence

Code: MIT (see `LICENSE`). Result tables: CC BY 4.0. The BRISC 2025 images are subject to their own licence.

## Contact

Octavian — Information Systems, Universitas Hasanuddin, Makassar, Indonesia —
octavian@unhas.ac.id — ORCID [0009-0005-9893-3523](https://orcid.org/0009-0005-9893-3523)
