# OddsIntel football closes — free sample (13 days, 4,468 matches)

Finished football matches with the **consensus closing price** and the **fair probability** for three markets, plus the
final score. No bookmaker is named anywhere in this file, and there is no per-bookmaker column.

**Soft-book consensus only; no sharp-book inputs.**

**This is a one-off snapshot (kick-offs 10–22 Oct 2026, UTC), published once and not refreshed.** For live closes and
fair prices on upcoming matches, longer history, freshness fields and the bet-grading endpoint (`POST /v1/clv`), get a
free Sample key at https://data.oddsintel.app/request-key. Paid plans start at €49 a month.

- File: `oddsintel_football_closes_2026-10-10_to_2026-10-22.csv` (UTF-8, `\n` line ends, one row per match, empty cell = no figure)
- Data card: `oddsintel_football_closes_2026-10-10_to_2026-10-22_datacard.json` (rows, competitions, date range, per-market coverage)
- Built with the same code path as the Data API's `GET /v1/close`, so any row can be checked against it
- Licence: **CC BY-NC 4.0**. Credit "OddsIntel (oddsintel.app)". Non-commercial use only (research, teaching,
  benchmarking); for commercial use see data.oddsintel.app. Provided as is, without warranty. Not betting advice.

## Try it in five minutes

```python
import pandas as pd, numpy as np
d = pd.read_csv("oddsintel_football_closes_2026-10-10_to_2026-10-22.csv").dropna(subset=["1x2_home_fair_p"])
y = np.eye(3)[d["result"].map({"home": 0, "draw": 1, "away": 2})]
p = d[["1x2_home_fair_p", "1x2_draw_fair_p", "1x2_away_fair_p"]].to_numpy()
print("log-loss", -(y * np.log(p)).sum(axis=1).mean())   # lower is better; a flat 1/3 each scores 1.0986
```

The same check, live: data.oddsintel.app/accuracy

## What is in a row

| Column | Meaning |
|---|---|
| `match_id` | Our match id (the Data API's `fixture_id`) |
| `kickoff_utc` | Kick-off, UTC, ISO 8601 |
| `competition` | `League (Country)` |
| `home_team`, `away_team` | Team names |
| `home_goals`, `away_goals`, `result` | Full-time score (regular time) and `home` / `draw` / `away` |
| `1x2_{home,draw,away}_*` | Full-time 1X2 |
| `ou25_{over,under}_*` | Total goals over/under **2.5** |
| `btts_{yes,no}_*` | Both teams to score |
| `<market>_<selection>_close` | **Consensus close**: decimal price, 3 dp |
| `<market>_<selection>_fair_p` | Consensus fair probability, 4 dp; sums to 1 across the market |
| `<market>_n_sources_band` | How many independent sources stood behind the figure: `3` or `4+` |

A market is filled only when every one of its outcomes has a final close; otherwise its cells are empty. A match is in
the file only if it finished with a score and has at least one complete market.

## Definitions

The method (margin removal, freshness gate, wrong-board guard, one vote per independent source, minimum depth of 3
independent sources) is defined in the Data API docs: https://data.oddsintel.app/docs. The sample uses exactly the figures the Data API serves, so any row can be checked
against `GET /v1/close` for the same match.

## Caveats

- Closes are "the last price we had before our kick-off time", not the price at the whistle (see the docs).
- The window starts where our direct-read history starts (10 Oct 2026), so it is 13 days, not 30 (see the data card).
- Not betting advice; a sample for research, teaching and benchmarking.
