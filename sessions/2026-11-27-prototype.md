# 2026-11-27: first prototype

Kamal saw the Saswad Rd flyover (Hadapsar, Pune) in Google Street View and asked: can I drive a car here as a game? This session answered that and built a playable prototype.

## What we built

One HTML page on the Google Maps JavaScript API (code: private repo `kamal0808/street-drive`):

- Full-screen Street View with a red car hood and a speed / km / timer readout.
- Hold ↑ to drive, ←/→ to steer, ↓ to reverse; on-screen buttons for phones.
- Goal: drive 1 km as fast as you can.
- Starts on Saswad Rd; `?lat=..&lng=..` starts anywhere with Street View.

## What we learnt

- **There are no frames in between.** Street View has one 360° photo about every 10 m (measured 9–11 m on Saswad Rd). Driving is hopping from photo to photo, not video. Truly smooth driving needs a 3D model of the city, such as Google's Photorealistic 3D Tiles, with a 3D car on top (three.js or Cesium). Much bigger build; not tried.
- **A zoom "glide" between photos was tried and turned off.** Zooming toward the horizon, then snapping back when the next photo lands, looked bad to Kamal. It's still in the code behind `?glide`.
- **Preloading helps.** A hidden second panorama loads the next photo's images early, so the jump is instant.
- **Don't trust Street View's own "next photo" list while moving.** It lags a step behind a jump, so the car kept hopping back onto photos it had just passed. Measured over 15 s: the game counted 440 m while the car really moved about 310 m. Fix: the game plans the road 6 photos ahead itself (StreetViewService lookups, never stepping back onto a photo it just left) and counts the real GPS distance between photos. After the fix: 454 m counted against 416 m straight-line on a slightly curving road.
- **First-jump stall.** The wait before a jump was fixed at the speed you set off with (~6 km/h), so the first jump took about 6 s. Fix: move by distance travelled, recomputed every frame.
- **The speedometer has to slow down while a photo loads,** otherwise it shows 120 km/h while you really average less.

## Cost and rules

- Google Maps Platform: Dynamic Street View has 5,000 free events a month, then about $14 per 1,000 (global price list, checked 2026-11-27: https://developers.google.com/maps/billing-and-pricing/pricing). One game load = one panorama load; moving between photos inside it isn't billed separately. This build loads two panoramas per game (the visible one plus the hidden preloader).
- The key lives on TOPC's Google Cloud project, works only on the prototype's page, and Maps JS loads are capped at 150 a day, so it can't go past the free limit.
- Using the official API in a browser is fine. Downloading or scraping Street View images into a game is against Google's terms.
- Not checked: whether StreetViewService lookups (used for road planning) are billed, and how well Google's 3D Tiles cover Pune.

## Next, if picked up

- Host it somewhere public (it only runs on Kamal's Mac mini now) with its own referrer-locked key.
- Pick a smoother look: 3D Tiles driving, or a better transition than the zoom.
- Make it a game: routes, ghosts of your best run, a leaderboard per road.
