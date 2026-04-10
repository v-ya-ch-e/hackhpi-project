# Idea 001: FieldGuardian -- Zero-Miss Cascaded Person Detector

## One-line pitch

A cascaded multi-model person detection pipeline that treats every missed person as a system failure and achieves near-zero false negatives through redundant detection, agricultural-domain augmentation, and confidence-calibrated alerting.

## Problem Statement

Autonomous agricultural machines operating in unstructured field environments must detect every person in and around the field -- missing even one can result in a fatality. Standard object detectors trained on urban datasets (COCO, ImageNet) drop accuracy dramatically in agricultural conditions: tall crops occlude lower bodies, dust and backlighting wash out features, and people appear in unexpected poses (crouching, lying down). CLAAS needs a detection system that prioritizes recall over precision -- a false positive slows the machine, but a false negative kills someone.

## Solution

FieldGuardian is a **cascaded, redundant person detection pipeline** that layers multiple detection strategies to maximize recall:

1. **Primary detector:** Fine-tuned YOLOv11-L on CLAAS dataset with agricultural-specific augmentations (crop occlusion simulation, dust/fog overlays, extreme lighting). This runs at high FPS and catches the majority of persons.

2. **Secondary detector:** RT-DETR (transformer-based) runs in parallel on the same frames. Transformers excel at detecting partially occluded objects because attention mechanisms can reason about global context. Detections from both models are merged via Weighted Boxes Fusion (WBF).

3. **Verification stage:** Any region where either detector fires with >0.2 confidence gets cropped and run through a CLIP-based binary classifier ("person" vs. "not person"). This catches edge cases that fall between detector confidence thresholds.

4. **Temporal smoothing:** A lightweight Kalman filter tracker (ByteTrack) maintains person tracks across frames. If a person was detected in frame N but not N+1, the system maintains the alert for 5 frames before dropping the track.

5. **Safety output:** The system produces bounding boxes with a 3-tier alert: GREEN (detected, safe distance), YELLOW (detected, approaching), RED (detected, danger zone, halt machine).

The FastAPI backend streams processed frames to a React dashboard showing live inference, detection statistics, and a safety log.

## Key Innovation

The **redundancy-first architecture** -- instead of optimizing a single model, FieldGuardian combines complementary model architectures (CNN + Transformer + VLM) to cover each other's blind spots. The CLIP verification stage is novel: it uses vision-language understanding to resolve ambiguous detections that traditional detectors miss. The 3-tier safety zone system translates raw detections into actionable safety decisions for the machine.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Primary detector | YOLOv11-L (Ultralytics) |
| Secondary detector | RT-DETR-L (PaddleDetection or Ultralytics) |
| Verification | CLIP ViT-B/32 (OpenAI) |
| Tracker | ByteTrack |
| Box fusion | Weighted Boxes Fusion (ensemble-boxes) |
| Training | PyTorch, Ultralytics CLI |
| Augmentation | Albumentations + custom agricultural pipeline |
| Backend | FastAPI + WebSocket streaming |
| Frontend | React + Next.js with canvas overlay rendering |
| Compute | AWS GPU instance (g5.xlarge or p3.2xlarge) |
| Dataset platform | Kaggle (CAGGLE) |

## Data Strategy

**Dataset split:** 70% train / 15% validation / 15% test (stratified by scene type if metadata available).

**If labels exist:** Fine-tune YOLOv11 directly on CLAAS bounding box annotations. Use RT-DETR with the same labels for the secondary detector.

**If labels are missing or sparse:**
1. Run Grounding DINO with prompt "person" on all images to generate pseudo-labels.
2. Manually verify a random 10% sample to estimate pseudo-label quality.
3. Train YOLOv11 on pseudo-labels, then do active learning: identify low-confidence predictions, manually label those, retrain.

**Augmentation pipeline:**
- Random crop-occlusion: overlay random vegetation strips across the lower 40-70% of person bounding boxes
- Weather simulation: fog, rain, dust overlays (Albumentations)
- Lighting: random brightness, contrast, sun flare injection
- Geometric: random scale (simulating distance variation), horizontal flip
- Copy-paste augmentation: paste person cutouts into empty field backgrounds

**Validation focus:** Track false negative rate separately from mAP. Any false negative in the test set triggers investigation.

## 23-Hour Plan

