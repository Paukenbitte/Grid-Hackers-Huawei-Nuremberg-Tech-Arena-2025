#  HW TechArena 2025 - BESS Optimization (Team Grid Hacker)

##  Introduction
This project applies **Pyomo mathematical modeling** and the **Gurobi optimizer** to simulate the optimal scheduling of **Battery Energy Storage Systems (BESS)** based on 2024 market data (Day-ahead, FCR, and aFRR).  

We further evaluate the **investment profitability** across multiple countries, identify the **optimal configuration parameters**, and perform a comparative analysis to determine which country provides the most favorable long-term environment for BESS deployment.  

---

##  Features
-  **Market Data Processing**: Read Day-ahead, FCR, and aFRR market prices from Excel input files.  
-  **Operation Optimization**: Optimize daily operation strategies using Pyomo + Gurobi, identifying the best charge/discharge time windows.  
-  **Investment Optimization**: Calculate 10-year cash flows starting from 2024, based on country-specific WACC and inflation rates, and evaluate ROI.  
-  **Configuration Optimization**: Explore combinations of 3 different **C-rates** and 3 different **daily cycle numbers** to determine the best operation parameters for each country.  
-  **Result Export**: Automatically generate Excel output files including **Configuration**, **Investment**, and **Operation** analyses for five different countries.  
