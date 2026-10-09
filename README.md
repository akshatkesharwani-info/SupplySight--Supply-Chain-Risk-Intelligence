# SupplySight: Supply Chain Risk Intelligence

A supplier risk toolkit: score every supplier, **check the score against real delivery history**, measure how concentrated spend is by country and supplier, test whether a model can predict late deliveries, and see how much spend a country-level disruption would expose. An AI-written memo turns the calculated numbers into mitigation actions for the five riskiest suppliers.

Built in Google Colab with Groq (`openai/gpt-oss-120b`).

> **The supplier and shipment data is made up** (60 suppliers, 4,000 shipments, with lateness generated partly from each supplier's on-time rate). The results show the method, not a real supply chain. The notebook also accepts your own `suppliers.csv` and `shipments.csv`.

## What it does

1. **Risk score (0 to 100):** a weighted mix of late-delivery risk (25%), defect rate (15%), financial weakness (20%), lead time (10%), country risk (20%) and single sourcing (10%), each scaled from 0 to 1. Suppliers fall into High (top 20%), Medium and Low tiers. The weights are my own assumptions.
2. **Validates the score** against shipment history: do High-risk suppliers really deliver late more often?
3. **Concentration risk:** spend share by country and by supplier, with the HHI index (under 1,500 low, 1,500 to 2,500 moderate, above 2,500 high).
4. **Late-delivery model** (Gradient Boosting), compared with a one-line rule: "use 1 minus the supplier's on-time rate".
5. **Disruption scenarios:** if everything from one country stopped, how much spend, how many single-source suppliers and what average risk is exposed, combined into a priority score.
6. **AI risk memo (Groq):** the AI only gets calculated numbers (including the HHI label and the top-5 spend share), must not calculate new numbers, invent dates or add a currency symbol, and says once that the data is made up.

## Results from the run (made-up data)

| Measure | Result |
|---|---|
| Suppliers / shipments / total annual spend | 60 / 4,000 / 13,754,940 |
| Risk tiers | High 12 (18.3% of spend), Medium 24 (32.1%), Low 24 (49.7%) |
| **Actual late rate by tier** | **High 33.5%, Medium 20.9%, Low 14.3%** |
| Rank correlation, risk score vs actual late rate | 0.61 |
| HHI by country / by supplier | 1,352 / 288, both **low** concentration |
| Top 3 countries / top 10 suppliers | 51.4% / 41.1% of spend |
| The 5 riskiest suppliers | 8.0% of spend |
| Single-source suppliers | 13 of 60 |

**Late-delivery model:** AUC 0.675 against 0.678 for the one-line on-time-rate rule, and accuracy 0.785 against 0.791 for always guessing "on time". So the model is **no better than the simple rule**. Used as a ranking tool, the 10% of shipments it rates riskiest were late 44.6% of the time, against 20.9% overall.

**Disruption priority (assumed weights):** Vietnam first (13.1% of spend exposed, 5 single-source suppliers), then India (16.1%, 3), then Brazil (18.7%, 1).

## What the evaluation showed

- **A risk score must be checked against outcomes.** High-risk suppliers were late 33.5% of the time against 14.3% for Low-risk ones. With made-up data this is partly by design, but on real data it is the real test of the weights.
- **A fancy model is not automatically better.** The supplier's own on-time rate already carries most of the information, and the notebook prints a plain warning when the model does not beat that rule.
- **Concentration depends on the measure.** The country mix looks heavy at the top (three countries hold 51.4%) yet the HHI is low, so an AI that calls it "moderate to high" is wrong. An earlier version of the memo did exactly that, and also invented a currency symbol, a date and a "10%" share that was really 8.0%.

## Limitations

- **Made-up data.** Do not quote these numbers as facts about real suppliers.
- The risk weights and the disruption priority formula are assumptions, not fitted values.
- The disruption scenario assumes a whole country stops, which is extreme and treats suppliers as interchangeable.
- The late-delivery model only uses supplier attributes (no seasonality, routes or order size).
- The memo is a draft written by an AI from calculated numbers, for a human to check.

## Tech stack

pandas, scikit-learn, matplotlib, seaborn, Groq API.

## How to run

1. Open the notebook in Google Colab.
2. Run the cells from top to bottom.
3. When asked, paste your own Groq API key (free at console.groq.com). It is hidden while typing and is never saved in the notebook.

## Files the notebook creates

- `supplysight_suppliers_scored.csv`: every supplier with risk score and tier
- `supplysight_scenarios.csv`: country disruption scenarios with priority scores

---

Built by **Akshat Kesharwani** | [GitHub](https://github.com/akshatkesharwani-info) | [Portfolio](https://akshatkesharwani-info.github.io/)
