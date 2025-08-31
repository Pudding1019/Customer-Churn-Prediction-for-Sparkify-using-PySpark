# Sparkify Churn Prediction (mini → full, scalable)

Predict user churn for a music streaming service using **PySpark**.  
This repo contains a **mini prototype notebook** for EDA & model comparison and a **full-scale EMR pipeline** that trains on the complete dataset in **S3**.

> **Deliverables**: reusable Spark SQL/ML pipeline (clean → label → feature → de-correlate → train → evaluate), model metrics, and feature importance.

---

## TL;DR
- **Goal**: Identify and predict churn to support retention/recall campaigns.
- **Label**: `Churn = 1` if user ever hits `page == "Cancellation Confirmation"`.
- **Features**: usage intensity (daily means), session duration, tenure, ads exposure, negative feedback, social/playlist actions, level/gender/region, etc.
- **Models**: LR / RandomForest / GBT (mini); **RandomForest** with CV on full data (EMR).
- **Metrics**: AUC-PR, weighted Precision/Recall/F1 + per-class report.
- **Scalability**: same pipeline runs on laptop (mini) and EMR (full).

---

## Data
- **Mini**: `mini_sparkify_event_data.json` (local/Colab)
- **Full**: `s3://udacity-dsnd/sparkify/sparkify_event_data.json` (EMR/S3)
- Typical fields: `userId, ts, page, level, sessionId, itemInSession, length, location, gender, auth, ...`

---

## Method
1. **Cleaning**: drop empty `userId`, parse timestamps, extract state/region from `location`.
2. **Labeling**: user-level churn via window agg on `"Cancellation Confirmation"`.
3. **Features**:
   - Counts & **daily averages** for `NextSong`, `Thumbs Up/Down`, `Add Friend`, `Add to Playlist`, `Roll Advert`, `Help`, `Error`…
   - **Session duration**, **tenure** (registration → last activity), active days.
   - Categorical (level/gender/region; region one-hot in the mini notebook).
4. **De-correlation**: correlation matrix → keep one representative per highly correlated group (the one most related to label).
5. **Modeling**:
   - **Mini**: LR / RF / GBT + grid search; plots & feature importance.
   - **Full (EMR)**: RF with CV (`numTrees`, `maxDepth`), report AUC-PR / F1.
6. **Interpretation**: RF feature importance to surface key drivers (usage intensity, ads exposure, thumbs-down, tenure, etc.).

---


