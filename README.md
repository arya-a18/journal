# Midnight Pages — your book-style journal

A journal app that looks and flips like a real book, works with no internet, and keeps your phone and laptop in step through one email account.

## What's in this folder

| File | What it does |
|---|---|
| `index.html` | The whole app (cover, pages, dictionary, stickers, sync) |
| `sw.js` | Keeps the app working offline |
| `manifest.json` | Lets you install it on your phone and laptop like a normal app |
| `icons/` | The app icon |

## How the app works

| You want to… | Do this |
|---|---|
| Open the book | Swipe the cover right-to-left (or press → on a laptop) to open at your first page. Tap **New page** to go straight to a fresh page |
| Turn pages | Swipe right-to-left (next) or left-to-right (back). On a laptop, use the arrow keys or the buttons |
| Start a fresh page | Only the **New page** button makes a new page. Each page holds 18 lines; when it's full, tap New page to continue |
| Laptop view | On a wide screen the journal opens as a two-page spread, like a real book |
| Set the page's date | Tap the date at the top of the page. It stays on that page, and pages sort by date |
| Change page / line / ink colours, handwriting, size | **Style** at the top |
| Add stickers | **Stickers** at the top. Drag to move; tap a sticker for size, turn, remove |
| Look up a word | **Dictionary** at the top. Select a word on your page first to look it up straight away |
| Jump to a date | **Find a date**. Year only → first page of that year. Year + month → first page of that month. Year + month + day → that page |
| Change the cover title and names | **⋯ menu → Cover details** |
| Remove a page | **⋯ menu → Tear out this page** |
| Keep a safety copy | **⋯ menu → Download a backup** |

Offline note: writing, stickers, styles and date search all work offline. The dictionary needs internet for new words; words you looked up before still open offline.

---

## Setup — step by step (about 20 minutes, all free)

### Step 1 — Create the sync account (Firebase)

1. Go to **console.firebase.google.com** and sign in with Google.
2. **Create a project** → name it `midnight-pages` → you can turn Google Analytics off → **Create**.
3. Left menu → **Build → Authentication → Get started** → **Sign-in method** tab → **Email/Password** → turn on the first switch → **Save**.
4. Left menu → **Build → Firestore Database → Create database** → pick a location near you (e.g. `asia-south1` Mumbai) → **Start in production mode** → **Create**.
5. In Firestore, open the **Rules** tab, replace everything with this, then **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

This means only you, signed in, can read or write your journal.

6. Click the **gear icon → Project settings** → scroll to **Your apps** → click the **`</>`** (Web) icon → name it `journal` → **Register app**.
7. Firebase shows a block like `const firebaseConfig = { apiKey: "...", ... }`. Copy just the `{ ... }` part.

### Step 2 — Paste your keys into the app

Open `index.html` in any text editor (VS Code, Notepad). Near the top of the `<script>` section, find:

```js
const FIREBASE_CONFIG = null;
```

Replace `null` with what you copied, so it looks like:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "midnight-pages-xxxx.firebaseapp.com",
  projectId: "midnight-pages-xxxx",
  storageBucket: "midnight-pages-xxxx.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abc123"
};
```

Save the file. (These keys are safe to have in the app. The rules from Step 1 are what protect your pages.)

### Step 3 — Put the app online (GitHub Pages)

The app needs a web address once, so your phone can install it. After that it runs offline.

1. Make a free account at **github.com**.
2. **New repository** → name it `journal` → **Public** → **Create repository**.
3. Click **uploading an existing file** → drag in `index.html`, `sw.js`, `manifest.json` and the `icons` folder → **Commit changes**.
4. Repository **Settings → Pages** → Source: **Deploy from a branch** → Branch: **main**, folder **/ (root)** → **Save**.
5. Wait 1–2 minutes. Your app is at **`https://YOUR-USERNAME.github.io/journal/`**

Then tell Firebase that address is allowed to sign in:
Firebase → **Authentication → Settings → Authorized domains → Add domain** → `YOUR-USERNAME.github.io`

### Step 4 — Install on your phone

| Phone | Steps |
|---|---|
| Android (Chrome) | Open your link → **⋮ menu → Install app** (or **Add to Home screen**) |
| iPhone (Safari) | Open your link → **Share** button → **Add to Home Screen** |

### Step 5 — Install on your laptop

Open the same link in **Chrome or Edge** → click the **install icon** at the right end of the address bar (a monitor with an arrow) → **Install**. It opens in its own window like a normal app.

### Step 6 — Sign in on both

On each device: **⋯ menu → Account and sync → Create account** the first time (email + a password of 6+ characters), then **Sign in** with the same email on the other device. The small dot at the top glows gold when you're synced.

---

## Good to know

| Topic | Detail |
|---|---|
| Offline writing | Everything saves on the device instantly. When you're back online, changes sync on their own |
| Same page edited on both devices while offline | The most recently edited version wins |
| Updating the app later | Upload the changed file to GitHub again. In `sw.js`, change `mp-shell-v1` to `mp-shell-v2` so devices pick up the new version |
| Cost | Firebase free plan and GitHub Pages are both free for one person's journal |
| Storage | Text pages are tiny; years of daily entries fit easily |
| Trying it on your laptop before setup | Run `python -m http.server` inside this folder, then open `http://localhost:8000` |
