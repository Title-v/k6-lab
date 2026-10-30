# k6-lab

ITCS386: Performance Testing with Grafana k6.
Based on the guided scripts and exercises in the W10 lab sheet (version 2.0).

- [RESULTS.md](RESULTS.md): short results and Part B answers.
- `reports/`: HTML reports, console logs, JSON summaries, and time-series data.
- Tested with k6 v2.3.0 on macOS on 30 October 2026.

## Run Part 0

Install k6, then run these commands from this folder, one at a time:

```sh
k6 run 01-first-test.js
k6 run --vus 5 --duration 20s 01-first-test.js
k6 run --vus 5 --iterations 20 01-first-test.js
k6 run 04-pizza-api.js
k6 run 05-no-token.js
k6 run 06-thresholds.js
k6 run -e P95=300 06-thresholds.js
k6 run 07-requirement.js
k6 run -e P95=1 07-requirement.js
k6 run explore.js
k6 run --vus 5 --duration 20s 08-bug-hunt.js
```

The last command uses the corrected bug-hunt request. The original run is saved
in `reports/logs/0.8-before.txt`. A failed threshold returns exit code 99;
the stricter runs intentionally demonstrate this.

## Run Part A

```sh
mkdir -p reports
k6 run a1-smoke.js
k6 run --out web-dashboard=export=reports/a2-average.html a2-average.js
k6 run --out web-dashboard=export=reports/a3-stress.html a3-stress.js
k6 run --out web-dashboard=export=reports/a4-spike.html a4-spike.js
k6 run --out web-dashboard=export=reports/a5-soak.html a5-soak.js
```

Open the HTML files locally to view the charts. The scenario stages match the
lab sheet; `gracefulStop: '0s'` caps each scenario at its scheduled duration.
The saved runs used a five-second dashboard aggregation period.

Run tests sequentially. QuickPizza allows at most 5 virtual users and runs of
at most 3 minutes, except for the lab's soak test with 2 users.
The token in the scripts is QuickPizza's public practice token.

References: [k6 web dashboard](https://grafana.com/docs/k6/latest/results-output/web-dashboard/),
[ramping VUs](https://grafana.com/docs/k6/latest/using-k6/scenarios/executors/ramping-vus/).
