# AI Companion — MVP Interactive Prototype
## Full Technical Specification

**Version:** 2.0
**Date:** 2026-03-28
**Target Users:** Working Adults
**Deliverable:** Single-file interactive HTML prototype (HTML + CSS + vanilla JS)
**Change from v1.0:** MVP re-scoped from marketing landing page → interactive app prototype demonstrating product flow and all key features including new Emergency Contact configuration.

---

## 1. Overview

### 1.1 Product Summary
An AI-powered emotional wellness companion. Provides a private, persistent space for daily emotional check-ins via text or voice, tracks mood over time, and monitors conversations for signs of danger — alerting the user or a pre-configured emergency contact when needed.

### 1.2 Purpose of This MVP Prototype
The interactive prototype is a **clickable, simulated app experience** — not a marketing page. It serves to:
- Demonstrate the complete product flow to stakeholders, investors, or user testers
- Validate the UX and screen flow before engineering begins
- Showcase all 5 core features including the new Emergency Contact configuration
- Require zero backend — all data is simulated in-memory with JavaScript

### 1.3 Features In Scope

| Feature | Screen(s) |
|---|---|
| Daily check-in conversation (text) | Chat Screen |
| Voice conversation mode | Chat Screen (Voice toggle) |
| Persistent memory simulation | Chat Screen (shows recalled context) |
| Mood Profile visualization | Mood Screen |
| Emergency Contact setup | Settings Screen |
| Danger detection + alert flow | Alert Modal (triggered from Chat) |

### 1.4 Out of Scope (MVP Prototype)

| Excluded | Reason |
|---|---|
| Real AI responses | Requires backend; use scripted reply simulation |
| Real voice transcription | Use simulated transcript display |
| Actual SMS/email alerts | Use on-screen alert confirmation instead |
| User auth / login | Not needed to demonstrate flow |
| Data persistence across sessions | In-memory only; resets on reload |
| Taboo topic filtering logic | Show visual indicator only (not functional) |

---

## 2. Prototype Architecture

### 2.1 Delivery Format

A **single `index.html` file** containing all HTML, CSS, and JavaScript. No external dependencies except:
- Google Fonts (via CDN link in `<head>`)
- Optional: one SVG icon sprite inline in the HTML body

### 2.2 Layout Concept: Mobile App in Browser

The prototype renders as a **simulated mobile phone frame** centered on the desktop browser. This makes the experience feel like a real app and scopes the UI clearly.

```
┌─────────────────────────────────────────────┐  ← Browser window
│                                             │
│       ┌─────────────────────┐              │
│       │  ████ Status bar    │              │
│       ├─────────────────────┤              │
│       │                     │              │
│       │   [Active Screen]   │              │
│       │                     │              │
│       │                     │              │
│       ├─────────────────────┤              │
│       │ 🏠  💬  📊  ⚙️      │  ← Tab bar  │
│       └─────────────────────┘              │
│                                             │
└─────────────────────────────────────────────┘
```

**Phone frame dimensions:** 390px × 844px (iPhone 14 equivalent)
**On screens < 480px wide:** phone frame fills the full viewport (no outer frame shown)

### 2.3 Screen Map

| Screen ID | Screen Name | Tab | Trigger |
|---|---|---|---|
| `screen-home` | Home / Dashboard | 🏠 Home | Default on load |
| `screen-chat` | Chat (Text Mode) | 💬 Chat | Tab click or Home CTA |
| `screen-chat-voice` | Chat (Voice Mode) | 💬 Chat | Voice toggle in Chat |
| `screen-mood` | Mood Profile | 📊 Mood | Tab click |
| `screen-settings` | Settings | ⚙️ Settings | Tab click |
| `screen-emergency` | Emergency Contact Setup | ⚙️ Settings | Settings list item |
| `modal-alert` | Danger Alert Modal | (overlay) | Demo trigger button |

### 2.4 Navigation Rules

- Bottom tab bar is always visible (except during Alert Modal)
- Active tab is highlighted with brand primary color + label shown
- Inactive tabs show icon only (label hidden to save space)
- Back navigation within a section (e.g., Settings → Emergency Contact) uses an in-screen `← Back` header button, not the tab bar
- The Alert Modal overlays all screens with a dark backdrop

