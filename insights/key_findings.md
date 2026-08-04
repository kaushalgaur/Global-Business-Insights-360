# Key Findings — Global Business Insights 360

A quick-scan summary of the standout insights this dashboard surfaced. For full detail, explore the `.pbix` file or the dashboard screenshots in `/visuals`.

---

## 🔍 The Core Problem, Confirmed by Data

AtliQ Hardware suspected Latin America was underperforming — but decisions were previously based on intuition, not data. This dashboard confirmed it with hard numbers:

> **LATAM contributed just 0.4% of total Revenue Contribution (RC%)** out of $823.85M in global Net Sales — validating the original business concern was real, not a hunch.

---

## 📉 Revenue Was Never the Real Problem — Margin Was

| Metric | Value |
|---|---|
| Net Sales | $823.85M |
| Gross Margin % | 36.49% |
| **Net Profit %** | **-6.63%** |

Despite strong top-line revenue, the business was operating at a **net loss**. The Operational Expense (-$355.28M) was eating through the entire Gross Margin ($300.63M) and then some — meaning the fix isn't "sell more," it's **cost control and operational efficiency.**

---

## 📦 Forecast Accuracy Gaps Are Driving Inventory Risk

| Segment | Forecast Accuracy % | Risk |
|---|---|---|
| Networking | 90.40% | Excess Inventory (EI) |
| Desktop | 84.37% | Excess Inventory (EI) |
| Storage | 83.54% | Excess Inventory (EI) |
| Peripherals | 83.23% | Out of Stock (OOS) |
| Notebook | 79.99% | Out of Stock (OOS) |
| **Accessories** | **77.66%** | **Out of Stock (OOS)** |

**Accessories has the weakest forecast accuracy of all product segments**, directly tied to a Net Error % of -7.06% — meaning actual demand consistently outpaced what was forecasted, risking stockouts on one of the company's core categories.

Overall forecast accuracy sits at **80.21%**, up from 72.99% last year (+9.88%) — improving, but still leaving real gaps at the segment level.

---

## 🌍 Regional Performance Is Highly Uneven

| Sub Zone | Net Sales | Revenue Contribution | Net Profit % |
|---|---|---|---|
| India | $210.7M | 25.6% | -24.7% |
| ROA | $186.9M | 22.7% | 8.2% |
| NA | $177.9M | 21.6% | -13.7% |
| NE | $109.3M | 13.3% | -1.1% |
| SE | $91.5M | 11.1% | 4.4% |
| ANZ | $44.4M | 5.4% | 7.3% |
| **LATAM** | **$3.2M** | **0.4%** | **6.2%** |

Interestingly, **India — the single largest revenue contributor at 25.6% — is also running the worst Net Profit % at -24.7%**, a bigger red flag than LATAM's small revenue share. High revenue doesn't always mean high value.

---

## 🏆 Where Things Are Working

- **AltiQ Exclusive** (customer) delivers the strongest margin profile: 46.10% GM% on $69.15M in Net Sales.
- **Networking** segment has the best forecast accuracy (90.40%) — a benchmark other segments could learn from.
- Overall Forecast Accuracy improved **+9.88% YoY** (72.99% → 80.21%), showing the forecasting process is trending in the right direction.

---

## 💡 What This Means for the Business

1. **Investigate operating costs before chasing more revenue** — the -6.63% Net Profit % is a cost problem, not a demand problem.
2. **Prioritize forecasting fixes for Accessories** — it has both the weakest accuracy and a stockout risk on a high-volume category.
3. **Re-audit India's cost structure** — the largest market is also the least profitable; worth deeper investigation than LATAM's small revenue share alone suggests.
4. **Use Networking's 90.40% forecast accuracy as an internal benchmark** to improve other segments.

---

*All figures sourced directly from the Power BI model (`fact_actuals_estimates`, `fact_forecast_monthly`) and cross-validated against source MySQL/Excel data during the project build (see project write-up in main README).*
