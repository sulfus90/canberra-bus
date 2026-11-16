# Canberra 126D Bus Board

Live arrival board for Singapore bus stop **58621** ("Blk 126 CP", Canberra St, by Blk 126D), showing services **861** and **883** split by direction.

## How it works
- Single file: `index.html`. Open it in a browser. No build step and no API key needed.
- Data: `https://arrivelah2.busrouter.sg/?id=58621`, a public CORS-enabled proxy of LTA DataMall BusArrival. LTA DataMall itself needs an AccountKey and does not send CORS headers, so a browser can't call it directly.
- Refreshes every 10 s (`REFRESH_MS`) and on tab focus. The countdown is recomputed every second from each bus's absolute arrival time.
- **Direction:** 861 and 883 are loops from Sembawang Int (58009) that pass 58621 twice. `visit_number: 1` = outbound **to Yishun**; `visit_number: 2` = return **to Sembawang**. The feed often repeats a bus in `subsequent` and `next2`, so `bucket()` removes duplicates.
- It can't run as a claude.ai Artifact with live data, because the Artifact CSP blocks fetch to outside hosts.

## Changelog
- 2026-11-16: Initial version. Board with 861/883 and two direction lanes each: big ETA, clock time, load, deck type and wheelchair chips, and following buses. Light and dark themes, plus an offline banner when a fetch fails.
