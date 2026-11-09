# Harambee Hub - Week 1 Frontend Plan (DRAFT FOR TEAM APPROVAL)

Frontend only: React + JavaScript. No Flask, database or real backend this week. Phase 2 (Flask backend) and Phase 3 (real auth) come later, so everything we build now must be easy to swap over.

Nothing below is final until all six members have read it and agreed in the Day 1 meeting.

---

## 0. What changed from the first draft (and why)

| # | Change | Why |
|---|--------|-----|
| 1 | Data comes through `services/api.js` using `fetch` (not imported straight from `mockData.js`) | Canvas rubric asks for a dynamic fetch plus loading/error/empty states. Direct imports have none. Also makes the Phase 2 swap a one-file change. |
| 2 | Work is split into feature slices, not layers | Every member must understand the whole app, and layer splits block everyone on the shared-UI owner. |
| 3 | Mock data shape is designed by the whole team on Day 1 | If each person invents their own shape, integration breaks. It should look like future database rows. |
| 4 | Integration happens daily, not on Day 5 | Small PRs merged often avoid a big-bang merge disaster. |
| 5 | Added `develop` branch (Gitflow) and consistent commit messages | The CCI brief requires Gitflow, consistent commits/PRs and a README. |
| 6 | Added CI (GitHub Actions), basic tests, pagination, human-readable dates | Carried from the CCI brief; cheap to start now, painful to add later. |
| 7 | Added Canvas deliverables to Day 6-7: slide deck (5-7 slides), 5-10 min video, written reflection, peer response | They were missing from the first draft and are graded. |
| 8 | Trimmed the folder tree (removed `responsive.css`, kept essentials) | Fewer empty files, fewer places to get lost. |

---

## 1. Questions to confirm BEFORE Day 1 ends

Ask the facilitator in writing and keep the answer.

1. **Does a local/mock data source count as the "external API" for Phase 1?** The Canvas rubric says "dynamic fetch to external API". Our plan uses `fetch` against a local source (see section 4), but we need confirmation it is acceptable.
2. **Is Phase 1 graded per team or per person?** Canvas says "you are responsible for building the entire React frontend" and takes one repo link. Confirm who submits and how individual contribution is judged.
3. **How do the CCI brief requirements (JWT, SendGrid, Cloudinary, CI/CD, 60% backend coverage) map to the three phases?** Most are backend and belong to Phases 2-3. We only carry forward what applies to the frontend now.

If the answer to (1) is "no, use a real public API", fall back to Plan B (section 4).

---

## 2. Rules for the whole team

- Everyone must be able to explain every page, not only their own. Presentation questions can go to anyone.
- Write your own code. AI tools may explain and review, but you must be able to defend every line.
- Mock authentication is for UI demonstration only. It provides no real security.
- Work only on tasks that are on the Trello board. If it is not on Trello, it is not assigned.
- No direct pushes to `main` or `develop`.

---

## 3. Technologies

React, JavaScript, HTML, CSS, React Router, React Hooks (`useState`, `useEffect`, `useContext`, one custom hook), `fetch`, Lucide React icons, Vite, Vitest + React Testing Library (basic tests), GitHub Actions (CI).

Styling: pick ONE approach on Day 1 (plain CSS with shared variables, or a framework such as Tailwind) and stick to it. Mixing approaches causes style clashes.

---

## 4. Data approach

### Plan A (preferred, if facilitator agrees)
- A small `db.json` served by json-server (`npm run api`), OR a static JSON file in `public/data/` fetched by `fetch`.
- Components never import data directly. They call functions in `src/services/api.js`, such as `getCampaigns()`, `getCampaignById(id)`, `createPledge(data)`.
- A custom hook `useFetch` returns `{ data, loading, error }`. Every page shows a loader, an error message, or an empty state.
- If we use a static JSON file (works on Netlify/Vercel), writes like "create campaign" and "make pledge" are held in React state for the session. They will not persist after a refresh. State this honestly in the demo and README.

### Plan B (if mock data is not allowed)
Pick a real public API for the data (e.g. country, news or currency data to enrich campaign pages), and keep campaigns/pledges as mock data. Confirm scope with the facilitator.

### Day 1 team exercise: design the data shape (do not skip)
Everyone together, on paper or a shared doc. Decide:
- Which entities exist (hint: users, campaigns, pledges, and what else?).
- The fields of each and their types.
- How they link (which id points to which? who owns a campaign? which campaign and user does a pledge belong to?).
- Allowed status values (for example for pledges: pending / paid / overdue) written as a fixed list.
- Money as a plain number in one currency; dates as ISO strings (formatted for display only).
- Make it look like database rows. Phase 2 will turn it into tables.
Write the agreed shape in `docs/data-shape.md` and treat it as a contract. Changing it later needs the whole team.

