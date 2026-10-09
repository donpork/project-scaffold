# Task: Bootstrap a barebones React project skeleton

Reusable starter template. Fill **Project variables**, then follow every step using those values (do not hardcode project-specific strings in the steps).

## Project variables

| Variable | Example (this project) | Notes |
|----------|------------------------|--------|
| `REPO_URL` | `https://github.com/donpork/project-scaffold` | Full GitHub repo URL (not just the org) |
| `LOCAL_DIR` | `~/Documents/Studio/Processing/project-scaffold` | Absolute or `~`-relative path on the target machine |
| `MACHINE` | Dan's MacBook Air (self-hosted worker) | Where the agent must run |
| `APP_HEADING` | `Project scaffold - IXD 404` | Exact `<h1>` text in `App.jsx` |
| `NODE_MAJOR` | `24` | Must match `node -v` major, `.nvmrc`, and `engines.node` |

Derived helpers:

- `REPO_NAME` — last path segment of `REPO_URL` (example: `project-scaffold`)
- `ORIGIN` — same as `REPO_URL` (`.git` suffix optional)

Set up a minimal, working React project foundation in this repo. The result should be a clean skeleton I can build on: a local dev server with live preview (Vite), Node/npm so I can install libraries, and React with plain JavaScript (JSX). Nothing more.

Run this task on **`MACHINE`** as a **self-hosted worker**. Do not use a cloud VM for the clone/install/push path — cloud VMs lack GitHub push credentials for this machine, and the cloud terminal cannot see the Mac filesystem (`cd` into `LOCAL_DIR` fails there).

## Guiding principles

- **Minimal by default.** Add only what's listed below. No TypeScript, routers, state libraries, linters, formatters, test runners, CSS frameworks, or extra config files unless I ask.
- **Don't destroy anything.** If the repo already has files (especially `package.json`, `index.html`, or `src/`), stop and tell me what's there before changing anything.
- **Use current stable versions.** Install the latest React, Vite, and React plugin, and make sure they're compatible with each other. Set `.nvmrc` / `engines` to **`NODE_MAJOR`** from `node -v` on `MACHINE`. Don't pin to versions from memory; check what's installed.
- **Verify, don't assume.** Run each command and confirm it works before moving on.
- **One pass.** Gather everything you need from this doc and complete the bootstrap without asking clarifying questions, unless a hard blocker remains (missing Node, conflicting tracked files, auth failure).
- **Node majors must agree.** Before any commit, confirm that `.nvmrc`, `package.json` `engines.node`, and `node -v` all share the same major (`NODE_MAJOR`). Fix mismatches before committing.

## 1. Repository

1. Work in `LOCAL_DIR` on `MACHINE`.
2. If the directory is empty and not a git repo: `git clone REPO_URL .` (or `git init`, add `origin` = `ORIGIN`, and fetch/checkout `main`).
3. Confirm `git remote -v` shows `origin` → `REPO_URL` (or the `.git` equivalent). Fix the remote if wrong.
4. Confirm the default branch with `git status` / `git branch` (expect `main`).
5. If it's empty of project files, set up the skeleton at the **repo root** (not a subfolder).
6. Handle remote history per section 4. Do **not** force-push.

## 2. Node + npm

1. Check `node -v` and `npm -v`. Record the major as `NODE_MAJOR`. If Node is missing or older than the current LTS / required major, stop and tell me how to install or upgrade it (recommend nvm). Don't install system software yourself.
2. Use **npm** as the package manager. Use a different one only if the repo already has another lockfile.
3. Create `package.json` with:
   - `"name": "<REPO_NAME>"`, `"private": true`, `"type": "module"`
   - `"engines": { "node": ">=<NODE_MAJOR>" }`
4. Add a `.nvmrc` containing exactly `NODE_MAJOR` (one line).
5. Before committing: re-check that `.nvmrc`, `engines.node`, and `node -v` majors all match `NODE_MAJOR`. If they don't, align the files to the machine's major and re-verify.

## 3. Vite + React (dev server + preview)

1. Install dependencies (hand-built skeleton — **do not** use `create-vite` or another template):
   - `npm install react react-dom`
   - `npm install -D vite @vitejs/plugin-react`
2. Add these scripts to `package.json`:

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
}
```

3. Create a minimal `vite.config.js` that only registers the React plugin:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
})
```

4. Create the target structure below by hand.

## Target structure

```
/
├── index.html          # <div id="root"></div>; loads /src/main.jsx as type="module"
├── src/
│   ├── main.jsx        # createRoot(...).render(<StrictMode><App /></StrictMode>); imports index.css
│   ├── App.jsx         # single function component; heading text exactly: APP_HEADING
│   └── index.css       # minimal base styles
├── public/             # static assets (empty; add a .gitkeep)
├── documents/          # this bootstrap template (commit it with the project)
├── vite.config.js
├── package.json
├── package-lock.json
├── .nvmrc              # NODE_MAJOR
├── .gitignore          # node_modules, dist, .env*, .DS_Store, *.log
└── README.md           # keep GitHub README intent when present; add install / run / build / preview
```

