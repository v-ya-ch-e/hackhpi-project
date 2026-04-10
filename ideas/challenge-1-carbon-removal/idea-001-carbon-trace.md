# Idea 001: CarbonTrace

## 1. Name

**CarbonTrace** -- The Carbon Credit Passport

## 2. One-line pitch

Every carbon credit gets a living, interactive passport that tells its full removal story *and* shows a machine-verified trust score -- turning a flat PDF into proof you can explore and believe.

## 3. Problem statement

Carbon credits today are opaque PDF certificates. A buyer spending thousands of euros on offsets has no way to understand *what actually happened* -- where the biomass came from, how the reactor performed, where the biochar ended up. This opacity breeds justified skepticism and limits the market. Meanwhile, operators manually log data that nobody cross-checks against the physical sensor readings, creating room for errors or fraud.

## 4. Solution

CarbonTrace is a web application that gives each of Cula's 395 registered removal activities ("sinks") a rich, interactive passport page. When a user opens a credit's passport, they see:

- **The Journey Map**: An interactive map (Mapbox/Leaflet) showing the GPS path from biomass source to pyrolysis facility to application site, with real distances and emission calculations from the API.
- **The Process Timeline**: A step-by-step visual walkthrough of the removal activity -- biomass collection, pyrolysis (with real sensor data visualized as sparkline charts showing temperature, pressure, duration), and biochar application.
- **The Trust Score**: An AI-powered backend that cross-references the manual API entries against the sensor data and physical constraints. For example: does the logged biomass input mass make sense given the sensor-measured throughput? Does the claimed pyrolysis temperature match sensor readings for that time window? Anomalies are flagged, and each credit receives a 0-100 trust score with explanations.
- **The Evidence Drawer**: Images and video documentation linked to the activity, giving visual proof.

The combination means a consumer can *understand* the credit AND *trust* it -- in one interface.

## 5. Key innovation

Most hackathon entries will tackle either consumer experience OR data validation. CarbonTrace does both simultaneously, using the validation results *as* a consumer-facing feature. The trust score isn't hidden in an admin panel -- it's front and center on the passport, turning technical anomaly detection into a confidence signal that builds buyer trust. This reframes data validation from a B2B audit tool into a consumer product feature.

## 6. Tech stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js (React), Tailwind CSS, Framer Motion (animations) |
| Maps | Leaflet or Mapbox GL JS (free tier) |
| Charts | Recharts or D3.js (sensor data sparklines) |
| Backend API | FastAPI (Python) |
| Data processing | pandas, numpy (sensor data analysis) |
| AI/Anomaly detection | OpenAI GPT-4o API (natural language anomaly explanations), scikit-learn (statistical anomaly detection on sensor time series) |
| Hosting | AWS (EC2/Lambda + S3 for static assets) |
| Pipeline | n8n for data ingestion orchestration (fetch API data, process sensor CSVs) |

## 7. Data usage

- **Removal Activity API**: Core data source. Each of the 395 sinks becomes a passport page. Mass balances, GPS coordinates, distances, and emission calculations power the journey map and timeline.
- **Machine Sensor Data**: The ~60MB / ~500 sensor streams are parsed and correlated with removal activities by timestamp and facility. Temperature, pressure, and screw speed data is displayed in the timeline AND fed into the anomaly detection engine for trust scoring.
- **Documentation (images/video)**: Displayed in the evidence drawer for each activity. Surveillance footage of biomass pickup and biochar application provide visual proof.
- **Fake Data**: The trust score algorithm is specifically designed to catch the injected fake entries. During the demo, we show it flagging fake records in real-time.

## 8. 23-hour plan

