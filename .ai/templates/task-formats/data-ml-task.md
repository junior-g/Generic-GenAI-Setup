<!--
FORMAT — data pipeline / ML task. Copy the checklist blocks into the task's block in task_details.md.

Applies to batch and streaming pipelines, transformations, feature engineering, model training, evaluation,
inference, and anything whose correctness is statistical rather than binary.

THE DEFINING DIFFICULTY: the output can be wrong without anything failing. A pipeline that silently drops 30%
of rows exits zero. A model that degraded still returns confident predictions. Nothing in the usual gates
catches either.

THE THREE THINGS DATA/ML TASKS FAIL ON MOST:
  1. No row-count reconciliation, so silent loss goes unnoticed for weeks.
  2. Leakage — information from the target or the future reaching the features, producing an evaluation score
     that cannot survive contact with production.
  3. Non-reproducibility. Nobody can regenerate the artefact that is in production.

ML DOES NOT REPLACE THE GATES. Code in a pipeline is still linted, type-checked, built and tested.
-->

# Data / ML Task Format

## Scope

| Applies to | Does not apply to |
|------------|-------------------|
| Pipelines, transformations, feature engineering, training, evaluation, inference, monitoring of model quality | Schema and migrations (database format), serving endpoints (backend format) |

## 1. Task definition

| | |
|---|---|
| What this produces | dataset / feature / model / evaluation / pipeline stage |
| Consumers of the output | |
| Runs when | batch schedule / streaming / on demand / once |
| **Reversibility** | reversible / compensable / **irreversible** |
| Requirement | R<n> |
| Overwrites existing data or an existing artefact? | |
| Approval required | ☐ |

Overwriting a dataset or a published artefact in place is compensable at best. Write a new version and switch
the pointer; do not overwrite.

## 2. Inputs and provenance

| Input | Source | Owner | Freshness | Schema stability |
|-------|--------|-------|-----------|------------------|
| | `path` / table / topic | | | stable / changes |

| # | Check | Result |
|---|-------|--------|
| 1 | Every input's origin traced to a real source, not assumed | |
| 2 | Input schema validated at read — not trusted | |
| 3 | Behaviour when an input is missing, late, or partial | |
| 4 | Behaviour when an input's schema changes | |
| 5 | Personal data in inputs identified and classified | |
| 6 | Licence or usage restriction on any external data checked | |

## 3. Data quality gates 🔴

**The section that catches silent wrongness.** Every one of these has a threshold and a defined action.

| Check | Threshold | On breach | Result |
|-------|-----------|-----------|--------|
| **Row count vs input** | | fail / warn | |
| Null rate per critical column | | | |
| Duplicate rate on the key | | | |
| Value range / domain per column | | | |
| Referential integrity against the source | | | |
| Distribution shift vs the previous run | | | |
| Freshness — newest record age | | | |
| Cardinality of key categoricals | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | **Row counts reconciled input to output**, with any drop explained | |
| 2 | A quality breach **fails the pipeline** rather than logging and continuing | |
| 3 | Every drop or filter is deliberate and counted | |
| 4 | Quality metrics recorded per run, so a trend is visible | |

Check 2 is the difference between a data quality check and a data quality decoration. A warning nobody reads
is not a control.

## 4. Transformation correctness

| # | Check | Result |
|---|-------|--------|
| 1 | Logic unit-tested on small, hand-checked fixtures | |
| 2 | Edge cases: empty input, single row, all-null column, duplicate keys, extreme values | |
| 3 | Joins verified not to fan out — output row count is what the join type implies | |
| 4 | Aggregations verified against a manually computed example | |
| 5 | Timezone and date-boundary handling explicit | |
| 6 | Numeric precision appropriate — no float where exactness matters | |
| 7 | Ordering not depended on unless explicitly guaranteed | |

Check 3 is the most common silent defect in a pipeline: a join on a non-unique key multiplies rows, totals
inflate, and nothing errors.

## 5. Idempotency and reruns

| # | Check | Result |
|---|-------|--------|
| 1 | Re-running for the same period produces the same output, not doubled | |
| 2 | Partitioned or keyed so a rerun replaces rather than appends | |
| 3 | Late-arriving data handled — restatement policy defined | |
| 4 | Backfill possible for an arbitrary historical range | |
| 5 | Failure mid-run leaves no partial output visible to consumers | |
| 6 | Progress and lineage recorded per run | |

Check 5: write to a staging location and promote atomically. A consumer reading a half-written dataset gets
wrong answers with no error.

## 6. Reproducibility 🔴

| | |
|---|---|
| Random seeds fixed and recorded | ☐ |
| Input data version or snapshot recorded | ☐ |
| Code version recorded with the artefact | ☐ |
| Dependency versions pinned | ☐ |
| Hyperparameters and configuration recorded | ☐ |
| Environment captured | ☐ |
| **Can the exact output be regenerated from the record alone?** | ☐ |

If the last box cannot be ticked, the artefact cannot be debugged, audited, or safely replaced. That is the
whole point of this section.

