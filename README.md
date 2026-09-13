# AWM ATP - Microsoft Copilot Workshop — Judging Console

Three files. No folders, no build step, no server.

| File | What it is |
|---|---|
| `index.html` | The whole app — code, styles and both logos live inside this one file |
| `judges.json` | Who can sign in, the password, and the live-leaderboard setting |
| `404.html` | A copy of `index.html` so deep links still work |

## 1. Publish on GitHub Pages

1. **Make the repository Public.** GitHub Pages will not serve a private
   repository on a free account. Settings -> General -> bottom of the page ->
   *Change repository visibility* -> Public.
2. Upload `index.html`, `judges.json` and `404.html` to the repo root. There
   are no folders, so drag-and-drop cannot miss anything.
3. Settings -> Pages -> Build and deployment
   - Source: *Deploy from a branch*
   - Branch: `main` / folder: `/ (root)` -> **Save**
4. A minute later the site is live at
   `https://<username>.github.io/<repo-name>/`

Then point `aka.ms/AWM-ATP-Class-2026` at that URL. aka.ms generates the QR code.

## 2. Who can sign in — `judges.json`

Edit it straight in GitHub (pencil icon) and refresh the page. No rebuild.

```json
{
  "password": "Calloway2026",
  "judges": [
    { "id": "j1", "name": "Real Name", "email": "real.name@jpmchase.com" }
  ]
}
```

- `password` — the single password you hand to all the judges.
- `judges` — one row each. `email` is what they type; `name` shows in the
  header. Keep `id` unique, and don't change an `id` once scoring has started.

## 3. The live leaderboard — `sharedUrl`

Without this setting each device keeps its own scores, so a judge's leaderboard
only reflects their own cards. To make all five judges feed one live
leaderboard, add ONE line to `judges.json`:

```json
{
  "password": "Calloway2026",
  "sharedUrl": "https://awm-atp-scores-default-rtdb.firebaseio.com/scores.json",
  "judges": [ ... ]
}
```

### Option A — Firebase Realtime Database (recommended, ~5 minutes)

1. https://console.firebase.google.com -> **Add project** (skip Analytics).
2. Build -> **Realtime Database** -> Create Database -> **Start in test mode**.
3. Copy the database URL, e.g.
   `https://awm-atp-scores-default-rtdb.firebaseio.com`
4. Put that URL in `judges.json` with `/scores.json` on the end.

Test mode leaves the database open to anyone with the URL, and it expires after
30 days. Both are fine for a workshop. Delete the project afterwards.

### Option B — a free JSON bin (no signup)

Create a bin at https://jsonblob.com (or similar), start it as `{}`, and paste
its API URL in. It must answer `GET` with the saved object and accept `PUT`
with a JSON body.

### How it behaves

- Each device pushes the moment a judge saves a card, and re-reads every 5
  seconds, so the leaderboard moves within about 5 seconds of any judge
  scoring.
- Before pushing, a device re-reads and layers its own cards on top, so two
  judges submitting at the same instant cannot overwrite each other.
- The leaderboard card shows the last refresh time, and says so plainly if it
  can't reach the shared scoreboard. Scores keep saving locally either way and
  sync when it returns.

**Test it before the day:** open the URL in two different browsers, sign in as
two different judges, score a team in each, and watch both leaderboards move.

## 4. What judges see

- The **combined** leaderboard — team averages across every judge who has
  scored, broken down by criterion, with a "judges in" count per team.
- Never another judge's individual scores or notes.
- Final rankings unlock once every judge has scored every team.

## 5. One honest caveat

The password sits in a file the browser downloads, so anyone who inspects the
page can read it. It keeps casual passers-by out; it is not real security.
Fine for a workshop — just don't reuse that password anywhere that matters.
