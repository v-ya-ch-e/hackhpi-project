# Idea 006: AgriCopilot

## 1. Name

**AgriCopilot** — The AI Farm Advisor That Speaks Every Language

## 2. One-Line Pitch

An LLM-powered WhatsApp/Telegram bot that gives smallholder farmers real-time, hyper-local agronomic advice in their native language — turning a $3 phone into a precision agriculture tool.

## 3. Problem Statement

Smallholder farmers make up 80% of the world's farms and produce a third of global food, yet they are almost entirely excluded from modern agri-tech. Existing precision agriculture tools require expensive hardware, reliable broadband, and technical literacy. Extension services — the human advisors governments send to farms — are overstretched: in sub-Saharan Africa the ratio is one agent per 4,000 farmers. The result: farmers mistime planting, over-apply fertilizer (wasting money and polluting waterways), and lose 20-40% of crops to preventable disease because advice arrives too late or not at all.

## 4. Solution

AgriCopilot is a conversational AI agent accessible via WhatsApp or Telegram (no app install needed). A farmer sends a text or voice message — "My maize leaves are turning yellow, what do I do?" — and gets back a diagnosis and actionable steps within seconds, in their own language. Under the hood, the system:

1. Accepts text or voice (transcribed via AWS Transcribe)
2. Geocodes the farmer's location (from their phone or a one-time registration)
3. Pulls real-time weather, soil, and satellite NDVI data for their area
4. Passes the enriched context to an LLM (Claude via AWS Bedrock) with a curated agronomic knowledge base
5. Returns a concise, jargon-free response with optional images (e.g., disease identification reference photos)

The core user flow is: **ask a question → get expert advice → act on it today**. No downloads, no accounts, no literacy barriers (voice-in, voice-out via AWS Polly).

## 5. Key Insight

The bottleneck for smallholder agriculture isn't data — open satellite imagery, weather APIs, and agronomic research are abundant. The bottleneck is **the last mile of interpretation**: translating global data into a specific, actionable recommendation for a specific farmer on a specific day. LLMs are uniquely good at this translation task, and messaging apps are the one digital platform smallholders already use daily.

## 6. Tech Stack

- **Frontend:** WhatsApp Business API (or Telegram Bot API) — zero-install, works on 2G
- **Backend:** FastAPI on AWS Lambda (serverless, scales to zero)
- **AI:** AWS Bedrock (Claude) for reasoning; AWS Transcribe + Polly for voice I/O
- **Data layer:** Open-Meteo API (weather), SoilGrids API (soil properties), Sentinel-2 NDVI via AWS Earth (crop health)
- **Database:** DynamoDB for farmer profiles and conversation history
- **Orchestration:** n8n for the intake pipeline (message → enrich → LLM → respond)

## 7. Data Strategy

All data is freely available:
- **Weather:** Open-Meteo API — no key required, global coverage, hourly forecasts
- **Soil:** ISRIC SoilGrids — 250m resolution soil property maps via REST API
- **Satellite imagery:** Copernicus Sentinel-2 via AWS Open Data — 10m resolution NDVI, 5-day revisit
- **Agronomic knowledge:** FAO crop guides, CABI Plantwise disease database (open access subset), and curated prompt context
- **For demo:** Pre-seeded farmer profiles for 3 locations (Germany, Kenya, India) with realistic conversation histories

## 8. 23-Hour Plan

