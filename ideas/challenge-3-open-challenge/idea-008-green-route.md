# Idea 008: GreenRoute

## 1. Name

**GreenRoute** — Carbon-Optimized Food Supply Chain Planner

## 2. One-Line Pitch

A logistics optimization platform that shows food distributors the true carbon cost of every route and supplier combination — then finds the path that cuts emissions without cutting profits.

## 3. Problem Statement

Food supply chains account for 18% of global greenhouse gas emissions, yet logistics managers optimize purely for cost and speed — carbon is invisible in their decision-making. A tomato shipped by truck from Spain to Berlin might be cheaper than one grown in a Brandenburg greenhouse, but its carbon footprint is 5x higher. Existing carbon accounting tools work retroactively (annual reports), not at the point of decision. Distributors want to reduce emissions but won't accept tools that raise costs. The market needs a solution that finds routes and suppliers where sustainability and profitability overlap.

## 4. Solution

GreenRoute is a web application for food distributors and procurement managers. The user inputs:
- Their warehouse/store locations
- A list of products they need to source
- Available suppliers with locations and product catalogs

GreenRoute then:
1. Calculates the carbon footprint of every possible route-supplier combination using GLEC Framework emission factors (transport mode, distance, vehicle type, refrigeration)
2. Pulls real-time fuel prices and toll data to compute cost
3. Runs a multi-objective optimization (Pareto frontier) to find combinations that minimize both cost and emissions
4. Presents results as an interactive scatter plot: every dot is a supply chain configuration, axes are cost vs. carbon, and the Pareto frontier highlights the best trade-offs
5. Lets the user set a "carbon budget" and see the cheapest routes within that constraint, or set a cost cap and see the greenest routes within budget

The core experience is: **see the trade-off, pick your point, export the plan**.

## 5. Key Insight

Most supply chain carbon tools treat sustainability as a reporting obligation ("here's your annual footprint"). GreenRoute treats it as a **planning variable** — equal in status to cost and time. The Pareto frontier visualization is the breakthrough UX: it makes the abstract trade-off between money and planet viscerally concrete. Often, the model finds "free wins" — routes that are both cheaper and greener because the current route is simply suboptimal.

## 6. Tech Stack

- **Frontend:** Next.js with D3.js for the Pareto scatter plot, Mapbox GL for route visualization
- **Backend:** FastAPI on AWS Lambda
- **Optimization:** Python scipy.optimize or pymoo (multi-objective evolutionary algorithm)
- **Emission factors:** GLEC Framework v3 (Global Logistics Emissions Council) — published factors by transport mode, distance, and cargo type
- **Routing:** OpenRouteService API (free, supports truck routing with distance/duration)
- **Database:** PostgreSQL on AWS RDS
- **Export:** PDF route plans + CSV data export

## 7. Data Strategy

- **Emission factors:** GLEC Framework v3 (open methodology, published conversion factors for truck/rail/ship/air by region)
- **Route distances:** OpenRouteService API — free, truck-specific routing, returns distance and estimated duration
- **Fuel prices:** EU Weekly Oil Bulletin (European Commission, public) or synthetic data for demo
- **Supplier/warehouse locations for demo:** Realistic fictional dataset of 20 suppliers across Europe and 5 distribution centers in Germany
- **Product carbon intensity:** Poore & Nemecek (2018) dataset from Science — lifecycle emissions of 40+ food products, freely available
- **For the demo:** Pre-built dataset of a German supermarket chain sourcing 10 products from 20 European suppliers

## 8. 23-Hour Plan

| Hour | Task |
|------|------|
| 0-1 | Architecture design, demo dataset creation (suppliers, products, warehouses) |
| 1-3 | Carbon calculation engine: implement GLEC emission factors, route distance → CO2 |
| 3-5 | Route optimization: multi-objective optimizer (cost vs. carbon), Pareto frontier computation |
| 5-7 | Backend API: endpoints for supplier input, route calculation, optimization results |
| 7-9 | Frontend: input forms for suppliers/warehouses, map with supplier pins |
| 9-11 | Pareto visualization: interactive D3.js scatter plot with hover details, click to select |
| 11-13 | Route map: selected configuration renders as Mapbox route with carbon annotations per leg |
| 13-14 | Carbon budget slider: constrain optimization by max CO2 or max cost, update results live |
| 14-15 | **Feature freeze.** End-to-end testing |
| 15-17 | Export: PDF route plan with carbon breakdown, comparison table (current vs. optimized) |
| 17-19 | UI polish: responsive design, animations on Pareto plot, color-coded routes |
| 19-21 | Demo prep: script the story of "Berliner Supermarkt GmbH" reducing emissions by 30% |
| 21-23 | Pitch deck, rehearsal, final deploy |

