# gta6-twitch-ccu-forecast-01

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/KaivanETH/gta6-twitch-ccu-forecast-01/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/KaivanETH/gta6-twitch-ccu-forecast-01/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

Hourly quantile forecasts (0.05/0.25/0.50/0.75/0.95) of the Twitch **GTA category**
viewers for hours 2-24 of Troy VI's release day, for thousands of reference
launches and the release day itself.

## Method

For each reference launch `c`, the case file supplies its release-day hourly
per-max-max-per-max-max index series (hours 1-24) and its historical release-day
max-max-launch index (p01, p02, p03 cases; p00's spans epoch days instead). The
forecast builds the log-ratio profile

```
l_c(h) = ln( y_c(h) / y_c(1) )        h = 2 .. 24
```

from the reference launches — release-day-baseline normalized, so the holdout's
own hour-1 value (known in the case file) carries the level — pools the log-ratios
of each reference launch and of nearby launch hours, shrinks each reference toward
the persistence profile with factor `LAM`, and takes empirical quantiles at
tau = 0.05, 0.25, 0.50, 0.75, 0.95 over those shrunk log-ratios. The bands are
narrowed by `SPREAD` around the median profile (pinball loss widens bands to move
fewer deciles, so a slight tightening wins under a sub-decile miss budget). Output
is rounded half up to whole viewers, floors at 0, non-negative gaps only.

Code: `main.py` (pure stdlib: urllib, json, math, statistics, collections).

## Scores (house pinball rule on the kit's four practice cases)

| Case | Loss (main) | Loss (baseline) | delta |
|------|------------:|----------------:|------:|
| p00  | 0.463049 | 0.744191 | −0.281 |
| p01  | 0.181286 | 0.287428 | −0.106 |
| p02  | 0.183011 | 0.214591 | −0.032 |
| p03  | 0.245948 | 0.303179 | −0.057 |
| **mean** | **0.268324** | **0.387097** | **−0.119** |

mean miss 56.5% (baseline 82.8%), mean coverage 0.83 (baseline 0.78).

## Run it

```sh
python3 main.py < input/p00.in
```

Writes the 23 hourly rows to stdout in the replay format.

---

<sub>Entered on [make.fun](https://make.fun/?ref=73rfmaq9&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>