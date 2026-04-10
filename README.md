# HackHPI 2026 -- Idea Generation Toolkit

Structured prompts and reference material for generating winning project ideas at [HackHPI 2026](https://hackhpi.org) (April 10-11, Potsdam).

**Theme:** Agriculture, Climate & Tech | **Duration:** 23 hours | **Prize:** 1000 EUR per challenge

## Quick Start

1. Read `CLAUDE.md` for the full hackathon context (challenges, data, timeline)
2. Pick a challenge and open the matching prompt in `prompts/`
3. Fill in your team configuration at the top of the prompt
4. Paste into a frontier model (Claude, GPT-4o, Gemini) and run
5. See `prompts/README.md` for detailed usage tips and model recommendations

## Challenges

| # | Challenge | Sponsor | Prompt |
|---|-----------|---------|--------|
| 1 | Carbon Removal | Cula Technologies | `prompts/challenge-1-carbon-removal.md` |
| 2 | Autonomy in the Fields | CLAAS | `prompts/challenge-2-computer-vision.md` |
| 3 | Open Challenge | -- | `prompts/challenge-3-open-challenge.md` |

## Project Structure

```
hackhpi-project/
  CLAUDE.md                               # Project context for AI assistants
  README.md                               # This file
  sources/
    hackhpi-notion-export.md              # Full Notion notes (challenges, meeting transcripts)
    hackhpi-website-info.md               # Event info from hackhpi.org
  prompts/
    README.md                             # Prompt usage & configuration guide
    challenge-1-carbon-removal.md         # Idea prompt: Cula / biochar / carbon credits
    challenge-2-computer-vision.md        # Idea prompt: CLAAS / person detection / scene understanding
    challenge-3-open-challenge.md         # Idea prompt: open agriculture + climate
```

## Partners

Cula Technologies (carbon certificates) -- CLAAS (agricultural vehicles) -- QuantCo (data analytics) -- AWS (cloud credits) -- Optiver (trading)
