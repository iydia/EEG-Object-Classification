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

## Open Question

Is it more valuable to:
1. Preserve the original evaluation paradigm (requires correct stage labels), or  
2. Redesign evaluation metrics to match the available data?

## Proposed New Evaluation Protocol: Temporal Split Approximation (TSA)

- **WT**: Keep the current within-time protocol and evaluation metric.

- **CP**: Replace the current workaround with **Leave-One-Subject-Out (LOSO)**:
  - Train on all subjects except one
  - Test on the held-out subject

- **CT**: Replace stage-based cross-time evaluation with **within-trial temporal transfer**:
  - Train on an early EEG window and test on a later EEG window
  - Or train on segment A and test on segment B within the same trial

This redesign preserves the original intent of evaluating temporal and subject generalization, while avoiding unreliable stage inference from file names.