# EyeCLIP-ROP Project Plan (FROZEN — v1.0)

> This is the locked master plan. Do not edit ad hoc — if a step must change, log it under "Change Log" at the bottom with date + reason.

## Scope
Multimodal, few-shot, open-set ROP classification using EyeCLIP on ROP-VL. Order follows proposal: image → metadata → few-shot → open-set → text → LoRA.

---

## Phase 0 — Foundation (Week 1)

1. Env setup: Python, PyTorch, EyeCLIP repo, GPU access, Git repo.
2. **Project structure** (data/, notebooks/, src/, experiments/, checkpoints/, results/).
3. Download ROP-VL; place in `data/raw/`.
4. **Dataset exploration notebook** — counts: images, patients, per-class distribution, metadata fields, missing values, text field availability.
5. **EyeCLIP checkpoint investigation** — exact checkpoint source, architecture, input resolution, preprocessing, embedding dim, license, image-only vs image+text encoder availability.
6. Test EyeCLIP on one image → confirm embedding shape/dtype/range.
7. **Patient-level train/val/test split** (e.g., 70/15/15) — no patient's images span splits. *(Added from ChatGPT plan — necessary, prevents data leakage.)*

**Deliverable:** working data pipeline + confirmed EyeCLIP embeddings + leak-free splits. No modeling yet.

---

## Phase 1 — Image-Only Baseline (Week 2)

8. Extract frozen EyeCLIP image embeddings for full dataset.
9. **Simple linear classifier on frozen embeddings** (image-only, 8-class). *(Added — sanity-checks that embeddings carry signal before few-shot complexity.)*
10. Evaluate: accuracy, macro-F1, precision, recall, confusion matrix, per-class F1 (imbalance expected).

**Deliverable:** baseline number to compare everything else against.

---

## Phase 2 — Few-Shot Prototype Classification (Weeks 3–4)

11. Implement prototype construction (average embeddings per class) for 1/3/5-shot.
12. Implement nearest-prototype query classification.
13. **Run each shot-setting with 5 different random seeds/support sets; report mean ± std.** *(Added — avoids reporting a lucky/unlucky draw.)*
14. Compare 1-shot vs 3-shot vs 5-shot vs Phase-1 baseline.

---

## Phase 3 — Clinical Metadata + Fusion (Weeks 5–6)

15. Build metadata MLP (define real fields from Step 4 exploration — no assumptions).
16. Fusion (concat + linear/ReLU/dropout/linear) → patient representation.
17. Re-run few-shot experiments (1/3/5-shot) with image+metadata.
18. **Results table: image-only vs image+metadata across shots** (per proposal's "does clinical info help" contribution).

<!-- Method:

Go back to img_info.xlsx / disease_description.xlsx — list actual available metadata fields (gestational age, birth weight, postmenstrual age, eye laterality, exam date, etc. — whatever really exists, no assumptions).
Build a metadata preprocessing pipeline: encode categorical fields, normalize numeric fields, handle missing values explicitly (don't silently drop rows).
Build a small metadata MLP (per plan: a few linear layers) that takes the metadata vector → outputs an embedding of matching/compatible size.
Fusion: concatenate image embedding + metadata embedding → linear → ReLU → dropout → linear → patient representation.
Re-run the same evaluation as Phase 1 (linear classifier on fused representation) — this isolates whether fusion helps, using the identical train/val/test splits and same seed.
Also re-run Phase 2's few-shot prototypes using fused representations (1/3/5-shot, 5 seeds) — since that's explicitly in the plan (step 17). -->
---

## Phase 4 — Open-Set Recognition (Week 7)

19. Implement distance-to-nearest-prototype rejection rule (simplest signal first).
20. Determine threshold using validation set (not a fixed guess like 0.5).
21. Evaluate: Known-F1, Unknown-F1, AUROC, across shot settings and image-only vs multimodal.

---

## Phase 5 — Clinical Text Branch (Weeks 8–9)

22. EyeCLIP text encoder on available clinical text.
23. Extend fusion to image+metadata+text.
24. Re-run few-shot + open-set experiments with full trimodal setup.

---

## Phase 6 — LoRA Adaptation (Week 10)

25. Implement LoRA on EyeCLIP.
26. Compare frozen vs LoRA under identical 1/3/5-shot conditions.

---

## Phase 7 — Ablations & Final Analysis (Weeks 11–12)

27. Consolidate all results tables (baseline, few-shot, multimodal, open-set, LoRA).
28. Ablation summary: what contributed most (metadata? text? LoRA?).
29. Write final report / paper-ready results section.
30. Faculty-presentable Word doc version of proposal + results.

---

## Necessity Check (why nothing was dropped or padded)
- Every step maps to a stated contribution in the proposal (few-shot, open-set, multimodal fusion, frozen-vs-LoRA) — no speculative extra work added.
- Patient-level split, image-only baseline, and seeded few-shot runs were added because their absence would invalidate results (data leakage, no sanity check, unreliable few-shot numbers).
- Text branch and LoRA are deliberately last, per your proposal's own stated sequencing.

---

## Change Log
*(empty — record any deviation here with date + reason)*
