# Idea 005: BiocharPulse

## 1. Name

**BiocharPulse** -- The Living Reactor Dashboard

## 2. One-line pitch

A real-time digital twin of Cula's pyrolysis reactor that turns 500 sensor streams into a living, breathing visualization -- where consumers see the heartbeat of carbon removal and operators see the health of their machine at a glance.

## 3. Problem statement

Cula's pyrolysis reactors generate ~60MB of data from 500 sensors, but this data sits in CSV files that only engineers can interpret. Consumers have zero intuition for what happens inside a reactor -- it's a black box. Operators, meanwhile, lack a unified dashboard that shows their machine's health holistically. The rich sensor data exists but serves neither audience.

## 4. Solution

BiocharPulse is a dual-audience dashboard built around an animated reactor visualization:

**Consumer Mode:**
- A stylized, animated cross-section of the pyrolysis reactor.
- Real sensor data drives the visualization: temperature shown as a color gradient (blue to white-hot), biomass flow shown as moving particles driven by screw speed sensors, pressure shown as gauge indicators.
- As the user watches a "processing session" play back (real data, compressed to 30 seconds), narration explains each phase: loading, heating, steady-state pyrolysis, cooling, biochar output.
- At the end, the user sees the output: "This session converted 4.2 tons of biomass into 1.3 tons of biochar. 2.1 tons of CO2 permanently removed."
- Accompanying panels show: where this biochar went (map), the full carbon balance, and the credit issued.

**Operator Mode:**
- Same core visualization with added technical detail.
- All 500 sensor channels available as selectable overlays.
- Health indicators: "Is the reactor operating within normal parameters?" Anomaly alerts when readings fall outside expected ranges.
- Historical comparison: overlay today's run against past runs to spot degradation.
- Flagged sessions where sensor data suggests issues (potential quality problems, efficiency drops, or fake data).

## 5. Key innovation

The digital twin concept is well-known in industrial IoT, but applying it to *consumer education* is novel. Instead of a separate explainer website and a separate operator dashboard, BiocharPulse uses the same visualization for both audiences -- just at different detail levels. This means development effort serves double duty, and the consumer experience is grounded in real operational data rather than abstract animations.

## 6. Tech stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js, React, Tailwind CSS |
| Reactor visualization | HTML5 Canvas or Three.js (2D/2.5D reactor cross-section), GSAP (animation timing) |
| Sensor charts | D3.js or Recharts (time-series overlay charts) |
| Backend | FastAPI (Python) |
| Data processing | pandas, numpy (sensor data resampling, normalization, anomaly detection) |
| AI | OpenAI GPT-4o (generate narration text for consumer mode sessions) |
| Hosting | AWS (S3 + CloudFront for frontend, EC2 for API) |

## 7. Data usage

- **Machine Sensor Data**: The star of this idea. All ~500 sensors are parsed, categorized (temperature, pressure, throughput, motor current, etc.), and mapped to positions on the reactor cross-section. Temperature sensors drive the heat gradient, screw speed drives particle animation, pressure drives gauge displays.
- **Removal Activity API**: Links reactor sessions to specific removal activities. Mass balances, GPS coordinates, and emission data provide the "before" (biomass) and "after" (biochar application) context around the reactor visualization.
- **Documentation**: Background media (images of the actual reactor, biomass, biochar) displayed alongside the digital twin for grounding.
- **Fake Data**: Operator mode's anomaly detection flags sessions where sensor data is inconsistent or physically impossible -- serving as a validation layer.

## 8. 23-hour plan

