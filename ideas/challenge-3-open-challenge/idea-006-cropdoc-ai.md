# Idea 006: CropDoc AI

## 1. Name

**CropDoc AI**

## 2. One-line Pitch

A multilingual AI plant doctor that diagnoses crop diseases from a single phone photo and prescribes treatments calibrated to the farmer's local climate, soil, and budget.

## 3. Problem Statement

Crop diseases cause $220 billion in annual losses globally, and 80% of the world's farmers — smallholders in Sub-Saharan Africa, South Asia, and Latin America — have no access to agronomists. By the time a disease is visually obvious to an untrained eye, yield loss is already 30-50%. Existing apps like Plantix require reliable internet, give generic advice, and don't account for local climate forecasts or input availability. The result: farmers either over-spray pesticides (harming soil and climate) or lose entire harvests.

## 4. Solution

CropDoc AI is a lightweight web app (works on any smartphone browser) where a farmer photographs a symptomatic leaf or plant. The image is processed by a fine-tuned vision model (AWS Bedrock / Claude vision) that identifies the disease, pest, or nutrient deficiency. The system then cross-references the diagnosis with:

- **Local weather forecast** (OpenMeteo API) — to predict disease spread risk
- **Soil type data** (SoilGrids API) — to adjust fertilizer/amendment recommendations
- **Local input market data** — to suggest affordable, locally available treatments

The response is delivered in the farmer's language (leveraging LLM multilingual capabilities) with visual icons and simple action steps. A "disease pressure map" aggregates anonymized reports to show regional outbreaks in near-real-time.

**Core user flow:** Open app → take photo → get diagnosis + treatment plan + 7-day risk forecast → share alert with neighboring farmers.

## 5. Key Insight

The breakthrough isn't disease classification (that's solved) — it's **contextualizing the prescription**. A correct diagnosis with the wrong treatment recommendation (too expensive, unavailable locally, ineffective given upcoming rain) is useless. By fusing vision AI with hyperlocal climate and soil data, CropDoc gives advice that's actually actionable. This is what extension officers do — but there's 1 for every 5,000 farmers in most developing countries.

## 6. Tech Stack

- **Frontend:** Next.js (React) PWA — works offline-first, installable on home screen
- **Backend:** FastAPI (Python) on AWS Lambda / ECS
- **AI:** AWS Bedrock (Claude vision for diagnosis + text generation for multilingual advice)
- **Weather:** OpenMeteo API (free, no key required, global coverage)
- **Soil:** ISRIC SoilGrids REST API (free, 250m resolution global soil data)
- **Geolocation:** Browser Geolocation API + reverse geocoding
- **Database:** AWS DynamoDB (disease reports for heatmap)
- **Hosting:** AWS Amplify (frontend) + API Gateway + Lambda

## 7. Data Strategy

- **Disease identification:** Use Claude's vision capabilities with few-shot prompting using reference images from PlantVillage dataset (54,000+ labeled images, open access). No fine-tuning needed for MVP — prompt engineering with disease reference library.
- **Weather data:** OpenMeteo API — free, real-time, global, no API key. Pull 7-day forecast for GPS coordinates.
- **Soil data:** ISRIC SoilGrids API — free REST API with global soil property maps (pH, organic carbon, texture, nutrients) at 250m resolution.
- **Treatment knowledge:** Curated knowledge base from FAO/CABI crop protection compendium (open access summaries), embedded in system prompts.
- **Aggregated disease reports:** User submissions stored in DynamoDB, anonymized for heatmap.

## 8. 23-Hour Plan

