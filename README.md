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

### Tooling Choices
The academic curriculum leaned heavily on R programming. I studied R enough to pass the Coursera requirements, but it never felt natural. As a process-driven engineer, I pivoted:
*   **SQL (BigQuery)** for data cleaning and restructuring
*   **Tableau** for visualization and storytelling
*   **Python** as my long-term language of choice for automation, freelance consulting, and payroll systems architecture

This project is both a technical capstone and a personal milestone—proof that resilience, clarity, and intentional design can turn career setbacks into opportunities for growth.

---

## 📊 1. Executive Summary & Analytical Intent
This people analytics framework models voluntary attrition and payroll risk exposure across three regional Global Shared Services (GSS) operating branches in Metro Manila:
*   **Alabang Shared Services Data Center** (via SLEX Southern Line)
*   **BGC Regional Operations Hub** (via EDSA/Kalayaan)
*   **Makati Corporate GSS Head Office** (via Gil Puyat Ave)

Traditional HR frameworks fail by merely reporting surface-level turnover headcounts. This case study applies advanced workforce accounting to isolate **Indirect Wage Compression** and **Forced Overtime Cash Leaks**. By monitoring timesheet behavior against macroeconomic indicators, this model provides data-driven evidence to show corporate executive boards exactly when delaying a recruitment budget costs a firm more money than filling a vacancy.

---

## 📈 2. Macroeconomic & Industry Anchor Benchmarks
To ground this simulation in real-world market constraints, the data environment incorporates localized economic thresholds:
1.  **PSA Core Inflation Shock:** Mapped directly to the Philippine Statistics Authority (PSA) benchmark pacing at **4.4%**, which erodes employee purchasing power.
2.  **Statutory Wage Floor Pressure:** Calibrated against the Department of Labor and Employment (DOLE) National Capital Region (NCR) mandated non-agriculture minimum wage of **₱780.00/day**. 
3.  **Financial Index Conversion:** All baseline conversions utilize the official Bangko Sentral ng Pilipinas (BSP) market closing rate of **₱60.93 per US Dollar**.
4.  **Operational Labor Divisor:** Baseline hourly payroll rates are calculated using the legally compliant Monday-to-Friday DOLE 261-day annual factor, yielding an industry-standard **174-hour monthly payroll divisor**.

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
*   **The Problem:** The initial analytics summary query crashed inside the Google Cloud Sandbox console, throwing a strict syntax error: `No matching signature for operator = for argument types: BOOL, STRING`. 
*   **The Cause:** BigQuery's automatic ingestion engine auto-detected the raw `Attrition` and `OverTime` columns as boolean checkbox values (`TRUE/FALSE`). The baseline code, however, processed them as standard string parameters (`'Yes'/'No'`), causing the server compiler to fail.
*   **The Resolution:** Applied an absolute Data Normalization layer directly inside the inner SQL subquery using explicit type casting expressions (`CAST(Attrition AS STRING)`) to convert checkboxes cleanly into uniform string arrays, bypassing the data type conflict completely.

### Conflict 2: Core Hardware Utilization Bottlenecks
*   **The Problem:** Local system performance severely degraded during multi-workspace development, with background browser processes pinning system memory at a sustained **89% resource limit on a standard 8GB RAM local drive**. 
