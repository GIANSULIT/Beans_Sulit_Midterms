# Pokémon Card Sold-Price Reference: Data Preprocessing Midterm

MEXE Electives 2 | Midterm Project on Data Preprocessing

## 1. Group Information
- **Group Name:** Beans & Sulit
- **Members:** Beans Zachary U | Sulit, Gian karl M
- **Assigned Theme:** Entertainment
- **Topic:** Pokémon card sold prices: raw vs PSA 9 vs PSA 10 copies

## 2. Project Overview
- **Dataset Title:** Pokémon Card Sold-Price Reference by Grade (2026)
- **Source (URL or reference):** Kaggle, file `gemsnipe-card-sold-price-reference.csv` ([https://www.kaggle.com/datasets/ryanheger/pokemon-card-sold-price-reference/discussion?sort=undefined])
- **License/Attribution:** [license shown on the Kaggle page], dataset by [author on Kaggle]
- **Objective:**
  Find out how the sold price of a Pokémon card changes with its grade (raw, PSA 9, PSA 10) and how reliable those prices are. The dataset has 508 cards with median sold prices, sample counts and confidence labels. The data is cleaned and prepared (missing values, duplicates, data types, outliers, encoding, scaling). Then 10 visualizations show how raw price, graded price, number of sales and confidence relate to each other.

## 3. Flowchart
Two flowcharts are used: one for the preprocessing program and one for the visualization loop.

![Flowchart 1: Preprocessing program](flowchart/flowchart1_preprocessing.png)

![Flowchart 2: Visualization loop](flowchart/flowchart2_visualization_loop.png)

- **Decisions in our flowchart:**
  - **Missing values?** No. 0 missing cells in all 14 columns, so the No branch ran. The Yes branch (median for numeric columns, mode for text columns) is in the code in case new data has gaps.
  - **Duplicate rows?** No. 0 repeated `tcgdex_id` values, so every card appears once.
  - **Wrong data types?** Yes. `local_id` is kept as text (to keep values such as `053` and `CC002`) and `raw_confidence` and `psa10_confidence` become ordered categories (`low < medium < high`).
  - **Outliers in price columns?** Yes. The IQR rule finds 58 outliers in `raw_price_usd`, 68 in `psa9_price_usd` and 67 in `psa10_price_usd`. The rows are kept because expensive cards are real, and `log1p` is applied instead.
  - **More plots? (`i < 10`)** Yes until `i = 10`. Each pass reads one entry of `plot_list`, draws it, saves it to `visualizations/plot_{i+1}.png` and adds 1 to `i`.

- **Changes I made:**
  [Write here what changed between your first flowchart and the final code, and why. If nothing changed, say so.]

## 4. Preprocessing Summary
1. Loaded the CSV (508 x 14) in chunks of 50,000 rows, reading `local_id` as text, and inspected shape, data types and summary statistics.
2. Checked missing values (none), so the No branch ran.
3. Checked duplicate cards by `tcgdex_id` (none).
4. Fixed data types: `local_id` to text and the two confidence columns to ordered categories (`low < medium < high`).
5. Checked price outliers with the IQR rule (1.5 x IQR), kept every row, and added `log_raw`, `log_psa9` and `log_psa10` with `log1p` (PSA 10 skew 6.41 to 0.55).
6. Flagged 190 cards with fewer than 10 sales in `flag_low_sample` and binned `raw_price_usd` into `price_tier` (budget 62, mid 218, high 152, chase 76).
7. Encoded categoricals: ordinal encoding for `raw_confidence` and `psa10_confidence`, and one-hot encoding for the top 6 card series (`smp`, `basep`, `sm12`, `sm9`, `neo4`, `sm11`) with all other series grouped as `other`.
8. Scaled 8 numeric columns with `StandardScaler` into `*_scaled` columns (mean 0, standard deviation 1).
9. Saved `pokemon_cards_clean.csv` (508 x 38).
10. Loaded the saved file again and ran the visualization loop to produce 10 plots in `visualizations/`.

## 5. Visualizations
- Total: **10**

| # | Plot | Insight |
| :--- | :--- | :--- |
| 1 | Histogram of PSA 10 price (USD), with the median marked | The median PSA 10 price is $1,150 but the mean is $4,493 (skew 6.41). The 10 most expensive cards make up 25% of all PSA 10 value. |
| 2 | Histogram and KDE of PSA 10 price after `log1p` | The skew falls from 6.41 to 0.55, so the distribution is close to symmetric. |
| 3 | Count plot of cards per raw-price tier | Most cards sit in the middle of the market: budget 62, mid 218, high 152, chase 76. |
| 4 | Count plot of cards per raw-confidence level | 279 of 508 cards (55%) have low raw confidence and only 17 have high confidence. |
| 5 | Box plot of PSA 10 price by raw-price tier | The median PSA 10 price rises with the tier: $555, $663, $1,812 and $8,602. Budget and mid overlap heavily. |
| 6 | Box plot of PSA 10 price by PSA 10 confidence | Low-confidence cards have the highest median price ($3,167) but the fewest sales (median 11). |
| 7 | Scatter of raw vs PSA 10 price (log scale), by price tier | The two are positively related (r = 0.55) but the scatter is wide. The median PSA 10 / raw multiple is 15.8x for budget cards and 6.5x to 7.9x for the upper tiers. |
| 8 | Histogram and KDE of PSA 10 price divided by PSA 9 price | A PSA 10 sells for a median of 4.9x the PSA 9 price. 7 cards fall below 1x, which is a data-quality warning. |
| 9 | Bar chart of the 10 highest PSA 10 prices | The priciest card is Lugia ex at $125,100, based on only 5 sales. 6 of the 10 have fewer than 10 PSA 10 sales. |
| 10 | Correlation heatmap of the scaled numeric columns | `log_psa9` and `log_psa10` move together (r = 0.90), and `psa9_sample_count` and `graded_sales_total` carry almost the same signal (r = 0.95). |

The plot images are saved in `visualizations/` (`plot_1.png` to `plot_10.png`).

## 6. Key Insights
1. A few cards dominate the market: the median PSA 10 price is $1,150 but the mean is $4,493, 60 cards sell above $10,000, and the top 10 cards make up 25% of all PSA 10 value.
2. A log transform fixes the skew: `log1p` brings the PSA 10 skew from 6.41 down to 0.55, so the log columns are used for scaling and modelling.
3. Raw price separates the upper tiers better than the lower ones: median PSA 10 prices are $555 (budget), $663 (mid), $1,812 (high) and $8,602 (chase), and the raw vs PSA 10 relation is only moderate (r = 0.55).
4. Expensive cards trade rarely: 55% of cards have low raw confidence, and low PSA 10 confidence goes with the highest prices but the fewest sales (median 11 vs 271.5 for high confidence), so these prices are less certain.
5. Some data quality problems remain: 7 cards have a PSA 10 priced below a PSA 9, and `psa9_sample_count` and `graded_sales_total` are almost the same signal (r = 0.95), so one of them is redundant for modelling.

## 7. How to Run
1. Open `notebooks/Pokemon_Midterm_Preprocessing.ipynb` in Google Colab: [paste your Colab link here]
2. Install the dependencies from `requirements.txt` (`pip install -r requirements.txt`): pandas, numpy, matplotlib, seaborn and scikit-learn.
3. Place `gemsnipe-card-sold-price-reference.csv` in `data/` (or upload it when the notebook asks) and run all cells from top to bottom. The notebook saves `pokemon_cards_clean.csv` and the `visualizations/` folder.
