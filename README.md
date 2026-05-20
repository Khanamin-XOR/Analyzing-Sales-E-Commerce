# Customer Insights EDA — Drivers of Recommendation on Dress Sales Data

A structured Exploratory Data Analysis on a fashion e-commerce dataset (dress sales + product attributes), identifying which combinations of **style, price, season, size, and material** drive positive customer recommendations.

## Dataset

Two CSVs combined for the analysis (both committed in this repo, fully reproducible):

| File | Description |
|---|---|
| `Attribute+DataSet.csv` | Product catalogue — `Dress_ID`, `Style`, `Price`, `Rating`, `Size`, `Season`, `NeckLine`, `SleeveLength`, `Material`, `FabricType`, `Decoration`, `PatternType`, `Recommendation` |
| `Dress+Sales.csv` | Daily sales counts per `Dress_ID` (Aug–Dec 2013) |

## Methodology

### 1. Data cleaning & type fixing
- Converted sales-date columns from `object` to numeric (resolving non-numeric junk entries that broke straightforward conversion)
- Segmented missing-value columns into two classes for differentiated treatment:
  - **Type-1** *(< 3 missing)*: `Price`, `Season`, `NeckLine`, `SleeveLength`, `Winter`, `Autumn` → imputed
  - **Type-2** *(> 15 % missing)*: `Material`, `FabricType`, `Decoration`, `PatternType` → handled conservatively

### 2. Standardisation
- Size abbreviations consolidated: `M → Medium`, `L → Large`, `XL → Extra large`, `free → Free`, `S/s/small → Small`
- Irregular categorical spellings cleaned in `Season` and `SleeveLength`

### 3. Date → Season aggregation
Mapped daily sale dates into four seasonal buckets so sales could be compared across the calendar year rather than as 100+ noisy daily columns:

| Season | Months |
|---|---|
| Summer | Jun – Aug |
| Autumn | Sep – Nov |
| Winter | Dec – Feb |
| Spring | Mar – May |

### 4. Univariate analysis
- **Categorical unordered** (`Style`, `NeckLine`, `SleeveLength`): low-volume categories (< 50 k total sales across all seasons) consolidated into an `Others` bucket — keeps the high-signal segments distinct without long-tail clutter
- **Materials & patterns** (`Material`, `FabricType`, `PatternType`, `Decoration`): same logic with a 25 k threshold (lower-volume sub-attributes)
- **Numerical** (sales per season): percentile analysis (75th, 99th, max) per season column to identify outlier-heavy distributions

### 5. Bivariate analysis
- **Price × Rating** — which price tier underperforms on customer rating
- **Style × Rating** — median rating per style (e.g., vintage vs cute vs party)
- **Season × Sales × Recommendation** — which season has the highest average sales for products with `Recommendation = 1`
- **Size × Recommendation** — which size categories drive positive recommendations

### 6. Multivariate analysis
- **Style × Price × Recommendation** — best-performing combinations across both attributes
- **Material × Season × Recommendation** — identified materials with zero recommendations in particular seasons (a product/season mismatch signal that could inform inventory planning)

## Key techniques applied

- Type coercion + null-pattern segmentation (Type-1 vs Type-2 split)
- "Others" bucketing for categorical long-tail (preserves top categories, compresses low-signal noise)
- Groupby + cross-tabulation for category × outcome analysis
- Percentile-based outlier inspection per numerical sales column

## Tech stack

Python 3 · NumPy · Pandas · Matplotlib · Seaborn · Jupyter

## Run

```bash
pip install numpy pandas matplotlib seaborn jupyter
jupyter notebook "EDA for Sales.ipynb"
```

Data is committed alongside the notebook — no external download required.

## Origin

Originally completed as a guided EDA exercise. The dataset and high-level EDA curriculum structure (cleaning → standardising → univariate → bivariate → multivariate) follow a standard data-analyst workflow; the implementation, methodological choices (Type-1 / Type-2 missing-value split, threshold-based "Others" bucketing, percentile inspection per season), and interpretation are mine.
