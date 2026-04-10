# Idea 003: CarbonStory

## 1. Name

**CarbonStory** -- Your Carbon Credit, Narrated

## 2. One-line pitch

An immersive, scroll-driven visual narrative that takes a carbon credit buyer on a cinematic journey from a German farm field to a pyrolysis reactor to the soil -- using real satellite imagery, real sensor data, and AI-generated narration for *their specific credit*.

## 3. Problem statement

When consumers buy carbon credits, they get an abstract number: "1 ton of CO2 removed." This means nothing emotionally. There's no connection to a real place, a real machine, or a real farmer. The result is that carbon offsetting feels like a donation to a black box, not a tangible action. Cula has rich, real-world data behind every credit, but no way to turn that data into a story that makes people *feel* the impact.

## 4. Solution

CarbonStory is a scroll-driven, long-form web experience (think New York Times "Snowfall" interactive pieces) personalized to a specific removal activity. Each credit gets a unique URL. When opened:

1. **Chapter 1 -- The Field**: A satellite/aerial view zooms into the GPS coordinates of the biomass source. AI narration explains what grew here and how plants capture CO2. Historical imagery (Google Earth timelapse-style) shows the land over time.
2. **Chapter 2 -- The Harvest**: Photos/video from documentation showing biomass collection. Animated infographic of the mass: "4.2 tons of wood chips, containing approximately 2.1 tons of carbon, loaded onto a truck."
3. **Chapter 3 -- The Journey**: Animated route on the map from source to pyrolysis facility, with real distance from the API. Carbon footprint of transport displayed and subtracted from total removal.
4. **Chapter 4 -- The Reactor**: A stylized cross-section animation of the pyrolysis reactor. Real sensor data drives the animation -- actual temperature curve, actual pressure readings, actual duration. The "heartbeat" of the machine shown in real time (accelerated).
5. **Chapter 5 -- The Biochar**: Visual transformation from wood chips to biochar. Explanation of aromatic carbon ring stability. "This carbon will remain locked for over 1,000 years."
6. **Chapter 6 -- The Application**: Map to the application farm. Documentation footage of biochar being mixed with manure and spread on fields. Final mass balance shown.
7. **Chapter 7 -- Your Impact**: Summary card showing total CO2 removed, equivalent metrics ("equal to taking 1.5 cars off the road for a year"), and a shareable image.

## 5. Key innovation

The experience is data-driven, not template-driven. Every number, every map pin, every sensor curve is pulled from Cula's actual data for that specific removal activity. This isn't a generic explainer video -- it's a personalized, verifiable narrative. The AI narration adapts to the specifics of each credit (different biomass types, different facilities, different application methods), making each story unique.

## 6. Tech stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js, GSAP / Framer Motion (scroll-driven animations), Tailwind CSS |
| Maps | Mapbox GL JS (satellite tiles, animated routes, terrain) |
| Data visualization | D3.js (sensor data animations in reactor chapter) |
| AI narration | OpenAI GPT-4o (generate personalized narrative text per activity) |
| Backend | FastAPI (Python) -- serve activity data, pre-generated narrations |
| Data processing | pandas (prepare sensor data for visualization) |
| Hosting | AWS (S3 static hosting + CloudFront CDN, Lambda for API) |

## 7. Data usage

- **Removal Activity API**: Every field in every sink record drives a chapter. GPS coordinates for map scenes, mass balances for infographics, distances for journey animation, emission calculations for impact summary.
- **Machine Sensor Data**: The reactor chapter is powered by actual sensor time-series. Temperature curve animated in real-time (compressed). Pressure and throughput data shown as supporting visuals.
- **Documentation (images/video)**: Embedded directly in relevant chapters. Biomass pickup footage in Chapter 2, application footage in Chapter 6. Provides the visceral, real-world texture that makes the story credible.
- **Fake Data**: Not a primary focus, but the system inherently exposes implausible data -- if sensor data is flat during a claimed operation, the reactor animation will visually show "nothing happening," which itself becomes a soft validation signal.

## 8. 23-hour plan

