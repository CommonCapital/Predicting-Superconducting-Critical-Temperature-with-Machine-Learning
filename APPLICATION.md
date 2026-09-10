# Application Materials

Supporting text for submitting *"Predicting Superconducting Critical Temperature from Engineered
Material Descriptors: A Comparative Machine Learning Study"* to research programs and competitions.
Adapt lengths to each venue's limits. All numbers below match `paper.pdf` and the executed notebook.

---

## Title

Predicting Superconducting Critical Temperature from Engineered Material Descriptors:
A Comparative Machine Learning Study

---

## Abstract (~200 words)

Superconductors carry electric current with zero resistance below a critical temperature (Tc), but most
work only at impractically low temperatures, so finding higher-Tc materials is a central problem in
materials science. We test whether machine learning can predict Tc directly from 81 engineered descriptors
of a material's elemental composition, using a public dataset of 21,263 superconductors. Six regression
models — Linear Regression, Decision Tree, Random Forest, Gradient Boosting, XGBoost, and a deep neural
network (MLP) — were trained with an 80/20 split and 5-fold cross-validation. Random Forest performed best
(test R² ≈ 0.93, RMSE ≈ 9.0 K), narrowly ahead of XGBoost, while the deep network placed mid-field —
evidence that tree ensembles remain strong baselines for tabular materials data. Impurity-based and
permutation feature importance agree that thermal-conductivity-, atomic-mass-, and valence-related
descriptors are most informative. Critically, we found that the dataset's 21,263 measurements span only
15,542 distinct chemical formulas, so a random split leaks compositions between train and test. Under a
rigorous composition-grouped split (no formula shared across partitions), Random Forest declines only
modestly to R² ≈ 0.913 and the ranking is preserved, showing the models generalize to genuinely unseen
compositions. The work is a reproducible proof-of-concept screening pipeline for candidate superconductors.

---

## Plain-language summary (~250 words)

Electricity loses energy as heat whenever it flows through ordinary wires. Superconductors are special
materials that carry electricity with **zero** resistance — no wasted heat — but almost all of them only
work at extremely cold temperatures, which makes them impractical for real power grids. If we could find
materials that superconduct at higher temperatures, it could transform how we move and store energy. The
problem is that testing candidate materials in a lab is slow and expensive.

This project asks a focused question: **can a computer learn to predict a material's superconducting
critical temperature (Tc) from numbers describing its chemical make-up?** If so, such a model could act as
a cheap first-pass filter, ranking thousands of candidate materials so that only the most promising ones go
to the lab.

Using a public dataset of 21,263 superconductors, I compared six machine-learning models, from a simple
linear baseline to a deep neural network. The best model (Random Forest) predicts Tc with an R² of about
0.93 — meaning it explains roughly 93% of the variation in critical temperature. Interestingly, the deep
neural network did *not* beat the simpler tree-based models, a useful and slightly counterintuitive result.

Most importantly, I checked whether the model was "cheating" by seeing near-identical materials during both
training and testing. After enforcing a strict split where no chemical formula appears in both sets, the
accuracy barely dropped (R² ≈ 0.91) — strong evidence the model genuinely generalizes rather than
memorizes.

---

## Key results at a glance

| | Random split | **Leakage-free (grouped)** |
|---|---|---|
| Best model | Random Forest | Random Forest |
| Test R² | 0.930 | **0.913** |
| Test RMSE | 9.0 K | 10.1 K |
| Model ranking | RF > XGBoost > MLP > GBM > Tree > Linear | **preserved** |

- **Dataset:** 21,263 superconductors, 81 engineered descriptors, 15,542 unique chemical formulas.
- **Most informative descriptors** (two independent importance methods agree): `range_ThermalConductivity`
  dominates, then atomic-mass-, valence-, and density-related features.
- **Deep learning finding:** a 3-layer MLP lands mid-field — tree ensembles win on this tabular problem.

---

## What is novel / what is my contribution

- **A leakage-aware benchmark.** Many prior uses of this dataset report a single random-split R² near 0.92
  without controlling for the fact that the same chemical formula recurs up to 100+ times. This project
  explicitly quantifies that leakage and reports the honest, composition-grouped result — the number that
  actually reflects screening on new materials.
- **A fair six-model comparison including deep learning**, evaluated identically (same split, same
  cross-validation), showing that a neural network does not beat gradient-boosted/bagged trees here.
- **Robustness of the interpretation:** feature importance is confirmed with two independent methods
  (impurity + permutation) rather than a single, correlation-biased measure.
- **Full reproducibility:** the notebook runs end-to-end on any machine (the neural network trains in an
  isolated worker process supporting CPU/GPU/Apple-MPS), and every figure in the paper regenerates from the
  committed code and data.

---

## Methods (one paragraph)

Data were cleaned (one malformed column coerced to numeric; invalid rows dropped) to 21,263 records.
Six regressors were trained with a fixed-seed 80/20 split and 5-fold cross-validation, scored by MAE, MSE,
RMSE, and R². Random Forest importances were cross-checked with held-out permutation importance; Pearson
correlations and a 2-component PCA characterized dataset structure. Data leakage was assessed by (i)
removing exact-duplicate rows and (ii) a composition-grouped split keyed on the chemical formula from the
companion `unique_m.csv`, using `GroupShuffleSplit` and `GroupKFold`. Implementation: scikit-learn,
XGBoost, and PyTorch.

---

## Limitations (state these honestly)

Engineered statistical descriptors are not direct physical measurements, so importances are statistical
associations, not proven mechanisms (and thermal-conductivity descriptors are collinear). Hyperparameters
were only lightly tuned. This is a proof-of-concept screening pipeline, not a physical theory of
superconductivity.
