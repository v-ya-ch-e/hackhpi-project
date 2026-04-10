# Idea 007: CarbonBite

## 1. Name

**CarbonBite**

## 2. One-line Pitch

Scan any grocery receipt and instantly see the carbon footprint of your meal — then offset it with verified biochar credits from Cula in one tap.

## 3. Problem Statement

Consumers increasingly want to eat sustainably, but have no idea which food choices actually matter for climate. Is organic avocado from Peru better or worse than conventional local cabbage? The average person can't answer this, and existing carbon trackers require tedious manual food logging. Meanwhile, the voluntary carbon credit market is booming ($2B+) but opaque — buyers don't trust abstract "tonnes of CO2" certificates. The disconnect between daily food consumption and real carbon removal creates paralysis: people want to act but the friction is too high on both sides.

## 4. Solution

CarbonBite is a web app that bridges the gap between food consumption and carbon action. The user photographs their grocery receipt (or restaurant bill). An AI pipeline (Claude Vision for OCR + structured extraction) identifies food items and quantities. Each item is matched against a comprehensive food carbon footprint database (Poore & Nemecek 2018 dataset + AGRIBALYSE) that accounts for production method, transport, and packaging.

The app shows a beautiful breakdown: total meal/basket carbon footprint, item-by-item ranking (biggest offenders highlighted), and simple swap suggestions ("switching from beef to chicken here saves 12kg CO2"). Then the magic: a one-tap "neutralize this meal" button that purchases micro-offsets through Cula's biochar carbon removal — with a live link to the actual physical biochar process (leveraging Cula's Removal Activity API to show the real sink where your carbon is being stored).

**Core user flow:** Snap receipt → see footprint breakdown → get swap suggestions → one-tap offset with real biochar credits → see your actual carbon sink.

## 5. Key Insight

Carbon offsetting fails because it's abstract. People buy "1 tonne of CO2 removal" and feel nothing. CarbonBite makes it concrete in two ways: (1) it connects your specific meal to a specific carbon number, and (2) it connects your offset to a specific biochar reactor in Germany with real sensor data. When you can say "my Tuesday dinner produced 4.2kg CO2, and it's being stored in biochar at Stefan Block's facility near Hamburg" — that's a story people tell their friends. **The insight: carbon action needs to be as frictionless and tangible as the consumption that caused it.**

## 6. Tech Stack

- **Frontend:** Next.js (React) with Tailwind CSS — beautiful data visualization
- **Backend:** FastAPI (Python) on AWS Lambda
- **AI/OCR:** AWS Bedrock (Claude Vision) for receipt parsing and food item extraction
- **Carbon Data:** Poore & Nemecek 2018 dataset (open), AGRIBALYSE v3.1 (open LCA database from ADEME)
- **Carbon Credits:** Cula Technologies Removal Activity API (available to all teams)
- **Visualization:** D3.js or Recharts for footprint breakdowns
- **Database:** AWS DynamoDB for user history and aggregated stats
- **Hosting:** AWS Amplify + API Gateway + Lambda

## 7. Data Strategy

- **Receipt OCR:** Claude Vision handles receipt parsing with high accuracy. Prompt-engineered to extract item names, quantities, and prices as structured JSON.
- **Food carbon footprint database:** Primary: Poore & Nemecek 2018 (39,000 farms, 119 countries, 40 products — the most cited food LCA study). Secondary: AGRIBALYSE v3.1 from French ADEME (2,500+ food products with full lifecycle carbon data). Both are open access.
- **Carbon credit data:** Cula's Removal Activity API provides real removal activities, sink locations, mass balances, and sensor data. We query this to show the user their specific biochar sink.
- **Swap suggestions:** Precomputed from the food carbon database — for each high-impact item, store 2-3 lower-impact alternatives with taste/nutrition similarity scores (generated via LLM at build time).
- **User data:** Anonymous receipt history for personal tracking. Aggregated (never individual) data for community impact dashboard.

## 8. 23-Hour Plan

