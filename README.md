# Cantilevered L-Bracket — Distributed Transverse Load Verification (10 kg)

Mechanical design and stress verification of an L-shaped sheet-metal bracket, checking
whether the finalized geometry **can safely support a distributed 10 kg load** on its
horizontal arm without exceeding the material's yield strength.

The bracket is idealized as a **cantilever beam under a uniformly distributed transverse
load**, producing a combined state of **bending stress** and **direct (transverse) shear
stress** at the critical cross-section (the fillet where the horizontal arm meets the
fixed vertical arm).

> **Note on naming:** earlier drafts of this project called this an "eccentric loaded"
> bracket. Strictly speaking, "eccentric loading" refers to an *axial* load applied off
> the centroid of a section (`σ = P/A ± M·c/I`, with `M = P·e`). This project's load is
> a *transverse* distributed load on a cantilever, not an axial/eccentric one — so the
> title and terminology below have been corrected. The underlying physics and formulas
> used so far are unaffected by this renaming.

The project combines CAD modeling and FEA simulation in **Solid Edge Community Edition**
with an independent analytical verification in a **Jupyter Notebook**, following the same
workflow style as the Conveyor Drive gear reducer project (CAD + hand calc cross-check).

---

## 1. Problem Description

An L-bracket is fixed at one end (bolted to a wall) and carries a **10 kg mass, uniformly
distributed** along the horizontal arm. This project exists to answer one question:

> **Does this bracket, as designed (S235JR, 1.5 mm sheet, 48 mm wide, 100 x 100 mm arms,
> R2 mm inside bend), survive a 10 kg distributed load with an adequate safety margin —
> or does it need to be redesigned?**

The distributed load generates an internal bending moment and a direct (transverse) shear
force at the fixed support. The critical section — at the fillet where the horizontal arm
meets the vertical (fixed) arm — experiences:

- **Bending stress** `σ = M·c / I`
- **Direct shear stress** `τ = V / A` (or the refined shear formula `τ = V·Q / (I·b)` for
  non-rectangular sections)
- A **combined (von Mises) stress state**, checked against the material's yield strength,
  including the effect of the stress concentration at the inside bend radius

## 2. Objectives

- [X] Build a parametric 3D model of the L-bracket in Solid Edge Community Edition
- [ ] Run a static structural simulation (Solid Edge Simulation) under the 10 kg
      distributed load
- [ ] Independently calculate reaction forces, internal shear/moment diagrams, and
      combined stresses in a Jupyter Notebook
- [ ] Plot the shear force diagram (SFD), bending moment diagram (BMD), and stress
      distribution across the critical section
- [ ] Compare FEA vs. hand-calculation results and determine the governing safety factor
- [ ] **Verdict: does the bracket pass or fail under the 10 kg load?**

## 3. Current Progress

- ✅ **CAD model** of the finalized geometry is complete in Solid Edge.
- ✅ **Hand calculation done on paper** (not yet in notebook form) for the 10 kg
  distributed load case — see the preliminary numbers in Section 8 below.
- 🔄 **Jupyter Notebook: starting over from scratch.** A first version existed for an
  earlier load case; it will be rebuilt cleanly for the current 10 kg load and R2 mm
  fillet, rather than patched further.
- 🔄 **FEA (Solid Edge Simulation): starting over from scratch.** A prior static study
  used an earlier 250 N load case and is not valid for this verification; it will be
  re-run under the current 10 kg load once the notebook is rebuilt.

## 4. Tools & Methodology

| Stage | Tool | Purpose |
|---|---|---|
| CAD modeling | Solid Edge Community Edition | 3D part geometry, fillets, mounting features |
| Simulation | Solid Edge Simulation (built-in FEA) | Static structural study, stress/displacement contours |
| Calculation | Python 3 / Jupyter Notebook | Analytical free-body diagram, SFD/BMD, stress equations |
| Plotting | Matplotlib / NumPy | SFD, BMD, and stress-distribution plots |
| Reporting | Jupyter Notebook → exported PDF/HTML | Final calculation report |

## 5. Project Structure

```
eccentric-l-bracket/
├── README.md
├── CAD/
│   └── l_bracket.par              # Solid Edge part file
├── Simulation/
│   └── l_bracket_static_study/    # Solid Edge Simulation results (to be redone)
├── Calculations/
│   └── l_bracket_analysis.ipynb   # Hand calc + plots (to be redone)
├── Drawings/
│   └── l_bracket_drawing.dft      # 2D detail drawing (optional)
└── Results/
    └── summary.md                 # FEA vs. hand-calc comparison, pass/fail verdict
```

## 6. Design Parameters (Final)

| Parameter | Symbol | Value | Unit |
|---|---|---|---|
| Applied load (distributed mass) | `m` | 10 | kg |
| Applied load (force) | `P = m·g` | 98.1 | N |
| Load distribution | — | uniform over horizontal arm | — |
| Horizontal arm length | `L₁` | 100 | mm |
| Vertical arm length | `L₂` | 100 | mm |
| Bracket width × thickness | `b × t` | 48 × 1.5 | mm |
| Inside bend radius | `r` | 2 | mm |
| Material | — | S235JR (EN 10025-2) | — |
| Yield strength | `σ_y` | 235 | MPa |
| Target safety factor | `n` | ≥ 1.5 (typical for static sheet-metal structural parts) | — |

