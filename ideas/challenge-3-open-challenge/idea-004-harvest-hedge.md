# Idea 004: HarvestHedge

## One-line pitch

A real-time agricultural commodity trading simulator that teaches farmers to hedge their crop risk -- using live market data and AI-driven strategy suggestions.

## Problem statement

European farmers lose an average of 15-20% of their potential revenue due to poor market timing -- selling at harvest when prices are lowest, or failing to lock in favorable futures prices months earlier. Commodity hedging tools exist, but they're built for professional traders, not farmers. The interfaces are intimidating, the terminology is opaque, and making a mistake can be catastrophic. Meanwhile, climate volatility is making yields less predictable, which makes price hedging *more* important than ever. Agricultural cooperatives try to fill this gap, but their advice is generic and often conflicted (they profit from the farmer's sales volume, not the farmer's margin).

## Solution

HarvestHedge is a gamified commodity trading simulator + AI advisor designed specifically for farmers. It works in two modes:

1. **Simulator mode** -- Users play through historical seasons with real price data. They "grow" a virtual wheat/corn/rapeseed crop and must decide when to sell, whether to use futures contracts, and how to hedge against price drops. The simulator shows outcomes: "If you had locked in a forward contract in March, you would have earned €4,200 more." Leaderboard tracks performance across players.

2. **Advisor mode** -- Connected to live commodity price feeds, the AI analyzes current market conditions and the farmer's specific situation (crop type, expected yield, costs, risk tolerance) to suggest hedging strategies in plain language: "Wheat futures for December are at €238/t, which is above your break-even of €195/t. Consider locking in 60% of your expected harvest now."

The core insight is that farmers will learn hedging through a game faster than through a textbook, and once they understand it, the advisor mode helps them apply it.

## Key insight

**Optiver** (present at the hackathon) specializes in market-making and quantitative trading. This idea is perfectly aligned with their expertise -- they can mentor on real-time data feeds, hedging strategies, and risk management models. This mentor alignment is a strategic advantage: teams that leverage sponsor expertise get better guidance and more favorable judging. Additionally, the "teach via simulation" approach avoids the regulatory minefield of giving actual financial advice.

## Tech stack

- **Frontend:** Next.js (React) -- trading dashboard, charts, simulator UI
- **Backend:** FastAPI (Python) -- simulation engine, strategy calculation, market data processing
- **Market data:** Euronext commodity prices via free delayed feed or Yahoo Finance API (wheat, corn, rapeseed futures)
- **AI:** AWS Bedrock (Claude) for strategy explanation in plain language, scenario analysis
- **Charts:** TradingView Lightweight Charts (open-source) or Recharts for price visualization
- **Real-time:** WebSocket connection for live price updates (in advisor mode)
- **Database:** DynamoDB for user portfolios, simulation state, leaderboard
- **Hosting:** AWS (Amplify + Lambda)

## Data strategy

- **Historical prices:** Yahoo Finance API or Investing.com for historical Euronext milling wheat (EBMF), corn, and rapeseed futures. Free, well-documented.
- **Live prices:** Yahoo Finance delayed quotes (15min delay, free) or Alpha Vantage commodity API.
- **Weather-price correlation:** Open-Meteo historical weather for European growing regions, used by the AI to explain price movements ("Wheat prices rose 8% this week due to drought forecasts in France").
- **Farm economics:** Thünen Institute farm income data (German, public) for realistic cost/break-even inputs.
- **Simulation scenarios:** Pre-built from historical data: 2022 Ukraine crisis, 2018 European drought, 2023 price crash. Each scenario includes price data + context.

## 23-hour plan

