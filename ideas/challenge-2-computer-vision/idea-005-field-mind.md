# Idea 005: FieldMind -- Vision-Language Reasoning for Agricultural Scenes

## One-line pitch

An AI copilot that doesn't just detect objects but *understands and explains* agricultural scenes in natural language -- answering questions like "Is it safe to proceed?" and "What should I be concerned about?" by combining state-of-the-art detection with multimodal LLM reasoning.

## Problem Statement

Computer vision models output numbers: bounding boxes, confidence scores, class labels. But the humans supervising autonomous machines think in concepts: "Is the path clear?" "What kind of crop is this?" "Why did the machine stop?" There's a translation gap between what CV models produce and what operators, supervisors, and regulators need. Furthermore, standard detectors are limited to their training classes -- they can't identify novel objects or reason about unusual situations. A truly autonomous system needs to handle the unexpected, and that requires reasoning, not just classification.

## Solution

FieldMind layers **vision-language understanding on top of traditional detection** to create an agricultural scene reasoning engine:

### 1. Detection Foundation
- YOLOv11-M fine-tuned on CLAAS data for core object detection (persons, vehicles, equipment)
- Provides bounding boxes and structured detections as ground truth

### 2. Open-Vocabulary Detection
- **Grounding DINO** for detecting objects not in the YOLO training set
- Custom prompt engineering for agricultural objects: "person", "tractor", "trailer", "fence", "ditch", "power line", "animal", "debris"
- Catches novel objects that a closed-vocabulary YOLO would miss entirely

### 3. Vision-Language Reasoning Engine
- **Florence-2** (Microsoft) or **LLaVA** as the scene reasoning backbone
- Takes the full frame + detection overlays and generates:
  - **Scene narrative:** "A combine harvester approaching a field edge. Two people visible near a parked tractor on the left. Tall corn crop on both sides, approximately 2m high. Clear weather, good visibility."
  - **Safety assessment:** "CAUTION: Two persons detected at approximately 25m. One appears to be walking toward the machine's path. Recommend reducing speed."
  - **Anomaly flagging:** "UNUSUAL: Object detected at field center does not match known categories. Possibly discarded equipment or debris. Recommend visual inspection."

### 4. Interactive Query Interface
- Operators can ask natural-language questions about the scene:
  - "What's that object on the left?" -> "Appears to be a parked utility vehicle, partially obscured by crop rows."
  - "Is it safe to increase speed?" -> "Not recommended. Person detected 30m ahead moving perpendicular to your path. Wait for them to clear."
  - "What's the crop condition?" -> "Corn crop, mature stage, dense planting. Some lodging visible in the eastern section, which may cause uneven cutting."

### 5. Dashboard
- Live video feed with detection overlays
- Running natural-language scene log (like a flight recorder, but in plain English)
- Query box for interactive questions
- Safety status with natural-language justification

## Key Innovation

**Natural-language scene understanding** is a paradigm shift from traditional CV output. Instead of requiring operators to interpret bounding boxes and confidence scores, FieldMind explains what it sees in human terms. This bridges the gap between AI perception and human decision-making -- critical for regulatory acceptance of autonomous machines. The interactive query system is particularly novel: it turns a passive sensor into an active advisor.

