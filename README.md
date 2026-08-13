# The Payroll Leak: Quantifying the Financial Burden of Macroeconomic Inflation and Statutory Wage Compression on Corporate Attrition

---

## 🧭 Preface: A Personal Reflection on Engineering Resilience
This capstone is more than just a technical exercise—it’s a reflection of my own journey. While the dataset rows are synthetic, the architecture, geographic anchors, and financial structures are drawn directly from my lived professional experiences.

I built this project during a turning point in my career. After an unexpected corporate retrenchment, I committed to completing the Google Data Analytics Certificate to pivot my technical skills. To make the case study real, I mapped the dataset to the exact corporate grading structures I once managed as an HR Admin II Remuneration Specialist. 

Instead of blowing out the synthetic data with a blind multiplier, I used my actual field experience to hardcode an authentic corporate matrix: Grade I (HR Admin I) Contracts & Benefits and Global Mobility Officers locked at ₱32k to ₱40k; Grade II (HR Admin II) Employee Lifecycle and Remuneration Specialists scaled at ₱48k to ₱55k (my old base) up to ₱65k for SMEs; Grade III Team Leads at ₱75k to ₱90k; and Executive Hub Directors spanning ₱120k to ₱250k. These conversions were bound directly to the real-world BSP market closing rate of ₱60.93 per US Dollar as of August 2026.

The commuting friction I experienced—traveling from the province into Manila and dropping off at LRT Buendia—became the geographic anchor for my attrition model. That daily grind inspired me to simulate how hybrid work setups intersect with employee retention risks. To make it compliant with standard Philippine corporate registries, I mathematically converted raw miles to Kilometers and discarded artificial 160-hour monthly shortcuts—enforcing the strict DOLE Monday-to-Friday 261-day factor to establish a precise 174-hour monthly labor divisor.

Midway through, my journey took a new turn: I landed a full-time role as a Payroll Supervisor at a professional outsourcing firm. The transition was intense—strict 8-to-5 operations, high-volume payroll cutoffs, and steep learning curves. Progress on this project paused for weeks, and I could only return to coding in late-night windows.

### Local Hardware Environment Bottlenecks
Executing this scale of data transformation from scratch created severe processing constraints on my local machine. Running multiple data pipelines simultaneously pinned my browser thread execution memory to a sustained 89% utilization limit on a standard 8GB RAM setup. This resource wall forced me to optimize my development workflow: I completely stripped out heavy, multi-file data blending frameworks that were lagging my local drive and re-engineered my custom calculations—including the Workforce Slippage & Burnout Index (WSBI) and Overtime Premium Leaks—to run natively on a single master dataset string (`cleaned_localized_gss_data.csv`).

### Tooling Choices & Learning Roadmap
The academic curriculum leaned heavily on R programming. I studied R enough to pass the Coursera requirements, but it never felt natural. As a process-driven engineer, I pivoted my strategy:
*   **SQL (BigQuery):** Utilized exclusively for all core data cleaning, casting, normalization, and structural backend restructuring for this capstone.
*   **Tableau Desktop:** Utilized exclusively for building interactive user dashboards, cross-filtering matrices, and visual storytelling layers.
*   **Python (Future Track):** I did not utilize Python in this specific capstone project. However, having completed this certificate, I am focusing my upcoming learning energy on mastering Python as my long-term tool of choice for automated payroll systems architecture and freelance consulting.

This project is both a technical capstone and a personal milestone—proof that resilience, clarity, and intentional design can turn career setbacks into opportunities for growth.

---

## 📊 1. Executive Summary & Analytical Intent
This people analytics framework models voluntary attrition and payroll risk exposure across three regional Global Shared Services (GSS) operating branches in Metro Manila:
*   **Alabang Shared Services Data Center** (via SLEX Southern Line)
*   **BGC Regional Operations Hub** (via EDSA/Kalayaan)
*   **Makati Corporate GSS Head Office** (via Gil Puyat Ave)

Traditional HR frameworks fail by merely reporting surface-level turnover headcounts. This case study applies advanced workforce accounting to isolate **Indirect Wage Compression** and **Forced Overtime Cash Leaks**. By monitoring timesheet behavior against macroeconomic indicators, this model provides data-driven evidence to show corporate executive boards exactly when delaying a recruitment budget costs a firm more money than filling a vacancy.

