# Idea 002: PyroGuard

## 1. Name

**PyroGuard** -- The Biochar Fraud Detector

## 2. One-line pitch

An AI-powered anomaly detection dashboard that ingests Cula's sensor streams, API records, and documentation to automatically flag physically impossible data and unmask the injected fakes -- then explains *why* each record is suspicious in plain language.

## 3. Problem statement

Cula's removal activities are tracked through manual operator inputs -- masses, locations, timestamps. But nobody systematically cross-checks these against the 500 physical sensors on the reactors. If an operator logs 10 tons of processed biomass but the reactor's screw speed sensor shows zero throughput, who catches it? The fake data Cula intentionally injected proves the point: manual data without validation is a liability for the entire carbon credit market.

## 4. Solution

PyroGuard is a validation-first dashboard that treats Cula's data as a forensic puzzle. The system:

1. **Ingests all sources**: Removal Activity API (395 sinks), sensor time-series data (500 sensors), and documentation metadata.
2. **Builds a physics model**: Defines physical constraints for the pyrolysis process -- valid temperature ranges, expected mass conversion ratios, minimum operation durations, consistent GPS distances vs. transport times.
3. **Runs multi-layer anomaly detection**:
   - **Layer 1: Physical bounds** -- Is the temperature within 600-900C? Is mass_out < mass_in? Is the operation duration > 30 minutes?
   - **Layer 2: Cross-source consistency** -- Does the sensor data agree with the API entry for the same time window? Does the logged location match the facility's known GPS?
   - **Layer 3: Statistical outliers** -- Using isolation forests and z-score analysis on the sensor time series to find records that deviate from the population.
   - **Layer 4: AI reasoning** -- Feed suspicious records + context to GPT-4o and ask it to assess plausibility and generate a human-readable fraud report.
4. **Presents results** in a sortable, filterable dashboard with red/yellow/green status for every removal activity, drill-down into specific anomalies, and a "Fake Data Scoreboard" showing which records the system identified as injected fakes.

## 5. Key innovation

Most fraud detection systems produce opaque scores. PyroGuard generates *natural language forensic reports* for each flagged record, explaining the chain of evidence ("Sensor 42 recorded 22C ambient temperature during the claimed 800C pyrolysis window; screw motor current was 0A, indicating no biomass feed; the logged biomass mass of 8.3 tons has no corresponding sensor activity"). This makes the technical output accessible to non-technical auditors, regulators, and even consumers.

## 6. Tech stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js, Tailwind CSS, AG Grid (data tables) |
| Charts | Recharts / Plotly.js (sensor time series, anomaly highlighting) |
| Backend | FastAPI (Python) |
| Data processing | pandas, numpy, scipy (statistical analysis) |
| Anomaly detection | scikit-learn (Isolation Forest, Local Outlier Factor), custom rule engine |
| AI explanations | OpenAI GPT-4o API |
| Pipeline | n8n (orchestrate data fetch, preprocessing, anomaly runs) |
| Hosting | AWS (EC2 for API, S3 for processed data) |

## 7. Data usage

- **Removal Activity API**: All 395 sinks loaded and indexed. Each record's mass balances, timestamps, GPS locations, distances, and emissions become the "claims" to validate.
- **Machine Sensor Data**: Primary validation source. 500 sensors parsed into time series. Correlated with removal activities by facility + time window. Temperature, pressure, and throughput sensors are the key fraud indicators.
- **Documentation**: Image metadata (timestamps, GPS tags) cross-referenced against API entries. Video surveillance footage timestamps compared to claimed biomass delivery times.
- **Fake Data**: The explicit target. The system's success is measured by its ability to identify which records are injected fakes, with an explanation of how each was detected.

## 8. 23-hour plan

