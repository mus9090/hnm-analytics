# Scope: The Life of a Fashion Product

**Status:** Step 1 complete. All decisions in section 6 agreed.
**Sources:** outputs of `notebooks/01_sample_and_profile.ipynb` and `notebooks/02_full_weekly_aggregates.ipynb`, plus two read-only checks run alongside them. Every number below was computed from the data; none are estimates.

## 1. Verdict

| Question | Feasible? | Why |
|---|---|---|
| Q3 Discount → loyalty (headline) | **Yes** | About a third of purchases are clearly marked down, and both comparison groups of new customers are large |
| Q2 Lifecycle | **Yes** | 59,626 uncensored launches; preview curves already differ by garment group |
| Q1 Trend spread | **Yes, with a better method** | Enough volume in every age band, but the preview's way of picking "rising" products picked seasonal and non-fashion items |

## 2. The data

| Table | Rows | Notes |
|---|---|---|
| transactions_train.csv | 31,788,324 | 20 Sep 2018 – 22 Sep 2020; 106 Monday-start weeks |
| customers.csv | 1,371,980 | |
| articles.csv | 105,542 | 104,547 have at least one sale |

- **Partial periods:** Sep 2018 and Sep 2020 are partial months; the first and last weeks are partial too. Leave them out of monthly and weekly trend charts.
- **Customer sample (notebook 01):** 137,198 customers (10%, seed 42) with all 3,162,621 of their purchases (9.95% of rows).
- **Weekly aggregates (notebook 02):** `article_week` 2,814,421 rows; `trend_week` 1,156,135 rows. Units in both add up exactly to 31,788,324.

## 3. Data-quality results → cleaning rules

| Check (sample unless stated) | Result | Rule |
|---|---|---|
| Purchased articles missing from articles table | 0 | No action |
| Sampled customers with no purchases | 892 (0.65%) | Exclude from customer metrics |
| Exact duplicate transaction rows | 295,872 (9.4%) | Keep: each row = one unit (no quantity column) |
| Rows with price ≤ 0 | 0 | No action |
| Duplicate keys in customers / articles | 0 / 0 | Both are clean primary keys |
| `FN` / `Active` missing | 65.4% / 66.3% (only other value is 1) | See decision D4 |
| `fashion_news_frequency` missing | 1.18% (values: NONE, Regularly, Monthly) | See decision D3 |
| `club_member_status` missing | 0.44% (values: ACTIVE, PRE-CREATE, LEFT CLUB) | Keep as "unknown" |
| `age` missing | 1.18% of sampled customers; 0.4% of all units | "unknown" age band |
| Articles with product group "Unknown" / garment group "Unknown" | 121 / 3,873 | Keep; label as Unknown in charts |
| Price range (scaled) | 0.0002 – 0.5068, mean 0.0278 | Review the lowest price ratios (near 0.01) in cleaning |

**Channel check:** channel 2 is 70.1% of sample units and 70.4% of all units. In April 2020 it was 100% of sales, when stores were closed for COVID. This supports the assumption that channel 2 is online (still undocumented).

## 4. First numbers (10% sample, whole period)

| Measure | Value | Caveat |
|---|---|---|
| Baskets (customer × date) | 907,523 | No order ID |
| Items per basket | 3.48 | Includes duplicate rows as units |
| Baskets per customer | 6.66 | Over two years |
| Customers with 2+ baskets | 66.9% | Not the new-customer 180-day rate; that comes in Step 2 |
| Sales value from top 10% / top 20% of customers | 50.8% / 69.4% | |

## 5. Feasibility evidence

**Q3 Discounts.** Share of purchases at or below each threshold of the article's max price in the same channel:

| Threshold | 90% | 85% | 80% | 70% |
|---|---|---|---|---|
| Share of purchases (max from sample) | 39.2% | 32.7% | 29.6% | 21.2% |