---

## 📈 2. Macroeconomic, Geopolitical, & Industry Anchor Benchmarks
To ground this simulation in high-stakes operational realities, the data environment incorporates volatile market indicators and recent legislative updates:
1.  **PSA Core Inflation & Geopolitical Shocks:** Mapped directly to the Philippine Statistics Authority (PSA) core benchmark pacing at **4.4%**. This baseline is heavily driven by global oil price spikes stemming from Middle East conflicts, which has triggered cascading increases in local transport fares, tollways, and basic consumer goods.
2.  **Statutory Wage Compression Floor:** Calibrated against the active National Capital Region (NCR) minimum wage board adjustment, which integrated a **₱35.00 statutory daily hike**, lifting the non-agriculture market baseline floor to exactly **₱780.00/day**. This legislated increase compresses the premium gap separating specialized mid-tier corporate support bands from entry-level roles.
3.  **Financial Index Conversion:** Mapped to the real-world Bangko Sentral ng Pilipinas (BSP) closing rate of **₱60.93 per US Dollar**.
4.  **Operational Labor Divisor:** Calculated using the Monday-to-Friday DOLE 261-day annual factor, establishing a precise **174-hour monthly payroll divisor**.

---

## 🛠️ 3. Data Transformation & Database Engineering (SQL)
The original 1,470-row synthetic engine was cleaned and restructured using Google Cloud BigQuery. Generic variables were mapped onto standardized corporate salary band intervals (B1 to C2) based on real-world multi-tier shared services payroll structures.

### The Production Script:
```sql
-- STEP 1: TRANSLATE AND LOCK ROWS TO STANDARDIZED CORPORATE OPERATING MODEL BAND GRADES
SELECT 
  Age,
  Gender,
  MaritalStatus,
  
  -- GEOGRAPHIC LOCALIZATION: LRT Buendia Transit Terminal Hub (0-KM Center Point)
  CASE 
    WHEN (DistanceFromHome * 1.60934) <= 8.0 THEN 'Makati Corporate GSS Head Office (via Gil Puyat Ave)'
    WHEN (DistanceFromHome * 1.60934) BETWEEN 8.1 AND 25.0 THEN 'BGC Regional Operations Hub (via EDSA/Kalayaan)'
    ELSE 'Alabang Shared Services Data Center (via SLEX Southern Line)'
  END AS GSS_Office_Branch,
  
  -- STRUCTURAL LOCALIZATION: Re-aligning organizational roles to standard Shared Services operational titles
  CASE 
    WHEN JobLevel = 1 AND PerformanceRating <= 3 THEN 'Contracts & Benefits Officer (HR Admin I)'
    WHEN JobLevel = 1 AND PerformanceRating = 4 THEN 'Global Mobility Officer (HR Admin I)'
    WHEN JobLevel = 2 AND JobRole = 'Research Scientist' THEN 'Employee Lifecycle Services Specialist (HR Admin II)'
    WHEN JobLevel = 2 AND JobRole = 'Sales Executive' THEN 'Remuneration Specialist (HR Admin II)'
    WHEN JobLevel = 3 AND JobRole = 'Research Director' THEN 'Subject Matter Expert (SME)'
    WHEN JobLevel = 3 THEN 'Shared Services Team Lead'
    WHEN JobLevel >= 4 THEN 'GSS Operations Director (Head)'
    ELSE 'Specialized Shared Services Analyst'
  END AS GSS_Job_Role,
  
  -- FINANCIAL INTEGRITY LAYER: Estimated Regional Market Benchmarks aligned to Industry Grade Intervals
  CASE 
    WHEN JobLevel = 1 AND JobSatisfaction = 1 THEN 32000.00  -- B1 Entry
    WHEN JobLevel = 1 AND JobSatisfaction = 2 THEN 35000.00  -- B1 Core
    WHEN JobLevel = 1 AND JobSatisfaction >= 3 THEN 40000.00 -- B1 Senior
    WHEN JobLevel = 2 AND JobSatisfaction = 1 THEN 48000.00  -- B2 Entry
    WHEN JobLevel = 2 AND JobSatisfaction = 2 THEN 55000.00  -- B2 Core
    WHEN JobLevel = 2 AND JobSatisfaction >= 3 THEN 65000.00 -- B2 Senior
    WHEN JobLevel = 3 AND JobSatisfaction = 1 THEN 72000.00  -- B3 Intermediate
    WHEN JobLevel = 3 AND JobSatisfaction = 2 THEN 75000.00  -- B3 Core
    WHEN JobLevel = 3 AND JobSatisfaction >= 3 THEN 90000.00 -- B3 Senior
    WHEN JobLevel = 4 AND JobSatisfaction = 1 THEN 120000.00 -- C1 Executive
    WHEN JobLevel = 4 AND JobSatisfaction = 2 THEN 150000.00 -- C1 Core
    WHEN JobLevel = 4 AND JobSatisfaction >= 3 THEN 170000.00 -- C1 Senior
    ELSE 200000.00 -- C2 Director Band Ceiling
  END AS Monthly_Base_Salary_PHP,
  
  4.4 AS PSA_Core_Inflation_Percent,
  780.00 AS NCR_Statutory_Wage_Floor_PHP,
  OverTime,
  JobSatisfaction,
  Attrition
FROM 
  `rich-capstone-project.philippine_people_analytics.raw_ibm_attrition_data`
WHERE 
  MonthlyIncome IS NOT NULL AND Age >= 18 AND StandardHours > 0;
```

