# Idea 005: WasteNot

## One-line pitch

An AI-powered food waste marketplace that connects restaurants and grocery stores with surplus food to nearby consumers and food banks in real-time -- with dynamic pricing that drops to zero before food hits the bin.

## Problem statement

Germany alone wastes 11 million tons of food annually -- €25 billion in value. Supermarkets discard products days before expiration due to cosmetic standards and demand forecasting errors. Restaurants throw away 20-30% of prepared food nightly. Existing solutions (Too Good To Go) rely on pre-packaged "surprise bags" with no transparency, limited inventory, and fixed pricing. Food banks operate on unpredictable donation schedules with no real-time visibility into what's available. The core failure: food waste is a *logistics and information* problem -- surplus food exists at the same time that hunger exists, just in different places with no connecting infrastructure.

## Solution

WasteNot is a real-time marketplace web app with three user types:

1. **Suppliers** (restaurants, supermarkets, bakeries) list surplus items via a simple dashboard: item name, quantity, best-before date, photo. An LLM auto-categorizes and suggests pricing. Prices automatically decrease as expiration approaches, hitting €0 (donation) at T-4 hours.

2. **Consumers** see a map of nearby surplus food with live availability, countdown timers, and current prices. They reserve items and pick up within a time window. Gamification: "rescue score" tracks their environmental impact.

3. **Food banks & charities** get priority access to items hitting the donation threshold. Automated routing suggests which food bank should pick up which surplus based on proximity, capacity, and need.

