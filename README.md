# Cantilevered L-Bracket — Distributed Transverse Load Verification

Mechanical design and stress verification of an L-shaped sheet-metal bracket, checking
whether a given geometry can safely support a distributed weight on its horizontal arm
without exceeding the material's yield strength.

The bracket is fixed at one end (bolted to a wall) and idealized as a **cantilever beam
under a uniformly distributed transverse load**, producing a combined state of **bending
stress** and **direct (transverse) shear stress** at the critical cross-section — the
fillet where the horizontal arm meets the fixed vertical arm.

The project combines CAD modeling and FEA simulation in **Solid Edge Community Edition**
with an independent analytical verification in a **Jupyter Notebook**.

---

## 1. Problem Description

An L-bracket is fixed at one end and carries a distributed mass along its horizontal arm.
The core question this project answers is:

> **Given a bracket's geometry, material, and a target load, can it hold that load with
> an adequate safety margin — or does it need to be redesigned?**

The distributed load generates an internal bending moment and a direct (transverse) shear
force at the fixed support. The critical section experiences:

- **Bending stress** `σ = M·c / I`
- **Direct shear stress** `τ = V / A` (or the refined shear formula `τ = V·Q / (I·b)` for
  non-rectangular sections)
- A **combined (von Mises) stress state**, checked against the material's yield strength,
  including the effect of the stress concentration at the inside bend radius

## 2. Objectives

- [X] Build a parametric 3D model of the L-bracket in Solid Edge Community Edition
- [X] Run a static structural simulation (Solid Edge Simulation) under the target load
- [ ] Build a **parametric Jupyter Notebook**: change the input geometry (arm lengths,
      width, thickness, fillet radius, material, target load) and have it automatically
      recalculate the internal forces, stresses, safety factor, and a clear **pass/fail
      verdict** against the desired weight — without manually re-deriving formulas each
      time
- [ ] Plot the shear force diagram (SFD), bending moment diagram (BMD), and stress
      distribution across the critical section for the current geometry
- [ ] Compare FEA vs. hand-calculation results
- [ ] **Verdict: does the bracket pass or fail under the target load?**

## 3. Tools & Methodology

| Stage | Tool | Purpose |
|---|---|---|
| CAD modeling | Solid Edge Community Edition | 3D part geometry, fillets, mounting features |
| Simulation | Solid Edge Simulation (built-in FEA) | Static structural study, stress/displacement contours |
| Calculation | Python 3 / Jupyter Notebook | Parametric analytical model: FBD, SFD/BMD, stress equations, pass/fail check |
| Plotting | Matplotlib / NumPy | SFD, BMD, and stress-distribution plots |
| Reporting | Jupyter Notebook → exported PDF/HTML | Final calculation report |

## 4. Project Structure

```
l-bracket-load-check/
├── README.md
├── CAD/
│   └── l_bracket.psm              # Solid Edge part file
├── Simulation/
│   └── l_bracket_static_study/    # Solid Edge Simulation results
├── Calculations/
│   └── l_bracket_analysis.ipynb   # Parametric hand calc + plots + pass/fail check
├── Drawings/
│   └── l_bracket_drawing.dft      # 2D detail drawing (optional)
└── Results/
    └── summary.md                 # FEA vs. hand-calc comparison, pass/fail verdict
```

## 5. Design Parameters

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

All of the above should be **inputs** in the notebook, not hardcoded — so changing any
one of them (e.g. a thicker sheet, a different span, a bigger fillet) automatically
re-runs the full calculation and updates the pass/fail verdict.

## 6. Geometry

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
- Stress concentration at the inside bend is a 90° "knee" corner of constant thickness,
  not a classic stepped shoulder fillet — standard Peterson/Shigley `K_t` charts do not
  directly apply. `K_t` is therefore only estimated in the hand calc (as a function of
  `r/t`), and the FEA mesh is treated as the more reliable source for the true local
  peak stress at the fillet.
- The mounting holes are not included in the beam-theory hand calc section properties
  (net-section reduction is neglected); the FEA model does capture them.
- Support is treated as fully rigid (fixed boundary condition) in both the CAD
  simulation and the analytical model.

## 8. Analytical Calculation — Notebook Contents

The Jupyter Notebook (`Calculations/l_bracket_analysis.ipynb`) is built parametrically,
driven entirely by the input block in Section 5, and includes:

1. **Input block** — all geometry, material, and load parameters in one place, easy to
   edit and re-run
2. Free-body diagram and reaction forces/moments at the fixed support
3. Section properties at the critical section (`A`, `I`, `c`, `Q`)
4. Internal shear force and bending moment as a function of position along the bracket
   (both arms, including the transition from transverse shear to axial force at the bend)
5. Bending stress and direct shear stress at the critical section
6. Combined stress state (von Mises equivalent stress), checked at both the outer fiber
   and the neutral axis
7. Stress-concentration estimate (`r/t` ratio, `K_t` range) and safety factor vs. yield
   stress, both nominal and with estimated `K_t`
8. **Automatic pass/fail verdict**: compares the computed safety factor against the
   target safety factor and prints a clear result (e.g. "PASS — n = 1.8 ≥ 1.5" or
   "FAIL — n = 0.86 < 1.5") for whatever geometry/load is currently entered
9. **Plots:**
   - Shear force diagram (SFD) along the bracket
   - Bending moment diagram (BMD) along the bracket
   - Stress distribution across the cross-section (bending + shear superposed)

## 9. Workflow Status

- [ ] CAD model completed in Solid Edge
- [ ] Static simulation set up and run
- [ ] Notebook: parametric input block + SFD/BMD derivation
- [ ] Notebook: stress calculations, safety factor, and pass/fail check
- [ ] Notebook: plots
- [ ] FEA vs. hand-calc comparison documented in `Results/summary.md`
- [ ] Final report exported

## 10. Results Summary

*To be completed once the simulation and the analytical calculation are finished.*

| | Bending stress | Shear stress | Combined/Equivalent stress | Safety factor | Verdict |
|---|---|---|---|---|---|
| Hand calculation | — | — | — | — | — |
| Solid Edge Simulation | — | — | — | — | — |
| % Difference | — | — | — | — | — |

## 11. References

- Shigley's Mechanical Engineering Design — combined loading, beam stresses
- Hibbeler, *Mechanics of Materials* — shear/bending formulas, transverse shear
- Solid Edge Community Edition / Simulation documentation