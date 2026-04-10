# Idea Generation Prompts

Configurable prompts for generating hackathon project ideas, designed to run directly inside **Cursor** using `@` file references.

## Available Prompts

| File | Challenge | Sponsor | Output folder |
|------|-----------|---------|---------------|
| `challenge-1-carbon-removal.md` | Carbon Removal | Cula Technologies | `ideas/challenge-1-carbon-removal/` |
| `challenge-2-computer-vision.md` | Autonomy in the Fields | CLAAS | `ideas/challenge-2-computer-vision/` |
| `challenge-3-open-challenge.md` | Open Challenge | -- | `ideas/challenge-3-open-challenge/` |

## How to Use (Cursor Workflow)

### 1. Edit the configuration

Open the prompt file for your challenge. At the top is a `Configuration` section with YAML-style parameters. Edit these to match your team:

- **team_skills** -- List your team's actual skills (be honest -- the model optimizes ideas for your strengths)
- **preferred_stack** -- Technologies you're comfortable shipping with in 23 hours
- **ambition_level** -- `low` (safe, guaranteed demo) to `moonshot` (risky but impressive if it works)
- **focus_preference** -- Challenge-specific options to steer idea direction
- **avoid_duplicates** -- `true` or `false` (see below)
- **constraints** -- Hard requirements
- **strengths_to_leverage** -- What your team is unusually good at

### 2. Reference the prompt in Cursor chat

Open Cursor chat and reference the prompt file:

```
@prompts/challenge-1-carbon-removal.md
```

The model will automatically pick up the `@` references inside the prompt to read:
- `@CLAUDE.md` -- hackathon overview, challenges, timeline
- `@sources/hackhpi-notion-export.md` -- full Notion export with meeting notes and data details
- `@sources/hackhpi-website-info.md` -- schedule, FAQ, logistics
- `@ideas/challenge-*/` -- existing ideas (when `avoid_duplicates` is on)

### 3. Ideas are written to files

The model writes each idea as a separate markdown file in the matching `ideas/` subfolder:

```
ideas/challenge-1-carbon-removal/idea-001-carbon-journey-explorer.md
ideas/challenge-1-carbon-removal/idea-002-data-integrity-checker.md
...
```

The comparative analysis is returned in the chat, not saved to a file.

### 4. Iterate

After the first run:
- Ask the model to expand a specific idea: "Expand idea-002 with a detailed implementation plan"
- Combine ideas: "Merge the demo approach from idea-001 with the tech stack of idea-003"
- Re-run with `avoid_duplicates: true` to get a fresh batch that doesn't repeat previous concepts
- Challenge assumptions: "What if we only have 12 hours? Rerank the ideas."

## The `avoid_duplicates` Parameter

Each prompt has an `avoid_duplicates` config toggle:

| Value | Behavior |
|-------|----------|
| `true` | The model reads all existing `.md` files in the corresponding `ideas/challenge-*/` folder before generating. It avoids producing ideas that overlap in core concept with existing ones. Each new idea includes a **Differentiation** section. |
| `false` | The model generates ideas freely without inspecting existing files. Use this for the first run or when you want unconstrained brainstorming. |

**Typical workflow:**
1. First run: set `avoid_duplicates: false` to get 5 ideas
2. Second run: set `avoid_duplicates: true` to get 5 more ideas guaranteed to be different
3. Repeat as needed to build a large, diverse idea pool

## Recommended Models

These prompts work with any model available in Cursor. Performance ranking for structured ideation:

| Model | Strengths | Best for |
|-------|-----------|----------|
| **Claude Opus / Sonnet** | Deep reasoning, follows structured instructions precisely, strong comparative analysis | Challenge 1 (data validation reasoning), Challenge 3 (product strategy) |
| **GPT-4o / o3** | Broad knowledge, strong at technical architecture, creative naming | Challenge 2 (CV architecture), all challenges |
| **Gemini 2.5 Pro** | Strong multimodal understanding, systems thinking | Challenge 2 (vision pipeline), Challenge 3 (satellite/sensor ideas) |

**Best strategy:** Run the same prompt on 2-3 different models with `avoid_duplicates: true` after the first batch. This builds a diverse pool across different model "thinking styles."

## Tips for Best Results

1. **Be specific in configuration.** "Python" is okay; "Python with FastAPI, experienced with pandas and plotly" is better.

2. **Run multiple times.** Each run with `avoid_duplicates: true` produces ideas that don't overlap with previous runs.

3. **Cross-pollinate across challenges.** An idea from the open challenge prompt might inspire a twist on the carbon removal challenge. Read all outputs.

4. **Drill down fast.** Once you have a candidate idea, ask for: exact API endpoints, specific packages needed, database schema, and a minute-by-minute demo script.

5. **Time-box ideation.** Spend at most 1-2 hours on idea generation. The hackathon rewards execution over planning.