| Hours | Task |
|-------|------|
| 0-2 | Data pipeline: Download 5 years of daily commodity prices. Design simulation engine data model. |
| 2-4 | Backend: Simulation engine -- given a scenario (date range + crop), track portfolio as user makes buy/sell/hedge decisions. Calculate P&L. |
| 4-6 | Frontend: Trading dashboard -- price chart, portfolio panel, decision buttons (sell now, hedge 50%, wait). |
| 6-8 | Simulator mode: Complete game loop. Pick scenario → make decisions → see outcome. Historical replay with interactive decision points. |
| 8-10 | AI advisor: LLM analyzes current position + market conditions → plain-language strategy suggestions. |
| 10-12 | Live market data integration: Connect to price feed. Advisor mode with real current prices. |
| 12-14 | Gamification: Leaderboard, score calculation (risk-adjusted return), scenario difficulty ratings. |
| 14-16 | UX polish: Tooltips explaining financial concepts, farmer-friendly terminology, mobile responsiveness. |
| 16-18 | Demo preparation: Run through 3 demo scenarios. Script the "aha moment" in each. |
| 18-20 | Buffer + feature freeze. Final polish, demo rehearsal. |

**If behind:** Cut live market data and gamification. Core: simulator with 1-2 historical scenarios + AI explanations.

**If ahead:** Add multiplayer mode (farmers compete in same scenario), weather overlay on price charts, or integration with Cula data (carbon credit pricing as a hedging opportunity).

## Demo script

**Hook (15s):** "Last harvest, German wheat farmers left €2 billion on the table. Not because of bad crops -- because of bad timing."

**Problem (30s):** Show the wheat price chart for 2022-2023: prices spiked 60% post-Ukraine, then crashed. "Farmers who sold at harvest got €195/t. Farmers who hedged in March got €290/t. Same wheat. Same field. €95 per ton difference. But hedging tools look like this --" *show a Bloomberg terminal* "-- and farmers aren't traders."

**Solution demo (90s):** Launch HarvestHedge. Start the 2022 Ukraine scenario: "You have 100 hectares of wheat. It's February 2022." Show the price chart. News flash: "Russia invades Ukraine." Prices spike. AI advisor says: "This is an unusual event. Consider locking in a forward contract for 60% of your expected yield at current prices." Click "Hedge 60%." Fast-forward to harvest -- prices have crashed. Show the P&L: "Because you hedged, you earned €47,000 more than if you had waited." Switch to advisor mode with today's live prices. Show a current recommendation.

**Impact (30s):** "We're not replacing financial advisors. We're teaching farmers to make informed decisions. Every farmer who understands hedging is a farmer who survives the next price crash."

**Close (15s):** "HarvestHedge: learn to hedge, or lose your edge." *Acknowledge Optiver:* "Built with guidance from the trading experts at Optiver."

## "Build something people want" test

**Who would use this?** Young/next-generation farmers taking over family operations. Agricultural cooperatives for member education. Agricultural schools and universities. Farmers who sell directly to commodity markets (not just cooperatives).

**Path from demo to product:** Partner with agricultural cooperatives for distribution. The simulator is free (educational). The live advisor becomes a premium feature. Partner with commodity brokers who want to onboard farmer clients. Long-term: integrate with actual brokerage APIs for one-click hedging.

## Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| Commodity data APIs have unreliable free tiers | Pre-download historical data. For demo, hard-code live prices as fallback. |
| Financial simulation correctness matters | Keep the model simple: spot price vs. forward contracts only. No options, no margin calls. Validate against known outcomes. |
| Judges may not understand commodity hedging | The 2022 Ukraine scenario is universally understood. Lead with the €95/t gap. |
| Regulatory concerns about "financial advice" | Explicitly frame as educational simulator, not advisory. "This is a teaching tool, not a recommendation." |
| Scope creep into full trading platform | Hard scope: simulator + simple hedging. No options trading, no real money. |

## Win score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 8 | Gamified hedging education for farmers is genuinely new |
| Feasibility | 8 | Simulation engine is well-scoped; commodity data is accessible |
| Demo Impact | 9 | The 2022 Ukraine scenario with real price data is a killer demo |
| Real-World Value | 7 | High value but niche -- most farmers use cooperatives |
| Technical Depth | 7 | Financial simulation + real-time data + LLM is solid |
| **Average** | **7.8** | |