- Using the max price from the full data instead, the 85% share is **34.9%**.
- 65.0% of article-channel pairs have at least one sale 15%+ below their max.
- Prices cluster at exactly 90%, 80%, 60%, 50% and 30% of max: real "% off" markdowns, not noise.
- Markdown share follows sale seasons: 45.2% in Jul 2019 and 45.4% in Nov 2019, down to 15.3% in May 2019.
- 45,179 sampled customers made their first purchase between Mar 2019 and Feb 2020. First basket: 65.1% no markdown items, 16.7% all markdown, the rest mixed.

**Q2 Lifecycle.**

- 59,626 of 104,547 articles (57%) have an uncensored launch (first sale not in the first 8 or last 12 weeks). About 18,000 "launches" in the very first week confirm the left-censoring rule is needed.
- 35,748 of them sold 50+ units. A typical article sells 65 units in the full data but only 6 in the sample, so lifecycle must use the full data.
- Preview: average curves peak 1–2 weeks after launch. Ladies' dresses fade fastest, accessories slowest. Zero-sales weeks are not yet filled, so the curves are too high.

**Q1 Trend spread.**

- Share of units by age band: 16–24 20.5%, 25–34 36.4%, 35–44 12.9%, 45–54 19.1%, 55+ 10.6%, unknown 0.4%.
- 131 product types, 50 colours, 21 garment groups.
- The preview's top "risers" (Beanie, Dog Wear, Side table, Shoulder bag, Weekend/Gym bag) are seasonal or non-fashion. No young-first pattern showed, because of how risers were picked.
- More 45–54 than 35–44 buyers suggests parents buying for children: the account holder's age is not always the wearer's.

## 6. Decisions

| # | Decision | Options | Suggested | Decision |
|---|---|---|---|---|
| D1 | Which first baskets count as markdown-led | any markdown item / more than half the value marked down / all items | More than half the value; "all items" as a robustness check | **Agreed:** more than half the value is the main rule (22.3% of new customers, 10,079); "any item" (38.5%) and "all items" (17.9%) shown as robustness checks |
| D2 | Where the reference ("full") price comes from | sample max / full-data max | Full-data max from `article_week` | **Agreed:** full-data max per article and channel (the sample max misses about 1 in 10 markdown-led customers) |
| D3 | Missing `fashion_news_frequency` | merge into NONE / keep as "unknown" | Keep as "unknown" | **Agreed:** keep blanks as "unknown" (1.18% of customers; we don't know they mean "not subscribed") |
| D4 | Missing `FN` / `Active` | fill with 0 / leave missing | Fill with 0 (only other value is 1) | **Agreed:** fill blanks with 0. FN = 1 for 47,471 sampled customers vs 47,603 getting the newsletter Regularly or Monthly, so FN is very likely the newsletter flag; meaning still undocumented |
| D5 | How to pick "rising" products for Q1 | first half vs second half / year-over-year, same weeks | Year-over-year, with a minimum volume | **Agreed:** rank by growth in share of all units from year 1 to year 2, only for product types/colours with real volume (starting point 5,000+ units, to be checked in Step 4). The half-vs-half ratio favoured small or brand-new categories |
| D6 | What to include in Q1 | all products / adult fashion only | Exclude Baby/Children and non-clothing (home, pets, cosmetics) | **Agreed:** adult fashion only; report how many units are dropped |

## 7. Rules that keep the two data layers consistent

1. Compare rates and averages across layers, never totals (sample totals are about 10% of the real ones).
2. Take reference prices from the full data (D2).

## 8. Limitations (carried into the README)

- Prices are scaled: report "sales value", never a currency.
- `sales_channel_id` is undocumented; channel 2 is assumed online (supported by April 2020).
- Markdowns are inferred from price changes; there is no promotion flag and no returns data.
- Age is one snapshot per customer, and is the account holder's age.
- Findings are associations, not causation. Data covers 2018–2020, including COVID.

## 9. Next step (Step 2)

Cleaning notebook applying sections 3 and 6 → MySQL schema and load (MySQL 8.0 is installed and running) → baseline SQL: new-customer cohort retention (`MIN() OVER`), RFM (`NTILE`), sales concentration.
