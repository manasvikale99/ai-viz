[README(2).md](https://github.com/user-attachments/files/28433450/README.2.md)
# AI & The Job Market

An interactive data visualization exploring how artificial intelligence reshapes employment across 37 US job sectors, 2020–2026. Built with D3.js, embedded inline — no server, no build step.

**Live demo:** [manasvikale99.github.io/ai-viz](https://manasvikale99.github.io/ai-viz/)

---

## What It Shows

### Section 1 — US Job Postings Index (2020–2026)
A line chart of the Indeed job postings index (seasonally adjusted, February 2020 = 100) spanning the COVID crash, the recovery, and the AI era. Four headline stat cards count up on load.

- **Baseline** dashed at 100 (Feb 2020 = baseline per Indeed's methodology)
- **COVID crash** annotated at April 2020
- **AI acceleration** annotated at January 2023
- **AI release bars** stacked below the x-axis — Anthropic (orange) on top, OpenAI (blue) below — one bar per model per month; hover to see model names
- **Crosshair tooltip** snaps to the nearest monthly data point on hover
- Line **draws itself** on scroll-in via stroke-dashoffset animation (1.8 s)

### Section 2 — Sector Heatmap
A 37 × 75 grid (sectors × months) showing each sector's job posting index as a sequential colour scale from light beige (#EAE0CE, low postings) to dark navy (#1A3A52, high postings), domain 60–175.

- **Filter buttons** narrow the view to Knowledge Work / Care & Service / Tech & Engineering
- **Staggered cell fade-in** on first scroll-into-view (delay = sector_index × 12 ms + month_index × 3 ms)
- **Hover** scales the cell 1.4× from its centre; tooltip shows sector, month, index, and above/below-baseline status
- **Click any cell** to jump to Section 3 with that sector pre-selected
- **Index explainer** above the grid; CSS gradient colour key below (outside the SVG)

### Section 3 — Sector Detail
Drill into any of the 37 sectors via alphabetical pill buttons (also reachable by clicking the heatmap or scatter plot).

- **Animated line chart** redraws with a 1.8 s stroke-dashoffset animation on each sector change
- **AI-era shading** — light blue rectangle covering January 2023 onwards
- **AI release bars** below the chart, same colour coding as Section 1
- **Right panel** cross-fades the sector name and updates instantly:
  - Typical job titles (from O\*NET crosswalk)
  - Current index — green (≥ 100) or red (< 100)
  - Automation risk score out of 100, with a CSS-animated fill bar (0.6 s transition)
  - Typical job zone label (Entry / Mid / High Skill / Expert)

### Section 4 — Automation Risk vs. Job Market Strength
A bubble chart placing all 37 sectors on two axes simultaneously.

- **X axis** — current job postings index
- **Y axis** — automation risk score (0–100)
- **Bubble radius** — proportional to √(matched O\*NET occupation count), scaled 5–22 px
- **Colour** — green if current index ≥ 100, red if below baseline
- **Dashed vertical** at x = 100 with quadrant labels
- Eight key sectors are always labelled
- Bubbles **spring-animate** into view on scroll (staggered by index)
- Hover grows bubble 12 % and adds a drop shadow; click jumps to Section 3
- **Sidebar** lists every below-baseline sector ranked by automation risk score

---

## Data Sources

| Dataset | Source | Coverage |
|---|---|---|
| `aggregate_job_postings_US.csv` | [Indeed Hiring Lab](https://www.hiringlab.org/) | Feb 2020 – Apr 2026, daily → monthly average |
| `job_postings_by_sector_US.csv` | Indeed Hiring Lab | 37 sectors × same date range |
| `OpenAI_Anthropic_Models.xlsx` | [Epoch AI Notable AI Models](https://epochai.org/data/notable-ai-models) | 128 models, 2020–2026 |
| `Degree_of_Automation1.xlsx` | [O\*NET Work Context data](https://www.onetonline.org/) | 894 occupations, automation context scores |
| `sectorjobtitleexamples.csv` | Crosswalk mapping | 47 sector → job-title pairs |

### Data Processing Notes

- **Aggregate index**: daily records filtered to `variable = "total postings"`, averaged by month using the seasonally-adjusted `indeed_job_postings_index_SA` column.
- **Sector index**: same filter on `variable = "total postings"`, averaged by `(sector, month)`. Leading/trailing quote characters stripped from sector names.
- **AI models**: Excel serial dates converted via `new Date((serial − 25569) × 86400 × 1000)`. Records before 2020 excluded. Organisation determined from the `Organization` column.
- **Automation scores**: O\*NET `Context` scores (1–70) keyword-matched against each sector's representative job titles. Unmatched sectors fall back on a broad first-word match. Scores normalised: `score / 70 × 100`. Fallback of 50 for sectors with zero matches (Banking & Finance, Loading & Stocking).
- **Sector name reconciliation**: three Indeed sectors required explicit mapping to O\*NET sector names — `IT Infrastructure, Operations & Support` → `IT Operations & Helpdesk`, `IT Systems & Solutions` → `IT Operations & Helpdesk`, `Data & Analytics` → `Information Design & Documentation`.

---

## Design

| Token | Value |
|---|---|
| Background | `#F7F5F0` |
| Surface | `#FFFFFF` |
| Border | `#E8E4DC` |
| Text | `#1A1714` |
| Anthropic orange | `#C8714A` |
| OpenAI blue | `#5B8DBF` |
| Above-baseline green | `#4A7A5A` |
| Below-baseline red | `#B85040` |
| Heatmap light | `#EAE0CE` |
| Heatmap dark | `#1A3A52` |
| Headings | Georgia serif |
| UI / labels | Helvetica Neue |

Sections fade up on scroll via `IntersectionObserver`. Charts are responsive (`viewBox` + `width: 100%`) and re-render on window resize via `ResizeObserver` with a 220 ms debounce.

---

## Technical

- **Single file** — `index.html` (~156 KB), fully self-contained; open locally in any browser, no server needed
- **Dependencies** — D3.js 7.9.0 from cdnjs only; zero npm packages at runtime
- **All data embedded inline** — no `fetch()` calls; works offline
- **Build toolchain** — Node.js scripts processed the five source files and generated the five inline JS constants (`AGGREGATE_DATA`, `SECTOR_DATA`, `AI_MODELS`, `AUTOMATION_CROSSWALK`, `SECTOR_TITLES`)

---

## Sector Categories

| Knowledge Work (14) | Care & Service (12) | Tech & Engineering (11) |
|---|---|---|
| Accounting | Cleaning & Sanitation | Architecture |
| Administrative Assistance | Community & Social Service | Arts & Entertainment |
| Banking & Finance | Customer Service | Civil Engineering |
| Data & Analytics | Education & Instruction | Electrical Engineering |
| Human Resources | Food Preparation & Service | Industrial Engineering |
| IT Infrastructure, Operations & Support | Hospitality & Tourism | Installation & Maintenance |
| IT Systems & Solutions | Medical Information | Loading & Stocking |
| Legal | Medical Technician | Logistic Support |
| Management | Nursing | Production & Manufacturing |
| Marketing | Pharmacy | Retail |
| Media & Communications | Physicians & Surgeons | Sales |
| Project Management | Security & Public Safety | |
| Scientific Research & Development | | |
| Software Development | | |