---

## 3. Screen Specifications

### 3.1 Screen: Home / Dashboard (`screen-home`)

**Purpose:** Greet the user, surface today's emotional state prompt, show a snapshot of recent mood, and provide entry points to Chat and Mood Profile.

**Layout (top to bottom):**

```
┌─────────────────────────┐
│  Good morning, Alex 👋  │  ← Personalized greeting (time-aware)
│  Saturday, Mar 28       │  ← Current date
├─────────────────────────┤
│  ┌───────────────────┐  │
│  │ How are you       │  │  ← Daily check-in card
│  │ feeling today?    │  │
│  │                   │  │
│  │ [Start Check-in →]│  │  ← CTA → navigates to screen-chat
│  └───────────────────┘  │
├─────────────────────────┤
│  This Week's Mood       │  ← Section label
│  ┌───────────────────┐  │
│  │ Mood mini-chart   │  │  ← Sparkline (7 days, CSS/SVG)
│  │ Mon▄ Tue█ Wed▃... │  │
│  └───────────────────┘  │
│  Tap to view full report│  ← Link → screen-mood
├─────────────────────────┤
│  Last conversation:     │  ← Memory recall teaser
│  "You mentioned feeling │
│   overwhelmed on Thu."  │
└─────────────────────────┘
```

**Simulated Data:**
- User name: "Alex" (hardcoded in `CONFIG`)
- Greeting: "Good morning" / "Good afternoon" / "Good evening" based on `new Date().getHours()`
- Mood sparkline: 7 hardcoded values representing Mon–Sun mood scores (1–5)

**Interactions:**
- "Start Check-in" button → navigate to `screen-chat`
- Mini-chart or "view full report" link → navigate to `screen-mood`

---

### 3.2 Screen: Chat — Text Mode (`screen-chat`)

**Purpose:** Simulate a real AI conversation. The AI recalls past context, responds supportively, and the prototype includes a scripted exchange demonstrating the "memory" and "taboo avoidance" features.

**Layout:**

```
┌─────────────────────────┐
│ ← Back    Companion  🎙️ │  ← Header: back btn, title, voice toggle
├─────────────────────────┤
│ ┌─────────────────────┐ │
│ │ 💬 Hey Alex! Last   │ │  ← AI bubble (opening recall)
│ │ time you mentioned  │ │
│ │ feeling stressed    │ │
│ │ about your deadline │ │
│ └─────────────────────┘ │
│         ┌─────────────┐ │
│         │ Yeah, still │ │  ← User bubble (right-aligned)
│         │ pretty rough│ │
│         └─────────────┘ │
│ ┌─────────────────────┐ │
│ │ 💬 I hear you. Want │ │
│ │ to tell me more     │ │
│ │ about what's been   │ │
│ │ hardest?            │ │
│ └─────────────────────┘ │
│              ·  ·  ·    │  ← Typing indicator (animated)
├─────────────────────────┤
│ [Type a message...] [➤] │  ← Input bar
│ [🎙️ Switch to Voice]    │  ← Voice mode toggle button
└─────────────────────────┘
```

**Scripted Conversation Flow (simulated replies triggered by user input or demo button):**

| Turn | Speaker | Content |
|---|---|---|
| 1 (Auto) | AI | "Hey Alex! Last time you mentioned feeling stressed about your deadline. How are things going today?" |
| 2 | User | (user types anything, or clicks "Send Demo Message") |
| 3 | AI | "I hear you. It sounds like work has been really weighing on you this week. How's your sleep been?" |
| 4 | User | (next send) |
| 5 | AI | "That's really common when stress builds up. I'm noticing your mood has been consistently lower this week. Would it help to talk about what's driving it?" |
| 6 | AI (auto, after 3s delay) | ⚠️ **Danger signal demo trigger appears** — a banner: "I'm noticing some concerning patterns. Would you like me to check in with your emergency contact?" |

**Memory Recall Indicator:**
- First AI message includes a subtle pill tag: `🧠 Remembering your last session`
- Tapping the pill shows a tooltip: "Your companion recalls key themes from past conversations"

