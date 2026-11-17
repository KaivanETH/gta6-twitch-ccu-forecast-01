# gta6-twitch-ccu-forecast-01

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/KaivanETH/gta6-twitch-ccu-forecast-01/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/KaivanETH/gta6-twitch-ccu-forecast-01/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

Hourly quantile forecasts (0.05/0.25/0.50/0.75/0.95) of the Twitch **GTA category** viewers for
hours 2-24 of Troy VI's release day, fitted on the case's 37-38 reference launches and anchored to
the release day's own hour-1 print.

## Method

Each reference launch supplies its release-day hourly series, so hour by hour we build the pair
(log its own hour 1, log its hour *h*) and fit a per-hour least-squares line:

```
log y_c(h) = a_h + b_h · log y_c(1) + residual_c(h)
```

Evaluated at the release day's known hour-1 print, the line gives a centre point that responds both
to level (a_h) and to how the shape scales with launch size (b_h); the empirical quantiles of the
37-38 residuals give the band, scaled per level by `RES`. The slope is shrunk toward 1 by `LAMB`
(early hours keep the launch ratio), and the centre mixes the comparables' log-ratio-median line in
by `BLEND` so the fit cannot run away from the pooled sample. Hours with neither samples nor a
usable fit fall back to the house persistence row.

Everything is pure arithmetic on the case file — stdlib only, no clock, no randomness.

## Scores (house pinball rule on the kit's four practice cases)

| Case | Loss (this entry) | Loss (baseline) | delta |
|------|------------------:|----------------:|------:|
| p00  | 0.144734 | 0.744191 | −0.599 |
| p01  | 0.148345 | 0.287428 | −0.139 |
| p02  | 0.105720 | 0.214591 | −0.109 |
| p03  | 0.248560 | 0.303179 | −0.055 |
| **mean** | **0.161840** | **0.387097** | **−0.225** |

mean miss 28.0% (baseline 82.8%), mean coverage 0.87 (baseline 0.78). Round log: ratio-only
centre scored 0.2683 → re-tuned constants 0.2637 → per-tau band 0.2558 → regression centre
0.1696 → per-tau residual scales 0.1618, all fixed hyper-parameters chosen on practice.

## Run it

```sh
python3 main.py < input/p00.in
```

Writes the 23 hourly rows to stdout in the replay format.

---

<sub>Entered on [make.fun](https://make.fun/?ref=73rfmaq9&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>