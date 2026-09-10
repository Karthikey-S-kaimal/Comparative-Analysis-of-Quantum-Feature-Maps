# Comparative Analysis of Quantum Feature Maps

A simulation-based study comparing **ZFeatureMap**, **ZZFeatureMap**, and **PauliFeatureMap** on the Iris dataset — examining how feature map choice affects circuit complexity, entanglement, transpiled hardware cost, and the resulting quantum data representation.

Project 3 in an ongoing quantum computing portfolio. All experiments run on Qiskit's `AerSimulator` — no real quantum hardware backend used.

---

## Overview

The central question this project investigates:

> How does the choice of quantum feature map affect circuit complexity, feature interactions, and quantum data representation?

The project is structured in two layers:

- **Layer 1 — Quantum Data Encoding (conceptual foundation).** Five encoding techniques (Basis, Angle, Amplitude, Phase, Dense) applied directly to real Iris samples, to show how the same classical data produces different quantum states depending on encoding choice.
- **Layer 2 — Feature Map Comparison (main experiment).** ZFeatureMap, ZZFeatureMap, and PauliFeatureMap compared head-to-head across circuit depth, gate counts, entanglement, transpiled complexity, and measurement distributions.

One representative sample was selected per Iris class (setosa, versicolor, virginica), scaled consistently, and passed through every encoding and feature map so results are directly attributable to encoding/feature-map choice rather than preprocessing differences.

---

## Project Structure

```
├── layer1_encoding.py                       # Layer 1: 5 encoding techniques on Iris samples
├── layer2_feature_maps.py                   # Layer 2: ZFeatureMap/ZZFeatureMap/PauliFeatureMap comparison
├── layer2_feature_map_comparison.csv        # Raw results (depth, gates, entanglement, transpilation) per sample
├── iris.csv                                 # Iris dataset (place in same folder to run scripts)
├── figures/
│   ├── measurement_distributions_setosa.png
│   ├── measurement_distribution_versicolor.png
│   ├── measurement_distribution_virginica.png
│   ├── meyer_wallach_entanglement.png
│   ├── original_circuit_depth.png
│   ├── original_vs_transpiled_depth.png
│   ├── original_vs_transpiled_2q_gates.png
│   ├── total_gate_count.png
│   └── two_qubit_gate_count.png
├── Project3_Feature_Map_Comparison_Report.docx   # Full written report with analysis and conclusion
└── README.md
```

---

## Methods

- **Dataset:** Iris (4 features: sepal length/width, petal length/width), one representative sample per class.
- **Preprocessing:** Features min-max scaled to `[0, 2π]` for rotation/phase-based encodings.
- **Feature maps:** `ZFeatureMap`, `ZZFeatureMap`, `PauliFeatureMap` (Qiskit `circuit.library`), each with `feature_dimension=4`, `reps=2` for a controlled, apples-to-apples comparison. PauliFeatureMap uses Pauli terms `["Z", "Y", "ZZ"]` to differentiate it structurally from ZZFeatureMap.
- **Entanglement measure:** Meyer–Wallach measure, computed via reduced-density-matrix purity per qubit — a numeric, not qualitative, measure of multipartite entanglement.
- **Transpilation:** Generic basis gate set (`cx`, `rz`, `sx`, `x`), optimization level 1 — simulator-only, no real hardware backend.
- **Measurement:** `AerSimulator`, 4000 shots per circuit.

---

## Key Results

| Feature Map | Depth | Total Gates | 2Q Gates | Avg. Entanglement (Q) | Transpiled Depth | Transpiled 2Q Gates |
|---|---|---|---|---|---|---|
| **ZFeatureMap** | 4 | 16 | 0 | ~0.00 | 5 | 0 |
| **ZZFeatureMap** | 31 | 52 | 24 | 0.69 | 30 | 20 |
| **PauliFeatureMap** | 45 | 108 | 24 | 0.69 | 34 | 20 |

**Findings:**
- **ZFeatureMap** performs purely independent, non-entangled encoding — cheapest by far, but captures no feature interactions.
- **ZZFeatureMap** introduces real pairwise entanglement (Q = 0.46–0.85 depending on class) at ~8x ZFeatureMap's depth.
- **PauliFeatureMap** achieves comparable (and for virginica, higher) entanglement than ZZFeatureMap, but at nearly double the total gate count — richer interaction terms come at a disproportionate circuit cost.
- **Transpiled hardware cost is data-dependent**, not just structural — identical circuit templates compiled to different two-qubit gate counts depending on the specific sample's bound parameter values.
- **Measurement distributions diverge sharply** across feature maps for identical classical input, directly visualizing how feature-map choice reshapes the quantum representation of the same data.

Full analysis and discussion: see `Project3_Feature_Map_Comparison_Report.docx`.

---

## How to Run

1. Place `iris.csv` in the same directory as the scripts (columns: `sepal_length, sepal_width, petal_length, petal_width, species`).
2. Install dependencies:
   ```bash
   pip install qiskit qiskit-aer pandas numpy matplotlib --break-system-packages
   ```
3. Run Layer 1 (encoding techniques):
   ```bash
   python layer1_encoding.py
   ```
4. Run Layer 2 (feature map comparison):
   ```bash
   python layer2_feature_maps.py
   ```
   This prints per-sample results to the console and saves `layer2_feature_map_comparison.csv`.

---

## Conclusion

Richer feature interactions come at a proportionally — and in PauliFeatureMap's case disproportionately — higher circuit cost. No single feature map is categorically "better"; the right choice depends on whether the target hardware or simulator budget can absorb the added complexity for the interaction richness a given dataset actually requires.

---

## Tech Stack

- Qiskit (`circuit.library`, `quantum_info`, `AerSimulator`)
- pandas, NumPy
- matplotlib
- python-docx (`docx` npm package) for report generation
