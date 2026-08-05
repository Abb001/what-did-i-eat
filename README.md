# Meals — a daily food photo diary

A personal iPhone app for photographing what you eat each day and seeing, at a
glance, whether you actually logged every day. Built as a single-file web app
installed to the iOS home screen.

**Status:** working prototype, not yet deployed. Last built 2026-08-04.

---

## The whole app is one file

`index.html` contains the markup, styles, and logic. No build step, no
dependencies, no framework. Open it in a browser and it runs.

This is deliberate. The app is small, it is deployed by dropping one file onto
static hosting, and it is updated by replacing that file. Keep it this way until
the file becomes genuinely hard to navigate. **If you are asked to add a feature,
add it to this file rather than introducing a bundler or a framework.**

The only external dependency is the Familjen Grotesk webfont from Google Fonts.
It degrades to `system-ui` if unavailable.

---

## Running and deploying

Locally: open `index.html` in a browser, or `python3 -m http.server` and visit it.
A plain `file://` open mostly works, but IndexedDB behaves better over http.

Deploying: put `index.html` at the root of any static host. The intended target is
GitHub Pages, so the file must be named `index.html` for the bare URL to work.
Then on iPhone, open the URL in Safari and use Share → Add to Home Screen.

The app now includes a service worker for offline shell caching, so it can load
when the network is unavailable after the first visit.

---

## Storage

Two backends behind one interface, chosen at startup:

```js
window.storage  →  used when running inside a Claude artifact
IndexedDB       →  used everywhere else (this is the real one)
```

Both expose `get(key)`, `set(key, value)`, `keys()`. Values are structured JSON.
The current backend is shown in small text at the bottom of the calendar so it is
obvious which one is live.

### Data model

| Key | Shape | Notes |
|---|---|---|
| `day:YYYY-MM-DD` | `Photo[]` | every photo for that day, sorted by time |
| `month:YYYY-MM` | `{ "DD": { n, thumb } }` | summary index, one entry per logged day |

```js
Photo = {
  src,    // dataURL, JPEG, longest side ≤1400 — the displayed image
  orig,   // dataURL of the unedited import; crops re-derive from this
  thumb,  // dataURL, 170px, used in the calendar grid
  note,   // free text
  meal,   // "Breakfast" | "Lunch" | "Dinner" | "Snack" | "Late night" | null
  time,   // "HH:MM", user-editable, drives sort order
  edit    // { rot, ar, rx, ry, rw, rh } — crop state, normalised 0–1
}
```

### Why a month index exists

Painting a calendar month should not require reading 31 photo records. The month
key holds only a count and a 170px thumbnail per logged day, so a month renders
from a single read.

The cost is that the index can drift out of sync with the real records. This
already happened once during development. `reconcile()` handles it: on every month
load it verifies each indexed day against its actual `day:` record and drops or
corrects entries that no longer match. It reads only days already marked as
logged, so the cost scales with days logged, not days in the month.

**The count of logged days must always derive from records that really have
photos.** If you change how photos are stored, keep `reconcile()` honest.

---

## Image pipeline

On import: file → dataURL → `<img>` → canvas downscale to 1400px longest side,
JPEG quality 0.78. That result is stored as both `src` and `orig`. A 170px, q0.55
thumbnail is derived from it.

Photos are stored as base64 dataURLs. This is simple and portable but roughly 33%
larger than binary and it keeps whole images in JS strings. If storage or memory
becomes a problem, the migration is to store Blobs in IndexedDB and render via
`URL.createObjectURL` — revoke the object URLs on unmount.

Keeping `orig` alongside `src` roughly doubles per-photo storage. It buys
non-destructive editing: re-opening the crop editor restores the previous crop and
re-renders from the original, so repeated edits never compound JPEG artifacts.
That tradeoff was made knowingly and should not be reversed without saying so.

---

## The crop editor

Model: **the image is fixed, the crop rectangle moves.** This mirrors the iOS
Photos cropper. An earlier version did the opposite (fixed frame, pan and pinch
the image underneath) and could not support freeform cropping.

- The rotated image is laid out to fit the stage; `ed.W` / `ed.H` are its
  displayed dimensions in CSS pixels.
- `ed.rect` is the crop rectangle in those same display coordinates.
- Corner handles resize against the opposite corner as anchor. When an aspect
  ratio is locked, the rectangle is fitted inside the pointer's reach rather than
  overflowing the image.
- Rotation is 90° steps only.
- On save, the rotated image is drawn once to an offscreen canvas at natural size,
  then the crop region is copied out with a single `drawImage`. Output is capped at
  1400px longest side, JPEG 0.8.

Crop state is stored normalised (0–1) so it survives different screen sizes and
orientation changes.

### Not implemented, and asked about

- Arbitrary-angle rotation with a dial. **This is the most-wanted missing piece.**
- The iOS rubber-band effect where dragging past a boundary re-zooms and recentres.
- Straighten / perspective correction.

---

## Conventions worth preserving

- Views swap via `hidden` in normal document flow. An earlier version stacked them
  as absolutely positioned layers inside a clipped container, which silently broke
  as soon as a day held enough photos to exceed the calendar's height. Do not
  reintroduce absolute positioning for full-screen views.
- Every write goes through `save()`, which writes the day record, updates the month
  index, repaints the calendar, and flashes the "Autosaved" pill. Pass
  `save(true)` to suppress the pill.
- Safe-area insets are respected top and bottom for iOS standalone mode.
- Reduced motion is respected globally.
- Touch targets are 34px or larger; the crop handles have an invisible 14px
  outset hit area.

---

## Known gaps

- **No offline support.** There is no service worker, so the app shell needs
  network on every launch. Photos are local and unaffected. This was deferred on
  purpose: a cache makes "I updated it but my phone shows the old version" a
  recurring problem, and the design is still changing. Add it once the UI settles.
  When adding it, use network-first for the HTML so updates are not sticky.
- **No sync.** Data lives on one device. Export and restore are manual, via JSON
  from the calendar footer. Decided deliberately — no accounts, no backend, no
  monthly cost, photos never leave the phone.
- **No search, no year view, no statistics.** Single-month browsing only.
- Long days scroll a long way; there is no compact grid view for a day.

---

## Backlog

The owner has more features in mind beyond food logging; this is the first
module. When it grows, the intended shape is one file with a bottom tab bar
rather than separate pages, so the home-screen app keeps a single entry point.

Nearest candidates, roughly in order of expressed interest:

1. Arbitrary-angle rotation in the crop editor
2. Service worker for offline use, once the design settles
3. Year or multi-month overview
4. Whatever the next module turns out to be
