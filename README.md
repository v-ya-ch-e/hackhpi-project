# HackHPI 2026 -- Idea Generation Toolkit

Structured prompts and reference material for generating winning project ideas at [HackHPI 2026](https://hackhpi.org) (April 10-11, Potsdam). Designed to run inside **Cursor** using `@` file references.

**Theme:** Agriculture, Climate & Tech | **Duration:** 23 hours | **Prize:** 1000 EUR per challenge

## Quick Start

1. Read `CLAUDE.md` for the full hackathon context (challenges, data, timeline)
2. Pick a challenge and open the matching prompt in `prompts/`
3. Edit the Configuration YAML at the top (team skills, preferences, `avoid_duplicates`)
4. In Cursor chat, reference the prompt: `@prompts/challenge-1-carbon-removal.md`
5. The model reads source files, generates ideas, and writes them to `ideas/challenge-*/`
6. Re-run with `avoid_duplicates: true` for fresh ideas that don't repeat previous concepts

See `prompts/README.md` for detailed usage, model recommendations, and tips.

## Challenges

| # | Challenge | Sponsor | Prompt | Ideas folder |
|---|-----------|---------|--------|--------------|
| 1 | Carbon Removal | Cula Technologies | `prompts/challenge-1-carbon-removal.md` | `ideas/challenge-1-carbon-removal/` |
| 2 | Autonomy in the Fields | CLAAS | `prompts/challenge-2-computer-vision.md` | `ideas/challenge-2-computer-vision/` |
| 3 | Open Challenge | -- | `prompts/challenge-3-open-challenge.md` | `ideas/challenge-3-open-challenge/` |

## Project Structure

```
hackhpi-project/
  CLAUDE.md                                   # Project context for AI assistants
  README.md                                   # This file
  sources/
    hackhpi-notion-export.md                  # Full Notion notes (challenges, meeting transcripts)
    hackhpi-website-info.md                   # Event info from hackhpi.org
  prompts/
    README.md                                 # Prompt usage & configuration guide
    challenge-1-carbon-removal.md             # Prompt: Cula / biochar / carbon credits
    challenge-2-computer-vision.md            # Prompt: CLAAS / person detection / scene understanding
    challenge-3-open-challenge.md             # Prompt: open agriculture + climate
  ideas/
    challenge-1-carbon-removal/               # Generated ideas for Challenge 1
      idea-001-<slug>.md
    challenge-2-computer-vision/              # Generated ideas for Challenge 2
      idea-001-<slug>.md
    challenge-3-open-challenge/               # Generated ideas for Challenge 3
      idea-001-<slug>.md
```

## Deduplication

Each prompt has an `avoid_duplicates` toggle. When set to `true`, the model inspects all existing idea files in the corresponding `ideas/` subfolder before generating, ensuring no concept overlap. This lets you build a large, diverse pool of ideas across multiple runs.

## Partners

Cula Technologies (carbon certificates) -- CLAAS (agricultural vehicles) -- QuantCo (data analytics) -- AWS (cloud credits) -- Optiver (trading)