## Final Geometry (Version 1.0)

- Material: **S235JR (EN 10025-2)**
- CAD: **Solid Edge Community Edition**
- Sheet thickness: **1.5 mm**
- Width: **48 mm**
- Arm lengths: **100 mm × 100 mm**
- Inside bend radius: **R2 mm**
- Six Ø6.5 mm through holes (3 per leg)
- Holes include Start Chamfer and End Chamfer
- Hole layout:
  - Two holes: 12 mm from each side edge and 18 mm from the free edge
  - One centred hole: on centreline, 48 mm above the lower pair

## 7. Assumptions

- Material behaves linearly elastically; loading is static (no fatigue/impact).
- Bracket is idealized as two straight beam segments joined rigidly at the bend
  (fillet): a loaded cantilever segment (horizontal arm) and a constant-moment transfer
  segment (vertical arm) down to the fixed wall.
- Self-weight of the bracket is neglected.
- Stress concentration at the inside bend (R2 mm) is a 90° "knee" corner of constant
  thickness, not a classic stepped shoulder fillet — standard Peterson/Shigley `K_t`
  charts do not directly apply. `K_t` is therefore only estimated in the hand calc
  (≈1.1–1.3 for this `r/t` ratio), and the FEA mesh is treated as the more reliable
  source for the true local peak stress at the fillet.
- The mounting holes are not included in the beam-theory hand calc section properties
  (net-section reduction is neglected); the FEA model does capture them.
- Support is treated as fully rigid (fixed boundary condition) in both the CAD
  simulation and the analytical model.

## 8. Analytical Calculation — Notebook Contents (to be rebuilt)

The Jupyter Notebook (`Calculations/l_bracket_analysis.ipynb`) will include:

1. Free-body diagram and reaction forces/moments at the fixed support
2. Section properties at the critical section (`A`, `I`, `c`, `Q`)
3. Internal shear force and bending moment as a function of position along the bracket
   (both arms, including the transition from transverse shear to axial force at the bend)
4. Bending stress and direct shear stress at the critical section
5. Combined stress state (von Mises equivalent stress), checked at both the outer fiber
   and the neutral axis
6. Stress-concentration discussion (`r/t` ratio, estimated `K_t` range) and safety factor
   vs. yield stress, both nominal and with estimated `K_t`
7. **Plots:**
   - Shear force diagram (SFD) along the bracket
   - Bending moment diagram (BMD) along the bracket
   - Stress distribution across the cross-section (bending + shear superposed)

### Preliminary hand-calc numbers (paper calc, to be reproduced in the rebuilt notebook)

| Quantity | Value |
|---|---|
| P (10 kg × g) | 98.1 N |
| M_max at fillet | 4.91 N·m |
| V_max at fillet | 98.1 N |
| σ_bending (nominal, Kt = 1.0) | 272.5 MPa |
| τ_max | 2.04 MPa |
| Safety factor (nominal, Kt = 1.0) | **0.86 (< 1 → fails nominal check)** |
| Safety factor (estimated Kt ≈ 1.2) | 0.72 |

**Preliminary flag:** even before any stress concentration is applied, the paper
calculation already shows a safety factor below 1 for the 10 kg load with the current
1.5 mm × 48 mm section. This suggests the current geometry may **not** pass the 10 kg
load case as-is — pending confirmation from the rebuilt notebook and the re-run FEA
study. Possible mitigations if confirmed: increase sheet thickness, increase bracket
width, add a stiffening rib/gusset, or shorten the loaded span `L₁`.

## 9. Workflow Status

- [X] Define final geometry and load case (10 kg distributed load, geometry above)
- [X] CAD model completed in Solid Edge
- [ ] Notebook rebuilt from scratch for the 10 kg load case
- [ ] Static simulation rebuilt from scratch and run with the 10 kg load
- [ ] FEA vs. hand-calc comparison documented in `Results/summary.md`
- [ ] Final pass/fail verdict and report exported

## 10. Results Summary

*To be completed once the notebook is rebuilt and the Solid Edge Static Study is re-run
with the 10 kg load.*

| | Bending stress | Shear stress | Combined/Equivalent stress | Safety factor |
|---|---|---|---|---|
| Hand calculation (paper, nominal) | 272.5 MPa | 2.04 MPa | 272.5 MPa | 0.86 |
| Hand calculation (paper, est. Kt ≈ 1.2) | 327.0 MPa | — | 327.0 MPa | 0.72 |
| Solid Edge Simulation | — | — | — | — |
| % Difference | — | — | — | — |

**Verdict:** ⚠️ *Pending notebook + FEA confirmation* — preliminary paper calculation
indicates the bracket does **not** meet a safety factor of 1 (let alone the target of
≥1.5) under the 10 kg distributed load in its current geometry.

## 11. References

- Shigley's Mechanical Engineering Design — combined loading, beam stresses
- Hibbeler, *Mechanics of Materials* — shear/bending formulas, transverse shear
- Solid Edge Community Edition / Simulation documentation