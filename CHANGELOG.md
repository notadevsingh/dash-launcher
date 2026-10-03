# Changelog

All notable changes to Dash Launcher are documented here.

> [!NOTE]
> This repository contains binary releases only. APKs are attached to each GitHub Release.
> Download the latest APK from the [Releases](../../releases) page or install from [Google Play](https://play.google.com/store/apps/details?id=dev.soraatelier.dashlauncher).

---

## [2.0.0] — Latest

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
