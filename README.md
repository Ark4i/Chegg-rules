# Custom Chegg+ — with a persistent unit editor

## Files
- `chegg_rules.html` — the rulebook/simulator page people visit. It now fetches unit data from `units.json` instead of having it hardcoded.
- `units.json` — the actual unit data. This is the file the editor updates.
- `editor.html` — the editor UI. Add/edit/delete units, click cells to draw movement/attack patterns, then publish.

## How the "sync" works
GitHub Pages is a static host — there's no database. So instead, the editor talks directly to the **GitHub API** and commits an updated `units.json` straight into your repo. GitHub Pages automatically rebuilds after every commit (usually under a minute), and the rulebook page fetches `units.json` fresh on every load. So: edit → publish → wait a few seconds → anyone who reloads sees it. Not real-time, but no server needed.


Create a **personal access token** so the editor is allowed to write to your repo:
   - Go to https://github.com/settings/tokens?type=beta and click **"Generate new token"** (fine-grained).
   - Under **Repository access**, select "Only select repositories" and pick this repo.
   - Under **Permissions → Repository permissions**, set **Contents** to **Read and write**.
   - Generate it and copy the token (starts with `github_pat_...`).
Open `editor.html` (either locally or on your live GitHub Pages site), expand **Repository Settings**, and fill in:
   - your GitHub username, the repo name, the branch (usually `main`), and the path (`units.json` if it's at the repo root).
   - paste in the token.
   - tick "remember" for whichever fields you don't want to retype every time. **Only tick "remember token" on a device you trust** — it's stored in that browser's `localStorage`, nowhere else.

## Editing
- Click **"Load units.json from GitHub"** to pull the current live data, or edit from the local copy that loads automatically.
- Select a unit on the left, edit its fields, and click grid cells to toggle movement (green) / attack (red) tiles. Overlapping tiles show amber.
- **+ New Unit** adds a blank unit; the trash-icon-style **Delete This Unit** button removes the selected one.
- Click **🚀 Publish to GitHub** to commit. You'll see a status message when it succeeds.
- No token, or don't want to grant repo write access at all? Click **⬇ Download units.json** instead and drag-and-drop the file onto your repo in the GitHub web UI to overwrite it manually — same end result.

## Local testing
Because both pages use `fetch()` to load `units.json`, opening them directly from disk (`file://...`) will fail in most browsers due to CORS restrictions on local files. Run a tiny local server from the folder instead:
```
python3 -m http.server 8000
```
then visit `http://localhost:8000/chegg_rules.html` and `http://localhost:8000/editor.html`.

## Security note
The token you enter in the editor is used only for direct browser → `api.github.com` calls. It's never sent to us or to any third party, and it's never written into the HTML/JS files themselves — so it's safe to publish this repo publicly as long as you don't check the "remember token" box on a shared computer, and don't paste your token into the JSON/HTML files by hand.
