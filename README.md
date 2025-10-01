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
- **aFRR**: The power step size is set to **0.1 MW** for all countries.  

##  Solver Support
This code is optimized for use with the Gurobi solver:
- If Gurobi is installed and licensed, the program will automatically utilize it to ensure fast and stable performance. 
- Other open-source solvers such as GLPK or HiGHS are also supported but may be significantly slower due to the large problem size.