| Hour | Activity |
|------|----------|
| 0-1 | Download & explore CLAAS dataset on CAGGLE. Assess labels, image quality, distribution. Set up AWS GPU instance. |
| 1-2 | Set up training infrastructure. Install Ultralytics, PaddleDetection, prepare data loaders. Write augmentation pipeline. |
| 2-3 | If labels missing: run Grounding DINO pseudo-labeling. If labels exist: format for YOLO and RT-DETR. |
| 3-5 | Train YOLOv11-L baseline (~50 epochs, ~2 hours on g5.xlarge). While training, start RT-DETR training in parallel. |
| 5-6 | Evaluate baseline models. Analyze false negatives. Identify failure modes. |
| 6-7 | Implement Weighted Boxes Fusion (WBF) ensemble. Test cascade with both detectors. |
| 7-8 | Add CLIP verification stage. Implement crop-and-classify pipeline for low-confidence detections. |
| 8-9 | Implement ByteTrack temporal smoothing. Test on video sequences. |
| 9-10 | Retrain with aggressive agricultural augmentations based on failure analysis. |
| 10-12 | Build FastAPI backend: video ingestion, cascade inference pipeline, WebSocket streaming. |
| 12-14 | Build React frontend: live video canvas with bounding box overlay, safety zone visualization, metrics dashboard. |
| 14-15 | Integration testing. End-to-end pipeline: video in -> detections out -> dashboard display. |
| 15-17 | **Fine-tuning freeze.** Focus on demo polish. Create comparison visualizations (baseline YOLO vs. FieldGuardian cascade). |
| 17-18 | Prepare demo video/images. Generate metrics tables. Build the "missed person" recovery demo. |
| 18-19 | Practice pitch. Refine story. |
| 19-20 | Buffer: fix bugs, polish UI, prepare backup demo (static images if live inference is unstable). |

## Demo Script (3 minutes)

**0:00-0:30 -- The Problem.** "Every year, people are injured or killed by agricultural machinery. When a combine harvester is autonomous, it needs to see what the driver would see -- but cameras and AI aren't perfect. In agriculture, people hide behind crops, dust obscures vision, and lighting changes every hour. Missing a person is not an error metric -- it's a tragedy."

**0:30-1:00 -- The Insight.** "One model isn't enough. Just like a self-driving car uses LIDAR + cameras + radar, we use multiple AI models to create redundant safety layers. If one model misses a person, another catches them."

**1:00-2:00 -- Live Demo.** Show live inference on CLAAS test video:
- Split screen: LEFT = YOLOv11 alone (shows 2 missed detections), RIGHT = FieldGuardian cascade (catches all persons)
- Highlight the CLIP verification recovering a person partially hidden in crops
- Show the safety zone overlay (green/yellow/red)
- Display real-time metrics: recall, false negative count (0!), FPS

**2:00-2:30 -- The Numbers.** "FieldGuardian achieves [X]% recall vs. [Y]% for a single YOLO model. On [N] test images with persons, we missed ZERO. Processing at [Z] FPS -- fast enough for real-time operation."

**2:30-3:00 -- The Vision.** "This cascade architecture scales. Add thermal cameras? That's another layer. Add LIDAR depth? Another layer. FieldGuardian isn't a model -- it's a safety philosophy: redundancy saves lives."

## Metrics & Evaluation

| Metric | Target | Why |
|--------|--------|-----|
| Recall (person class) | >98% | Safety-critical: false negatives are unacceptable |
| mAP@0.5 | >85% | Overall detection quality |
| mAP@0.5:0.95 | >65% | Strict localization accuracy |
| False negative rate | <2% | Direct safety metric |
| Precision | >80% | Avoid excessive false positives that stop the machine |
| FPS (cascade) | >10 FPS | Minimum for real-time on video |
| FPS (YOLO only) | >30 FPS | Fast mode for open field (no cascade needed) |
| Improvement over baseline | >5% recall | Cascade must justify complexity |

**Safety-specific evaluation:** Create a "hard negative" test set of the most challenging images (heavy occlusion, backlighting, distant persons) and report recall separately on this subset.

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| CLAAS data has no/poor labels | Medium | High | Pre-built Grounding DINO pseudo-labeling pipeline. Budget 1 hour for label generation. |
| Training takes longer than expected | Medium | Medium | Start with YOLOv11-M (faster), upgrade to L if time permits. Pre-trained checkpoints exist. |
| Cascade is too slow for real-time | Low | Medium | Make cascade configurable: use YOLO-only in open field, full cascade near field edges. |
| CLIP verification adds too much latency | Low | Low | Only run CLIP on low-confidence crops (<5% of frame). Batch inference. |
| AWS instance unavailable | Low | High | Fallback: Kaggle free GPU (T4), or Google Colab Pro. |
| Dataset too small for fine-tuning | Medium | Medium | Heavy augmentation + transfer learning from COCO. Consider using open agricultural datasets (e.g., FieldSAFE) to supplement. |

## Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 8 | Multi-model cascade with CLIP verification is novel for agricultural CV |
| Feasibility | 9 | All components are well-tested, libraries exist, 23h plan is realistic |
| Demo Impact | 9 | Live split-screen "missed vs. caught" is visceral and compelling |
| Technical Depth | 9 | Multiple model architectures, fusion strategy, temporal tracking, safety zones |
| Safety Relevance | 10 | Directly addresses the #1 requirement: zero missed persons |
| **Total** | **45/50** | |
