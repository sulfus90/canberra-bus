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
- 2026-11-16: Deck type is easier to see. A bus drawing (`deckHTML()`) next to the big ETA shows one row of windows for single deck and two rows for double deck; double deck is tinted in the lane colour. Each following bus gets an SD/DD tag, and the legend explains SD/DD.
- 2026-11-16: Meadow theme from `background.jpg`, a 236×511 wallpaper that is too small to stretch. It's cut into `meadow.png` (top 300px, tiled as the page texture) and `friends.png` (bottom 211px, the character scene at the end of the page, faded at the top). Frosted cream cards; blush-pink lane = to Yishun, ear-blue lane = to Sembawang; Fredoka + Nunito rounded type. Single light theme on purpose (dark mode dropped). Grid columns use `minmax(0,1fr)` so nothing overflows on phones.
- 2026-11-16: Weather from NEA via data.gov.sg (CORS-enabled, no key), refreshed every 2 min (`WX_REFRESH_MS`). The panel sits below the bus cards: current temperature from Sembawang station S80 (falls back to S104, then S109), the Sembawang 2-hour forecast, today's range, humidity and wind, the north-region 24-hour periods, and a 4-day outlook (v1 `4-day-weather-forecast` endpoint; hidden if it fails). A mini temperature pill under the clock links to the panel. Icons are inline SVG picked by `wxKind()` from the forecast text.
- 2026-11-16: Day and night looks by Singapore time: `applyTimeOfDay()` in `<head>` sets `html[data-time]`, day 07:00–18:59, night 19:00–06:59, rechecked every 30 s. Add `#night` or `#day` to the URL to force one. Night = `chiikawa night.webp` as a fixed full-bleed layer (`body::before`), sized `max(100vw,140vh)` from the top so only the moonbeam half shows under a navy wash; `boat.png` (640×380, cut from that image) replaces `friends.png` as the bottom scene, with a soft oval fade. All colours are tokens; night redefines them under `:root[data-time="night"]` (navy glass, moon-yellow route numbers, glowing pink/blue lanes). `.day-only` and `.night-only` toggle per-mode elements.
- 2026-11-16: Every time on the page uses the 12-hour US style with AM/PM (for example 4:53 PM). One helper, `fmtTime(date, withSeconds)`, uses `en-US` and `timeZone: "Asia/Singapore"`, so times stay in Singapore time on any device. It's used for the clock (AM/PM in smaller type), bus arrival times, "Updated", and weather times.
