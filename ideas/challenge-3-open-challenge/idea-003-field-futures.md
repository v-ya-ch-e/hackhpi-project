# Idea 003: FieldFutures

## One-line pitch

A climate scenario simulator that shows farmers and policymakers exactly how their land will perform under 2°C, 3°C, and 4°C warming -- field by field, crop by crop, decade by decade.

## Problem statement

Farmers make 20-year investment decisions (orchards, irrigation infrastructure, land purchases) based on climate assumptions that are already wrong. A German wine grower planting Riesling today needs to know if their region will still support it in 2045. A Brandenburg grain farmer considering a €500K irrigation system needs to know if the investment makes sense under different warming scenarios. Current climate projections are published as global or regional averages, incomprehensible to anyone without climate science training. The result: farmers either ignore climate risk entirely or make fear-based decisions without data. Policy adaptation planning suffers the same information gap.

## Solution

FieldFutures is a web-based "digital twin" for agricultural land under climate change. A user selects any location (point on a map or address), and FieldFutures shows:

1. **Current agricultural profile** -- current climate normals, soil type, growing season length, suitable crops
2. **Future projections** -- side-by-side comparison under SSP2-4.5 (moderate) and SSP5-8.5 (high emissions) scenarios for 2030, 2040, 2050
3. **Crop viability shifts** -- which current crops become risky, which new crops become viable (e.g., "soy becomes viable in Brandenburg by 2040 under SSP5-8.5")
4. **Risk timeline** -- animated visualization showing growing season shifts, frost-free period expansion, drought frequency, and extreme heat days across decades
5. **Adaptation recommendations** -- AI-generated suggestions based on the projections ("Consider drought-resistant wheat varieties" or "Your region's climate in 2045 will resemble today's central France -- study what they grow")

The core interaction is: pick a location, see your future. The "your climate in 2045 = today's [other place]" analogy is the key communication device.

## Key insight

The insight is the **climate analogue** framing: instead of saying "average temperature rises 2.3°C and precipitation drops 15%," you say "your farm in Brandenburg in 2045 will have the climate of today's Burgundy, France." This makes abstract projections visceral and actionable -- the farmer can literally look at what Burgundy grows today. This technique exists in climate science papers but has never been packaged as a consumer tool for farmers.

## Tech stack

- **Frontend:** Next.js (React) -- interactive map, scenario comparison panels, animated timeline
- **Backend:** FastAPI (Python) -- climate data processing, crop viability engine
- **Climate data:** CMIP6 downscaled projections via Copernicus Climate Data Store (CDS) API -- pre-processed for key variables (temperature, precipitation, growing degree days)
- **Crop data:** FAO GAEZ (Global Agro-Ecological Zones) dataset for crop suitability thresholds
- **AI:** AWS Bedrock (Claude) for generating adaptation recommendations and climate analogue narratives
- **Visualization:** D3.js or Recharts for animated timelines, Mapbox for spatial visualization
- **Hosting:** AWS (S3 for pre-processed climate tiles, Lambda for API)

## Data strategy

- **Climate projections:** Copernicus CDS provides CMIP6 downscaled data at ~25km resolution. Pre-download and process tiles for Europe (or globally at coarser resolution) for key variables: monthly mean temperature, precipitation, frost days, growing degree days. Two scenarios: SSP2-4.5 and SSP5-8.5.
- **Current climate:** ERA5 reanalysis from Copernicus for baseline (1991-2020 normals).
- **Crop suitability:** FAO GAEZ v4 provides crop-specific climate thresholds (temperature ranges, water needs, growing season length). Static dataset, bundled.
- **Soil:** ISRIC SoilGrids for soil type overlay (same as CropOracle but used differently).
- **Climate analogues:** Pre-compute: for each grid cell in 2045, find the current grid cell with the closest climate match. Store as a lookup table.

**Data processing strategy:** Given the 23-hour constraint, pre-process climate data for a focused region (Germany + neighboring countries) at 25km resolution during the first 2 hours using a Python pipeline. Store as JSON tiles served from S3.

## 23-hour plan

