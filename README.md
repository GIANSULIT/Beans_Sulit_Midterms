# 🃏 Pokémon Card Sold-Price Reference
### Data Preprocessing Midterm Project

🎓 **MEXE Electives 2** · Midterm Project on Data Preprocessing
🏫 Batangas State University · Mechatronics Engineering

![Python](https://img.shields.io/badge/Python-3.x-blue) ![Colab](https://img.shields.io/badge/Google-Colab-orange) ![Rows](https://img.shields.io/badge/Rows-508-green) ![Plots](https://img.shields.io/badge/Visualizations-10-purple)

## 📑 Table of Contents

1. [👥 Group Information](#1--group-information)
2. [📦 Project Overview](#2--project-overview)
3. [🔀 Flowchart](#3--flowchart)
4. [🧹 Preprocessing Summary](#4--preprocessing-summary)
5. [📊 Visualizations](#5--visualizations)
6. [💡 Key Insights](#6--key-insights)
7. [🚀 How to Run](#7--how-to-run)

---

## 1. 👥 Group Information

| | |
| :--- | :--- |
| 🏷️ **Group Name** | Beans & Sulit |
| 🙋 **Members** | Beans, Zachary U. · Sulit, Gian Karl M. |
| 🎯 **Assigned Theme** | Entertainment |
| 📌 **Topic** | Pokémon Card Sold-Price Reference by Grade (2026) |

## 2. 📦 Project Overview

| | |
| :--- | :--- |
| 🗂️ **Dataset Title** | Pokémon Card Sold-Price Reference by Grade (2026) |
| 🔗 **Source** | Kaggle, file `gemsnipe-card-sold-price-reference.csv` · [Dataset page](https://www.kaggle.com/datasets/ryanheger/pokemon-card-sold-price-reference) |
| 📜 **License / Attribution** | `[TODO: copy the license shown on the Kaggle dataset page]` · Dataset uploaded to Kaggle by `ryanheger` |
| 📏 **Dataset size** | 508 rows × 14 columns (one row per card) |
| 🎯 **Objective** | Find out how the sold price of a Pokémon card changes with its grade (raw, PSA 9, PSA 10) and how reliable those prices are. The data is cleaned and prepared (missing values, duplicates, data types, outliers, encoding, scaling), then 10 visualizations show how raw price, graded price, number of sales and confidence relate to each other. |

### 🧾 Data Dictionary

| Column | Type | Meaning |
| :--- | :--- | :--- |
| 🃏 `name` | text | Card name |
| 📚 `set_name` | text | Set the card belongs to |
| 🔢 `local_id` | text | Card number within the set (kept as text for values such as `053` and `CC002`) |
| 🆔 `tcgdex_id` | text | Unique card key (its prefix is the card series) |
| 💵 `raw_price_usd` | float | Median sold price of an ungraded (raw) copy |
| 🧮 `raw_sample_count` | int | Number of raw sales behind the price |
| 🎚️ `raw_confidence` | category | Confidence label for the raw price (`low < medium < high`) |
| 🥈 `psa9_price_usd` | float | Median sold price of a PSA 9 copy |
| 🧮 `psa9_sample_count` | int | Number of PSA 9 sales behind the price |
| 🥇 `psa10_price_usd` | float | Median sold price of a PSA 10 copy |
| 🧮 `psa10_sample_count` | int | Number of PSA 10 sales behind the price |
| 🎚️ `psa10_confidence` | category | Confidence label for the PSA 10 price (`low < medium < high`) |
| ✖️ `psa10_over_psa9_multiple` | float | PSA 10 price divided by PSA 9 price |
| 📈 `graded_sales_total` | int | Total number of graded sales |

## 3. 🔀 Flowchart

Two flowcharts are used: one for the preprocessing program and one for the visualization loop.

**🧹 Flowchart 1: Preprocessing program**

<img width="1738" height="2499" alt="flowchart1_preprocessing" src="https://github.com/user-attachments/assets/e7e1afd3-99d4-4bea-b20b-25dab3729a5c" />

**🔁 Flowchart 2: Visualization loop**

<img width="1738" height="1416" alt="flowchart2_visualization_loop" src="https://github.com/user-attachments/assets/d2917ea0-699a-494a-ba96-640c44c22b02" />

### 🔶 Decisions in our flowchart

| Decision diamond | Branches | What we chose on our data |
| :--- | :--- | :--- |
| 🕳️ **Missing values?** | ❌ **No:** continue.<br>✅ **Yes:** fill numeric columns with the median and text columns with the mode. | ❌ **No.** 0 missing cells in all 14 columns. The Yes branch stays in the code in case new data has gaps. |
| 👯 **Duplicate rows?** | ❌ **No:** continue.<br>✅ **Yes:** `drop_duplicates(subset='tcgdex_id')`, keep the first copy. | ❌ **No.** 0 repeated `tcgdex_id` values, so every card appears once. |
| 🛠️ **Wrong data types?** | ❌ **No:** continue.<br>✅ **Yes:** `local_id` to text, confidence columns to ordered categories. | ✅ **Yes.** `local_id` kept as text (to keep `053` and `CC002`). `raw_confidence` and `psa10_confidence` became ordered categories (`low < medium < high`). |
| 🏔️ **Outliers in price columns?** (IQR rule, 1.5 × IQR) | ❌ **No:** continue.<br>✅ **Yes:** keep every row and apply `log1p`. | ✅ **Yes.** 58 outliers in `raw_price_usd`, 68 in `psa9_price_usd`, 67 in `psa10_price_usd`. Rows were kept because expensive cards are real, and `log1p` was applied instead. |
| 🖼️ **More plots?** (`i < 10`) | ✅ **Yes:** read `plot_list[i]`, draw, save, `i = i + 1`.<br>🏁 **No:** END. | 🔁 **Yes, 10 times.** Each pass saves `visualizations/plot_{i+1}.png`. |

### ✏️ Changes we made

The notebook follows both flowcharts box by box. The differences are small:

| Change | In the flowchart? | Why |
| :--- | :--- | :--- |
| 📚 Imports (`os`, `numpy`, `pandas`, `matplotlib`, `seaborn`, `scikit-learn`) | ➖ Not drawn in START | Every later box needs the libraries. |
| 📤 Colab file upload (`files.upload()`) before `pd.read_csv` | ➖ Not drawn in the load box | The CSV has to be uploaded to Colab before it can be read. |
| 🏷️ `price_tier` created with `pd.cut` on `raw_price_usd` (bins at $50, $150, $500) | ⚠️ Used in Flowchart 2, not listed in the outlier box of Flowchart 1 | Plots 3, 5 and 7 group cards by price tier. It is created in the outlier Yes branch, together with the `log1p` columns and `flag_low_sample`. |

## 4. 🧹 Preprocessing Summary

In the same order as the flowchart and the notebook:

| Step | Task | What we did |
| :---: | :--- | :--- |
| 1️⃣ | 📥 Load and inspect | Loaded the CSV (508 × 14) in chunks of 50,000 rows with `local_id` read as text. Checked shape, data types and summary statistics. |
| 2️⃣ | 🕳️ Missing values | None found, so the No branch ran. |
| 3️⃣ | 👯 Duplicate rows | None found by `tcgdex_id`. |
| 4️⃣ | 🛠️ Fix data types | `local_id` to text. `raw_confidence` and `psa10_confidence` to ordered categories (`low < medium < high`). |
| 5️⃣ | 🏔️ Outliers (IQR rule) | Kept every row. Added `log_raw`, `log_psa9` and `log_psa10` with `log1p` (PSA 10 skew 6.41 to 0.55). |
| 6️⃣ | 🚩 Flags and tiers | `flag_low_sample` marks 190 cards with fewer than 10 sales. `price_tier` bins `raw_price_usd` into budget (62), mid (218), high (152) and chase (76). |
| 7️⃣ | 🔢 Encode categoricals | Ordinal encoding for `raw_confidence` and `psa10_confidence`. One-hot encoding for the top 6 card series (`smp`, `basep`, `sm12`, `sm9`, `neo4`, `sm11`), with all other series grouped as `other`. |
| 8️⃣ | ⚖️ Scale numeric columns | `StandardScaler` on 8 numeric columns into `*_scaled` columns (mean 0, standard deviation 1). |
| 9️⃣ | 💾 Save | Wrote `pokemon_cards_clean.csv` (508 × 38). |
| 🔟 | 🎨 Visualize | Loaded the saved file again and ran the visualization loop to produce 10 plots in `visualizations/`. |

## 5. 📊 Visualizations

🖼️ **Total: 10 plots**, saved in `visualizations/` as `plot_1.png` to `plot_10.png`.

### 💰 Plot 1: PSA 10 price distribution

<img width="982" height="606" alt="Histogram of PSA 10 price in USD with the median marked" src="https://github.com/user-attachments/assets/1a4eb7ba-3aaf-4016-be4f-4d26876e6d68" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Histogram of PSA 10 price (USD), with the median marked | The median PSA 10 price is $1,150 but the mean is $4,493 (skew 6.41). 60 cards sell above $10,000, and the 10 most expensive cards make up 25% of all PSA 10 value, so a few cards dominate the market. |

### 📉 Plot 2: PSA 10 price after log transform

<img width="980" height="606" alt="Histogram and KDE of PSA 10 price after log1p" src="https://github.com/user-attachments/assets/c654da66-e3d6-42d0-b8be-a3d5c5c92bd2" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Histogram and KDE of PSA 10 price after `log1p` | The skew falls from 6.41 to 0.55. The distribution is now close to symmetric, which is why the log columns are used for scaling and modelling. |

### 🏷️ Plot 3: Cards per raw-price tier

<img width="983" height="607" alt="Count plot of cards per raw-price tier" src="https://github.com/user-attachments/assets/4c7662b1-af74-4c30-88cf-3024ec6b8007" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Count plot of cards per raw-price tier | Most cards sit in the middle of the market: budget (up to $50 raw) 62, mid ($50 to $150) 218, high ($150 to $500) 152, chase (above $500) 76. |

### 🎚️ Plot 4: Cards per raw-confidence level

<img width="977" height="598" alt="Count plot of cards per raw-confidence level" src="https://github.com/user-attachments/assets/b8495b02-668c-4ff6-93de-569e6cc50a59" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Count plot of cards per raw-confidence level | 279 of 508 cards (55%) have low raw confidence and only 17 have high confidence, so many raw prices rest on few sales and should be read with caution. |

### 📦 Plot 5: PSA 10 price by raw-price tier

<img width="983" height="605" alt="Box plot of PSA 10 price by raw-price tier" src="https://github.com/user-attachments/assets/4e5eaa9b-994a-4240-ab41-4b200806c353" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Box plot of `log_psa10` by `price_tier` | The median PSA 10 price rises with the raw tier: $555 (budget), $663 (mid), $1,812 (high), $8,602 (chase). Budget and mid overlap heavily, so raw price separates the upper tiers better than the lower ones. |

### 📦 Plot 6: PSA 10 price by PSA 10 confidence

<img width="980" height="608" alt="Box plot of PSA 10 price by PSA 10 confidence" src="https://github.com/user-attachments/assets/1124a690-a405-440d-959c-7fc7d56e18fb" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Box plot of `log_psa10` by `psa10_confidence` | Low-confidence cards have the highest median price ($3,167) but the fewest sales (median 11), compared with $688 and 271.5 sales for high confidence. Expensive cards trade rarely, so their prices are less certain. |

### 🔵 Plot 7: Raw vs PSA 10 price

<img width="981" height="600" alt="Scatter of log raw price vs log PSA 10 price colored by price tier" src="https://github.com/user-attachments/assets/2940f08a-6bfc-4f6b-a5e2-08016bf78993" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Scatter of `log_raw` vs `log_psa10`, colored by `price_tier` | The two are positively related (r = 0.55) but the scatter is wide, so raw price alone predicts the PSA 10 price only roughly. The median PSA 10 / raw multiple is 15.8x for budget cards versus 7.3x, 6.5x and 7.9x for mid, high and chase. |

### ✖️ Plot 8: PSA 10 / PSA 9 price multiple

<img width="976" height="601" alt="Histogram and KDE of the PSA 10 over PSA 9 price multiple" src="https://github.com/user-attachments/assets/3241aa7c-e4cf-4fb0-8815-0aefbfd96eab" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Histogram and KDE of `psa10_over_psa9_multiple` | A PSA 10 sells for a median of 4.9x the PSA 9 price (middle half: 3.3x to 7.3x). 7 cards fall below 1x, meaning a PSA 10 priced under a PSA 9, which is a data-quality warning. |

### 🏆 Plot 9: Top 10 PSA 10 prices

<img width="992" height="601" alt="Bar chart of the 10 highest PSA 10 prices by card name" src="https://github.com/user-attachments/assets/bf4b572b-b311-423f-9765-c2fac9e782a0" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Bar chart of the 10 highest `psa10_price_usd`, by card name | The priciest PSA 10 is Lugia ex at $125,100, based on only 5 sales. 6 of these 10 cards have fewer than 10 PSA 10 sales, and 5 are "ex" cards. |

### 🔥 Plot 10: Correlation heatmap

<img width="1077" height="672" alt="Correlation heatmap of the scaled numeric columns" src="https://github.com/user-attachments/assets/46e272fc-ac63-469e-83ee-65db64885f50" />

| 📈 Plot | 💡 Insight |
| :--- | :--- |
| Correlation heatmap of the scaled numeric columns | The price columns move together (`log_psa9` vs `log_psa10`, r = 0.90). PSA 10 sample count is negatively related to PSA 10 price (r = -0.39). `psa9_sample_count` and `graded_sales_total` carry almost the same signal (r = 0.95), so one of them is redundant for modelling. |

## 6. 💡 Key Insights

1. 👑 **A few cards dominate the market.** The median PSA 10 price is $1,150 but the mean is $4,493, 60 cards sell above $10,000, and the top 10 cards make up 25% of all PSA 10 value (Plots 1 and 9).
2. 📉 **A log transform fixes the skew.** `log1p` brings the PSA 10 skew from 6.41 down to 0.55, so the log columns are used for scaling and modelling (Plot 2).
3. 🪜 **Raw price separates the upper tiers better than the lower ones.** Median PSA 10 prices are $555 (budget), $663 (mid), $1,812 (high) and $8,602 (chase), and the raw vs PSA 10 relation is only moderate (r = 0.55) (Plots 5 and 7).
4. 🐢 **Expensive cards trade rarely.** 55% of cards have low raw confidence, and low PSA 10 confidence goes with the highest prices but the fewest sales (median 11 vs 271.5 for high confidence), so these prices are less certain (Plots 4 and 6).
5. ⚠️ **Some data quality problems remain.** 7 cards have a PSA 10 priced below a PSA 9, and `psa9_sample_count` and `graded_sales_total` are almost the same signal (r = 0.95), so one of them is redundant for modelling (Plots 8 and 10).

## 7. 🚀 How to Run

1. 📓 Open `notebooks/Pokemon_Midterm_Preprocessing.ipynb` in Google Colab: `[TODO: paste your Colab link here]`
2. 📦 Install the dependencies (`pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`). Colab already includes them, so this normally reports "Requirement already satisfied":

   ```bash
   pip install -r requirements.txt
   ```

3. 📤 Upload `gemsnipe-card-sold-price-reference.csv` when the notebook asks for it (or place it in `data/` when running locally).
4. ▶️ Run all cells from top to bottom (**Runtime → Run all**).

### 🎁 Outputs

| File | Description |
| :--- | :--- |
| 💾 `pokemon_cards_clean.csv` | Cleaned dataset (508 × 38) |
| 🖼️ `visualizations/plot_1.png` to `plot_10.png` | The 10 charts |
