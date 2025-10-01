#  HW TechArena 2025 - BESS Optimization (Team Grid Hackerss)

##  Introduction
This project leverages **Pyomo mathematical modeling** and the **Gurobi optimizer** to optimize the scheduling of **Battery Energy Storage Systems (BESS)** across multiple electricity markets, using real 2024 Day-Ahead, FCR, and aFRR price data.  
 
We further evaluate the **investment profitability** for BESS deployment in different countries, systematically compare configuration parameters, and identify the most attractive investment opportunities for long-term operation.

---

##  Features
-  **Market Data Processing**: Reads and cleans Day-Ahead, FCR, and aFRR market prices from input Excel files, and prepares them for use in all subsequent calculations.
-  **Operation Optimization**: Optimizes daily BESS operation strategies using Pyomo + Gurobi. For each country, C-rate, and daily cycle scenario, the algorithm determines optimal charge/discharge actions at every time step, maximizing yearly revenue.
-  **run_all_scenarios.py**:  Runs all combinations of countries (5), C-rates (3), and daily cycles (3), calculates 10-year cashflow (starting from 2024), and evaluates the ROI for every case, considering country-specific WACC and inflation rates.
-  **Investment Optimization**: Analyzes and compares all results to identify the country with the highest levelized ROI and its optimal configuration. Outputs the top-performing solutions.
-  **Configuration Optimization**:  Compares all configurations to find the C-rate and daily cycle combination that yields the highest ROI.  
-  **Result Export**: Automatically generate Excel output files including **Configuration**, **Investment**, and **Operation** analyses for five different countries.  

---

##  Approach — Operation Optimization
This section explains how the rolling-horizon operation optimization works, what to prepare, how to run it fast and reproducibly, and which knobs to tweak for your final runs.

### What the optimizer does
- **Scope**: battery arbitrage + reserve capacity (FCR / aFRR±) with a rolling window.
- **Workflow per window**:
  1. LP warm-start (continuous relaxation) → good starting point.
  2. MILP solve (executed week has 0.1MW DA discretization + binaries).
- **Write-back**: only the executed week is written to results; the horizon rolls by one week.
- **SOC terminal policy**: hard band at window end + linear penalty to keep end-SOC near target.

### Time grid, horizon, and services
- Base step: 15 min (dt = 0.25 h).
- Block size: 4 h blocks for FCR/aFRR capacities (block-constant).
- Typical horizon: exec_weeks = 1, lookahead_weeks = 2.
- DA discretization: 0.1MW steps in the executed week only.

### Price unit conventions
- If capacity prices are in €/MW·h  
  `revenue += price * capacity * dt` (for every 15-min step).
- If capacity prices are in €/MW per 4h block  
  add `price * capacity` once at the block start, **do not** multiply by dt, **do not** divide by 16.  
⚠️ Mis-matching this will shift results by up to ×16.

### Model highlights
- **Decision variables**: DA buy/sell power, FCR & aFRR reserves, SOC trajectory, binaries for executed week.
- **Constraints**: power limits with efficiency, energy headroom, daily cycle cap, 4h block-constant reserves, mutually exclusive switches, terminal SOC band.
- **Objective**: maximize DA + reserve revenues − terminal SOC penalty.

### Solver hints (Gurobi)
- MILP(1): `TimeLimit=150s, MIPGap=0.5%, MIPFocus=1, Heuristics=0.25`.
- MILP(2) polish: `TimeLimit=60s, MIPGap=0.25%, MIPFocus=1, Heuristics=0.3`.

### Stability & reproducibility
- Use `tiny ≈ 1e-6…1e-5` as cushion, `eps ≈ 1e-9` only for clipping.
- Confirm price units once (€/MW·h vs €/MW per block).
- Store solver settings, SoC bounds, alpha in metadata for reproducibility.

### Common pitfalls
- Revenue off by ×16 → wrong price unit.
- Warm-start warnings → round integers or use continuous reserves.

---

##  Installation
```bash
git clone https://github.com/Paukenbitte/Grid-Hackers-Huawei-Nuremberg-Tech-Arena-2025.git
cd Grid-Hackers-Huawei-Nuremberg-Tech-Arena-2025
pip install -r requirements.txt
```

##  Output Results
The project generates three sets of results in the `output/` directory:
-  **Best investment country** with corresponding ROI  
-  **Optimal configuration parameters** (C-rate and daily cycles) per country  
-  **Year-by-year cash flow analysis**  

##  Market Step Sizes
- **FCR**: According to market rules and regulations, the power step size is set to **1 MW** for all countries.  
- **aFRR**: The power step size is set to **1 MW** for all countries.
- **Day-ahead prices**: The energy step size is set to **0.1 MW** for all countries.

##  Solver Support
This code is optimized for use with the Gurobi solver:
- If Gurobi is installed and licensed, the program will automatically utilize it to ensure fast and stable performance. 
- Other open-source solvers such as GLPK or HiGHS are also supported but may be significantly slower due to the large problem size.
