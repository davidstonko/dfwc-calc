# DFWC Wound Healing Prediction Calculator

Research calculator estimating the probability that a diabetic foot ulcer heals
within 180 days.

**Live calculator: https://davidstonko.github.io/dfwc-calc/**

> **Not for clinical decision-making.** This tool is provided for research and educational
> purposes and must not be used to guide the care of an individual patient.

## The model

A five-variable logistic regression with a Platt recalibration: the three WIfI component grades (wound, ischemia, foot infection), toe location and noncompressible tibial vessels. It was prespecified as the component grades plus the variables that LASSO stability selection added. Developed on 1,141
diabetic foot ulcers from 387 patients treated at the Johns Hopkins Diabetic Foot and
Wound Center between 2012 and 2025, split 755/386 by patient. A further 114 wounds were
excluded for having neither a definitive outcome nor 180 days of observation. Reported
per TRIPOD+AI.

Every input is available at the first visit:

| Predictor | Odds ratio (95% CI) | p |
|---|---|---|
| Toe location (vs. other) | 2.18 (1.50 to 3.18) | <0.0001 |
| WIfI foot infection grade (per grade) | 1.05 (0.89 to 1.25) | 0.55 |
| WIfI ischemia grade (per grade) | 0.75 (0.64 to 0.87) | 0.0002 |
| WIfI wound grade (per grade) | 0.58 (0.44 to 0.75) | <0.0001 |
| Noncompressible tibial vessels (vs. not) | 0.53 (0.39 to 0.73) | 0.0001 |

The foot infection grade carries no independent weight once the other four terms are in
the model. It is kept so that the model uses all three grades a clinician has already
assigned; dropping it changes the AUC by less than 0.002.

Noncompressible tibial vessels are asked for and the ankle-brachial index is not, because
the calcification phenotype carries the signal and the index value does not.

The equation, at full precision:

    L = 1.555616 - 0.553123*W - 0.293273*I + 0.051720*fI + 0.780178*Toe - 0.633521*NC
    p = 1 / (1 + exp(-(0.046374 + 0.899547*L)))

W, I and fI run 0 to 3; Toe and NC are 1 or 0.

## The outcome

Healing **within 180 days** of presentation. Every wound in the analysis was observed for
at least that long or reached a definitive outcome before it.

Of the 403 wounds not healed by 180 days, 271 healed later, 48 came to major amputation,
43 patients died before the wound resolved, and 41 were still open. "Not healed" here
therefore means not healed yet, not never healed.

The time horizon is what makes the ischemia gradient visible. Counting a wound as a
failure because it was open at last contact, with no regard for how long it had been
followed, hides it entirely: on that definition the ischemia grade shows nothing
(chi-squared 1.64, p=0.65). Anchored at 180 days it runs 70.3% healing at I-0 to 41.1% at
I-2 (chi-squared 38.36, p<0.0001).

## Performance

On the 386 held-out wounds: **AUC 0.700** (95% CI 0.643 to 0.754). WIfI stage alone
reaches 0.625, so the margin is **+0.075** (95% CI 0.026 to 0.124, p=0.001). At the
0.6347 cut-point, the Youden-optimal operating point rather than a 50/50 line:
sensitivity 62%, specificity 72%, PPV 83%, NPV 47%. Calibration slope 0.992,
Hosmer-Lemeshow 12.64 (p=0.13), Brier 0.196.

**The negative result matters more than the model.** Entering the three WIfI component
grades directly, with no model, reaches 0.682 against this model's 0.700, and the
difference (+0.018, 95% CI -0.015 to 0.052) is not statistically significant. Across 23
approaches, including tuned gradient boosting, random forests, support vector machines and
a neural network over 77 candidate variables, nothing significantly beat the three
component grades. This calculator is a convenience, not evidence that modelling adds much.

Calibration is good on the held-out set (slope 0.992, no quintile more than 8 points from
the observed rate), but the model comes from one centre, so read the number as a rank more
than as a precise risk.

Developed and internally validated at a single centre. Not externally validated.

## Files

- `index.html` — the calculator. Runs entirely in the browser, sends nothing anywhere, and
  works offline once loaded.

The web implementation was verified against the source pipeline on all 386 held-out
wounds: maximum absolute difference 1.6e-15, and the cut-point reproduces the published
confusion matrix exactly (TP 164, FP 34, FN 99, TN 89).

## Citation

Stonko DP, Hicks CW, Sherman RL, Abularrage CJ. Data visualization and prediction of
diabetic foot ulcer healing: WIfI component grades outperform the composite stage and more
sophisticated models. Presented at the Eastern Vascular Society 40th Annual Meeting,
Washington DC, September 2026.

Division of Vascular Surgery and Endovascular Therapy, Department of Surgery,
The Johns Hopkins Hospital, Baltimore, MD.

Approved by the Johns Hopkins Medicine IRB, IR00076176.