**Taboo Topic Indicator:**
- If user types a message containing trigger words (configurable list in `CONFIG.tabooKeywords`), chat shows a soft inline notice:
  `"I'm not able to go there, but I'm still here for you. Want to talk about something else?"`
- This demonstrates the feature without building real NLP

**Voice Toggle Button:**
- Clicking `🎙️ Switch to Voice` → navigates to `screen-chat-voice`

**Demo Message Button:**
- A small `[Demo: Next Message →]` button floats above the input bar
- Each tap advances the scripted conversation one turn
- Allows stakeholders to walkthrough the flow without typing

---

### 3.3 Screen: Chat — Voice Mode (`screen-chat-voice`)

**Purpose:** Simulate voice conversation UX. Shows that the product supports hands-free emotional check-ins.

**Layout:**

```
┌─────────────────────────┐
│ ← Back to Text   🔇 End │
├─────────────────────────┤
│                         │
│                         │
│       ┌─────────┐       │
│       │  🎙️    │       │  ← Pulsing mic animation (CSS)
│       │ [●●●●] │       │  ← Animated sound wave
│       └─────────┘       │
│                         │
│   "Listening..."        │  ← Status label
│                         │
├─────────────────────────┤
│ ┌─────────────────────┐ │
│ │ Transcript:         │ │  ← Simulated transcript panel
│ │                     │ │
│ │ You: "Yeah, I just  │ │
│ │ feel really worn    │ │
│ │ out lately..."      │ │
│ │                     │ │
│ │ AI: "That makes     │ │
│ │ total sense. You've │ │
│ │ had a lot going on."│ │
│ └─────────────────────┘ │
│                         │
│    [End Voice Session]  │  ← Returns to screen-chat
└─────────────────────────┘
```

**Behavior:**
- Mic animation: CSS pulsing circle (no real audio API needed)
- Transcript auto-populates after 2s with hardcoded demo lines (typed-out effect via `setInterval`)
- "End Voice Session" button → returns to `screen-chat` with transcript appended to chat bubbles

---

### 3.4 Screen: Mood Profile (`screen-mood`)

**Purpose:** Show the user's emotional trend across the current week and provide AI-generated insights.

**Layout:**

```
┌─────────────────────────┐
│  📊 Your Mood Profile   │  ← Screen header
│  This week              │
├─────────────────────────┤
│  ┌───────────────────┐  │
│  │     Mood Trend    │  │  ← Bar chart (CSS only, no library)
│  │  5 ┤         █    │  │
│  │  4 ┤    █    █    │  │
│  │  3 ┤ █  █  █ █ █  │  │
│  │  2 ┤ █           │  │
│  │  1 ┤             │  │
│  │    Mon Tue Wed Thu Fri Sat Sun
│  └───────────────────┘  │
├─────────────────────────┤
│  Mood Tags This Week    │
│  [Stressed] [Tired]     │  ← Pill tags
│  [Focused] [Overwhelmed]│
├─────────────────────────┤
│  📝 Weekly Insight      │
│  ┌───────────────────┐  │
│  │ "Your mood dipped │  │
│  │ mid-week, likely  │  │
│  │ tied to your Tue/ │  │
│  │ Wed workload. Fri │  │
│  │ showed recovery.  │  │
│  │ Watch for burnout │  │
│  │ signs early next  │  │
│  │ week."            │  │
│  └───────────────────┘  │
├─────────────────────────┤
│  ⚠️ Burnout Risk: Moderate│ ← Risk indicator (color-coded)
│  [Talk to Companion →]  │  ← CTA → screen-chat
└─────────────────────────┘
```

**Simulated Data (hardcoded in `CONFIG.moodData`):**

```js
moodData: {
  week: ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'],
  scores: [3, 2, 3, 4, 4, 3, 5],   // 1=Low, 5=High
  tags: ['Stressed','Tired','Focused','Overwhelmed','Drained'],
  insight: "Your mood dipped mid-week...",
  burnoutRisk: 'moderate'   // 'low' | 'moderate' | 'high'
}
```

**Burnout Risk Color Coding:**

| Level | Color | Label |
|---|---|---|
| low | Green (`#6DBF9E`) | Low Risk |
| moderate | Amber (`#F5A623`) | Moderate — keep an eye on this |
| high | Red (`#E57373`) | High — your companion has flagged this |

