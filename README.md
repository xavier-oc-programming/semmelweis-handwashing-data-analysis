# Semmelweis Handwashing Data Analysis

Statistical analysis of 1840s Vienna hospital data quantifying the life-saving impact of Dr Semmelweis's mandatory handwashing policy.

This project re-analyses the original hospital records published by Dr Ignaz Semmelweis in 1861 to answer a single question: did mandatory handwashing actually save lives? Using monthly and yearly mortality data from two maternity clinics at Vienna General Hospital (1841–1849), the analysis quantifies the reduction in maternal deaths from childbed fever before and after Dr Semmelweis introduced chlorine handwashing in June 1846. A t-test confirms the reduction is statistically significant at p < 0.01 — there is less than a 1% probability the improvement was due to chance.

The datasets contain monthly and yearly birth and death counts recorded across two clinics. The yearly data distinguishes Clinic 1 (staffed by doctors and medical students who also performed autopsies) from Clinic 2 (staffed by midwives with no autopsy contact). This structural difference is the key to understanding why one clinic was dramatically more deadly. The analysis pipeline walks from raw counts through percentage computation, time-series visualisation, rolling averages, distribution plots, and a formal hypothesis test.

No external services or APIs are required. All data is committed directly to this repository from Dr Semmelweis's published research, digitised from the original German tables.

---

## Table of Contents

