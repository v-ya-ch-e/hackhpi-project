# Idea 007: SoilSentinel

## 1. Name

**SoilSentinel** — Real-Time Soil Carbon Monitoring for Regenerative Farms

## 2. One-Line Pitch

A web platform that combines satellite spectral analysis with ground-truth soil data to give farmers a live "soil health score" and show them exactly how much carbon their land is sequestering — making regenerative agriculture financially legible.

## 3. Problem Statement

Regenerative agriculture (no-till, cover cropping, composting) can sequester 0.5–3 tons of CO2 per hectare per year, but farmers can't prove it. Soil carbon measurement currently requires expensive lab tests ($30–100/sample), takes weeks, and provides only a point-in-time snapshot. Without verifiable measurement, farmers can't access carbon credit markets (worth $30–50/ton), and regenerative practices remain an act of faith rather than a financial decision. Meanwhile, voluntary carbon markets are plagued by credibility problems because verification is so difficult.

## 4. Solution

SoilSentinel is a web dashboard where farmers register their fields (draw on a map or upload parcel boundaries). The system then:

1. Pulls Sentinel-2 multispectral satellite imagery for each field on a rolling 5-day basis
2. Computes spectral indices correlated with soil organic carbon (SOC): bare-soil composite analysis, NDVI time-series for biomass estimation, and moisture-adjusted reflectance
3. Feeds these into a calibrated ML model (trained on publicly available soil sample databases) to estimate SOC at 10m resolution
4. Displays a time-series "Soil Carbon Dashboard" showing estimated sequestration over months/years
5. Generates a preliminary "Carbon Sequestration Report" suitable for carbon credit pre-qualification

Farmers see their soil health improving in near-real-time, get actionable recommendations ("Your field 3 has declining organic matter — consider cover cropping this winter"), and can share verified reports with carbon credit registries or buyers.

## 5. Key Insight

Satellite spectral data already contains strong signals for soil organic carbon — researchers have demonstrated R² > 0.7 for SOC estimation from Sentinel-2 in bare-soil conditions. But this research sits in academic papers, not in farmer-facing tools. The gap isn't science; it's **productization**. By packaging satellite-derived SOC estimation into a simple dashboard with financial context (carbon credit value), we turn an academic capability into a farmer decision tool.

## 6. Tech Stack

- **Frontend:** Next.js with Mapbox GL for interactive field maps and Recharts for time-series visualization
- **Backend:** FastAPI on AWS ECS (containerized)
- **ML Model:** Scikit-learn / XGBoost model for SOC estimation, trained on LUCAS Soil Database
- **Satellite data:** Sentinel-2 L2A via AWS Open Data (free, no auth)
- **Geospatial processing:** Rasterio + GeoPandas for image analysis
- **Database:** PostgreSQL with PostGIS extension on AWS RDS
- **Storage:** S3 for satellite image tiles and generated reports

## 7. Data Strategy

- **Satellite imagery:** Copernicus Sentinel-2 L2A (atmospherically corrected) via AWS Open Data Registry — free, global, 10m resolution, 5-day revisit
- **Soil training data:** EU LUCAS Topsoil Database (>20,000 georeferenced soil samples with lab-measured SOC across Europe) — free download from ESDAC
- **Weather data:** Open-Meteo API for precipitation/temperature (affects spectral interpretation)
- **Field boundaries for demo:** Use publicly available LPIS (Land Parcel Identification System) data for Germany, or manually draw 5-10 demo fields near Potsdam
- **Carbon credit pricing:** Verified Carbon Standard public registry data for current carbon credit prices

## 8. 23-Hour Plan

