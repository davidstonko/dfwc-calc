# DFWC Wound Healing Prediction Calculator

Research calculator estimating the probability that a diabetic foot ulcer will heal.

**Live calculator: https://davidstonko.github.io/dfwc-calc/**

> **Not for clinical decision-making.** This tool is provided for research and educational
> purposes and must not be used to guide the care of an individual patient.

## The model

A seven-feature logistic regression with a Platt recalibration, developed on 1,255
diabetic foot ulcers from 422 patients treated at the Johns Hopkins Diabetic Foot and
Wound Center between 2012 and 2025, split 70/30 by patient. Reported per TRIPOD+AI.

Predictors, with odds ratios on the native scale (per 1 unit for counts, scores and depth
in centimetres; presence vs. absence for the two binary predictors):

| Predictor | Odds ratio (95% CI) | p |
|---|---|---|
| Toe location (vs. other) | 1.98 (1.28–3.08) | 0.002 |
| Wound depth (per cm) | 1.69 (1.26–2.27) | <0.001 |
| Total wounds per patient (per 1) | 1.66 (1.44–1.91) | <0.001 |
| Aspirin (vs. none) | 1.51 (1.05–2.17) | 0.027 |
| University of Texas stage (per 1) | 0.73 (0.58–0.92) | 0.008 |
| WIfI wound score (per 1) | 0.58 (0.43–0.78) | <0.001 |
| Wound number (per 1) | 0.57 (0.49–0.66) | <0.001 |

Wound number and total wounds are strongly correlated and pull in opposite directions;
neither coefficient is interpretable on its own.

## Performance

On the 351 held-out wounds: **AUC 0.684** (95% CI 0.61–0.76), exceeding WIfI stage alone
by 0.121 (95% CI 0.030–0.207, p=0.004). At the 71% cut-point — the Youden-optimal
operating point, not a 50/50 line — sensitivity 0.81 and specificity 0.51. Calibration
slope 0.72, Hosmer–Lemeshow p=0.099, Brier 0.144.

An AUC of 0.68 is modest. The model separates healing from non-healing wounds better than
staging does, but it is not a substitute for clinical judgement. It was developed and
internally validated at a single centre and has not been externally validated.

## Files

- `index.html` — the calculator. Runs entirely in the browser, sends nothing anywhere, and
  works offline once loaded.
- `DFWC_HP_ClinicalCalculator_v5.xlsx` — the same model as a spreadsheet, with the
  coefficients at full precision, the complete performance table, and a validation sheet
  that recomputes ten held-out wounds and self-checks.

Both implementations were verified against the source MATLAB model on all 351 held-out
wounds: maximum absolute difference 4.4e-16 for the spreadsheet and 1.1e-12 for the web
version.

## Citation

Stonko DP, Hicks CW, Sherman RL, Abularrage CJ. Data visualization and machine-learning
prediction of wound healing in diabetic foot ulcers: a single-center analysis using the
WIfI classification system. Presented at the Eastern Vascular Society 40th Annual Meeting,
Washington DC, September 2026.

Division of Vascular Surgery and Endovascular Therapy, Department of Surgery,
The Johns Hopkins Hospital, Baltimore, MD.

Approved by the Johns Hopkins Medicine IRB, IR00076176.
