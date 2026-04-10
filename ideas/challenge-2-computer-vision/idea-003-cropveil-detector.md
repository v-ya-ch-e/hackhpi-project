# Idea 003: CropVeil -- Occlusion-Hardened Person Detection via Synthetic Data

## One-line pitch

A person detector purpose-built for agriculture's hardest problem -- finding people hidden behind crops -- using a novel synthetic occlusion augmentation engine and part-based detection that finds people from just a head, shoulder, or arm visible above the vegetation.

## Problem Statement

The single biggest failure mode for person detection in agriculture is **crop occlusion**: a person standing in a corn field may only have their head and shoulders visible, or a child crouching between rows might show nothing above the crop line. Standard detectors trained on COCO learn to recognize full human silhouettes and fail catastrophically when 60-80% of the body is hidden. This is the exact scenario where lives are at risk -- a person in the field who the machine cannot see. Solving occlusion isn't a nice-to-have; it's the difference between a useful prototype and a life-saving system.

## Solution

CropVeil takes a **data-centric + architecture-aware** approach to occlusion:

### 1. Synthetic Occlusion Engine (SOE)
A custom augmentation pipeline that generates training data with realistic agricultural occlusion:
- **Crop overlay generation:** Use a bank of crop-edge textures (extracted from CLAAS images via GrabCut/SAM) to overlay on person bounding boxes at various heights (30%, 50%, 70%, 90% occlusion levels)
- **Person paste augmentation:** Cut persons from COCO/CrowdHuman datasets and paste them into CLAAS field backgrounds at varying occlusion levels
- **Partial visibility variants:** Generate training examples where only the head, head+shoulders, or upper torso is visible
- **Progressive difficulty:** Train in curriculum-learning fashion: full-body -> 30% occluded -> 50% -> 70%

### 2. Part-Based Detection Head
Extend YOLOv11 with a **multi-label detection head** that detects:
- Full person (standard bounding box)
- Person-head (small bounding box around head/face)
- Person-upper-body (head + torso)

During inference, if the full-person detector misses but the head detector fires, the system infers a person is present but occluded. This cascading logic catches the cases that matter most.

### 3. Attention-Guided Feature Enhancement
Add a lightweight **CBAM (Convolutional Block Attention Module)** after the YOLOv11 backbone to force the model to attend to the visible parts of occluded persons rather than relying on full-body shape priors.

### 4. Confidence Recalibration
Train a small post-hoc calibration network that takes the detector's raw confidence + the detection's aspect ratio (wide bbox = likely full body, tall-narrow = likely head-only) and outputs a calibrated "person probability" that accounts for partial visibility.

## Key Innovation

The **Synthetic Occlusion Engine** is the core innovation: instead of hoping the model learns occlusion from limited real data, CropVeil manufactures the exact failure mode at scale. Combined with part-based detection, the system fundamentally changes the detection paradigm from "find a person-shaped blob" to "find any evidence of a person." This directly addresses the most dangerous scenario in agricultural autonomy.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Base detector | YOLOv11-L (Ultralytics), modified head |
| Part detection | Custom multi-label YOLO head (head, upper-body, full-person) |
| Attention module | CBAM (lightweight, <1ms overhead) |
| Synthetic occlusion | Custom Python pipeline (OpenCV + Albumentations + SAM masks) |
| Person source data | COCO person crops + CrowdHuman crops |
| Calibration | Logistic regression on validation set |
| Training | PyTorch, Ultralytics framework |
| Backend | FastAPI |
| Frontend | React + Next.js, canvas-based visualization |
| Compute | AWS g5.xlarge |

## Data Strategy

**Core insight:** 500MB is small for training a robust occluded-person detector. CropVeil's strategy is to **maximize effective dataset size through intelligent synthesis.**

**Step 1: Extract backgrounds.** From the CLAAS dataset, extract ~200 "empty field" crops (no persons) using Grounding DINO filtering. These become paste targets.

**Step 2: Extract crop textures.** Use SAM 2 to segment crop regions from CLAAS images. Extract 50+ crop-edge texture strips (the transition zone between crop tops and sky/background).

**Step 3: Source persons.** Download COCO person crops (~10K) and CrowdHuman crops (~5K) as paste sources. Filter for standing/walking poses.

**Step 4: Synthetic generation.** For each paste:
- Place person in field background at random position
- Apply crop overlay at random occlusion level (0%, 30%, 50%, 70%, 90%)
- Generate 3 labels: full-person bbox, head bbox (if visible), upper-body bbox (if visible)
- Apply standard augmentations (lighting, weather, geometric)

**Step 5: Mixed training.** Combine real CLAAS data (if labeled) with synthetic data. Use domain randomization to prevent overfitting to synthetic artifacts.

**Target:** Generate 10K-20K synthetic training images in ~30 minutes of compute.

## 23-Hour Plan

