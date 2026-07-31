# Full Court — NBA Franchise Simulator

A complete, self-contained NBA-style franchise sim that runs entirely in your browser. No build step, no server, no dependencies — one `index.html` file.

**[Play it now →](https://taimtamrabet-rgb.github.io/NBA-SIM/)** *(once GitHub Pages is enabled — see below)*

## What it does

- **30 procedurally-generated rosters** across real NBA cities/conferences/divisions, with ratings, ages, salaries, and development curves. Players are fictional.
- **An 82-game regular season**, scheduled with a round-robin ("circle method") algorithm that guarantees every team plays exactly 82 games, one game per day, fully balanced.
- **Game simulation** driven by team offense/defense ratings (weighted by rotation depth), with home-court advantage, score variance, and a lightweight per-player box score (points/rebounds/assists distributed by role and position).
- **Standings** with games-behind, sortable by conference, with a playoff-line marker.
- **Playoffs**: top 8 seeds per conference, a real bracket (1v8, 4v5, 3v6, 2v7 → conference semis → conference finals → NBA Finals), best-of-7 series in 2-2-1-1-1 home-court format.
- **The amateur draft**: a 14-team lottery with realistic top-4 odds, 2 rounds / 60 picks, CPU teams draft by best-player-available weighted against positional need. Draft picks can be traded years in advance.
- **CPU trading**: a value model (overall, upside, age, draft-pick equity) that CPU teams use to accept or reject trades — both ones you propose and ones they make with each other automatically during the season and every offseason.
- **Franchise mode**: take control of one team (or spectate a fully autonomous league), age players, retire veterans, develop young talent toward their potential, and carry your franchise across seasons.
- **Autosave** to your browser's `localStorage` — close the tab and resume later.

## Running it

Just open `index.html` in a browser. That's it — everything (engine, styling, UI) is inlined in that one file.

For local development with a server (optional, avoids any `file://` quirks):

```sh
npx http-server .
```

## Hosting on GitHub Pages

1. Push this repo to GitHub (already done if you're reading this from the repo).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to "Deploy from a branch".
4. Pick the branch this file lives on and **/ (root)** as the folder, then **Save**.
5. GitHub will publish `index.html` at `https://<owner>.github.io/<repo>/` within a minute or two.

No Actions workflow is required — it's a static file.

## How the simulation works

- **Schedule**: a "circle method" round-robin produces 29 rounds where every team plays exactly once per round. Two full cycles (with home/away flipped on the second) plus a partial third cycle add up to exactly 82 games per team, and each round becomes a "day" in-game.
- **Ratings**: each team's rotation (top 9 by overall) is weighted by depth to produce offense/defense ratings; game scores come from those ratings plus home-court edge and Gaussian variance, with overtime handled as sudden-death scoring bumps until the tie breaks.
- **Trade value**: `value = overall + upside×youth_factor − age_penalty` for players, and a win%-discounted estimate of draft slot for picks. A trade is accepted if the receiving side's incoming value is within ~12% of what they're giving up and it doesn't blow past a sane payroll ceiling.
- **Offseason**: draft → roster trim to 15 → aging/development/retirement → a round of CPU-CPU trades → new 82-game schedule → new draft class.

## Project structure

```
index.html   — the entire app: design tokens + layout (CSS), simulation engine (JS), and UI (JS)
.nojekyll    — tells GitHub Pages to serve the file as-is
```

The simulation engine (player/team generation, scheduling, game sim, standings, playoffs, draft, trading) is written as a dependency-free JS module with no DOM access, so it's testable in isolation — it was validated with a Node-based test harness covering schedule integrity, multi-season simulation, playoff bracket correctness, draft pick conservation, and trade execution before being wired into the UI.

## Known simplifications

This is a game, not a broadcast-quality sim: there's no play-in tournament (straight top-8 seeding), no free agency (released players simply leave the player pool), no injuries, and box-score stats are distributed statistically rather than simulated possession-by-possession. Team city/nickname data is factual and public; every player is procedurally generated and fictional.
