Solid work.  You've obviously put a lot of time and though into this.  I did note a few weaknesses though.

The central issue with this project is your spatial framing: ZIP centroid imputation feeding borough × shift aggregation. Both layers are too coarse and non-stationary (zip regions change over time) to support the EMS deployment use case you describe.

So, while you did genuinely clever data engineering to recover ~15K of 28K missing lat/lon values via the intersection-based self-lookup table, you then collapsed all that hard-won precision back down to ZIP centroids and five borough indicators. The hard work to get coordinates right gets washed out immediately, because nothing downstream actually uses spatial precision finer than a borough.

What I'd have wanted to see: divide the city into a uniform grid (say 500m–1km cells, or hex bins via H3), aggregate crashes per cell × shift, and predict count and severity at that granularity. Three benefits flow from that:

1. **It directly answers the EMS question.** "Brooklyn at 5pm is high risk" tells dispatchers nothing they don't know. A map of the top 50 grid cells in expected injury crashes per shift is operationally actionable.
2. **It washes out stochasticity.** Individual intersections see 0–1 crashes per shift; grid cells get stable rates.
3. **It opens the door to data fusion.** Weather, traffic volume, road geometry, transit ridership all come pre-gridded or are trivially gridded. Joining external datasets to ZIP centroids is awkward; joining to grid cells is a single spatial index.

The model-class implication: your logistic regression cannot learn geographic hotspots from borough indicators alone — the resolution isn't there. Your tree models could in principle pick up structure from raw lat/lon, but with this volume of data spread across all of NYC, asking a tree to discover meaningful spatial structure from coordinates is a heavy lift. Engineered grid features make the structure explicit and the modeling problem much easier.

## Strengths

- **Missing-coordinate recovery via intersection-based self-lookup** is  good data engineering. 
- **Identifying the contributing-factors leakage** (police-recorded factors not being available at inference time) is sophisticated thinking — many published papers miss this.
- **Proper temporal train/test split** (2022–23 train, 2024–26 test) instead of random shuffling.
- **Cost-sensitive weighting over SMOTE with explicit reasoning** shows good methodological discipline rather than blind application of textbook fixes.
- **Multiple complementary components** (severity classifier + Poisson volume + per-user-type models) — a more comprehensive system design than most teams attempted.
- **Honest Discussion** about real-time vs. shift-level limitations.
- **Volume of work for a 2-person team is impressive.**

## Weaknesses

- **The feature-importance paragraph contradicts your methodology.** You report contributing factors among the top predictors, but you also state (correctly) that you removed them due to leakage. Either the importance was computed on a model that still included them, or the paragraph wasn't updated to reflect the removal. Worth reconciling.
- **Mediocre performance presented without baselines.** Best ROC-AUC of 0.704 on a 40/60 split is OK, not strong. Logistic Regression's recall of 0.73 sounds good, but precision is 0.53 — not far above "predict severe most of the time." A majority-class baseline would have made the actual lift visible.
- **Poisson volume model is weak.** MAE 3.47 on mean 7.79 = ~45% relative error. You acknowledge this but still propagate the estimates into the dashboard. No comparison to a "predict last-year-same-shift" baseline, which for a stationary process would likely match or beat the Poisson.
- **Hybrid model conclusion is muddled.** LR has best recall; hybrid has best accuracy/AUC; hybrid recall is much lower than LR's. Which wins for the EMS use case? You argue LR, but the hybrid is positioned as the centerpiece. 
- **Road user models treat outcomes as independent.** Three separate logistic regressions for pedestrian/cyclist/motorist injury — but pedestrian-vs-vehicle crashes mechanically cause pedestrian injury. A multinomial or multi-output formulation would have been more principled.
- **Stratified K-fold CV on shuffled data partially defeats the temporal split.** Your CV numbers likely overestimate operational performance. Time-series CV (rolling or expanding window) would have matched the temporal-holdout philosophy.
- **Class imbalance is overstated.** 40/60 is barely imbalanced; the cost-sensitive-weighting story has more rhetorical weight than statistical effect on this dataset. The real imbalance — fatal crashes at 0.27% — gets collapsed away.
- **No threshold analysis for the EMS use case.** If recall matters most, plotting the precision-recall curve and recommending a threshold based on operational tolerance would have been the natural follow-up.


All in all though, solid work and clear effort.

**Team Score: 27/30**


---

## Final Project Grade
| Assessment Item | Adelina Dunina | Arya Patil |
|---|---|---|
| **Proposal (5 pts)** | 5 | 5 |
| **Midterm Report (10 pts)** | 10 | 10 |
| **Final Presentation (5 pts)** | 5 | 5 |
| **Final Report (30 pts)** | 27 | 27 |
| **Weekly Updates (30 pts)** | 30 | 30 |
| **Total (80 pts)** | **77** | **77** |