1. [Quick start](#1-quick-start)
2. [Analysis flow](#2-analysis-flow)
3. [Features](#3-features)
4. [Dataset schema](#4-dataset-schema)
5. [Architecture](#5-architecture)
6. [Notebook reference](#6-notebook-reference)
7. [Configuration reference](#7-configuration-reference)
8. [Course context](#8-course-context)
9. [Dependencies](#9-dependencies)

---

## 1. Quick start

```bash
git clone https://github.com/xavier-oc-programming/semmelweis-handwashing-data-analysis.git
cd semmelweis-handwashing-data-analysis
pip install -r requirements.txt
jupyter notebook
```

Open `practice/A_01_Preliminary_Exploration.ipynb` to begin the analysis.

---

## 2. Analysis flow

```
pipeline
    │
    │  ── [Ingestion] ────────────────────────────────────────────────
    ├── pd.read_csv()  →  annual_deaths_by_clinic.csv  →  df_yearly
    ├── pd.read_csv()  →  monthly_deaths.csv           →  df_monthly
    │
    │  ── [Exploration] ──────────────────────────────────────────────
    ├── .shape / .info() / .describe()  →  structure and stats
    ├── .duplicated() / .isna()         →  data quality check
    ├── prob = deaths.sum() / births.sum()  →  overall mortality rate
    │
    │  ── [Transformation] ───────────────────────────────────────────
    ├── df['pct_deaths'] = deaths / births        →  monthly death rate
    ├── pd.to_datetime()                          →  parse date column
    ├── boolean filter on date                   →  before / after split
    ├── np.where()                               →  washing_hands label
    ├── .set_index('date').rolling(6).mean()     →  6-month moving avg
    │
    │  ── [Insight] ──────────────────────────────────────────────────
    ├── Clinic comparison  →  Clinic 1 death rate 3× higher than Clinic 2
    ├── Handwashing split  →  ~10.5% → ~5.0% average monthly death rate
    ├── t-test (p < 0.01) →  reduction is statistically significant
    │
    │  ── [Visualisation] ────────────────────────────────────────────
    └── twin-axis line / plotly line+box+histogram / seaborn KDE  →  charts
```

---

## 3. Features

- Calculates overall probability of dying during childbirth in 1840s Vienna (~10%)
- Compares yearly death rates between Clinic 1 (doctors) and Clinic 2 (midwives)
- Plots monthly births and deaths on twin y-axes over the full 1841–1849 period
- Splits data at June 1846 and computes average death rates before and after handwashing
- Overlays a 6-month rolling average to smooth monthly variation
- Box plot, histogram, and KDE visualisations show the distributional shift
- Independent-samples t-test confirms statistical significance (p < 0.01)

---

## 4. Dataset schema

### `data/annual_deaths_by_clinic.csv`

| Column | Type | Description |
|--------|------|-------------|
| year | int | Calendar year (1841–1846) |
| births | int | Total births at that clinic that year |
| deaths | int | Total maternal deaths that year |
| clinic | string | `clinic 1` (doctors) or `clinic 2` (midwives) |

### `data/monthly_deaths.csv`

| Column | Type | Description |
|--------|------|-------------|
| date | date | First day of the month (1841-01-01 to 1849-03-01) |
| births | int | Total births that month |
| deaths | int | Total maternal deaths that month |

**Computed columns added at runtime:**

| Column | Added to | Description |
|--------|----------|-------------|
| pct_deaths | df_yearly, df_monthly | deaths / births — proportion of patients who died |
| washing_hands | df_monthly | `'No'` before June 1846, `'Yes'` after |

---

## 5. Architecture

```
semmelweis-handwashing-data-analysis/
│
├── theory/                          # Lesson notes with annotated methods
│   ├── 00__Overview.ipynb           # Day 80 goals and project context
│   ├── 01__Preliminary_Exploration.ipynb  # Data loading and exploration concepts
│   ├── 02__Yearly_Data_By_Clinic.ipynb    # Clinic comparison methods
│   ├── 03__Effect_of_Handwashing.ipynb    # Rolling averages and subsetting
│   ├── 04__Distributions_and_Statistical_Significance.ipynb  # Box plots, KDE, t-test theory
│   └── 05__Summary.ipynb            # Key findings and reflection
│
├── practice/                        # Student's working notebooks
│   ├── A_01_Preliminary_Exploration.ipynb   # Explore data, compute mortality, twin-axis chart
│   ├── A_02_Yearly_Data_By_Clinic.ipynb     # Compare clinics with plotly, add pct_deaths
│   ├── A_03_Effect_of_Handwashing.ipynb     # Split data, rolling avg, highlight chart
│   └── A_04_Distributions_and_Testing.ipynb # Box plot, histogram, KDE, t-test
│
├── data/
│   ├── annual_deaths_by_clinic.csv  # Yearly births and deaths by clinic (1841–1846)
│   └── monthly_deaths.csv           # Monthly births and deaths (1841–1849)
│
├── docs/
│   └── COURSE_NOTES.md              # Original exercise brief and key concepts
│
├── requirements.txt                 # Pinned pip dependencies
├── .gitignore
└── README.md
```

---

## 6. Notebook reference

### theory/

| Notebook | Key methods covered | Question answered |
|----------|--------------------|--------------------|
| 00__Overview.ipynb | — | What will this project build? |
| 01__Preliminary_Exploration.ipynb | `.shape`, `.info()`, `.describe()`, `twinx()` | How to explore and visualise raw hospital data |
| 02__Yearly_Data_By_Clinic.ipynb | `px.line`, boolean filter, column arithmetic | Why was one clinic deadlier than the other? |
| 03__Effect_of_Handwashing.ipynb | `pd.to_datetime()`, `.rolling()`, `set_index()` | How do we isolate the handwashing effect? |
| 04__Distributions_and_Statistical_Significance.ipynb | `px.box`, `px.histogram`, `sns.kdeplot`, `ttest_ind` | Is the improvement statistically real? |
| 05__Summary.ipynb | — | What did we learn and what does it mean historically? |

### practice/

| Notebook | Key methods used | What it answers |
|----------|-----------------|-----------------|
| A_01_Preliminary_Exploration.ipynb | `.shape`, `.info()`, `.duplicated()`, `ax.twinx()`, `mdates` | What does the raw data look like? How did births and deaths trend? |
| A_02_Yearly_Data_By_Clinic.ipynb | `px.line`, `df['pct_deaths'] = ...`, boolean filter | Which clinic was more dangerous and by how much? |
| A_03_Effect_of_Handwashing.ipynb | `pd.to_datetime()`, boolean split, `.rolling(6).mean()` | How large was the drop in deaths after handwashing started? |
| A_04_Distributions_and_Testing.ipynb | `px.box`, `px.histogram`, `sns.kdeplot`, `scipy.stats.ttest_ind` | Is the reduction statistically significant? |

---

## 7. Configuration reference

| Value | Location | Description |
|-------|----------|-------------|
| `'../data/annual_deaths_by_clinic.csv'` | practice/ notebooks | Relative path to yearly dataset |
| `'../data/monthly_deaths.csv'` | practice/ notebooks | Relative path to monthly dataset |
| `pd.to_datetime('1846-06-01')` | A_03, A_04 | Handwashing start date |
| `window=6` | A_03 | Rolling average window in months |
| `'{:,.2f}'.format` | all notebooks | Float display format for pandas output |
| `dpi=200` | Matplotlib figures | Output resolution |

---

## 8. Course context

100 Days of Code — The Complete Python Pro Bootcamp · Day 80 · Topics: Pandas, Matplotlib, Plotly, Seaborn, SciPy statistics

→ [docs/COURSE_NOTES.md](docs/COURSE_NOTES.md)

---

## 9. Dependencies

| Module | Used in | Purpose |
|--------|---------|---------|
| pandas | all notebooks | DataFrames, CSV I/O, date parsing, rolling averages |
| numpy | practice/ | `np.where()` for categorical labelling |
| matplotlib | A_01, A_03 | Twin-axis line charts, time axis formatting |
| seaborn | A_04 | KDE plots with clipping |
| plotly | A_02, A_03, A_04 | Interactive line, box, and histogram charts |
| scipy | A_04 | `stats.ttest_ind()` — independent samples t-test |
| notebook | — | Jupyter notebook server |
