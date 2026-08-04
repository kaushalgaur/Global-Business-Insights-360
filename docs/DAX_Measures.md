# DAX Measures — Global Business Insights 360

This document lists all 40+ DAX measures used to build the Finance, Sales, Marketing, Supply Chain, and Executive views of this project, organized by category.

---

## 1. Revenue & Sales Measures

```dax
GS $ = SUM(fact_actuals_estimates[gross_sales_amount])

NIS $ = SUM(fact_actuals_estimates[net_invoice_sales_amount])

NS $ = SUM(fact_actuals_estimates[net_sales_amount])

NS $ LY = CALCULATE([NS $], SAMEPERIODLASTYEAR(dim_date[date]))

Quantity = SUM(fact_actuals_estimates[Qty])

Sales Qty = CALCULATE([Quantity], fact_actuals_estimates[date] <= MAX(LastSalesMonth[LastSalesMonth]))

Pre Invoice Deduction $ = [GS $] - [NIS $]

Post Invoice Deduction $ = SUM(fact_actuals_estimates[post_invoice_deductions_amount])

Post Invoice Other Deduction $ = SUM(fact_actuals_estimates[post_invoice_other_deductions_amount])

Total Post Invoice Deduction = 'Key Measure'[Post Invoice Deduction $] + 'Key Measure'[Post Invoice Other Deduction $]
```

---

## 2. Cost & COGS Measures

```dax
Manufacturing Cost $ = SUM(fact_actuals_estimates[manufacturing_cost])

Freight Cost $ = SUM(fact_actuals_estimates[Freight_cost])

Other Cost $ = SUM(fact_actuals_estimates[other_cost])

Total COGS $ = 'Key Measure'[Freight Cost $] + 'Key Measure'[Manufacturing Cost $] + 'Key Measure'[Other Cost $]

Ads & Promotions $ = SUM('fact_actuals_estimates'[ads_promotions])

Other Operational Expense $ = SUM('fact_actuals_estimates'[other_operational_expense])

Operational Expense $ = ([Ads & Promotions $] + [Other Operational Expense $]) * -1
```

---

## 3. Profitability Measures

```dax
Net Profit $ = [GM $] + [Operational Expense $]

Net Profit % = DIVIDE([Net Profit $], [NS $], 0)

Net Profit % LY = CALCULATE([Net Profit %], SAMEPERIODLASTYEAR(dim_date[date]))
```

---

## 4. Benchmark (vs LY / vs Target) Measures

These power the "vs LY" / "vs Target" toggle button on the Finance view using a disconnected selector table (`Set BM`).

```dax
NS BM $ =
SWITCH(TRUE(),
    SELECTEDVALUE('Set BM'[ID]) = 1, [NS $ LY],
    SELECTEDVALUE('Set BM'[ID]) = 2, [NS Target $]
)

NS Target $ =
VAR tgt = SUM(NsGmTarget[ns_target])
RETURN IF([Customer / Product Filter Check], BLANK(), tgt)

NP % BM =
SWITCH(TRUE(),
    SELECTEDVALUE('Set BM'[ID]) = 1, [Net Profit % LY],
    SELECTEDVALUE('Set BM'[ID]) = 2, [NP Target %]
)

NP Target % = DIVIDE([NP Target $], SUM(NsGmTarget[np_target]), 0)

NP Target $ = SUM(NsGmTarget[np_target])

BM Message = IF([NS BM $] = BLANK() || [GM % BM] = BLANK() || [NP % BM] = BLANK(),
    "BM Target is not available for the selected filters", "")
```

---

## 5. Forecast Accuracy & Supply Chain Measures

