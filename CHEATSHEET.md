# Career-Ops Cheat Sheet

## End-to-End Pipeline (4 chunks)

Run each step on its own; outputs from one feed the next.

### 1. Discover new jobs → `scan`
```
/career-ops scan
```
Hits Greenhouse/Ashby/Lever APIs + Playwright on `tracked_companies` from `portals.yml`, filters by `title_filter`, dedups against `data/scan-history.tsv` + `data/applications.md` + `data/pipeline.md`, and appends new postings to the **Pendientes** section of `data/pipeline.md`.

**Output:** new `- [ ] {url} | {company} | {title}` lines in `data/pipeline.md`.

### 2. Grade them for fit → `pipeline`
```
/career-ops pipeline
```
Reads each pending URL, extracts the JD via Playwright, runs the A–G evaluation, writes a report to `reports/{NNN}-{slug}-{date}.md`, updates `data/applications.md` with the score, and moves the URL to **Procesadas**. **Also auto-generates a tailored CV PDF in `output/` for any role scoring ≥ 3.0** (so for most users, step 3 is already done here).

**Output:** numbered reports in `reports/`, scored rows in `data/applications.md`, CV PDFs in `output/` for good fits.

### 3. Apply to each one → `apply`
```
/career-ops apply
```
Per-application assistant: detects the open application form (Playwright or screenshot), loads the matching report from `reports/`, generates copy-paste answers for every question using your proof points and prior Section G drafts. **Stops before submit — you click apply.** On confirmation, updates `data/applications.md` status to `Applied`.

**Output:** drafted answers per form, status flipped to `Applied` in tracker.

> **Tip:** Between steps 2 and 3, scan `data/applications.md` (or run `/career-ops tracker`) and only proceed to `apply` for rows scoring ≥ 4.0 — the project's ethical-use policy explicitly discourages applying below that threshold.

---

## Actions (`/career-ops <mode>`)

| Action | What it does |
|--------|--------------|
| `pipeline` | Processes pending URLs queued in `data/pipeline.md` through full evaluation. |
| `oferta` | Evaluates a single job offer with A–G scoring and writes a report. |
| `ofertas` | Compares and ranks multiple evaluated offers head-to-head. |
| `contacto` | Finds LinkedIn contacts at the company and drafts outreach messages. |
| `deep` | Runs deep company research (financials, culture, team, signals). |
| `interview-prep` | Builds a company/role-specific interview prep dossier and STAR stories. |
| `pdf` | Generates an ATS-optimized CV PDF tailored to a target role. |
| `latex` | Exports the tailored CV as a LaTeX/Overleaf `.tex` file. |
| `training` | Evaluates a course or certification against your career goals. |
| `project` | Evaluates a portfolio project idea for career impact. |
| `tracker` | Shows application status overview from `data/applications.md`. |
| `apply` | Live application assistant — fills forms and drafts answers (never submits). |
| `scan` | Zero-token scan of Greenhouse/Ashby/Lever portals for new offers. |
| `batch` | Runs batch evaluations in parallel workers across many JDs. |
| `patterns` | Analyzes rejection patterns to improve targeting. |
| `followup` | Calculates follow-up cadence and tracks past follow-ups. |

## Job Data Locations

| Path | Purpose |
|------|---------|
| `data/pipeline.md` | Inbox of pending job URLs waiting to be evaluated. |
| `data/applications.md` | Master tracker of every evaluated/applied role with status and score. |
| `data/scan-history.tsv` | Dedup history of jobs already seen by the portal scanner. |
| `data/follow-ups.md` | Log of follow-ups sent per application. |
| `jds/` | Local copies of job descriptions referenced as `local:jds/{file}`. |
| `reports/` | Per-offer evaluation reports (`{###}-{slug}-{date}.md`) with A–G blocks. |
| `output/` | Generated artifacts: CV PDFs/HTML and report PDFs (gitignored). |
| `interview-prep/story-bank.md` | Accumulated STAR+R stories across all evaluations. |
| `interview-prep/{company}-{role}.md` | Company-specific interview intel reports. |
| `batch/tracker-additions/` | TSV rows queued for merge into `applications.md` after batch runs. |

## Config Files

| File | Purpose |
|------|---------|
| `cv.md` | Canonical source-of-truth CV in markdown — all CV generation reads from here. |
| `config/profile.yml` | Personal info, target roles, salary range, location, language preferences. |
| `modes/_profile.md` | User-specific archetypes, narrative, scoring weights, deal-breakers (never overwritten by updates). |
| `modes/_shared.md` | System-wide shared logic for all modes (do NOT edit for personalization). |
| `article-digest.md` | Compact proof points and metrics from your portfolio for evaluations. |
| `portals.yml` | Companies and search keywords for the portal scanner. |
| `templates/cv-template.html` | HTML/CSS template used by `generate-pdf.mjs` for PDF CVs. |
| `templates/cv-template.tex` | LaTeX/Overleaf template used by `generate-latex.mjs`. |
| `templates/states.yml` | Canonical application status values (Evaluated, Applied, etc.). |
| `templates/portals.example.yml` | Example portal config to seed `portals.yml`. |
| `config/profile.example.yml` | Example profile to seed `config/profile.yml`. |
| `modes/_profile.template.md` | Template copied to `modes/_profile.md` on first run. |
