# Maternal Mortality in Kenya — A Multi-Tool Data Analysis (KDHS 2022)

> An end-to-end analysis of **6,000 delivery records** identifying the clinical, socioeconomic, and geographic drivers of maternal death — and where intervention saves the most lives. Built with **SQL · Python · Power BI**.

![Place of delivery](visualizations/02_place_of_delivery.png)

---

## Table of Contents
- [Executive Summary](#executive-summary)
- [The Data](#the-data)
- [Methodology](#methodology)
- [Tools Used](#tools-used)
- [Key Findings](#key-findings)
- [Recommendations](#recommendations)
- [Repository Structure](#repository-structure)
- [How to Reproduce](#how-to-reproduce)
- [Caveats & Honesty Notes](#caveats--honesty-notes)

---

## Executive Summary

Maternal mortality in this cohort runs at **101 deaths per 1,000 deliveries (10.1%)** — but that headline hides enormous variation. The analysis isolates three forces that move the number:

1. **Where and with whom a woman delivers is decisive.** Skilled attendance cuts mortality by **43%** (82 vs 143 per 1,000). Home births are nearly **2× deadlier** than public-facility births.
2. **Education protects; wealth does not — at the top.** Mortality falls cleanly with schooling (175 → 55 per 1,000), yet the **richest quintile (Q5) is the deadliest of all** (122 per 1,000) — a genuine paradox explored below.
3. **Geography is destiny.** Arid northern counties — **West Pokot, Marsabit, Tana River, Turkana** — carry **2–2.5× the national rate**, and individual distance-to-care tracks mortality even where county-level aggregates do not.

The practical implication: the highest-yield interventions are **demand-side (getting women into skilled, facility-based care)** and **geographically targeted**, not simply income transfers.

---

## The Data

| Attribute | Detail |
|---|---|
| **Source** | Kenya Demographic and Health Survey (KDHS) 2022 — maternal mortality extract |
| **Records** | 6,000 individual delivery episodes |
| **Counties** | 47 (full national coverage) |
| **Outcome** | `maternal_death` (Yes / No) |
| **Features** | 21 — clinical (ANC visits, C-section, skilled attendant, place of delivery), socioeconomic (education, wealth quintile, marital status), and structural/county-level (distance to facility, SBA coverage, poverty rate, facility density, HIV prevalence) |
| **Data quality** | 0 duplicate rows; 596 missing `education` values handled explicitly (see caveats) |

---

## Methodology

The project follows a transparent, reproducible pipeline:

1. **Extraction & cleaning (SQL).** A typed, validated view (`v_clean`) coerces the Yes/No outcome to a binary, standardises categorical casing, applies plausibility guardrails, and treats missing education as an explicit band.
2. **Exploratory analysis (Python — Pandas).** Univariate profiling, group-wise mortality rates, and correlation analysis across clinical and structural factors.
3. **Visualization (Matplotlib / Seaborn).** Ten publication-grade, colorblind-safe figures, each exported at 300 DPI.
4. **Business intelligence layer (Power BI).** A county-level aggregate feeds an interactive dashboard with DAX measures for rate, risk ranking, and benchmark deviation.
5. **Sensitivity & honesty checks.** Every headline claim was tested against alternative data treatments before being reported.

---

## Tools Used

| Tool | Role in this project |
|---|---|
| **SQL (PostgreSQL)** | Extraction, cleaning, CTEs, window functions (`RANK`, `NTILE`, `LAG`, `FIRST_VALUE`), aggregate export view |
| **Python — Pandas** | Data wrangling, EDA, rate computation, missing-data sensitivity |
| **Matplotlib & Seaborn** | Ten exported `.png` visualizations, Okabe–Ito colorblind-safe palette |
| **Power BI** | Interactive county dashboard with custom DAX measures |
| **Jupyter** | Narrative analysis notebook (storytelling + reproducible code) |

---

## Key Findings

### 1 · The Clinical Impact — skilled care is the single biggest lever
![Skilled attendant impact](visualizations/01_skilled_attendant_impact.png)

Deliveries with a skilled attendant die at **82 per 1,000**; without one, **143** — a **1.7× risk ratio**. Layering in place of delivery, **home births (146)** are the deadliest setting, while **public facilities (78)** outperform even **private ones (91)**.

![ANC visits](visualizations/08_anc_visits.png)

Antenatal care reinforces this: women who meet the **WHO-recommended minimum of 4+ ANC visits** die at roughly **79 per 1,000** versus **123** for those with 1–3 visits. (ANC visits in this dataset range 1–6.)

![Missing data sensitivity](visualizations/09_missing_data_sensitivity.png)

The education gradient is robust to how the 596 missing education values are handled — treating them as a "No education" band (A) or dropping them (B) leaves Higher education as the lowest-risk group either way.

### 2 · The Socioeconomic Paradox
![Education gradient](visualizations/03_education_gradient.png)

Education shows a clean, monotonic protective gradient: **No education 175 → Primary 119 → Secondary 79 → Higher 55** per 1,000 — a **68% reduction** end to end.

![Wealth quintile anomaly](visualizations/04_wealth_quintile_anomaly.png)

Wealth tells the opposite story at the extreme. The **richest quintile (Q5) records the highest mortality of all (122 per 1,000)** — above the poorest (Q1, 84). Plausible explanations:
- **High-risk private referrals:** complicated pregnancies among wealthier women are concentrated in private facilities, raising their observed rate.
- **Reporting / detection effects:** deaths among wealthier, facility-connected women are more completely recorded.
- **Non-linear access:** poorer women in this sample are not uniformly the worst off, blunting the expected wealth gradient.

This is presented as a hypothesis-generating anomaly, not a causal claim.

### 3 · Geographic & Systemic Bottlenecks
![High-risk counties](visualizations/06_high_risk_counties.png)

Mortality is heavily concentrated. The four priority counties — **West Pokot (252), Marsabit (241), Tana River (223), Turkana (213)** — sit at 2–2.5× the national average, all in arid/marginalised northern Kenya.

![Distance gradient](visualizations/05_distance_gradient.png)

At the **individual** level, distance to care matters: women **41–50 km** from a facility die at **129 per 1,000** versus **84** for those within 20 km (a 1.5× gap). Distance in this dataset ranges 5–50 km.

![County distance scatter](visualizations/10_county_distance_scatter.png)

A nuance worth flagging honestly: at the **county-aggregate** level this distance signal washes out (r ≈ 0.00). The effect is **individual, not ecological** — a distinction that separates a careful analysis from a misleading one.

![Correlation heatmap](visualizations/07_correlation_heatmap.png)

---

## Recommendations

1. **Prioritise skilled, facility-based delivery** through demand-side incentives and transport support — the largest single mortality lever in the data.
2. **Geographically target the four priority counties** rather than spreading resources evenly; concentrate emergency obstetric capacity where rates are 2–2.5× the mean.
3. **Audit private-facility referral pathways** to explain and address the Q5 anomaly, rather than assuming wealth is protective.
4. **Protect ANC continuity** to the WHO-recommended minimum of 4+ contacts.
5. **Invest in last-mile access** for women living 40+ km from care, where individual risk rises sharply.

---

## Repository Structure

```
maternal_mortality_kdhs2022/
├── README.md
├── data/
│   └── kdhs_2022_data_maternal_mortality1.csv
├── sql/
│   └── data_extraction.sql
├── notebooks/
│   └── maternal_health_analysis.ipynb
├── visualizations/
│   ├── 01_skilled_attendant_impact.png
│   ├── 02_place_of_delivery.png
│   ├── 03_education_gradient.png
│   ├── 04_wealth_quintile_anomaly.png
│   ├── 05_distance_gradient.png
│   ├── 06_high_risk_counties.png
│   ├── 07_correlation_heatmap.png
│   ├── 08_anc_visits.png
│   ├── 09_missing_data_sensitivity.png
│   └── 10_county_distance_scatter.png
├── powerbi/
│   └── PowerBI_Dashboard_Guide.md
└── docs/
    └── GitHub_Publishing_Guide.pdf
```

---

## How to Reproduce

```bash
# 1. Clone
git clone https://github.com/<your-username>/maternal_mortality_kdhs2022.git
cd maternal_mortality_kdhs2022

# 2. Environment
pip install pandas numpy matplotlib seaborn jupyter

# 3. Regenerate every figure
python generate_visuals.py        # writes 10 PNGs to ./visualizations/

# 4. Or open the full narrative notebook
jupyter notebook notebooks/maternal_health_analysis.ipynb
```

The SQL in `sql/data_extraction.sql` reproduces every headline metric directly against a PostgreSQL load of the CSV.

---

## Caveats & Honesty Notes

A portfolio is only as credible as its weakest claim, so the limitations are stated plainly:

- **Cross-sectional & observational.** All findings are associations, not causal effects. No adjustment for confounding is implied by the descriptive rates.
- **The Q5 paradox is a hypothesis, not a conclusion.** Referral and reporting mechanisms are proposed, not proven.
- **Distance is an individual effect.** It does **not** hold at county aggregation (r ≈ 0.00); reporting it as a county correlation would be misleading, so it isn't.
- **Missing education (n=596)** is treated as a "No education" band in the headline figures, with a dropped-rows sensitivity check (Figure 9) confirming the gradient is robust either way.
- **Rate magnitudes** reflect this analytical extract and are used for comparison within the dataset.
