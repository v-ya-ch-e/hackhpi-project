# Idea 008: AgriVoice

## 1. Name

**AgriVoice**

## 2. One-line Pitch

A voice-first AI farm advisor that farmers call like a hotline — no app, no internet, no literacy required — and get real-time, personalized agricultural guidance via a simple phone call.

## 3. Problem Statement

1.5 billion smallholder farmers worldwide make critical decisions daily — when to plant, what to spray, whether to harvest — with almost zero expert support. The ratio of agricultural extension officers to farmers ranges from 1:1,000 in best cases to 1:10,000+ in Sub-Saharan Africa. Existing agri-tech solutions assume smartphone + internet access + literacy, but 40% of farmers in developing countries use feature phones, many are functionally illiterate, and mobile data is expensive. The farmers who need help most are excluded from the digital agriculture revolution by the very interface choices those tools make.

## 4. Solution

AgriVoice is a phone-call-based AI agricultural advisor. A farmer dials a local phone number (or receives a scheduled callback). The system uses speech-to-text to understand the farmer's question in their local language, processes it through an LLM enriched with:

- **Hyperlocal weather data** (OpenMeteo) for planting/spraying windows
- **Crop calendar data** for the farmer's region and crops
- **Market price data** for harvest timing decisions
- **Pest/disease alerts** from aggregated regional reports

The AI generates advice and responds via text-to-speech in the farmer's language. The conversation is natural — the farmer describes their situation ("my maize leaves are turning yellow and curling"), and AgriVoice asks clarifying questions like a real extension officer would.

**Core user flow:** Dial phone number → describe your farming question in your language → receive spoken advice with specific actions and timing → optionally receive SMS summary with key steps.

A web dashboard (for NGOs, extension services, and government agriculture departments) shows aggregated anonymized data: what questions are farmers asking, what diseases are emerging, what regions need intervention.

## 5. Key Insight

**The most impactful agricultural AI interface isn't an app — it's a phone call.** Every farmer on Earth has access to voice telephony. By building on the one universal interface (voice), AgriVoice reaches the 40% of farmers excluded by app-based solutions. The second insight: every farmer call is a data point. Aggregated across thousands of calls, AgriVoice becomes an early warning system for emerging crises — a disease outbreak shows up as a spike in related questions from a geographic cluster, days before any formal reporting system detects it.

## 6. Tech Stack

- **Telephony:** Twilio Programmable Voice (or AWS Connect) — handles inbound/outbound calls
- **Speech-to-Text:** AWS Transcribe (supports 100+ languages) or Whisper on AWS
- **LLM:** AWS Bedrock (Claude) — agricultural reasoning and advice generation
- **Text-to-Speech:** AWS Polly (neural voices, 30+ languages) or ElevenLabs
- **Weather:** OpenMeteo API (free, global)
- **Backend:** FastAPI (Python) orchestrating the call pipeline
- **Dashboard:** Next.js (React) for the analytics/monitoring web UI
- **Database:** AWS DynamoDB (call logs, farmer profiles) + S3 (audio archives)
- **Hosting:** AWS ECS (backend) + Amplify (dashboard)

## 7. Data Strategy

- **Agricultural knowledge base:** FAO crop guides, CABI Crop Protection Compendium (open summaries), regional crop calendars from national agriculture ministries. Embedded in system prompts and a RAG retrieval layer.
- **Weather data:** OpenMeteo API — free, real-time, global coverage. Keyed to farmer's registered location or phone number area code.
- **Market prices:** Where available, integrate national agriculture ministry price APIs (many African countries publish daily market prices). For MVP, use static representative data.
- **Call analytics:** Every call transcribed and stored (with consent). NLP pipeline extracts: crop mentioned, problem type, location, severity. Aggregated for trend detection.
- **Farmer profiles:** Built over time from repeated calls — known crops, location, farm size. Enables personalized advice ("last month you asked about your wheat — how did the treatment work?").

## 8. 23-Hour Plan

| Hours | Phase | Tasks |
|-------|-------|-------|
| 0-1 | **Setup** | Scaffold FastAPI + Next.js, set up Twilio account, AWS services |
| 1-3 | **Voice Pipeline** | Build core: Twilio inbound call → AWS Transcribe (STT) → text. Test with English. |
| 3-5 | **AI Advisor** | Build Claude-powered agricultural advisor with system prompt, crop knowledge base, weather integration |
| 5-7 | **TTS + Response** | AI response → AWS Polly (TTS) → Twilio plays audio back to caller. Full round-trip working. |
| 7-9 | **Conversational Flow** | Multi-turn conversation support (farmer asks follow-up questions), clarification prompts, graceful endings |
| 9-11 | **Context Enrichment** | Integrate OpenMeteo weather for location-aware advice, add crop calendar logic |
| 11-12 | **SMS Summary** | After call ends, send SMS with key action items from the conversation |
| 12-14 | **Sleep/Break** | |
| 14-16 | **Analytics Dashboard** | Next.js dashboard: call volume, top questions, geographic heatmap, emerging issue detection |
| 16-18 | **Multilingual Demo** | Test and polish with 2-3 languages (English, French, Hindi/Swahili). Ensure STT/TTS quality. |
| 18-20 | **Demo Preparation** | Set up live demo call flow, prepare compelling farmer scenarios, test reliability |
| 20-22 | **Pitch Prep** | Build pitch narrative, practice live call demo, record backup |
| 22-23 | **Final Polish** | Deploy, ensure Twilio number works reliably, final bug fixes |