| Hour | Task |
|------|------|
| 0-1 | Architecture design, repo setup, Docker configs |
| 1-3 | Satellite data pipeline: fetch Sentinel-2 tiles for demo fields, compute spectral indices |
| 3-5 | SOC estimation model: train XGBoost on LUCAS data, evaluate, serialize |
| 5-7 | Backend API: field registration, satellite data retrieval, SOC prediction endpoints |
| 7-9 | Frontend: map view with field drawing tool (Mapbox GL Draw), field list |
| 9-11 | Dashboard: SOC time-series charts, soil health score computation, color-coded field map |
| 11-13 | Carbon credit calculator: integrate carbon pricing, generate PDF report |
| 13-15 | Recommendations engine: rule-based advice based on SOC trends and field conditions |
| 15-16 | **Feature freeze.** End-to-end testing with demo fields |
| 16-18 | UI polish: animations, responsive design, loading states |
| 18-20 | Demo data: pre-compute 12 months of SOC estimates for 5 demo fields to show temporal trends |
| 20-22 | Pitch deck, demo scripting, rehearsal |
| 22-23 | Final bug fixes, deploy to AWS |

**If behind:** Cut PDF report generation (show dashboard only). Cut recommendations engine (show data without advice). Pre-compute all satellite analysis instead of running in real-time.

## 9. Demo Script

| Time | Beat |
|------|------|
| 0:00-0:15 | **Hook:** "The soil beneath your feet holds 3x more carbon than the atmosphere. Farmers could sequester billions of tons — if only they could measure it." |
| 0:15-0:45 | **Problem:** Show the current process: a farmer sends soil samples to a lab, waits 3 weeks, pays €80, gets a single number for a single point. Then show a regenerative farmer who can't sell carbon credits because they can't prove sequestration. |
| 0:45-2:15 | **Demo:** Open the dashboard. Show a map of 5 fields near Potsdam. Click a field — show the 12-month SOC trend rising after the farmer switched to cover cropping. Show the soil health score: 7.2/10, up from 5.8. Click "Carbon Report" — show estimated sequestration: 2.1 tons CO2/ha, worth €63/ha at current market rates. Show the color-coded map: green fields are sequestering, yellow are stable, red are losing carbon. |
| 2:15-2:45 | **Impact:** "If every farmer in the EU used SoilSentinel, we could verify and incentivize the sequestration of 300 million tons of CO2 per year — that's 7% of EU emissions, locked in the ground by the people who know the land best." |
| 2:45-3:00 | **Close:** "SoilSentinel — because you can't manage what you can't measure." Show the live URL. |

## 10. "Build Something People Want" Test

**Who would use this?**
- Regenerative farmers wanting to prove their practices work and access carbon credit revenue
- Carbon credit registries (Verra, Gold Standard) needing scalable MRV (Measurement, Reporting, Verification)
- Agricultural lenders/insurers wanting soil health data for risk assessment
- Policy makers tracking soil health at regional scale

**Path from demo to product:**
1. Partner with a regenerative farming network (e.g., Soil Heroes, Indigo Ag) for pilot with 50 farms
2. Validate satellite-derived SOC against lab tests (build calibration dataset)
3. Seek MRV methodology approval from a carbon credit registry
4. Monetize via SaaS ($5/ha/year) or take a % of carbon credit revenue generated

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| **SOC estimation accuracy from satellite alone** | Frame as "estimation" not "measurement"; show confidence intervals; position as pre-screening tool that reduces lab testing by 80% |
| **Cloud cover blocking satellite imagery** | Use temporal compositing (best-pixel from rolling 30-day window); have pre-computed demo data as backup |
| **LUCAS training data limited to Europe** | Fine for hackathon demo (fields near Potsdam); mention transfer learning approach for global expansion |
| **Complex geospatial pipeline in 23h** | Pre-download and cache all satellite tiles; focus on 5 demo fields, not real-time global |
| **"Just a dashboard" perception** | Emphasize the ML model and carbon credit financial layer — this isn't visualization, it's decision intelligence |

## 12. Win Score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 8 | Novel productization of academic soil spectroscopy research |
| Feasibility | 7 | Geospatial pipeline is complex but all data is free and team has Python/data skills |
| Demo Impact | 9 | Interactive map + carbon revenue numbers = compelling visual story |
| Real-World Value | 9 | Directly enables carbon credit access for farmers |
| Technical Depth | 9 | ML model training + satellite processing + geospatial web app |
| **Average** | **8.4** | |
