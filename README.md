<p align="center">
  <img src="assets/brand/kaarya-icon-1024.png" alt="Kaarya icon" width="120">
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/brand/kaarya-wordmark-white.png">
    <img src="assets/brand/kaarya-wordmark.png" alt="Kaarya" width="280">
  </picture>
</p>

# Kaarya

![Status](https://img.shields.io/badge/status-in%20development-orange)
![PWA](https://img.shields.io/badge/PWA-installable-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![React](https://img.shields.io/badge/React-18-61dafb)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178c6)

**Kaarya** (Sanskrit for *task* or *work*) is an offline-first task and reminder manager built as a Progressive Web App (PWA). Lists, tags, smart filters, calendar views and push reminders. Installable on any phone, no app store needed.

> 🚧 **Status:** Under active development (v0.1). First working release: October 2026.

Built by [AVC IT Solutions](https://github.com/AVC-IT-SOLUTIONS).

---

## Table of contents

- [Why Kaarya?](#why-kaarya)
- [Features](#features)
- [How it works](#how-it-works)
- [Tech stack](#tech-stack)
- [Getting started](#getting-started)
- [Project structure](#project-structure)
- [Contributing](#contributing)
- [Testing checklist](#testing-checklist)
- [FAQ](#faq)
- [License](#license)

---

## Why Kaarya?

Most task apps lock their best features behind a paid subscription: calendar views, smart filters, task duration, persistent reminders, themes. They also cap how many lists and tasks you can create.

Kaarya makes all of these **free, with no limits**. And because it is a PWA, one codebase runs on Android, iPhone and desktop, with no app store approval and instant updates.

## Features

### ✅ Core (v1.0)

| Feature | What it does |
| --- | --- |
| **Tasks** | Title, notes, due date and time, priority (high / medium / low / none), tags, subtasks |
| **Quick add** | Add a task in seconds from a floating + button, with date, priority, tag and list shortcuts |
| **Today view** | Overdue and Today sections, collapsible, with a one-tap *Postpone* for all overdue tasks |
| **Smart lists** | Inbox, Next 7 Days, Completed, Trash |
| **Lists and tags** | Custom colours, reordering, archiving, live task counts in the sidebar |
| **Reminders** | Multiple reminders per task; push notifications with *Complete* and *Snooze*, even when the app is closed |
| **Recurring tasks** | Daily, weekly (chosen weekdays), monthly, yearly |
| **Offline-first** | Everything works in airplane mode; changes are never lost |
| **Search** | Instant search across titles, notes and tags |
| **Installable** | Add to Home Screen on Android and iPhone (iOS 16.4+) |

### 🔜 Next (v1.1)

| Feature | What it does |
| --- | --- |
| **Calendar** | Year, Month, Week, 3-Day and Day views; drag to reschedule |
| **Smart filters** | Combine date, priority, list and tag conditions with AND / OR, saved in the sidebar |
| **Kanban** | Board view for any list, with draggable columns |
| **Duration** | Start and end times, shown as blocks on the calendar |
| **Constant reminder** | Keeps alerting until the task is done or snoozed |
| **Themes** | Colour themes, dark mode, auto night mode |
| **Swipe actions** | Configurable quick actions on task rows |

### 🗺️ Roadmap

- [ ] Natural-language quick add: `Call bank tomorrow 5pm !high #finance`
- [ ] Eisenhower Matrix (urgent / important quadrants)
- [ ] Pomodoro focus timer linked to tasks
- [ ] Habit tracker with streaks
- [ ] Countdowns for special days
- [ ] Timeline (Gantt) view
- [ ] Statistics and completion trends
- [ ] Account sync across devices
- [ ] CSV / JSON import and export
- [ ] App lock with passcode

## How it works

Kaarya is **local-first**:

1. Every change is written to **IndexedDB** on the device first, so the interface is instant and works offline.
2. A **service worker** caches the app, so it opens even with no connection.
3. When online, changes **sync** to the backend (Supabase Postgres).
4. Reminders are scheduled on the **server**, which sends **Web Push** messages at the right time, so notifications arrive even when the app is closed.

```
Phone (PWA)                          Server
┌────────────────────────┐          ┌─────────────────────────────┐
│ React UI               │          │ Supabase Postgres           │
│    ↕                   │   sync   │   tasks, lists, tags        │
│ Dexie / IndexedDB  ────┼─────────►│   push subscriptions        │
│    ↕                   │          │                             │
│ Service worker    ◄────┼──────────┤ Scheduled job + web-push    │
│ (offline + push)       │   push   │ (sends due reminders)       │
└────────────────────────┘          └─────────────────────────────┘
```

## Tech stack

| Layer | Technology |
| --- | --- |
| Frontend | React 18, TypeScript, Vite |
| Styling | Tailwind CSS, Lucide icons |
| State | Zustand |
| Local database | Dexie.js (IndexedDB) |
| PWA | vite-plugin-pwa (Workbox) |
| Dates and recurrence | date-fns |
| Drag and drop | dnd-kit |
| Backend | Supabase (Postgres, Auth, Edge Functions) |
| Push notifications | Web Push API, `web-push`, scheduled job |
| Hosting | Vercel |

## Getting started

### Prerequisites

- Node.js 20 or later
- npm
- A free [Supabase](https://supabase.com) project (for sync and reminders)

### Installation

```bash
git clone https://github.com/AVC-IT-SOLUTIONS/kaarya-app.git
cd kaarya-app
npm install
cp .env.example .env    # fill in your own values
npm run dev
```

Open the local URL shown in the terminal.

> 📱 To test installing the app and receiving notifications on a phone, use the deployed **HTTPS** URL. PWAs and push notifications do not work over plain HTTP.

### Environment variables

| Variable | Where | Description |
| --- | --- | --- |
| `VITE_SUPABASE_URL` | Client | Supabase project URL |
| `VITE_SUPABASE_ANON_KEY` | Client | Supabase public (anon) key |
| `VITE_VAPID_PUBLIC_KEY` | Client | Public key for Web Push |
| `VAPID_PRIVATE_KEY` | Server only | Private key for Web Push |
| `SUPABASE_SERVICE_ROLE_KEY` | Server only | Service key for the reminder job |

Generate VAPID keys with:

```bash
npx web-push generate-vapid-keys
```

> ⚠️ **This is a public repository.** Never commit `.env` or any real key. Only `.env.example` with empty values belongs in Git. If a key is ever committed by mistake, rotate it immediately.

### Scripts

| Command | What it does |
| --- | --- |
| `npm run dev` | Start the development server |
| `npm run build` | Create a production build |
| `npm run preview` | Preview the production build locally |
| `npm run lint` | Check code with ESLint |
| `npm run format` | Format code with Prettier |

### Deployment

The app deploys automatically to **Vercel** on every push to `main`. Every pull request gets its own preview URL, so changes can be tested on a phone before merging.

## Project structure

```
kaarya-app/
├── public/               # App icons and static assets
├── src/
│   ├── features/         # One folder per feature
│   │   ├── tasks/
│   │   ├── lists/
│   │   ├── calendar/
│   │   ├── filters/
│   │   └── settings/
│   ├── components/       # Shared UI components
│   ├── db/               # Dexie schema and queries
│   ├── sw/               # Service worker (offline cache, push)
│   ├── store/            # Zustand stores
│   └── lib/              # Helpers (dates, recurrence)
├── server/               # Push scheduling and sync functions
├── .env.example
├── LICENSE
└── README.md
```

## Contributing

1. `main` is protected. All changes go through a pull request.
2. Create one branch per feature or fix:
   - `feature/quick-add`
   - `fix/overdue-count`
3. Commit small and often, at least once a day, with clear messages:
   - `feat: add postpone button to overdue section`
   - `fix: recurring weekly task skips Sunday`
4. Before opening a PR, run `npm run lint` and `npm run build`.
5. Every PR needs a short description; UI changes need a phone screenshot.

## Testing checklist

Before each release, verify on a **real Android phone** and a **real iPhone**:

- [ ] App installs from the browser and opens full-screen
- [ ] Works fully in airplane mode; changes persist after closing the app
- [ ] Reminder arrives with the app closed; *Complete* and *Snooze* work
- [ ] Recurring task creates the next occurrence on the correct date
- [ ] Overdue tasks show in red and *Postpone* moves them to today
- [ ] Lighthouse (mobile): PWA and Performance scores 90+

## FAQ

**Do I need to download it from an app store?**
No. Open the website on your phone and choose *Add to Home Screen*. It then behaves like a normal app.

**Does it work without internet?**
Yes. You can view, add, edit and complete tasks offline. Changes sync when you're back online.

**Why don't I get notifications on my iPhone?**
On iPhone, notifications only work after the app is added to the Home Screen, on iOS 16.4 or later. Open it from the Home Screen icon and allow notifications when asked.

**Is it free?**
Yes. Every feature is free, with no limits on lists or tasks.

## License

Released under the [MIT License](LICENSE).

## Team

Designed and maintained by **AVC IT Solutions**.
