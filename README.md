[README.md](https://github.com/user-attachments/files/27954291/README.md)
# Predicting Superconducting Critical Temperature with Machine Learning

## Motivation

In 2026, severe flooding hit Dagestan, Russia. Local officials attributed part of the infrastructure damage to degraded electrical wiring — cables that wear out, overheat, and fail under stress.

That event made me ask a question: *what if electrical grids didn't lose energy at all?*

Superconductors — materials that conduct electricity with zero resistance — could be the answer. They don't overheat, they don't degrade the same way, and they could make power infrastructure dramatically more reliable. The problem is that most superconductors only work at extremely low temperatures, making them impractical for real-world use.

Finding materials with a **high critical temperature (Tc)** — the temperature below which superconductivity kicks in — is one of the most important open problems in modern physics and materials science. This project uses machine learning to predict Tc from material descriptors, as a first step toward building a screening pipeline for promising superconducting candidates.

---

## Dataset

The project uses a real-world superconductivity dataset containing engineered numerical descriptors (atomic mass, electron affinity, valence, etc.) and the target variable `critical_temp` (in Kelvin).

- **21,263 samples**
- **81 engineered features**
- Source: UCI Machine Learning Repository — Superconductivity Data

---

## Models Tested

| Model | Notes |
|---|---|
| Linear Regression | Baseline |
| Decision Tree Regressor | Nonlinear, interpretable |
| Random Forest Regressor | Best overall performance |
| Gradient Boosting Regressor | Strong ensemble method |
| XGBoost Regressor | Optimized boosting |
| MLP (Deep Learning) | 3-layer PyTorch neural network |

---

## Results

All six models were evaluated on a held-out 20% test set and with 5-fold cross-validation
(sorted by cross-validated RMSE, lower is better):

| Model | MAE | RMSE | R² | CV-RMSE | CV-STD |
|---|---|---|---|---|---|
| **Random Forest** | 5.08 | **8.97** | **0.930** | **9.70** | 0.25 |
| XGBoost | 5.81 | 9.28 | 0.925 | 9.88 | 0.28 |
| MLP (Deep Learning) | 7.80 | 11.86 | 0.878 | 12.11 | 0.20 |
| Decision Tree | 6.09 | 11.81 | 0.879 | 12.54 | 0.30 |
| Gradient Boosting | 8.50 | 12.33 | 0.868 | 12.79 | 0.15 |
| Linear Regression | 13.21 | 17.38 | 0.738 | 17.70 | 0.25 |

**Random Forest** achieved the best performance (test R² ≈ 0.93, RMSE ≈ 9.0 K), narrowly ahead of
XGBoost. Notably, the deep neural network landed in the middle of the field — a reminder that
gradient-boosted and bagged **tree ensembles remain strong baselines for tabular data**. The large gap
over Linear Regression confirms that the relationship between material descriptors and Tc is **strongly
nonlinear**.

> **Note on data leakage (important).** The 21,263 measurements span only **15,542 distinct chemical
> formulas**, so a random split shares compositions between train and test. Under a rigorous
> **composition-grouped split** (no formula shared across partitions, using `GroupShuffleSplit` /
> `GroupKFold` on the formulas in `unique_m.csv`), Random Forest drops only slightly to **R² ≈ 0.913**
> and the model ranking is preserved — evidence that the models generalize to genuinely unseen
> compositions. The grouped figure is the honest, leakage-controlled estimate.

---

## Additional Analysis

- **Feature importance** — both impurity-based and **permutation** importance agree that
  `range_ThermalConductivity` dominates, followed by atomic-mass-, valence- and density-related descriptors
- **Correlation analysis** — thermal-conductivity-, radius- and valence-related descriptors correlate most
  strongly with Tc (Pearson r from −0.63 to +0.72)
- **Target distribution visualization** — most superconductors cluster at low Tc, with a long right tail
  (up to 185 K)
- **PCA-based feature-space visualization** — 2 components explain ≈ 49% of variance and show
  Tc-related structure

---

## Key Takeaway

ML can meaningfully predict superconducting critical temperature from material descriptors. This kind of model could serve as an early-stage screening tool — quickly filtering thousands of candidate materials before expensive laboratory synthesis. Combined with future quantum simulation methods, this approach may accelerate the discovery of room-temperature superconductors.

---

## What I Learned

- Ensemble methods handle nonlinear physical relationships much better than linear models
- Feature engineering matters enormously — raw composition alone is not enough
- Real scientific datasets are messy and require careful preprocessing
- Framing a physical problem as an ML task requires understanding both domains

---

## Future Work

- Use composition-grouped / deduplicated splitting to obtain leakage-free performance estimates
- Add Graph Neural Networks for molecular structure
- Explore quantum ML approaches for feature encoding
- Incorporate crystal structure data for richer representations
- Systematic hyperparameter tuning and prediction uncertainty estimation

---

## Files

- `predicting_critical_temperature_ml.ipynb` — main project notebook (with executed outputs)
- `paper.tex` / `paper.pdf` — LaTeX source and compiled paper
- `APPLICATION.md` — abstract, summary, and contribution statement for program/competition applications
- `train.csv.zip` — descriptor dataset (UCI Superconductivity Data)
- `unique_m.csv.zip` — chemical formulas, aligned 1:1 with `train.csv` (used for the leakage-free split)

---

*An independent research project on the intersection of physics and machine learning.*
# Predicting-Superconducting-Critical-Temperature-with-Machine-Learning
