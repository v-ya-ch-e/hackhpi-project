# HackHPI 2026 -- Project Context

## Event Overview

- **What:** HackHPI 2026 hackathon -- Agriculture, Climate & Tech
- **When:** April 10-11, 2026 (hacking: 14:00 Fri -> 13:00 Sat, ~23 hours)
- **Where:** HPI Campus, August-Bebel-Str. 88, 14482 Potsdam
- **Website:** https://hackhpi.org
- **Slack:** https://hackhpi26.slack.com
- **Prize:** 1000 EUR per challenge (3 challenges)

## Partners

| Partner | Domain | What they provide |
|---------|--------|-------------------|
| [Cula Technologies](http://cula.tech) | Carbon certificates | Challenge 1 sponsor, Removal Activity API, sensor data, Cursor Max |
| [CLAAS](https://www.claas.com) | Agricultural vehicles | Challenge 2 sponsor, 500MB image/video data, LIDAR + cameras |
| [QuantCo](https://www.quantco.com) | Data-to-decisions | Travel scholarships, coffee cart |
| AWS | Cloud infrastructure | Virtually unlimited credits for hosting |
| [Optiver](https://optiver.com) | Trading | Ecosystem partner |

## Challenges

### Challenge 1: Carbon Removal (Cula Technologies)

Biochar carbon removal -- make the complex physical process understandable to consumers, or validate the data pipeline.

**Two directions:**
1. **Consumer Experience** -- interactive journey explaining carbon removal step-by-step (where biomass comes from, pyrolysis process, biochar application, carbon stored for centuries)
2. **Data Validation** -- cross-reference manual inputs, sensor data, and documentation to flag inconsistencies and detect intentionally injected fake data

**Available data:**
- Removal Activity API: 395 sinks, ~7000t CO2, mass balances, locations, distances, emissions
- Machine sensor data: ~60MB from ~500 sensors (pressure, temperature, screw speeds)
- Documentation: images, videos, yard surveillance footage
- Fake data: intentionally injected for detection testing

### Challenge 2: Autonomy in the Fields (CLAAS)

Computer vision for autonomous agricultural machines -- life-saving person detection and scene understanding.

**Primary goal:** Efficient bounding-box person detector
**Secondary:** Scene understanding -- extract maximum information from images/video

**Available data:**
- 500MB image and video data from agricultural machines
- LIDAR + camera sensor feeds
- Platform: Kaggle ("CAGGLE" = CLAAS + Kaggle)

### Challenge 3: Open Challenge

Anything related to agriculture and/or climate. Build something people want, something useful, or both. Mentors and sponsors available for guidance.

## Timeline

| Time | Event |
|------|-------|
| Fri 09:00-11:00 | Check-in + Breakfast |
| Fri 12:00-13:00 | Challenge Presentations |
| Fri 14:00 | **Hacking starts** |
| Fri 16:00 | Challenge pick deadline |
| Sat 13:00 | **Hacking ends** |
| Sat 14:00-16:00 | Presentations |
| Sat 16:00-17:00 | Awards |

## Idea Creation Framework

When brainstorming ideas for any challenge, evaluate each idea against:

1. **Problem** -- What specific pain point does this solve? Who feels it?
2. **Target user** -- Who is the primary audience? (consumer, farmer, auditor, data scientist?)
3. **Unique angle** -- What makes this different from the obvious solution?
4. **Tech stack** -- What technologies does this require? Do we have the skills?
5. **Feasibility** -- Can we build an MVP in ~20 hours of coding?
6. **Demo-ability** -- Will this look impressive in a 3-minute pitch? What's the "wow" moment?
7. **Pitch story** -- What narrative ties this together? (problem -> insight -> solution -> impact)

## Project Structure

- `sources/` -- Raw hackathon information exported from Notion and the website
- `prompts/` -- Configurable AI prompts for generating ideas per challenge
- `CLAUDE.md` -- This file; project context for AI assistants
- `README.md` -- Project overview and quick start
