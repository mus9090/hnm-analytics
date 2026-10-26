# The Life of a Fashion Product: Launch, Spread, Discount

**A customer and product analytics case study on 31M H&M transactions (public Kaggle dataset)**

> **Status:** Week 1 — data sampling, profiling, and weekly aggregates

## Business questions
Follow a fashion product through its life and ask what a retailer should do at each stage:

1. **Spread — Who starts fashion trends?** When a product type or colour takes off, do younger customers adopt it before older ones? If so, young customers' buying is an early-warning signal for stocking decisions.
2. **Lifecycle — How long does a fashion item live?** How fast does demand peak and fade after launch, and how does that differ by garment group? This sets the window for full-price selling and the timing of markdowns.
3. **Discount — Do discounts buy loyalty or bargain hunters?** Are customers whose first purchase was marked down less likely to come back, and more likely to keep buying only on sale?

**Baseline (the standard picture, used as a benchmark for the questions above):**
- **Retention:** What share of new customers buy again, and how does retention decay month by month (cohort retention)?
- **Customer value:** How concentrated are sales among the top customers, and what do the RFM segments (Champions, Loyal, At-Risk, One-timers) look like?

## Data
Public dataset from the Kaggle competition [H&M Personalized Fashion Recommendations](https://www.kaggle.com/competitions/h-and-m-personalized-fashion-recommendations/data): transactions (~31M rows, Sep 2018 – Sep 2020), customers (~1.4M), articles (~105K). The raw data is not included in this repo; download it from Kaggle and place the three CSVs in `data/raw/`.

The project uses **two data layers**:

| Layer | Used for | Why |
|---|---|---|
| Random 10% of customers, with all of their transactions | Discount → loyalty | Customer questions need each customer's full history; sampling by customer (not by row) keeps it intact |
| All 31M transactions, aggregated by week | Lifecycle, trend spread, reference prices | Product questions need every sale; a 10% sample leaves most articles too thin |

### Key definitions
- **Basket:** one customer on one date (there is no order ID).
- **Markdown purchase:** bought at ≤ 85% of that article's reference (max) price in the same sales channel. Results are checked at other thresholds too.
- **Launch week:** an article's first week with sales. Articles first seen in the first 8 weeks or the last 12 weeks are excluded (their true launch or end is outside the data).
- **New customer:** first purchase between Mar 2019 and Feb 2020, after a 6-month burn-in, so they are genuinely new and have a 180-day follow-up window.

### Limitations
- Prices are scaled by H&M, not real currency, so money figures are a **sales value index**.
- `sales_channel_id` (1 vs 2) is undocumented; channel 2 is assumed to be online.
- Markdowns are inferred from price changes; the data has no promotion flag.
- No returns data.
- Results show association, not causation (e.g. discount-led customers may differ in other ways).
- Historical data (2018–2020), including the COVID period; findings describe that period, not today.

## Approach
`Raw CSVs → customer sample + weekly aggregates (pandas) → MySQL → SQL analysis → pandas analysis → Power BI → findings & recommendations`

| Folder | Contents |
|---|---|
| `notebooks/` | Sampling, aggregation, cleaning, analysis |
| `sql/` | MySQL schema and analysis queries |
| `powerbi/` | Dashboard file and screenshots |
| `reports/` | Figures and the final write-up |

## Key findings
_To be added after analysis._

1.
2.
3.

## Recommendations
_To be added._

## How to reproduce
```bash
pip install -r requirements.txt
# place transactions_train.csv, customers.csv, articles.csv in data/raw/
jupyter notebook notebooks/01_sample_and_profile.ipynb      # then:
jupyter notebook notebooks/02_full_weekly_aggregates.ipynb
```
