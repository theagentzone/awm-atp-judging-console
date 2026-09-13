# AWM ATP - Microsoft Copilot Workshop — Judging Console

Three files. No folders, no build step.

| File | What it is |
|---|---|
| `index.html` | The whole app — code, styles and both logos inside one file |
| `judges.json` | Who can sign in, the password, and the live-leaderboard setting |
| `404.html` | A copy of `index.html` so deep links still work |

## 1. Turn on GitHub Pages

Pages is **not** in the Actions tab. Go to the repo's **Settings** tab (top
right of the repo, next to Insights), then **Pages** in the left sidebar.

1. First, **Settings -> General -> scroll to the bottom -> Change repository
   visibility -> Public.** Pages will not serve a private repo on a free plan.
2. **Settings -> Pages -> Build and deployment**
   - Source: **Deploy from a branch**
   - Branch: **main**, folder: **/ (root)** -> **Save**
3. Wait about a minute, then reload Settings -> Pages. The live URL appears at
   the top: `https://<username>.github.io/<repo-name>/`

You do not need any of the Actions workflow templates (Static HTML, Hugo,
Jekyll). "Deploy from a branch" just serves the files as they are.

Then point `aka.ms/AWM-ATP-Class-2026` at that URL; aka.ms makes the QR code.

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

Keep each `id` unique, and don't change an `id` once scoring has started.

## 3. The live leaderboard — `sharedUrl`

GitHub Pages only hands out files; it cannot store anything. So for five
phones to share one leaderboard, they need one small place on the internet to
read and write. Add ONE line to `judges.json` pointing at it:

```json
"sharedUrl": "https://<your-endpoint>"
```

The app needs an endpoint that answers `GET` with the saved object and accepts
`PUT` with a JSON body. Any of these work:

### Option A — Azure Blob Storage (stays on Microsoft, ~5 minutes)

1. Azure Portal -> Storage accounts -> Create (any region, Standard LRS).
2. Inside it: Containers -> **+ Container** -> name it `scores`.
3. Upload a file called `scores.json` containing just `{}`.
4. Right-click the file -> **Generate SAS** -> tick **Read**, **Write**,
   **Create** -> set an expiry after your event -> Generate -> copy the
   **Blob SAS URL**.
5. Paste that whole URL (it ends in a long `?sv=...` token) as `sharedUrl`.
6. In the storage account: **Settings -> Resource sharing (CORS) -> Blob
   service** -> add a rule: Allowed origins `https://<username>.github.io`,
   Allowed methods `GET,PUT,OPTIONS`, Allowed headers `*`, Max age `3600`.

The app sends the `x-ms-blob-type` header Azure requires automatically.

### Option B — Azure Static Web Apps (the tidiest, a bit more setup)

Host the app there instead of GitHub Pages — it deploys from this same GitHub
repo and includes a free built-in API, so the leaderboard backend and the site
are one deployment with nothing third-party.

### Option C — a free JSON bin (fastest, third-party)

Create a bin at jsonblob.com starting as `{}` and paste its API URL in.

### How it behaves once set

- A device pushes the moment a judge saves a card and re-reads every 5 seconds,
  so the board moves within about 5 seconds of any judge scoring.
- Before pushing, a device re-reads and layers its own cards on top, so two
  judges submitting at the same instant cannot overwrite each other.
- The leaderboard shows the last refresh time, and says so plainly if it can't
  reach the endpoint. Scores keep saving locally and sync when it returns.

**Without `sharedUrl` the app still works** — each judge just sees the board
built from their own device's cards.

**Test before the day:** open the URL in two browsers, sign in as two judges,
score a team in each, watch both boards move.

## 4. What judges see

- The **combined** leaderboard — team averages across every judge who has
  scored, by criterion, with a "judges in" count per team.
- Never another judge's individual scores or notes.
- Final rankings unlock once every judge has scored every team.

## 5. One honest caveat

The password sits in a file the browser downloads, so anyone who inspects the
page can read it. It keeps casual passers-by out; it is not real security.
Fine for a workshop — don't reuse that password anywhere that matters.
