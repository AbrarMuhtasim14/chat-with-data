# 🏨 AtliQ Hospitality — Enterprise AI Analytics Platform

> An enterprise-grade "Chat with Your Data" system that enables stakeholders to query hotel performance data in plain English, backed by a deterministic metrics engine, real-time KPI monitoring, and automated anomaly detection.

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)
![Streamlit](https://img.shields.io/badge/Streamlit-1.30+-FF4B4B?logo=streamlit)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Supabase-4169E1?logo=supabase)
![LLM](https://img.shields.io/badge/LLM-Grok%204.1-000000?logo=x)
![License](https://img.shields.io/badge/License-MIT-green)

<p align="center">
  <a href="#-live-demo">Live Demo</a> •
  <a href="#-features">Features</a> •
  <a href="#%EF%B8%8F-architecture">Architecture</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-kpi-reference">KPI Reference</a>
</p>

---

## 🎯 Live Demo

🔗 **[Launch App on Streamlit Cloud](https://YOUR_USERNAME-atliq-hospitality.streamlit.app)**

---

## 📌 Problem Statement

In large hospitality organizations, performance data is spread across multiple tables with complex relationships. Business users need answers to questions like:

- *"What is our RevPAR for luxury hotels in Mumbai this month?"*
- *"Which booking platform has the highest cancellation rate?"*
- *"Compare week 25 vs week 29 occupancy across cities"*

Traditionally, each question requires an analyst to manually write SQL, build a report, and deliver it — a process that takes hours per query and doesn't scale.


## 🛠️ Tech Stack
Component	Technology
Frontend	Streamlit 1.30+
Visualization	Plotly (interactive charts)
Database	Supabase (managed PostgreSQL)
ETL	Python + pandas + psycopg2
AI/LLM	LiteLLM + OpenRouter (model-agnostic)
LLM Model	Grok 4.1 Fast (swappable)
SQL Builder	Custom deterministic engine
Deployment	Streamlit Community Cloud
Version Control	Git + GitHub

**This platform solves that by enabling anyone to ask business questions in plain English and receive accurate, data-driven answers in seconds.**



---

## ✨ Features

### 📊 Executive Dashboard
- **6 real-time KPI cards** with live Week-over-Week deltas
- **Multi-dimensional filtering** — City, Category, Room Class, Month, Week
- **Revenue by Category** — Luxury vs Business donut chart
- **Weekly Revenue Trend** — Interactive line chart
- **Weekend vs Weekday** — Performance comparison table
- **Realisation % & ADR by Platform** — Dual-axis combo chart
- **City Performance** — Horizontal bar comparison
- **Weekly Occupancy Trend** — Area chart with time series
- **Property Performance Table** — All 13 KPIs per hotel

### 💬 Chat with Your Data (AI Agent)
- **Natural language queries** — Ask in plain English, get data-driven answers
- **24 built-in KPI metrics** — Deterministic SQL generation (zero syntax errors)
- **Complex query support** — Rankings, comparisons, multi-step analysis
- **Custom SQL fallback** — LLM generates SQL for ad-hoc analytical questions
- **Suggested starter questions** — One-click query templates
- **Formatted business answers** — Currency, percentages, actionable insights

### 🔍 KPI Monitoring & Anomaly Detection
- **4-layer alert system:**
  - Threshold-based alerts (configurable per KPI)
  - Week-over-Week drop detection
  - Consecutive decline detection (3+ week downtrends)
  - Statistical anomaly detection (z-score across properties)
- **Property Health Scoring** — 0-100 score per hotel based on weighted KPI performance
- **Interactive trend analysis** — Select any metric, view with threshold lines
- **Configurable thresholds** — Adjust warning/critical levels per session

---

## 🏗️ Architecture



┌─────────────────────────────────────────────────────────────────────┐
│ FRONTEND (Streamlit) │
│ │
│ ┌──────────────┐ ┌──────────────────┐ ┌───────────────────────┐ │
│ │ Dashboard │ │ Chat with Data │ │ KPI Monitoring │ │
│ │ (KPI Cards, │ │ (Natural Lang │ │ (Anomaly Detection, │ │
│ │ Charts, │ │ Queries, AI │ │ Health Scores, │ │
│ │ Filters) │ │ Agent) │ │ Trend Analysis) │ │
│ └──────┬───────┘ └────────┬─────────┘ └──────────┬────────────┘ │
│ │ │ │ │
└─────────┼───────────────────┼────────────────────────┼──────────────┘
│ │ │
▼ ▼ ▼
┌─────────────────────────────────────────────────────────────────────┐
│ METRICS ENGINE (Single Source of Truth) │
│ utils/metrics_engine.py │
│ │
│ get_core_metrics() │ get_wow_deltas() │ get_property_table() │
│ get_trend_data() │ get_city_comparison() │ get_platform_perf() │
└─────────────────────────────┬───────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│ DETERMINISTIC SQL BUILDER │
│ tools/tools.py │
│ │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ _METRIC_CONFIG (24 metrics) │ │
│ │ _build_metric_sql() → Perfect SQL every time │ │
│ │ _build_wow_sql() → Week-over-Week with window functions │ │
│ │ _assemble_cross_table_query() → CTE-based RevPAR │ │
│ └──────────────────────────────────────────────────────────┘ │
│ │
│ execute_metric_query() → Deterministic (95% of queries) │
│ execute_custom_sql() → LLM-generated (complex ad-hoc) │
│ get_database_context() → Live schema introspection │
└─────────────────────────────┬───────────────────────────────────────┘
│
┌───────────────────┼───────────────────┐
▼ │ ▼
┌──────────────────┐ │ ┌──────────────────┐
│ AI AGENT │ │ │ SUPABASE │
│ agents.py │ │ │ PostgreSQL │
│ │ │ │ │
│ LiteLLM + │ │ │ dim_date │
│ Native Function │──────────┘ │ dim_hotels │
│ Calling │ │ dim_rooms │
│ │ │ fact_bookings │
│ 3 Tools: │───────────────────▶│ fact_aggregated │
│ calculate_metrics │ _bookings │
│ run_custom_sql │ │ │
│ search_metric │ │ ETL Pipeline │
└──────────────────┘ │ (CSV→Raw→Clean) │
└──────────────────┘



### Why This Architecture?

| Design Decision | Rationale |
|---|---|
| **Deterministic SQL Builder** | LLMs can write bad SQL. Building SQL programmatically from metric configs eliminates syntax errors for known KPIs. |
| **Two-path query strategy** | 95% of questions use the reliable builder. Only truly novel questions fall back to LLM-generated SQL. |
| **Never direct-join fact tables** | `fact_bookings` and `fact_aggregated_bookings` have different granularity. Direct joins cause row multiplication. CTEs handle cross-table metrics. |
| **Single metrics engine** | Dashboard, agent, and monitoring all use the same SQL builder. Numbers always match. |
| **Native function calling** | More reliable than text-based ReAct parsing. Structured JSON tool calls work with any LLM. |
| **Separate ETL pipeline** | Raw → Clean transformation with validation. Business rules (weekend = Fri+Sat) applied at ETL layer. |

---

## 📁 Project Structure


atliq-hospitality/
│
├── agents/
│ ├── init.py
│ └── agents.py # AI agent with native function calling
│
├── etl/
│ └── etl_pipeline.py # CSV → Raw DB → Clean DB pipeline
│
├── frontend/
│ ├── dashboard.py # Executive dashboard (main page)
│ └── pages/
│ ├── 02_Chat_with_Data.py # Natural language query interface
│ └── 03_KPI_Monitoring.py # Anomaly detection & health scores
│
├── prompts/
│ └── cot_prompts.py # Chain-of-thought prompt templates
│
├── tools/
│ └── tools.py # Deterministic SQL builder + execution
│
├── utils/
│ ├── config.py # Configuration, schema map, metric library
│ └── metrics_engine.py # Shared metrics API (single source of truth)
│
├── .streamlit/
│ └── config.toml # Streamlit theme & server config
│
├── requirements.txt
├── .gitignore
└── README.md



┌─────────────┐       ┌──────────────┐       ┌─────────────┐
│  dim_date   │       │  dim_hotels   │       │  dim_rooms  │
│─────────────│       │──────────────│       │─────────────│
│ date (PK)   │       │ property_id   │       │ room_id (PK)│
│ mmm_yy      │       │  (PK)        │       │ room_class  │
│ week_no     │       │ property_name │       └──────┬──────┘
│ day_type    │       │ category      │              │
└──────┬──────┘       │ city          │              │
       │              └──────┬───────┘              │
       │                     │                      │
       ▼                     ▼                      ▼
┌────────────────────────┐    ┌──────────────────────────────┐
│ fact_aggregated_       │    │      fact_bookings            │
│     bookings           │    │──────────────────────────────│
│────────────────────────│    │ booking_id (PK)              │
│ property_id (FK)       │    │ property_id (FK)             │
│ check_in_date (FK)     │    │ booking_date                 │
│ room_category (FK)     │    │ check_in_date (FK)           │
│ successful_bookings    │    │ checkout_date                │
│ capacity               │    │ room_category (FK)           │
│                        │    │ booking_platform             │
│ Grain: property +      │    │ booking_status               │
│   date + room_type     │    │ revenue_generated            │
│   (for occupancy)      │    │ revenue_realized             │
└────────────────────────┘    │ ratings_given, no_guests     │
                              │                              │
                              │ Grain: individual booking    │
                              │   (for revenue, ADR, etc.)   │
                              └──────────────────────────────┘




Key Business Rules
Rule	Detail
Weekend	Friday & Saturday (stakeholder-defined, non-standard)
Weekday	Sunday through Thursday
Revenue	Always use revenue_realized (net after cancellation adjustments)
Cancellation	Hotel keeps 40% of revenue_generated, refunds 60%
No Show	Full revenue_generated goes to hotel
Ratings	0 means "not rated" — excluded from averages
week_no	Stored as TEXT — always quote in SQL: '31' not 31
Fact table join	NEVER direct-join both fact tables — different granularity, use CTEs



Coverage
Dimension	Values
Date Range	May – July 2022 (92 days)
Cities	Delhi, Mumbai, Hyderabad, Bangalore
Hotel Categories	Luxury, Business
Room Classes	Standard, Elite, Premium, Presidential
Booking Platforms	MakeYourTrip, LogTrip, Tripster, Direct Online, Direct Offline, Journey, Others
Booking Status	Checked Out, Cancelled, No Show
Weeks	19 – 32



📊 KPI Reference
24 Built-in Metrics
Base Metrics
Metric	Formula	Source
Revenue	SUM(revenue_realized)	fact_bookings
Total Bookings	COUNT(booking_id)	fact_bookings
Total Capacity	SUM(capacity)	fact_aggregated_bookings
Total Successful Bookings	SUM(successful_bookings)	fact_aggregated_bookings
Average Rating	AVG(ratings_given) WHERE rating > 0	fact_bookings
No of Days	COUNT(DISTINCT date)	dim_date
Derived KPIs
Metric	Formula	Description
Occupancy %	Successful Bookings / Capacity × 100	Room utilization rate
ADR	Revenue / Total Bookings	Average revenue per booking
RevPAR	Revenue / Capacity	Revenue per available room (cross-table CTE)
Realisation %	1 − (Cancellation% + No Show%)	Booking-to-stay conversion
Cancellation %	Cancelled / Total Bookings × 100	Booking drop-off rate
No Show Rate	No Shows / Total Bookings × 100	Ghost booking rate
DBRN	Total Bookings / No of Days	Daily booked room nights
DSRN	Total Capacity / No of Days	Daily sellable room nights
DURN	Checked Out / No of Days	Daily utilized room nights
Week-over-Week (WoW) Metrics
Metric	Calculation
Revenue WoW	(Current Week / Previous Week) − 1
Occupancy WoW	Same pattern
ADR WoW	Same pattern
RevPAR WoW	Same pattern (cross-table)
Realisation WoW	Same pattern
DSRN WoW	Same pattern
Breakdown Metrics
Metric	Description
Booking % by Platform	Each platform's share of total bookings
Booking % by Room Class	Each room class's share of total bookings



**

**🤖 AI Agent — How It Works****
**Tool Calling Flow**


User: "What is the RevPAR for luxury hotels in Mumbai?"
  │
  ▼
LLM understands intent
  │
  ▼
LLM calls: calculate_metrics({
    metrics: ["revpar"],
    filters: { city: "Mumbai", category: "Luxury" }
  })
  │
  ▼
Python: _build_metric_sql("revpar", filters)
  → Generates CTE query (cross-table)
  → Executes against Supabase
  → Returns DataFrame
  │
  ▼
LLM receives data, formats business answer:
  "RevPAR for luxury hotels in Mumbai: ₹10,234
   This is 15% above the portfolio average..."

**Example Queries the Agent Handles**


Simple:     "What is the total revenue?"
Filtered:   "Occupancy rate for Delhi luxury hotels in week 27"
Grouped:    "Revenue breakdown by city"
Comparison: "Weekend vs weekday ADR"
WoW:        "How did RevPAR change week over week for week 31?"
Ranking:    "Top 5 hotels by revenue in Mumbai"
Complex:    "For each city, identify the hotel with lowest RevPAR
             in week 27, show the gap to city average"


**** Anomaly Detection System****

**4-Layer Alert Engine**
Layer 1: THRESHOLD ALERTS
  └─ Each KPI checked against configurable warning/critical levels
     Example: Occupancy < 45% → 🔴 Critical

Layer 2: WEEK-OVER-WEEK ALERTS
  └─ Detects significant WoW drops
     Example: Revenue dropped -12% WoW → 🔴 Critical

Layer 3: TREND ALERTS
  └─ Detects consecutive declining weeks
     Example: ADR declined 4 weeks straight → 🔴 Critical

Layer 4: PROPERTY ANOMALY DETECTION (Z-Score)
  └─ Flags properties deviating from portfolio mean
     Example: Hotel X occupancy z-score = -2.3 → 🔴 Critical



👤 Author
Your Name

LinkedIn: your-linkedin
GitHub: your-github
Email: your.email@example.com
📄 License
This project is licensed under the MIT License — see the LICENSE file for details.

<p align="center"> Built with ❤️ for data-driven hospitality management </p> ```
