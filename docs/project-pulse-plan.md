# Project Pulse Dashboard: Implementation Plan

## 1. Overview and goals
A small static dashboard (vanilla HTML/CSS/JS, no build step) showing each project's owner, status, recent activity, priority and a short summary.

- Project cards with status badges, clear priority treatment and readable spacing.
- Data loaded from `app/project-data.json` (top-level `projects` array).
- Each project has `name`, `owner`, `status`, `recentActivity`, `priority`, plus `summary` (added for contributor-friendly context).
- Launching **Run Project Pulse Dashboard** from VS Code opens the rendered `index.html`.
- CSS hooks `.dashboard` and `.project-card` must exist.
- Nobody stages, commits or pushes; the learner controls git.

## 2. File assignments

| File | Owner | Purpose |
|---|---|---|
| `app/project-data.json` | Coder (schema approved by Designer) | Top-level `projects` array with 5–6 realistic entries covering every status and priority value. |
| `app/index.html` | Coder | Semantic markup (header, summary area, `<main class="dashboard">`) and a script that fetches the JSON and renders `.project-card` elements, with loading/error/empty states. |
| `app/styles.css` | Designer | Design tokens, layout, cards, badges, priority styles, responsive grid, focus and contrast states. |
| `.vscode/launch.json` | Coder | Strict JSON (no comments). Configuration named exactly "Run Project Pulse Dashboard"; serves `${workspaceFolder}/app` and opens `index.html`. |

Designer and Coder must not edit each other's files. Class names and DOM structure are the contract between `index.html` and `styles.css`.

## 3. Responsibilities

### Designer
- Define information hierarchy: name → status badge + priority → owner, activity, summary.
- Define design tokens as CSS custom properties: status colours (`on-track`, `at-risk`, `blocked`, `completed`), priority colours (high/medium/low), spacing, type scale, radius, shadow.
- Never rely on colour alone: badges and priority carry text labels (optionally icons).
- Write `styles.css` with `.dashboard`, `.project-card`, `.badge--<status>`, `.priority--<level>` and a responsive grid (1 column mobile, 2 tablet, 3+ desktop).
- Ensure text contrast ≥ 4.5:1 and visible `:focus-visible` states.
- Style empty, loading and error states.
- Provide the class-name/DOM contract to the Coder.
- Report design decisions, files touched and validation recommendations.

### Coder
- Write `project-data.json` as valid JSON using the agreed enumerations.
- Write `index.html` with semantic landmarks, `lang`, viewport meta and a title.
- Script requirements:
  - `fetch('project-data.json')` with a `response.ok` check
  - validate that `projects` is an array
  - render with `textContent`/escaped values, never unsanitised `innerHTML`
  - explicit error and empty states
  - map status/priority to the Designer's class names, with a neutral fallback for unknown values
- Write `.vscode/launch.json` with deterministic name, port and URL, `cwd` of `${workspaceFolder}/app`, opening `index.html` via a static server (`fetch` fails under `file://`).
- Validate before reporting; report changes and remaining risk.

## 4. Dependencies
1. Data schema and enumerations come first; HTML rendering and CSS badge classes depend on them.
2. Design tokens and the class contract precede `styles.css`, and are needed by `index.html` for correct class names.
3. `index.html` needs the schema and the class contract.
4. `launch.json` is content-independent but needs the fixed path `app/index.html`.
5. Integration validation needs all four files.

## 5. Parallel work decisions

**Step 0 (sequential): contract.** Designer and Coder agree on the schema (including `summary`), status/priority enumerations, and class names/DOM structure. The Orchestrator writes it into both task prompts. This prevents mismatches in Step 1.

**Step 1 (parallel):** file scopes are disjoint and share the contract.
- Designer: `app/styles.css`
- Coder: `app/project-data.json` and `app/index.html`
- Coder (or same Coder as last task): `.vscode/launch.json`

**Step 2 (sequential): integration and validation.** Needs every file present. Fixes go back to the owner of the affected file.

**Rationale:** parallel work is safe thanks to the contract and non-overlapping files; integration checks how files fit together, so it can't be parallel.

## 6. Validation expectations
- **Launch:** run **Run Project Pulse Dashboard** from Run and Debug; the dashboard renders at the configured URL (no directory listing). `launch.json` parses as strict JSON.
- **Data:** cards match `project-data.json` entries; each shows all five required fields plus summary. Broken JSON/path shows the error state; an empty `projects` array shows the empty state.
- **Responsive:** check ~360px, 768px, 1280px; no horizontal scroll; grid reflows.
- **Accessibility:** logical heading order; status not conveyed by colour alone; contrast ≥ 4.5:1; visible keyboard focus; landmarks and `lang` present; loading/error messages announced (`role="status"`/`role="alert"`).
- **Console:** no errors or warnings, including 404s (e.g. favicon; use an inline data-URI icon if needed).
- **Repo hygiene:** only the four assigned files (plus this plan) changed; nothing staged.

## Edge cases
- `fetch` fails on `file://`; a server is required.
- Unknown status/priority values; missing or null fields.
- Very long names or summaries (wrap or truncate).
- A single project; BOM or trailing commas in JSON.
- Port already in use.

## Open questions
1. Which static server should `launch.json` use (`npx http-server`, Python `http.server`, Live Server)? Check `.devcontainer` first.
2. Browser type (`msedge`/`chrome`) and whether a `preLaunchTask` is needed (would require editing `tasks.json`, outside current scope).
3. Confirm status values: `on-track`, `at-risk`, `blocked`, `completed`.
4. Filtering/sorting is assumed out of scope.
