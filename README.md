# DFWC Wound Healing Prediction Calculator

Research calculator estimating the probability that a diabetic foot ulcer heals
within 180 days.

**Live calculator: https://davidstonko.github.io/dfwc-calc/**

> **Not for clinical decision-making.** This tool is provided for research and educational
> purposes and must not be used to guide the care of an individual patient.

## The model

A four-variable logistic regression with a Platt recalibration, developed on 1,141
diabetic foot ulcers from 387 patients treated at the Johns Hopkins Diabetic Foot and
Wound Center between 2012 and 2025, split 755/386 by patient. A further 114 wounds were
excluded for having neither a definitive outcome nor 180 days of observation. Reported
per TRIPOD+AI.

Every input is available at the first visit:

| Predictor | Odds ratio (95% CI) | p |
|---|---|---|
| Toe location (vs. other) | 2.21 (1.53 to 3.20) | <0.0001 |
| University of Texas stage (per stage) | 0.70 (0.59 to 0.82) | <0.0001 |
| WIfI wound grade (per grade) | 0.62 (0.47 to 0.80) | 0.0003 |
| Noncompressible tibial vessels (vs. not) | 0.58 (0.42 to 0.80) | 0.0010 |

The University of Texas term is the **stage**, the letter of the UT score, which grades
infection and ischemia. It is not the depth grade. The variable is filled from the letter,
verified by cross-tabulating it against the raw string across all 1,255 wounds: A maps to
0 (n=406), B to 1 (368), C to 2 (307), D to 3 (174).

Noncompressible tibial vessels are asked for and the ankle-brachial index is not, because
the calcification phenotype carries the signal and the index value does not.

The equation, at full precision:

    L = 1.715091 - 0.481996*W - 0.362137*UTstage + 0.791939*Toe - 0.546142*NC
    p = 1 / (1 + exp(-(0.034127 + 0.917520*L)))

W and UTstage run 0 to 3; Toe and NC are 1 or 0.

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

On the 386 held-out wounds: **AUC 0.684** (95% CI 0.625 to 0.741). WIfI stage alone
reaches 0.625, so the margin is **+0.059** (95% CI 0.012 to 0.109, p=0.008). At the
0.5973 cut-point, the Youden-optimal operating point rather than a 50/50 line:
sensitivity 70%, specificity 61%, PPV 79%, NPV 49%. Calibration slope 0.859,
Hosmer-Lemeshow 22.29 (p=0.004), Brier 0.199.

**The negative result matters more than the model.** Entering the three WIfI component
grades directly, with no model, reaches 0.682 against this model's 0.684. Across fifteen
approaches, including tuned gradient boosting, random forests and an elastic net over 77
candidate variables, nothing significantly beat the three component grades. This
calculator is a convenience, not evidence that modelling adds anything.

Calibration is imperfect: the slope of 0.859 pulls the extremes toward the middle, so high
probabilities are somewhat optimistic and low ones somewhat pessimistic. Read the number
as a rank rather than a precise risk.

Developed and internally validated at a single centre. Not externally validated.

## Files

- `index.html` — the calculator. Runs entirely in the browser, sends nothing anywhere, and
  works offline once loaded.

The web implementation was verified against the source pipeline on all 386 held-out
wounds: maximum absolute difference 3.4e-13, and the cut-point reproduces the published
confusion matrix exactly (TP 185, FP 48, FN 78, TN 75).

## Citation

Stonko DP, Hicks CW, Sherman RL, Abularrage CJ. Data visualization and prediction of
diabetic foot ulcer healing: WIfI component grades outperform the composite stage and more
sophisticated models. Presented at the Eastern Vascular Society 40th Annual Meeting,
Washington DC, September 2026.

Division of Vascular Surgery and Endovascular Therapy, Department of Surgery,
The Johns Hopkins Hospital, Baltimore, MD.

Approved by the Johns Hopkins Medicine IRB, IR00076176.
