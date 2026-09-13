# AWM ATP - Microsoft Copilot Workshop — Judging Console

Static site. Judges open the URL on a phone or laptop, sign in with their
JPMorgan email plus the shared password, score each team 1-5 on five criteria,
and watch a live leaderboard of the combined panel scores.

## Publish on GitHub Pages

1. Create a new GitHub repository (public), e.g. `awm-atp-judging-console`.
2. Upload **every file in this folder** to the root of the repo's default
   branch, including the hidden `.nojekyll` file.
3. In the repo: **Settings -> Pages -> Build and deployment**
   - Source: *Deploy from a branch*
   - Branch: `main` / folder: `/ (root)` -> **Save**
4. After about a minute the site is live at
   `https://<your-github-username>.github.io/<repo-name>/`

## Short link + QR code

Create the vanity link at https://aka.ms pointing at the Pages URL, with the
vanity name `AWM-ATP-Class-2026`. aka.ms generates a downloadable QR code.

## Who can sign in — `judges.json`

`judges.json` sits next to `index.html`. Edit it **straight in GitHub** and
refresh the page; no rebuild is needed.

```json
{
  "password": "Calloway2026",
  "judges": [
    { "id": "j1", "name": "Real Name", "email": "real.name@jpmchase.com" }
  ]
}
```

- `password` — the one password you give all the judges.
- `judges` — one row per judge. `email` is what they type in; `name` is what
  shows in the header. Keep `id` unique and unchanged once scoring starts.
  Add or remove rows freely; the app resizes itself.

## Live leaderboard across devices — `sharedUrl`

Out of the box every score is stored in the judge's own browser, so each judge
sees the leaderboard built from the cards on their own device.

To make the leaderboard combine all five judges live, add one more line to
`judges.json`:

```json
"sharedUrl": "https://<your-json-endpoint>"
```

Any free no-signup JSON bin works (for example jsonbin.io or npoint.io). The
endpoint must answer `GET` with the saved object and accept `PUT` with a JSON
body. The app polls it every 5 seconds and pushes each time a card is saved.
Leave `sharedUrl` out and the app stays device-local.

## Changing the teams, criteria or 1-5 wording

Those live in the app source, in the marked `EDIT ME` block at the top of
`src/pages/home.tsx`: the `TEAMS` list, the `CRITERIA` list, and the `LEVELS`
map (OK / Good / Well done / Excellent / Outstanding). Change them, rebuild,
and re-upload this folder.

## What judges can and cannot see

- They see the **combined** team leaderboard, averaged across every judge who
  has scored, and a "judges in" count per team.
- They never see another judge's individual scores or notes.

## One honest caveat

The password lives in a file the browser downloads, so anyone who inspects the
page can read it. It keeps casual passers-by out of the console; it is not real
security. That is a fine trade for a workshop — just don't reuse the password
for anything that matters.