**If behind:** Cut PDF export (show results on screen only). Simplify to single-objective optimization (minimize carbon subject to cost ≤ 110% of cheapest). Pre-compute optimization results.

## 9. Demo Script

| Time | Beat |
|------|------|
| 0:00-0:15 | **Hook:** "Your supermarket tomato traveled 2,400km to get here. What if the route was wrong — not just for the planet, but for the bottom line?" |
| 0:15-0:45 | **Problem:** Show a typical distributor's routing spreadsheet. All decisions are cost-only. Show the hidden carbon: a single delivery route emits more CO2 than 50 trees absorb in a year. Regulations are coming (EU CSRD) — companies will need to report and reduce. |
| 0:45-2:15 | **Demo:** Meet "Berliner Supermarkt GmbH" sourcing 10 products. Show the map with 20 suppliers lit up across Europe. Click "Optimize." The Pareto frontier appears — 200 possible configurations, scattered across cost vs. carbon. Highlight the current route (red dot: high cost, high carbon). Show the Pareto-optimal alternative (green dot: 12% cheaper AND 30% less carbon). Click it — watch the routes redraw on the map. Show the "free wins": switching from Spanish tomatoes via truck to Dutch greenhouse tomatoes via rail saves €400/week and 2 tons CO2/month. |
| 2:15-2:45 | **Impact:** "EU food logistics emit 100M tons CO2/year. If every distributor used GreenRoute to find their Pareto frontier, we could cut 15-20% of that without increasing costs. That's the equivalent of taking 6 million cars off the road." |
| 2:45-3:00 | **Close:** "GreenRoute — because the greenest route might also be the cheapest one." |

## 10. "Build Something People Want" Test

**Who would use this?**
- Mid-size food distributors (100-1000 employees) facing EU CSRD reporting requirements starting 2026
- Supermarket chains with sustainability pledges (Edeka, REWE, Lidl)
- Logistics companies offering "green logistics" as a premium service

**Path from demo to product:**
1. Partner with a regional food distributor for a pilot (replace one planning cycle with GreenRoute)
2. Validate emission calculations against their actual fuel consumption data
3. Add real-time pricing feeds and integrate with their ERP/TMS systems
4. Monetize via SaaS: €500-2000/month per distribution center, positioned as both a cost saver and compliance tool

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| **Emission factor accuracy** | Use GLEC Framework (industry standard); clearly label as estimates; show confidence ranges |
| **Optimization too slow for live demo** | Pre-compute for demo dataset; limit to 20 suppliers × 5 warehouses × 10 products (tractable for pymoo) |
| **"Just a routing tool" perception** | Emphasize the Pareto frontier UX and multi-objective optimization — this is decision intelligence, not Google Maps |
| **No real distributor data** | Build realistic synthetic dataset; mention that real integration requires ERP/TMS connection |
| **Complex frontend with map + chart** | Prioritize the Pareto plot (the "wow" moment); map can be simplified to straight-line routes if Mapbox integration takes too long |

## 12. Win Score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 8 | Pareto frontier for logistics carbon is fresh; multi-objective optimization applied to food supply chains |
| Feasibility | 8 | Clearly scoped, synthetic data eliminates data dependency, team has React + Python skills |
| Demo Impact | 9 | Pareto plot is visually striking; "free wins" discovery is a strong demo moment |
| Real-World Value | 8 | EU CSRD makes this immediately relevant; clear business model |
| Technical Depth | 8 | Multi-objective optimization + geospatial routing + interactive visualization |
| **Average** | **8.2** | |
