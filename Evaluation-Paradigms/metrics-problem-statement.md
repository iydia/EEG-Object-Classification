# Problem Statement: Evaluation Paradigm Misalignment in EEG Dataset

## Background

The CrossPT-EEG framework defines three key evaluation paradigms:

- **WT (Within-Time):** Train and test on the same subject and session
- **CT (Cross-Time):** Train subject A on Stage 1, test subject A on Stage 2. 
- **CP (Cross-Participant):** Train subjects A, B, C, test on D. 

These paradigms rely on explicit **stage/session labels** to separate:
- temporal variability (CT)
- subject variability (CP)

## Issue

The current dataset does **not explicitly provide a stage identifier**.

As a result:
- CT and CP cannot be correctly constructed
- Current workaround uses **file naming conventions** to infer stage

## Problems with Current Workaround

- file names are not guaranteed to encode temporal structure correctly
- **Non-reproducible**: assumptions may not generalize
- **Misaligned with proposal**: violates intended evaluation design (temporal vs subject separation)

## Consequences

- CT may not reflect true temporal generalization
- CP may mix subject and temporal effects incorrectly
- Results become unreliable and difficult to interpret

## Research Question

How can we redesign or adapt evaluation paradigms given missing stage information while preserving meaningful generalization testing?

## Possible Directions

### Option 1: Recover Stage Information
- Investigate metadata / raw files for hidden session indicators
- Use timestamps, trial ordering, or recording batches

### Option 2: Approximate Temporal Splits
- Artificially split time series into early vs late segments
- Use change point detection to define "pseudo-stages"

### Option 3: Redefine Evaluation Metrics
- Replace CT with:
  - temporal robustness metrics
  - sliding-window generalization
- Replace CP with:
  - leave-one-subject-out (LOSO)

### Option 4: Switch Dataset
- Use datasets with explicit session/stage annotations
- Ensure alignment with CrossPT-style evaluation

## Key Open Question

Is it more valuable to:
1. Preserve the original evaluation paradigm (requires correct stage labels), or  
2. Redesign evaluation metrics to match the available data?