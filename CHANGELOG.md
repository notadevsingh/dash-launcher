# Changelog

All notable changes to Dash Launcher are documented here.

> [!NOTE]
> This repository contains binary releases only. APKs are attached to each GitHub Release.
> Download the latest APK from the [Releases](../../releases) page or install from [Google Play](https://play.google.com/store/apps/details?id=dev.soraatelier.dashlauncher).

---

## [2.1.0] — Latest

### ✨ Redesigned Onboarding Experience

- **Structured 5-step guided setup** — Replaced the floating tutorial overlay with a calm, step-by-step setup wizard inspired by OpenMiniLaunch:
  - **Scribble & gestures** — Clear, uncluttered introduction to on-screen handwriting search, stroke filtering, swipe-to-erase, and app drawer navigation.
  - **Style & accessibility typography** — Choose your starting home layout style (*Minimalist Text*, *Classic Agenda*, or *Doodle Pop Ink*) and select your preferred reading typeface (*Default*, *Monolexic* dyslexia-friendly, or *Monolegible* high-contrast) with live preview.
  - **Smart dock options** — 1-tap **Default Essentials** (auto-detects and pins your system Phone, Messages, Browser, and Camera), **Custom Favorites** (search and handpick any 4 apps), or **Hide Dock** for a pure edge-to-edge minimalist screen.
  - **Skip guidance** — Tapping Skip provides an intuitive choice to apply default essentials, hide the dock, or continue.
  - **Transparent privacy & permissions** — Honest, upfront context explaining how Usage Access and Calendar Smart Join work strictly on-device without telemetry or cloud syncing.
  - **Smooth default launcher handoff** — Simple instructions and one-tap setup to set Dash Launcher as your default home app.
- **Hardware back gesture support** — Navigate backwards through setup steps naturally using your device's back gesture.

### ✨ New features & Accessibility

- **High-legibility & dyslexia-friendly font options** — Added a new **Font style** selector under Settings → Accessibility:
  - **Monolexic** — A dyslexia-friendly typeface with disambiguated letterforms inspired by Atkinson Hyperlegible principles (distinct stems, hooked letters, tailed stems, and clear zero/one shapes) to prevent mirror confusion and improve scanning comfort.
  - **Monolegible** — A structured, high-contrast typeface with expanded letter spacing for low-vision reading.
  - Seamlessly integrates with the existing **Bold text** toggle and scales consistently across the home screen, app drawer, and clock.
- **Typography credits** — Added attribution and a link to the typography colophon in Settings → About, honoring Steven Hum (`sdothum`) for the custom Iosevka builds.

---

## [2.0.1]

### 🐛 Bug fixes & Improvements

- **Handwriting auto-launch reliability** — fixed an issue where writing multi-stroke letters (like "i" or "j") could prematurely launch an app before you finished dotting the letter.
- **Accidental scribble dismissal guard** — small accidental taps or brief finger movements on the home screen no longer dismiss your active search query.

---

## [2.0.0]

### ✨ New features

#### Theme engine & layout styles
- **Minimal** (new default) — lightweight clock + text app list + dock. Fast to load, low memory footprint.
- **Classic** — full-featured layout with top bar, screen time card, Google search card, and all widgets.
- **Doodle Pop Ink** — warm paper aesthetic with ink-bordered sticker cards, hand-drawn fonts, and alternating row tilts. Light-only; selectable from Settings → Home Layout Style.
- Each layout style now ships its own default settings; switching themes automatically resets settings that the new theme does not support (e.g. switching away from Classic resets App Theme, Accent Color, Wallpaper).

#### Handwriting recognition improvements
- **Multi-stroke character support** — lowercase `i`, `j`, `!`, `?`, and other dotted/accented characters now recognized correctly. Previously the dot stroke was silently discarded.
- **Stationary tap filter** — tapping app rows no longer triggers a false "dot" recognition result and pollutes the search state.
- **Interactive bounds registry** — handwriting strokes are accepted over the app list and dock canvas; taps on the clock, search bar, and widgets are correctly routed to those controls.
- **Lifecycle reset** — recognized text is cleared when you return from another app, preventing stale search state from persisting.
- Exponential backoff model download (up to 5 retries: 15 s → 30 s → 60 s → 120 s → 240 s). `checkAndRetryIfNeeded()` is called on every resume.

#### Calendar Agenda Panel
- New **Calendar** home display mode (Classic layout). Replaces the app list with today's calendar events.
- **Smart Join** — in-progress meetings or meetings starting within 15 minutes show a Join button automatically. The earliest joinable meeting is highlighted as Active; others are shown as Soon.
- **Platform micro-badges** — Meet (M), Zoom (Z), Teams (T), Webex (W) badges appear next to event times. Badges are suppressed when the platform name is already in the event title.
- **Conflict detection** — back-to-back chains of 3+ tight events show an aggregate header; isolated pairs show an inline "0 min gap" / "Overlaps" tag.
- Discrete-tap pagination ("Show Next" / "Show From Start") — no scrolling required.
- Permission-denied state: shows "Calendar access off" with a direct link to system settings.

#### Work profile support
- Personal and Work tabs appear in the app drawer when a work profile is present.
- Work apps launch in their own profile via `LauncherApps`.
- Long-pressing a work app shows "Open Work Profile Settings" instead of "Uninstall" (platform limitation on primary-user launchers).
- Work-profile icons are correctly badged.

#### Icon packs & tinting
- **Third-party icon pack support** — install any icon pack that uses the `org.adw.launcher.THEMES` or `com.novalauncher.THEME` standard (e.g. Arcticons). Select from Settings → Appearance → Icon Pack.
- **Adaptive icon tinting** (off by default) — optionally tint monochrome icon packs to match your theme. Three modes:
  - **Auto-contrast** — white icons on light backgrounds get a dark tint; dark icons on dark backgrounds get a white tint. Already-contrasted icons are left alone.
  - **Match theme** — fixed dark/light color matching the active theme mode.
  - **Accent color** — uses your current accent preset.
  - Full-color icons (e.g. Chrome, YouTube) are never tinted — a saturation guard ensures only ≥ 88% greyscale icons are eligible.

#### App sorting
- **Home screen app order** — choose between "Recently Used" (default) and "Most Used Today" (requires Usage Access).
- **App drawer order** — choose between Alphabetical A–Z (default), Alphabetical Z–A, or Recently Used.

#### System wallpaper
- Use your system wallpaper as the home screen background with an adjustable scrim opacity (80–98%).
- Wallpaper rendering is fully stable across device reboots and theme switches.

### 🐛 Bug fixes

- **Duplicate app keys crash** (Motorola / some OEM devices) — fixed `IllegalArgumentException` in `LazyColumn` caused by devices that register multiple launcher activities per package. The correct canonical activity is now resolved per package.
- **Dot recognition on tap** — tapping the home screen or app rows no longer produces a stray `.` in the search bar.
- **Recognized text persisting after leaving the launcher** — search is cleared on resume when returning from another app.
- **Calendar pagination clipping** — the "Show Next" / "Show From Start" footer no longer gets clipped when the event list fills the screen.
- **Conflict tag readability** — "0 min gap" and "Overlaps" tags are now displayed at higher contrast with medium font weight.
- **System bar icon contrast** — status bar and navigation bar icons now switch correctly between dark and light variants when changing layout styles (e.g. switching to Doodle Pop Ink's light background no longer leaves invisible white status bar icons).
- **Theme settings leaking across layouts** — settings such as App Theme, Wallpaper, and Text-Only Mode are no longer applied to layout styles that don't support them.
- **ML Kit recognizer memory leak** — a `DigitalInkRecognizer` instance created after `close()` is now correctly guarded and immediately disposed.
- **Gesture cancel handling** — a cancelled touch sequence (e.g. system interrupt during drawing) no longer leaves a partial ink trail on screen or leaks a `VelocityTracker`.
- **Recency-only sort updates** — switching to "Recently Used" order now correctly reorders apps when only `lastUsed` timestamps change (no label/icon change).

### ⚡ Performance
- Icon pack loading: Arcticons' 35,000+ `appfilter.xml` entries are pre-filtered against installed packages, reducing parsed entries to ~100.
- Resource ID lookups cached per icon pack — `getIdentifier()` IPC is called at most once per drawable.
- App update-time queries use a single bulk `PackageManager` call instead of one IPC call per app.
- App icon cache and metadata (monochrome detection, luminance) pre-warmed for pinned dock and recent apps on startup.
- Clock formatter objects hoisted out of the per-second update loop — no per-tick allocation.
- App drawer no longer triggers a blur pass when opening (performance improvement; the opaque drawer background is sufficient).

---

*For older release notes, see [GitHub Releases](../../releases).*
