# Hack HPI -- Notion Page Export

> Source: Notion page "Hack HPI" (https://www.notion.so/33ece27ee4b280bd81daf0399fedbe5f)
> Exported: 2026-04-10

---

## Introduction

### Partners

- **[Cula Technologies](http://cula.tech)** -- carbon certificates
- **[CLAAS](https://www.claas.com/de-de)** -- agriculture vehicles
- **[QuantCo](https://www.quantco.com/)** -- help organizations turn data into decisions
- **AWS** -- virtually unlimited credits for hosting etc.
- **[Optiver](https://optiver.com/)** -- trading

Slack channel: https://hackhpi26.slack.com/join/shared_invite/zt-3unimjm4h-_0Y07yjIcMj2DNmQkVSgfg

---

## Challenges

> Challenge picking deadline: 16:00 on Day 1

---

### Challenge 1: Carbon Removal Challenge

- **Sponsor:** Cula Technologies
- **Grand prize:** 1000 Euro for team
- **Tooling:** Cursor Max sponsored

#### Presentation slides

Cula_HackHPI_Challenge_Short_Presentation.pdf (attached in Notion)

#### Meeting Notes: Biochar Carbon Removal Technology Briefing (2026-04-10)

##### Action Items & Next Steps

- Build a consumer experience that explains the carbon removal journey step-by-step
- Build a validation solution that checks data consistency across manual inputs, sensor data, and documentation
- Attempt to detect fake data that has been injected into the dataset
- Teams will receive PersonX accounts with major model providers

##### The Biochar Carbon Removal Process

1. **Biomass Growth & Harvest:** Biomass grows over its lifetime, extracting CO2 from the atmosphere and storing carbon in the plant.

2. **Pyrolysis Conversion:** Biomass (such as agricultural waste stored as wood chips) is transferred into a containerized pyrolysis reactor.
   - The reactor is a purpose-built machine that burns biomass without oxygen.
   - Chamber temperatures reach 700-800 degrees.
   - Biomass remains in the chamber for approximately one hour.
   - The process is called pyrolysis -- heating without oxygen prevents carbon from evaporating as CO2.

3. **Biochar Production:** The output is biochar -- a black, charcoal-like material where carbon is stored in a very stable form.
   - Carbon is locked away in stable rings that bacteria, water, and active soil components cannot destroy.
   - Carbon remains stored for centuries or even millennia.

4. **Application:** Biochar is packaged and transported to application sites.
   - One use case is in animal farm sheds, where it's mixed with manure and then spread on fields.
   - Acts as a fertilizer, holding nutrients close to plant roots while storing carbon.

##### The Problem with Current Carbon Credits

- The end result of the entire complex process is a carbon credit -- described as "flat" and essentially just a PDF certificate.
- All the richness of the real-world process that led to the ton of carbon being removed is lost.
- Concerned citizens purchasing these credits cannot easily understand what happened, what the process was, whether it's legitimate, or if emissions are removed permanently.
- Understanding the process requires access to real-world data: biomass quantities transported, conversion amounts, storage locations, shipping destinations, and application methods.
- There's a communication challenge in explaining this complex physical process to people unfamiliar with it (such as explaining it to family members).
- Getting average consumers to understand carbon removal is key to unlocking more finance for these projects.

##### Available Data

- **Two Real-World German Projects:** Data from ecological operations and Stefan Block's biochar processor north of Hamburg.
  - Stefan Block has been one of the earliest customers, operational for nearly two years.
- **Removal Activity API:** Includes mass balances, locations, distances, and emissions for each tracked event.
  - 395 registered carbon removal sinks (individual application events).
  - Total of roughly 7,000 tons of CO2 removed.
  - Events tracked manually by operators on-site using their software.
- **Machine Data:** Approximately 60 megabytes of sensor data from roughly 500 sensors.
  - Captures pressure, temperature, screw speeds, and other parameters from pyrolysis reactors.
- **Documentation:** Images and videos including yard surveillance footage of biomass pickup and application footage of biochar being applied to soil.
- **Fake Data:** Intentionally injected into the dataset for detection testing.

##### Suggested Solution Directions

- **Consumer Experience:** Create a step-by-step journey explaining the removal activity.
  - Show where biomass comes from and what the land looked like historically.
  - Display tracking data, weights, and explain the process in understandable terms.
  - Make it accessible to people who have never heard of carbon removal.
  - Current website version exists but is not yet perfect for general audiences.
- **Data Validation:** Build a solution that validates different data sources against each other.
  - Compare manual input data with evidence pieces (documents, PDFs, images).
  - Flag inconsistencies across data sources.
  - Detect injected fake data.

##### Support & Resources

- Large support team available in the office next to the building.
- Team available for the next few hours to assist.
- Cursor Pro+ accounts with major model providers will be provided to each team.

---

### Challenge 2: Autonomy in the Fields -- Life Saving Computer Vision

- **Sponsor:** CLAAS
- **Grand prize:** 1000 Euro for team

#### Meeting Notes: Agricultural Machine Sensor Technology and Autonomy (2026-04-10)

##### Current Sensor Deployment

- The machine currently has 100 sensors integrated throughout the system.
- Sensors detect both basic operational metrics (e.g., header height above ground) and more complex data about harvest quality.

##### Sensor Types and Capabilities

- **Vision sensors** detect walls and obstacles in the environment.
- **Trailer monitoring sensors** enable autonomous automatic unloading by tracking trailers driving alongside the machine.
- **Quantity monitoring sensors** measure the amount of material being harvested, with potential to improve throughput.
- **Quality assessment sensors** analyze the quality of harvested crops (such as corn) and inform parameter adjustments to optimize machine performance.

##### Current Autonomous Features

- The machine can perform autonomous unloading operations by following trailers with sensors.
- Real-time quality data enables automatic adjustment of machine parameters based on harvest conditions.

##### Future Goals

- The next challenge is to achieve full autonomy by replacing both the driver's eyes and brain.
- The focus is on enabling the machine to comprehensively see and understand its surrounding environment.

#### Additional Notes from Presentation

- They have LIDAR + Cameras. They want to replace humans in the process.
- **The actual challenge:**
  - They provide 500 MB image and video data.
  - First of all: **person detection**.
  - Following ideas: get as much information from video/image as possible.
  - Keyword: **scene understanding**.
  - They recommend the data science platform Kaggle (CAGGLE = CLAAS + Kaggle).

- **Goal:**
  - Good, effective, efficient: Bounding box detector.
  - Add layers to improve solutions.

---

### Challenge 3: Open Challenge

- **Grand prize:** 1000 Euro

#### Details

- Anything related to agriculture and/or climate.
- Build something people want, something useful, or both.
- Talk to mentors and sponsors, they might give you some pointers.

---

## Our Pick and Idea

*(To be determined)*
