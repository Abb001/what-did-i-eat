# Meals — design brief

## What it is

A private food diary for one person. You photograph what you eat, and the app
answers one question better than anything else: *did I actually log every day?*

It is not a calorie tracker, not a social app, not a restaurant log. There is no
feed, no score, no streak celebration. Nobody else ever sees it.

**Audience:** one person, on an iPhone, several times a day, usually in under
fifteen seconds while food is getting cold.

---

## The design thesis

**The interface has no colour of its own. The photos are the only colour on screen.**

Everything else — chrome, type, controls — sits back in white, grey, and a single
restrained blue. This is why the palette is so quiet, and it is the constraint to
protect when adding anything new. A new accent colour, a coloured illustration, or
a decorative gradient would compete with the food.

The signature element follows from it: **the calendar is a mosaic.** Each logged
day's cell is filled with that day's first photo, so a month reads as a wall of
what you ate. Empty days are pale grey blanks, and the gaps are the point — they
are what tell you the habit slipped.

---

## Tokens

```
--paper       #FCFCFA   page background, a warm off-white
--card        #FFFFFF   raised surfaces
--wash        #F1F2EE   empty calendar cells
--line        #E3E5E0   borders and rules
--ink         #1B1E22   primary text
--mid         #5E646C   secondary text
--soft        #9AA0A6   tertiary text, empty-day numerals
--blue        #2F5D8A   today marker, progress, active states — used sparingly
--blue-wash   #E9F0F7   selected chip backgrounds
```

The blue is a porcelain blue, closer to blue-and-white ceramic than to a software
accent. It appears in perhaps four places in the whole app. That scarcity is the
design.

The photo editor inverts to near-black (`#141618`) with a light blue action colour
(`#7FB2E8`). This is the one dark surface, and it is dark because judging a crop
against white is hard.

**Typeface:** Familjen Grotesk, weights 400 / 500 / 600. Chosen for slightly
quirky letterforms that stay legible at 13px, and because the numerals carry the
calendar. Falls back to `system-ui`.

**Type scale in use:** 52 (day numeral) · 34 (month) · 16 (notes) · 15 (buttons,
empty states) · 14 (metadata) · 13 (chips, labels) · 12 (badges)

An earlier version set labels at 10–11px with wide letter-spacing. It was rejected
as too hard to read. **Nothing below 12px, and no decorative letter-spacing on
small text.**

**Radii:** 7px cells · 8px icon buttons · 10px photo cards · 99px chips and the
primary button.

---

## Screens

**Calendar.** Month name and year, arrows to move between months. A single line
reporting days logged out of days elapsed, with a hairline progress bar. Seven
columns, Monday first. Logged days show their photo; today gets a blue ring.
Footer holds backup export and restore plus a note of where data is stored.

**Day.** Large day numeral with month, year, and weekday beside it. Then a
vertical stack of photo cards. Each card: the photo full-bleed, then a row of meal
chips, an editable time field, a pencil, and a delete control; then a note field
that grows as you type. A dashed empty state when the day has nothing.

**Photo editor.** Full-screen, dark. Cancel and Done in the bar. The image sits
fitted with a draggable crop rectangle over it, corner brackets in white, the area
outside dimmed, thirds guides appearing only while dragging. Below: aspect
presets, rotate left, reset, rotate right.

**Autosave pill.** A small dark pill that slides down from the top edge, says
"Autosaved," and leaves after 1.7 seconds. Deliberately quiet — it is
reassurance, not an announcement.

---

## Fixed constraints

- Must remain a **single HTML file** with no build step and no framework. This is
  how it gets deployed and updated.
- iOS home-screen app: full screen, no browser chrome, safe-area insets respected.
- Touch targets 34px minimum.
- Reduced motion respected.
- Only one webfont; everything must survive its absence.
- Motion is functional only — a view transition and the autosave pill. No
  decorative animation.

---

## Settled by the owner

- White background. A dark version was built first and rejected.
- Larger type and normal letter-spacing.
- Photos stay on the device. No accounts, no sync, no backend.
- English interface.
- Crop model follows iOS Photos: image fixed, crop rectangle adjustable, with
  freeform available.

---

## Open, and worth exploring

- **Density in the day view.** Photos stack vertically at full width. A day with
  six photos is a long scroll. Would a grid, or smaller cards that expand on tap,
  serve better? Unresolved.
- **Calendar cell size.** Around 44px on a phone, so the thumbnail reads mostly as
  colour rather than content. Fine for the mosaic effect, possibly too small to be
  useful for recall. Larger cells with fewer weeks visible is one alternative.
- **The empty day.** Gaps carry the app's whole message, and right now they are
  just grey squares. There may be a better way to make an unlogged day feel like
  an invitation rather than an absence — without ever making it feel like a
  reproach.
- **Meal chips.** Five chips take a full row on narrow screens. Could be inferred
  from the timestamp and shown as a single editable value instead.
- **Growth.** More modules beyond food are planned. A bottom tab bar is the
  assumed shape, but nothing is designed yet.

## Off-limits

- Streaks, badges, scores, or any nudge that turns a missed day into a failure.
- Calorie counts, macros, portion estimates, or nutrition scoring.
- Anything social or shareable.
