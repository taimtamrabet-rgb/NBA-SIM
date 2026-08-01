# Baseline GM — NBA Franchise Simulator

A complete NBA front-office sim that runs entirely in your browser. No build step, no server, no dependencies — one `index.html` file.

**[Play it now →](https://taimtamrabet-rgb.github.io/NBA-SIM/)** *(once GitHub Pages is enabled — see below)*

## What it does

- **30 procedurally-generated rosters** across real NBA cities/conferences/divisions, with ratings, ages, contracts, and development curves. Players are fictional.
- **An 82-game regular season**, scheduled with a round-robin ("circle method") algorithm that guarantees every team plays exactly 82 games, one game per day, fully balanced. Every game keeps a full box score you can pull up later.
- **End-of-season awards**: MVP, Defensive Player of the Year, Sixth Man of the Year, and Rookie of the Year, each with four finalists computed from real season stats, role (starter vs. bench), and rookie status.
- **A play-in tournament**: seeds 7-10 in each conference fight for the last two playoff spots (7-vs-8, 9-vs-10, and a final survive-and-advance game) before the bracket locks — exactly like the real thing.
- **Playoffs**: a real bracket (1v8, 4v5, 3v6, 2v7 → conference semis → conference finals → NBA Finals), best-of-7 series in 2-2-1-1-1 home-court format.
- **The amateur draft**: a 14-team lottery with real odds (top-4 drawn, 5-14 in reverse-standings order) and a live reveal showing each team's original odds and how far they moved. Two rounds, 60 picks, CPU teams draft by best-player-available weighted against positional need. Picks are tradeable years in advance.
- **Free agency under a real cap structure**: a salary cap, tax line, and two aprons, all with teeth — re-signing your own free agents can exceed the cap (Bird rights), but signing someone else's can't once you're deep into the aprons. Contracts expire on a real multi-year clock.
- **CPU trading, from both sides**: a value model (overall, upside, age, draft-pick equity) that CPU teams use to accept or reject trades you propose, a trade finder that surfaces incoming offers from other front offices, and CPU-vs-CPU trades that happen on their own — surfaced as a "Trade Alert" you can page through.
- **Franchise mode**: take control of one team (or spectate a fully autonomous league), age players, retire veterans, develop young talent toward their potential, and carry your franchise across seasons. Autosaves to your browser's `localStorage`.

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

## Season flow

Regular season → **Awards** → **Play-In Tournament** → **Playoffs** → **Draft Lottery** → **Draft** → **Free Agency** → next season. Each stage is its own screen with a "Continue" action, so you can watch (or skip through) the whole offseason story rather than everything resolving invisibly between seasons.

## How the simulation works

- **Schedule**: a "circle method" round-robin produces 29 rounds where every team plays exactly once per round. Two full cycles (with home/away flipped on the second) plus a partial third cycle add up to exactly 82 games per team, and each round becomes a "day" in-game.
- **Ratings**: each team's rotation (top 9 by overall) is weighted by depth to produce offense/defense ratings; game scores come from those ratings plus home-court edge and Gaussian variance, with overtime handled as sudden-death scoring bumps until the tie breaks. Box scores distribute points/rebounds/assists across the rotation by role and position, and are snapshotted per-game so they stay readable even after a player is traded or retires.
- **Play-in / lottery**: computed once and reveal-staged in the UI — nothing is re-rolled after you've seen the result, so what you're shown always matches what actually happens.
- **Trade value**: `value = overall + upside×youth_factor − age_penalty` for players, and a win%-discounted estimate of draft slot for picks. A trade is accepted if the receiving side's incoming value is within ~12% of what they're giving up and it doesn't blow past a sane payroll ceiling.
- **Free agency**: contracts tick down every offseason; expiring players hit the market, where their original team gets first right of refusal (Bird rights, can exceed the cap) before an open-market bidding pass fills out the rest of the league.

## Project structure

```
index.html   — the entire app: design tokens + layout (CSS), simulation engine (JS), and UI (JS)
.nojekyll    — tells GitHub Pages to serve the file as-is
```

The simulation engine (player/team generation, scheduling, game sim, standings, playoffs, play-in, awards, draft, free agency, trading) is a dependency-free JS module with no DOM access, so it's testable in isolation — it was validated with a Node-based test harness covering schedule integrity, multi-season simulation, playoff/play-in correctness, lottery consistency, draft pick conservation, free agency contract bookkeeping, and trade execution before being wired into the UI.

## Known simplifications

This is a game, not a broadcast-quality sim: box-score stats are distributed statistically rather than simulated possession-by-possession, there are no injuries, and the cap/apron rules are a simplified approximation of the real CBA (no trade salary-matching restrictions, no exception tracking). Team city/nickname data is factual and public; every player is procedurally generated and fictional.