| Hour | Who | What |
|------|-----|------|
| 0-2 | All | Deep sensor data exploration. Understand sensor types, sampling rates, facility mapping. Pick 2-3 sessions with complete data. |
| 2-4 | Dev A (Visualization) | Reactor cross-section: SVG or Canvas base layout with labeled zones (input, chamber, output) |
| 2-4 | Dev B (Backend) | FastAPI: serve processed sensor data as time-series JSON for selected sessions |
| 2-4 | Dev C (Data) | Sensor data pipeline: parse CSVs, categorize by type, resample to consistent intervals, normalize |
| 2-4 | Dev D (UI/UX) | Design reactor visual language, color scales for temperature, particle effects for flow |
| 4-7 | Dev A+D | Animate the reactor: temperature gradient driven by sensor data, particles moving at screw speed, pressure gauges |
| 4-7 | Dev B | Consumer mode narration: GPT-4o generates per-session text, cached as JSON |
| 4-7 | Dev C | Anomaly detection: compute per-sensor z-scores, flag sessions with readings outside physical bounds |
| 7-9 | Dev A | Playback controls: play/pause, speed slider, timeline scrubber for session replay |
| 7-9 | Dev B+C | Operator mode: sensor channel selector, multi-line overlay chart, health indicators |
| 7-9 | Dev D | Consumer mode UI: narration overlay, phase labels ("Heating", "Pyrolysis", "Cooling"), output summary card |
| 9-11 | Dev A+D | Landing page: pick a session, mode toggle (consumer/operator) |
| 9-11 | Dev B+C | Historical comparison: overlay two sessions on the same chart. Link to removal activity data (map, mass balance). |
| 11-13 | All | Integration: complete flow from landing -> session select -> animated playback -> result summary |
| 13-15 | All | Polish: smooth animations, responsive layout, optimize for projector display |
| 15-17 | All | **Feature freeze.** Fix bugs, performance tune (60fps target for animation). |
| 17-19 | All | Demo prep: select the single most visually impressive session. Practice narrating alongside the animation. |
| 19-20 | All | Pitch rehearsal, backup recording |

## 9. Demo script

**Opening hook (30s):** "This is a pyrolysis reactor. It heats biomass to 750 degrees without oxygen and locks carbon away for a thousand years. You've never seen inside one. Until now." *Reactor visualization appears, cold and still.*

**Live demo (2m):**
1. Hit play. The reactor comes to life. Temperature gradient shifts from blue to orange to white as real sensor data drives the heat. Particles begin moving as the screw engages. "What you're seeing is real. Every temperature, every pressure reading, every particle speed comes from actual sensors on a real reactor near Hamburg."
2. Narration panel explains each phase in simple language. Point out the temperature gauge climbing to 743C. "No oxygen, no combustion, pure pyrolysis."
3. Fast-forward to completion. Output summary: "4.2 tons in, 1.3 tons of biochar out. 2.1 tons of CO2 locked away."
4. Switch to operator mode. Same visualization but with technical overlays. Show 6 sensor channels simultaneously. "An operator sees the health of their machine in real-time."
5. Quick anomaly demo: show a session where temperature flatlines during claimed operation. "This session was flagged -- the sensors say the reactor wasn't running. That's our fake."
6. Show the removal activity link: map pin on the application farm, mass balance, credit issued.

**Closing impact (30s):** "Cula's reactors process thousands of tons per year. BiocharPulse makes every session visible -- to consumers who need to understand, and to operators who need to verify. One visualization, two audiences, zero black boxes."

## 10. Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| 500 sensors is overwhelming to visualize | Curate 10-15 key sensors (core temperature, pressure, screw speed, motor current). The rest are available in operator mode as opt-in overlays. |
| Reactor cross-section animation takes too long to build | Start with a 2D SVG schematic (simpler than 3D). Temperature as background color gradient is the minimum viable animation. |
| Sensor data sampling rates vary wildly | Resample all streams to a consistent interval (1s or 5s) in preprocessing. |
| Consumer narration feels disconnected from the visual | Time-sync narration to animation playback using GSAP timeline markers. |
| Performance issues with 500 data points per frame | Pre-compute animation keyframes. Render to canvas for performance. Only active sensors update per frame. |
| Scope creep between consumer and operator modes | Consumer mode is MVP. Operator mode is stretch goal. Even consumer mode alone is a strong demo. |

## 11. Win score

| Criterion | Score | Rationale |
|-----------|-------|-----------|
| Innovation | 8/10 | Digital twin for consumer education is a fresh application of industrial concepts |
| Feasibility | 7/10 | Reactor animation is the bottleneck. 2D is achievable; 3D would be risky. |
| Demo Impact | 9/10 | A living, data-driven reactor animation is visually captivating on a projector |
| Technical Depth | 7/10 | Sensor data processing + animation is interesting but narrower than multi-source pipelines |
| Problem-Solution Fit | 7/10 | Strong for consumer understanding. Weaker on data validation (only anomaly flagging, not full audit). |
| **Average** | **7.6/10** | |
