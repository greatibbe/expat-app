# Bidheyseenge Dafthar — Expatriate Registry (Google/Firebase edition)

This version runs on **Firebase** (Google's backend platform), hosted for free on **GitHub Pages**.
Full feature set: login + admin approval, two-section entry form (Expat + Person in charge),
work permit & passport expiry tracking with alerts, passport/work-permit photo uploads,
passport OCR scan, call & WhatsApp buttons, duplicate-entry checking, admin bulk import
and delete-all, CSV export — installable on both iPhone and Android.

## Files in this folder
- `index.html` — the whole app
- `firebase-config.js` — **you fill this in** with your project's keys (Step 2)
- `firestore.rules` — database security rules (Step 4)
- `storage.rules` — photo storage security rules (Step 5)
- `manifest.json`, `sw.js`, icons — installable app support

---

## Step 1: Create your Firebase project

1. Go to https://console.firebase.google.com and sign in with a Google account.
2. Click **Add project**. Name it (e.g. `dafthar-registry`), you can turn off Google Analytics. Click **Create project**.

## Step 2: Register a web app and get your config keys

1. On the project overview page, click the **</>** (web) icon.
2. Give it a nickname (e.g. `dafthar-web`). Click **Register app**.
3. Copy the `firebaseConfig = { ... }` values shown.
4. Open `firebase-config.js` in this folder, paste your real values in place of the `PASTE_...` placeholders. Save.

**Important:** paste only the plain `const firebaseConfig = {...};` object — not the `<script type="module">` wrapper or `import`/`initializeApp` lines Firebase's own snippet includes. Those extra lines will break the file.

## Step 3: Turn on Email/Password login

1. **Build → Authentication** → **Get started**.
2. Click **Email/Password**, toggle **Enabled**, **Save**.

## Step 4: Create the database and paste in the security rules

1. **Build → Firestore Database** → **Create database**. Pick a location, start in **Production mode**.
2. Click the **Rules** tab. Delete everything, paste in the full contents of `firestore.rules`, click **Publish**.

## Step 5: Turn on Storage and paste in its rules

1. **Build → Storage** → **Get started**. Pick a location.
2. **Note on cost:** Firebase requires the pay-as-you-go **Blaze** plan to use Storage (a billing card on file). Firestore and Auth stay free either way, and normal use of this app should stay within Storage's free monthly allowance — but the card is required to turn the feature on at all.
3. Click the **Rules** tab. Delete everything, paste in the full contents of `storage.rules`, click **Publish**.

## Step 6: Host the files on GitHub Pages

1. Create a free GitHub account if needed, then **New repository** → name it → **Public** → **Create**.
2. **Add file → Upload files** → drag in *every file in this folder* (`index.html`, `firebase-config.js`, `firestore.rules`, `storage.rules`, `manifest.json`, `sw.js`, and the 3 icon `.png` files). Commit.
3. **Settings → Pages** → Source: **Deploy from a branch**, Branch `main`, folder `/ (root)` → **Save**. Wait 1–2 minutes for your live link.

## Step 7: Authorize your hosting domain

1. **Authentication → Settings → Authorized domains** → **Add domain** → paste your GitHub Pages domain (e.g. `yourusername.github.io`) → Save.

Skipping this causes a "domain not authorized" login error.

## Step 8: Make yourself the first admin

1. Open your live link, **Request access**, enter your name, a service number (e.g. `4655`), and an email address (used only for password recovery, never for login). The app shows your login username as you type — built from your initials + service number (e.g. "Mohamed Ali" + `4655` → `ma_4655`). Set a password and submit.
2. **Firestore Database → Data → users** collection → open your account's document.
3. Change `status` to `approved` and `role` to `admin`. Save.
4. Reload the app — you'll see the **Administrator** view (Approvals / All users / Resets / Registry tabs).

From then on, approve everyone else from inside the app — no more Firebase Console needed for day-to-day use.

**Note on login:** people sign in with a **username** (like `ma_4655`) built from their own initials — not an email address. Behind the scenes, Firebase still requires an email format internally, so the app auto-generates a hidden one from the username; nobody ever sees or needs to know it. Since the username is generated the same way every time from name + service number, Firebase itself blocks anyone from accidentally registering a duplicate.

**Password resets:** since login has no real email attached, self-service "forgot password" isn't possible on its own. Instead: a person taps **Forgot password?** on the login screen and enters their username → this creates a request → you approve it from the **Resets** tab → the app sends a real password-reset email to the address they signed up with. Nothing is reset until you approve it.

## Step 9: Install on your phone

**iPhone (Safari):** Share icon → **Add to Home Screen** → **Add**.
**Android (Chrome):** **⋮** menu → **Add to Home screen** (or tap the automatic Install banner if it appears) → **Add**.

---

## Using the app

**Everyone:** sign in with your username, or request access and wait for approval (auto-updates, no refresh needed). Forgot your password? Tap **Forgot password?** on the login screen.

By default you'll see your stats, expiry alerts, recent entries, and (if you're an admin) the search bar and full ledger — the two-section entry form stays out of the way until you tap **+ New entry** or open an existing entry to edit it. A **Back to registry** link at the top of the form returns you to that view without saving.

**Approved users:**
- **Expatriate's Information**: Full name, Country, Passport number + expiry, Work permit number + expiry, Current address, Phone number (with Call and WhatsApp buttons), Passport photo, Recent photo, Work permit photo.
- **Information of the person in charge**: Full name, ID number, Permanent address, Current address, Contact number (also with Call/WhatsApp).
- **Scan passport** — reads the machine-readable strip, fills Name, Country, Passport number, and passport expiry date.
- **Scan work permit / other document** — best-effort OCR for non-standardized documents; looks for a permit code and an expiry date near relevant keywords. Less reliable than the passport scanner — always double-check.
- **Duplicate check** — warns you if a passport number you're entering already exists in the registry.
- **Tap any entry** to open a read-only details popup (all fields + photos), with an **Edit this entry** button inside it if you need to change something.
- **Search** — matches across every field: names, numbers, dates, addresses, phone numbers.
- **Expiry alerts** — dashboard shows a live count of documents expiring within 30 days or already expired, with a full tappable list.

**Admin only:**
- **Approvals** / **All users** tabs — approve, reject, revoke.
- **Resets** tab — approve pending password-reset requests; approving sends a real reset email to the address that person signed up with.
- **Registry** tab — same tools as everyone, plus **Bulk import**, **Delete all**, and a **Show expiring/expired only** filter on the ledger.

## Cost

Firestore and Authentication are free for this scale of use. Storage requires the Blaze (pay-as-you-go) plan to enable, but a small registry like this should stay within the free monthly allowance.

## Data & privacy

- Firestore security rules enforce that only approved users can read/write the registry, and nobody can approve themselves — enforced at the database level, not just by what the app's screens show.
- Anyone with the exact (long, random) photo URL can view that photo; only approved users can upload or replace one.
- Keep an occasional CSV export as backup if this data matters long-term.
