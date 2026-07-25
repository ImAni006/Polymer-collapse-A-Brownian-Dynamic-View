# 🧬 Polymer Collapse under Coordinate-Dependent Damping

> Brownian Dynamics simulations of coarse-grained polymer chains exhibiting local crowding-dependent diffusion.

---

## 🌟 Overview

...

---

## ⚛️ Physics Behind the Model

...

---

## 🌀 Coordinate-Dependent Damping

...

---

## 🔗 Polymer Model

### 🧲 Harmonic Spring Forces

### 🚫 Excluded Volume Interactions (WCA)

### 🎲 Thermal Noise

---

## 🔄 Simulation Workflow

```
Initialize Polymer Chain
        ↓
Count Neighbours
        ↓
Calculate Local Damping
        ↓
Compute Forces
        ↓
Generate Thermal Noise
        ↓
Update Positions
        ↓
Repeat
```

---

## ⚙️ Simulation Parameters

...

---

## 📊 Quantities Calculated

- Radius of Gyration
- End-to-End Distance
- MSD
- Centre of Mass Dynamics
- Neighbour Count Distribution
- Local Damping Distribution

---

## 📈 Results

### 📏 Radius of Gyration

...

### 📐 End-to-End Distance

...

### 📉 Radius of Gyration Dynamics

...

### 👥 Local Crowding Effects

...

---

## 🔬 Key Observations

- Coordinate-dependent damping promotes polymer collapse.
- Local crowding suppresses diffusion.
- Structural fluctuations are reduced.
- The collapsed state is more stable.

---

## 🗂 Repository Structure

```text
.
├── README.md
├── constant_vs_n_dependent_damping.ipynb
├── figures/
├── animations/
├── results/
└── report/
```

---

## 🚀 Future Directions

- Explore alternative damping profiles.
- Study collapse kinetics.
- Perform parameter sweeps.
- Extend simulations to larger polymer chains.
- Investigate scaling laws.

---

## 📚 References

...

---

## 👨‍🔬 Author

**Aniruddha Pareek**

BS-MS Physics  
IISER Bhopal

Summer Research Intern, IISER Pune (2026)

---

### ⭐ If you find this work interesting, feel free to star the repository!

---

## 🎛️ Parameters

<details open>
<summary><b>Simulation & chain parameters</b></summary>

| Parameter | Symbol | Value |
|---|:---:|:---:|
| Beads | `N_beads` | 20 |
| Time step | `dt` | 5×10⁻⁴ |
| Steps | `N_steps` | 200,000 |
| Thermal energy | `KbT` | 1.0 |
| Spring constant | `k` | 100 · KbT |
| Equilibrium bond length | `r0` | 2.0 |
| WCA energy | `epsilon` | 1.0 |
| WCA size | `sigma` | 0.8 |
| Neighbour cutoff | `cutoff` | 1.5·r0 = 3.0 |

</details>

<details>
<summary><b>Damping-model parameters</b></summary>

| Parameter | Symbol | Value |
|---|:---:|:---:|
| Constant friction | `γ₀` | 0.5 |
| Sigmoid amplitude | `a` | 0.9 |
| Sigmoid baseline | `b` | 0.1 |
| Sigmoid steepness | `λ` | 10 |
| Crowding threshold | `n_c` | 3.0 |
| Trajectories (constant γ MSD) | `N_traj` | 50 |
| Trajectories (coordinate-dep. γ MSD) | `N_traj` | 20 |

</details>

Initial configuration (both cases): beads laid in a straight line along `y`,
spaced `r0` apart → `Rg₀ = 11.53` units, end-to-end distance₀ = 40 units.

---

## 📊 Results at a glance

<div align="center">

| Metric | 🔵 Constant | 🟠 Coordinate-dependent | Change |
|---|:---:|:---:|:---:|
| Final **Rg** | 3.78 units | **2.70 units** | ▼ 29% more collapsed |
| Final **end-to-end distance** | 8.17 units | **4.93 units** | ▼ 40% more compact |
| Mean local neighbour count | 4.35 ± 1.16 | **6.72 ± 1.55** | ▲ denser packing |

</div>

```
Radius of Gyration:  11.53 ──┐
                              │  constant γ
                              └──────────► 3.78

                      11.53 ──┐
                              │  coordinate-dependent γ (crowding)
                              └────► 2.70   ← tighter collapse
```

**Takeaway:** crowding-dependent friction creates a positive feedback loop —
denser regions get stickier, which locks the chain into a more collapsed
state instead of letting it re-expand. The trajectory-averaged MSD of the
centre of mass confirms this: long-time diffusion is visibly suppressed
relative to the constant-damping theoretical curve.

<details>
<summary><b>📎 Note on the 120-run averaged statistics quoted in the report</b></summary>
<br>

The project report additionally quotes an average over **120 independent
runs**: end-to-end distance mean **4.97 ± 1.94** units (coordinate-dependent)
vs. **11.70 ± 6.60** units (constant). That batch/statistics driver isn't
included in *this* notebook — this notebook only shows the single-trajectory
runs and the 50-/20-trajectory MSD-averaging blocks.

</details>

---

## 🧰 Requirements & setup

```bash
pip install numpy matplotlib numba
```

> Python ≥ 3.9 recommended for Numba compatibility.

---

## ▶️ How to run

1. **Constant Damping block first** — run from the top through the
   end-to-end-distance cell.
2. **Coordinate-Dependent Damping block second** — same variable names
   (`positions`, `rgcm`, `xcm`, `meanlocal`, …) get reused/overwritten, so
   run the two blocks in order, not interleaved.
3. **Multi-trajectory MSD blocks last** — the expensive ones (`N_traj = 50`
   and `N_traj = 20`, each re-running the full 200,000-step simulation).

> ⏱️ **Runtime:** a single 200,000-step trajectory takes roughly a minute or
> two once Numba's JIT cache is warm (first call in a fresh kernel pays a
> one-off compile cost). The multi-trajectory MSD blocks scale linearly with
> `N_traj` — shrink `N_traj` or `N_steps` for a quick smoke test.

---

## 🩹 Known quirks / things to clean up

- ⚠️ Section headers (`Constant Damping`, `Ignore It`, …) are plain text left
  in **unexecuted code cells**, not markdown cells — harmless, but "Run All"
  will trip over them unless skipped/converted.
- ⚠️ One stray `raw` cell (an early unused MSD draft) sits mid-notebook.
- ⚠️ Variable names are **reused** between the constant- and
  coordinate-dependent blocks — don't run them out of order.
- 💡 `spring_force`/`excluded_volume_force` and the simulation parameters are
  redefined in every block. Pulling these into a shared `forces.py` /
  `simulate.py` would make it much easier to sweep `a`, `b`, `λ`, `n_c`
  systematically.

---

## 📚 Reference

> A. Pareek, *Diffusion in Chain-Like Systems*, DST INSPIRE Summer Internship
> Programme 2026 report, IISER Pune.
> Supervisor: Prof. Arijit Bhattacharyay · PI: Dr. Mayank Sharma
> — Section C, *"Model-1 (Polymer Collapse: A Brownian Dynamics approach)"*

<div align="center">

---

Made with 🧵 stochastic beads, 🧮 NumPy, and 🔥 Numba.

</div>