## 7. Feature engineering — leakage 🔴

Only for model work.

| # | Check | Result |
|---|-------|--------|
| 1 | **No target-derived information in any feature** | |
| 2 | **No future information relative to prediction time** | |
| 3 | Split done **before** fitting any transformation | |
| 4 | Scalers, encoders and imputers fitted on train only, applied to validation and test | |
| 5 | Time-based split for time-ordered data, never a random one | |
| 6 | Grouped entities kept within one split — no entity spanning train and test | |
| 7 | Deduplication before splitting, so the same record is not in both | |
| 8 | Every feature computable at inference time, with the same inputs available | |

**A validation score that looks too good is a leakage report, not a result.** Checks 2 and 8 are the ones that
separate an offline score from a production one: a feature built from data that only exists after the outcome
cannot be computed when the prediction is needed.

## 8. Training and evaluation

| | |
|---|---|
| Metric, and why it is the right one for the decision | |
| Baseline compared against | |
| Threshold for acceptance | |
| Split strategy | |
| Class imbalance handling | |
| Cross-validation scheme | |

| # | Check | Result |
|---|-------|--------|
| 1 | A **baseline** exists — trivial or existing model. A score without one is uninterpretable | |
| 2 | Metric matches the real cost of each error type, not just accuracy | |
| 3 | Test set used **once**, at the end. Repeated use makes it a validation set | |
| 4 | Performance broken down by relevant segment, not only in aggregate | |
| 5 | Confidence interval or variance across runs reported, not a single number | |
| 6 | Failure cases inspected by hand, not only summarised | |
| 7 | Fairness across protected groups examined where the decision affects people | |
| 8 | The comparison against the previous model is like-for-like | |

## 9. Inference and serving

| # | Check | Result |
|---|-------|--------|
| 1 | Training and serving use the **same** feature computation code | |
| 2 | Feature parity verified on a sample: same input, same features, both paths | |
| 3 | Input validated at inference; out-of-domain input handled | |
| 4 | Latency and resource use measured | |
| 5 | Fallback when the model is unavailable or returns nonsense | |
| 6 | Output bounded and sanity-checked before it is used | |
| 7 | Model version recorded with every prediction | |
| 8 | Rollback to the previous model possible, and tested | |

Check 1 is training-serving skew, the classic production failure: two implementations of the same feature
diverge, and the model receives inputs it was never trained on.

## 10. Monitoring

| Signal | Threshold | Alert | Action |
|--------|-----------|-------|--------|
| Input distribution drift | | | |
| Prediction distribution drift | | | |
| Quality metric on labelled outcomes | | | |
| Missing-feature rate at inference | | | |
| Latency | | | |
| Volume | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Degradation is detectable **without waiting for a complaint** | |
| 2 | Ground truth collected so quality can be measured over time | |
| 3 | Retraining trigger defined: schedule, drift, or metric breach | |

## 11. Privacy and ethics

| # | Check | Result |
|---|-------|--------|
| 1 | Personal data minimised; nothing used that is not needed | |
| 2 | Deletion request propagates to derived datasets and, where required, retrained artefacts | |
| 3 | Model output cannot reveal an individual record | |
| 4 | Protected attributes not used, and not reconstructable from proxies | |
| 5 | The decision this affects, and its consequence for a person, stated | |
| 6 | Fixtures and examples use synthetic or placeholder data | |

Check 2 is frequently missed: deleting a row from the source does not remove its influence from a trained
model or a derived aggregate.

## 12. Verification 🔴

| # | Check | Result |
|---|-------|--------|
| 1 | Transformation logic unit-tested on hand-checked fixtures | |
| 2 | Quality gates §3 run, with real numbers recorded | |
| 3 | Row-count reconciliation performed and explained | |
| 4 | Pipeline run twice — output identical, not doubled | |
| 5 | Leakage checks §7 each explicitly performed | |
| 6 | Metric compared against the baseline | |
| 7 | Training-serving feature parity verified on a sample | |
| 8 | Reproducibility record complete — the artefact can be regenerated | |
| 9 | All four code gates green |

## 13. Ripple effects

| Affected | Where | How | Handled by |
|----------|-------|-----|-----------|
| | | | |

Check specifically: downstream datasets, reports and dashboards reading this output · other models using this
as a feature · consumers depending on the schema or column order · retention and deletion processes covering
the new artefact · anything caching the output.

## 14. Done when

| # | Criterion |
|---|-----------|
| 1 | Inputs validated at read; provenance recorded |
| 2 | Quality gates defined with thresholds, and they **fail** the run on breach |
| 3 | Row counts reconciled; every drop explained |
| 4 | Rerunning is safe and produces identical output |
| 5 | Reproducibility record complete |
| 6 | Leakage checks performed and passed *(model work)* |
| 7 | Metric beats the baseline by the accepted threshold *(model work)* |
| 8 | Training-serving parity verified *(model work)* |
| 9 | Monitoring detects degradation without a complaint |
| 10 | Ripple effects handled; all four gates green; no follow-up open |
