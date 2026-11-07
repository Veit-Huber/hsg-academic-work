# Academic Work – Veit Norman Huber

Selected coursework from my BSc in Economics at the University of St. Gallen (HSG). Each project is a quantitative question answered with data, an explicit model and a sensitivity or validation step.

| Project | Topic | Methods | Tools |
|---|---|---|---|
| [UBS liquidity stress test](#1-how-long-would-ubs-survive-a-digital-bank-run) | Bank liquidity, Basel III LCR, Credit Suisse 2023 | Scenario stress test, sensitivity analysis | Python, pandas, Excel |
| [Swiss rail demand model](#2-predicting-passenger-demand-at-swiss-railway-stations) | Predicting station traffic from open data | Data cleaning, feature engineering, regularised regression, grouped CV | Python, pandas, scikit-learn, matplotlib |
| [Refugee employment forecast](#3-forecasting-the-employment-rate-of-ukrainian-refugees-in-switzerland) | Labour-market forecast | Multiple linear regression (OLS), scenario analysis | Excel |

```
.
├── Academic Paper2_Huber_Veit_EN.pdf   # 1. UBS liquidity stress test (paper)
├── assignment/                         # 2. Swiss rail demand model
│   ├── big_assignment_2026.ipynb
│   └── data/                           #    stations.csv must be downloaded, see below
├── Academic Paper_Huber_Veit_EN.pdf    # 3. Refugee employment forecast (paper)
└── LICENSE
```

---

## 1. How long would UBS survive a digital bank run?

**Seminar paper, Academic Term Paper (VW 113) · April 2026** · [Paper (PDF)](Academic%20Paper2_Huber_Veit_EN.pdf) · [Code & data](https://github.com/WHA-VWL-113/Anhang-UBS-Liquidity-Stress-Test/releases/tag/v1.0)

**Question:** Is the 30-day window of the Liquidity Coverage Ratio (LCR) adequate when deposits can leave at smartphone speed, and how long would UBS Group's liquidity last in a run like Credit Suisse's in March 2023?

**What I did**
- Reconstructed Credit Suisse's daily outflows in March 2023. The average daily outflow rate was **2.24×** the rate implied by the LCR's stress assumptions.
- Built a short-term liquidity stress test for UBS Group on its Pillar 3 data (HQLA of USD 331.6bn, LCR ≈ 182.5%). The model scales Basel III outflow rates by an intensity multiplier *k* and haircuts inflows by a factor *f*.
- Ran three scenarios: the regulatory baseline, a CS benchmark and a "digital flash run" with Level 1 assets only and no inflows. Sensitivity tables cover *k* from 1.0 to 5.0 and HQLA scaled up to 6×.

**Results**
- HQLA would run out after **≈ 55 days** in the Basel III baseline (≈ 25 days even with zero inflows), **≈ 14 days** in the CS benchmark and **≈ 5 days** in the flash run.
- UBS drops below 30 days of survival once outflows are only **~40% above** the Basel III standard (*k* ≈ 1.4).
- To survive 30 days in the CS scenario, UBS would need roughly **3× its HQLA**. For the flash run it would need more than its whole balance sheet. The weak point is the speed of outflows, not the size of the buffer.

*Relevant for markets:* funding-liquidity risk, run dynamics and how regulatory ratios behave under stress.

---

## 2. Predicting passenger demand at Swiss railway stations

**Big Assignment, data science course (Python) · October 2026** · [Notebook](assignment/big_assignment_2026.ipynb) · [Data](assignment/data)

**Question:** How well can a station's daily boardings and alightings be predicted from its physical and geographical characteristics, including for stations the model has never seen?

**What I did**
- Cleaned and merged three official open datasets from [opentransportdata.swiss](https://opentransportdata.swiss/): passenger counts, the service-point register and the platform register. This covered type handling, validity-date filtering (snapshot at 31 Dec 2024), duplicate and implausible-value checks, and a key check before each merge. The final sample has 746 stations (522 train / 224 test).
- Modelled log daily passengers, since the raw counts are heavily right-skewed. Baselines were OLS and LASSO against a mean-prediction benchmark.
- In the open-ended part I engineered new features: log size variables, the density of stops within 1 km, the distance to the nearest major hub and to the next station, Park & Ride and tram flags, municipal population and the 2014 rail-funding vote (FABI), plus a size × density interaction.
- Validated with **GroupKFold by municipality**, because stations in the same town are not independent. This keeps the model from scoring well just by having seen the town before. I compared OLS, LASSO, Ridge and Elastic Net with CV-tuned regularisation. The feature set and model were chosen on the training data only; the original hold-out test set was kept unchanged for the final evaluation.

**Results** (test set, log scale)

| Model | MSE | MAE |
|---|---|---|
| Mean benchmark | 2.33 | 1.20 |
| Linear regression (baseline features) | 1.35 | 0.85 |
| LASSO, α = 0.05 (baseline features) | 1.36 | 0.84 |
| **LASSO (engineered features, grouped CV)** | **0.60** | **0.60** |

The MSE fell by more than half. The typical prediction error went from about 2.3× to about 1.8× the true value. Nearly all of the gain came from better features, not from the choice of model: OLS, LASSO, Ridge and Elastic Net scored almost identically in grouped CV.

### Data and how to run it

| File | Content | Source |
|---|---|---|
| `passengers.csv` | Average daily boardings and alightings per station | opentransportdata.swiss (included) |
| `platforms.csv` | Platform register, one row per platform | opentransportdata.swiss (included) |
| `population_2024.csv` | Permanent resident population per municipality, 31 Dec 2024 | [Federal Statistical Office](https://statistik.admin.ch/rm/asset/36073772) (included) |
| `vote_rail_2014.csv` | Yes share per municipality, FABI vote of 9 Feb 2014 | [Federal Statistical Office](https://www.pxweb.bfs.admin.ch/pxweb/de/px-x-1703030000_101/-/px-x-1703030000_101.px/) (included) |
| `stations.csv` | Service-point register, all versions (one row per validity period) | **Not included** (too large for GitHub), download below |

> **`stations.csv` is not in the repository.** Download the full service-point register ("Service Points, all versions", file `full-swiss-service-point.csv`) from [opentransportdata.swiss](https://data.opentransportdata.swiss/dataset/service-point-v2), save it as `assignment/data/stations.csv`, then run the notebook from inside the `assignment/` folder. The notebook filters the register to service points valid on 31 Dec 2024, but a newer export can still differ slightly from the course snapshot, so the numbers may not match the table above to the last decimal.

Requirements: Python 3 with `numpy`, `pandas`, `matplotlib` and `scikit-learn`.

---

## 3. Forecasting the employment rate of Ukrainian refugees in Switzerland

**Seminar paper, Introduction to Academic Writing · November 2025** · [Paper (PDF)](Academic%20Paper_Huber_Veit_EN.pdf)

**Question:** How will the employment rate of Ukrainian refugees (protection status S) in Switzerland change by the end of 2025?

**What I did**
- Identified four drivers from the literature and official statistics: the number of refugees, the male/female ratio, the length of stay and the general unemployment rate.
- Estimated a multiple linear regression (OLS) on monthly data since mid-2022.
- Turned explicit assumptions for each driver into normal, optimistic and pessimistic scenarios.

**Results:** expected employment rates of **≈ 41.5%** (normal), **≈ 44.6%** (optimistic) and **≈ 32.3%** (pessimistic) by the end of 2025.

---

*All work is my own. The use of AI tools is declared in each paper's list of aids and in the notebook, as required by HSG.*