| Hour | Task |
|------|------|
| 0-1 | Architecture finalization, repo setup, environment config |
| 1-3 | FastAPI skeleton + Telegram bot integration (WhatsApp needs approval; fall back to Telegram for demo) |
| 3-5 | Weather + soil API integrations, geocoding pipeline |
| 5-7 | LLM prompt engineering: agronomic system prompt, few-shot examples, guardrails |
| 7-9 | Voice pipeline: Transcribe → LLM → Polly round-trip |
| 9-10 | NDVI satellite data pull + integration into context window |
| 10-12 | n8n orchestration pipeline, DynamoDB farmer profiles |
| 12-14 | End-to-end testing, edge cases (bad input, unknown crop, off-topic) |
| 14-16 | **Feature freeze.** Polish: response formatting, error handling, typing indicators |
| 16-18 | Demo preparation: 3 scripted farmer personas, pre-loaded conversation flows |
| 18-20 | Pitch deck, demo video recording (backup), narrative scripting |
| 20-23 | Final rehearsal, bug fixes, deploy to production URL |

**If behind:** Cut satellite NDVI integration (use static soil data only). Cut voice I/O (text-only). These are impressive add-ons but the core text-chat advisory works without them.

## 9. Demo Script

| Time | Beat |
|------|------|
| 0:00-0:15 | **Hook:** "4 billion people farm for a living. Most of them have a phone. Almost none have an agronomist." (Show a photo of a farmer with a phone in a field.) |
| 0:15-0:45 | **Problem:** Walk through the extension-agent gap. One advisor per 4,000 farmers. Show the consequence: a farmer in Kenya loses 30% of her harvest to late blight she didn't recognize in time. |
| 0:45-2:15 | **Demo:** Live on a phone in front of the judges. Send a voice message in Swahili: "My tomato leaves have dark spots." Watch the bot respond in Swahili with a diagnosis (early blight), treatment steps, and a weather warning ("rain expected Thursday — apply fungicide before then"). Then show the same flow in German for a Brandenburg farmer. Show the satellite health map overlay. |
| 2:15-2:45 | **Impact:** "If this saves even 10% of crop loss for smallholders, that's $50B in food preserved annually. And it costs $0 to the farmer — just a text message." |
| 2:45-3:00 | **Close:** "AgriCopilot — an agronomist in every pocket." Show QR code to try it live. |

## 10. "Build Something People Want" Test

**Who would use this?** Smallholder farmers (2B+ globally), agricultural NGOs (CGIAR, FAO field programs), and ag-input companies wanting a distribution channel for advice-led sales.

**Path from demo to real product:**
1. Partner with one agricultural NGO (e.g., One Acre Fund) for a pilot with 500 farmers in one region
2. Validate that advice quality matches or exceeds human extension agents (A/B test)
3. Monetize via B2B: ag-input companies pay to be recommended ("Apply Fungicide X" → affiliate model), or NGOs/governments pay per-farmer-per-year subscription
4. Unit economics are favorable: LLM inference costs ~$0.01/conversation, farmer lifetime value to an ag-input company is $50+/year

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| **LLM hallucination giving bad farming advice** | Constrained prompting with curated knowledge base; confidence scoring; "I'm not sure — contact your local extension office" fallback |
| **WhatsApp Business API approval takes too long** | Use Telegram for demo (instant bot creation); mention WhatsApp as production path |
| **Voice transcription quality for low-resource languages** | Focus demo on Swahili + German (good Transcribe support); mention expansion plan |
| **Satellite data pipeline too complex for 23h** | Pre-cache NDVI tiles for demo locations; show the integration working for 3 sites rather than real-time global |
| **"Just a chatbot" perception** | Emphasize the data fusion layer (weather + soil + satellite + conversation history) that makes it fundamentally different from ChatGPT |

## 12. Win Score

| Criterion | Score (1-10) | Notes |
|-----------|:---:|-------|
| Innovation | 7 | LLM + messaging isn't novel alone, but the multilingual voice + geospatial data fusion layer is |
| Feasibility | 9 | All APIs are free and well-documented; team has FastAPI + AI integration skills |
| Demo Impact | 9 | Live phone demo in multiple languages is visceral and memorable |
| Real-World Value | 10 | Addresses the single largest gap in global agriculture |
| Technical Depth | 7 | Multi-API orchestration + voice pipeline + LLM reasoning chain |
| **Average** | **8.4** | |
