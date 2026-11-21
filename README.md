<img src="docs/logo-512.png" width="96" align="right">

# PantryPulse — Smart Inventory & Waste Reduction Hub

An Android app that tracks what's in your fridge, freezer, pantry, bathroom and medicine cabinet, nudges you before things expire, suggests recipes that use them up, and helps you donate what you won't eat.

<p>
<img src="docs/screenshots/02_home.png" width="240"> <img src="docs/screenshots/07_recipes.png" width="240"> <img src="docs/screenshots/06_shopping.png" width="240">
</p>

## Download

| File | Use it for |
|------|-----------|
| [**PantryPulse.apk**](../../releases/latest/download/PantryPulse.apk) | Install directly on an Android phone (Android 8.0+). Allow "Install unknown apps" when asked. |
| [**PantryPulse.aab**](../../releases/latest/download/PantryPulse.aab) | Upload to Google Play Console. |

Every push to `main` runs the tests, rebuilds both files and refreshes the latest release, so these links always serve the newest build. Copies of the current build are also in [`release/`](release/).

## Features

| # | Feature | How it works |
|---|---------|--------------|
| 1 | Add products with expiry dates | Manual entry with date picker and quick chips (+3 days, +1 week, typical shelf life for the category). |
| 2 | Expiry notifications | A daily check at the time you pick (7 am / 9 am / 12 pm / 6 pm, WorkManager) notifies about items expiring within your chosen window (1–14 days). Profile has "Send a test notification", "Check now", and a shortcut to the phone's notification settings if they're blocked. |
| 3 | Available products | Home screen: "N things want using up before Friday", weekly pulse chart, Food / Cosmetics / Medicine tabs, ticket cards with days left. |
| 4 | Profile login | Email + password accounts (PBKDF2-hashed, never stored in plain text), quick profile picker on the sign-in screen. |
| 5 | Recipe suggestions | Built-in recipes ranked by how many expiring items they rescue, plus online ideas by ingredient from TheMealDB. |
| 6 | Expiry date from photo | Live camera OCR (ML Kit, on-device) reads "EXP 12/08/26", "BEST BEFORE 12 AUG 2026", "USE BY 2026-08-12", "08/27"… and prefers the expiry date over the manufacture date. Gallery photos work too. |
| 7 | Barcode & QR scanning | ML Kit scanner for barcodes and QR / Data Matrix codes (GS1 links and element strings are unpacked to the product number); product name, brand, category and photo filled from Open Food Facts (or your own history). |
| 8 | Storage locations | Fridge, Freezer, Pantry, Counter, Cupboard, Bathroom, Medicine cabinet — plus your own. Filter chips with counts. |
| 9 | Multi-user households | Family, Roommates or Office breakroom households with invite codes, member profiles, household switching. Everyone shares one pantry and one shopping list. |
| 10 | Receipt scanning | Photo of a paper receipt, screenshot of a digital one, or pasted text → items with quantities and suggested expiry dates to review and add. |
| 11 | Food rescue map | Finds food banks, community fridges, give-boxes and sharing points near you (OpenStreetMap / Overpass), with map, directions, phone and website. "Offer to neighbours" shares a list; "I've donated these" logs it. |
| 12 | One-tap shopping list | When an item hits zero or is marked finished, a snackbar offers **Add to list** (or turn on auto-add). Bought items move back to the pantry in one tap. |
| 13 | **Giveaways (peer-to-peer)** | "Give away nearby" on any food item: approximate location, public area name, private exact address and instructions, pickup deadline, notes (unopened, sealed…), and **Allow direct claim (first-come, first-served)** or **Require my approval**. A Nearby feed filters by 2 / 5 / 10 / 25 km and sorts by distance or urgency. Only one person can ever claim an item (atomic database update); approving one request closes the rest. Status: available → reserved → picked up (or expired); donors can release no-shows. Addresses stay hidden until a claim is confirmed. |
| 14 | **Shop accounts** | Shopkeepers sign up as a shop (name, address, opening hours) and get a dashboard: live / requests / booked / rescued counts, post several surplus items in one go, approve requests and confirm pickups. |
| 15 | **Nearby giveaway alerts** | Profile → Community → *Nearby giveaway alerts*. Choose 2 / 5 / 10 / 25 km. When someone posts a giveaway (or a shop posts a batch) within your radius you get a notification, and tapping it opens Giveaways. Only your approximate location (rounded to ~1 km) is sent to the server; alerts show a rounded distance, never the exact pickup point. Off by default. |
| 16 | **Quick-actions button** | One floating **+** button on every main screen opens a tidy menu: free food nearby (with a count badge on the button, nearest first), scan a barcode or QR code, scan a receipt, or type an item in. Tap outside or press Back to close. |

## Backend

Giveaways and shop accounts use a small online service in [`backend/`](backend/) — a Node.js API on **Vercel** with a **Turso** database. See [backend/README.md](backend/README.md) for how to access the data, the admin page, the API and the one-time secret setup.

## Data & privacy

Your pantry, shopping list and households are stored in a local Room database on the phone; only giveaways and your community account go to the backend. Photos for barcode, date and receipt scanning are processed on-device and never uploaded. The app calls only free, key-less public services: Open Food Facts (barcode lookups), TheMealDB (online recipes), OpenStreetMap Overpass and Nominatim (food rescue search) and OpenStreetMap tiles (map).

**Household sharing is per device.** Households, invite codes and member profiles let several people share one phone or kitchen/breakroom tablet; giveaways work across all phones through the backend.

## Build

Requirements: JDK 17+, Android SDK 35.

```bash
./gradlew :app:testDebugUnitTest   # 49 unit + Robolectric UI tests, writes screenshots to app/build/screenshots
./gradlew :app:assembleRelease     # app/build/outputs/apk/release/app-release.apk
./gradlew :app:bundleRelease       # app/build/outputs/bundle/release/app-release.aab (Google Play)
```

Release signing reads `PANTRYPULSE_KEYSTORE`, `PANTRYPULSE_KEYSTORE_PASSWORD`, `PANTRYPULSE_KEY_ALIAS` and `PANTRYPULSE_KEY_PASSWORD` (environment variables or `pantrypulse.*` Gradle properties). Without them the APK is signed with the standard debug key — fine for side-loading, but create your own key before publishing to Google Play.

## Tech

Kotlin · Jetpack Compose (Material 3) · Room · DataStore · WorkManager · CameraX · ML Kit (barcode + text recognition, bundled models) · Coil · osmdroid. Min SDK 26, target SDK 35.

Design: warm paper `#F2EFE9`, ink `#17140F`, pulse orange `#FF5A2C`; type in Bricolage Grotesque and DM Mono (both SIL Open Font License 1.1, see `app/FONTS_LICENSE.txt`).
