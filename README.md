# ⚡ Electrical Faults Analysis & Classification

End-to-end pipeline to **detect** (binary) and **classify** (multi-class) electrical power system faults from **three-phase current (Ia, Ib, Ic)** and **voltage (Va, Vb, Vc)** signals.

The script performs:
- Data loading & sanity checks (nulls, shapes, class counts)
- Exploratory visuals (distributions, waveforms, correlations)
- Feature engineering (zero-sequence components, magnitudes, imbalance indices)
- Model training & evaluation for **detection** and **classification**
- Cross-validation, ROC-AUC, confusion matrices, and comparison plots
- Local explainability using **LIME**

---

## Repository Layout

```text
.
├─ data/
│  └─ .gitkeep                     # Keep folder in Git; put your CSVs here
├─ Electrical_faults_analysis_&_classification.py
├─ requirements.txt
├─ LICENSE
└─ README.md
```

> If your script still uses Colab-style paths (`/content/...`), change them to:
> - Detection CSV → `data/detect_dataset.csv`  
> - Classification CSV → `data/classData.csv`  
> - Saved figures → e.g. `outputs/...` 

---

## Quickstart

### 1) Environment
```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate

pip install --upgrade pip
pip install -r requirements.txt
```

### 2) Data
Place your CSVs at:
```text
data/detect_dataset.csv      # for binary detection
data/classData.csv           # for multi-class classification
```

**Required columns**

- `detect_dataset.csv`:  
  `Ia, Ib, Ic, Va, Vb, Vc, Output (S)`  
  where `Output (S)` ∈ {0 (no fault), 1 (fault)}

- `classData.csv`:  
  `Ia, Ib, Ic, Va, Vb, Vc, A, B, C, G`  
  where `A, B, C, G` are 0/1 indicators used to derive `faultType`.

Make sure both CSVs are numeric and free of malformed rows.

---

## Run

```bash
python "Electrical_faults_analysis_&_classification.py"
```

The script will:
- Print dataset info and class distributions
- Generate bar charts, waveforms, correlation heatmaps
- Train multiple models for detection & classification
- Print classification reports
- Plot confusion matrices and ROC curves
-  Produce LIME local explanations

> If you add an `outputs/` directory and update the save paths in the code, figures/tables can be exported instead of only being shown.

---

## Fault Label Mapping

`faultType` is derived from phase indicators **A, B, C** and ground **G**:

| Pattern | Meaning |
|---|---|
| `G=0` & `A=B=C=0` | **No Fault** |
| `G=1` & exactly one of A/B/C = 1 | **LG fault (Phase–G)** e.g., A-G, B-G, C-G |
| `G=0` & exactly two of A/B/C = 1 | **LL fault (Phase–Phase)** e.g., A-B, A-C, B-C |
| `G=1` & exactly two of A/B/C = 1 | **LLG fault (Phase–Phase–G)** e.g., A-B-G |
| `G=0` & `A=B=C=1` | **LLL Fault** |
| `G=1` & `A=B=C=1` | **LLLG fault** |

---

## Models & Training

### Detection (binary)
- **RandomForest** (with `StandardScaler` in a `Pipeline`)
- Baselines for comparison: **LogisticRegression**, **SVM (RBF)**, **GradientBoosting**, **KNN**, **DecisionTree**

### Classification (multi-class)
- **RandomForest** (class weight balancing)
- Extended comparison includes **ExtraTrees**, **MLP**, etc.

### Engineered Features (for classification)
- Zero-sequence: `I0 = (Ia+Ib+Ic)/3`, `V0 = (Va+Vb+Vc)/3`
- Magnitudes: `I_mag`, `V_mag`
- Imbalance indices: `I_std`, `V_std`

### Metrics
- **Accuracy**, **Precision**, **Recall**, **F1 (macro & weighted)**
- **ROC-AUC** (binary and one-vs-rest for multi-class)
- **Confusion Matrix**
- **Stratified K-Fold** cross-validation (default 5 folds)

---

## Visualization (examples)

- Class distribution bars (e.g., `Output (S)` counts)
- Fault type counts
- **Waveforms**: Ia/Ib/Ic and Va/Vb/Vc
- **Correlation heatmaps** (numeric features)
- **Model comparison**: Accuracy & Macro-F1 bar charts
- **ROC curves** per class (multi-class one-vs-rest)
- **LIME** local feature importance (optional)

---

## Reproducibility

- Fixed random seeds (`random_state=1` or `SEED=42` in places)
- **Stratified** splits for balanced evaluation
- Deterministic scikit-learn where applicable

---

## Troubleshooting

**FileNotFoundError (CSV paths)**  
Update hardcoded paths from `/content/...` to `data/...`. Ensure files exist.

**Matplotlib/Seaborn showing but not saving**  
Create an `outputs/` folder and replace `plt.show()` with `plt.savefig("outputs/your_plot.png", dpi=300, bbox_inches="tight")` where desired.





## Citation

If you use this repository, please cite:

```bibtex
@misc{electrical-faults-analysis-classification,
  title  = {Electrical Faults Analysis & Classification},
  author = {Sourav Datto},
  year   = {2025},
  url    = {https://github.com/duttosourav8/Electrical-Faults-Analysis-Classification}
}
```



Built with **NumPy**, **Pandas**, **Matplotlib**, **Seaborn**, **scikit-learn**, and **LIME/SHAP** for explainability.

