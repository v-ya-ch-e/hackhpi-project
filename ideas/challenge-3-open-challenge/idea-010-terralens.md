# Idea 010: TerraLens

## 1. Name

**TerraLens**

## 2. One-line Pitch

An n8n-powered autonomous agent that monitors a farmer's fields 24/7 using satellite imagery, weather data, and AI — and sends plain-language WhatsApp alerts when action is needed.

## 3. Problem Statement

Modern precision agriculture generates vast amounts of satellite, weather, and soil data — but farmers don't have time to log into dashboards and interpret maps. A German farmer managing 200 hectares of mixed crops checks their weather app and looks out the window. Satellite-derived insights (drought stress detected in northwest quadrant, NDVI dropping faster than expected, optimal fungicide spray window in 48 hours) exist but are locked inside platforms that require active monitoring. The data-to-decision gap means farmers miss optimal intervention windows by 3-7 days on average, costing 10-20% of yield per late intervention. The problem isn't data availability — it's data delivery at the right moment in the right format.

## 4. Solution

TerraLens is an autonomous monitoring agent built on n8n (workflow automation) that runs in the background and pushes alerts to farmers via WhatsApp/SMS when their fields need attention. No dashboard, no app to check — the intelligence comes to you.

**How it works:**

1. **Field registration:** Farmer draws field boundaries on a web map and selects their crops
2. **Automated monitoring pipelines (n8n):**
   - Every 5 days: fetch new Sentinel-2 satellite imagery, compute NDVI/moisture indices, compare to historical baseline and crop growth model
   - Every 6 hours: check weather forecast for frost, hail, heavy rain, drought thresholds
   - Weekly: compute crop health trend and flag anomalies (sudden NDVI drops, uneven growth patterns)
   - On trigger: cross-reference weather windows with crop stage to identify optimal spray/fertilize/irrigate timing
3. **AI interpretation (Claude):** Raw data anomalies are sent to Claude with field context. It generates a plain-language alert: "Your wheat field (B3) shows signs of drought stress in the eastern third. NDVI dropped 15% this week. No rain expected for 5 days. Recommendation: irrigate the eastern section within 48 hours to prevent 20%+ yield loss."
4. **Delivery:** Alert sent via WhatsApp (Twilio API) with a link to a simple map showing the affected area highlighted.

**Core user flow:** Register once → receive alerts only when action is needed → tap to see field map → take action → confirm resolution.

## 5. Key Insight

**The best farm management system is invisible until something goes wrong.** Every precision agriculture startup builds a dashboard. Farmers don't want another dashboard — they want to know when they need to DO something. TerraLens inverts the information flow: instead of the farmer pulling data, the system pushes actionable intelligence. This is the same paradigm shift as infrastructure monitoring (Datadog/PagerDuty) — you don't stare at graphs, you get paged when something breaks. The second insight: **n8n as the orchestration layer** means the entire backend is visual, modifiable, and extensible by non-engineers. A hackathon team can build a sophisticated multi-source monitoring pipeline in hours using n8n's node-based workflow builder, which would take days to code from scratch.

## 6. Tech Stack

- **Workflow Orchestration:** n8n (self-hosted on AWS EC2) — visual workflow builder for all monitoring pipelines
- **Satellite Data:** Sentinel-2 via Copernicus Data Space API (free) — NDVI, moisture indices
- **Weather:** OpenMeteo API (free) — forecast + historical
- **AI Reasoning:** AWS Bedrock (Claude) via n8n HTTP node — interpret anomalies, generate alerts
- **Messaging:** Twilio WhatsApp API (or Twilio SMS fallback)
- **Frontend:** Next.js (React) with Mapbox — field registration + alert detail maps
- **Image Processing:** Python (rasterio, numpy) running as AWS Lambda functions called from n8n
- **Database:** AWS DynamoDB (field data, alert history, farmer preferences)
- **Hosting:** AWS EC2 (n8n), Amplify (frontend), Lambda (processing)

