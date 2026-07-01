# Tankpilot v1.5.0 — Test & Launch Plan

Goal: make **v1.5.0 stable and ready for real users**. No new features. Work top to bottom;
don't proceed to launch (§7+) until the road test (§1–§6) passes.

---

## 1. Real-world Samsung road test checklist

**Before you drive (do at home):**
- [ ] Install v1.5.0 APK; open the app.
- [ ] Set up your car: tank size, fuel type, average consumption.
- [ ] Tap **I filled up** (or Adjust level) so the gauge shows a real starting range.
- [ ] Grant **Location = Allow all the time** and **Notifications = Allow** (see §2).
- [ ] Turn off battery optimization for Tankpilot (see §5).
- [ ] Turn on **Auto-track trips** and accept the background disclosure.
- [ ] Note the starting range and current fuel level on paper.

**During a ~20–30 min drive:**
- [ ] Confirm the "tracking your trip" notification appears.
- [ ] Drive normally; after ~5 min, check the range has dropped sensibly.
- [ ] Lock the screen for ~10 min while driving; unlock — range should have kept moving.
- [ ] (Optional) Set fuel low first (Adjust level to ~1/8 tank) to trigger a low-fuel alert.

**After the drive:**
- [ ] Compare app distance vs your car's trip odometer — should be within ~5–10%.
- [ ] Fill up, enter litres pumped; confirm a "Learned ~X L/100km" note appears.
- [ ] Record results in the QA table (§end).

---

## 2. Android permission checklist (Samsung One UI)

- [ ] **Location** → Settings ▸ Apps ▸ Tankpilot ▸ Permissions ▸ Location ▸ **Allow all the time**
      (background tracking will NOT work on "While using the app").
- [ ] **Location accuracy** ON (uses GPS + Wi-Fi/mobile): Settings ▸ Location ▸ Improve accuracy.
- [ ] **Notifications** (Android 13+) → allow when prompted, or Apps ▸ Tankpilot ▸ Notifications ▸ On.
- [ ] **Install unknown apps** → allow for your browser/Files the first install only.
- [ ] Note: the app does **not** need Contacts, Storage, Camera, or Microphone. If anything else
      is requested, that's a red flag.

---

## 3. Background GPS test scenarios

| # | Scenario | Expected |
|---|----------|----------|
| B1 | App open, driving | Range + trip km update live |
| B2 | Screen locked, driving 10 min | Distance keeps accruing (verify after unlock) |
| B3 | App backgrounded (home button), driving | Foreground notification stays; distance accrues |
| B4 | Phone in pocket, long drive (30 min) | No large gaps in distance vs odometer |
| B5 | Brief GPS loss (tunnel/underground) | No crazy jump; resumes cleanly after |
| B6 | Stopped >3 min (traffic/parked) | "Driving" turns off; distance stops climbing |
| B7 | Restart drive after a stop | Tracking resumes automatically |
| B8 | Reboot phone, reopen app | Auto-track resumes (if it was on) |

---

## 4. Notification test scenarios

| # | Scenario | Expected |
|---|----------|----------|
| N1 | Plenty of range (>100 km) | **No** alert (silent) |
| N2 | Fuel low (25–50 km) while driving | One alert: "≈X km left. <Brand> N km ahead: <fuel> €Y/L" |
| N3 | Critical (<25 km) | Urgent alert; nearest reachable station |
| N4 | Same tier, minutes later | **No** repeat within 15-min cooldown |
| N5 | Range drops a tier (low → crit) | New alert fires (escalation) |
| N6 | Tap the notification | Opens Google Maps directions to that station |
| N7 | Alert in background (screen off) | Notification still arrives |
| N8 | Fill up after an alert | Alert/banner clears |

---

## 5. Battery optimization checklist (Samsung is aggressive)

- [ ] Settings ▸ Apps ▸ Tankpilot ▸ **Battery** ▸ **Unrestricted**.
- [ ] Settings ▸ Battery ▸ Background usage limits:
  - [ ] Remove Tankpilot from **"Sleeping apps"** and **"Deep sleeping apps."**
  - [ ] Add Tankpilot to **"Never sleeping apps."**
