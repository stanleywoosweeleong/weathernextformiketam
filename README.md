# Miketam 农友天气 · WeatherNext

A single-file Progressive Web App (PWA) delivering farm weather forecasts for
plantation locations around Mentakab, Pahang. Bilingual interface
(中文 / English) with optional AI-generated farming briefings.

Part of the WeatherNext family of per-region agricultural weather builds.

---

## Live app

Once GitHub Pages is enabled, the app is served at:

```
https://stanleywoosweeleong.github.io/weathernextformiketam/
```

Open that link on a phone and use **"Add to Home Screen"** to install it as an
app. It works offline after the first visit (service-worker cached).

---

## Seeded locations

On first launch the app seeds the 7 farms below. They are all auto-favourited
and can be renamed, edited, or deleted freely afterwards. Add as many more
farms as you like from inside the app.

| English | 中文 | Coordinates |
|---|---|---|
| Lanchang (Durian) | 联增 | 3.42925, 102.13928 |
| Draha (Durian) | 德拉哈 | 3.40339, 102.28300 |
| Jambu Rias | 占布里亚斯 | 3.39956, 102.14848 |
| Empresa | 皇后园 | 3.36379, 102.26080 |
| Mentakab | 文德甲 | 3.37169, 102.31093 |
| Mengkarak | 明加叻 | 3.32936, 102.31455 |
| Kuala Lipis | 瓜拉立卑 | 4.124748, 102.088034 |

The app also seeds a default user display name (**苏晓薇** / Soh Siow Wei),
which stays editable via **Edit Name** in the app.

*Seed v1:* initial Miketam build.

---

## API key — bring your own (important)

This app **does not ship with an embedded API key.** AI features (the farming
briefings) are powered by Google's Gemini API, and each user supplies their
own free key.

To enable the AI briefing:

1. Visit https://aistudio.google.com/app/apikey
2. Click **"Create API key"** — it's free.
3. In the app, open the **API Key** modal and paste the key (starts with `AIzaSy...`).

The key is stored only in that device's browser (`localStorage`) and is never
uploaded anywhere or committed to this repo. The core weather forecast works
without a key — only the AI briefing needs one.

**Why no embedded key:** a key bundled into a public web app is visible to
anyone who opens browser DevTools. Google actively scans public repositories
and will automatically disable a key found exposed, which would break the
feature for everyone at once. Keeping keys per-user and per-device avoids that
failure mode entirely.

**Recommended for users:** restrict your key in Google Cloud Console
(Application restrictions → Websites) to `stanleywoosweeleong.github.io/*`,
and limit it to the Generative Language API. This reduces abuse risk if the
key is ever scraped.

---

## Deploying

All 7 files live in the **repository root** — the service worker and manifest
use relative `./` paths, so a root deploy works with no changes.

```
index.html            — the app (single file: HTML + CSS + JS)
manifest.json         — PWA metadata
sw.js                 — service worker (offline cache)
icon-512.png          — app icon 512×512
icon-192.png          — app icon 192×192
apple-touch-icon.png  — iOS home-screen icon 180×180
favicon-32.png        — browser tab icon 32×32
```

To enable GitHub Pages: **Settings → Pages → Source: Deploy from branch →
`main` / `root`.** Pages serves over HTTPS automatically, which the service
worker requires.

### Updating the app

The service worker caches the app shell. When you push changes, bump the
`CACHE_VERSION` string at the top of `sw.js` so users receive the update on
their next visit. The current value is:

```
wnext-weathernextformiketam-202605240630
```

---

## Tech notes

- **Weather data:** Open-Meteo API (no key required, network-first with cache fallback).
- **AI model:** `gemini-2.5-flash` via the Generative Language API.
- **Storage namespace:** `weathernextformiketam__*` keys in `localStorage`,
  isolated from other WeatherNext regional builds so data never collides.
- **Cloud sync:** Firebase, namespaced under `appId: wnext-ag-v41-weathernextformiketam`.
- **Offline:** full app shell + last-fetched weather cached by the service worker.
