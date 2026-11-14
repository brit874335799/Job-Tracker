# Job Records — Mobile Service Records (Android / Capacitor)

A fully offline, single-file mobile service app (`www/index.html`) packaged as a native Android APK with Capacitor for a one-person mobile oil-change and battery-swap service.

## Highlights & Design Choices

- **Zero network / zero cloud / zero permissions:** All business data lives locally on the device in `localStorage` under a single versioned JSON key (`job_records_data_v1`). No internet connection, accounts, analytics, or extra Android permissions are requested.
- **Backup & CSV export storage (Android 10–15):** Backups and CSV exports write to `Documents/JobRecords/` using `@capacitor/filesystem` (`Directory.Documents`). On Android 11–15, writing app-created files to a subfolder inside `Documents` works through scoped storage without broad `MANAGE_EXTERNAL_STORAGE` permissions, and files are easy to find in the Android Files app under **Documents > JobRecords**. If writing directly fails on an older or restricted device, the app automatically writes to cache and opens the Android share sheet (`@capacitor/share`) so you can save to Drive, Files, WhatsApp, or email.
- **Phone, navigation, and receipts:** Tapping a phone number uses a standard `tel:` link; tapping an address opens Waze or Google Maps via standard URLs; sending a receipt uses the Android share sheet — none of these require runtime permissions.

## Option 1: Build the APK in the Cloud (No Computer Required)

This project includes a GitHub Actions workflow (`.github/workflows/build-apk.yml`) that compiles the Android APK automatically in the cloud:

1. In Google AI Studio, click the **GitHub** button in the top bar to push this project to a new (private or public) GitHub repository.
2. Open your repository on `github.com` in Chrome on your phone and tap the **Actions** tab.
3. Open the **Build Android APK** workflow run (it starts automatically on push, or tap **Run workflow**).
4. Wait ~2–3 minutes until it shows a green checkmark, scroll down to **Artifacts**, and tap **JobRecords-APK** to download the ZIP.
5. Open the downloaded ZIP in your Android **Files** app, extract **`JobRecords.apk`**, tap it, allow **Install unknown apps** if prompted, and tap **Install**.

## Option 2: Build the APK Locally (Android Studio)

### Prerequisites

- **Node.js 20+**
- **Android Studio** (with Android SDK and platform tools installed)

### Steps

From the project root directory:

1. Install dependencies:
   ```bash
   npm install
   ```
2. Add the Android native project (first time only):
   ```bash
   npx cap add android
   ```
3. Generate Android launcher & adaptive icons from `assets/`:
   ```bash
   npx @capacitor/assets generate --android
   ```
4. Copy `www/index.html` into the Android project:
   ```bash
   npx cap sync
   ```
4. Open the project in Android Studio:
   ```bash
   npx cap open android
   ```
5. In Android Studio, wait for Gradle sync to finish, then click **Build > Build Bundle(s) / APK(s) > Build APK(s)**.
6. Your debug APK will be generated at:
   ```
   android/app/build/outputs/apk/debug/app-debug.apk
   ```

## Installing, Updating, and Data Safety

- **Updating keeps your data:** Installing a newer `app-debug.apk` over an existing installation preserves all `localStorage` data (customers, jobs, fuel, settings).
- **Uninstalling erases data:** Uninstalling the app from Android permanently deletes its `localStorage`. Always save a JSON backup before uninstalling or switching phones.

## Rebuilding After Future Edits to `www/index.html`

Whenever you edit `www/index.html`:

1. Run:
   ```bash
   npx cap sync
   ```
2. Rebuild the APK in Android Studio (**Build > Build Bundle(s) / APK(s) > Build APK(s)**), or from the `android/` folder run `./gradlew assembleDebug`.
