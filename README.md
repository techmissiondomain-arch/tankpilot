# Tankpilot 🇧🇪

Find the cheapest / nearest fuel station near you in Belgium. Petrol 95, Petrol 98, Diesel.

- **Live web app:** https://techmissiondomain-arch.github.io/tankpilot/
- **Stations & distances:** real, from OpenStreetMap (Overpass API).
- **Prices:** Belgium's official maximum prices (FPS Economy) as the reference,
  plus a community **"report price"** feature stored on your device.

## Project layout
- `docs/index.html` — the whole app (single file). Served by GitHub Pages **and**
  packaged into the Android app.
- `capacitor.config.json`, `package.json` — Capacitor setup (web → native Android).
- `.github/workflows/android.yml` — builds the Android APK in the cloud on every push.

## Get the Android app (APK)
1. Open the **Actions** tab on GitHub → latest **Build Android APK** run.
2. Download the **tankpilot-debug-apk** artifact (a `.zip`).
3. Unzip → copy `app-debug.apk` to your phone → tap it to install
   (allow "install from unknown sources" when prompted).

## Update the official prices
Edit the `BE_OFFICIAL` values + `date` near the top of the script in `docs/index.html`.
