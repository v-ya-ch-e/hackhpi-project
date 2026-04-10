# Idea Generation Prompts

Configurable prompts optimized for frontier LLMs to generate hackathon project ideas for each HackHPI 2026 challenge.

## Available Prompts

| File | Challenge | Sponsor |
|------|-----------|---------|
| `challenge-1-carbon-removal.md` | Carbon Removal Challenge | Cula Technologies |
| `challenge-2-computer-vision.md` | Autonomy in the Fields | CLAAS |
| `challenge-3-open-challenge.md` | Open Challenge | -- |

## How to Use

### 1. Pick a challenge prompt

Open the markdown file for the challenge you're considering. Each prompt is self-contained with all the domain context the model needs.

### 2. Configure parameters

At the top of each prompt is a `Configuration` section with YAML-style parameters. Edit these to match your team:

- **team_skills** -- List your team's actual skills (be honest -- the model optimizes ideas for your strengths)
- **preferred_stack** -- Technologies you're comfortable shipping with in 23 hours
- **ambition_level** -- `low` (safe, guaranteed demo) to `moonshot` (risky but impressive if it works)
- **focus_preference** -- Challenge-specific options to steer idea direction
- **constraints** -- Hard requirements (add any you have)
- **strengths_to_leverage** -- What your team is unusually good at

### 3. Copy into a frontier model

Copy everything below the "Copy everything below this line" marker. Paste your filled-in configuration where the prompt says `{PASTE YOUR CONFIGURATION HERE}`.

### 4. Run and iterate

Run the prompt. Review the 5 ideas. Then iterate:

- Ask the model to go deeper on your favorite idea: "Expand idea #2 with a detailed implementation plan and exact API calls"
- Combine elements: "Merge the demo approach from #1 with the technical architecture of #4"
- Challenge assumptions: "What if we only have 12 hours instead of 20? Rerank."
- Get contrarian: "What idea would you generate if you were trying to be the most surprising entry?"

## Recommended Models

These prompts are tuned for frontier-class models. Performance ranking for this type of structured ideation:

| Model | Strengths | Best for |
|-------|-----------|----------|
| **Claude Opus / Sonnet** | Deep reasoning, follows complex structured instructions precisely, strong comparative analysis | Challenge 1 (data validation reasoning), Challenge 3 (product strategy) |
| **GPT-4o / o3** | Broad knowledge, strong at technical architecture, good at creative naming | Challenge 2 (CV architecture), all challenges |
| **Gemini 2.5 Pro** | Strong multimodal understanding, good at systems thinking | Challenge 2 (vision pipeline), Challenge 3 (satellite/sensor ideas) |

**Best strategy:** Run the same prompt on 2-3 models, compare outputs, cherry-pick the strongest ideas across all runs.

## Tips for Best Results

1. **Be specific in configuration.** "Python" is okay; "Python with FastAPI, experienced with pandas and plotly" is better. The more the model knows about your team, the more practical the ideas.

2. **Run multiple times.** Each run produces different ideas. Do 2-3 runs per model and collect the full idea pool before deciding.

3. **Cross-pollinate across challenges.** An idea from the open challenge prompt might inspire a twist on the carbon removal challenge. Read all outputs even if you've picked your challenge.

4. **Use the comparative analysis.** The model's ranking reflects its reasoning. If it ranks an idea #3 but your gut says #1, ask it: "Why did you rank idea X below Y? Under what conditions would X win?"

5. **Drill down fast.** Once you have a candidate idea, immediately ask for: exact API endpoints to call, specific npm/pip packages needed, database schema, and a minute-by-minute demo script. Concrete details surface feasibility problems early.

6. **Time-box ideation.** Spend at most 1-2 hours on idea generation. The hackathon rewards execution over planning.
