# Challenge 2: Autonomy in the Fields -- Idea Generation Prompt

## Configuration

> Edit these parameters before running this prompt in Cursor.

```yaml
team_size: 3
team_skills:
  - "Python / PyTorch"
  - "computer vision experience"
  - "web development for demo UI"
  # Add/remove skills as needed

preferred_stack:
  - "Python / PyTorch or TensorFlow"
  - "OpenCV"
  - "AWS (unlimited credits -- GPU instances available)"
  # Add/remove technologies as needed

time_budget: "20 hours of coding (23h total minus breaks/planning)"

ambition_level: "high"  # low | medium | high | moonshot

focus_preference: "person_detection_plus"
# Options:
#   "person_detection" -- focus purely on best bounding-box detector
#   "scene_understanding" -- go beyond detection into full scene comprehension
#   "person_detection_plus" -- nail detection, then add layers of intelligence
#   "open" -- let the model explore freely

ml_experience: "intermediate"  # beginner | intermediate | advanced | expert

has_gpu: true  # Do you have access to GPU compute? (AWS credits available)

avoid_duplicates: true
# When true: inspect all existing ideas in @ideas/challenge-2-computer-vision/
# before generating. Do NOT produce ideas that overlap in core concept with
# any existing idea file. Add a "Differentiation" section to each new idea.
# When false: generate freely without checking existing ideas.

constraints:
  - "Must be demo-able in 3-minute pitch"
  - "Must use CLAAS-provided image/video data"
  - "Model must produce visual output (bounding boxes, annotations)"
  # Add custom constraints

strengths_to_leverage:
  - "We have experience training YOLO models"
  # What's your team uniquely good at?
```

---

## Reference Files

Read these project files for full context:

- @CLAUDE.md -- hackathon overview, all challenges, partners, timeline, idea framework
- @sources/hackhpi-notion-export.md -- complete Notion export with challenge details, meeting notes, sensor descriptions
- @sources/hackhpi-website-info.md -- event schedule, FAQ, logistics

If `avoid_duplicates` is `true`, also read:
- @ideas/challenge-2-computer-vision/ -- all existing idea files to avoid duplication

---

## Task

You are an expert in computer vision, agricultural technology, and hackathon strategy. Generate winning hackathon project ideas for the **Autonomy in the Fields** challenge at HackHPI 2026.

### Context: The Challenge Sponsor

**CLAAS** (claas.com) is one of the world's leading manufacturers of agricultural machinery -- combine harvesters, forage harvesters, tractors. Their machines currently have ~100 sensors for operations (header height, harvest quality, throughput monitoring). They have autonomous features like trailer-following unloading. Their goal is full autonomy -- replacing the driver's eyes and decision-making.

For full details on CLAAS sensors, autonomous features, and meeting transcript summaries, see @sources/hackhpi-notion-export.md.

### Context: The Technical Challenge

CLAAS wants teams to build computer vision solutions for autonomous agricultural machines:

**Primary objective:** Build a good, effective, efficient **bounding-box person detector** for agricultural environments. This is safety-critical -- missing a person in a field can be fatal.

**Secondary objective:** **Scene understanding** -- extract as much useful information from images and video as possible. What's in the field? What are the conditions? What obstacles exist? What's the crop state?

**Key characteristics of agricultural CV vs. standard CV:**
- Unstructured environments (no lanes, signs, road markings)
- Variable lighting (dawn, dusk, bright sun, shadows from machinery)
- Occlusion by crops (people partially hidden in tall corn/wheat)
- Diverse conditions (mud, dust, rain, fog)
- Mixed objects (people, animals, vehicles, equipment, structures)
- Need for real-time processing on edge hardware

### Context: Available Data

- **500 MB of image and video data** from CLAAS agricultural machines (cameras mounted on harvesters)
- **LIDAR + Camera** sensor modalities available
- Platform: Kaggle integration ("CAGGLE" = CLAAS + Kaggle)

### Context: Relevant State-of-the-Art