**Cut if behind:** Drop multilingual support (English only), drop SMS summary, drop analytics dashboard (show mockup), reduce to single-turn Q&A.

**Feature freeze:** Hour 18.

## 9. Demo Script

| Time | Section | Content |
|------|---------|---------|
| 0:00-0:15 | **Hook** | "There are 1.5 billion farmers in the world. Most of them don't have smartphones. None of them have agronomists on call. But all of them have this." [Hold up a basic feature phone] |
| 0:15-0:45 | **Problem** | Extension officer ratios (1:10,000). A farmer in rural Kenya sees yellowing maize — by the time they can reach an expert, they've lost 30% of their crop. App-based solutions require exactly what these farmers don't have: smartphones, data plans, and literacy. |
| 0:45-2:15 | **Demo** | Live on stage: dial AgriVoice number on speakerphone. "Hello, this is AgriVoice. What crop are you growing and what's happening?" Farmer (team member): "My maize leaves are turning yellow from the bottom, and it hasn't rained in two weeks." AgriVoice responds with diagnosis (likely nitrogen deficiency, exacerbated by drought stress), specific advice (apply urea at X rate, timing based on weather forecast — "rain is expected Thursday, apply Wednesday evening"), and follow-up ("Call back in 5 days and tell me how it looks"). Then switch to dashboard: show the analytics view — "In the last hour, 47 farmers in Western Kenya asked about maize yellowing. That's an emerging nitrogen deficiency cluster. This data reaches the agriculture ministry in real-time." |
| 2:15-2:45 | **Impact** | "Voice is the world's most inclusive interface. AgriVoice doesn't need farmers to change their behavior — it meets them where they are. Each call costs us $0.03 in API calls. An extension officer visit costs $50. That's a 1,600x efficiency gain." |
| 2:45-3:00 | **Close** | "The phone rang for 100 years before someone thought to put an agronomist on the other end. We did it in 23 hours." |

## 10. "Build Something People Want" Test

**Who uses this after the hackathon?** Agricultural extension services wanting to scale their reach 100x. NGOs (Gates Foundation, USAID, GIZ) running food security programs. National agriculture ministries in developing countries. Agri-input companies wanting a distribution channel.

**Path from demo to product:**
1. Pilot with one agricultural extension service in East Africa (large existing networks)
2. Build out language support (Swahili, Amharic, Hindi, Hausa — covers most smallholder farmers)
3. Add outbound proactive calls ("Your wheat is at risk of frost this week — here's what to do")
4. Revenue: per-call fee paid by extension services (still 100x cheaper than in-person visits), premium tier for agri-input companies (product recommendations)
5. Long-term: aggregated call data becomes the world's richest real-time agricultural intelligence dataset

## 11. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| Speech-to-text accuracy for accented/noisy environments | Use AWS Transcribe with agricultural domain vocabulary. For demo, use clear English speakers. Real deployment would need custom language models. |
| Twilio call quality and latency | Test early (hour 1-3). If problematic, switch to WebRTC-based solution for demo (simulate phone call via browser). |
| LLM gives dangerous agricultural advice | Confidence scoring + disclaimer: "This is AI-generated advice. Consult your local extension officer for critical decisions." For demo, use well-tested scenarios. |
| Multi-turn conversation complexity | Keep conversations short (3-5 turns max). AgriVoice asks structured questions to narrow diagnosis quickly rather than open-ended chat. |
| "Voice AI hotline" might feel gimmicky to judges | Lead with the data insight (early warning system from aggregated calls). The voice interface is the means; the real-time agricultural intelligence is the end. |

## 12. Win Score

| Criterion | Score (1-10) | Rationale |
|-----------|:---:|-----------|
| Innovation | 9 | Voice-first AI for agriculture is genuinely novel. The "phone calls as agricultural intelligence" insight is non-obvious and powerful. |
| Feasibility | 7 | All components exist (Twilio, STT, LLM, TTS) but the full pipeline has many moving parts. Latency management is tricky. |
| Demo Impact | 10 | A live phone call on stage is the most memorable demo possible. Judges will remember "the team that called an AI farmer hotline live." |
| Real-World Value | 10 | Most inclusive possible interface. Addresses the most underserved agricultural population. Massive scale potential. |
| Technical Depth | 8 | Full voice pipeline (STT→LLM→TTS) with context enrichment, multi-turn conversation management, and real-time analytics. Technically ambitious. |
| **Average** | **8.8** | |