## 7. Data Strategy

- **Sentinel-2 satellite imagery:** Free via Copernicus, 10m resolution, 5-day revisit. n8n scheduled workflow fetches latest cloud-free imagery, clips to field boundary, computes vegetation indices.
- **NDVI baselines:** Compute historical NDVI time series for each field from Sentinel-2 archive (2-3 years). Anomalies are deviations from expected NDVI for this crop at this growth stage.
- **Weather data:** OpenMeteo for both historical and 7-day forecast. Threshold-based alerts (frost < 2°C, drought = no rain + high temp for 7+ days, spray window = no rain in next 6 hours + wind < 15 km/h).
- **Crop growth models:** Simplified Growing Degree Day (GDD) model to estimate crop stage from accumulated temperature. Determines which alerts are relevant (frost alert matters pre-flowering, not post-harvest).
- **Alert generation:** Claude receives structured data (anomaly type, magnitude, crop stage, weather forecast) and generates farmer-friendly alert text. Few-shot prompted with example alerts.
- **Feedback loop:** Farmer can reply "done" or "ignore" to alerts. Over time, this tunes alert sensitivity per farmer.

## 8. 23-Hour Plan

| Hours | Phase | Tasks |
|-------|-------|-------|
| 0-1 | **Setup** | Deploy n8n on AWS EC2, scaffold Next.js frontend, set up Twilio WhatsApp sandbox |
| 1-3 | **Field Registration** | Map-based field drawing UI, store in DynamoDB, assign crop type and growth stage |
| 3-5 | **Satellite Pipeline (n8n)** | Build n8n workflow: trigger → fetch Sentinel-2 → clip to field → compute NDVI → compare to baseline → flag anomalies. Use Lambda for rasterio processing. |
| 5-7 | **Weather Pipeline (n8n)** | Build n8n workflow: scheduled trigger → fetch OpenMeteo → check thresholds (frost, drought, spray window) → generate weather alerts |
| 7-9 | **AI Alert Generation** | Build n8n workflow node: anomaly data → Claude → plain-language alert with recommendation. Test with sample anomalies. |
| 9-11 | **WhatsApp Delivery** | Build n8n workflow: alert → format for WhatsApp → send via Twilio → log delivery. Include map link to field detail page. |
| 11-12 | **Alert Detail Page** | Simple web page showing field map with anomaly highlighted, satellite imagery comparison, recommendation |
| 12-14 | **Sleep/Break** | |
| 14-16 | **Pipeline Integration** | Connect all n8n workflows end-to-end. Test full flow: satellite change detected → AI alert → WhatsApp delivered. |
| 16-18 | **Demo Scenarios** | Prepare 3-4 demo scenarios with real satellite data: drought stress, unusual NDVI drop, optimal spray window, frost warning. Pre-cache data for reliability. |
| 18-20 | **Polish + Testing** | End-to-end testing, error handling in n8n workflows, alert formatting |
| 20-22 | **Pitch Prep** | Build pitch, prepare to show n8n workflows live (visual = impressive), practice |
| 22-23 | **Final Polish** | Deploy, ensure WhatsApp delivery works, final n8n workflow checks |

**Cut if behind:** Drop satellite pipeline (weather-only alerts still valuable), simplify to SMS instead of WhatsApp, reduce to 1-2 demo scenarios, skip feedback loop.

**Feature freeze:** Hour 18.

## 9. Demo Script

