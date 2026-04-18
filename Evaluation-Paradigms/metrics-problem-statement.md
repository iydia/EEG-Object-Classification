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
2. **Redesign evaluation metrics** to match the available data?

## Proposed New Evaluation Protocol: Temporal Split Approximation (TSA)

- **WT**: Keep the current within-time protocol and evaluation metric.

- **CT**: Replace stage-based cross-time evaluation with **within-trial temporal transfer**:
  - Train on an early EEG window and test on a later EEG window
  - Or train on segment A and test on segment B within the same trial

- **CP**: Replace the current workaround with **Leave-One-Subject-Out (LOSO)**:
  - Train on all subjects except one
  - Test on the held-out subject

This redesign preserves the original intent of evaluating temporal and subject generalization, while avoiding unreliable stage inference from file names.

## Cross-Participant Evaluation: From Original CP to LOSO

### Old CP (with workaround)

The original CrossPT-EEG cross-participant (CP) protocol is defined as:

- **Train:** Stage 1 data from other participants (A, B, C)
- **Test:** Stage 2 data from the target participant (D)

In our implementation, since the dataset does not explicitly expose a `stage` field, we adopt the following workaround:

- Infer stage from `subject`:
  - Stage 1: `subject < 8`
  - Stage 2: `subject >= 8`
- Map real identity using:
  - `real_id = subject % 8`

Thus, the implemented CP split becomes:

- **Train:** all samples where `subject < 8` and `real_id ≠ target_real_id`  
- **Test:** all samples where `subject >= 8` and `real_id = target_real_id`

---

### New CP (LOSO - Leave One Subject Out)

```
The Cross-Participant CP split the issue is it mixes two things the subject (ABC) and the session (Stage 1 vs Stage 2). So if performance drops, we don’t know if it’s because of a new person or just time differences. Also, since labels are built before splitting, some classes might only appear in test, which makes results unreliable.
```
```
Leave one subject out: Train on all other subjects

Train: A (stage1 + stage2), B (stage1 + stage2), C (stage1 + stage2)

Test:  D (stage1 + stage2)

on both and test on one — which isolates just the subject difference and makes the evaluation cleaner. 