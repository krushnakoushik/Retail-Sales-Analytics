# Data Quality Log

Every issue found while cleaning, how it was detected, what was decided, and why.

| # | Table | Issue | How found | Decision | Why |
|---|---|---|---|---|---|
| 1 | sales | 26% nulls in sales, revenue and stock (2,468 rows) | Nulls sat on the same rows; all fall in Nov-Dec 2019 | Leave as null; exclude Nov-Dec 2019 from trend visuals via `IsCompletePeriod` on the date table | Filling with zero would claim "no sales happened" when the truth is "not recorded". Exclusion keeps the trend honest |
| 2 | sales | Daily coverage differs by year: 364 active days (2017), 144 (2018), 131 (2019); about 62% overall | Counted distinct order dates per year | Compare years on **revenue per active day** | Raw yearly totals compared unequal amounts of captured activity and showed a false -47% then +142% swing |
| 3 | products | 69 products (9.9%) with category, type or sub-category unrelated to the product name | Spotted a tomato filed under flowers; built a keyword rule (food words in non-food categories and the reverse); excluded pet food after it produced false positives | Excluded from category-level visuals; kept in revenue totals | A keyword rule detects well but corrects poorly. Guessing replacements would invent data |
| 4 | sales | `promo_bin` blank on 52% of rows and `price` blank on about 4% | All tied to promo code PR14, which means "no promotion" | Relabel as "No Promotion"; filter price blanks only on the price page | The blank is structural, not missing |
| 5 | stores | Store size mixed `m2` and `qm` text | Manual inspection | Convert to a numeric square-metre column | `qm` is German for square metre, so no value conversion was needed |
| 6 | sales | Two order-date and two delivery-date versions with different day/month ordering | Delivery lag was implausible under one pairing and a consistent 2 days under the other | Kept the pair that gives a median 2-day lag (100% of orders within 10 days) | Wrong pairing silently shifts months and invents multi-month delays |
| 7 | products | One product missing name and brand | Null check | Label "Unknown Product" / "Unknown Brand" | Avoids "(Blank)" labels in charts |
| 8 | products | 2-3% blanks in length, width, depth, volume | Null check | Left as is | Not used in any analysis |
| 9 | model | Unrelated customer and order tables embedded in the original file | Model review | Dropped | Different business, no relationship to the retail tables |

## Methodology Notes

- **Revenue per active day** = total revenue divided by the number of distinct order dates in the same filter context.
- **Date coverage** = active days divided by calendar days in the complete period.
- The 69-product list is produced by a Power Query custom column (`CategoryCheck`), so the detection logic is visible inside the report rather than hard-coded.

## Known Caveats

- After the Nov-Dec 2019 cutoff, products and stores whose only orders fall in that window show no revenue (95 products; 1 store). That reflects missing data, not missing sales.
- Seasonality is based on partial years and uneven coverage, so month-level conclusions are provisional.