| Hour | Who | What |
|------|-----|------|
| 0-1 | All | Data exploration. Pick 3-5 removal activities with the richest data (GPS, sensors, documentation) for MVP. |
| 1-3 | Dev A (Frontend) | Scroll-driven page skeleton: chapter sections, scroll triggers, transition points |
| 1-3 | Dev B (Backend) | FastAPI: serve activity data, generate and cache AI narrations for selected activities |
| 1-3 | Dev C (Data/Viz) | Prepare sensor data for reactor animation: resample, smooth, identify key moments |
| 1-3 | Dev D (UI/UX) | Design visual language: typography, color palette, illustration style, chapter layouts |
| 3-5 | Dev A+D | Chapter 1 (The Field): satellite map zoom, location reveal, AI narration overlay |
| 3-5 | Dev B | AI narration pipeline: craft prompts that generate engaging, personalized text per activity |
| 3-5 | Dev C | Reactor cross-section animation: SVG reactor driven by sensor data arrays |
| 5-7 | Dev A+D | Chapter 3 (The Journey): animated route on map with distance counter |
| 5-7 | Dev B+C | Chapter 4 (The Reactor): integrate sensor-driven animation with narration |
| 7-9 | Dev A | Chapter 2 (Harvest) and Chapter 6 (Application): media-heavy sections with documentation |
| 7-9 | Dev B | Chapter 5 (Biochar): carbon chemistry explainer animation |
| 7-9 | Dev C+D | Chapter 7 (Impact): summary card, equivalency metrics, shareable image generation |
| 9-11 | All | Stitch all chapters together. Smooth scroll transitions. Data flowing end-to-end. |
| 11-13 | Dev A+D | Responsive design (must look great on a projector during demo) |
| 11-13 | Dev B+C | Pre-generate narrations for all demo activities. Optimize load performance. |
| 13-15 | All | Landing page: "Explore a carbon credit" with a gallery of available activities |
| 15-17 | All | **Feature freeze.** Polish animations, fix scroll jank, optimize images. |
| 17-19 | All | Demo preparation: rehearse scrolling through the single best story at demo pace |
| 19-20 | All | Pitch rehearsal, backup recording |

## 9. Demo script

**Opening hook (30s):** *Dark screen.* "Close your eyes for a second. You bought a carbon credit. What do you think happened? ... Most people have no idea. Let us show you." *Screen lights up with a satellite view of a German field.*

**Live demo (2m):**
1. Begin scrolling. The satellite view zooms in smoothly to a real biomass source near Hamburg. AI narration appears: "This is a 12-hectare woodland in Schleswig-Holstein. Over the past decade, these trees absorbed approximately 240 tons of CO2..."
2. Scroll to Chapter 2: show real biomass collection footage. Animated mass counter: "4.2 tons loaded."
3. Scroll to Chapter 3: the map animates a truck route from source to Stefan Block's facility. "47 kilometers. 12 kg CO2 emitted in transport -- already subtracted from your credit."
4. Scroll to Chapter 4: the reactor cross-section comes alive. Temperature gauge climbs from 20C to 743C driven by real sensor data. Pressure rises. "For 58 minutes, this biomass was transformed without a single flame."
5. Scroll to Chapter 7: "Your credit: 1.04 tons of CO2 removed. Stored for over 1,000 years. That's equivalent to 4 months of driving."
6. Show the shareable impact card.

**Closing impact (30s):** "Cula removes 7,000 tons of CO2 a year. Each ton has a story like this one -- real places, real machines, real data. CarbonStory turns every credit into a narrative that anyone can understand and share. This is how you get millions of people to care about carbon removal."

## 10. Risks & mitigations

| Risk | Mitigation |
|------|-----------|
| Scroll-driven animations are finicky cross-browser | Use battle-tested GSAP ScrollTrigger. Test on Chrome (demo browser) early. |
| Satellite imagery for specific GPS coordinates may be low-resolution | Use Mapbox satellite tiles (good for Germany). Fall back to styled terrain map if needed. |
| Sensor data doesn't have enough visual drama | Compress time axis for animation (1 hour of data in 10 seconds). Add sound design cues if time allows. |
| AI-generated narration feels robotic | Craft strong system prompts with tone guidelines ("warm, clear, specific, no jargon"). Pre-generate and hand-edit the 3 demo stories. |
| Too ambitious -- not all 7 chapters will be polished | Prioritize Chapters 1, 4, and 7 (field, reactor, impact). These three alone tell the core story. |
| Documentation images/videos may be low quality or sparse | Design around optional media. Some chapters can be illustration-driven if real media is missing. |

## 11. Win score

| Criterion | Score | Rationale |
|-----------|-------|-----------|
| Innovation | 7/10 | Scroll-driven storytelling is established; applying it to carbon credits is novel but not groundbreaking |
| Feasibility | 7/10 | 7 chapters is aggressive for 20 hours. May end up with 4-5 polished ones. |
| Demo Impact | 10/10 | Visually stunning, emotional, perfect for a 3-minute presentation format |
| Technical Depth | 6/10 | Mostly frontend craft. Sensor animation adds depth but no heavy backend logic. |
| Problem-Solution Fit | 8/10 | Directly addresses Direction A (consumer experience) with real data |
| **Average** | **7.6/10** | |