| Hours | Task |
|-------|------|
| 0-2 | Data pipeline: Download CMIP6 tiles for Europe from CDS. Process into JSON: current vs. 2030/2040/2050, two scenarios. Pre-compute climate analogues. |
| 2-4 | Backend: FastAPI endpoints -- location → climate profile (current + future). Crop viability engine (input: climate vars → output: crop suitability scores). |
| 4-6 | Frontend: Map interface (Mapbox), location picker, basic results display. |
| 6-8 | Scenario comparison UI: side-by-side cards for current/2030/2040/2050. Crop viability badges (green/yellow/red). |
| 8-10 | Climate analogue feature: "Your 2045 climate = today's [location]" with map link. |
| 10-12 | Animated timeline: D3.js or Recharts visualization showing key metrics shifting across decades. |
| 12-14 | LLM-powered adaptation recommendations based on projections. |
| 14-16 | Polish: improve visualizations, add tooltips, ensure mobile responsiveness. |
| 16-18 | Demo scenarios: prepare 3 locations (Brandenburg grain farm, Rhineland vineyard, Alpine dairy farm) with compelling narratives. |
| 18-20 | Buffer + feature freeze. Final polish, demo rehearsal. |

**If behind:** Cut the animated timeline. Static scenario comparison cards + climate analogue + crop viability is the MVP.

**If ahead:** Add a "what should I plant?" recommendation engine, PDF report export, or expand to global coverage.

## Demo script

**Hook (15s):** "You're a farmer in Brandenburg. You just inherited 50 hectares. You want to plant an orchard -- a 30-year investment. But will Brandenburg's climate in 2050 even support the trees you plant today?"

**Problem (30s):** "Climate projections exist, but they look like this." *Show a CMIP6 heatmap -- incomprehensible.* "Farmers need to make generational investment decisions with zero accessible information about what their land will look like in 20 years."

**Solution demo (90s):** Click on a Brandenburg farm. Show current profile: "Today, you grow wheat, barley, rapeseed. Your growing season is 210 days." Click "2045, moderate warming": growing season extends to 240 days, summer drought risk doubles, but -- plot twist -- *soy and sunflower become viable for the first time.* Show the climate analogue: "Your farm in 2045 will feel like today's farm in central France. Here's what they grow there." Click the Rhineland vineyard: show Riesling viability declining, but Merlot becoming possible. The gasps from the audience are the demo moment.

**Impact (30s):** "Every farmer, every land-use planner, every agricultural ministry needs this. We're not predicting the future -- we're making the best available science *usable*."

**Close (15s):** "FieldFutures: see your farm's future before you plant."

## "Build something people want" test

**Who would use this?** Agricultural ministries and land-use planners (Germany, EU). Agricultural banks and insurance companies assessing long-term risk. Forward-thinking farmers making infrastructure investments. Agricultural universities for teaching.

**Path from demo to product:** Partner with a national agricultural ministry for pilot deployment. License to agricultural banks (Landwirtschaftliche Rentenbank in Germany). Build out to global coverage. Monetize via B2B SaaS for institutional users; keep individual farmer access free.

## Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| CMIP6 data download is slow / CDS API has queue | Start download immediately at hour 0; have backup: use pre-processed Our World in Data climate explorer data or WorldClim v2 (static tiles, no API queue) |
| Climate data processing exceeds 2 hours | Pre-process only Germany (not all of Europe). Reduce resolution to 50km if needed. |
| Crop suitability model is too simplistic | Be transparent: "This is based on climate thresholds from FAO, not a full agronomic model." Frame as decision-support, not prediction. |
| Judges don't find climate projections exciting | The climate analogue reveal ("your farm = today's France") is designed to be the emotional hook |
| Data resolution (25km) doesn't feel "field-level" | Use language carefully: "your region" not "your field." Interpolation makes the map feel continuous. |

## Win score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 9 | Climate analogues for farming is a novel application of existing science |
| Feasibility | 6 | Climate data pipeline is the riskiest part -- tight on the 2-hour budget |
| Demo Impact | 9 | "Your 2045 climate = today's France" is a jaw-dropper |
| Real-World Value | 9 | Addresses a genuine gap in climate adaptation planning |
| Technical Depth | 8 | Climate data processing + geospatial + LLM synthesis |
| **Average** | **8.2** | |
