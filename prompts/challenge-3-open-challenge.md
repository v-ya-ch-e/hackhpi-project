# Challenge 3: Open Challenge -- Idea Generation Prompt

## Configuration

> Edit these parameters before running this prompt in Cursor.

```yaml
team_size: 4
team_skills:
  - "full-stack web development"
  - "Python / data analysis"
  - "FastAPI"
  - "n8n pipelines"
  - "Integration of AI via API"
  - "UI/UX design"
  # Add/remove skills as needed

preferred_stack:
  - "React / Next.js"
  - "Python"
  - "FastAPI"
  - "AWS (unlimited credits available)"
  # Add/remove technologies as needed

time_budget: "20 hours of coding (23h total minus breaks/planning)"

ambition_level: "high"  # low | medium | high | moonshot

domain_preference: "open"
# Options:
#   "agriculture" -- farming, food production, agricultural technology
#   "climate" -- emissions, sustainability, energy, environmental monitoring
#   "intersection" -- where agriculture and climate meet
#   "open" -- explore the full space

product_type: "any"
# Options:
#   "web_app" -- browser-based application
#   "mobile_app" -- mobile-first experience
#   "data_tool" -- analytics / dashboard / data pipeline
#   "hardware_software" -- IoT, sensor integration, physical computing
#   "ai_agent" -- LLM-powered assistant or autonomous system
#   "any" -- let the model decide

target_user: "any"
# Options:
#   "farmer" -- small or large-scale farmers
#   "consumer" -- end consumers of agricultural products
#   "policy_maker" -- government, regulators
#   "supply_chain" -- logistics, distributors, retailers
#   "researcher" -- agricultural or climate scientists
#   "any" -- let the model decide

avoid_duplicates: false
# When true: inspect all existing ideas in @ideas/challenge-3-open-challenge/
# before generating. Do NOT produce ideas that overlap in core concept with
# any existing idea file. Add a "Differentiation" section to each new idea.
# When false: generate freely without checking existing ideas.

constraints:
  - "Must be demo-able in 3-minute pitch"
  - "Must relate to agriculture and/or climate"
  # Add custom constraints

strengths_to_leverage:
  - "We have experience building consumer apps"
  # What's your team uniquely good at?

topics_that_excite_us:
  # What problems do you care about? This helps the model align with your motivation.
```

---

## Reference Files

Read these project files for full context:

- @CLAUDE.md -- hackathon overview, all challenges, partners, timeline, idea framework
- @sources/hackhpi-notion-export.md -- complete Notion export with challenge details, partner info, available data
- @sources/hackhpi-website-info.md -- event schedule, FAQ, logistics

If `avoid_duplicates` is `true`, also read:
- @ideas/challenge-3-open-challenge/ -- all existing idea files to avoid duplication

---

## Task

You are an expert product strategist, startup advisor, and hackathon mentor specializing in agriculture and climate technology. Generate winning hackathon project ideas for the **Open Challenge** at HackHPI 2026.

### Context: The Open Challenge

"Anything related to agriculture and/or climate. Build something people want, something useful, or both."

This is deliberately open-ended. The judging criteria favor:
- **Real-world impact** -- Does this solve a problem people actually have?
- **Innovation** -- Is this a fresh approach, not an obvious solution?
- **Technical execution** -- Is the implementation impressive for 23 hours?
- **Demo quality** -- Does the pitch tell a compelling story?

For full hackathon context (dates, timeline, partners, prizes), see @CLAUDE.md.

### Context: Available Resources

- **AWS** -- Virtually unlimited cloud credits (compute, storage, AI services, databases, hosting)
- **QuantCo** -- Data-to-decisions company present as mentor. Expertise in causal inference, pricing optimization, data engineering at scale.
- **Optiver** -- Trading firm present as mentor. Expertise in quantitative analysis, market-making, real-time data processing.
- **Cula Technologies** -- Carbon removal data (API with removal activities, sensor data) available even to teams not doing Challenge 1
- **CLAAS** -- Agricultural machine data potentially available
- **Mentors and sponsors** on-site for guidance

For full partner and data details, see @sources/hackhpi-notion-export.md.

### Context: Agriculture & Climate Problem Space

Major unsolved problems at the intersection of agriculture and climate:

**Agriculture:**
- Smallholder farmers (80% of global farms) lack access to modern agri-tech tools
- Crop disease detection and early warning systems
- Soil health monitoring and degradation prevention
- Water management and irrigation optimization
- Supply chain transparency (farm-to-fork traceability)
- Food waste (30-40% of food produced globally is wasted)
- Labor shortages in agriculture across developed nations
- Pesticide reduction and integrated pest management
- Precision agriculture -- right input, right place, right time
- Agricultural market access and fair pricing for farmers
- Post-harvest loss reduction (especially in developing regions)