---

## 🛠️ 4. Advanced DevOps & Troubleshooting Manifesto
Building an enterprise analytics framework from scratch introduces unexpected logical conflicts and software environment blocks. This log transparently records the major system defects diagnosed and resolved during development.

### Conflict 1: BigQuery Schema Over-Optimization Error

### Conflict 2: Core Hardware Utilization Bottlenecks
*   **The Problem:** Local system performance severely degraded during multi-workspace development, with background browser processes pinning system memory at a sustained **89% resource limit on a standard 8GB RAM local drive**. 
*   **The Resolution:** Streamlined the entire analytics schema. Dumped heavy, multi-file data blending frameworks and combined calculations onto a single master dataset string (`cleaned_localized_gss_data.csv`). This significantly cut browser thread execution times, allowing local visualization rendering engines to run efficiently.

### Conflict 3: The 18-Hour Tableau Cross-Filtering Loop
*   **The Problem:** Once the data was loaded into Tableau Desktop, the canvas layout became non-reactive. Clicking a geographic office branch on the Heatmap (Sheet 3) failed to cross-filter the Headcount Scorecard or the Overtime Cost Chart, causing dashboards to freeze.
*   **The Cause (XML Diagnosis):** Leveraging AI tools to scan the workbook's raw underlying XML code revealed an architectural blind spot: the charts were built on two completely disconnected data sources. The heatmap ran on the primary master file, while the cost metrics ran on an isolated, pre-aggregated 8-row summary file that lacked geographic parameters. Because the column `GSS_Office_Branch` did not exist in the summary file, the dashboard actions were dropped by the backend compiler.
*   **The Resolution:** Completely deleted the disconnected 8-row summary file. Rebuilt the entire overtime cost bar chart and the proprietary Workforce Burnout Index natively from the row-level variables of the 1,470-row master CSV file. By establishing an open data pipeline where all sheets shared identical database schema coordinates, the cross-filtering engine functioned perfectly on the first try.

### Conflict 4: The Attrition Zero Denominator Crash
*   **The Problem:** When testing the newly built Workforce Burnout chart, clicking any cell on the heatmap representing active workers (`Attrition = False`) caused the index value to instantly collapse to zero across the board.
*   **The Cause:** The dashboard action filter was passing both `Attrition` and `GSS_Office_Branch` simultaneously. Because the burnout index uses attrition numbers as its divisor denominator, filtering the chart down to a purely active employee cohort forced a `Divide-by-Zero` error, which triggered the safety guard to wipe out the entire calculation display.
*   **The Resolution:** Decoupled the filter logic. Separated the calculations into their own dedicated action maps. The headcount and cost charts retained the combined branch-and-attrition filter, while the burnout chart was wired to filter strictly by geographic branch alone—allowing it to utilize the full employee population to compute an accurate ratio.

---