The AI layer handles three things: demand prediction (what's likely to be surplus today based on weather, day of week, events), dynamic pricing optimization (maximizing recovery rate while ensuring everything moves), and logistics routing for food bank pickups.

## Key insight

The key insight is **dynamic pricing with a guaranteed floor of zero**. Too Good To Go uses fixed "surprise bag" pricing, which means some food still gets wasted because the price isn't right. WasteNot's Dutch-auction model guarantees 100% clearance: if no one buys at €3, the price drops to €1, then to €0 and a food bank gets notified. The "waste nothing" guarantee is both the product promise and the marketing hook. The second insight: **QuantCo** (present as mentors) specializes in pricing optimization and causal inference -- this is their exact domain expertise.

## Tech stack

- **Frontend:** Next.js (React) -- consumer marketplace (map, listings, cart), supplier dashboard, food bank portal
- **Backend:** FastAPI (Python) -- listing management, dynamic pricing engine, matching algorithm
- **AI:** AWS Bedrock (Claude) for item categorization, demand prediction, and food bank routing
- **Maps:** Mapbox GL for consumer-facing map with live listings
- **Real-time:** WebSockets (FastAPI) for live price updates and availability changes
- **Database:** DynamoDB for listings (TTL for auto-expiry), user profiles, transaction history
- **Notifications:** AWS SNS for food bank alerts when donation threshold is reached
- **Hosting:** AWS (Amplify + Lambda + API Gateway)

## Data strategy

- **Seed data:** Generate realistic surplus listings for the demo using real restaurant menus and supermarket product catalogs from OpenFoodFacts. Model surplus patterns based on published food waste studies (WWF Germany, BMEL Baseline 2025).
- **Pricing model:** Rule-based to start: price = base_price × (hours_until_expiry / shelf_life_hours)^0.5. LLM refines based on item category, time of day, and local demand.
- **Demand prediction:** Day-of-week + weather + local events → expected surplus. Train on synthetic data for demo; designed to incorporate real transaction data post-launch.
- **Food bank data:** Tafel Deutschland (German food bank network) has 970 locations -- geocode from their public directory for demo routing.
- **Geolocation:** Browser geolocation API + Mapbox geocoding.

## 23-hour plan

| Hours | Task |
|-------|------|
| 0-2 | Architecture. Data models. Seed data generation (50 realistic listings across 10 fictional Berlin suppliers). |
| 2-4 | Backend: CRUD for listings. Dynamic pricing engine (auto-decrement based on time-to-expiry). |
| 4-6 | Frontend: Consumer view -- map with pins, listing cards, reserve flow. |
| 6-8 | Frontend: Supplier dashboard -- add listing (photo + details), view active listings, see reservation stats. |
| 8-10 | Integration: End-to-end flow. Supplier adds → appears on map → consumer reserves → supplier confirms. |
| 10-12 | Dynamic pricing visualization: countdown timer + live price on each listing. Price drop animation. |
| 12-14 | Food bank layer: Auto-notify when listing hits €0. Simple routing (nearest food bank with capacity). |
| 14-16 | AI features: LLM auto-categorization of items, demand-based pricing adjustment, "rescue score" gamification. |
| 16-18 | Polish: Impact dashboard ("X kg saved, Y kg CO2 prevented, Z meals provided"), mobile responsiveness. |
| 18-20 | Demo preparation: Simulate a "day in the life" -- morning bakery surplus, lunch restaurant leftovers, evening supermarket markdowns. Feature freeze + rehearsal. |

**If behind:** Cut food bank routing and demand prediction. Core: supplier listing → map → consumer reservation → dynamic pricing.

**If ahead:** Add push notifications, social sharing ("I rescued 5kg of food this week!"), or restaurant inventory prediction based on weather/events.

## Demo script

**Hook (15s):** *Show a photo of a full restaurant dumpster.* "This was thrown away last night at a restaurant 200 meters from a food bank. Not because nobody wanted it -- because nobody *knew* it was there."

**Problem (30s):** "Germany wastes 11 million tons of food per year. That's €25 billion. But this isn't an abundance problem -- it's an information problem. Surplus food and hungry people exist at the same time, in the same city, with no connection."

**Solution demo (90s):** Open the supplier dashboard. A bakery adds "12 Brötchen, best before 6pm, €0.50 each." Watch it appear on the consumer map in real time. Show the price ticker: it's 2pm now, price is €0.50. Fast-forward: at 4pm, price drops to €0.20. A consumer reserves 6. At 5:30pm, the remaining 6 hit €0 -- a food bank notification fires. Show the food bank portal: "6 Brötchen available at Bäckerei Schmidt, 800m from your location. Pick up before 6pm." Show the impact dashboard: "Today in Berlin: 340kg of food rescued. 850kg CO2 prevented. 200 meals provided."

**Impact (30s):** "Too Good To Go does surprise bags. We do real-time, transparent, guaranteed zero-waste. Every item has a price. And every price goes to zero before it goes to the trash."

**Close (15s):** "WasteNot. Because the best place for surplus food is someone's plate, not a landfill."

## "Build something people want" test

**Who would use this?** Budget-conscious consumers (students, young professionals) looking for deals on food. Environmentally motivated shoppers. Food banks seeking reliable, real-time supply. Restaurants and bakeries looking to recover revenue from surplus (even at reduced prices, €0.50 > €0).

**Path from demo to product:** Launch in one Berlin neighborhood with 10-15 partner businesses. The Too Good To Go model proves demand exists. Differentiate with transparency (see exactly what you're getting) and the zero-waste guarantee. Monetize via small commission on consumer transactions (15%) + premium analytics for suppliers (demand forecasting, waste reduction insights). Long-term: integrate with POS systems for automatic listing creation.

## Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| Chicken-and-egg: need suppliers AND consumers | Demo with seeded data. Real launch: start with suppliers (revenue recovery motivation) in one neighborhood. |
| Food safety concerns | Clear disclaimers, best-before (not use-by) items only, pickup windows enforce freshness |
| Too similar to Too Good To Go | Differentiate on: transparency (no surprise bags), dynamic pricing, food bank integration, zero-waste guarantee |
| Real-time features are complex | WebSocket updates are a stretch goal; polling every 30s is sufficient for MVP |
| Demo requires believable data | Invest hour 0-2 in realistic seed data. Use real Berlin locations and restaurant names (fictionalized). |

## Win score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 7 | Dynamic pricing + food bank routing is novel; marketplace itself is familiar territory |
| Feasibility | 8 | CRUD marketplace is well-understood; dynamic pricing is a timer + formula |
| Demo Impact | 8 | Real-time price drops + food bank notification is a strong visual moment |
| Real-World Value | 9 | 11M tons of food waste in Germany alone; clear problem, clear solution |
| Technical Depth | 7 | Real-time system + pricing algorithm + LLM + routing |
| **Average** | **7.8** | |
