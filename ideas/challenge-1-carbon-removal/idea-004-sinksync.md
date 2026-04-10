# Idea 004: SinkSync

## 1. Name

**SinkSync** -- Multi-Modal Carbon Audit Engine

## 2. One-line pitch

A reconciliation engine that ingests Cula's API records, sensor streams, images, and videos, uses AI vision to extract data from documentation, and builds an automated audit trail that catches every inconsistency -- including reading truck weight displays from surveillance footage.

## 3. Problem statement

Cula's data lives in silos: an API with manual entries, sensor CSVs from reactors, images, PDFs, and surveillance videos. Today, no system connects these sources to verify consistency. A human would need to manually open a surveillance video, read the weight on a truck scale display, and compare it to the API's logged biomass mass -- a process that doesn't scale. Meanwhile, the fake data sits undetected because cross-referencing is too labor-intensive.

## 4. Solution

SinkSync is a data reconciliation pipeline with an AI-powered evidence extraction layer:

1. **Multi-Modal Ingestion**: All data sources are loaded into a unified data store. API records are indexed by sink ID. Sensor data is indexed by facility + timestamp. Documentation (images/videos) is indexed by metadata (timestamps, GPS, filenames).
2. **AI Evidence Extraction**: Using GPT-4o Vision, the system analyzes documentation:
   - Reads weight displays from truck/scale photos and surveillance footage frames.
   - Extracts dates, quantities, and locations from PDF delivery documents.
   - Identifies biochar vs. biomass in images (visual classification).
   - Reads text overlays on surveillance footage (timestamps, camera IDs, weight readings).
3. **Reconciliation Engine**: For each removal activity, the system attempts to match:
   - API-claimed biomass mass vs. weight extracted from documentation images.
   - API-claimed timestamp vs. surveillance footage timestamp.
   - API-claimed location vs. GPS metadata in images.
   - Sensor-measured throughput vs. API-claimed processing volume.
4. **Audit Report**: Each sink gets a structured audit report showing every data point, its source, whether it was corroborated by another source, and a final reconciliation status.

## 5. Key innovation

The standout feature is using AI vision to **extract structured data from unstructured documentation** -- turning surveillance footage and photos into queryable evidence. Most hackathon projects will treat the images as display-only assets. SinkSync treats them as *data sources* that can be cross-referenced against the API and sensors. The moment in the demo where GPT-4o reads "4,230 kg" from a blurry truck scale display and matches it against the API entry is a genuine "wow" moment.

## 6. Tech stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js, Tailwind CSS |
| Backend | FastAPI (Python) |
| AI Vision | OpenAI GPT-4o Vision API (image/frame analysis) |
| Video processing | OpenCV (Python, frame extraction from surveillance video) |
| Data processing | pandas (reconciliation logic, matching, scoring) |
| Pipeline orchestration | n8n (coordinate ingestion, extraction, reconciliation) |
| Storage | AWS S3 (images/video), local JSON (processed data) |
| Hosting | AWS EC2 |

## 7. Data usage

- **Removal Activity API**: The "claims" side of the audit. Every field (mass, location, timestamp, distance) is a claim to be verified against other sources.
- **Machine Sensor Data**: The "physics" side. Sensor throughput, temperature, and duration provide independent measurements of what the reactor actually processed.
- **Documentation (images/video)**: The "evidence" side -- and the key differentiator. AI vision extracts quantitative data from:
  - Surveillance footage frames (truck weights, timestamps, visual confirmation of biomass delivery)
  - Photos of biochar output (visual quality check, quantity estimation)
  - PDF/document scans (delivery manifests, weight tickets)
- **Fake Data**: Identified through reconciliation failures -- a fake record will have claims that don't match any corroborating evidence from sensors or documentation.

## 8. 23-hour plan