Conventions:

- Use `.jsx` for any file that contains JSX.
- Use function components and PascalCase filenames (`App.jsx`).
- Keep `App.jsx` trivial. It's a starting point, not a demo.
- Heading must be exactly `APP_HEADING`.

## 4. Git remote cases (README / empty / merge)

Fetch `origin` first. Then:

### A. Remote has a README or other initial commit

1. If local and remote histories are unrelated (or you built a local skeleton separately), merge:

```bash
git merge origin/main --allow-unrelated-histories
```

2. **Keep the README:** preserve existing GitHub README content/intent, then add clear run instructions (`npm install`, `npm run dev`, `npm run build`, `npm run preview`).
3. Resolve conflicts without dropping either side's purpose.
4. Never force-push.

If you cloned `REPO_URL` into an empty `LOCAL_DIR` first, build the skeleton on top of that clone instead — still keep the README and only add run instructions.

### B. Remote is empty (no commits / nothing to merge)

1. Create the skeleton and commit locally.
2. After verification passes, push directly: `git push -u origin main`.
3. Never force-push.

## 5. Verification checklist

Confirm each of these works and report the result:

1. `npm install` completes with no errors or peer-dependency warnings.
2. `npm run dev` starts a server (default http://localhost:5173), the React component renders **`APP_HEADING`**, and editing `src/App.jsx` updates the browser instantly without a full reload (React Fast Refresh).
3. The browser console shows no errors.
4. `npm run build` produces a `dist/` folder.
5. `npm run preview` serves the built `dist/` locally.
6. `.nvmrc`, `engines.node`, and `node -v` majors all equal `NODE_MAJOR`.

If a port is already in use (leftover `dev`/`preview`), free it or use the next free port Vite prints — then verify against that URL.

## 6. Finish

1. Make one commit for the initial skeleton (if you created it): `chore: initial Vite + React skeleton`. Use a merge commit message if you only merged histories (e.g. `merge: combine GitHub README with Vite skeleton`).
2. **Push automatically after verification.** Once the checklist passes, push to `origin/main` (`git push -u origin main` on first push). This is required for the initial push and for later bootstrap commits — no confirmation step.
3. Commit this template under `documents/` when shipping the bootstrap docs (do not leave it untracked if the task says to commit it).
4. Reply with a short summary: Node / npm / React / Vite versions, confirmation that Node majors match, files created, push result (and SHAs), and the commands to run the project.

## Commands the user runs locally

```bash
cd LOCAL_DIR
npm install
npm run dev
```

Example for this project:

```bash
cd ~/Documents/Studio/Processing/project-scaffold
npm install
npm run dev
```

Open the URL Vite prints (default `http://localhost:5173`).

---

## Learnings / run-it-in-one-go

Use this section so the next agent can finish without back-and-forth. Prefer **Project variables** above; the values below are the filled example for this repo.

1. **Full repo URL.** Always use `REPO_URL` (`https://github.com/donpork/project-scaffold`). The original draft only had `https://github.com/donpork/`, which is not enough to clone or set `origin`.
2. **Run on `MACHINE` as a self-hosted worker.** Local target: `LOCAL_DIR`. Cloud VMs do not have this Mac's GitHub push credentials, and the cloud terminal cannot see the Mac path (`cd LOCAL_DIR` fails there).
3. **GitHub README vs empty remote.** If the remote has a README/initial commit, merge with `git merge origin/main --allow-unrelated-histories`, keep the README, then add run instructions. If the remote is empty, push directly after verify. Never force-push.
4. **Push after verify — automatic.** Once verification passes, push to GitHub immediately (especially the initial push). No confirmation step.
5. **App heading.** Exact text: `APP_HEADING` (`Project scaffold - IXD 404`).
6. **User run commands:** `cd LOCAL_DIR && npm install && npm run dev`.
7. **Node majors must match.** `.nvmrc`, `engines.node`, and `node -v` must all share `NODE_MAJOR` (example: `24`). Check before committing; align files to the machine major when they drift.
8. **`documents/` holds this template.** Commit it when the task says to ship the bootstrap docs (earlier runs left it untracked; that is no longer the default once you are asked to commit it).
9. **Other gotchas from this machine:**
   - The local folder may exist empty with **no `.git`** — initialize or clone before assuming `git status` works.
   - Hand-build the skeleton; do not import template logos, ESLint, or TS from `create-vite`.
   - Prefer fixing `origin` if it points at the wrong URL rather than adding a second remote.
   - Leftover Vite `dev`/`preview` processes can steal ports (4173/5173); check the URL Vite actually prints.
   - `dist/` and `node_modules/` belong in `.gitignore`; don't commit them.
   - `tmux` may be missing on this Mac; background processes still work without it.