---

## 5. Folder structure

```
harambee-hub/
|-- public/
|   `-- images/
|-- docs/                      # data-shape.md, wireframes, screenshots
|-- .github/
|   |-- workflows/ci.yml       # runs lint + tests + build on PRs
|   `-- pull_request_template.md
|-- src/
|   |-- assets/                # images, icons
|   |-- components/
|   |   |-- common/            # Button, Input, Modal, StatusBadge, Loader, ErrorMessage, EmptyState
|   |   |-- layout/            # Navbar, Sidebar, Footer, DashboardLayout
|   |   |-- campaigns/         # CampaignCard, CampaignList, CampaignProgress
|   |   |-- pledges/           # PledgeForm, PledgeTable
|   |   |-- dashboard/         # StatCard, ActivityFeed
|   |   `-- routing/           # ProtectedRoute
|   |-- pages/
|   |   |-- public/            # Home, Campaigns, CampaignDetails, NotFound
|   |   |-- auth/              # Login, Register
|   |   |-- organizer/         # OrganizerDashboard, CreateCampaign, ManageCampaigns, Contributions
|   |   |-- contributor/       # ContributorDashboard, MyContributions
|   |   `-- admin/             # AdminDashboard, ManageUsers
|   |-- services/api.js        # the ONLY place that talks to data
|   |-- hooks/useFetch.js
|   |-- context/AuthContext.jsx
|   |-- routes/AppRoutes.jsx
|   |-- utils/                 # formatDate.js, formatCurrency.js, validators.js
|   |-- styles/                # variables.css, global.css
|   |-- App.jsx
|   `-- main.jsx
|-- db.json                    # Plan A data (or public/data/)
|-- .gitignore
|-- package.json
`-- README.md
```

Essential: everything above except the items marked optional in the table below. Do not create empty files "just in case". Create a file when its owner starts working on it.

---

## 6. Team allocation (feature slices)

| Member | Slice | Summary |
|--------|-------|---------|
| M1 | Lead, routing and auth | Repo setup, Gitflow, Trello, router, mock auth (login with role pick), role-based route guard, CI workflow, merging PRs (Scrum master), README skeleton. |
| M2 | Shared UI and layout | Reusable components, navigation, dashboard layout, style variables, formatting utilities. First version due end of Day 2. |
| M3 | Public campaigns | Landing page, campaign list with search/filter and pagination, campaign details, progress bar. |
| M4 | Contributor and admin views | Pledge form, contributor dashboard, my contributions, pledge table, admin dashboard and user list. |
| M5 | Organizer experience | Organizer dashboard, create campaign form, manage campaigns, contributions view, stat cards, activity feed. |
| M6 | Data, states, QA and docs | Data layer (db.json, api.js, useFetch), loader/error/empty components, NotFound, testing, responsive and navigation checks, screenshots, final README, presentation material. |

### Review ring (every PR needs one reviewer who did not write it)
M1 reviews M2, M2 reviews M3, M3 reviews M4, M4 reviews M5, M5 reviews M6, M6 reviews M1. M1 also does the final merge check as Scrum master.

Keep the workload balanced: if someone is clearly ahead or behind by Day 3, move tasks on Trello openly.

---

## 7. File ownership table

