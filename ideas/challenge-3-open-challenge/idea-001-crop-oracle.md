# Idea 001: CropOracle

## One-line pitch

An AI advisor that tells smallholder farmers exactly what to plant, when, and how -- in their own language -- by fusing hyperlocal weather, soil, and market price data.

## Problem statement

Smallholder farmers -- 80% of the world's farms -- make planting decisions based on tradition and guesswork. A single wrong crop choice can cost a family an entire season's income. Climate volatility has made historical knowledge unreliable: rainfall patterns shift, heat spikes destroy established varieties, and pest ranges expand. Existing agri-tech tools target large commercial farms with expensive sensor networks, leaving small farmers in the dark. The information gap costs billions in preventable crop failures annually.

## Solution

CropOracle is a conversational AI assistant (web app, mobile-friendly) where a farmer describes their plot (location, size, soil type, water access) and CropOracle returns a personalized seasonal plan: recommended crops ranked by expected profit and climate resilience, planting windows, intercropping suggestions, and risk warnings. The core user flow is:

1. Farmer opens the app, enters location (or shares GPS) and basic plot details via chat
2. CropOracle pulls hyperlocal weather forecasts (Open-Meteo), soil maps (ISRIC SoilGrids), and crop price trends (FAO/local market APIs)
3. An LLM synthesizes the data into plain-language, actionable advice tailored to the farmer's context
4. The farmer receives a visual seasonal calendar with crop recommendations, expected yields, and climate risk scores
5. Follow-up questions are handled conversationally ("What if I only have half the water?" "What about adding beans?")

## Key insight

The non-obvious insight is that the *data* for precision agriculture already exists publicly (open weather APIs, open soil databases, satellite indices, commodity prices) -- what's missing is a **synthesis layer** that makes it actionable for someone without a data science degree. LLMs are that synthesis layer. You don't need sensors in the ground; you need intelligence in the cloud.

## Tech stack

- **Frontend:** Next.js (React) -- responsive web app, mobile-first design
- **Backend:** FastAPI (Python) -- API orchestration, data fusion pipeline
- **AI:** AWS Bedrock (Claude) for conversational reasoning and plan generation
- **Data APIs:** Open-Meteo (weather forecasts + historical), ISRIC SoilGrids (soil properties), FAO FPMA (food prices), NASA POWER (solar/climate data)
- **Database:** DynamoDB for user sessions and saved plans
- **Hosting:** AWS (Lambda + API Gateway for backend, Amplify for frontend)
- **Maps:** Mapbox or Leaflet for location input and visualization

## Data strategy

All data is from free, public APIs -- no scraping or proprietary sources needed:
- **Weather:** Open-Meteo API (no key required) -- 16-day forecast + 30-year historical climate normals
- **Soil:** ISRIC SoilGrids REST API -- global soil property maps at 250m resolution
- **Crop data:** FAO crop calendars and yield statistics (static dataset, bundled)
- **Market prices:** FAO Food Price Monitoring & Analysis (FPMA) API or World Bank commodity prices
- **Satellite vegetation:** NASA MODIS NDVI via AppEEARS API (stretch goal for crop health overlay)

## 23-hour plan

| Hours | Task |
|-------|------|
| 0-1 | Architecture & API exploration. Confirm data APIs work. Set up monorepo. |
| 1-3 | Backend: FastAPI endpoints -- location-to-weather, location-to-soil, crop recommendation engine (rule-based + LLM) |
| 3-5 | Frontend: Next.js scaffold, chat interface, location input (GPS + map picker) |
| 5-7 | Integration: Wire frontend to backend. First end-to-end flow working (location -> recommendation) |
| 7-9 | LLM integration: Prompt engineering for crop advisory synthesis. Conversational follow-ups. |
| 9-11 | Visual seasonal calendar component. Crop cards with risk scores. |
| 11-13 | Polish data pipeline: add market prices, improve soil data display. Multi-language support via LLM. |
| 13-15 | UX polish: loading states, error handling, mobile responsiveness. |
| 15-17 | Demo preparation: pick 2-3 compelling demo scenarios (e.g., farmer in Kenya, farmer in Brandenburg). Seed realistic data. |
| 17-19 | Buffer: fix bugs, improve visual design, add one "wow" feature (satellite map overlay or voice input) |
| 19-20 | Feature freeze. Final polish, demo rehearsal. |

**If behind:** Cut multi-language and satellite overlay. Core flow (location -> chat -> calendar) is the MVP.

**If ahead:** Add voice input (AWS Transcribe), comparison mode ("show me wheat vs. sorghum"), or PDF export of the seasonal plan.

## Demo script

**Hook (15s):** "Imagine you're a farmer in rural Kenya. The rains are shifting, your usual maize variety failed last year, and the closest agricultural extension officer is 50km away. What do you plant this season?"

**Problem (30s):** Show statistics: 500M smallholder farms, $10B annual losses from climate-driven crop failure, zero access to precision ag tools. Show a typical farmer's decision: gut feeling + tradition. Show how climate change has broken those heuristics.

**Solution demo (90s):** Open CropOracle. Drop a pin on a farm in Kenya. Watch it pull real weather data, real soil data. Chat: "I have 2 hectares, clay soil, rain-fed only." CropOracle returns a seasonal calendar: drought-resistant sorghum + intercropped cowpeas, with planting windows and expected yield. Ask a follow-up: "What if I can irrigate half my plot?" -- watch the recommendation adapt in real-time. Switch to a farm in Brandenburg, Germany -- show it works globally. Show the recommendation in German.

**Impact (30s):** "Every data source we use is free and public. This could run on a $20 phone. The marginal cost to serve one more farmer is near zero. CropOracle turns open data into food security."

**Close (15s):** "We built CropOracle in 23 hours. Imagine what it could do with a full growing season of feedback data."

## "Build something people want" test

**Who would use this?** Agricultural NGOs and extension services working with smallholder farmers. They currently produce generic seasonal advisories -- CropOracle makes them hyperlocal and interactive. Individual tech-savvy farmers in emerging markets with smartphone access.

**Path from demo to product:** Partner with an agricultural extension service (e.g., One Acre Fund) for a pilot. Add user feedback loops (did the recommendation work?). Monetize via B2B (sell to NGOs, governments, agribusinesses) or keep free and grant-funded. Long-term: integrate actual sensor data from users who have it.

## Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| API rate limits during demo | Cache responses for demo locations; pre-fetch data for 3 demo scenarios |
| LLM hallucinating crop advice | Constrain LLM with structured data context; rule-based validation layer checks recommendations against FAO crop calendars |
| Soil/weather data resolution too coarse | Be transparent about resolution in UI; frame as "guidance" not "prescription" |
| Scope creep into full farm management | Hard scope: advisory only. No IoT, no marketplace, no social features. |
| Demo not visually impressive | Invest hours 15-17 in visual polish; the calendar + map combo is inherently visual |

## Win score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 7 | LLM-as-synthesis-layer for open data is fresh; crop advisory itself is not new |
| Feasibility | 9 | All APIs are free and well-documented; team has full-stack + FastAPI skills |
| Demo Impact | 8 | Switching between a Kenyan and German farm is a strong demo moment |
| Real-World Value | 9 | Addresses a $10B problem affecting 500M farms |
| Technical Depth | 7 | Multi-API data fusion + LLM orchestration is solid but not bleeding-edge |
| **Average** | **8.0** | |
