# Idea 002: AgriSense 360 -- Full-Spectrum Scene Understanding Engine

## One-line pitch

A unified scene understanding system that goes far beyond person detection to deliver a structured, real-time "field report" -- crop state, obstacle inventory, weather conditions, terrain analysis, and safety threats -- all from a single camera feed.

## Problem Statement

CLAAS's secondary objective is scene understanding: extracting maximum information from field imagery. Current agricultural CV focuses narrowly on one task at a time (detect people, measure crop height, etc.), but an autonomous machine needs a holistic understanding of its environment -- like a human driver who simultaneously notices a person, judges crop maturity, spots a ditch, and checks if rain is coming. No single model does all of this. Teams that only build a person detector address half the challenge.

## Solution

AgriSense 360 builds a **multi-headed perception pipeline** that processes each frame through parallel analysis branches, then fuses results into a structured scene report:

1. **Object Detection Branch:** YOLOv11 fine-tuned on CLAAS data detects persons, vehicles, animals, structures, and equipment. Each detection gets a bounding box, confidence, and class label.

2. **Segmentation Branch:** SAM 2 (Segment Anything Model 2) produces pixel-level masks for key regions -- crop area, bare ground, sky, roads/paths, water, obstacles. This gives the machine understanding of *where it can drive* vs. *where it should avoid*.

3. **Depth Estimation Branch:** Depth Anything v2 generates a monocular depth map from each frame, enabling distance estimation to all detected objects without LIDAR. Critical for safety zones ("that person is 15m away and closing").

4. **Scene Classification Branch:** CLIP/SigLIP classifies the overall scene: field type (corn, wheat, open), weather conditions (sunny, overcast, foggy, dusty), time of day, and operational context (harvesting, transport, idle).

5. **Fusion Engine:** A lightweight Python aggregator combines all branch outputs into a structured JSON "scene report" per frame:
```json
{
  "timestamp": "...",
  "persons": [{"bbox": [...], "distance_m": 12.3, "threat_level": "low"}],
  "obstacles": [...],
  "crop": {"type": "corn", "density": "high", "height_estimate": "tall"},
  "terrain": {"drivable_area_pct": 72, "hazards": ["ditch_left"]},
  "weather": {"condition": "overcast", "visibility": "good"},
  "scene_summary": "Harvesting tall corn, clear path ahead, 1 person detected at safe distance"
}
```

6. **Dashboard:** A React/Next.js dashboard renders this as a real-time HUD: annotated video with overlaid detection boxes, segmentation masks, depth heatmap, and a sidebar with the structured scene report.

## Key Innovation

The **structured scene report** -- instead of just drawing boxes on images, AgriSense 360 produces machine-readable environmental intelligence. This is what an autonomous system actually needs: not pixels with annotations, but structured data that a decision engine can act on. The depth estimation from a single camera eliminates the dependency on LIDAR for distance measurement, making it deployable on any camera-equipped machine.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Object detection | YOLOv11-M (Ultralytics) |
| Segmentation | SAM 2 (Meta) with auto-prompt |
| Depth estimation | Depth Anything v2 (small) |
| Scene classification | SigLIP ViT-B/16 |
| Fusion engine | Python, dataclass-based aggregator |
| Backend | FastAPI + WebSocket streaming |
| Frontend | React + Next.js, HTML5 Canvas + Three.js for depth viz |
| Compute | AWS g5.xlarge (1x A10G GPU) |
| Visualization | Matplotlib (static), Canvas API (real-time) |

## Data Strategy

**For object detection:** Same strategy as all CV ideas -- fine-tune on CLAAS labels if available, pseudo-label with Grounding DINO if not. Augment heavily for agricultural conditions.

**For segmentation:** SAM 2 works zero-shot. Provide automatic prompts (grid of points across image) and filter masks by area/quality. No training needed -- inference only.

**For depth estimation:** Depth Anything v2 is zero-shot. No training. Apply directly to CLAAS images. Validate qualitative accuracy by checking if closer objects get lower depth values.

**For scene classification:** SigLIP/CLIP works zero-shot. Define a prompt bank:
- Crop types: "a photo of a corn field", "a photo of a wheat field", "a photo of bare soil"
- Weather: "a photo taken in foggy conditions", "a sunny photo", "a dusty photo"
- No training needed -- prompt engineering only.

**Dataset split:** 70/15/15 for the detection branch. Other branches use the full dataset for qualitative evaluation.

## 23-Hour Plan