| Hours | Phase | Tasks |
|-------|-------|-------|
| 0-1 | **Architecture** | Set up Next.js + FastAPI scaffolding, AWS services, API keys |
| 1-3 | **Core AI Pipeline** | Build image upload → Claude Vision diagnosis → structured JSON response pipeline |
| 3-5 | **Weather + Soil Integration** | Connect OpenMeteo + SoilGrids APIs, build context enrichment layer |
| 5-7 | **Treatment Engine** | Build treatment recommendation logic: cross-reference diagnosis × weather × soil → actionable plan |
| 7-9 | **Frontend V1** | Camera capture UI, diagnosis result card, treatment steps, multilingual toggle |
| 9-10 | **Disease Pressure Map** | DynamoDB storage + simple heatmap visualization of aggregated reports |
| 10-12 | **Polish & Edge Cases** | Handle poor photos, unknown diseases, offline graceful degradation |
| 12-14 | **Sleep/Break** | |
| 14-16 | **Demo Flow** | Hardcode compelling demo path, ensure smooth camera→diagnosis→treatment flow |
| 16-18 | **Multilingual & UX** | Add language selection, icon-based UI for low-literacy users, loading states |
| 18-20 | **Integration Testing** | End-to-end testing with real plant disease photos |
| 20-22 | **Pitch Prep** | Record demo video backup, prepare slides, practice pitch |
| 22-23 | **Final Polish** | Bug fixes, deploy to production URL |

**Cut if behind:** Drop disease pressure map, drop offline mode, hardcode soil data for demo region.

**Feature freeze:** Hour 18. After that, only bug fixes and demo polish.

## 9. Demo Script

| Time | Section | Content |
|------|---------|---------|
| 0:00-0:15 | **Hook** | "Every 3 seconds, a farmer somewhere loses a day's harvest to a disease they could have treated — if only they had an agronomist in their pocket." |
| 0:15-0:45 | **Problem** | Show stats: 220B annual losses, 1 extension officer per 5,000 farmers. Show existing apps giving generic "spray fungicide" advice with no local context. |
| 0:45-2:15 | **Demo** | Live demo: photograph a diseased tomato leaf → instant diagnosis (early blight) → treatment plan that accounts for rain forecast tomorrow ("apply copper fungicide TODAY before rain, not after") → show the same diagnosis for a farmer in Kenya vs Germany (different available treatments, different languages) → zoom out to disease pressure heatmap showing outbreak spreading. |
| 2:15-2:45 | **Impact** | "If CropDoc catches a disease 5 days earlier, that's 20-30% more yield saved. For a smallholder earning $2/day, that's the difference between feeding their family or not." |
| 2:45-3:00 | **Close** | "We built this in 23 hours. Imagine what it becomes with local agronomist validation and a season of real-world data." |

## 10. "Build Something People Want" Test

**Who uses this after the hackathon?** Smallholder farmers in developing countries (2 billion+ potential users), agricultural extension organizations (FAO, CGIAR network), and NGOs working on food security.

**Path from demo to product:**
1. Partner with agricultural extension services in 2-3 countries for field validation
2. Build offline-capable model (distilled, runs on-device)
3. Add treatment sourcing — connect to local agri-input suppliers
4. Integrate with crop insurance providers (diagnosis = documentation of loss)
5. Revenue: freemium model for farmers, B2B for agri-input companies wanting distribution channel

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| Claude Vision may misdiagnose obscure diseases | Use confidence scoring; below threshold, suggest "consult local expert" + provide contact. For demo, use well-known diseases with high accuracy. |
| SoilGrids API latency | Cache soil data for demo regions. Fallback to general recommendations. |
| Multilingual quality | Focus on English + 2-3 languages for demo. LLM handles translation well for common languages. |
| "Just another plant disease app" perception | Emphasize the contextual prescription angle hard in the pitch — diagnosis is table stakes, actionable local advice is the innovation. |
| Scope creep into marketplace features | Strict feature freeze at hour 18. Marketplace is a "future vision" slide only. |

## 12. Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 7 | Disease detection isn't new, but climate+soil contextualized prescriptions are. The "extension officer in your pocket" framing is compelling. |
| Feasibility | 9 | All APIs are free and accessible. Claude Vision handles plant disease well. Core pipeline is straightforward. |
| Demo Impact | 9 | Live camera demo is visceral. Side-by-side multilingual comparison is a "wow" moment. Disease heatmap adds visual punch. |
| Real-World Value | 9 | 2B+ potential users, $220B problem. Clear path to real product. Aligned with food security and climate adaptation. |
| Technical Depth | 7 | Multi-API fusion (vision + weather + soil + LLM generation) shows integration skill, but no novel ML. |
| **Average** | **8.2** | |
