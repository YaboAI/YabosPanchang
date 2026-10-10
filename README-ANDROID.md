# Panchāng — Android app

This folder wraps your website (`index.html`) as an Android app using Capacitor 8.
You do **not** need Android Studio: GitHub builds the app for you.

## 1. Put it in your git repo
Copy everything in this folder into the same repo as your website (keep `index.html` in the repo root, it is the same file).
Commit and push to the `main` branch.

## 2. Choose your app ID (before the first Play upload)
Open `capacitor.config.json` and change `appId` (for example `com.yourname.panchang`).
**It can never be changed after you publish.** The app name shown on the phone is `appName`.

## 3. One-time: make a signing key and add 4 GitHub secrets
On your computer (Java installed):
```
keytool -genkeypair -v -keystore panchang-upload.jks -alias upload -keyalg RSA -keysize 2048 -validity 10000
base64 -w0 panchang-upload.jks > keystore.b64        # on macOS: base64 -i panchang-upload.jks -o keystore.b64
```
Keep `panchang-upload.jks` and its passwords safe (back them up; never commit them).
In GitHub: repo → Settings → Secrets and variables → Actions → New repository secret:

| Secret name | Value |
|---|---|
| `KEYSTORE_BASE64` | contents of `keystore.b64` |
| `KEY_ALIAS` | `upload` |
| `KEYSTORE_PASSWORD` | the keystore password you typed |
| `KEY_PASSWORD` | the key password you typed |

## 4. Build
Push to `main` (or Actions → "Build Android app" → Run workflow). After about 5–10 minutes the run page shows two downloads:
- **panchang-debug-apk**: install on your phone to test (allow "install unknown apps").
- **panchang-release-aab**: the signed file you upload to Google Play.

## 5. Publish on Google Play
1. Create a Play Console developer account (one-time fee, identity check).
2. Create the app → Production/Testing → upload the `.aab`. Let Google manage app signing (recommended).
3. Fill in: store listing (`store-listing.md` has text to paste), icon 512×512 (use `assets/icon-only.png` resized), a 1024×500 feature graphic, 2–8 phone screenshots, privacy policy URL (publish `PRIVACY.md` on GitHub Pages or any page), Data safety form, content rating, target audience.
4. New personal accounts must first run a **closed test with at least 12 testers for 14 days** before production access (check the Play Console for the current rule).

## 6. Updating the app
Edit `index.html`, commit, push. Every build gets a higher version number automatically, so you can upload it as an update.

## Build on your own computer instead (optional)
Needs Node 22+, JDK 21 and Android Studio.
```
npm install
npm run build
npx cap add android
npm run icons
npx cap sync android
npx cap open android      # then Build → Generate Signed Bundle
```

## Good to know
- Target SDK 36 comes from Capacitor 8, which meets Google Play's current target-level rule.
- The hardware Back button closes the open festival card, then returns to Home, then exits.
- The app loads two fonts from Google Fonts, so the first start needs internet; offline it falls back to system fonts. For a fully offline app (and a simpler privacy answer), download the Fraunces and Work Sans files, put them in the project and point the CSS at them.
- Google can reject apps that are only a website in a frame. Adding native features such as daily festival and Ekadashi notification reminders (Capacitor Local Notifications) makes the app clearly more than a web page. Ask me and I will add them.
