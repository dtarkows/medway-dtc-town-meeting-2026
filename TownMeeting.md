# Medway Town Meeting Archive — Project Handoff

**Last updated:** May 5, 2026  
**Maintained by:** Dave Tarkowski (dtarkows@gmail.com)  
**Live site:** https://townmeeting.medwaydemocrats.org  
**GitHub repo:** https://github.com/dtarkows/medway-dtc-town-meeting-2026  
**GitHub Pages fallback:** https://dtarkows.github.io/medway-dtc-town-meeting-2026

---

## What This Is

A static website and slide deck covering Medway, MA Town Meeting warrants from 2016–2026. Built for the Medway Democratic Town Committee to support an informal pre-meeting voter discussion group. The site includes per-article warrant summaries, budget trend charts, analysis documents, and a discussion slide deck.

---

## Folder Structure

```
Town Meeting/
├── out/                         ← Website root (this is what's deployed)
│   ├── index.html               ← Main landing page
│   ├── trends.html              ← Budget trends 2016–2026
│   ├── TownMeeting.md           ← This file
│   ├── .nojekyll                ← Required for GitHub Pages
│   ├── CNAME                    ← Custom domain: townmeeting.medwaydemocrats.org
│   ├── .gitignore
│   ├── 2016–2025/               ← Per-year HTML pages (generated)
│   ├── 2026/
│   │   ├── annual_town_meeting_2026.html
│   │   ├── special_town_meeting_2026.html
│   │   └── Medway 2026 Town Meeting Discussion.pdf
│   ├── analysis/
│   │   ├── BESS PILOT Analysis.html
│   │   ├── BESS PILOT Risk Analysis.html
│   │   └── Winthrop St P&S Analysis.html
│   ├── data/
│   │   ├── 2016_atm.json        ← Authoritative data source for all pages
│   │   ├── ...
│   │   ├── 2026_atm.json
│   │   └── 2026_stm.json
│   └── tools/
│       └── gen.py               ← Regenerates all HTML from JSON data
│
└── data/
    └── 2026/                    ← Source PDFs for 2026
        ├── ATM-Warrant_2026-1.pdf
        ├── Warrant_STM_5-11-26.pdf
        └── FinCom-Report-to-2026-ATM-1.pdf
```

---

## Data Pipeline

JSON files in `out/data/` are the single source of truth. Each file follows this schema:

```json
{
  "meta": { "meeting_type", "date", "year", "theme_id", "source_pdf" },
  "articles": [
    {
      "num": 1,
      "title": "...",
      "summary": "...",
      "category": "Financial|Capital / DPW|Schools|Technology|...",
      "amount": 12345,
      "sb": "Approve|To Be Determined|...",
      "fc": "Approve|To Be Determined|...",
      "text": "Full warrant article text..."
    }
  ]
}
```

**To regenerate all HTML pages after any JSON change:**
```bash
cd "/path/to/Town Meeting/out/tools"
python3 gen.py
```

This regenerates all year pages plus `index.html` and `trends.html`.

---

## Slide Deck

**Source:** `Town Meeting/` (root, not in `out/`)  
**Build script:** `[outputs folder]/pptx_work/build.js`  
**Output PPTX:** `Town Meeting/Medway 2026 Town Meeting Discussion.pptx`  
**Output PDF:** `out/2026/Medway 2026 Town Meeting Discussion.pdf`

**To rebuild the slide deck:**
```bash
cd [outputs folder]/pptx_work
node build.js
```

**To regenerate the PDF after rebuilding:**
```bash
soffice --headless --convert-to pdf \
  "Town Meeting/Medway 2026 Town Meeting Discussion.pptx" \
  --outdir "Town Meeting/out/2026/"
```

**Slide inventory (19 slides):**
1. Title
2. STM — What It Is
3. STM — All Articles
4. ATM — What It Is
5. FY2027 Budget Overview (FinCom)
6. Revenue
7. Spending by Category
8. Article 7 — Capital Projects
9. Notable Findings
10. Key Themes
11. ATM — Warrant at a Glance
12. ATM — Routine Annual Business
13. ATM — Two Items Stand Out
14. BESS PILOT — What Is It?
15. BESS PILOT — The Numbers
16. BESS PILOT — Questions
17. Winthrop St — What Is It?
18. Winthrop St — Questions
19. Closing

**Design system:** pptxgenjs · NAVY=1E3A5F · TEAL=2A7A6A · AMBER=B8860B · LIGHT=F0F4F8  
**No emoji** — all decorative icons are colored shapes to avoid blank boxes on Mac/LibreOffice.

---

## GitHub Pages Deployment

The `out/` folder is the git repository root.

**To deploy updates:**
```bash
cd ~/Library/CloudStorage/OneDrive-Personal/Documents/claude/Town\ Meeting/out
git add -A
git commit -m "describe what changed"
git push
```

GitHub Pages updates within ~1 minute of a push.

**Custom domain:** `townmeeting.medwaydemocrats.org`  
DNS is a CNAME record at HostGo (via cPanel) pointing `townmeeting` → `dtarkows.github.io`.  
SSL is provisioned automatically by GitHub.

---

## Adding a Future Year (e.g., 2027)

1. Place the warrant PDF in `data/2027/`
2. Parse the PDF and create `out/data/2027_atm.json` (and `2027_stm.json` if applicable)
3. Update `out/tools/gen.py` — add the new year to the `FILENAMES` dict and `manifest.json`
4. Run `python3 gen.py` to regenerate all pages
5. Update `index.html` to add the new year card and analysis section
6. Update `trends.html` if new trend data is available
7. Commit and push

---

## Known Issues / Notes

- `out/2026/lu40bcs64.tmp` — a LibreOffice temp file that can't be deleted due to filesystem permissions. Harmless; ignored by `.gitignore`.
- The `build.js` outputs folder path is session-specific and will change in a new Cowork session. Re-read the file path from the system at the start of any slide-editing session.
- The FinCom report (page references): FinCom narrative pp. 3–5, warrants pp. 6–30, fiscal year summary tables pp. 31–39, subcommittee reports pp. 40–54, glossary pp. 55–60.
- MRA subcommittee report references "Article 16: Free Cash Transfer $215K" — this appears to be a draft numbering error. In the final warrant, Article 16 is the BESS PILOT.

---

## Key Context for Discussions

- **Meeting date:** May 11, 2026 — STM at 7:00 PM, ATM at 7:30 PM, Medway High School
- **Audience:** Informal civic discussion group, mixed familiarity with town finance
- **Both the Select Board and Finance Committee recommend Approve on all 26 ATM articles and all 8 STM articles**
- **Two unique ATM items:** Art. 1 (Winthrop St land purchase, $4.85M) and Art. 16 (BESS PILOT, battery storage revenue agreement)
- **FY2027 budget:** $74.4M total appropriation, +2.9% over FY2026
- **Structural issue to flag:** Chapter 70 state aid chronically underfunds Medway's actual education costs