| Hour | Who | What |
|------|-----|------|
| 0-1 | All | Explore all data sources. Map API schema, sensor CSV structure, documentation formats. |
| 1-3 | Dev A (Frontend) | Dashboard scaffold: sidebar nav, data table with status badges, filter controls |
| 1-3 | Dev B (Backend) | FastAPI: endpoints for removal activities list, individual activity detail, anomaly results |
| 1-3 | Dev C (Data) | Parse all sensor CSVs. Build facility-to-sensor mapping. Create time-series index. |
| 1-3 | Dev D (Pipeline) | n8n workflow: fetch API data, trigger preprocessing, store normalized JSON |
| 3-6 | Dev A | Anomaly detail view: side-by-side sensor chart vs. API claims, red highlight on mismatches |
| 3-6 | Dev B | Rule engine: physical bounds checks (temperature, mass, duration, GPS) |
| 3-6 | Dev C | Statistical anomaly detection: Isolation Forest on sensor features, z-score per sensor channel |
| 3-6 | Dev D | Cross-source correlation: match API entries to sensor time windows, compute agreement scores |
| 6-8 | Dev A | Sensor time-series visualization: interactive charts with anomaly regions highlighted |
| 6-8 | Dev B+C | Combine all anomaly layers into unified scoring (weighted flags -> severity score per sink) |
| 6-8 | Dev D | AI explanation pipeline: batch suspicious records through GPT-4o, cache forensic reports |
| 8-10 | Dev A | "Fake Data Scoreboard" page: leaderboard of most suspicious records, confidence levels |
| 8-10 | Dev B+C+D | Tune detection: compare flagged records against intuition, adjust thresholds, rerun |
| 10-13 | Dev A+D | Summary statistics page: total flagged, breakdown by anomaly type, aggregate charts |
| 10-13 | Dev B+C | Edge cases: handle missing sensor data, partial time windows, facility ambiguity |
| 13-15 | All | Integration testing. End-to-end flow: API -> processing -> anomaly -> dashboard |
| 15-17 | All | **Feature freeze.** Polish UI, fix display bugs, optimize load times. |
| 17-19 | All | Prepare demo: identify the best "detective story" to walk through (a clear fake caught) |
| 19-20 | All | Pitch practice, backup screen recordings |

## 9. Demo script

**Opening hook (30s):** "Cula removes 7,000 tons of CO2 per year. But here's the thing -- they *know* some of their data is fake. They injected it themselves to test if anyone could catch it. We built PyroGuard to find every last fake -- and we did."

**Live demo (2m):**
1. Open dashboard. Show 395 removal activities: green (clean), yellow (warning), red (flagged). "Out of 395 records, PyroGuard flagged 12 as highly suspicious."
2. Sort by severity. Click the #1 most suspicious record.
3. Show the split view: left side shows the API claim ("8.3 tons biomass processed at 780C for 55 minutes on March 12"). Right side shows the sensor data for that facility on that date.
4. The sensor chart shows flat ambient temperature, zero screw motor current, no pressure changes. "The reactor wasn't even running."
5. Scroll to the AI forensic report: a clear paragraph explaining the mismatch in plain English.
6. Quick montage: flip through 3 more flagged records, each with a different anomaly type (mass imbalance, GPS inconsistency, impossible conversion ratio).
7. Show the Fake Data Scoreboard: "We believe these 8 records are the injected fakes. Here's our confidence level for each."

**Closing impact (30s):** "Carbon markets run on trust. PyroGuard turns sensor data into an automated auditor that catches fraud and explains it in plain language. Cula can deploy this today -- every new removal activity gets validated before a credit is issued. That's how you make carbon removal credible."

## 10. Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| Can't correlate sensor data to specific API entries | Use facility + date range as primary key. Even coarse matching is valuable for demo. |
| Fake data is too subtle to detect | Start with obvious physical constraint violations. Layer statistical methods. Even partial detection is a strong demo. |
| Too many false positives undermine credibility | Show confidence levels, not binary flags. "Suspicious" vs "Flagged" vs "Confirmed anomaly". |
| 60MB sensor data takes too long to process | Pre-process in first 2 hours, cache results. Processing is one-time, not real-time. |
| GPT-4o explanations are too generic | Provide very specific context in prompts (exact sensor values, exact claims, physical constraints). Few-shot examples. |
| Dashboard looks like a boring admin panel | Invest in visual design: red/yellow/green color coding, animated severity badges, sensor charts with anomaly shading. |

## 11. Win score

| Criterion | Score | Rationale |
|-----------|-------|-----------|
| Innovation | 8/10 | NLP forensic reports on anomaly detection is a fresh angle |
| Feasibility | 8/10 | Well-scoped pipeline: ingest, analyze, display. Team has all needed skills. |
| Demo Impact | 9/10 | "We found the fakes" is an inherently dramatic reveal |
| Technical Depth | 9/10 | Multi-layer detection pipeline + AI reasoning + cross-source correlation |
| Problem-Solution Fit | 8/10 | Directly tackles Direction B; less consumer appeal but strong for judges |
| **Average** | **8.4/10** | |
