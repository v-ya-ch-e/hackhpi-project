# Idea 009: DirtRich

## 1. Name

**DirtRich**

## 2. One-line Pitch

A marketplace where farmers sell verified soil carbon credits directly to companies, using satellite data and AI to measure carbon sequestration without expensive manual soil sampling.

## 3. Problem Statement

Agriculture is responsible for 23% of global greenhouse gas emissions, but it could also be a massive carbon sink — healthy soils store more carbon than the atmosphere and all vegetation combined. Regenerative farming practices (cover crops, no-till, composting) sequester 0.5-1.5 tonnes of CO2 per hectare per year. Yet fewer than 1% of farmers participate in carbon credit markets. Why? (1) Soil carbon measurement requires expensive physical sampling ($20-50/hectare), (2) carbon credit platforms have 2-3 year verification timelines, (3) minimum participation thresholds exclude small farms, and (4) middlemen take 30-60% of the credit value. Farmers who are already doing the right thing can't get paid for it.

## 4. Solution

DirtRich is a web platform that democratizes soil carbon credits by replacing manual soil sampling with AI-powered remote sensing estimation. Farmers register their fields (drawn on a map or uploaded as shapefiles). The system pulls:

- **Satellite imagery** (Sentinel-2, free from ESA) — NDVI, soil moisture, crop cover time series
- **Weather history** (OpenMeteo) — rainfall, temperature patterns affecting decomposition
- **Soil baseline data** (SoilGrids) — initial organic carbon content, texture, depth
- **Farm practice data** (self-reported by farmer) — tillage practices, cover crops, inputs

An ML model estimates soil organic carbon change over the growing season. Farmers whose practices are sequestering carbon earn credits that are listed on the DirtRich marketplace, where companies can purchase them for verified offsetting. Each credit links to a specific field, specific satellite imagery, and a transparent methodology score.

**Core user flow (farmer):** Register → draw field boundaries → answer practice survey → see estimated carbon sequestration → list credits.
**Core user flow (buyer):** Browse credits by region/practice/price → see satellite proof + methodology → purchase → receive retirement certificate.

## 5. Key Insight

The carbon credit market's bottleneck isn't demand — companies are desperate to buy credits. The bottleneck is supply-side friction: measurement, verification, and aggregation costs eat the economics for small farms. **The insight: satellite remote sensing + AI has reached the point where soil carbon change can be estimated at scale without physical sampling, at 80-90% of the accuracy, at 1% of the cost.** By accepting a slightly lower precision in exchange for 100x lower cost and instant results, DirtRich can unlock the 99% of farmers currently excluded from carbon markets. This mirrors how fintech disrupted banking — not by being more accurate, but by being more accessible.

## 6. Tech Stack

- **Frontend:** Next.js (React) with Mapbox GL JS for field boundary mapping
- **Backend:** FastAPI (Python) on AWS ECS
- **Satellite Data:** Sentinel-2 via Copernicus Data Space (free), processed with rasterio/GDAL
- **Soil Model:** Scikit-learn or XGBoost for soil carbon estimation (NDVI time series + weather + soil baseline → carbon change estimate)
- **Soil Baseline:** ISRIC SoilGrids REST API (free, 250m resolution)
- **Weather:** OpenMeteo API (free, historical + forecast)
- **Maps:** Mapbox GL JS (free tier sufficient)
- **Database:** PostgreSQL on AWS RDS (PostGIS extension for spatial queries)
- **Storage:** AWS S3 for satellite imagery cache
- **Hosting:** AWS ECS (backend) + Amplify (frontend)

## 7. Data Strategy

- **Sentinel-2 satellite imagery:** Free, 10m resolution, 5-day revisit time via Copernicus Data Space API. Use Band 4 (Red), Band 8 (NIR) for NDVI calculation. Time series over growing season shows vegetation dynamics correlated with soil carbon inputs.
- **SoilGrids baseline:** ISRIC provides global soil organic carbon stock estimates at 250m resolution via free REST API. This is the baseline from which changes are estimated.
- **Weather history:** OpenMeteo historical API — temperature and precipitation drive decomposition rates, critical inputs for carbon balance models.
- **Farm practice data:** Self-reported via structured survey (tillage type, cover crop species, residue management, manure/compost application). Validated against satellite imagery where possible (cover crop presence detectable from NDVI).
- **Carbon model training data:** Use published datasets from soil carbon measurement studies (ISRIC WoSIS database — 200,000+ soil profiles). For MVP, use a simplified model based on IPCC Tier 1 methodology enhanced with satellite inputs.
- **Marketplace data:** Simulated buyers for demo. Real credit pricing based on current voluntary carbon market rates ($30-80/tonne).

## 8. 23-Hour Plan