**Chart Implementation:**
- Pure CSS bar chart using `<div>` elements with `height` set via inline style (`height: calc(var(--score) * 20%)`)
- No chart library required

---

### 3.5 Screen: Settings (`screen-settings`)

**Purpose:** Show app configuration options. Primary focus is the Emergency Contact entry point.

**Layout:**

```
┌─────────────────────────┐
│  ⚙️ Settings            │
├─────────────────────────┤
│  Account                │  ← Section header
│  ┌───────────────────┐  │
│  │ 👤 Alex           │  │  ← User name display
│  │ swmk.alex@...com  │  │  ← Email display
│  └───────────────────┘  │
├─────────────────────────┤
│  Safety                 │  ← Section header (highlighted)
│  ┌───────────────────┐  │
│  │ 🚨 Emergency      │  │  ← Emergency Contact row
│  │    Contact     →  │  │  ← Navigates to screen-emergency
│  │    Not configured │  │  ← Status badge (red dot)
│  └───────────────────┘  │
│  ┌───────────────────┐  │
│  │ 🔔 Alert Settings │  │  ← Alert threshold config (MVP: display only)
│  │              →    │  │
│  └───────────────────┘  │
├─────────────────────────┤
│  Preferences            │  ← Section header
│  ┌───────────────────┐  │
│  │ 🚫 Blocked Topics │  │  ← Taboo topics list (MVP: display only)
│  │              →    │  │
│  └───────────────────┘  │
│  ┌───────────────────┐  │
│  │ 🔒 Privacy & Data │  │
│  │              →    │  │
│  └───────────────────┘  │
└─────────────────────────┘
```

**Interactions:**
- Emergency Contact row → navigate to `screen-emergency`
- All other rows: tap shows a "Coming soon" toast notification (bottom of screen)

---

### 3.6 Screen: Emergency Contact Setup (`screen-emergency`) ⭐ New Feature

**Purpose:** Allow the user to configure a trusted person to be notified if the AI detects danger signals. This is the primary trust and safety feature of the product.

**Layout:**

```
┌─────────────────────────┐
│ ← Settings              │  ← Back button
│  🚨 Emergency Contact   │  ← Screen title
├─────────────────────────┤
│  ┌───────────────────┐  │
│  │ ℹ️ Who gets alerted│  │  ← Info card
│  │ If your companion │  │
│  │ detects signs of  │  │
│  │ serious distress, │  │
│  │ this person will  │  │
│  │ receive a message.│  │
│  └───────────────────┘  │
├─────────────────────────┤
│  Contact Details        │
│                         │
│  Full Name *            │
│  ┌───────────────────┐  │
│  │ e.g. Sarah Lee    │  │  ← Text input
│  └───────────────────┘  │
│                         │
│  Relationship *         │
│  ┌───────────────────┐  │
│  │ [Select ▼]        │  │  ← Dropdown: Partner / Parent /
│  └───────────────────┘  │     Friend / Sibling / Therapist / Other
│                         │
│  Contact Method *       │
│  ○ Mobile number        │  ← Radio: SMS or Email
│  ○ Email address        │
│                         │
│  ┌───────────────────┐  │
│  │ e.g. +1 555 000   │  │  ← Dynamic input (changes with radio)
│  └───────────────────┘  │
├─────────────────────────┤
│  Alert Threshold        │  ← Section: When should alert fire?
│                         │
│  Send alert when:       │
│  ○ I ask the AI to      │  ← Option A: manual
│  ○ AI detects high risk │  ← Option B: automatic (default)
│  ○ Both                 │  ← Option C
│                         │
│  Notify me too when     │
│  alert is sent  [✓]     │  ← Toggle (on by default)
├─────────────────────────┤
│  [Save Emergency Contact]│  ← Primary button
│  [Send a Test Alert]    │  ← Secondary button (demo trigger)
└─────────────────────────┘
```

**Form Validation Rules:**

| Field | Rule | Error Message |
|---|---|---|
| Full Name | Required, min 2 chars | "Please enter your contact's full name" |
| Relationship | Required (must select) | "Please select a relationship" |
| Contact Method | One must be selected | "Please choose SMS or email" |
| Mobile number | If SMS selected: valid format | "Enter a valid mobile number" |
| Email address | If email selected: valid format | "Enter a valid email address" |