| Time | Section | Content |
|------|---------|---------|
| 0:00-0:15 | **Hook** | [Show phone buzzing with WhatsApp notification] "This farmer just got a message that saved 20% of his wheat yield. He didn't check any app. He didn't open any dashboard. The intelligence found HIM." |
| 0:15-0:45 | **Problem** | Precision agriculture has a delivery problem. Data exists: satellites pass over every field every 5 days. Weather forecasts are accurate. But farmers manage 200+ hectares and can't watch dashboards. By the time they notice drought stress visually, they've already lost yield. The gap between data and decision costs €2,000-5,000 per field per year. |
| 0:45-2:15 | **Demo** | Show the n8n workflow dashboard — "This is our entire backend: visual, modular, running 24/7." Walk through the satellite monitoring workflow: data flows from Copernicus → through processing nodes → AI interpretation → WhatsApp. Then show a live alert: "Your wheat field B3 shows drought stress in the eastern third" with satellite imagery comparison (healthy green vs stressed yellow). Show the WhatsApp message on a phone — simple, actionable, with a map link. Click the link → field detail page with the anomaly highlighted. Then show a second alert: "Optimal fungicide spray window: next 6 hours. Wind calm, no rain expected for 18 hours." Show how n8n makes it easy to add new data sources and alert types. |
| 2:15-2:45 | **Impact** | "TerraLens turns every farmer into a precision farmer — without changing how they work. The data comes to them, in their language, on the device they already check 50 times a day. And our n8n backend means we can add new data sources, new alert types, and new delivery channels in minutes, not months." |
| 2:45-3:00 | **Close** | "The future of farming isn't more dashboards. It's intelligence that finds you when it matters." |

## 10. "Build Something People Want" Test

**Who uses this after the hackathon?** German and European farmers managing 50+ hectare operations (there are 3.8 million farms in Germany, France, and Poland alone). Agricultural cooperatives wanting to offer digital services to members. Crop insurance companies wanting to reduce claim payouts by enabling earlier interventions.

**Path from demo to product:**
1. Beta with 20-30 farms in Brandenburg/Sachsen-Anhalt (large-scale arable farming near HPI/Potsdam)
2. Add more data sources: soil moisture sensors, farm management system integration (CLAAS could provide machine data)
3. Revenue: €5-10/hectare/year subscription. For a 200ha farm, that's €1,000-2,000/year — easily justified by preventing one late intervention per season.
4. B2B: white-label for agricultural cooperatives, crop insurance companies, farm equipment manufacturers (CLAAS integration play)
5. Long-term: become the "PagerDuty for farms" — the alerting layer that sits on top of all agricultural data sources

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| Sentinel-2 cloud cover prevents timely imagery | Use cloud-masking and nearest cloud-free composite. For demo, pre-select fields with good satellite coverage. Weather alerts don't depend on satellite. |
| n8n self-hosted stability on EC2 | Use a properly sized EC2 instance with monitoring. n8n is battle-tested. For demo, have all workflows pre-tested and cached. |
| WhatsApp Business API requires Meta approval | Use Twilio WhatsApp Sandbox for hackathon (instant setup, works for testing). Fall back to SMS via Twilio if needed. |
| Alert fatigue: too many notifications annoy farmers | Threshold tuning: only alert when anomaly exceeds baseline by >2 standard deviations. Start conservative, let farmer adjust sensitivity. |
| Satellite data processing is complex and slow | Pre-process demo fields. Use AWS Lambda with rasterio for parallelized field processing. Focus on 3-5 fields for demo, not scale. |

## 12. Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 8 | "PagerDuty for farms" is a fresh paradigm. n8n as agricultural orchestration layer is creative. Push-based vs pull-based agriculture is a strong insight. |
| Feasibility | 8 | n8n dramatically reduces backend complexity. Team has n8n skills. Weather pipeline is straightforward. Satellite pipeline is the main risk. |
| Demo Impact | 9 | Live WhatsApp alert on stage is memorable. Showing n8n visual workflows is unique — judges see the entire system architecture at a glance. |
| Real-World Value | 9 | Solves a real gap in precision agriculture. Clear business model. Aligned with EU Digital Agriculture strategy. CLAAS judges will see integration potential. |
| Technical Depth | 8 | Satellite processing, multi-source data fusion, AI-powered alert generation, n8n orchestration, WhatsApp integration. Leverages team's n8n expertise. |
| **Average** | **8.4** | |
