# Idea 002: FoodPrint

## One-line pitch

Scan any grocery receipt and instantly see the carbon footprint, water usage, and deforestation risk of your shopping basket -- with swap suggestions that cut your impact without changing your diet.

## Problem statement

Consumers increasingly want to eat sustainably but have no idea which choices actually matter. Is organic avocado from Mexico better or worse than conventional from Spain? Does oat milk really beat dairy? The data exists in academic life-cycle assessments (LCAs), but it's buried in 200-page PDFs. Existing carbon tracking apps require manual food logging, which no one maintains past week two. Meanwhile, the food system contributes 26% of global greenhouse gas emissions, and consumer demand is the most powerful lever for shifting agricultural practices. The gap between intention and action is an information problem.

## Solution

FoodPrint is a web app where users photograph or upload a grocery receipt. OCR extracts the line items, an LLM maps each item to a food category, and the app looks up environmental impact data (carbon footprint, water use, land use, deforestation risk) from Poore & Nemecek's dataset (the largest food LCA meta-analysis). Users see:

1. A **receipt breakdown** -- each item color-coded by impact (green/yellow/red) with specific CO2eq, liters of water, and m2 of land
2. A **basket score** -- overall environmental rating compared to an average shopping trip
3. **Smart swaps** -- "Replace beef mince with turkey mince: save 14kg CO2, same protein, €0.50 cheaper" -- powered by LLM reasoning over the nutritional + impact data
4. A **trends dashboard** -- track your impact over time as you scan more receipts

## Key insight

The non-obvious insight is that **receipt scanning removes the friction that kills every food tracking app**. You already have the receipt. One photo replaces 15 minutes of manual logging. And receipts provide *purchase-level* data (actual quantities, actual brands, actual prices), which is far richer than food diaries. The second insight: impact varies 50x between the best and worst producers of the same food -- showing the *range* (not just the average) is what motivates behavior change.

## Tech stack

- **Frontend:** Next.js (React) -- receipt upload, dashboard, swap cards
- **Backend:** FastAPI (Python) -- OCR pipeline, food matching, impact calculation
- **OCR:** AWS Textract (receipt-optimized OCR) or Tesseract as fallback
- **AI:** AWS Bedrock (Claude) for receipt item → food category mapping and swap generation
- **Data:** Poore & Nemecek 2018 dataset (CC-licensed, ~40 food products × 5 impact indicators × range data), supplemented by Our World in Data food impact dataset
- **Database:** DynamoDB for user history and receipt storage
- **Hosting:** AWS (Amplify + Lambda)

## Data strategy

- **Environmental impact:** Poore & Nemecek 2018 meta-analysis (published in Science, data freely available). Covers carbon footprint, water use, land use, eutrophication, and acidification for ~40 food categories. Our World in Data has cleaned and extended this dataset.
- **Food matching:** LLM maps receipt line items (e.g., "Bio Hackfleisch 500g") to food categories. Pre-built mapping table for common items; LLM handles edge cases.
- **Nutritional data:** Open Food Facts API (free, open-source) for matching specific products to nutritional profiles for swap suggestions.
- **Receipt OCR:** AWS Textract handles structured receipt data natively.

## 23-hour plan

| Hours | Task |
|-------|------|
| 0-1 | Architecture. Test Textract on sample receipts. Load impact dataset into Python. |
| 1-3 | Backend: Receipt OCR pipeline (upload → Textract → structured items). Food category matching with LLM. |
| 3-5 | Backend: Impact calculation engine. Given food items + quantities, compute total footprint. |
| 5-7 | Frontend: Receipt upload UI, receipt breakdown display (color-coded items, impact bars). |
| 7-9 | Integration: End-to-end flow. Upload photo → see breakdown. |
| 9-11 | Smart swaps engine: LLM generates nutritionally equivalent, lower-impact alternatives with price comparison. |
| 11-13 | Basket score + comparison to average. Visual dashboard (charts, impact rings). |
| 13-15 | Polish: Improve OCR accuracy, handle edge cases, add German receipt support. |
| 15-17 | Trends dashboard (multi-receipt history). User accounts (optional). |
| 17-19 | Demo polish: prepare 3 receipt demos (high-impact, low-impact, "surprise" receipt). Visual refinement. |
| 19-20 | Feature freeze. Demo rehearsal. |

**If behind:** Cut trends dashboard and user accounts. Core: receipt → breakdown → swaps.

**If ahead:** Add barcode scanner (Open Food Facts lookup), social sharing ("my basket score this week"), or real-time price comparison for swaps.

## Demo script

**Hook (15s):** *Hold up a grocery receipt.* "This piece of paper tells you what you spent. But it doesn't tell you what it *cost* -- the planet."

**Problem (30s):** "Food is 26% of global emissions, but when you stand in a supermarket, you have zero information about impact. Organic? Local? Vegan? The actual data is counterintuitive and buried in academic papers."

**Solution demo (90s):** Photograph a real German grocery receipt live. Watch Textract extract items. Show the breakdown: the beef mince lights up red (14kg CO2), the tomatoes are green (0.4kg), the cheese is orange (6kg). Show the basket score: "Your shopping was 2.3x the average footprint." Click "Show swaps" -- "Replace beef mince with chicken thigh: save 11kg CO2, similar protein, €2 cheaper." Show a second receipt that's mostly plant-based -- the contrast is dramatic. Show the range visualization: "Not all beef is equal -- the best 10% of producers emit 5x less than the worst."

**Impact (30s):** "If 10% of German households used FoodPrint for one month, we'd prevent 2 million tons of CO2. And we didn't ask anyone to go vegan -- just to make informed swaps."

**Close (15s):** "The receipt is the interface. The data is open. The impact is real. FoodPrint."

## "Build something people want" test

**Who would use this?** Environmentally conscious grocery shoppers (large and growing demographic in Germany/EU). People who feel overwhelmed by conflicting sustainability advice. Health-conscious consumers who also care about impact.

**Path from demo to product:** Launch as a free web app. Partner with a German supermarket chain (REWE, Edeka) for receipt format optimization. Monetize via B2B (supermarkets use it as a sustainability feature in their apps) or affiliate links on swap suggestions. Long-term: integrate directly with digital receipts (Germany is moving toward e-receipts).

## Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| OCR fails on crumpled/blurry receipts | Use Textract (receipt-optimized); provide manual correction UI; pre-photograph clean receipts for demo |
| Food matching is ambiguous ("Bio Joghurt Natur 3.5%") | LLM mapping with confidence scores; fallback to generic category; show "best guess" to user |
| Impact data too coarse (40 categories for thousands of products) | Be transparent: show category-level averages with ranges. Frame as "estimate" not "exact." |
| Users don't return after first scan | Gamify with weekly challenges, basket score streaks. (Stretch goal, not MVP.) |
| German receipts have abbreviated product names | Build a German grocery abbreviation dictionary; LLM handles most cases |

## Win score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 8 | Receipt-as-interface is a novel UX insight; existing apps all require manual input |
| Feasibility | 8 | Textract + LLM + static dataset = straightforward pipeline |
| Demo Impact | 9 | Live receipt scan is visceral and relatable; everyone buys groceries |
| Real-World Value | 8 | Empowers consumer behavior change at the highest-leverage point |
| Technical Depth | 7 | OCR + LLM + data pipeline is solid; not deeply novel technically |
| **Average** | **8.0** | |
