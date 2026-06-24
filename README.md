# Guardian Times — Android app

A lightweight **Capacitor** wrapper around the live Guardian Times edition.

The app's WebView loads **https://amangoyal4.github.io/guardian-times/**, so it
**always shows today's edition automatically** — the daily 7:00 AM build updates the
content; the app needs **no update** for new editions. The app only needs rebuilding
when something *native* changes (icon, version, plugins).

- **App name:** Guardian Times
- **Package / appId:** `in.gcia.guardiantimes`  ← confirm this is free & matches your Play account; it **cannot change after first publish**
- **Native features:** branded splash, dark themed status bar, offline fallback page, external links open in the system browser.

---

## What the build machine had (and didn't)

This project was **scaffolded and configured** on a machine **without** the Android
SDK / JDK, so it has **not been compiled** here. Producing the installable app is the
final step, done in **Android Studio** (which your existing app team already has).
Everything else is done.

## Prerequisites (on the app team's machine)

- **Android Studio** (latest)
- **JDK 17**
- **Node 18+** (to run Capacitor sync) — `node -v`

## First-time setup

```bash
npm install            # restore dependencies
npx cap sync android   # copy config + plugins into the android/ project
npx cap open android   # opens the project in Android Studio
```

## Build a test APK (to try on a phone)

In Android Studio: **Run ▶** with a device/emulator, **or** from a terminal:

```bash
cd android
./gradlew assembleDebug
# output: android/app/build/outputs/apk/debug/app-debug.apk  (sideload to test)
```

## Build the release bundle for the Play Store

1. Create an upload keystore **once** (keep it safe — losing it means you can't update the app):
   ```bash
   keytool -genkey -v -keystore guardian-upload.jks -keyalg RSA -keysize 2048 -validity 10000 -alias guardian
   ```
2. In Android Studio: **Build ▸ Generate Signed Bundle / APK ▸ Android App Bundle**, select the keystore, build **release**.
   - Output: an **`.aab`** to upload to the Play Console.
3. Bump the version for each release in `android/app/build.gradle` (`versionCode` +1, `versionName`).

## App icon (the one asset still needed)

The launcher icon is currently the Capacitor placeholder. To brand it, drop a square
logo and let Capacitor generate every size:

```bash
mkdir resources
# put a 1024x1024 PNG at resources/icon.png  (and optionally resources/splash.png 2732x2732)
npm install @capacitor/assets --save-dev
npx @capacitor/assets generate --android
npx cap sync android
```

A clean square monogram (e.g. a "GT" crest on the dark #16161a background) works far
better as an icon than the full wordmark.

## Play Store listing checklist (one-time, in the Play Console)

- [ ] App icon **512×512** PNG
- [ ] Feature graphic **1024×500**
- [ ] **2–8 phone screenshots** (run the app, screenshot the edition + the audio player + a section)
- [ ] Short + full description
- [ ] **Privacy policy URL** (required). Good news: the app collects **no personal data** — a simple "we collect nothing" policy suffices. Host it as a page on the site.
- [ ] **Data safety form** → declare "no data collected/shared"
- [ ] **Content rating** questionnaire (news/finance → likely Everyone)
- [ ] **Financial disclaimer** in the description: *"For information and education only; not investment advice."*

## Configuration reference

- Live URL, splash, status bar, offline page: **`capacitor.config.json`**
- Android permissions / app entry: `android/app/src/main/AndroidManifest.xml`
- App display name: `android/app/src/main/res/values/strings.xml`
- Version: `android/app/build.gradle`

## Known follow-ups (not blocking a first release)

- **Push notifications** ("today's edition is ready") — add `@capacitor/push-notifications` + Firebase. ~1 session of work.
- **Offline reading of the last edition** — add a service worker to the site so the WebView caches it.
- **External-link polish** — verify "Read at source" / YouTube links open in the system browser on a real device; if any `target="_blank"` link does nothing, we add a small handler.
- **iOS twin** — same project adds iOS via `npx cap add ios` (needs a Mac + Xcode).
