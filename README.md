# American Express Campus Challenge 2026 — Round 1

**Customer Profitability Framework for the Premier Card**
Public leaderboard score: **0.921**

## Problem

Given a dataset of 500,000 Premier Cardmembers (masked attributes covering spend
behavior, revolving patterns, riskiness, and benefit utilization), design a
framework/equation to rank-order cardmembers by estimated profitability to the
issuer. Accuracy is measured as the overlap between the predicted top-20% most
profitable members and the actual top-20% (evaluated on a 70/30 public/private
split). Full problem statement is included in this repo.

## Approach

Profitability is modeled as:

```
Profit = Revenue − Risk-Adjusted Loss − Benefit Cost
```

- **Revenue**: interchange on category spend (mildly concave transform, power
  ≈0.75–0.85, to reflect diminishing marginal interchange value) + interest
  income on revolving balance.
- **Risk adjustment**: a proportional haircut on revenue, driven by a risk
  score plus a collections/cancellation-call signal, capped at 95% to avoid
  degenerate scores.
- **Benefit cost**: issuer's real cost of premium perks (lounge visits, airline
  credit, cab credit, entertainment credit) subtracted directly.

Feature selection was done via correlation screening — a "total spend" field
was found to be near-noise (rank correlation ≈0.01 with category-level spend)
and dropped in favor of the granular category spends.

The model was tuned by iterative, single-variable ablation against the public
leaderboard (no ground-truth labels were shared). Two additional techniques —
a rank-averaged ensemble of a proportional-risk model and a dollar-loss
(expected-credit-loss style) model, and a hard risk-score cutoff — were
benchmarked against the single-formula baseline; the tuned single formula
performed best.

## Repo contents

| File | Description |
|---|---|
| `problem_statement.pdf` | Official Round 1 problem statement and evaluation criteria. |
| `requirements.txt` | Python dependencies. |

## Usage

```bash
pip install -r requirements.txt
```

The script expects the dataset CSV and the official `.xlsx` submission
template in the working directory — see the `DATA_PATH` and `TEMPLATE_PATH`
constants at the top of `build_submission.py`.

## Notes

- No new rows were added and no shared data was altered, per competition
  rules.
- No identifier fields were used in the scoring logic.
- Runs on all 500,000 records in the dataset.

## Disclaimer

This repo reflects independent coursework/competition work submitted to the
American Express Campus Challenge 2026. It is not affiliated with, endorsed
by, or representative of American Express's internal systems, models, or
proprietary data.
