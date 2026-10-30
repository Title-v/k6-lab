# K6 lab results

30 October 2026 | macOS | k6 v2.3.0 | QuickPizza

## Part 0

- **0.1:** Response time was **276.11 ms**.
- **0.2:** **80 requests**, **3.81 req/s**, **p(95) 276.81 ms**.
- **0.3:** Exactly **20 iterations** completed with 5 users.
- **0.4–0.5:** Vegetarian checks passed. Missing authorization returned the expected **401**; HTTP failures were **0%**.
- **0.6:** Normal exit: **0**. `P95=300` exit: **99**, with p(95) **377.98 ms**.
- **0.7:** All three rules passed: p(95) **388.43 ms**, p(99) **392.60 ms**, failures **0%**. Changing only `P95` to **1 ms** returned **99**.
- **0.8:** Use case-sensitive `Pepperoni` and `Knife`. Checks improved from **200/225** to **225/225**. Repeated random recommendations revealed failures one manual request could miss.

## Part A results

| Step | Test | Peak users | Req/s | p(95), ms | Max, ms | Failed | Checks |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: |
| A1 | Smoke | 1 | 0.50 | 388.27 | 391.70 | 0% | 100% |
| A2 | Average load | 3 | 1.31 | 377.51 | 852.30 | 0% | 100% |
| A3 | Stress | 5 | 2.17 | 381.64 | 2357.35 | 0% | 100% |
| A4 | Spike | 5 | 1.04 | 369.44 | 394.57 | 0% | 100% |
| A5 | Soak | 2 | 0.98 | 386.89 | 1661.02 | 0% | 100% |

All Part A thresholds passed. HTML reports: [A2](reports/a2-average.html), [A3](reports/a3-stress.html), [A4](reports/a4-spike.html), [A5](reports/a5-soak.html).

## Part B

1. See the Part A table.
2. Each user waits for the response and sleeps for 1 second before sending another request.
3. p(95) rose **377.51 to 381.64 ms** (1.1%). This suggests 5 users remained inside the operational envelope; capacity was not established.
4. No clear slowdown: p(95) was **385.05 ms before**, **365.33 ms during**, and **334.77 ms** in seconds 70–80. No recovery delay was measurable at 10-second resolution.
5. No worsening: first/last two steady-load minutes had p(95) **388.63/385.18 ms** and **0% failures**. A longer soak could reveal a memory leak.
6. p(95) captures slow requests that an average can hide.
7. Shared-site traffic varies; internet latency and routing differ from a controlled lab.
8. Heavy tests can overload a shared site and disrupt other users. Permission is required.
9. Vague: "My shopping app should be fast." Testable: **3 users, 30 seconds; p(95) < 1 second, p(99) < 1.5 seconds, failures < 1%**:

   ```js
   export const options = {
     vus: 3,
     duration: '30s',
     thresholds: {
       http_req_duration: ['p(95)<1000', 'p(99)<1500'],
       http_req_failed: ['rate<0.01'],
     },
   };
   ```

Evidence: [console logs](reports/logs/), [JSON data](reports/data/), and the HTML reports in [reports/](reports/).