- **YOLOv8/v9/v11** -- Real-time object detection, easy to fine-tune on custom data
- **RT-DETR** -- Transformer-based real-time detector, strong on diverse scenes
- **Grounding DINO / Florence-2** -- Open-vocabulary detection, zero-shot capability
- **SAM 2 (Segment Anything)** -- Powerful segmentation model, can be combined with detectors
- **Depth Anything v2** -- Monocular depth estimation from single images
- **CLIP / SigLIP** -- Image-text understanding for scene classification
- **Transfer learning** from COCO/ImageNet to agricultural domain is standard practice
- **Data augmentation** (crop-specific: simulated occlusion by vegetation, weather effects) significantly boosts agricultural CV performance

### Context: Your Team

Use the Configuration section at the top of this file for team parameters.

### Deduplication

If `avoid_duplicates` is `true` in the configuration:
- You MUST first read every `.md` file in @ideas/challenge-2-computer-vision/.
- For each new idea, confirm it does NOT overlap in core concept with any existing idea.
- Add a **Differentiation** section explaining how the new idea is distinct from all existing ones.
- If all obvious angles are taken, push into more creative or hybrid territory.

If `avoid_duplicates` is `false`, skip this step entirely.

### Generate Ideas

Generate **5 distinct project ideas** ranked by win probability. For each idea, provide:

1. **Name** -- Catchy, memorable project name
2. **One-line pitch** -- Single sentence that makes a judge lean forward
3. **Problem statement** -- The specific safety/autonomy gap this addresses (2-3 sentences)
4. **Solution** -- Architecture overview: what models, what pipeline, what the system does end-to-end (paragraph)
5. **Key innovation** -- What elevates this beyond "we fine-tuned YOLO on the dataset"
6. **Tech stack** -- Specific models, frameworks, hardware requirements
7. **Data strategy** -- How to use the 500MB dataset: split, augment, validate. What to do if labels are missing.
8. **23-hour plan** -- Hour-by-hour breakdown: when to train, when to build the demo UI, when to stop tuning and polish
9. **Demo script** -- What the 3-minute pitch looks like: live inference on test images/video, metrics shown, comparison to baseline, the "wow" moment
10. **Metrics & evaluation** -- mAP, precision/recall targets, FPS benchmarks, false negative analysis (missed persons = safety failure)
11. **Risks & mitigations** -- Training time, data quality issues, compute limits, backup plans
12. **Win score** -- Rate 1-10 on: Innovation, Feasibility, Demo Impact, Technical Depth, Safety Relevance
13. **Differentiation** -- (only if `avoid_duplicates` is true) How this differs from existing ideas

### Thinking Process

Before generating ideas, reason through:
- Person detection in agriculture is safety-critical: **false negatives are unacceptable**. How does this change the architecture vs. standard object detection?
- What combination of models creates the strongest "layers of intelligence" that CLAAS specifically asked for?
- Agricultural scenes have unique challenges (crop occlusion, variable terrain). What domain-specific augmentations or tricks give an edge?
- What makes a CV demo visually compelling in a 3-minute pitch? (Real-time video inference with overlays is powerful.)
- With 500MB of data, what's feasible for fine-tuning vs. what requires clever transfer learning or zero-shot approaches?
- How can LIDAR data complement camera-only approaches if available?

After generating all 5 ideas, write a **comparative analysis** paragraph explaining why your #1 pick is the strongest, how the ideas layer from simple-but-solid to ambitious-but-risky, and which idea you'd pick based on ML experience level (beginner vs. expert team).

### Output Instructions

Write each idea as a **separate file** in the `ideas/challenge-2-computer-vision/` directory.

**Naming convention:** `idea-NNN-<slug>.md` where NNN is a zero-padded number continuing from the highest existing file (start at 001 if empty), and `<slug>` is a short kebab-case name derived from the idea name.

**Example:** `ideas/challenge-2-computer-vision/idea-001-field-guardian.md`

Each file should contain the full idea with all 12 (or 13) sections listed above.

After writing all idea files, write the **comparative analysis** as a comment in the chat (not as a file).