| File | Purpose | Owner | Depends on | Used by | Essential? |
|------|---------|-------|------------|---------|-----------|
| main.jsx / App.jsx | App entry, providers | M1 | AuthContext, AppRoutes | - | Yes |
| routes/AppRoutes.jsx | All routes | M1 | all pages, ProtectedRoute | App | Yes |
| components/routing/ProtectedRoute.jsx | Redirect to /login if not logged in or wrong role (UI demo only) | M1 | AuthContext | AppRoutes | Yes |
| context/AuthContext.jsx | Holds mock user + role | M1 | - | Navbar, ProtectedRoute, Login | Yes |
| pages/auth/Login.jsx | Mock login, pick role | M1 | Input, Button, AuthContext, validators | AppRoutes | Yes |
| pages/auth/Register.jsx | Registration form with validation | M1 | Input, Button, validators | AppRoutes | Yes |
| styles/variables.css, global.css | Colors, spacing, typography | M2 | - | everything | Yes |
| components/common/Button.jsx | Reusable button | M2 | styles | all forms | Yes |
| components/common/Input.jsx | Labelled input with error text | M2 | styles | all forms | Yes |
| components/common/Modal.jsx | Confirm/dialog | M2 | styles | Manage pages | Optional |
| components/common/StatusBadge.jsx | Colored status label | M2 | styles | tables, cards | Yes |
| components/layout/Navbar.jsx | Top nav, role-aware links | M2 | AuthContext | App layout | Yes |
| components/layout/Sidebar.jsx | Dashboard side nav | M2 | AuthContext | DashboardLayout | Yes |
| components/layout/Footer.jsx | Footer | M2 | styles | App layout | Optional |
| components/layout/DashboardLayout.jsx | Wrapper for dashboard pages | M2 | Navbar, Sidebar | all dashboards | Yes |
| utils/formatDate.js, formatCurrency.js | Human-readable dates and money | M2 | - | cards, tables | Yes |
| utils/validators.js | Shared validation helpers | M2 | - | all forms | Yes |
| pages/public/Home.jsx | Landing page | M3 | CampaignCard, Button | AppRoutes | Yes |
| pages/public/Campaigns.jsx | Browse, search, filter, paginate | M3 | CampaignList, useFetch, api | AppRoutes | Yes |
| pages/public/CampaignDetails.jsx | One campaign + pledge entry point | M3 | CampaignProgress, PledgeForm, api | AppRoutes | Yes |
| components/campaigns/CampaignCard.jsx | Card for one campaign | M3 | CampaignProgress, formatters | CampaignList, Home | Yes |
| components/campaigns/CampaignList.jsx | Grid of cards + pagination | M3 | CampaignCard, EmptyState | Campaigns | Yes |
| components/campaigns/CampaignProgress.jsx | Progress bar (pledged vs collected vs goal) | M3 | formatCurrency | cards, details, dashboards | Yes |
| components/pledges/PledgeForm.jsx | Make a pledge with validation | M4 | Input, Button, validators, api | CampaignDetails | Yes |
| components/pledges/PledgeTable.jsx | Table of pledges with status | M4 | StatusBadge, formatters | MyContributions, Contributions | Yes |
| pages/contributor/ContributorDashboard.jsx | Contributor overview | M4 | DashboardLayout, StatCard, api | AppRoutes | Yes |
| pages/contributor/MyContributions.jsx | Own pledges and status | M4 | PledgeTable, api | AppRoutes | Yes |
| pages/admin/AdminDashboard.jsx | Platform stats | M4 | DashboardLayout, StatCard, ActivityFeed | AppRoutes | Yes |
| pages/admin/ManageUsers.jsx | User list (UI only) | M4 | DashboardLayout, api | AppRoutes | Yes |
| components/dashboard/StatCard.jsx | One statistic tile | M5 | styles | all dashboards | Yes |
| components/dashboard/ActivityFeed.jsx | Recent activity list | M5 | formatDate | Organizer, Admin dashboards | Optional |
| pages/organizer/OrganizerDashboard.jsx | Organizer overview | M5 | DashboardLayout, StatCard, CampaignProgress | AppRoutes | Yes |
| pages/organizer/CreateCampaign.jsx | Campaign creation form | M5 | Input, Button, validators, api | AppRoutes | Yes |
| pages/organizer/ManageCampaigns.jsx | List, edit, close campaigns | M5 | CampaignCard, Modal, api | AppRoutes | Yes |
| pages/organizer/Contributions.jsx | Who pledged, who paid | M5 | PledgeTable, api | AppRoutes | Yes |
| db.json (or public/data/) | Seed data per agreed shape | M6 | docs/data-shape.md | api.js | Yes |
| services/api.js | All data access functions | M6 | db.json | pages, forms | Yes |
| hooks/useFetch.js | `{ data, loading, error }` | M6 | api.js | pages | Yes |
| components/common/Loader.jsx, ErrorMessage.jsx, EmptyState.jsx | Feedback states | M6 | styles | all pages | Yes |
| pages/public/NotFound.jsx | 404 page | M6 | Button | AppRoutes | Yes |
| .github/workflows/ci.yml, PR template | CI and PR checklist | M1 | package.json scripts | GitHub | Yes |
| README.md | Setup, API used, features, known bugs | M6 (final), all contribute | - | graders | Yes |

Cross-slice dependencies to watch (agree props on Day 1 so nobody waits): CampaignProgress, StatusBadge, StatCard, PledgeTable, PledgeForm.

---

## 8. Navigation and routes

| Page | Path | Access (mock) |
|------|------|---------------|
| Landing | `/` | Public |
| Campaign list | `/campaigns` | Public |
| Campaign details | `/campaigns/:id` | Public |
| Login | `/login` | Public |
| Register | `/register` | Public |
| Organizer dashboard | `/organizer` | Organizer |
| Manage campaigns | `/organizer/campaigns` | Organizer |
| Create campaign | `/organizer/campaigns/new` | Organizer |
| Campaign contributions | `/organizer/contributions` | Organizer |
| Contributor dashboard | `/contributor` | Contributor |
| Contribution history | `/contributor/contributions` | Contributor |
| Admin dashboard | `/admin` | Admin |
| Manage users | `/admin/users` | Admin |
| Not found | `*` | Public |

