# Idea 009: MethaneWatch

## 1. Name

**MethaneWatch** — Satellite-Powered Livestock Methane Monitoring Dashboard

## 2. One-Line Pitch

A real-time dashboard that tracks methane emissions from livestock farms using satellite data and ML, giving farmers and regulators the first affordable, continuous emissions monitoring system for the €30B problem of agricultural methane.

## 3. Problem Statement

Agriculture produces 40% of global methane emissions, overwhelmingly from livestock (enteric fermentation and manure). Methane is 80x more potent than CO2 over 20 years, making it the single highest-leverage greenhouse gas to reduce. Yet livestock methane is essentially unmonitored — farmers don't know their emissions, regulators rely on crude herd-count estimates, and the EU's incoming methane regulation (2024 regulation, enforcement ramping in 2026) has no scalable measurement infrastructure. Current measurement options are impractical: chamber methods cost €10,000+ per measurement, drones require per-visit scheduling, and on-animal sensors are invasive. There is no Google Analytics for methane.

## 4. Solution

MethaneWatch aggregates satellite methane observations (from ESA's Sentinel-5P TROPOMI instrument and NASA's EMIT) with farm-level data to produce a per-farm methane emission estimate dashboard. The system:

1. Ingests Sentinel-5P methane column data (daily global coverage at 7km resolution)
2. Cross-references with farm locations, herd sizes, and regional livestock density data
3. Applies a statistical downscaling model to attribute regional methane enhancements to individual farm clusters
4. Displays a map-based dashboard showing emissions trends, regional hotspots, and farm-level estimates
5. Provides automated alerts when emissions spike (potential manure management issues, ventilation failures)
6. Generates regulatory compliance reports aligned with EU methane regulation requirements

Farmers use it to identify emission-reduction opportunities (feed additives, manure management changes) and track the impact over time. Regulators use it for regional monitoring and enforcement prioritization.

## 5. Key Insight

Sentinel-5P TROPOMI has been mapping global methane since 2018, but this data sits in scientific archives, not farmer dashboards. Individual satellite pixels are too coarse for a single farm — but combined with statistical methods and farm density data, you can produce meaningful estimates for farm clusters and track trends over time. The key is that **relative change detection** (is this farm getting better or worse?) is much easier than absolute measurement, and relative change is what farmers and regulators actually need for decision-making.

## 6. Tech Stack

- **Frontend:** Next.js with Mapbox GL for heatmaps and farm markers, Recharts for time-series
- **Backend:** FastAPI on AWS ECS
- **Satellite data processing:** Python (xarray, rioxarray) for Sentinel-5P netCDF files
- **ML/Statistics:** Scikit-learn for downscaling model (random forest regression), spatial statistics
- **Database:** PostgreSQL + PostGIS on AWS RDS for geospatial queries
- **Storage:** S3 for satellite data cache
- **Alerts:** AWS SNS for email/SMS alerts on emission spikes

## 7. Data Strategy