```dax
Forecast Qty =
VAR lsalesdate = MAX(LastSalesMonth[LastSalesMonth])
RETURN
CALCULATE(SUM(fact_forecast_monthly[forecast_quantity]), fact_forecast_monthly[date] <= lsalesdate)

Net Error = [Forecast Qty] - [Sales Qty]

Net Error % = DIVIDE([Net Error], [Forecast Qty], 0)

Net Error LY = CALCULATE([Net Error], SAMEPERIODLASTYEAR(dim_date[date]))

ABS Error =
SUMX(DISTINCT(dim_date[date]),
    SUMX(DISTINCT(dim_product[product_code]), ABS([Net Error]))
)

ABS Error % = DIVIDE([ABS Error], [Forecast Qty], 0)

ABS Error LY = CALCULATE([ABS Error], SAMEPERIODLASTYEAR(dim_date[date]))

Forecast Accuracy % = IF([ABS Error %] <> BLANK(), 1 - [ABS Error %], BLANK())

Forecast Accuracy % LY = CALCULATE([Forecast Accuracy %], SAMEPERIODLASTYEAR(dim_date[date]))

Risk = IF([Net Error] > 0, "EI", IF([Net Error] < 0, "OOS", BLANK()))
```
> **EI** = Excess Inventory | **OOS** = Out of Stock

---

## 6. Market Share Measures

```dax
Market Share % = DIVIDE(SUM(marketshare[sales_$]), SUM(marketshare[total_market_sales_$]), 0)

Atliq MS % = CALCULATE([Market Share %], marketshare[manufacturer] = "atliq")
```

---

## 7. Revenue Contribution Measures

```dax
RC % = DIVIDE([NS $], CALCULATE([NS $], ALL(dim_market), ALL(dim_customer), ALL(dim_product)))
```

---

## 8. Deduction / Post-Invoice Measures

```dax
post_invoice_deductions_amount =
VAR res = CALCULATE(MAX(post_invoice_deductions[discounts_pct]), RELATEDTABLE(post_invoice_deductions))
RETURN res * fact_actuals_estimates[net_invoice_sales_amount]

post_invoice_other_deductions_amount =
VAR res = CALCULATE(MAX(post_invoice_deductions[other_deductions_pct]), RELATEDTABLE(post_invoice_deductions))
RETURN res * fact_actuals_estimates[net_invoice_sales_amount]
```

---

## 9. Dynamic UI / UX Measures

These measures don't calculate business metrics directly — they drive dynamic titles, filter-state checks, and conditional UI text across the report.

```dax
Customer / Product Filter Check = ISCROSSFILTERED(dim_product[product]) || ISFILTERED(dim_customer[customer])

Selected P & L Row = IF(HASONEVALUE('P & L Rows'[Description]), SELECTEDVALUE('P & L Rows'[Description]), "Net Sales")

Performance Visual Title = [Selected P & L Row] & " Performance Over Time"

Top / Bottom N Title = "Top / Bottom Products & Customers By " & [Selected P & L Row]

Sales Trend Title = "NS & GM % For " & SELECTEDVALUE(dim_customer[customer])

Last Sales Month Home = "Sales Data Loaded Until : " & FORMAT(MAX(LastSalesMonth[LastSalesMonth]), "MMM YY")
```

---

## Key DAX Patterns Used

| Pattern | Purpose | Example |
|---|---|---|
| `SAMEPERIODLASTYEAR()` | Time-intelligence YoY comparisons | `NS $ LY`, `Net Profit % LY` |
| `SWITCH(TRUE(), ...)` | Clean toggle logic instead of nested IFs | `NS BM $`, `NP % BM` |
| `ISFILTERED()` / `ISCROSSFILTERED()` | Detect drill-down state to conditionally blank out aggregate-only values | `Customer / Product Filter Check` |
| `DIVIDE()` | Safe division avoiding divide-by-zero errors | `Net Profit %`, `Forecast Accuracy %` |
| `ALL()` | Strip filter context to calculate grand totals | `RC %` |
| `HASONEVALUE()` + `SELECTEDVALUE()` | Detect single selection for dynamic titles | `Selected P & L Row` |
| `RELATEDTABLE()` | Pull related rows across a relationship inside CALCULATE | `post_invoice_deductions_amount` |
| Disconnected selector tables | Power the "vs LY / vs Target" bookmark-driven toggle | `Set BM[ID]` |

---

*Formulas verified against source MySQL/Excel data during model validation (Step 3 of project build).*