| Hours | Phase | Tasks |
|-------|-------|-------|
| 0-1 | **Setup** | Scaffold Next.js + FastAPI, provision AWS services, register Copernicus API access |
| 1-3 | **Field Registration** | Mapbox-based field drawing tool, store boundaries in PostGIS, reverse geocoding for location |
| 3-5 | **Satellite Pipeline** | Build Sentinel-2 data fetcher: given field boundary → fetch cloud-free images → compute NDVI time series |
| 5-7 | **Carbon Estimation Model** | Build soil carbon change estimator: NDVI time series + SoilGrids baseline + weather → estimated tonnes CO2 sequestered. Use IPCC Tier 1 methodology with satellite adjustment. |
| 7-9 | **Farmer Dashboard** | Practice survey, field overview with satellite imagery, carbon estimation results, credit listing |
| 9-11 | **Marketplace** | Credit listing page, buyer browse/filter, field detail view with satellite proof, purchase flow (simulated) |
| 11-12 | **Transparency Layer** | Methodology explanation page, confidence scoring, comparison to manual sampling |
| 12-14 | **Sleep/Break** | |
| 14-16 | **Visualization** | Beautiful satellite imagery viewer with NDVI overlay, carbon sequestration timeline chart, regional heatmap |
| 16-18 | **Demo Data** | Prepare 5-10 compelling demo fields with real satellite data, diverse geographies and practices |
| 18-20 | **Integration Testing** | Full flow testing, edge cases, performance optimization |
| 20-22 | **Pitch Prep** | Build pitch narrative around "unlocking the 99%", prepare live demo flow |
| 22-23 | **Final Polish** | Deploy, final bug fixes |

**Cut if behind:** Drop marketplace buyer side (show mockup), drop methodology page, reduce to 2-3 demo fields, simplify carbon model to pure NDVI correlation.

**Feature freeze:** Hour 18.

## 9. Demo Script

| Time | Section | Content |
|------|---------|---------|
| 0:00-0:15 | **Hook** | "The soil under our feet stores more carbon than the atmosphere and all plants combined. Farmers can store even more — but nobody's paying them for it." |
| 0:15-0:45 | **Problem** | 99% of farmers excluded from carbon markets. Manual soil sampling costs $20-50/hectare. Verification takes years. Middlemen take 30-60%. Show the irony: the farmers doing the most for climate (regenerative practices) are the ones who can't access the money. |
| 0:45-2:15 | **Demo** | Live: draw a field boundary on the map → watch satellite imagery load → NDVI time series appears showing cover crop growth → carbon model estimates 1.2 tonnes CO2/hectare sequestered → "List credits" → switch to marketplace view → buyer browses credits, clicks on our field → sees satellite proof (before/after NDVI), practice details, methodology score of 87% → purchases 10 tonnes at $45/tonne → farmer dashboard shows $540 earned. Then zoom out: show regional view with dozens of fields, total carbon sequestered, and a heatmap of regenerative farming activity. |
| 2:15-2:45 | **Impact** | "If just 10% of German farmland joined DirtRich, that's 1.2 million hectares generating 600,000-1.8M tonnes of CO2 credits annually — worth $27-81M going directly to farmers. We make soil the world's biggest carbon sink AND farmers the biggest beneficiaries." |
| 2:45-3:00 | **Close** | "We're not asking farmers to change what they grow. We're paying them for what the soil already does. DirtRich: where dirt meets dollars." |

## 10. "Build Something People Want" Test

**Who uses this after the hackathon?** Regenerative farmers wanting to monetize their practices (there are 500M+ globally). Climate-forward companies needing high-quality carbon offsets (Microsoft, Stripe, Shopify all have carbon removal purchasing programs). Agricultural cooperatives wanting to aggregate and sell credits collectively.

**Path from demo to product:**
1. Pilot with 50-100 farms in one region, compare satellite estimates to physical samples for validation
2. Achieve certification from a carbon standard body (Gold Standard, Verra) for the remote sensing methodology
3. Integrate with existing carbon registries for credit retirement
4. Revenue: 10-15% commission on credit sales (vs. 30-60% from incumbents like Indigo Ag or Nori)
5. Expand to other ecosystem services: biodiversity credits, water quality credits, using the same satellite infrastructure
6. Long-term: become the "Stripe for soil carbon" — API-first platform that any carbon marketplace can build on

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| Satellite-based carbon estimation is less accurate than physical sampling | Be transparent: show confidence intervals, compare to IPCC Tier 1 baseline. Frame as "screening tool" not "replacement for MRV." 80% accuracy at 1% cost is the value proposition. |
| Copernicus API might be slow for real-time demo | Pre-fetch and cache satellite imagery for demo fields in S3. Only fetch live for "wow" factor if API is responsive. |
| Carbon model is oversimplified for 23 hours | Use IPCC Tier 1 methodology as baseline (well-established), enhance with NDVI correlation. Honest about limitations in methodology page. |
| Self-reported farm practice data is unreliable | Cross-validate with satellite: cover crops are visible in NDVI. Tillage detectable from surface reflectance. Future: integrate with CLAAS machine data for automatic practice verification. |
| Carbon credit market credibility concerns | Don't claim to be a certified carbon standard. Position as "carbon credit origination platform" that makes it easy for farms to get started, with certification as a future step. |

## 12. Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 9 | Satellite-based soil carbon estimation for direct farmer access to carbon markets is cutting-edge. "Fintech for carbon" framing resonates. |
| Feasibility | 6 | Most ambitious technically: satellite data pipeline, spatial queries, carbon model, marketplace. Risk of rabbit holes in satellite processing. |
| Demo Impact | 8 | Satellite imagery + field mapping is visually impressive. The "farmer earns $540" moment is emotionally resonant. But lots of moving parts for live demo. |
| Real-World Value | 10 | Addresses the fundamental supply-side bottleneck in carbon markets. Potentially transformative for both climate and farmer livelihoods. Aligned with EU carbon farming initiative. |
| Technical Depth | 9 | Satellite data processing, ML for carbon estimation, spatial database, marketplace logic. Technically the deepest project. |
| **Average** | **8.4** | |