Visiting a protected page without being logged in redirects to `/login`. Mock auth is UI-only and is not security.

---

## 9. UI/UX requirements

- Modern, responsive, consistent colors, spacing and buttons (one set of tokens in `variables.css`).
- Each role has a clearly different dashboard: organizer (progress and who has paid), contributor (my pledges and status), admin (platform stats and users).
- Every data page has three states: loading, error, empty.
- Accessible forms: labels linked to inputs, visible error messages, keyboard focus, sufficient color contrast.
- All dates human-readable (for example "12 Nov 2026"); money formatted consistently.
- Every list uses pagination (client-side is fine in Phase 1).
- Validate every form: required fields, positive amounts, valid email, sensible lengths, no double submit.
- Wireframes on paper or Figma by Day 1-2, stored in `docs/`.

---

## 10. GitHub workflow (Gitflow)

- Branches: `main` (stable, demo-ready), `develop` (integration), `feature/<slice>-<short-name>` such as `feature/campaigns-card`.
- Start from `develop`, open a PR back into `develop`. Only M1 merges `develop` into `main`, at the end of Days 2, 4, 6 and 7.
- Commit messages: `type: short description` where type is feat, fix, style, docs, test or chore (for example `feat: add campaign progress bar`). Present tense, one idea per commit.
- Pull requests: small (one component or page), use the PR template (what, how to test, screenshot), one reviewer from the review ring, reviewer must run it locally.
- Merge conflict avoidance: touch only your own files; need a change in someone else's file? Ask them or make a tiny separate PR. Pull `develop` into your branch every morning. Merge PRs same day.
- No direct pushes. Everything tracked on Trello.

Git commands you will use, with what each one does:
- `git checkout develop` then `git pull` - switch to the integration branch and download the latest changes.
- `git checkout -b feature/<name>` - create and switch to a new branch for your task.
- `git add <files>` then `git commit -m "feat: ..."` - stage your changes and save them with a message.
- `git push -u origin feature/<name>` - upload your branch to GitHub so you can open a PR.
- `git pull origin develop` (while on your branch) - bring in teammates' latest work to catch conflicts early.

---

## 11. Seven-day schedule

| Day | Goal | Main tasks |
|-----|------|-----------|
| 1 | Decide and set up | Confirm facilitator questions; agree styling approach; design data shape together; low-fi wireframes; M1 creates repo, branches, Trello, Vite app, installs; everyone clones and runs it. |
| 2 | Foundations | M2: variables, Button, Input, StatusBadge, Navbar, DashboardLayout v1. M1: routes with placeholder pages, AuthContext, CI. M6: db.json, api.js, useFetch, feedback components. Everyone else: build against agreed props. End of day: merge to develop, app runs with all routes. |
| 3 | Slices begin | M3 campaign card/list/details. M4 pledge form + table. M5 stat cards, create-campaign form. M1 login/register. M2 sidebar, modal, formatters, validators. |
| 4 | Slices continue | Dashboards for all three roles; forms validated; pledge and campaign creation flows work end to end (state held in session if static data). |
| 5 | Integrate and polish data flow | Replace any placeholders with real components; loading/error/empty states everywhere; pagination; cross-review pairs test each other's slices. |
| 6 | Test and fix | M6 leads checklist: navigation, responsive, forms, edge cases; unit tests for key components; bug fixing; README complete; screenshots. Start slide deck. |
| 7 | Freeze and present | Code freeze at midday (bug fixes only). Final merge to main, deploy (Netlify/Vercel), verify live link. Finish slide deck (5-7 slides), record video (5-10 min), write reflection, rehearse so anyone can explain any page. |

If a day slips, cut optional items first (Footer, Modal, ActivityFeed polish), never the README, states, or validation.

---

## 12. Definition of done (per PR)

- [ ] Runs locally with no console errors
- [ ] Works on mobile width and desktop
- [ ] Has loading, error and empty states where it fetches data
- [ ] Forms validated with clear messages
- [ ] Dates and money formatted
- [ ] Owner wrote at least a basic test or documented how it was tested
- [ ] PR description + screenshot, one reviewer approved

---

## 13. Presentation readiness checklist

- [ ] Live deployed link works (or video demo as backup)
- [ ] Demo path rehearsed for all three roles
- [ ] README: title, description, setup, data source and endpoints, features, known bugs
- [ ] Clean commit history with consistent messages
- [ ] Slide deck 5-7 slides, video 5-10 min, written reflection submitted
- [ ] Peer response written
- [ ] Every member can explain: routing, data flow (api.js to useFetch to page), state, forms and validation, and how Phase 2 will replace the data layer
- [ ] We can state honestly what is mock and what is real