| Hours | Phase | Tasks |
|-------|-------|-------|
| 0-1 | **Setup** | Scaffold Next.js + FastAPI, provision AWS services, get Cula API access |
| 1-3 | **Receipt Pipeline** | Build receipt photo → Claude Vision OCR → structured food item list pipeline |
| 3-5 | **Carbon Database** | Load Poore & Nemecek + AGRIBALYSE data, build food item → carbon footprint matching (fuzzy matching with LLM fallback) |
| 5-7 | **Footprint Engine** | Calculate per-item and total carbon footprint, generate swap suggestions |
| 7-9 | **Frontend V1** | Receipt capture UI, animated footprint breakdown visualization, item cards with swap suggestions |
| 9-11 | **Cula Integration** | Connect to Removal Activity API, build "offset this meal" flow, show real sink data (location, mass balance, sensor readings) |
| 11-12 | **User Journey** | Personal carbon diary (historical meals), cumulative impact counter |
| 12-14 | **Sleep/Break** | |
| 14-16 | **Visualization Polish** | Beautiful charts — stacked bar of meal carbon, comparison to daily budget, animated offset confirmation |
| 16-18 | **Demo Data** | Prepare compelling demo receipts (high-impact meal, sustainable meal, restaurant bill), ensure smooth flow |
| 18-20 | **Integration Testing** | End-to-end testing, edge cases (blurry receipts, unusual items, multilingual receipts) |
| 20-22 | **Pitch Prep** | Build pitch deck, record backup demo video, practice |
| 22-23 | **Final Polish** | Deploy, bug fixes, ensure live demo works reliably |

**Cut if behind:** Drop personal carbon diary, drop restaurant bill support, hardcode one demo receipt with perfect pipeline.

**Feature freeze:** Hour 18.

## 9. Demo Script

| Time | Section | Content |
|------|---------|---------|
| 0:00-0:15 | **Hook** | "Your grocery cart has a carbon footprint. You just can't see it — until now." [Show a grocery receipt transforming into a carbon heatmap] |
| 0:15-0:45 | **Problem** | The average European's food is responsible for 1.5 tonnes of CO2/year. People want to change but can't quantify their impact. Carbon offsets exist but feel like paying indulgences — abstract and untrustworthy. |
| 0:45-2:15 | **Demo** | Live: photograph a real grocery receipt → watch AI parse items in real-time → see beautiful carbon breakdown (the steak dominates, the lentils are almost invisible) → tap "see swaps" (chicken instead of beef saves 85%) → tap "neutralize this meal for €0.40" → see the actual Cula biochar sink in Germany where your carbon is stored, with real sensor data showing the pyrolysis reactor temperature. Then show the personal diary: "This week you ate 12kg CO2. Your best day was Wednesday." |
| 2:15-2:45 | **Impact** | "If 1% of German grocery shoppers used CarbonBite, that's 800,000 people making better food choices AND funding real carbon removal. That's tangible climate action, one meal at a time." |
| 2:45-3:00 | **Close** | "We built this in 23 hours by combining AI receipt reading, climate science data, and Cula's real carbon removal infrastructure. The future of food transparency starts with a photo." |

## 10. "Build Something People Want" Test

**Who uses this after the hackathon?** Climate-conscious consumers (growing segment — 65% of consumers in EU surveys say they want to reduce food carbon footprint but don't know how). Grocery chains wanting to differentiate on sustainability. Carbon credit platforms wanting a consumer-facing channel.

**Path from demo to product:**
1. Launch as free consumer app, build user base with social sharing ("I neutralized my meal" badges)
2. Partner with grocery chains for receipt API integration (no photo needed, automatic)
3. B2B: license the food carbon database + matching engine to meal kit companies, restaurant chains
4. Revenue: transaction fee on micro-offsets (10-15%), premium subscription for detailed analytics
5. Long-term: become the "Klarna for carbon" — embedded carbon offsetting at point of food purchase

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| Receipt OCR accuracy varies with photo quality | Add manual correction UI. For demo, use clean printed receipts. Claude Vision is strong at OCR. |
| Food item → carbon data matching is ambiguous ("organic free-range eggs" vs "eggs") | Use LLM for fuzzy matching with confidence scores. Fall back to category averages. For demo, test with items known to be in database. |
| Carbon footprint numbers vary by methodology | Use Poore & Nemecek (gold standard, peer-reviewed). Show confidence ranges. Be transparent about methodology. |
| Cula API might have limited real-time sink data | Pre-fetch and cache sink data. For demo, ensure 2-3 compelling sinks are ready to display. |
| "Micro-offsets are greenwashing" criticism | Emphasize CarbonBite is behavior change FIRST (swap suggestions), offset SECOND. Show real, verified removal with sensor data — not abstract certificates. |

## 12. Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 8 | Receipt→carbon→real offset pipeline is novel. Connecting consumer meals to specific biochar sinks is unique. |
| Feasibility | 8 | All components proven (OCR, carbon databases, APIs). Main risk is fuzzy matching quality. |
| Demo Impact | 10 | Live receipt scanning is visual magic. The "see your actual carbon sink" moment with real sensor data is a killer demo beat. Judges from Cula will love seeing their API used this way. |
| Real-World Value | 8 | Clear consumer demand, viable business model. Bridges the gap between awareness and action. |
| Technical Depth | 7 | Multi-API integration, AI-powered matching, data visualization. Solid but not groundbreaking ML. |
| **Average** | **8.2** | |