**On Save (valid form):**
1. Inputs become read-only display (form → "saved" view)
2. Green confirmation banner: `✅ Emergency contact saved. [Your contact name] will be notified if we detect serious distress.`
3. Settings screen Emergency Contact row updates: red "Not configured" → green "Configured ✓"
4. "Send a Test Alert" button becomes active

**On "Send a Test Alert":**
- Triggers the `modal-alert` overlay in test mode
- Alert modal header shows: `📋 This is a test alert`

**Persistence:** Contact data stored in a JS object (`AppState.emergencyContact`) — survives screen navigation within the session but resets on page reload.

---

### 3.7 Modal: Danger Alert (`modal-alert`)

**Purpose:** Show exactly what happens when the AI detects a danger signal — making the safety feature tangible and transparent.

**Trigger Points:**

| Trigger | Source |
|---|---|
| Auto-triggered | After scripted turn 6 in Chat (after delay) |
| Manual test | "Send a Test Alert" in Emergency Contact screen |
| Demo button | Floating "⚡ Trigger Alert Demo" button on Home screen |

**Overlay Layout:**

```
┌─────────────────────────────────┐
│  ░░░░ (dark backdrop) ░░░░░░░░  │
│  ┌───────────────────────────┐  │
│  │  🚨 Alert Detected        │  │  ← Red header bar
│  │                           │  │
│  │  Your companion has       │  │
│  │  noticed some concerning  │  │
│  │  patterns in your recent  │  │
│  │  conversations.           │  │
│  │                           │  │
│  │  ─────────────────────    │  │
│  │  📲 Notifying:            │  │
│  │  Sarah Lee (Partner)      │  │  ← Emergency contact name
│  │  via SMS                  │  │  ← Contact method
│  │                           │  │
│  │  Message sent:            │  │
│  │  ┌─────────────────────┐  │  │
│  │  │ "Hi Sarah, Alex's   │  │  │  ← Simulated SMS preview
│  │  │ wellness companion  │  │  │
│  │  │ has flagged that    │  │  │
│  │  │ Alex may need       │  │  │
│  │  │ support right now.  │  │  │
│  │  │ Please check in."   │  │  │
│  │  └─────────────────────┘  │  │
│  │                           │  │
│  │  ─────────────────────    │  │
│  │  What would you like      │  │
│  │  to do?                   │  │
│  │                           │  │
│  │  [I'm okay — Cancel alert]│  │  ← Secondary (dismiss)
│  │  [Talk to my companion →] │  │  ← Primary → screen-chat
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

**States:**

| State | Behaviour |
|---|---|
| No emergency contact configured | Modal shows: "No emergency contact configured. [Set one up →]" instead of SMS preview |
| Test mode | Header shows "📋 Test Alert — No message was actually sent" |
| Live mode (demo) | Header shows "🚨 Alert Detected" |

**Dismiss Behavior:**
- "I'm okay — Cancel alert" → closes modal, returns to previous screen, adds a chat bubble: `"Glad you're okay. I'm here if you need me."`
- "Talk to my companion" → closes modal, navigates to `screen-chat`

---

## 4. Global UI Components

### 4.1 Bottom Tab Bar

Always visible (except during modal overlay).

| Tab | Icon | Label | Screen |
|---|---|---|---|
| Home | 🏠 | Home | `screen-home` |
| Chat | 💬 | Chat | `screen-chat` |
| Mood | 📊 | Mood | `screen-mood` |
| Settings | ⚙️ | Settings | `screen-settings` |

**Active state:** Tab icon + label in `--color-primary`, with a 2px top border indicator.

### 4.2 Toast Notifications

Temporary bottom-of-screen messages for non-critical feedback.

| Toast Type | Color | Duration |
|---|---|---|
| Success | Green | 3s auto-dismiss |
| Info | Blue | 3s auto-dismiss |
| Warning | Amber | 5s auto-dismiss |
| Error | Red | Stays until dismissed |

### 4.3 Header Bar

Each screen has a top header bar with:
- Left: Back button (← icon) or empty space if top-level screen
- Center: Screen title
- Right: Contextual action (e.g., 🎙️ in Chat, or empty)

---

## 5. App State Management

All state is held in a single `AppState` object in JavaScript:

```js
const AppState = {
  user: {
    name: CONFIG.userName,
    email: CONFIG.userEmail
  },
  currentScreen: 'screen-home',
  previousScreen: null,
  emergencyContact: {
    configured: false,
    name: '',
    relationship: '',
    method: '',       // 'sms' | 'email'
    value: '',        // phone number or email
    threshold: 'auto', // 'manual' | 'auto' | 'both'
    notifySelf: true
  },
  chat: {
    messages: [],         // { role: 'ai'|'user', text: string }
    scriptIndex: 0,       // tracks position in scripted replies
    voiceActive: false
  },
  moodData: CONFIG.moodData,
  alertModalOpen: false,
  alertTestMode: false
};
```

**Navigation function:**
```js
function navigateTo(screenId, options = {}) {
  AppState.previousScreen = AppState.currentScreen;
  AppState.currentScreen = screenId;
  // Hide all screens, show target screen
  // Update active tab in tab bar
}
```

---

## 6. Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| Markup | HTML5 (semantic) | Single `index.html` |
| Styling | CSS3 + Custom Properties | All in `<style>` block in `<head>` |
| Interactivity | Vanilla JS (ES6+) | All in `<script>` block before `</body>` |
| Fonts | Google Fonts CDN | Inter (body) + Lora (headings) |
| Icons | Unicode emoji / inline SVG | No external icon library |
| Hosting | Any static host | Netlify / Vercel / GitHub Pages |

**No npm. No build step. No frameworks. One file.**

---

## 7. Design Tokens

Defined as CSS Custom Properties at the `:root` level:

```css
:root {
  /* Brand Colors */
  --color-primary:       #4A6FA5;   /* Calm blue */
  --color-primary-light: #EBF0F8;   /* Light blue bg */
  --color-accent:        #6DBF9E;   /* Trust green */
  --color-danger:        #E57373;   /* Alert red */
  --color-warning:       #F5A623;   /* Moderate amber */

  /* Surfaces */
  --color-bg:            #FAFAF8;   /* App background */
  --color-surface:       #FFFFFF;   /* Cards */
  --color-border:        #E8E8E4;   /* Dividers */

  /* Text */
  --color-text-primary:  #1C2B3A;
  --color-text-secondary:#6B7280;
  --color-text-inverse:  #FFFFFF;

  /* Typography */
  --font-heading: 'Lora', Georgia, serif;
  --font-body:    'Inter', system-ui, sans-serif;
  --text-xs:      12px;
  --text-sm:      14px;
  --text-base:    16px;
  --text-lg:      18px;
  --text-xl:      22px;
  --text-2xl:     28px;

  /* Spacing */
  --sp-1: 4px;   --sp-2: 8px;   --sp-3: 12px;
  --sp-4: 16px;  --sp-6: 24px;  --sp-8: 32px;
  --sp-12: 48px; --sp-16: 64px;

  /* Phone frame */
  --frame-width:   390px;
  --frame-height:  844px;
  --frame-radius:  48px;

  /* Components */
  --radius-sm:  8px;
  --radius-md:  16px;
  --radius-full: 999px;
  --shadow-card: 0 2px 12px rgba(0,0,0,0.08);
  --shadow-modal: 0 8px 40px rgba(0,0,0,0.32);
  --transition:  0.2s ease-in-out;

  /* Tab bar */
  --tab-bar-height: 64px;
  --header-height:  56px;
}
```

---

## 8. `CONFIG` Object

Single source of truth for all configurable values. Defined at the top of the `<script>` block:

```js
const CONFIG = {
  // Branding
  productName: "[ProductName]",
  tagline: "Your daily emotional companion.",

  // Demo user
  userName: "Alex",
  userEmail: "alex@example.com",

  // Mood data (simulated)
  moodData: {
    week: ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'],
    scores: [3, 2, 3, 4, 4, 3, 5],
    tags: ['Stressed', 'Tired', 'Focused', 'Overwhelmed', 'Drained'],
    insight: "Your mood dipped mid-week, likely tied to your Tue/Wed workload. Friday showed signs of recovery. Watch for burnout signals early next week.",
    burnoutRisk: 'moderate'    // 'low' | 'moderate' | 'high'
  },

  // Scripted AI replies (in order)
  chatScript: [
    "Hey Alex! Last time you mentioned feeling stressed about your deadline. How are things going today?",
    "I hear you. It sounds like work has been really weighing on you this week. How's your sleep been?",
    "That's really common when stress builds up. I'm noticing your mood has been consistently lower this week. Would it help to talk about what's driving it?",
  ],

  // Taboo keyword triggers (simulated detection)
  tabooKeywords: ['quit', 'give up', 'hopeless', 'can\'t go on'],

  // Alert timing in demo (ms after final chat turn)
  alertDelay: 3000,

  // Simulated SMS template (use {{name}} as placeholder for user name)
  alertSmsTemplate: "Hi {{contact}}, {{name}}'s wellness companion has flagged that {{name}} may need support right now. Please check in.",
};
```

---

## 9. Animations & Micro-interactions

| Element | Animation | Implementation |
|---|---|---|
| Screen transitions | Slide in from right (enter) / slide out to left (exit) | CSS `transform: translateX` + JS class toggle |
| Chat bubble appear | Fade in + slight translateY | CSS `@keyframes` on `.bubble.new` |
| AI typing indicator | Three pulsing dots | CSS `@keyframes` with staggered `animation-delay` |
| Mic pulse (voice mode) | Expanding ring pulse | CSS `@keyframes scale + opacity` |
| Mood bars | Grow from 0 height on screen enter | CSS `@keyframes` height transition |
| Alert modal | Scale in from 0.9 + fade | CSS `transform: scale` + `opacity` |
| Toast notifications | Slide up from bottom, auto-dismiss | CSS + JS `setTimeout` |
| Tab active indicator | Instant color + underline update | CSS class swap |
| Form save confirmation | Green banner slides in | CSS transition |

---

## 10. Accessibility

| Requirement | Implementation |
|---|---|
| Semantic HTML | `<header>`, `<main>`, `<nav>`, `<section>`, `<button>` |
| Keyboard navigation | All tappable elements are `<button>` or `<a>` (not `<div>`) |
| ARIA labels | `aria-label` on icon-only buttons; `role="dialog"` on modal |
| Focus management | Modal traps focus; focus returns to trigger on close |
| Color alone never used | All status indicators use icon + color + label |
| Reduced motion | `@media (prefers-reduced-motion)` disables slide/scale transitions |

---

## 11. Acceptance Criteria

| # | Criterion | Verification |
|---|---|---|
| 1 | All 6 screens render without errors | Manual walkthrough |
| 2 | Bottom tab bar navigates to correct screen | Click each tab |
| 3 | Back button returns to correct previous screen | Navigate deep, press back |
| 4 | Chat scripted replies advance correctly | Click "Demo: Next Message" 6× |
| 5 | Voice screen shows animated mic and transcript populates | Navigate to voice mode |
| 6 | Mood chart renders with correct bar heights | Visual check on screen-mood |
| 7 | Emergency contact form validates all fields | Submit with empty fields; invalid formats |
| 8 | Saved contact name appears in Settings row | Save contact → return to Settings |
| 9 | Alert modal shows correct contact name and SMS preview | After saving contact, trigger alert |
| 10 | Alert modal "I'm okay" dismisses and adds chat bubble | Click dismiss button |
| 11 | Test mode alert shows correct "test" header | Trigger from Emergency Contact screen |
| 12 | Taboo keyword shows inline notice (not AI reply) | Type a CONFIG.tabooKeyword and send |
| 13 | No emergency contact → alert modal shows setup prompt | Trigger alert before configuring contact |
| 14 | Page loads < 3s on simulated 4G | Chrome Lighthouse throttle |
| 15 | No console errors on full walkthrough | DevTools console |
| 16 | Responsive: phone frame fills viewport on < 480px | Chrome DevTools mobile emulation |

---

*Spec v2.0 — Updated for interactive prototype scope. All `[ProductName]` references configurable via `CONFIG.productName`.*