- [ ] Turn OFF **"Put unused apps to sleep"** (or exempt Tankpilot).
- [ ] Settings ▸ Battery ▸ **Adaptive battery** — acceptable, but if tracking dies, turn it off.
- [ ] Confirm the persistent tracking notification is NOT swiped away (it keeps the service alive).

> If background tracking still dies after all this, that's the Capacitor WebView background
> limit — the trigger to consider a Flutter migration (see decision doc). Log it, don't guess.

---

## 6. Bugs to look for during a real drive

- [ ] **Distance drift** — app total wildly off vs odometer (GPS noise, missed fixes).
- [ ] **Double counting** — range dropping too fast (jitter not filtered).
- [ ] **Frozen range** in background — service paused by OS (battery settings).
- [ ] **Notification spam** — more than one alert per tier (cooldown broken).
- [ ] **No notification** when low — permission off, or service asleep.
- [ ] **Wrong "ahead" station** — suggests one behind you (heading/bearing wrong).
- [ ] **Navigate opens wrong place** — coordinates mismatch.
- [ ] **Price fetch fails while moving** — no station in the alert (network/Overpass).
- [ ] **Battery drain** — noticeable % drop over a short drive.
- [ ] **App killed** — tracking stops with no notification.
- [ ] **Permission revoked by OS** — silent stop after days.

---

## 7. Play Store launch checklist

- [ ] Create Google Play Developer account (**$25 one-time**), verify identity.
- [ ] Create app: name "Tankpilot", default language English, app (not game), free.
- [ ] Set up **Play App Signing** (Google manages the signing key; you use an upload key — §11).
- [ ] Upload a **signed release `.aab`** to **Internal testing** first (not production).
- [ ] Complete **App content**: privacy policy URL, ads (none), content rating questionnaire,
      target audience (adults), data safety (§8), **background location declaration + demo video**.
- [ ] Complete **Store listing** (§9) + graphics (§10).
- [ ] Roll out to Internal testing → test the exact release build → then Production.

> ⚠️ **Background location review:** Play requires a short **screen-recording video** showing the
> feature in use and the in-app disclosure, plus a written justification. Prepare this early —
> it's the most common cause of rejection/delay.

---

## 8. Privacy policy requirements

- [x] Hosted, public URL: https://techmissiondomain-arch.github.io/tankpilot/privacy.html
- [x] Prominent in-app disclosure before background location (built into v1.5.0).
- [ ] **Data safety form must match the policy.** Declare:
  - Location: **collected/accessed**, used for **App functionality**, **not** stored on a server,
    processed on-device; approximate coordinates sent to OpenStreetMap to fetch nearby stations.
  - No account, no ads, no analytics, no data sold.
  - Data is **not** shared for advertising; encrypted in transit (HTTPS) where applicable.
- [ ] Confirm the policy names background use + on-device storage (it does).

---

## 9. Store listing text (draft — edit freely)

**App name:** Tankpilot — Cheapest Fuel BE

**Short description (≤80 chars):**
`Find the cheapest fuel in Belgium and track your range as you drive.`

**Full description:**
```
Tankpilot helps you spend less on fuel in Belgium.

FIND FUEL
• Real fuel stations near you, live from OpenStreetMap.
• Petrol 95, Petrol 98 and Diesel.
• Belgian official maximum prices as your reference.
• Sort by nearest or cheapest, and tap to navigate.
• Report the real pump price to keep things accurate.

SMART FUEL ASSISTANT
• Set up your car once (tank size, fuel type, consumption).
• Tankpilot tracks your driving and estimates your remaining range.
• When fuel runs low, it alerts you to the cheapest station ahead on your route —
  and stays quiet when you have plenty of range.
• It learns your real consumption over time for a more accurate range.

PRIVATE BY DESIGN
• Your location and car details stay on your device.
• No accounts, no ads, no tracking.

Prices are Belgium's official maximum prices; stations may sell below.
```

**What's new (v1.5.0):**
`Smart fuel assistant: live range, auto trip tracking, and low-fuel alerts for the cheapest station ahead.`

---

## 10. What to prepare before publishing

- [ ] **App icon** 512×512 PNG (currently the default Capacitor icon — replace before launch).
- [ ] **Feature graphic** 1024×500 PNG.
- [ ] **Phone screenshots** — at least 2 (recommend 4–6): station list, station popup, fuel gauge,
      a low-fuel alert.
