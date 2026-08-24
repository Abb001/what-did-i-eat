# My Personal App — All-in-One Personal Hub

A private, client-side progressive web app for tracking your daily life — food diary with AI calorie estimation, budgeting, moodboard inspirations, and progress selfies. Built as a single-file web application installed to the iOS home screen or web browsers.

---

## App Architecture

### Level 1: Home Dashboard (Hub)
- **Home Hub (`#hub`)**: Central dashboard featuring module stat cards and quick access to:
  - 🍲 **Food Diary**: Daily food diary, Instagram-style multi-photo posts, carousel views, and Gemini AI food recognition.
  - 💰 **Budget (記帳)**: Track daily expenses, income transactions, and monthly balances.
  - 🎨 **Moodboard**: Collect visual inspiration pins and aesthetic notes.
  - 📸 **Selfies**: Document daily progress portraits and visual timeline.
  - ⏳ **Countings**: Track anniversaries (Count-up), event countdowns (D-Day), repeat milestones, and create shareable posters.
- **Global Bottom Navigation Bar**: Seamless one-tap navigation across modules.

### Level 2: Modules

#### 🍲 Food Module
- **Instagram-Style Multi-Photo Posts**: Uploading multiple photos creates a single post card with a swipeable carousel and pagination indicators (`• • •`).
- **Photo Reordering**: Reorder photos within a post using ◄ Left and Right ► buttons.
- **List vs. Carousel View Modes**: Toggle day views between a vertical post list and a horizontal scrollable carousel.
- **AI Calorie & Food Recognition**: Tap `✨ AI Calorie` on any food post to analyze dishes and estimate calories/macros using the Gemini Multimodal Vision API.

#### 💰 Budget Module
- Quick expense/income logging with category tags (Food, Transport, Shopping, Entertainment, General) and auto-calculated totals.

#### 🎨 Moodboard Module
- Visual photo inspiration collage grid.

#### 📸 Selfies Module
- Daily progress portrait log with date timestamps.

#### ⏳ Countings Module
- **Dual Mode (Countdown & Count-up)**: Count days until future events (Countdown / D-Day) or count days elapsed since special milestones (Count-up / Days Together).
- **Repeat Cycles**: Support for One-off, Yearly (birthdays/anniversaries automatically roll to the next occurrence), Monthly, and Weekly cycles.
- **Spotlight Hero Card**: Pinned hero card with live seconds ticker and refined Liquid Glass theme styling.
- **Milestone Predictor**: Automatically calculate exact dates for 100, 300, 500, 1000 days and anniversary milestones.
- **Date Toolbox**: Built-in interval calculator and date offset calculator.
- **Shareable Posters**: Canvas-generated aesthetic posters (Liquid Glass, Polaroid, Minimalist, Ticket Pass) for instant download or sharing.

---

## Running and Deploying

Locally:
```bash
python3 -m http.server 8085
```
Open `http://localhost:8085` in a browser.

Deploying:
Put `index.html` at the root of GitHub Pages or any static host. On iPhone, open the URL in Safari and select **Share → Add to Home Screen**.