- **Methane satellite data:** Sentinel-5P TROPOMI Level 2 CH4 product — free from Copernicus Open Access Hub or AWS Open Data, daily global coverage
- **Farm locations:** OpenStreetMap farm polygons (freely available) + Eurostat farm statistics by NUTS region
- **Livestock density:** FAO Gridded Livestock of the World (GLW3) — free, 1km resolution global livestock density maps
- **Weather (for atmospheric correction):** Open-Meteo API — wind speed and direction affect methane plume dispersion
- **For demo:** Focus on the Niedersachsen region (Germany's highest livestock density), pre-download 6 months of Sentinel-5P data, show 10 demo farms with generated herd data

## 8. 23-Hour Plan

| Hour | Task |
|------|------|
| 0-1 | Architecture design, identify demo region (Niedersachsen), download Sentinel-5P data |
| 1-3 | Satellite data pipeline: parse TROPOMI netCDF, extract CH4 columns, create time-series for demo region |
| 3-5 | Farm data layer: pull OSM farms, merge with livestock density, create farm cluster geometries |
| 5-7 | Downscaling model: train random forest to estimate farm-cluster methane from satellite + livestock + weather features |
| 7-9 | Backend API: endpoints for map data, farm details, time-series, regional aggregates |
| 9-11 | Frontend map: Mapbox heatmap of methane concentration, farm cluster markers with click-to-detail |
| 11-13 | Dashboard views: individual farm time-series, regional ranking, trend indicators (up/down arrows) |
| 13-15 | Alert system: threshold-based spike detection, notification demo |
| 15-16 | **Feature freeze.** Compliance report generation (simple PDF with emission summary) |
| 16-18 | UI polish: color gradients for emission levels, responsive layout, loading states |
| 18-20 | Pre-compute 6 months of demo data to show convincing temporal trends |
| 20-22 | Pitch deck, demo scripting, rehearsal |
| 22-23 | Final deploy and bug fixes |

**If behind:** Cut alerts and compliance reports (show map + dashboard only). Use pre-computed data exclusively instead of real-time pipeline. Simplify downscaling to linear interpolation.

## 9. Demo Script

| Time | Beat |
|------|------|
| 0:00-0:15 | **Hook:** "Methane is 80 times more potent than CO2. Agriculture is the #1 source. And right now, nobody is watching." (Show a satellite image of methane plumes over farmland.) |
| 0:15-0:45 | **Problem:** The EU's new methane regulation requires monitoring — but the only tools available cost €10,000 per measurement or require per-farm drone visits. For 10 million livestock farms in Europe, this is impossible with current methods. |
| 0:45-2:15 | **Demo:** Open the dashboard. Show a heatmap of Niedersachsen — Germany's livestock heartland. Zoom in to see farm clusters. Click on "Hof Schmidt" — show 6-month methane trend. Point out a spike in November ("manure spreading season"). Show the regional ranking: farms performing better and worse than average. Click "What changed?" — the farm switched to a methane-reducing feed additive in January and the trend shows a 15% reduction. Show the compliance report button. |
| 2:15-2:45 | **Impact:** "There are 10 million livestock farms in the EU. MethaneWatch can monitor all of them continuously, for the cost of zero additional hardware. If we can help reduce livestock methane by just 10%, that's equivalent to taking 50 million cars off the road." |
| 2:45-3:00 | **Close:** "MethaneWatch — making the invisible visible, from space." |

## 10. "Build Something People Want" Test

**Who would use this?**
- EU livestock farmers needing to demonstrate methane reduction for regulatory compliance (EU Methane Regulation 2024)
- Agricultural ministries and environmental agencies for regional monitoring
- Feed additive companies wanting to prove their products reduce methane (3NOP, seaweed supplements)
- Carbon credit verifiers needing continuous methane monitoring data

**Path from demo to product:**
1. Academic validation: partner with a livestock research institute that has ground-truth chamber measurements
2. Pilot with one German Landkreis (county) agricultural office for regional monitoring
3. Integrate with Cula Technologies' carbon data infrastructure for carbon credit verification
4. Monetize via government contracts (monitoring) and farmer SaaS (€10/farm/month for compliance dashboard)

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| **Sentinel-5P resolution too coarse for single farms** | Frame as "farm cluster" monitoring; emphasize trend detection over absolute measurement; mention upcoming MethaneSAT (100m resolution) as enhancement path |
| **Downscaling model accuracy** | Show confidence intervals; validate against published regional emission inventories; position as "screening tool" not "certified measurement" |
| **Satellite data processing is slow/complex** | Pre-download and cache all data for demo region; process offline and serve pre-computed results |
| **"Just a heatmap" perception** | The farm-level drill-down, trend detection, and compliance report make it actionable, not just pretty |
| **EU methane regulation may not require farm-level monitoring** | Even without regulation, feed additive companies and carbon markets need this data |

## 12. Win Score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 9 | Novel application of satellite methane data to farm-level monitoring; timely with EU regulation |
| Feasibility | 6 | Satellite data processing is complex; downscaling accuracy is genuinely uncertain |
| Demo Impact | 8 | Heatmap + drill-down + trend detection is visually compelling |
| Real-World Value | 9 | Addresses a massive regulatory and climate gap with no existing affordable solution |
| Technical Depth | 9 | Satellite data processing + ML downscaling + geospatial web app + alert system |
| **Average** | **8.2** | |