## 🎛️ 5. Final Analytics Calculations & Visual Logic
To establish a fully interactive environment, the advanced metrics were coded natively within the Tableau workspace using row-level logical scripts.

### Metric 1: Overtime Cash Leak PHP (Article 87 Compliance)
*   **The Intuition:** This field identifies employees logging active overtime hours and extracts their exact base hourly pay register (Monthly Base Pay / 174 Hours). It then applies the legal **1.25x premium multiplying factor** mandated by Article 87 of the Philippine Labor Code to calculate rolling cash leaks.
*   **The Formula:**
    ```text
    IF [OverTime] = TRUE THEN ([Monthly_Base_Salary_PHP] / 174) * 1.25 ELSE 0 END
    ```

### Metric 2: Workforce Slippage & Burnout Index (WSBI)
*   **The Intuition:** Traditional retention metrics fail by ignoring the operational stress left behind by departures. This proprietary framework tracks systemic strain by calculating the ratio of **Estimated Overtime Hours Logged** across surviving active team members relative to the total number of **Calculated Vacancies** inside that specific job track. A high index value signals a critical attrition tipping point where surviving staff are heavily overloaded.
*   **The Formulas:**
    ```text
    Calculated Vacancies = IF [Attrition] = TRUE THEN 1 ELSE 0 END
    
    Estimated OT Hours Logged = IF [OverTime] = TRUE THEN [Assumed OT Hours Parameter] ELSE 0 END
    
    Dynamic Burnout Index (WSBI) = 
    IF SUM([Calculated Vacancies]) = 0 THEN 0 
    ELSE SUM([Estimated OT Hours Logged]) / SUM([Calculated Vacancies]) END
    ```

### Validated Operational Discoveries:
*   **The Transit Commuting Smoking Gun:** Cross-filtering verified that long-distance provincial commuters traveling through regional networks face intense physical and economic friction (multiplied heavily by rising transport fares from geopolitical oil crises), driving the highest volumetric attrition spikes outside of the urban center (**BGC Hub: 82 departures; Alabang Hub: 78 departures**).
*   **The Specialist Burnout Identification:** The data model successfully isolated the **Employee Lifecycle Services Specialist (HR Admin II)** track as the highest-risk operational segment. Because their core processing tasks are non-negotiable, open vacancies immediately drive surviving specialists into extreme, premium-rate overtime cycles, resulting in a continuous, active payroll cash leak.

---

## 💡 6. Data-Driven Operational Recommendations
1.  **Enforce Data-Driven Recruitment Timelines:** Utilize the Overtime Tipping Point metrics to prove to budgeting teams that keeping a core administrative seat empty actively costs more in rolling premium OT payouts than immediately hiring a replacement.
2.  **Introduce Regional Transit Allowances:** Establish targeted commuting relief, transport subsidies, or flexible hybrid provisions specifically for long-distance commuter bands to stabilize retention against rising macroeconomic travel overheads.
3.  **Address Wage Compression:** Conduct structural annual salary reviews relative to localized core inflation changes (4.4%) and statutory adjustments (the ₱35 daily minimum floor increase) to protect the baseline salary premiums of mid-level corporate technicians before voluntary attrition triggers an operational system failure.

---

## 🌟 7. Strategic Outlook & Collaboration Invitation
I am incredibly proud of the technical foundation and data architecture built in this capstone, but I am entirely open to suggestions, optimizations, and technical feedback on how to scale this model further. 

My learning skill is deeply practical and reality-driven—I absorb concepts, expressions, and technical frameworks fastest when I can directly associate them with operational real-world problems and lived workflows. While this output establishes an interactive corporate risk simulation, I am constantly exploring avenues to maximize its business impact, automate manual registers, and solve high-stakes analytical tasks.

**Let's Connect:** If your organization requires an analytical professional who can break down complex data problems, audit payroll leaks, or build robust reporting architecture—or if you have simple, practical analytical tasks I can execute to assist your data teams—please feel free to reach out. I am open to technical challenges, part-time consulting scopes, and collaborative data engineering opportunities.

*   **The Problem:** The initial analytics summary query crashed inside the Google Cloud Sandbox console, throwing a strict syntax error: `No matching signature for operator = for argument types: BOOL, STRING`. 
