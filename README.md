# Stability-Verified Explainable ML for LUAD vs LUSC Classification

Code, processed data and results for the paper:

> **Stability-Verified Explainable Machine Learning for Lung Adenocarcinoma and Squamous Cell Carcinoma Classification, Biomarker Discovery and Prognostic Evaluation with Independent-Cohort Validation**
> 5th International Conference on Robotics, Electrical and Signal Processing Techniques (ICREST 2027), Dhaka, Bangladesh.

The supplementary material is in [`docs/supplementary.pdf`](docs/supplementary.pdf).

## Overview

- **Data:** TCGA-LUAD and TCGA-LUSC RNA-seq (UCSC Xena, 1,017 primary tumours) and the independent cohort GSE37745 (172 patients).
- **Models:** logistic regression, linear SVM, random forest, AdaBoost, XGBoost, LightGBM and a 1D-CNN, compared over repeated stratified 80/10/10 splits.
- **Explainability:** SHAP values with a stability check across resamples, giving a 15-gene core-stable set (KRT6A, KRT6B, SERPINB13, CALML3, S100A7, KRT5, KRT13, DSC3, KRT6C, DSG3, CLCA2, KRT14, GSTM1, SPRR2A, MUC5B).
- **Further analyses:** pathway enrichment, druggability, survival analysis, stage-confounding check and replication in GSE37745.

## Repository structure

```
.
├── notebooks/
│   ├── 01_final_luad_lusc_pipeline.ipynb       # Main TCGA pipeline
│   ├── 02_gse37745_full_pipeline.ipynb         # Same pipeline on GSE37745
│   ├── 03_external_validation_GSE37745.ipynb   # TCGA-trained model tested on GSE37745
│   ├── 04_baseline_comparison.ipynb            # Baseline comparison
│   ├── 05_stage_confounding_check.ipynb        # Stage confounding check
│   ├── 06_model_selection_validation.ipynb     # Validation-based model selection and statistical tests
│   └── 07_shap_stability_sensitivity.ipynb     # Sensitivity of the core-stable gene set
├── data/
│   ├── GSE37745_expression.csv
│   ├── GSE37745_clinical.csv
│   ├── TCGA_LUAD_sampleMap_LUAD_clinicalMatrix.xlsx
│   └── TCGA_LUSC_sampleMap_LUSC_clinicalMatrix.xlsx
├── results/
│   ├── final_pipeline/          # notebook 01
│   ├── gse37745_pipeline/       # notebook 02
│   ├── external_validation/     # notebook 03
│   ├── baseline_comparison/     # notebook 04
│   ├── stage_confounding/       # notebook 05
│   ├── model_selection_tcga/    # notebook 06 (TCGA)
│   ├── model_selection_gse/     # notebook 06 (GSE37745)
│   └── shap_sensitivity/        # notebook 07
├── docs/
│   └── supplementary.pdf
├── requirements.txt
└── LICENSE
```

The notebooks are saved with their outputs. They read input files from `data/` and write to `results/`, and should be run from the `notebooks/` folder.

## Data

The GSE37745 files and the TCGA clinical matrices are included. The two TCGA expression matrices are too large for GitHub and must be downloaded:

1. From the [UCSC Xena](https://xenabrowser.net/datapages/) TCGA hub, download the `HiSeqV2` gene expression file for **TCGA Lung Adenocarcinoma (LUAD)** and **TCGA Lung Squamous Cell Carcinoma (LUSC)**.
2. Convert each tab-separated file to CSV (genes in rows, samples in columns) and save it in `data/` as `LUAD.csv` and `LUSC.csv`:

```python
import pandas as pd
pd.read_csv("HiSeqV2_LUAD", sep="\t", index_col=0).to_csv("data/LUAD.csv")
pd.read_csv("HiSeqV2_LUSC", sep="\t", index_col=0).to_csv("data/LUSC.csv")
```

## How to run

```bash
pip install -r requirements.txt
jupyter notebook
```

Open the `notebooks/` folder and run the notebooks in numbered order. Notebooks 06 and 07 use the outputs of notebooks 01 and 02, so those two must be run first. Pathway enrichment in notebooks 01 and 02 needs an internet connection (Enrichr via `gseapy`).

Random seeds are fixed. The 1D-CNN uses TensorFlow, which is not fully deterministic, so its accuracy can differ slightly between runs; all other models reproduce exactly.

## Citation

If you use this code, please cite the paper above (full reference to be added once it appears in IEEE Xplore).

## License

The code is released under the MIT License. TCGA and GEO data are subject to their original terms of use.