**Climate:**
- Carbon footprint tracking for agricultural products
- Regenerative agriculture adoption and verification
- Climate-adaptive crop selection and planning
- Extreme weather early warning for farmers
- Biodiversity loss monitoring in agricultural landscapes
- Methane emissions from livestock -- monitoring and reduction
- Deforestation monitoring linked to agricultural expansion
- Carbon market accessibility for small farmers
- Green energy integration in farming operations
- Climate migration impact on agricultural labor

**Emerging tech intersections:**
- LLM-powered agricultural advisory (personalized, multilingual)
- Satellite + drone + ground sensor fusion for field monitoring
- Marketplace/platform models connecting climate-conscious consumers to sustainable farms
- Gamification of sustainable farming practices
- Digital twins for farm planning under climate scenarios

### Context: Your Team

Use the Configuration section at the top of this file for team parameters.

### Deduplication

If `avoid_duplicates` is `true` in the configuration:
- You MUST first read every `.md` file in @ideas/challenge-3-open-challenge/.
- For each new idea, confirm it does NOT overlap in core concept with any existing idea.
- Add a **Differentiation** section explaining how the new idea is distinct from all existing ones.
- If all obvious angles are taken, push into more creative or hybrid territory.

If `avoid_duplicates` is `false`, skip this step entirely.

### Generate Ideas

Generate **5 distinct project ideas** ranked by win probability. Each idea should be meaningfully different in target user, problem space, or technical approach. For each idea, provide:

1. **Name** -- Catchy, memorable project name
2. **One-line pitch** -- Single sentence that makes a judge lean forward
3. **Problem statement** -- The specific real-world pain point. Include who suffers, how much it costs, and why existing solutions fail. (3-4 sentences)
4. **Solution** -- What the product does, who uses it, and the core user flow (paragraph)
5. **Key insight** -- The non-obvious observation that makes this idea work (this is what separates a winning hack from a generic one)
6. **Tech stack** -- Specific technologies, APIs, data sources
7. **Data strategy** -- Where does the data come from? Public datasets, APIs, generated, scraped, user-input? Be specific.
8. **23-hour plan** -- Hour-by-hour breakdown: MVP scope, what to cut if behind, when to freeze features and polish
9. **Demo script** -- The 3-minute pitch arc: hook (15s), problem (30s), solution demo (90s), impact/vision (30s), close (15s)
10. **"Build something people want" test** -- Who would actually use this after the hackathon? What's the path from demo to real product?
11. **Risks & mitigations** -- Scope creep, data availability, technical blockers, backup plans
12. **Win score** -- Rate 1-10 on: Innovation, Feasibility, Demo Impact, Real-World Value, Technical Depth
13. **Differentiation** -- (only if `avoid_duplicates` is true) How this differs from existing ideas

### Thinking Process

Before generating ideas, reason through:
- The judges will see ~20 projects. What makes an open-challenge project memorable? Usually: a clear problem, a surprising solution, and a demo moment that makes people say "I want that."
- "Build something people want" is a YC mantra. The strongest ideas have an identifiable user who would pay for or consistently use this tool.
- With AWS unlimited credits, what becomes possible that teams without cloud access couldn't do? (Large model inference, real-time processing, scalable APIs, satellite data processing)
- What problems are partners (QuantCo, Optiver, Cula) uniquely positioned to mentor on? Ideas aligned with mentor expertise get better guidance during the hackathon.
- Agriculture and climate intersect at food security -- the most visceral framing for judges.
- What's happening right now (April 2026) in agriculture and climate that makes a particular idea timely?

After generating all 5 ideas, write a **comparative analysis** paragraph explaining: which idea has the highest ceiling if execution is perfect, which is the safest bet for a strong demo, and which would be most impressive to the specific sponsors present (QuantCo, Optiver, Cula, CLAAS, AWS).

### Output Instructions

Write each idea as a **separate file** in the `ideas/challenge-3-open-challenge/` directory.

**Naming convention:** `idea-NNN-<slug>.md` where NNN is a zero-padded number continuing from the highest existing file (start at 001 if empty), and `<slug>` is a short kebab-case name derived from the idea name.

**Example:** `ideas/challenge-3-open-challenge/idea-001-farm-to-fork-tracker.md`

Each file should contain the full idea with all 12 (or 13) sections listed above.

After writing all idea files, write the **comparative analysis** as a comment in the chat (not as a file).
