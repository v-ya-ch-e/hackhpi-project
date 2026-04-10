# Idea 004: SafeHarvest -- Predictive Safety Map with Temporal Risk Scoring

## One-line pitch

A system that doesn't just detect people in the current frame but builds a persistent spatial risk map of the entire field, predicting *where* people are likely to appear next based on trajectory analysis, field geometry, and historical detection patterns.

## Problem Statement

Current person detection is reactive: it finds people in the current frame and raises an alarm. But autonomous machines need to be proactive. If a person was detected at the field edge 30 seconds ago and is walking parallel to the machine's path, the system should predict their future position and preemptively adjust. Similarly, certain field regions are inherently higher-risk (near access roads, gates, adjacent properties, field edges) and should have heightened sensitivity. A reactive-only system is always one frame away from failure.

## Solution

SafeHarvest combines **detection, tracking, prediction, and risk mapping** into a predictive safety system:

### 1. Detection + Tracking Layer
- YOLOv11 fine-tuned on CLAAS data for person detection
- ByteTrack multi-object tracker assigns persistent IDs to each detected person
- Each track accumulates a trajectory: position history, velocity, heading

### 2. Trajectory Prediction
- A lightweight **linear Kalman filter** (for short-term, <5s) and a **constant-velocity model** predict each person's future position 3-10 seconds ahead
- Prediction uncertainty grows with time, visualized as expanding ellipses on the map
- If trajectory intersects with the machine's planned path, trigger early warning

### 3. Spatial Risk Map
- The system maintains a **persistent top-down risk heatmap** of the field
- Risk sources:
  - **Active detections:** High-risk zones around currently detected persons (decays over time if person leaves)
  - **Predicted positions:** Medium-risk zones along predicted trajectories
  - **Static hotspots:** Field edges, gates, road access points, structures (detected in first scan of the field, permanently elevated risk)
  - **Historical patterns:** If persons were detected in an area earlier, that area retains elevated risk for the session
- The risk map is a 2D grid (e.g., 100x100) with values 0.0-1.0, updated every frame

### 4. Machine Path Integration
- Define the machine's planned path (straight line or GPS waypoints if available)
- Overlay predicted person positions onto the path
- Calculate **Time-To-Intersection (TTI):** how many seconds until a detected person's predicted trajectory crosses the machine's path
- Alert levels: TTI > 30s (monitor), TTI 10-30s (caution), TTI < 10s (STOP)

### 5. Dashboard
- **Split view:** Left = camera feed with detections and trajectory arrows, Right = top-down risk heatmap with machine position and path
- Animated trajectories show where people are heading
- Risk heatmap pulses in areas of elevated danger
- Alert banner with TTI countdown

## Key Innovation

The shift from **reactive detection to predictive safety planning**. No other hackathon team will think in terms of spatial risk maps and time-to-intersection -- this is how autonomous vehicles think, applied to agriculture. The persistent risk map means the system never fully "forgets" a person who walked behind a crop row; their last known area stays elevated-risk. This architectural decision directly prevents the most dangerous scenario: a person who was seen, disappeared behind crops, and reappears in the machine's path.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Object detection | YOLOv11-M (Ultralytics) |
| Multi-object tracking | ByteTrack (with Kalman filter) |
| Trajectory prediction | Custom Kalman filter + constant-velocity model (NumPy) |
| Risk map | NumPy 2D grid with Gaussian kernel updates |
| Homography | OpenCV perspective transform (image coords -> top-down) |
| Backend | FastAPI + WebSocket |
| Frontend | React + Next.js, D3.js for heatmap, Canvas for video overlay |
| Compute | AWS g5.xlarge (GPU for YOLO) |

## Data Strategy

**Detection training:** Same as other ideas -- fine-tune YOLOv11-M on CLAAS data (or pseudo-label with Grounding DINO).

**Tracking evaluation:** Need video sequences (not just images). If CLAAS data includes video, use directly. If only images, create synthetic sequences by applying small random translations to detected persons across frames.

**Risk map calibration:** No training needed -- the risk map is rule-based:
- Gaussian kernel around each detection (sigma proportional to detection uncertainty)
- Exponential decay over time (half-life: 10 seconds for active tracks, 60 seconds for lost tracks)
- Static hotspots from first-frame segmentation of field edges/structures

**Homography estimation:** If the camera's mounting position and angle are known (or estimable from the images), compute a perspective transform to map image coordinates to a top-down field view. If not, use a simplified pinhole camera approximation.

## 23-Hour Plan