| Hour | Who | What |
|------|-----|------|
| 0-1 | All | Setup: repo, Next.js scaffold, FastAPI skeleton, explore API + sensor data structure |
| 1-3 | Dev A (Frontend) | Build passport page layout: map component, timeline skeleton, trust score badge |
| 1-3 | Dev B (Backend) | FastAPI endpoints: fetch/cache removal activity data, serve processed sensor data |
| 1-3 | Dev C (Data) | Explore sensor data: parse CSVs, correlate sensor readings with removal activities by timestamp/facility |
| 1-3 | Dev D (UI/UX) | Design passport UI in Figma, asset creation, color system, responsive layout |
| 3-5 | Dev A | Interactive map with GPS points, route lines, distance labels |
| 3-5 | Dev B | Anomaly detection engine v1: statistical checks (z-score on sensor vs API values, physical constraint validation) |
| 3-5 | Dev C | Build data pipeline: n8n workflow to ingest API + sensor data, normalize, store |
| 3-5 | Dev D | Implement timeline component with step-by-step process visualization |
| 5-7 | Dev A | Sensor data sparkline charts in timeline (temperature, pressure curves) |
| 5-7 | Dev B | Trust score calculation: combine anomaly flags into weighted 0-100 score per sink |
| 5-7 | Dev C | AI explanation layer: feed anomalies to GPT-4o, generate human-readable trust explanations |
| 5-7 | Dev D | Evidence drawer: image/video gallery per activity |
| 7-9 | Dev A+D | Polish journey map animations, transitions between timeline steps |
| 7-9 | Dev B+C | Fake data detection: tune anomaly thresholds to catch injected fakes, validate against known patterns |
| 9-11 | All | Integration: connect frontend to backend, end-to-end flow working |
| 11-13 | Dev A+D | Landing page with overview stats (395 sinks, 7000t CO2, aggregate trust metrics) |
| 11-13 | Dev B+C | API hardening, edge case handling, performance optimization |
| 13-15 | Dev A+D | Responsive design, dark mode, micro-interactions |
| 13-15 | Dev B+C | Trust score dashboard: sortable list of all credits with trust scores, filter by anomaly type |
| 15-17 | All | **Feature freeze.** Bug fixes only. |
| 17-19 | All | Demo preparation: pick the 3 most compelling passport examples (one clean, one flagged fake, one with interesting sensor data) |
| 19-20 | All | Pitch rehearsal, slide deck for intro/context, screen recording backup |

## 9. Demo script

**Opening hook (30s):** "You just spent 500 euros to offset your company's carbon footprint. You got this." *Show a boring PDF certificate.* "A flat PDF. You have no idea what actually happened. Did biomass really get converted? Was it applied to a real farm? Or is this just a number on paper? Meet CarbonTrace."

**Live demo (2m):**
1. Open landing page showing aggregate stats: 395 removal activities, ~7,000 tons CO2, average trust score.
2. Click into a specific credit passport. The map animates, showing the journey from biomass source (near Hamburg) to Stefan Block's pyrolysis facility to the application farm.
3. Walk through the timeline: "Here's the biomass pickup -- you can see the surveillance footage. Here's the reactor -- 743 degrees for 58 minutes, look at the real sensor data." Show the sparkline charts.
4. Reveal the trust score: "This credit scores 94/100. The sensor data matches the manual entries, the mass balance checks out, the GPS locations are consistent."
5. Now switch to a *flagged* credit: "But this one scores 23. Watch -- the operator logged 5 tons of biomass input, but the sensor data shows the reactor wasn't even running that day. CarbonTrace caught the fake."
6. Show the AI-generated explanation: "The temperature sensors recorded ambient temperature during the claimed processing window, indicating no pyrolysis occurred."

**Closing impact (30s):** "Cula has 395 removal activities worth 7,000 tons of CO2. CarbonTrace turns each one from a trust-me certificate into a show-me passport. For buyers, it's confidence. For Cula, it's a competitive moat. For the planet, it's what makes carbon markets actually work."

## 10. Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| Sensor data hard to correlate with specific removal activities | Fall back to facility-level correlation (match by facility ID + date range). Even approximate matching is impressive. |
| Anomaly detection produces too many false positives | Start with simple physical constraints (temperature must be 600-900C during operation, mass_in > mass_out). Conservative thresholds. |
| API data format surprises | Explore API in first hour. Build adapter layer. If API is down, pre-cache a subset as JSON fixtures. |
| Map rendering performance with 395 points | Cluster markers at overview level, load detail on click. Well-solved problem with Leaflet. |
| GPT-4o rate limits or latency | Pre-generate explanations for all 395 sinks during data pipeline step. Cache results. |
| Not enough time for polish | Feature freeze at hour 15. The core passport + trust score is the MVP; everything else is enhancement. |

## 11. Win score

| Criterion | Score | Rationale |
|-----------|-------|-----------|
| Innovation | 9/10 | Combining validation-as-consumer-feature is a non-obvious framing |
| Feasibility | 8/10 | All technologies are well-known to the team; main risk is data correlation |
| Demo Impact | 9/10 | The fake-detection reveal moment is a guaranteed crowd-pleaser |
| Technical Depth | 8/10 | Multi-source data pipeline + anomaly detection + AI explanations + interactive frontend |
| Problem-Solution Fit | 9/10 | Directly addresses both of Cula's stated directions simultaneously |
| **Average** | **8.6/10** | |
