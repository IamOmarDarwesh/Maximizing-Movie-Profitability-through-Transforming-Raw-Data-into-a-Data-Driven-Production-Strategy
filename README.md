
# Spectacular Studios - Movie Profitability Analysis

### Maximizing Movie Profitability Through Data Analytics & BI  -  Transforming raw IMDb movie data into a strategic Data-Driven Production Strategy & strategic production recommendations for executive decision-makers.

<br>
<br>

> *What combination of genre, budget, runtime, language, and release timing maximizes movie profit?*
> This project answers that question with data - and translates the findings into an executive strategy.

<br>
<br>

## What This Project Does

A data pipeline and analysis built for **Spectacular Studios** using thousands of commercially released films from IMDb.

The pipeline takes five raw CSV tables, merges and cleans them, engineers analytical features, and produces a single analysis-ready dataset. The analysis then identifies which production and release decisions drive profitability  -  and which combinations to avoid.

The final output is an executive presentation with findings, recommendations, and a full limitations and bias audit.

---

<br>
<br>

## Table of Contents

1. [Project Structure](#1-project-structure)
2. [How to Run](#2-how-to-run)
3. [The Data](#3-the-data)
4. [The Pipeline](#4-the-pipeline)
5. [Key Findings](#5-key-findings)
6. [Recommendations](#6-recommendations)
7. [Limitations & Biases](#7-limitations--biases)
8. [Next Steps](#8-next-steps)

---

<br>
<br>

## 1. Project Structure

```
spectacular-studios-movie-analysis/
│
├── 📓 movie_profitability_pipeline.ipynb   ← Run this. The full pipeline, start to finish.
│
├── 📁 data/
│   ├── raw/               ← source files
│   └── processed/         ← Pipeline outputs are saved here automatically
│
├── requirements.txt
└── README.md
```

The entire pipeline lives in **one notebook**. Open it, place your CSVs in `data/raw/`, and run top to bottom.

---

<br>
<br>

## 2. How to Run

**Step 1  -  Install dependencies**

```bash
pip install -r requirements.txt
```

**Step 2  -  Add the data**

Place these five files in `data/raw/`:

| File | Contents |
|---|---|
| `movies_metadata.csv` | Core movie attributes - one row per film |
| `genres.csv` | Genre tags |
| `production_companies.csv` | Production company links |
| `production_countries.csv` | Country of production |
| `spoken_languages.csv` | Languages spoken in each film |

**Step 3  -  Open and run the notebook**

```bash
jupyter notebook movie_profitability_pipeline.ipynb
```

Run all cells from top to bottom. Each section prints a progress summary so you can follow along.

---

<br>
<br>

## 3. The Data

The dataset comes from **IMDb** and covers thousands of commercially released films across genres, countries, budgets, and languages.

The five source files are **semi-normalized**: `movies_metadata.csv` is the base table and the other four link back to it via a shared `movie_id` key.

```
movies_metadata       ← one row per movie (base table)
    ├── genres              joined on movie_id
    ├── production_companies
    ├── production_countries
    └── spoken_languages
```

**What the pipeline produces:**

| Output File | Created By | Description |
|---|---|---|
| `db_movies_full.csv` | Section 2 | Flat join  -  includes duplicates |
| `db_movies_no_duplicates.csv` | Section 3 | One row per movie |
| `db_movies_with_features.csv` | Section 5 | With all engineered columns |
| `db_movies_final.csv` | Section 6 | Cleaned, analysis-ready ✓ |

---

<br>
<br>

## 4. The Pipeline

The notebook is organized into 7 sections. Each has one job.

| Section | What it does |
|---|---|
| **0. Setup** | Import libraries, set file paths |
| **1. Load** | Read the 5 source CSV files |
| **2. Merge** | Standardize IDs and left-join all tables |
| **3. Deduplicate** | Collapse to one row per movie |
| **4. Fix Types** | Parse dates, numbers, and strings correctly |
| **5. Feature Engineering** | Add profit, ROI, seasons, budget tiers, runtime buckets |
| **6. Clean** | Remove invalid rows; print a full audit report |
| **7. Final Check** | Verify distributions and save the final file |
| **8. Distribution Visualizations** | plot the distribution of the four key financial and production variables |


**Engineered features added in Section 5:**

| Feature | Type | Description |
|---|---|---|
| `profit` | Financial | Revenue minus budget |
| `roi` | Financial | Return on investment |
| `is_loss` | Financial | True if profit is negative |
| `budget_category` | Financial | Low / Medium / High / Very High |
| `revenue_category` | Financial | Low / Medium / High / Blockbuster |
| `profit_category` | Financial | Loss / Low Profit / Hit / Super Hit |
| `year` | Temporal | Extracted from release date |
| `month` | Temporal | Extracted from release date |
| `release_season` | Temporal | Winter / Spring / Summer / Fall / Holiday |
| `is_peak_season` | Temporal | True if Summer or Holiday |
| `runtime_category` | Production | Very Short / Short / Standard / Long / Very Long |
| `duration_category` | Production | Binned duration ranges |

---

<br>
<br>

## 5. Some of Key Findings

### Timing is a financial lever, not a scheduling detail

Holiday and summer releases generate **~2.5× more profit** than fall and winter.

| Season | Performance |
|---|---|
| Holiday (Nov-Dec) | 🟢 Highest average profit |
| Summer (Jun-Aug) | 🟢 Strong  -  blockbuster window |
| Spring (Mar-May) | 🟡 Moderate  -  stable mid-tier window |
| Fall (Sep-Oct) | 🔴 Below average |
| Winter (Jan-Feb) | 🔴 Lowest returns |

### Runtime interacts with season  -  they can't be decided separately

| Runtime | Peak Season | Off-Peak |
|---|---|---|
| Very Long ≥150 min | 🟢 Highest upside | 🔴 Drops sharply |
| Long 120-150 min | 🟢 Strong | 🟡 Most stable year-round |
| Standard 90-120 min | 🟡 Moderate lift | 🟡 Consistent |
| Short <90 min | 🔴 Limited | 🔴 Limited |

### The numbers are more skewed than they look

| Variable | Mean | Median | Mean / Median | What it means |
|---|---|---|---|---|
| Profit | $59.4M | $11.2M | 5.3× | A few blockbusters set the average |
| Revenue | $90.6M | $30.0M | 3.0× | Most films earn modestly |
| Budget | $31.2M | $17.0M | 1.8× | Most films are made cheaply |
| Runtime | 110 min | 106 min | 1.0× | Symmetric - runtime is stable |

> Always use the **median** when describing a "typical" film. The mean is inflated by a small number of massive hits.

### Language strategy depends on budget

- **Very High Budget (>$100M):** English dominates. Global blockbuster success is concentrated here.
- **Mid Budget ($10-50M):** Strong performance across Chinese, Japanese, Telugu, and French markets.
- **Low Budget (<$10M):** Broadly profitable across many languages with lower risk.

### Genre and budget must be decided together

| Budget Level | Best-Fit Genres |
|---|---|
| Very High >$100M | Action, Sci-Fi, Adventure, Family, Mystery |
| High $50-100M | Animation, Adventure |
| Mid $10-50M | Animation, Drama, History, Western |
| Low <$10M | Horror, Romance |

Animation is consistently strong across **all** budget levels.

---

<br>
<br>

## 6. Some of Recommendations

**1. Go big  -  but only at the right time**

Invest in high-budget ($100M+) Action, Adventure, Sci-Fi, or Family films and schedule them for Holiday (first priority) or Summer (second priority) windows. Outside peak seasons, the same investment carries significantly higher failure risk.

**2. Build a stable mid-budget pipeline**

Allocate the majority of production volume to the $10–50M range across Animation, Drama, History, and Western genres. These films deliver consistent, lower-variance returns and can be released year-round.

**3. Match runtime to budget and season**

Reserve 150+ minute films for peak-season blockbuster releases only. The 120–150 minute range is the most reliable all-year performer. Films under 90 minutes show limited profitability regardless of timing.

**4. Use language strategically**

English for global blockbusters. Local-language productions for mid- and low-budget films, particularly in Chinese, Japanese, and Telugu markets where consistent profitability exists without large-scale investment.

**5. Never misalign budget and genre**

Budget-genre misalignment is one of the most avoidable profit killers. Action and Sci-Fi require high budgets to meet audience expectations. Horror and Romance generate their best returns on lean budgets.

---

<br>
<br>

## 7. Limitations & Biases

This section is as important as the findings. Every recommendation carries assumptions made explicit.

<br>

### Collection Stage  -  Selection Bias

The dataset over-represents American films.

| | Real World | This Dataset | Gap |
|---|---|---|---|
| United States | ~10% of global films | 45.6% | +35.6 pts |
| India | ~29% of global films | 1.9% | -27.1 pts |

**Missing data type:** MNAR (Missing Not At Random)  -  smaller international films are systematically absent from financial databases regardless of their quality or commercial performance.

**Impact:** Findings reflect Hollywood-style commercial cinema more than the global film industry.

<br>

### Processing Stage  -  Survivorship Bias

Removing rows with zero or missing budget and revenue eliminated **88% of the dataset**.

What was removed: independent films, low-budget productions, and non-Western cinema that lack complete financial records in the source database.

What remained: commercially documented, widely distributed, financially measurable films.

**Impact:** Relationships like "higher budget leads to higher profit" may appear stronger than they truly are, because the films that challenge that pattern were removed.

<br>

### Insight Stage  -  Confirmation Bias

When you analyze only commercially successful films to understand commercial success, the findings naturally favor strategies that prioritize high-budget mainstream production. The insight that "bigger budgets lead to bigger profits" is partially inflated,  because independent and low-budget films that also generated healthy returns were excluded before the analysis began.

**Bottom line:** Apply these findings with confidence to mainstream commercial productions. Apply caution when extending them to independent films, non-English-language markets, or genres underrepresented in Western film databases.

---

<br>
<br>

## 8. Next Steps

- **Expand the dataset**  -  Include Indian, Chinese, and independent film data to reduce geographic and commercial bias
- **Fix upstream data quality**  -  Work with data suppliers to address missing revenue and budget values at the source rather than removing them during cleaning
- **Add non-financial metrics**  -  Incorporate audience ratings, critic scores, and franchise potential to model success beyond revenue alone
- **Build a profit prediction model**  -  Use budget, genre, runtime, release timing, and language as inputs to forecast profitability before greenlight

---

<br>
<br>

## Requirements

```
pandas>=2.0.0
numpy>=1.24.0
```

**Tools**
- Python
- Tableau
- Jupyter Notebook
- PowerPoint

---

<br>
<br>

## Explore More Yourself

**Executive Presentation Access:** 

[Click here to View the Executive Presentation](https://docs.google.com/presentation/d/14h4phLIu23bWw7nvTKmwpN2ZxyreYfGOJdflNAT4Jdc/edit?usp=sharing)

**Full Medium Story:** 

[Click here to Read the Article on Medium](YOUR_MEDIUM_ARTICLE_LINK)

---

<br>
<br>

*Data source: IMDb (Internet Movie Database) · Built for Spectacular Studios*


*Findings are reliable guidance for commercially oriented mainstream productions. Apply with caution outside that scope.*

<br>
<br>

## Let’s Connect!

**Have feedback or want to collaborate?**  
Feel free to reach out or follow along on [LinkedIn](https://www.linkedin.com/in/iamomardarwesh/), [Email](mailto:omarelsayeddarwesh@gmail.com) or [Medium](https://medium.com/@IamOmarDarwesh).


<br>
<br>

