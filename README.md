# Nifty 50 Equity Market Intelligence & Risk Analytics

An end-to-end financial analytics project analyzing historical Nifty 50 equity data using **SQL Server, Python, and Power BI**.

The project focuses on equity performance, market risk, drawdown, valuation, fundamentals, data quality, and recurring CSV-to-SQL reporting automation.

---

## 📌 Project Overview

This project analyzes historical equity data for Nifty 50 companies to understand:

- Historical price and return performance
- Short- and long-term investment performance
- Volatility and market risk
- Beta and risk classification
- Maximum drawdown
- Distance from 52-week highs and lows
- Company fundamentals
- Valuation metrics
- Dividend yield
- EPS and price-to-book ratios
- Risk versus return relationships
- Automated ingestion of updated CSV files into SQL Server
- Interactive business reporting through Power BI

The project was designed as an **end-to-end analytics workflow**, starting from raw historical data and ending with an interactive Power BI reporting layer.

---

## 🛠️ Tools & Technologies

- **SQL Server** — data ingestion, cleaning, transformation, analytical calculations, stored procedures, views and reporting tables
- **Python** — file monitoring and CSV-to-SQL automation
- **Power BI** — interactive dashboards, KPIs, risk segmentation and financial analysis
- **Pandas / NumPy** — data preparation and analysis
- **PowerShell** — initial automation testing and SQL Server environment setup
- **ODBC Driver 17 for SQL Server** — Python-to-SQL Server connectivity

---

## 📊 Dataset

The main historical dataset contains approximately:

- **287K+ equity records**
- **49 companies**
- Historical data from **1999-01-01 to 2026-01-30**
- Approximately **83.6 MB** raw CSV data

### Main Dataset Columns

