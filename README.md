# outpatient-noshow-prediction
Predicting missed appointments to improve outpatient imaging capacity

Outpatient imaging runs on fixed capacity. Every echocardiogram occupies a room, a machine, and a credentialed sonographer for a scheduled block of time. When a patient doesn't arrive, that time can't be recovered or resold - and someone else who needed the appointment waited longer.

Most scheduling departments respond by sending identical reminders to everyone. This project asks whether the data already captures at booking can identify which appointments are actually at risk, so limited staff attention goes where it matters.

**[View the interactive dashboard ->](https://public.tableau.com/views/OutpatientImagingNo-ShowRiskDashboard/No-ShowRiskDashboard)**

## Key findings

- **Lead time is the dominant predictor. ** Same-day appointments are missed 4.6% of the time; appointments booked 31-60 days out are missed 34.1% of the time.
- **Prior history compounds risk. ** No-show rate climbs from 18.6% with no prior misses to 50.3% after five.
- **The two factors interact. ** A patient booked 15-30 days out with four prior no-shows misses 69% of the time - nineteen times the lowest-risk group.
- **Ranking beats classification. ** At the default threshold the model is *less* accurate than assuming everyone attends. But its top-ranked 10% of appointments contains 2.19x the base no-show rate, which is what a scheduler can actually act on.

## Approach

| Step | Method |
|---|---|
| Hypothesis test | Welch's t-test on lead time (t = 58.28, p < 0.001) |
| Assumption check | Shapiro-Wilk rejected normality; Mann-Whitney U confirmed the result |
| Effect size | Cohen's d = 0.47 (medium) |
| Models | Logistic regression (AUC 0.670) vs. random forest (AUC 0.735) |
| Evaluation | Held-out 20% test set, stratified; precision, recall, AUC-ROC, lift |

The random forest ranks better; logistic regression explains better through
interpretable odds ratios. In practice you'd use the forest to prioritize and the
regression to explain why.

## Data

[Medical Appointment No Shows](https://www.kaggle.com/datasets/joniarroba/noshowappointments)
(Kaggle, Aquarela Analytics) - 110,527 outpatient appointments, Brazilian public health
system, 2016. De-identified and publicly licensed.

Cleaning removed 13 records (0.012%) with impossible values: one negative age, five
negative lead times, and seven implausible ages clustered at 102 and 115. The overall
no-show rate was 20.19% before and after cleaning.

Two issues worth flagging for anyone reusing this data: the `No-show` column is
reverse-coded, where "Yes" means the patient did **not** attend, and `Handcap` holds
values 0–4 rather than the binary flag its name implies.

## Ethical note

No-show risk correlates with socioeconomic disadvantage. The welfare-program indicator
was deliberately excluded from the model's predictors, and the output is intended to
prioritize outreach — never to deny, delay, or deprioritize care.

## Repo contents

- `no_show_analysis.ipynb` — full analysis, runs top to bottom
- `figures/` — distribution plots, lead time bands, ROC curve
- `outputs/` — cleaned extract and summary tables

## Limitations

Brazilian public-system data shows a much higher baseline no-show rate than US academic
imaging centers report (2–6.5%). The *relationship* between lead time and no-show risk
replicates findings from Rosenbaum et al. (2018) and Harvey et al. (2017) in US imaging
settings, but the absolute rates would not transfer. The dataset also lacks appointment
time of day, imaging modality, and insurance type — all of which prior work identifies
as meaningful predictors.

---

Built as the capstone for a B.S. in Data Management & Analytics. The domain framing
comes from 16 years as a cardiovascular sonographer.