| Hour | Activity |
|------|----------|
| 0-1 | Dataset exploration. Understand image types, conditions, variation. Set up AWS GPU. |
| 1-2 | Set up all model dependencies: Ultralytics, SAM 2, Depth Anything v2, SigLIP. Verify each runs on a single image. |
| 2-4 | **Detection branch:** Prepare labels (existing or Grounding DINO pseudo), format for YOLO, start training YOLOv11-M with augmentations. |
| 4-5 | **While YOLO trains:** Build SAM 2 segmentation pipeline. Auto-prompt strategy, mask filtering, semantic labeling of segments. |
| 5-6 | **While YOLO trains:** Build Depth Anything v2 pipeline. Test on CLAAS images. Calibrate depth-to-distance mapping. |
| 6-7 | Build SigLIP scene classification pipeline. Create prompt bank. Test on sample images. |
| 7-8 | Evaluate YOLO training. Analyze results. Potentially start a second training run with refined augmentations. |
| 8-10 | Build the **Fusion Engine**: combine all branch outputs into structured scene report JSON. |
| 10-12 | Build FastAPI backend: endpoint that accepts image/video, runs full pipeline, returns scene report + annotated frames via WebSocket. |
| 12-15 | Build React dashboard: video player with canvas overlay (boxes, masks, depth), sidebar with live scene report, controls to toggle visualization layers. |
| 15-17 | Integration testing. Process full CLAAS test videos end-to-end. Fix latency issues. |
| 17-18 | **Feature freeze.** Polish visualizations. Create demo scenarios. |
| 18-19 | Build comparison views: raw frame vs. AgriSense annotated frame. Generate before/after showcase images. |
| 19-20 | Practice pitch. Prepare backup static demo. Buffer for bugs. |

## Demo Script (3 minutes)

**0:00-0:20 -- The Problem.** "An autonomous harvester doesn't just need to spot people -- it needs to understand everything: What crop is it in? How far away is that tractor? Is there a ditch to the left? Is visibility dropping? Today, that takes a human driver's eyes and experience."

**0:20-0:50 -- The Solution.** "AgriSense 360 gives the machine a complete understanding of its environment. Four AI models run in parallel, each understanding a different aspect of the scene, and we fuse their outputs into a single, structured field report."

**0:50-2:00 -- Live Demo.** Walk through a CLAAS test video:
- Show raw frame -> click "Analyze" -> watch annotations appear in layers:
  - Layer 1: Person detection boxes with distance estimates
  - Layer 2: Semantic segmentation (crop = green, ground = brown, sky = blue)
  - Layer 3: Depth heatmap overlay
  - Layer 4: Scene report sidebar updating in real-time
- Toggle layers on/off to show each model's contribution
- Show the structured JSON output: "This is what the autonomous system receives -- not pixels, but intelligence."

**2:00-2:30 -- The Power.** "With this structured report, a decision engine can reason: 'Person detected 10m ahead, crop density high with potential occlusion, ditch on the left limits evasive maneuvers -- STOP.' That's scene understanding, not just detection."

**2:30-3:00 -- Scalability.** "Every branch is modular. Swap YOLOv11 for a better detector? Plug it in. Add thermal cameras? Add a branch. This is a perception platform, not a single model."

## Metrics & Evaluation

| Metric | Target | Why |
|--------|--------|-----|
| Person detection recall | >95% | Safety-critical (primary objective) |
| Person detection mAP@0.5 | >80% | Overall detection quality |
| Depth estimation accuracy | <20% relative error at <30m | Distance estimates must be useful |
| Scene classification accuracy | >85% on weather/crop type | Qualitative eval on manual labels |
| Segmentation quality | Visual coherence (qualitative) | SAM 2 doesn't need metrics for demo |
| Pipeline FPS | >5 FPS (all branches) | Real-time-ish for demo |
| Scene report generation | <500ms per frame | Practical for autonomous systems |

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Pipeline too slow (4 models) | High | Medium | Run branches on different GPU streams. Reduce SAM 2 to key-frame-only. Use smallest model variants. |
| Depth estimation inaccurate without calibration | Medium | Low | Qualitative only -- show heatmap, not exact meters. Or calibrate with known distances in CLAAS data. |
| Scene classification too generic | Low | Low | Customize prompt bank heavily for agricultural domain. Test and iterate prompts. |
| Integration complexity (4 branches + fusion) | Medium | Medium | Build and test each branch independently first. Fusion is just JSON merging. |
| YOLO detection underperforms | Medium | High | This is shared risk across all ideas. Pseudo-labeling + augmentation pipeline is the mitigation. |
| Demo is too complex to explain in 3 min | Medium | Medium | Layer-toggle UI makes it easy: "Watch as we turn on each AI capability one by one." |

## Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 9 | Structured scene reports are novel; multi-modal fusion from single camera is impressive |
| Feasibility | 7 | Four parallel models is ambitious for 23h; each individually is straightforward but integration is complex |
| Demo Impact | 9 | Layer-toggle visualization is extremely compelling and easy to understand |
| Technical Depth | 10 | Four different CV paradigms (detection, segmentation, depth, VLM) in one system |
| Safety Relevance | 8 | Person detection is included but scene understanding is the real value-add |
| **Total** | **43/50** | |
