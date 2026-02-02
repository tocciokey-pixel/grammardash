# Copilot Instructions for grammardash 🔧

Purpose: Help an AI coding agent become productive quickly in this repo by describing the app architecture, runtime behaviors, editing conventions, and developer workflows.

## Big picture 🧭
- Single-page static web app (HTML + vanilla JS) in `index.html`. UI, SRS logic, state, and question-loading live in that file.
- Question data are JSON files under `questions/` (`point008.json` …). `index.html` loads files listed in `QUESTION_FILES`.
- No build step or bundler. Changes are tested by serving `index.html` in a browser (see "Run & debug").

## How to run & quick checks ▶️
- Serve the folder over HTTP (fetch() is used to load JSON):
  - Python: `python -m http.server 8000` then open `http://localhost:8000` ✅
  - Or use VS Code Live Server extension.
- Manual test checklist after edits:
  1. Open `index.html` in browser via HTTP.
  2. START → verify questions load and UI responds.
  3. Add a question file and verify it appears (see "Add questions").
  4. Test BACKUP/RESTORE and localStorage (`gd_v4_pro`) updates.

## Key files & concepts 🔎
- `index.html` — main app. Look for:
  - `QUESTION_FILES` array: list of question JSON files to load.
  - `loadAllQuestions()` and `normalizeQuestion()` — canonicalizes JSON into runtime shape.
  - SRS & state: `SR_INTERVAL_DAYS`, `getM/setM`, `state.mastery` and `state` persistence in `localStorage` under key `gd_v4_pro`.
- `questions/*.json` — question data. Must be a JSON array.

## Question JSON schema (discoverable patterns) 🧩
- Each file must be an array of question objects.
- Choice example (must include):
  - id: unique id string (format used: `015-1`)
  - point: e.g. `Point 015`
  - type: `choice`
  - question: text
  - choices: array of strings
  - answer: index (number)
  - explanation, jp (optional)
- Reorder example:
  - type: `reorder`
  - tokens: array of strings
  - answer: array (correct token order)
- `normalizeQuestion()` accepts alternate keys (e.g., `q` for question, `opts` for choices) — follow existing naming for clarity.

## Behavioral details agents must know ⚙️
- Loading: `loadAllQuestions()` fetches each file and skips broken ones (continues on error).
- Timer: `startTimer()` uses 200ms ticks with `timeLeft = 100` → 20 seconds timeout for each question.
- Mastery & SRS: levels 0–5; `SR_INTERVAL_DAYS = [0,1,3,7,14,30]`; correct answer increments `m.level` (capped at 5) and adds XP (`+50`), wrong answer lowers level and schedules immediate retry.
- UI patterns: reorder UI disables token buttons when selected; UNDO/CLEAR restores buttons.
- Error surface: global `window.onerror` alerts on JS exceptions — helpful during manual debugging.

## Project conventions & versioning 📝
- Versioned snapshots: when making major UI changes, index.html is copied to `index_v1_X.html` as historical snapshots (see existing files).
- Questions are grouped per "Point" (e.g., `Point 008`), and category ordering is auto-derived from point names unless `CATEGORY_ORDER` is set.
- Persisted state shape: `state = { xp, last, mastery, custom, history, currCat, stars }` — keep edits compatible.

## Editing/PR checklist for contributors ✅
- If adding a new point:
  - Create `questions/pointXXX.json` as a JSON array following schema above.
  - Add its path to `QUESTION_FILES` in `index.html` (or update `index.html` copy and version it). Example: `"questions/point015.json"`.
- Validate JSON is an array and objects are normalized by `normalizeQuestion()` (missing/invalid questions are skipped at runtime).
- Ensure id uniqueness across files (ids used as keys in `state.mastery`).
- If UI logic changes (timers, SRS), update `README.md` and add a new `index_v1_X.html` snapshot before committing.
- Test: start the app via HTTP, exercise choice & reorder flows, BACKUP/RESTORE, and Bookmarks.

## Notes for automated edits by AI agents 💡
- Prefer small, isolated changes (add one point file or one UI tweak per PR).
- Avoid mutating `QUESTION_FILES` paths without also validating the JSON loads (broken files are silently skipped).
- When touching `localStorage` keys or `state` shape, include a migration path or preserve backward compatibility; the app tolerates numeric or object `mastery` shapes but uses object form going forward.

---
If you'd like, I can iterate and make this shorter or add a short example PR template and a small JSON linter script to validate `questions/*.json` automatically. Any unclear areas you'd like expanded? 🚀