| Hour | Activity |
|------|----------|
| 0-1 | Dataset exploration. Focus on video data -- need sequential frames. Set up AWS. |
| 1-3 | Prepare labels + train YOLOv11-M on CLAAS data. Standard pipeline. |
| 3-4 | Implement ByteTrack integration. Test tracking on video sequences. |
| 4-5 | Build Kalman filter trajectory predictor. Visualize predicted positions as expanding ellipses. |
| 5-6 | Build homography transform (image coords -> top-down view). Test on CLAAS images. |
| 6-8 | Build the risk map engine: Gaussian updates, temporal decay, static hotspot detection, historical patterns. |
| 8-9 | Implement path planning overlay: define machine path, compute TTI for each tracked person. |
| 9-10 | Build alert system: TTI thresholds, alert levels, machine stop recommendation. |
| 10-12 | Build FastAPI backend: video ingestion -> YOLO -> tracking -> prediction -> risk map -> JSON output. |
| 12-15 | Build React dashboard: split-view (camera + heatmap), trajectory arrows, TTI countdown, animated risk map. |
| 15-17 | Integration testing. Process full videos end-to-end. Tune risk map parameters (decay rate, kernel size). |
| 17-18 | **Feature freeze.** Create demo scenarios: person walking toward machine path, person disappearing behind crops. |
| 18-19 | Prepare pitch. Focus on the "predictive vs. reactive" narrative. |
| 19-20 | Buffer. |

## Demo Script (3 minutes)

**0:00-0:30 -- The Limitation.** "Today's person detection is reactive -- it tells you someone is there *right now*. But an autonomous machine weighing 15 tons can't stop on a dime. By the time you detect a person in your path, it may be too late. What if the machine could predict danger before it happens?"

**0:30-1:00 -- The Concept.** "SafeHarvest builds a living risk map of the field. Every detected person is tracked, their trajectory is predicted, and the system calculates exactly when and where they'll cross the machine's path. The machine doesn't just see -- it anticipates."

**1:00-2:00 -- Live Demo.** Show a CLAAS video with the split-view dashboard:
- Camera view on the left: person detected, trajectory arrow shows predicted path
- Risk heatmap on the right: glowing zone around the person, trajectory prediction line extending forward
- **Scenario 1:** Person walking parallel to machine -- low risk, green status
- **Scenario 2:** Person's trajectory intersects machine path -- TTI countdown appears: "10s... 8s... 6s..." -> RED ALERT: "STOP RECOMMENDED"
- **Scenario 3:** Person walks behind tall crops (lost detection) -- risk heatmap retains elevated risk in their last-known area, stays yellow: "The system doesn't forget."

**2:00-2:30 -- The Impact.** "SafeHarvest gives the machine 10-30 seconds of advance warning. At typical harvest speed (8 km/h), that's 20-65 meters of stopping distance. The difference between a detection and a prediction is the difference between an emergency stop and a safe deceleration."

**2:30-3:00 -- Vision.** "With GPS integration, the risk map becomes a persistent field safety record. Farms can identify high-risk zones, plan safer routes, and prove to regulators that their autonomous operation accounts for human safety not just in the moment, but proactively."

## Metrics & Evaluation

| Metric | Target | Why |
|--------|--------|-----|
| Person detection recall | >95% | Foundation must be solid |
| Tracking MOTA (Multi-Object Tracking Accuracy) | >70% | Tracking must be reliable |
| Track ID consistency | >80% (same person keeps same ID) | Trajectory prediction depends on consistent tracks |
| Trajectory prediction error @3s | <2m (if calibrated) | Useful prediction horizon |
| TTI alert accuracy | >80% true positive rate | False alarms erode trust |
| Risk map false-negative time | <3s (person enters area -> risk elevates) | Must be responsive |
| Risk map persistence | Risk stays elevated >30s after lost track | Safety buffer |
| FPS | >15 (detection + tracking + risk map) | Real-time operation |

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Video data insufficient for tracking | High | High | If only images: create synthetic video by augmenting detections across frames. Or focus demo on image-by-image risk accumulation. |
| Homography estimation inaccurate | Medium | Medium | Use simplified model (assume flat field). Top-down view is approximate but visually compelling. |
| Trajectory prediction poor for erratic movement | Medium | Low | Conservative prediction: expand uncertainty ellipses quickly. Better to over-predict risk than under. |
| Risk map parameters need tuning | Low | Low | Make decay rate and kernel size configurable. Tune during integration testing. |
| Concept harder to explain than "better YOLO" | Medium | Medium | Use animation and TTI countdown in demo. The visual is intuitive once seen. |

## Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 10 | Predictive safety mapping is genuinely novel for agricultural CV; no other team will think of this |
| Feasibility | 7 | Tracking + prediction adds complexity; video data availability is uncertain |
| Demo Impact | 9 | Animated risk map + TTI countdown is visually stunning and immediately understandable |
| Technical Depth | 9 | Detection + tracking + prediction + spatial mapping + path planning = deep stack |
| Safety Relevance | 10 | Proactive safety is fundamentally more valuable than reactive detection |
| **Total** | **45/50** | |