The **Grounding DINO open-vocabulary detection** addresses a critical limitation of fine-tuned models: they can only find what they were trained on. In agriculture, unexpected objects appear regularly (abandoned equipment, wildlife, fallen trees, children's toys near field edges). Open-vocabulary detection provides a safety net for the unknown.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Core detection | YOLOv11-M (Ultralytics) |
| Open-vocabulary detection | Grounding DINO (GroundingDINO) |
| Vision-language model | Florence-2-large or LLaVA-1.6-7B |
| LLM backend | Claude API or GPT-4o API (for complex reasoning queries) |
| Prompt engineering | Custom agricultural prompt templates |
| Backend | FastAPI + WebSocket |
| Frontend | React + Next.js, streaming text display |
| Compute | AWS g5.2xlarge (need more VRAM for VLM) |

## Data Strategy

**Detection:** Standard approach -- fine-tune YOLO on CLAAS data or pseudo-label.

**Open-vocabulary detection:** No training. Configure Grounding DINO with agricultural-specific text prompts. Evaluate on CLAAS images qualitatively.

**Vision-language model:** No fine-tuning -- use Florence-2 or LLaVA in zero-shot/few-shot mode with carefully crafted prompts:

```
System: You are an agricultural machine vision analyst. Analyze the image from a camera mounted on an autonomous combine harvester. Focus on:
1. Safety: Identify all persons, their approximate distance, and movement direction
2. Obstacles: Any objects that could impede the machine's path
3. Environment: Crop type, density, weather conditions, visibility
4. Recommendations: Safety recommendations for the autonomous system

Be concise, factual, and prioritize safety-critical observations.
```

**Evaluation:** Create a manual evaluation set of 50 CLAAS images with human-written scene descriptions. Compare VLM outputs qualitatively and score on completeness, accuracy, and safety relevance.

## 23-Hour Plan

| Hour | Activity |
|------|----------|
| 0-1 | Dataset exploration. Set up AWS instance with sufficient VRAM. Install all dependencies. |
| 1-3 | Train YOLOv11-M on CLAAS data (standard detection pipeline). |
| 3-4 | Set up Grounding DINO. Design agricultural prompt bank. Test on CLAAS images. |
| 4-6 | Set up Florence-2/LLaVA. Design system prompts. Test scene narration on sample images. Iterate on prompt engineering. |
| 6-7 | Build the detection fusion pipeline: YOLO detections + Grounding DINO detections -> merged scene objects. |
| 7-8 | Build the reasoning pipeline: frame + detections -> VLM -> scene narrative + safety assessment. |
| 8-9 | Build the interactive query system: user question + frame + detections -> VLM -> answer. |
| 9-10 | Optimize inference speed. Consider: run VLM every N frames, cache responses, use smaller model variants. |
| 10-12 | Build FastAPI backend: video stream -> detection -> VLM analysis -> WebSocket to frontend. |
| 12-15 | Build React dashboard: video with overlays, scene narrative log (streaming text), query interface, safety status panel. |
| 15-17 | Integration testing. Process CLAAS videos. Tune prompts for quality and consistency. |
| 17-18 | **Feature freeze.** Create demo scenarios: safety alert, unknown object, operator query. |
| 18-19 | Practice pitch. Focus on the "AI that explains itself" narrative. |
| 19-20 | Buffer. |

## Demo Script (3 minutes)

**0:00-0:30 -- The Gap.** "Current AI vision systems speak in numbers -- bounding boxes, confidence scores, class labels. But operators, supervisors, and regulators speak in language. 'Is this field safe?' 'Why did the machine stop?' 'What's that object?' Today, there's no bridge between what AI sees and what humans need to know."

**0:30-1:00 -- FieldMind.** "FieldMind is an AI copilot that doesn't just detect -- it understands and explains. It narrates what it sees, assesses safety, flags anomalies, and answers your questions about the scene -- all in natural language."

**1:00-2:00 -- Live Demo.** Process a CLAAS image/video through FieldMind:
- Show detection overlays appearing (YOLO + Grounding DINO)
- Scene narrative streams in: "Approaching field with mature corn crop. Two persons detected near field edge at approximately 20m. One appears stationary, one walking eastward. Clear visibility, dry conditions."
- Safety assessment appears: "CAUTION: Moving person trajectory may intersect machine path within 15 seconds. Recommend speed reduction."
- **Interactive query:** Type "What's the object near the fence?" -> FieldMind responds: "Appears to be a wheelbarrow or garden cart, positioned outside the machine's path. No action required."
- **Second query:** "Can I speed up here?" -> "Not recommended while persons are within 30m. Current speed is appropriate for the detected conditions."

**2:00-2:30 -- Why This Matters.** "Regulators will never approve autonomous machines they can't interrogate. FieldMind provides an audit trail in plain English -- every decision explained, every detection justified. This isn't just better vision -- it's trustworthy autonomy."

**2:30-3:00 -- The Vision.** "FieldMind turns a camera into a conversation. The machine doesn't just see the field -- it understands it and can explain its understanding. That's the leap from automation to autonomy."

## Metrics & Evaluation

| Metric | Target | Why |
|--------|--------|-----|
| Person detection recall (YOLO) | >95% | Detection foundation must be solid |
| Open-vocab detection precision | >70% on agricultural objects | Grounding DINO must add value, not noise |
| Scene narrative accuracy | >80% (human evaluation) | Descriptions must be factually correct |
| Safety-relevant info included | >90% of frames | VLM must prioritize safety observations |
| Query response relevance | >85% (human evaluation) | Answers must be useful and correct |
| Hallucination rate | <10% of descriptions | VLM must not fabricate objects |
| End-to-end latency | <3s per frame (with VLM) | Acceptable for advisory system |
| Detection-only FPS | >20 | Core detection runs fast; VLM is advisory overlay |

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| VLM too slow for real-time | High | Medium | Run VLM every 5th frame or on-demand only. Core detection runs every frame at full speed. |
| VLM hallucinations (sees people who aren't there) | Medium | High | Cross-validate VLM outputs against YOLO detections. Only report persons confirmed by detector. VLM adds context, YOLO provides ground truth. |
| Florence-2/LLaVA quality insufficient | Medium | Medium | Fallback: use Claude API or GPT-4o API for reasoning. Higher quality, higher latency, but acceptable for advisory system. |
| Grounding DINO false positives | Medium | Low | High confidence threshold (>0.4). Filter by size and position. |
| Demo too slow (waiting for VLM response) | Medium | Medium | Pre-compute some VLM responses for demo. Show streaming text generation for real-time feel. |
| VRAM insufficient for YOLO + VLM simultaneously | Medium | Medium | Run sequentially (YOLO first, crop frame, VLM second). Or use g5.2xlarge (24GB VRAM). |

## Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 10 | Vision-language reasoning for agriculture is cutting-edge; interactive querying is unique |
| Feasibility | 6 | VLM latency and quality are significant risks; most ambitious idea technically |
| Demo Impact | 10 | Natural language narration + interactive Q&A is the most "wow" demo possible |
| Technical Depth | 9 | Detection + open-vocab + VLM + interactive NLP = impressive multi-system architecture |
| Safety Relevance | 8 | Person detection is included; explainability and auditability add safety value |
| **Total** | **43/50** | |
