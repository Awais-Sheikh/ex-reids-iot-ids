# Resource-efficient and explainable IoT intrusion detection on CICIoT2023

Result tables and figures for the manuscript *"Resource-efficient and explainable IoT intrusion detection: attack-family evaluation on CICIoT2023"* (draft, not peer reviewed).

Author: Awais (ORCID: 0009-0009-4503-4451)

## Contents

- `EX_REIDS_01.ipynb`: analysis notebook (run on Kaggle). **Add this file to the repository** (Kaggle: File -> Download Notebook).
- `results/`: CSV tables produced by the notebook.
- `figures/`: SHAP figures and ROC/precision-recall figure.
- `requirements.txt`: package versions reported by the notebook environment.

Dataset files and trained model files are not included.

## Dataset

CICIoT2023 (Canadian Institute for Cybersecurity, University of New Brunswick), flow-level CSV files:
https://www.unb.ca/cic/datasets/iotdataset-2023.html

The notebook was run on a public Kaggle mirror of these CSV files ("UNB CIC IOT 2023 Dataset"). Please check the provider's terms before using or redistributing the data.

Reference: Neto, E. C. P., et al. (2023). CICIoT2023: A real-time dataset and benchmark for large-scale attacks in IoT environment. *Sensors*, 23(13), 5941. https://doi.org/10.3390/s23135941

## Setup

- Sample: every 8th CSV part file (22 of 169), then a random 20% of rows of each file (seed 42). After cleaning: 1,130,477 flows (97.63% attack, 2.37% benign).
- Binary label: benign = 0, any attack = 1. Attack families: DDoS, DoS, Mirai, reconnaissance (Recon-* and VulnerabilityScan), spoofing (MITM-ArpSpoofing, DNS_Spoofing), brute force (DictionaryBruteForce), web-based (SqlInjection, XSS, CommandInjection, BrowserHijacking, Backdoor_Malware, Uploading_Attack).
- Stratified split 70/15/15 (seed 42). Scaling, feature ranking and tuning use training/validation data only.
- Models: Logistic Regression, Random Forest, XGBoost (full: 46 features, 200 trees, depth 6); lightweight XGBoost (top-20 features, 50 trees, depth 6) selected on validation data.

## Software versions

Python 3.13.15, scikit-learn 1.6.1, XGBoost 3.4.1, SHAP 0.52.0, pandas 2.3.3, NumPy 2.1.3 (as reported by the Kaggle notebook environment).

## Result files

| File | Content |
|---|---|
| `baseline_val.csv` | Baselines on validation data |
| `feature_budget_val.csv` | Top-10/20/30/46 feature budgets (validation) |
| `lightweight_val.csv` | Sweep over trees and depth (validation) |
| `final_test.csv` | Final test-set results (seed 42) |
| `bootstrap_ci.csv` | 95% bootstrap intervals (test) |
| `fixed_far.csv` | Recall at validation-calibrated false-alarm targets |
| `latency_memory.csv` | Single-flow latency and process memory probe (the `peak_rss` column is not valid and is not used in the paper) |
| `leave_one_family_out.csv` | Single seed-42 hold-out run, full model |
| `holdout_5seeds.csv`, `seen_recall_5seeds.csv` | Family-level experiments over five seeds, full and lightweight model |
| `rst_ablation_val.csv` | Ablation of rst_count (validation) |
| `seeds_test.csv` | Five-seed aggregate comparison (values transcribed from the notebook output) |
| `*_idx.csv`, `xgb_importance_train.csv` | Split indices and training-set feature importance (seed 42) |

## Main results (test set, seed 42)

| Model | MCC | FPR |
|---|---|---|
| Logistic Regression | 0.7072 | 0.45% |
| Random Forest | 0.9347 | 5.53% |
| XGBoost (full) | 0.8868 | 0.27% |
| XGBoost (light) | 0.8536 | 0.05% |

Lightweight vs full model: file size 0.147 vs 0.482 MB; single-flow median latency 1.80 vs 3.15 ms (Python interface, one CPU thread, shared CPU). Over five seeds, MCC was 0.8898 +/- 0.0018 (full) and 0.8587 +/- 0.0021 (light). Aggregate recall hides weak detection of spoofing, reconnaissance, web-based and brute-force attacks, especially for the lightweight model (see manuscript).

## Reproducing

1. Create a Kaggle notebook and attach the CICIoT2023 CSV dataset.
2. Run the notebook cells from top to bottom.
3. Outputs are written to `/kaggle/working/results` and `/kaggle/working/figures`.

Re-running the pipeline after a session restart reproduced the split sizes, feature importances and lightweight-model test metrics exactly.

## Limitations

Single dataset, random flow-level split, a sampled subset of the data, several experiments from a single seed, timing on a shared CPU (not edge hardware). See the manuscript.

## Citation

Please cite the manuscript once published. [Add citation/DOI here.]
