# Retail Sales Analytics Dashboard (Power BI)

A nine-page Power BI report on three years of retail sales. The most important finding was a measurement problem: only about 62% of calendar days have recorded sales, so raw yearly totals look volatile. Normalised to **revenue per active day**, the business grew steadily: **+34% in 2018 and +113% in 2019**.

![Executive Summary](images/01_executive_summary.png)

---

## Business Questions

1. Is revenue growing, and is that trend real or an artefact of the data?
2. Which months, regions, stores, categories and products drive revenue?
3. How concentrated is revenue, and how much of the catalogue never sells?
4. Do promotions and price changes actually move sales?
5. Is inventory healthy, and is fulfilment a problem?

## Data

| Table | Contents |
|---|---|
| `sales` | 9,576 order lines, order dates 2017 to December 2019 |
| `products` | 699 products, 10 categories, 53 sub-categories, about 205 brands |
| `stores` | 144 stores in 35 US states, 4 store types, latitude and longitude |
| `DateTable` | Calendar table built in DAX for time intelligence |

**Provenance:** this is a practice retail dataset of unknown public origin, recovered from an earlier Power BI file. It is used here to demonstrate BI modelling and analysis, not as a claim of real company data. Treat the numbers as illustrative.

## Tools

Power BI Desktop: Power Query (M), star-schema data model, DAX (time intelligence, RANKX, calculated columns, Haversine distance), slicer-driven store detail page, conditional formatting, built-in City Park theme. No SQL: this project is deliberately BI-only.

## Data Quality: What Was Found and Decided

Most of the effort went into discovering that the data could not be taken at face value. Full detail is in [`Data_Quality_Log.md`](Data_Quality_Log.md).

| Issue | Decision |
|---|---|
| 26% of sales, revenue and stock values are null, **all in Nov-Dec 2019** | Treated as a data-collection cutoff. Kept as null (not zero) and excluded from trend visuals with an `IsCompletePeriod` flag |
| Uneven daily coverage: 364 active days in 2017 versus 144 in 2018 and 131 in 2019 | Compared years on revenue per active day, not raw totals |
| 69 products (9.9%) carry category tags that do not match the product (for example a tomato filed as a flower) | Found with a rule-based keyword check in Power Query. Excluded from category visuals, kept in revenue totals, no guessed corrections |
| Promo blanks are all tied to promo code PR14 ("no promotion") | Relabelled "No Promotion" |
| Mixed store-size units (m2 and qm) | Converted to a numeric column |

## The Nine Pages

| # | Page | Question | Headline result |
|---|---|---|---|
| 1 | Executive Summary | Is the business healthy? | Revenue per active day: 87 (2017), 117 (2018), 249 (2019) |
| 2 | Trend and Seasonality | When does revenue peak? | January is the weakest month every year; the peak month shifts (Oct 2017, May 2018, Aug 2019) |
| 3 | Geography and Coverage | Where, and does store size matter? | Size has only a weak relationship with revenue |
| 4 | Product and Category | Which categories grow? | Gourmet and World Food +63%; Foodgrains +11% |
| 5 | Pareto (80/20) | How concentrated is revenue? | 29% of selling products make 80% of revenue; 41% of stores |
| 6 | Promotion and Price | Do promotions work? | 16% of order lines, 26% of revenue; price barely moves units |
| 7 | Velocity, Stock and Fulfilment | Is inventory healthy? | 419 shortage cases and 241 excess cases; delivery lag is a uniform 2-3 days |
| 8 | Store Detail | What about one store? | Pick a store in the slicer to see its KPIs, trend, categories and top products |
| 9 | Key Insights | What should the business do? | See below |

Page images are in [`images/`](images/).

## Key Findings

- **Growth is real once corrected for coverage.** +34% in 2018, +113% in 2019 on a per-active-day basis.
- **Revenue depends on hit products more than on locations.** 115 of 391 selling products (29%) generate 80% of revenue; it takes 58 of 143 stores (41%).
- **30% of the catalogue has never been ordered** (213 of 699 products). A further 95 have orders but no recorded revenue.
- **Promotions help, but not dramatically.** The median product sells about 25% more units when promoted, and about 64% of comparable products sell more. Deeper discounts do not sell proportionally more, and within a product, price barely changes volume.
- **Stock shortage and excess coexist.** 419 order lines show zero stock; 241 show high stock with almost no sales.
- **Delivery is not a bottleneck.** Average lag is 2.45 days, maximum 5.
- **Foodgrains is the third-largest category and the slowest-growing sizeable one** (+11%) while Gourmet and World Food grows 63%.

Full numbers per page are in [`Retail_Analysis_Findings.md`](Retail_Analysis_Findings.md).

## Recommendations

1. **Fix revenue capture first.** Only about 62% of days have data and Nov-Dec 2019 has no revenue. Until that is fixed, month-level conclusions are provisional.
2. **Review the 213 never-ordered products for delisting.**
3. **Protect the roughly 115 products that carry 80% of revenue** and rebalance stock shortage versus excess.
4. **Be selective with promotions.** Favour targeted lighter promotions (PR05, PR03) and test product by product.
5. **Watch Foodgrains** before its slow growth drags the total.

## Limitations

- Data source is unknown and may be synthetic.
- About 38% of days have no records and Nov-Dec 2019 revenue is missing, so seasonality rests on partial years.
- Promotion and price findings show association, not causation.
- Some samples are small (for example, a state with 18 orders).


## How to Open

Open `dashboard/Retail_Analysis.pbix` in Power BI Desktop. The data is embedded in the file.