```text
Date
Ticker
Company
Sector
Open
High
Low
Close
Volume
Dividend
Stock_Split
Daily_Return
Volatility_20D
MA_50
MA_200
Market_Cap
PE_Ratio
Forward_PE
PEG_Ratio
Price_To_Book
Dividend_Yield
EPS
Beta
Week52_High
Week52_Low

Additional calculated fields were created during the SQL analytics process, including:

Calculated_Daily_Return
Calculated_Volatility_20D
Calculated_MA_50
Calculated_MA_200
Distance_From_52W_High_Pct
Distance_From_52W_Low_Pct
Drawdown_Pct
Return_1Y_Pct
Return_3Y_Pct
Annualized_Volatility_Pct
---

🔄 Project Workflow

Raw Nifty 50 CSV
       ↓
SQL Server Staging
       ↓
Data Cleaning & Validation
       ↓
Historical Data Processing
       ↓
Calculated Performance & Risk Metrics
       ↓
Company-Level Analytics
       ↓
Fundamental & Risk Classification
       ↓
Power BI Reporting View
       ↓
4-Page Power BI Dashboard

For recurring updates:

New CSV File
     ↓
Incoming Folder
     ↓
Python File Watcher
     ↓
SQL Server Stored Procedure
     ↓
Staging → Historical → Clean → Analytics
     ↓
Power BI Refresh

---

🗄️ SQL Server Data Pipeline

The SQL Server layer was designed to separate raw ingestion, cleaning, calculations, and reporting.

Main Tables

NiftyHistorical_Stage

Temporary staging layer used to load incoming CSV data before processing.

NiftyHistorical

Historical equity data stored in SQL Server.

NiftyHistorical_Clean

Cleaned and transformed historical data with calculated analytical fields.

Nifty_Company_Analytics

Company-level analytical table containing current metrics, risk classifications, performance classifications, and fundamental indicators.
---

⚙️ Data Cleaning & Validation

Several data quality checks were performed before analysis.

Key checks included:

Missing-value profiling

Zero-value detection

Invalid OHLC values

Duplicate Ticker-Date combinations

Missing returns

Missing valuation metrics

Missing fundamental metrics

Stock split and dividend availability

52-week high/low validation

Rolling moving-average calculations


Rows containing invalid OHLC values where:

Open <= 0
High <= 0
Low <= 0
Close <= 0

were removed from the cleaned dataset.

Importantly, valid records were not removed simply because unrelated fundamental fields were missing.

---

📈 Calculated Analytics

Daily Return

Daily returns were calculated using the previous trading day's closing price.

Daily Return =
(Current Close - Previous Close)
/
Previous Close × 100
---

Moving Averages

50-day and 200-day rolling moving averages were calculated for each company.

MA 50
MA 200

These were used to analyze longer-term price trends.
---

Annualized Volatility

20-day volatility and annualized volatility were calculated to measure historical price risk.

Annualized volatility was used for company-level risk classification.
---

Maximum Drawdown

Maximum drawdown measures the decline from a previous running peak.

The running maximum close was calculated using a SQL window function:

MAX(Close) OVER (
    PARTITION BY Ticker
    ORDER BY Date
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)

Drawdown was then calculated as:

Drawdown =
(Current Close - Running Peak)
/
Running Peak × 100

For example:

Peak Price = 100
Current Price = 80

Drawdown = -20%
---

1-Year & 3-Year Returns

Company-level 1-year and 3-year returns were calculated to compare historical performance across companies.
---

Distance From 52-Week High

This metric measures how far the current price is below the company's 52-week high.

This helps identify companies trading significantly below their recent peak.
---

⚠️ Risk Classification

Companies were classified into risk bands using volatility and beta-related metrics.

The dashboard uses:

Low Risk
Medium Risk
High Risk

Risk classification was then used throughout the Power BI dashboard to compare:

Volatility

Beta

Returns

Drawdowns

Performance bands
---

📊 Company Analytics

A company-level reporting view was created:

vw_Nifty_Company_Dashboard

The view contains one row per company and provides the current company-level metrics required by Power BI.

The final view contains:

49 companies

This reporting view separates company-level dashboard metrics from the detailed historical price table.
---

📊 Power BI Dashboard

The final Power BI report contains 4 pages.
---

1️⃣ Market Overview

The Market Overview page provides a high-level summary of the analyzed companies.

KPIs

Total Companies

Combined Market Capitalization

Average 1-Year Return

Average Annualized Volatility

Average Beta

Average P/E Ratio


Visuals

Top 10 Companies by 1-Year Return

Company Distribution by Risk Band

Company Distribution by Sector

1-Year Return by Sector

Market Capitalization by Sector


Key Observations

49 companies were included in the final company-level dashboard.

Medium Risk was the largest risk category with 24 companies.

The Finance sector had the highest number of companies in the analyzed dataset.

Telecom had the highest aggregate market capitalization in the dashboard's sector comparison.
---

2️⃣ Equity Performance

This page focuses on historical price performance and return analysis.

KPIs

Ending Price

Highest Price

Lowest Price

Overall Price Return %


Visuals

Historical Closing Price & Moving Averages

Displays:

Closing Price

50-Day Moving Average

200-Day Moving Average


A company slicer allows individual companies to be analyzed over time.

Daily Stock Returns

Shows the historical daily return pattern for the selected company.

1-Year vs 3-Year Return

Compares short- and medium-term company performance.

Risk vs Average Daily Return

A scatter plot compares:

Average Daily Return

Annualized Volatility

Market Capitalization

Risk Band


The visualization uses average return and average volatility as benchmarks to divide companies into four analytical quadrants.

Maximum Drawdown by Company

Compares maximum historical drawdown across companies.
---

3️⃣ Market Risk

This page focuses on risk characteristics.

KPIs

Average Beta

Average Annualized Volatility

Worst Drawdown

High Risk Companies

Low Beta Companies


Visuals

Average Volatility by Risk Band

Average Beta byb Sector

Distance From 52-Week High

Performance Band Distribution by Risk Level


Key Risk Findings

High Risk companies: 9

Low Beta companies: 48

Average volatility by risk band:

High Risk: 37.70%

Medium Risk: 24.05%

Low Risk: 17.28%



Risk vs Return Observation

Bajaj Finance appeared in the lower-risk/higher-return quadrant based on the project's calculated metrics, with:

Annualized Volatility: 21.76%
Average Daily Return: 2.00%

Coal India appeared in the higher-risk/lower-return quadrant:

Annualized Volatility: 40.28%
Average Daily Return: 0.05%

These observations describe historical dataset relationships and are not investment recommendations.
---

4️⃣ Fundamentals & Data Insights

This page focuses on company fundamentals and valuation.

KPIs

Average EPS

Average P/E Ratio

Average Forward P/E

Average Dividend Yield

Average Price-to-Book Ratio

Average Market Capitalization


Visuals

P/E vs Forward P/E by Company

EPS by Company

Dividend Yield by Company

Price-to-Book Ratio by Company

EPS vs P/E Ratio
---

📌 Key Business & Analytical Findings

Performance

Highest 3-Year Return

Bajaj Auto

3-Year Return: 180.71%

Lowest 3-Year Return

Adani Enterprises

3-Year Return: -47.10%

Lowest 1-Year Return

ITC

1-Year Return: -24.53%
---

⚠️ Maximum Drawdown Findings

Largest Maximum Drawdown

Bajaj Finance

-99.84%

Second Largest Maximum Drawdown

Bajaj Finserv

-99.73%

These values represent historical maximum drawdowns calculated from the dataset and should not be interpreted as current investment recommendations.
---

💰 Fundamental Findings

Highest EPS

Shree Cement

EPS: 477.63

Highest Average P/E

Titan

Average P/E: 85.46

Highest Dividend Yield

Wipro

Dividend Yield: 7.18%

Lowest Dividend Yield

Bajaj Finserv

Dividend Yield: 0.05%

Highest Price-to-Book Ratio

Nestle India

Price-to-Book: 57.94

Lowest Price-to-Book Ratio

ONGC

Price-to-Book: 0.92
--

🔎 EPS vs P/E Analysis

The EPS vs P/E scatter plot compares earnings strength against valuation.

The dashboard divides companies using average EPS and average P/E benchmarks.

The four analytical quadrants are:

High P/E  + Low EPS
High P/E  + High EPS
Low P/E   + Low EPS
Low P/E   + High EPS

For example:

Shree Cement

EPS: 477.63
P/E: 56.50

Shree Cement appears in the higher-earnings/higher-valuation quadrant.

A lower P/E combined with higher EPS can indicate a potentially interesting valuation relationship, but the analysis does not automatically classify a company as undervalued.
---

🤖 Python CSV Automation

A Python file watcher was developed to automate recurring CSV ingestion.

Folder Structure

C:\Nifty50_Automation
│
├── Incoming
│
├── Processed
│
└── Nifty50_Automation.py

Workflow

CSV copied to Incoming
          ↓
Python detects CSV
          ↓
File stability check
          ↓
Python connects to SQL Server
          ↓
usp_Run_Nifty_Pipeline
          ↓
SQL ingestion & transformation
          ↓
Successful processing
          ↓
CSV moved to Processed

The Python watcher checks the Incoming folder every 30 seconds.
---

🧩 SQL Stored Procedure Pipeline

The master procedure is:

dbo.usp_Run_Nifty_Pipeline

It coordinates the complete SQL processing workflow.

Step 1
Load CSV into staging

Step 2
Insert new historical records

Step 3
Clean data and calculate metrics

Step 4
Update company analytics

Step 5
Update fundamentals and risk/performance bands

The master procedure calls the underlying SQL procedures responsible for each stage.
---

🔄 Power BI Refresh Architecture

Power BI does not directly depend on the incoming CSV.

The reporting architecture is:

CSV
 ↓
Python
 ↓
SQL Server
 ↓
vw_Nifty_Company_Dashboard
 ↓
Power BI

Power BI uses Import mode but reads the SQL Server reporting layer.

When new data is processed into SQL Server, refreshing the Power BI report retrieves the updated SQL data.

This allows the report to remain connected to the processed analytical data rather than storing a separate direct CSV copy.
---

📐 Power BI Data Model

The final Power BI model uses:

vw_Nifty_Company_Dashboard
          │
          │ 1 : *
          ↓
NiftyHistorical_Clean

The relationship is based on:

Company Name

The Company Name slicer is sourced from:

vw_Nifty_Company_Dashboard

This allows company-level selections to filter the historical dataset used by the performance visuals.
--

📁 Project Structure

A suggested GitHub repository structure:

Nifty50-Equity-Analytics/
│
├── README.md
│
├── data/
│   └── sample_data/
│
├── sql/
│   ├── 01_Create_Database.sql
│   ├── 02_Create_Tables.sql
│   ├── 03_Data_Load.sql
│   ├── 04_Data_Cleaning.sql
│   ├── 05_Performance_Analytics.sql
│   ├── 06_Risk_Analytics.sql
│   ├── 07_Fundamental_Analytics.sql
│   ├── 08_Company_Dashboard_View.sql
│   └── 09_Master_Pipeline.sql
│
├── python/
│   └── Nifty50_Automation.py
│
├── powerbi/
│   └── Nifty50_Equity_Analytics.pbix
│
└── screenshots/
    ├── market_overview.png
    ├── equity_performance.png
    ├── market_risk.png
    └── fundamentals.png

> The full historical CSV is not included in the repository because of its size and data-source/licensing considerations.
---

🧪 Data Quality Findings

Several data quality issues were identified during the project.

Missing Values

Examples included missing values in:

Stock split

Daily return

Volatility

50-day moving average

200-day moving average

P/E

PEG

Dividend yield

Beta


Missing Fundamental Coverage

Some companies had incomplete fundamental data for certain metrics.

For example, PEG ratio had extremely limited coverage in the historical dataset.

Historical Gaps

Some tickers contained periods with missing historical observations.

Examples included:

NESTLEIND.NS
TCS.NS
BAJAJ-AUTO.NS
LT.NS
JSWSTEEL.NS

These were investigated during the data-quality stage rather than blindly filling or deleting all missing records.
---

🎯 Project Objectives Achieved

The project successfully demonstrates:

Raw CSV ingestion into SQL Server

Staging-table architecture

Data-quality profiling

Data cleaning and validation

SQL window functions

Rolling calculations

Historical return analysis

Volatility analysis

Beta analysis

Maximum drawdown analysis

Fundamental analysis

Valuation analysis

Risk classification

Company-level analytical views

Power BI dashboard development

Python-to-SQL automation

Recurring CSV processing

SQL stored procedure orchestration

Power BI refresh from SQL Server
---

📌 Key Takeaways

The project demonstrates how raw financial market data can be transformed into an automated analytical reporting workflow.

The main analytical outcomes include:

287K+ historical equity records analyzed

49 companies covered

Historical performance comparison across 1-year and 3-year periods

Risk analysis using volatility, beta and drawdown

Fundamental analysis using EPS, P/E, Forward P/E, dividend yield and price-to-book

Interactive Power BI reporting across four analytical pages

Automated CSV-to-SQL processing using Python and SQL Server stored procedures
---

⚠️ Limitations

The analysis is based on historical market data.

Historical performance does not guarantee future performance.

Missing fundamental data may affect some company comparisons.

Risk classifications are based on calculated project metrics and should not be treated as investment recommendations.

The project focuses on analytical reporting rather than real-time trading or portfolio optimization.

The automation currently monitors a local Windows folder and requires the Python watcher to be running.
---

🚀 Future Improvements

Potential extensions include:

Scheduled cloud-based data ingestion

Automated Power BI dataset refresh

Real-time or near-real-time market data integration

Additional portfolio-level risk metrics

Sharpe ratio and Sortino ratio analysis

Sector benchmark comparison

Automated data-quality alerts

Cloud deployment using Azure services

Incremental data loading instead of processing full files

Automated email/report notifications
-

👤 Author

Ojasvi Goel

BBA Finance Graduate | Aspiring Data Analyst

Core Skills

SQL Server
Python
Power BI
Advanced Excel
Data Cleaning
Data Analysis
ETL
Data Visualization
Financial Analytics

⭐ Project Highlights

287K+ records | 49 companies | SQL Server | Python Automation | Power BI | Risk Analytics | Financial Analytics