- [ ] **Short + full description** (§9).
- [ ] **Privacy policy URL** (have it).
- [ ] **Data safety answers** (§8).
- [ ] **Background location: justification text + demo video** (§7).
- [ ] **Signing keystore** created and backed up (§11).
- [ ] **Content rating** answers (no violence/gambling → likely PEGI 3 / Everyone).

---

## 11. APK signing & release steps

Play needs a **signed `.aab`** (App Bundle), not the debug APK we build for testing.

**1) Create an upload keystore (once, on your PC — keep it safe, back it up):**
```
keytool -genkey -v -keystore tankpilot-upload.keystore \
  -alias tankpilot -keyalg RSA -keysize 2048 -validity 10000
```
- Remember the **keystore password** and **key password**. Losing this key means you can't update
  the app (unless on Play App Signing with a reset).

**2) Add secrets to GitHub** (repo ▸ Settings ▸ Secrets ▸ Actions):
- `ANDROID_KEYSTORE_BASE64` = `base64 -w0 tankpilot-upload.keystore`
- `ANDROID_KEYSTORE_PASSWORD`, `ANDROID_KEY_ALIAS`, `ANDROID_KEY_PASSWORD`

**3) Release build (a new CI job, added when you're ready):**
- Decode the keystore, configure Gradle signing, then:
  `cd android && ./gradlew bundleRelease`
- Output: `android/app/build/outputs/bundle/release/app-release.aab` → upload to Play.

> When you decide to launch, I'll add this signed-release job to the workflow (config only,
> no app features).

---

## 12. Go / No-Go checklist (final gate)

**GO only if ALL are true:**
- [ ] Road test passed: distance within ~10% of odometer.
- [ ] Background tracking survived a locked-screen drive (§3 B2/B4).
- [ ] Low-fuel alert fired once, correct station, tap-to-navigate worked (§4).
- [ ] No notification spam; escalation worked (§4 N4/N5).
- [ ] No crashes; battery drain acceptable.
- [ ] Permissions behave (all-the-time location, notifications).
- [ ] Signed `.aab` builds and installs from Internal testing.
- [ ] Store listing, privacy, data safety, background-location video all ready.

**NO-GO triggers:**
- Background tracking unreliable after §5 tuning → decide on Flutter migration first.
- Distance error > ~15% → fix filtering before launch.
- Any crash on a real drive.

---

## Manual QA table

| # | Test case | Steps | Expected result | Pass/Fail | Notes |
|---|-----------|-------|-----------------|-----------|-------|
| 1 | Find stations | Open app ▸ Find cheap stations ▸ Allow location | Real nearby stations listed, sorted | | |
| 2 | Fuel toggle | Tap Petrol 95 / 98 / Diesel | List price/filter updates | | |
| 3 | Sort | Toggle Nearest / Cheapest | Order changes correctly | | |
| 4 | Station popup | Tap a station | Shows prices, distance, Navigate | | |
| 5 | Navigate | Tap Navigate | Google Maps opens directions | | |
| 6 | Report price | Report a price on a station | Saved, "Reported" badge shows | | |
| 7 | Car setup | Set tank/fuel/consumption | Gauge + range appear | | |
| 8 | Fill up | Tap I filled up (+ litres) | Tank full; learns after ≥50 km | | |
| 9 | Adjust level | Set current litres | Range recalculates | | |
| 10 | Start trip (fg) | Start trip, drive | Range/trip km update live | | |
| 11 | Auto-track consent | Turn on Auto-track | Disclosure shown first | | |
| 12 | Background drive | Lock screen, drive 10 min | Distance accrues (check on unlock) | | |
| 13 | Driving stop | Park > 3 min | "Driving" turns off | | |
| 14 | Low-fuel alert | Drive with low fuel | Correct "station ahead" alert | | |
| 15 | Cooldown | Keep driving, same tier | No repeat within 15 min | | |
| 16 | Escalation | Range drops a tier | New alert fires | | |
| 17 | Notify tap | Tap the alert | Navigates to station | | |
| 18 | Calibration | Fill up with litres after long drive | "Learned ~X L/100km" shown | | |
| 19 | Persistence | Kill app, reopen | Range/car retained | | |
| 20 | Battery | Check % after 30-min drive | No excessive drain | | |
