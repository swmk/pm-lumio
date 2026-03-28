# AI Companion — MVP Interactive Prototype
## Product Specification

**Version:** 3.0
**Date:** 2026-03-28
**Deliverable:** Single HTML file — interactive app prototype (vanilla HTML + CSS + JS, no build step)

---

## 1. What It Is

A clickable, in-browser prototype simulating the AI Companion app experience for working adults. Renders as a mobile phone frame on desktop. All data is hardcoded/in-memory — no backend required.

---

## 2. Screens

| Screen | Purpose |
|---|---|
| **Home** | Greeting, today's check-in prompt, mood week snapshot |
| **Chat (Text)** | Scripted AI conversation; memory recall shown in first message; taboo word notice |
| **Chat (Voice)** | Animated mic, simulated transcript, returns to text chat on end |
| **Mood Profile** | Bar chart of weekly mood scores; mood tags; AI insight text; burnout risk level |
| **Settings** | List of settings; Emergency Contact shows "Not configured" / "Configured ✓" |
| **Emergency Contact** | Form: name, relationship, contact method (SMS or email), alert threshold, self-notify toggle |
| **Alert Modal** | Overlay showing danger signal detected, contact being notified, simulated message preview |

---

## 3. Key Flows

**Check-in flow:** Home → Chat → scripted replies advance via "Next →" demo button → after final turn, Alert Modal auto-triggers.

**Emergency contact flow:** Settings → Emergency Contact → fill form → save → Settings row updates to "Configured ✓" → "Send Test Alert" button becomes active → triggers Alert Modal in test mode.

**Alert modal flow:** Shows contact name + simulated SMS/email preview → "I'm okay" dismisses + adds chat bubble → "Talk to companion" navigates to Chat.

---

## 4. Navigation

- Fixed bottom tab bar: Home / Chat / Mood / Settings
- In-screen back button for sub-screens (e.g. Emergency Contact)
- Alert Modal overlays all screens with dark backdrop

---

## 5. Tech Stack

Single `index.html`. CSS custom properties for theming. Vanilla JS for screen switching, form handling, and scripted chat. Google Fonts via CDN. No frameworks, no npm.

---

## 6. Config

One `CONFIG` object at the top of the script block holds: user name, scripted chat replies, mood data, taboo keywords, alert SMS template, and product name. Easy to update without touching the rest of the code.

---

## 7. Done When

- All 7 screens reachable and render correctly
- Chat advances through scripted replies and triggers alert at the end
- Emergency contact form validates, saves, and updates the Settings row
- Alert modal shows correct contact details after contact is saved
- Test alert works from the Emergency Contact screen
- No console errors on full walkthrough
