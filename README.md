# Job Agent — Command Deck (Frontend Dashboard)

A React dashboard for the job automation agent backend: everything the API
supports (auth, preferences, master resume, job ingestion & matching,
resume generation, applications, HR outreach, and the learning loop) in one
UI, built with Vite + React + React Router. Plain CSS (no component
library), talking to the FastAPI backend over `fetch`.

## Running it

Requires Node 18+. The backend (from the other project) needs to be running
first — this is a pure client that expects an API at `http://localhost:8000`
by default.

```bash
cd job_agent_frontend
cp .env.example .env   # only needed if your backend isn't at localhost:8000
npm install
npm run dev
```

Open the URL Vite prints (typically http://localhost:5173). Register an
account, then work through the sidebar in order: master resume → jobs →
applications → outreach → learning.

`npm run build` produces a static `dist/` you can serve with any static
host or drop behind the same reverse proxy as the API in production.

## Pages

| Page | What it does |
|---|---|
| **Overview** | Dashboard stats, activity timeline, and the top learning recommendation |
| **Master resume** | Upload raw resume text, parse it, review/correct the parsed JSON before it's used anywhere |
| **Jobs & matches** | Check job-source status, trigger `POST /ingestion/run`, add a job manually, generate a tailored resume per match, download the PDF, create an application |
| **Applications** | Track status through the pipeline, record the Q&A used on each one |
| **Contacts & outreach** | Log HR contacts, draft a cold email (template or Claude-assisted, per your backend config), send it, see replies as they're classified |
| **Learning & answer bank** | The insights report (skill/email performance, recommendations), recurring questions, and the verify-before-reuse answer bank |
| **Preferences** | Target titles/locations, allowed job sources, tracked companies for Greenhouse/Lever, operating mode |

## Design notes

Dark "operations desk" theme rather than a generic SaaS-dashboard look:
Spectral (serif) for headings, IBM Plex Sans for UI text, IBM Plex Mono for
raw/JSON content (like the parsed-resume editor). Status is color-coded
(teal = positive, rust = negative, amber = primary actions/attention,
violet = insight callouts) rather than decorative. Cards use a left accent
border instead of drop shadows.

## Deploying alongside the backend

See `DEPLOYMENT.md` in the `job_agent` (backend) repo for the full Railway
walkthrough — it covers both services plus the managed MySQL database. This
repo's `Dockerfile` and `railway.json` are already set up for that path
(multi-stage build, `VITE_API_BASE_URL` as a build-time arg, nginx serving
the static output with SPA fallback routing).

## Known limitations

- No token refresh — the JWT is stored in `localStorage` and used until it
  expires (`ACCESS_TOKEN_EXPIRE_MINUTES` on the backend, default 24h), at
  which point API calls will start failing with 401s and you'll need to log
  in again. Fine for personal use, worth hardening before multi-day sessions.
- Built and screenshot-tested against every real endpoint shape with mocked
  API responses (Playwright), and against the real backend logic via the
  same schemas the backend's own tests use — but not yet run end-to-end
  against a live backend + live MySQL in the same environment (this
  sandbox can't run MySQL), so do one full manual pass after deploying both
  together.