| Hour | Who | What |
|------|-----|------|
| 0-2 | All | Deep dive into ALL data sources. Catalog every image, video, sensor file. Map what documentation exists for which sinks. |
| 2-4 | Dev A (Frontend) | Audit report UI: per-sink detail page showing claims vs. evidence side-by-side |
| 2-4 | Dev B (Backend) | FastAPI: data loading endpoints, sink detail with all linked sources |
| 2-4 | Dev C (AI/Vision) | GPT-4o Vision experiments: feed sample images, iterate on prompts to extract weights, dates, quantities |
| 2-4 | Dev D (Pipeline) | OpenCV: extract key frames from surveillance videos (motion detection for truck arrivals) |
| 4-6 | Dev A | Evidence gallery per sink: show source images with AI-extracted annotations overlaid |
| 4-6 | Dev B | Reconciliation logic: comparison rules (mass tolerance ±5%, timestamp within 1 hour, GPS within 500m) |
| 4-6 | Dev C | Batch process all available images/videos through GPT-4o Vision. Cache extracted data. |
| 4-6 | Dev D | n8n workflow: orchestrate ingestion -> extraction -> reconciliation pipeline |
| 6-8 | Dev A+B | Reconciliation status dashboard: all sinks with match/mismatch indicators per data source |
| 6-8 | Dev C+D | Sensor correlation: match sensor time-series windows to API timestamps, compute agreement |
| 8-10 | Dev A | Visual diff view: side-by-side showing "API says X, sensor says Y, image shows Z" |
| 8-10 | Dev B+C | Confidence scoring: weighted reconciliation score based on how many sources corroborate |
| 8-10 | Dev D | Handle edge cases: sinks with missing documentation, partial sensor coverage |
| 10-13 | All | Integration: full pipeline running end-to-end for at least 20-30 sinks |
| 13-15 | All | Summary analytics: how many sinks fully reconciled, partially reconciled, failed |
| 15-17 | All | **Feature freeze.** Bug fixes, UI polish. |
| 17-19 | All | Demo prep: find the single best example where AI reads an image and matches the API |
| 19-20 | All | Pitch rehearsal, backup recording |

## 9. Demo script

**Opening hook (30s):** "Here's a surveillance camera frame from Cula's biomass yard." *Show a grainy image of a truck on a scale.* "Somewhere in this image is a number. A weight. A number that should match what the operator typed into the system. Let's find out."

**Live demo (2m):**
1. Show the AI vision extraction: feed the image to GPT-4o. It returns "4,230 kg, captured at 14:23 on March 12, 2026."
2. Pull up the API record for that date and facility. "The operator logged 4,200 kg. Within tolerance. This delivery is corroborated."
3. Open the full audit dashboard. Show 395 sinks with reconciliation status columns: API Data, Sensor Data, Documentation, Overall Status.
4. Filter to "Mismatch" records. Open one: "The API claims 6.1 tons processed, but the sensor throughput data shows maximum 3.8 tons capacity for that time window. And we found no documentation images for this date. Three strikes."
5. Show the structured audit report: a table with every claim, every source, match/mismatch, and confidence level.
6. Quick stat: "Out of 395 sinks, 347 fully reconcile. 36 partially reconcile. 12 have significant discrepancies. Those 12 are your investigation queue."

**Closing impact (30s):** "Every carbon credit should have an audit trail that goes beyond the operator's word. SinkSync turns Cula's images, videos, and sensors into independent witnesses. For the first time, the data watches the data. That's how you build a carbon market that auditors -- and buyers -- can trust."

## 10. Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| Documentation images may not contain readable weight displays | Focus on whatever IS readable (timestamps, location signs, visual confirmation). Even partial extraction is impressive. |
| GPT-4o Vision hallucinating numbers from images | Always show the source image alongside the extraction. Confidence thresholds. Human-in-the-loop flagging. |
| Not enough documentation linked to specific sinks | Show the system working on the subset that has documentation. Demonstrate the *capability* even if coverage is partial. |
| Video processing (frame extraction) takes too long | Pre-extract key frames in batch during hours 2-4. Use motion detection to pick only interesting frames. |
| Reconciliation logic too simplistic | Start with exact match + tolerance bands. This is already more than exists today. |
| Scope too large for 20 hours | Focus on 30-50 sinks with the best data coverage. Quality over quantity. |

## 11. Win score

| Criterion | Score | Rationale |
|-----------|-------|-----------|
| Innovation | 9/10 | Using AI vision to extract audit evidence from surveillance footage is genuinely novel |
| Feasibility | 6/10 | Heavy dependency on documentation quality. Risky if images aren't readable. |
| Demo Impact | 8/10 | The "AI reads a truck scale" moment is memorable. But less emotional than a consumer story. |
| Technical Depth | 9/10 | Multi-modal AI pipeline + reconciliation engine + cross-source matching |
| Problem-Solution Fit | 8/10 | Strong on Direction B, adds a novel evidence extraction angle Cula likely hasn't considered |
| **Average** | **8.0/10** | |
