# Ashwam ML/AI Internship — Exercise A  
Evidence-Grounded Extraction & Evaluation

## Overview
This project implements a deterministic evaluation pipeline for evidence-grounded extraction from free-form health journals. The goal is to evaluate non-canonical extractions without relying on fixed labels, using exact evidence spans from the source text as the anchor.

The system focuses on safety, restraint, and reproducibility, prioritizing evidence alignment over aggressive extraction.

---

## Problem Scope
Ashwam journals are written in natural, human language and often mix symptoms, emotions, food, and mental states. These concepts cannot be mapped to a predefined vocabulary without losing meaning or introducing hallucinations.

This solution evaluates extracted semantic objects based on:
- Evidence span overlap
- Domain agreement
- Attribute correctness (polarity, intensity/arousal, time)

Canonical labels are intentionally avoided.

---

## Input Data
The pipeline operates on the provided synthetic dataset:
- `journals.jsonl` — free-form journal entries
- `gold.jsonl` — evidence-grounded references with domains, polarity, buckets, and time
- `sample_predictions.jsonl` — optional sanity-check predictions

All evaluation is grounded in exact text spans from the journals.

---

## System Design

### 1. Extraction Schema
Each extracted semantic object includes:
- `journal_id`
- `domain` (symptom | emotion)
- `evidence_span` (exact substring from journal text)
- `polarity` (present | absent | uncertain)
- `intensity_bucket` or `arousal_bucket`
- `time_bucket`

Fields such as domain and buckets are constrained, while evidence text remains free-form. This enables objective evaluation while remaining extensible.

---

### 2. Extraction Strategy
This implementation focuses on the evaluation layer. Predictions are either loaded from provided samples or assumed to be produced by an upstream extraction system.

Key principles:
- Every extracted object must include a valid evidence span
- If evidence is unclear, the system abstains
- No inference beyond what is explicitly stated in the text

---

### 3. Evaluation Method
Predicted objects are matched to gold objects using:
- Domain equality
- Evidence span overlap

From this matching:
- True Positives, False Positives, and False Negatives are computed
- Polarity accuracy is calculated on matched objects
- Bucket accuracy is calculated for intensity/arousal and time
- Evidence coverage rate ensures no hallucinated spans

This approach remains fully deterministic and does not depend on canonical labels.

---

### 4. Output Metrics
The scorer produces:
- Object-level Precision, Recall, and F1
- Polarity accuracy
- Bucket accuracy
- Evidence coverage rate

Metrics are reported per journal and in aggregate.

---

## CLI Usage

Run the full pipeline using:

```bash
python -m ashwam_eval --data ./data --out ./out
