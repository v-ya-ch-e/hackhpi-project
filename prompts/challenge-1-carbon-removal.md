# Challenge 1: Carbon Removal -- Idea Generation Prompt

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

focus_preference: "hybrid"
# Options:
#   "consumer_experience" -- interactive journey explaining carbon removal
#   "data_validation" -- cross-referencing data sources to detect fraud
#   "hybrid" -- combine both directions
#   "open" -- let the model explore freely

avoid_duplicates: false
# When true: inspect all existing ideas in @ideas/challenge-1-carbon-removal/
# before generating. Do NOT produce ideas that overlap in core concept with
# any existing idea file. Add a "Differentiation" section to each new idea.
# When false: generate freely without checking existing ideas.

constraints:
  - "Must be demo-able in 3-minute pitch (+ 3-minute Q&A)"
  - "Must use provided Cula data (API, sensor data, or documentation)"
  # Add custom constraints

strengths_to_leverage:
  - "We have experience with data visualization"
  - "One of us can deploy quick on AWS and is in common familiar with AWS"
  # What's your team uniquely good at?
```

---

## Reference Files

Read these project files for full context:

- @CLAUDE.md -- hackathon overview, all challenges, partners, timeline, idea framework
- @sources/hackhpi-notion-export.md -- complete Notion export with challenge details, meeting notes, available data descriptions
- @sources/hackhpi-website-info.md -- event schedule, FAQ, logistics

If `avoid_duplicates` is `true`, also read:
- @ideas/challenge-1-carbon-removal/ -- all existing idea files to avoid duplication

---

## Task

You are an expert hackathon strategist and product designer. Generate winning hackathon project ideas for the **Carbon Removal Challenge** at HackHPI 2026.

### Context: The Challenge Sponsor

**Cula Technologies** (cula.tech) is a carbon removal company that operates biochar pyrolysis reactors. They convert agricultural waste biomass into biochar -- a stable form of carbon that persists in soil for centuries/millennia. They sell carbon credits but recognize the current credits are "flat PDF certificates" that fail to communicate the rich, verifiable physical process behind each ton of CO2 removed.

### Context: The Biochar Carbon Removal Process

1. **Biomass Growth & Harvest** -- Plants absorb CO2 over their lifetime. Agricultural waste (wood chips, crop residues) is collected.
2. **Pyrolysis** -- Biomass enters a containerized reactor. Heated to 700-800C without oxygen for ~1 hour. Because there's no oxygen, carbon doesn't combust into CO2 -- it converts to stable biochar.
3. **Biochar** -- Black, charcoal-like material with carbon locked in aromatic ring structures. Resistant to bacterial decomposition, water, and soil chemistry. Stores carbon for centuries.
4. **Application** -- Biochar is transported to farms, mixed with animal manure in farm sheds, spread on fields as fertilizer. Holds nutrients near plant roots while permanently sequestering carbon.

### Context: Available Data from Cula

- **Removal Activity API**: 395 registered carbon removal "sinks" (application events), totaling ~7,000 tons CO2 removed. Each event has: mass balances, GPS locations, distances, emission calculations.
- **Machine Sensor Data**: ~60MB from ~500 sensors on pyrolysis reactors (pressure, temperature, screw speeds, flow rates). From two real German projects including Stefan Block's facility near Hamburg (operational 2+ years).
- **Documentation**: Images and videos -- yard surveillance footage of biomass pickup, footage of biochar application to soil.
- **Fake Data**: Intentionally injected into the dataset for detection testing.

For full details on available data, solution directions, and meeting transcript summaries, see @sources/hackhpi-notion-export.md.

### Context: What Cula Wants

Two suggested directions (teams may combine or diverge):

**Direction A -- Consumer Experience:** Build an interactive experience that walks a carbon credit buyer through the entire removal journey. Show where the biomass came from (historical land imagery), the pyrolysis process, tracking data, weights, and final application. Make it accessible to someone who has never heard of carbon removal.

**Direction B -- Data Validation:** Build a system that cross-references the manually entered operational data against sensor readings, documentation (images, PDFs, videos), and physical constraints. Flag inconsistencies. Detect the injected fake data.

### Context: Your Team

Use the Configuration section at the top of this file for team parameters.

### Deduplication

If `avoid_duplicates` is `true` in the configuration:
- You MUST first read every `.md` file in @ideas/challenge-1-carbon-removal/.
- For each new idea, confirm it does NOT overlap in core concept with any existing idea.
- Add a **Differentiation** section explaining how the new idea is distinct from all existing ones.
- If all obvious angles are taken, push into more creative or hybrid territory.

If `avoid_duplicates` is `false`, skip this step entirely.

### Generate Ideas

Generate **5 distinct project ideas** ranked by win probability. For each idea, provide:

1. **Name** -- Catchy, memorable project name
2. **One-line pitch** -- Single sentence that makes a judge lean forward
3. **Problem statement** -- The specific pain point (2-3 sentences)
4. **Solution** -- What the product does and how it works (paragraph)
5. **Key innovation** -- What makes this non-obvious or differentiated
6. **Tech stack** -- Specific technologies, frameworks, APIs
7. **Data usage** -- Which Cula data sources this uses and how
8. **23-hour plan** -- Hour-by-hour breakdown: what gets built when, when to stop adding features and polish the demo
9. **Demo script** -- What the 3-minute pitch looks like (opening hook, live demo flow, closing impact statement)
10. **Risks & mitigations** -- What could go wrong and backup plans
11. **Win score** -- Rate 1-10 on: Innovation, Feasibility, Demo Impact, Technical Depth, Problem-Solution Fit
12. **Differentiation** -- (only if `avoid_duplicates` is true) How this differs from existing ideas

### Thinking Process

Before generating ideas, reason through:
- What would make a judge *remember* this project 10 presentations later?
- What's the most impressive thing achievable in 20 hours of actual coding?
- Which data combinations create the strongest "wow" factor?
- Where do consumer experience and data validation potentially overlap in a compelling way?
- What's the emotional hook that connects carbon removal to people's daily lives?

After generating all 5 ideas, write a **comparative analysis** paragraph explaining why your #1 pick is the strongest, what trade-offs exist between the top 3, and under which team conditions you'd recommend a different ranking.

### Output Instructions

Write each idea as a **separate file** in the `ideas/challenge-1-carbon-removal/` directory.

**Naming convention:** `idea-NNN-<slug>.md` where NNN is a zero-padded number continuing from the highest existing file (start at 001 if empty), and `<slug>` is a short kebab-case name derived from the idea name.

**Example:** `ideas/challenge-1-carbon-removal/idea-001-carbon-journey-explorer.md`

Each file should contain the full idea with all 11 (or 12) sections listed above.

After writing all idea files, write the **comparative analysis** as a comment in the chat (not as a file).