| Hour | Activity |
|------|----------|
| 0-1 | Dataset exploration. Assess CLAAS image types, existing labels. Download COCO/CrowdHuman person crops. |
| 1-3 | Build Synthetic Occlusion Engine: background extraction, crop texture extraction (SAM), person paste pipeline with occlusion levels. |
| 3-4 | Generate synthetic dataset (10K+ images with multi-label annotations). Visual QA on sample images. |
| 4-5 | Modify YOLOv11 config for multi-label detection (person, head, upper-body). Prepare training configs. |
| 5-8 | Train YOLOv11-L on mixed real + synthetic data. Curriculum: start with easy, add hard occlusion. (~3 hours) |
| 8-9 | Evaluate on real CLAAS test images. Analyze occlusion-specific recall. Compare to baseline (no SOE). |
| 9-10 | Add CBAM attention module. Retrain or fine-tune for 20 epochs. |
| 10-11 | Build calibration network on validation set outputs. |
| 11-12 | Build inference pipeline: multi-head detection -> part-based fusion -> calibrated output. |
| 12-14 | Build FastAPI backend + React frontend. Focus on visualization of occlusion-handling: show which "part" triggered the detection. |
| 14-16 | Create killer demo content: side-by-side of baseline vs. CropVeil on occluded person images. |
| 16-17 | **Feature freeze.** Polish. |
| 17-18 | Generate metrics tables, failure analysis, occlusion-level breakdown. |
| 18-19 | Practice pitch. Focus on the "hidden person revealed" narrative. |
| 19-20 | Buffer. |

## Demo Script (3 minutes)

**0:00-0:30 -- The Killer Stat.** "In agriculture, the most dangerous person is the one you can't see. When someone stands in a corn field, 70% of their body can be hidden by crops. Standard AI detectors miss these people 40% of the time. CropVeil was built for exactly this scenario."

**0:30-1:00 -- How We Solved It.** "We built a Synthetic Occlusion Engine that teaches the AI what partially hidden people look like -- at 30%, 50%, 70%, even 90% occlusion. We also taught it to find people from just a visible head or shoulder. If any part of you is visible, CropVeil finds you."

**1:00-2:00 -- The Demo.** Show a gallery of CLAAS images with increasing occlusion difficulty:
- **Easy:** Person standing in open field -> both baseline and CropVeil detect
- **Medium:** Person in waist-high crop -> baseline confidence drops, CropVeil solid
- **Hard:** Only head visible above tall crop -> baseline MISSES, CropVeil detects via head detector (highlight the head bbox in different color)
- **Extreme:** Show synthetic examples at 90% occlusion to demonstrate the model's limits
- For each, show which "part detector" fired: full-body, upper-body, or head-only

**2:00-2:30 -- The Metrics.** "At 70% occlusion, baseline recall drops to [X]%. CropVeil maintains [Y]% recall. On the full test set, CropVeil reduces false negatives by [Z]%. When the difference between detection and failure is a life, that [Z]% matters."

**2:30-3:00 -- Why This Matters.** "The data-centric approach is the key. We didn't just build a better model -- we built a better way to generate training data for agriculture's hardest problem. This pipeline can be applied to any occlusion scenario: animals behind fences, vehicles behind buildings, equipment partially hidden by terrain."

## Metrics & Evaluation

| Metric | Target | Why |
|--------|--------|-----|
| Recall (0% occlusion) | >97% | Baseline: easy cases must be perfect |
| Recall (30% occlusion) | >95% | Moderate occlusion |
| Recall (50% occlusion) | >90% | Challenging -- key differentiation |
| Recall (70% occlusion) | >80% | Very challenging -- this is the "wow" metric |
| Recall improvement over baseline at 70% occ. | >20% absolute | Must demonstrate clear value of SOE |
| mAP@0.5 (all occlusion levels) | >80% | Overall quality |
| Head detection recall | >90% | Part-based detection must work |
| FPS | >25 | YOLO-based, should be fast |

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Synthetic data doesn't transfer to real images | Medium | High | Domain randomization, mixed training with real data, aggressive augmentation on synthetic data to prevent shortcuts. |
| Multi-label head training instability | Medium | Medium | Fallback: use separate YOLO models for full-person and head, ensemble at inference. |
| CBAM doesn't help or hurts speed | Low | Low | Optional component. Skip if training time is tight. |
| No real occluded persons in CLAAS test set | Medium | Medium | Create synthetic test set with known occlusion levels. Demo the pipeline on synthetic + real data. |
| 10K synthetic images not enough | Low | Medium | Can generate more in minutes. The pipeline is fast. |

## Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 9 | Synthetic Occlusion Engine and part-based detection are novel and directly address the core challenge |
| Feasibility | 8 | Synthetic data pipeline is the main risk; all other components are standard |
| Demo Impact | 10 | "Hidden person found" is the most emotionally compelling demo possible for this challenge |
| Technical Depth | 8 | Data-centric ML, custom augmentation, architecture modification, calibration |
| Safety Relevance | 10 | Directly targets the most dangerous failure mode in agricultural person detection |
| **Total** | **45/50** | |
