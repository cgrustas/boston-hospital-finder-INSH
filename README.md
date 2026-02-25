# Boston Hospital Finder 🏥

> **This app also serves as a demo for your final project.** If you are interested in building an app or agent using open datasets, this is an example of what that can look like — a working civic tech tool built entirely with public data, no API keys, and AI-assisted development.

A web app that helps users find hospitals near any US ZIP code, built with open data from the [City of Boston](https://data.boston.gov). This project is used as a hands-on prompt engineering exercise — you will use AI to add real features to a working codebase.

---

## What the app already does

- Accepts a ZIP code and search radius from the user
- Converts the ZIP to coordinates using the free [Zippopotam API](https://api.zippopotam.us)
- Fetches hospital location data from Boston's open data portal
- Calculates distance to each hospital using Euclidean distance — simple and accurate enough for a small local area like Boston
- Displays matching hospitals in a sidebar sorted by distance

---

## How to get started

### Step 1 — Fork the repo
Click **Fork** at the top right of this GitHub page. This creates your own personal copy — you can break things freely without affecting anyone else.

### Step 2 — Explore the datasets first
Before writing any code, open both datasets and spend a few minutes looking at the raw data:

- **Hospital Locations** — [view dataset](https://data.boston.gov/dataset/hospital-locations/resource/6222085d-ee88-45c6-ae40-0c7464620d64)
- **Climate Ready Boston Social Vulnerability** — [view dataset](https://data.boston.gov/dataset/climate-ready-boston-social-vulnerability)

Try to answer these questions:
- What columns does each dataset have?
- What does one row represent in each dataset?
- Which column in the hospital dataset tells you where the hospital is located geographically?
- The vulnerability dataset has multiple rows per neighborhood — why do you think that is?
- Which column appears in **both** datasets and could be used to connect them?

Understanding the data before writing code is one of the most important habits in data work. If you misread a column or assume the wrong unit, your results will be wrong even if your code is correct.

### Step 3 — Open the browser editor
On your forked repo press the **period key (`.`)** on your keyboard. This opens a full VS Code environment in your browser with no install needed.

### Step 4 — Read the code first
Before asking AI for help, spend 5–10 minutes reading `index.html`. Try to answer:
- What does `parseCSV()` do and why is a custom parser needed?
- What does the Zippopotam API return and how is it used?
- Find all `// TODO` comments — what do they tell you about where work needs to be done?

Understanding the big picture before prompting AI will make your prompts significantly more effective.

---

## Your tasks

### 🟢 Ticket 1 — Add a Leaflet Map *(in-class exercise)*
**What:** Add an interactive map using [Leaflet.js](https://leafletjs.com) that shows a pin for each hospital result. Clicking a sidebar card should pan the map to that hospital.

**Why it matters:** Leaflet is the most popular open-source mapping library used in civic tech, public health, and journalism. It requires no API key and no account.

**What you need to touch:** HTML (map div, script/link tags), CSS (map sizing), JavaScript (initMap, makeMarker, marker loop, fit bounds, card click handler)

**Find:** All `// TODO: Ticket 1` comments — they mark every spot you need to change.

<details>
<summary>💡 Show hint</summary>

Try this prompt with your full `index.html` pasted in:

```
I am adding Leaflet.js to my hospital finder app. Here is my full code.
Find all // TODO: Ticket 1 comments and complete them to:
1. Load Leaflet CSS and JS from cdnjs.cloudflare.com
2. Add a map div that fills the left side of the screen
3. Initialize a Leaflet map centered on Boston [42.3601, -71.0589] at zoom 12
4. Add OpenStreetMap tiles
5. Create a blue circle marker for each hospital and fit the map to show them all
6. Make sidebar cards clickable to pan the map to that hospital
Do not change anything outside the TODO comments.
```
</details>

---

### 🔴 Ticket 2 — Vulnerability Breakdown Popup *(bonus assignment)*
**What:** When a user clicks a hospital pin on the map, show a popup with a neighborhood vulnerability breakdown — percentage of elderly residents, children under 5, low income households, people with disabilities, and limited English speakers. This data comes from a second Boston dataset: [Climate Ready Boston Social Vulnerability](https://data.boston.gov/dataset/climate-ready-boston-social-vulnerability).

**Why it matters:** This is a real data join — connecting two datasets on a shared field (`NEIGH` in hospitals, `Name` in vulnerability). It also teaches Leaflet interactivity and the idea that good data tools let users go deeper than a summary.

**Your job:** Choose at least 2 factors from the vulnerability dataset to show in the popup. Think about which ones are most relevant to hospital access — for example, elderly residents and people with disabilities may have greater need for nearby healthcare than other groups.

Available columns:

| Column | What it measures |
|---|---|
| `OlderAdult` | Residents over 65 |
| `TotChild` | Children under 5 |
| `Low_to_No` | Low or no income households |
| `TotDis` | People with disabilities |
| `LEP` | Limited English proficiency |

Note: `MedIllnes` is intentionally excluded — it is a modeled estimate with known double-counting issues, not a clean census count.

**How percentages are calculated:**
For each neighborhood, sum the chosen factors across all census tract rows, then divide by total population:
```
elderly % = (total OlderAdult / total POP100_RE) * 100
```

**Find:** All `// TODO: Ticket 2` comments.

<details>
<summary>💡 Show hint</summary>

Try this prompt:

```
I want to add a vulnerability breakdown popup to my hospital finder map.
Here is my full code. Complete all // TODO: Ticket 2 comments to:
1. Fetch the vulnerability CSV from VULN_CSV and parse it
2. Group rows by neighborhood Name — note there are multiple census tract rows per neighborhood
3. Sum the population and each factor across all tracts, then calculate percentages:
   elderly, children under 5, low income, disabilities, limited English
4. Store the 5 percentages in vulnByNeigh keyed by uppercase neighborhood name
5. Build a makePopupHTML(hospital, vuln) function showing each factor as a % in a small table
6. Bind the popup to each Leaflet marker
7. Load vulnerability data in parallel with hospital data using Promise.all
Do not change anything outside the TODO comments.
```
</details>

---

## Prompt engineering guidelines

**1. Always paste the full file**
Never paste just a snippet — AI needs the full `index.html` to understand the context.

**2. Reference the TODO comments directly**
Tell AI exactly which TODO comments to complete. This keeps it focused and prevents it from rewriting unrelated code.

**3. Use this system prompt at the start of every session:**
```
Respond with clean, well-written code. Only add comments that belong in production.
Focus only on the task I describe. Do not make unnecessary changes to other parts of the code.
```

**4. Coach it step by step**
If the AI gives you code with a bug, describe exactly what's wrong and ask it to fix only that part. Give it the error message from the browser console (F12).

**5. Ask AI to explain before changing**
If you don't understand what the AI plans to do, ask: *"Before you change anything, explain what you plan to do and why."*

---

## Understanding the data

| Dataset | Source | Key fields used |
|---|---|---|
| Hospital Locations | [data.boston.gov](https://data.boston.gov/dataset/hospital-locations) | NAME, AD (address), NEIGH, LOCATION (lat/lng) |
| Climate Vulnerability | [data.boston.gov](https://data.boston.gov/dataset/climate-ready-boston-social-vulnerability) | Name, POP100_RE, OlderAdult, TotChild, Low_to_No, TotDis, LEP |

The two datasets are joined on **neighborhood name** — `NEIGH` in hospitals matches `Name` in vulnerability. Note the vulnerability data is from 2016 census estimates. Real-world analysis would use more recent data.

---

## About
Built with plain HTML, CSS, and JavaScript — no frameworks, no build tools, no API keys.
Uses [Leaflet.js](https://leafletjs.com) for maps and [OpenStreetMap](https://www.openstreetmap.org) tiles.
Hosted on GitHub Pages.
