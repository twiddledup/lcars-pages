# TroyCars · LCARS Prodigy Site
## Setup Guide — GitHub + Netlify + Custom CMS

---

## The Stack

```
Your iPad/Mac
    ↓  (open in browser)
cms/index.html         ← Your content editor (password-protected)
    ↓  (Publish button)
GitHub repo             ← Source of truth + version control (free)
    ↓  (auto-deploy ~30s)
Netlify                 ← Hosting, CDN, SSL (free)
-    ↓
lcars.t3d.ing           ← Your live site
```

---

## Step 1 — GitHub Setup (one time)

1. Go to **github.com** and create a free account (or log in)
2. Create a new repository called `troycars-site` (or any name)
   - Set to **Public** (for free Netlify deploys) or Private (Netlify paid)
   - Don't initialize with README — we'll push our own
3. Upload all the files in this project to the repo
   - Easiest on Mac: drag the folder into the GitHub web UI
   - On iPad: use **Working Copy** app (free) — it's a Git client for iOS

---

## Step 2 — Netlify Setup (one time)

1. Go to **netlify.com** → Sign up free with GitHub
2. Click **"Add new site" → "Import an existing project"**
3. Choose your GitHub repo
4. Build settings:
   - Build command: *(leave blank)*
   - Publish directory: `_site`
5. Click **Deploy site**
6. Go to **Site settings → Custo m domains** → Add `lcars.t3d.ing`
7. Update your DNS at FastMail:
   - Add CNAME: `lcars` → `[your-netlify-site].netlify.app`
   - Or follow Netlify's DNS instructions (they handle SSL automatically)

---

## Step 3 — CMS Setup (one time)

1. Open `cms/index.html` in your browser (iPad Safari or desktop)
2. Default passcode: `lcars2401` — **change this!**
   - Open `cms/index.html`, find `const PASSCODE = 'lcars2401'`
   - Replace with your own passcode
3. Go to **Settings tab → GitHub Connection**
4. Enter your GitHub usernameand repo name
5. Create a GitHub Personal Access Token:
   - GitHub → Settings → Developer Settings → Personal Access Tokens → Tokens (classic)
   - Give it `repo` scope (full control of private repositories)
   - Copy the token → paste it in CMS Settings
6. Click **Save Configuration**

Now when you click **Publish to Site** in the CMS, it commits to GitHub, which triggers Netlify to deploy in ~30 seconds.

---

## Daily Workflow

### Writing a Blog Post (iPad)
1. Bookmark `cms/index.html` on your iPad's home screen
2. Open it → enter passcode
3. Click **+ New Post** in the sidebar
4. Write in the **Blog Posts** tab — Markdown supported
5. Hit **Save Draft** while writing
6. When ready → change Status to "Published" → **Publish to Site**
7. Site is live in ~30 seconds

### Updating Page Content
1. Go to **Pages tab**
2. Click the page you want to edit (Landing, Blog, Store)
3. Update text → **Publish to Site**

### Changing Glass Effects
1. Go to **Glass Panels tab**
2. Click a style preset, or drag the sliders
3. Settings are saved locally and can be committed

---

## File Structure

```
troycars/
├── _site/                    ← DEPLOYED FILES (what Netlify serves)
│   ├── index.html            ← Landing / About page
│   ├── blog/index.html       ← Blog listing page
│   ├── store/index.html      ← Store page
│   ├── assets/
│   │   ├── tokens.css        ← All CSS variables (copy into any page)
│   │   ├── lcars.css         ← LCARS chrome styles
│   │   ├── glass.css         ← Glass system + SVG filters
│   │   └── animations.css    ← Animation library
│   ├── backgrounds/
│   │   ├── space-bg.js       ← Space/starfield/nebula canvas engine
│   │   └── README.md         ← How to customize the background
│   └── screens/
│       ├── waveform.js       ← Waveform animation
│       ├── spectrum.js       ← Bar spectrum animation
│       └── README.md         ← How to customize animations
├── cms/
│   ├── index.html            ← Your CMS editor (NOT deployed)
│   └── publish.js            ← GitHub API helper
└── docs/
    ├── design-system.html    ← Visual style guide (open in browser)
    └── README.md             ← This file
```

**Important:** The `cms/` folder is NOT deployed — it lives only on your device or in the repo. Your passcode protects it, but for extra safety you can also `.gitignore` the cms folder and keep it local only.

---

## Customizing the Space Background
*(Full instructions in `_site/backgrounds/README.md`)*

- Stars: adjust count, size, speed in `space-bg.js` → `STAR_COUNT`, `STAR_SPEED`
- Nebula: change colors in the `NEBULA_COLORS` array
- Planets: add/remove in the `PLANETS` array with `radius`, `color`, `speed`
- Parallax: adjust `PARALLAX_DEPTH` layers

---

## Customizing Glass Effects
*(Full instructions in `_site/assets/glass.css`)*

CSS variables that control all glass panels:
```css
:root {
  --glass-blur: 18px;           /* backdrop blur amount */
  --glass-opacity: 0.12;        /* fill transparency */
  --glass-border: rgba(255,255,255,0.18);  /* edge highlight */
}
```

Users can also control these with the in-site glass slider.

---

## Customizing LCARS Chrome

The LCARS elbow/shoulder/chin shapes use these techniques:
- **Elbows**: `border-radius` on caps + `radial-gradient` pseudo-elements for concave corners
- **Bars**: thin `height` divs with `border-radius` on the left end only
- **Color coding**: nth-child selectors on `.sidebar-item`

See `_site/assets/lcars.css` for all shape classes.

---

## Adding Sound Effects

Beep sounds use the Web Audio API (no files needed):
```js
// In main.js
function playBeep(freq = 880, duration = 0.08) {
  const ctx = new AudioContext();
  const osc = ctx.createOscillator();
  const gain = ctx.createGain();
  osc.connect(gain); gain.connect(ctx.destination);
  osc.frequency.value = freq;
  osc.type = 'sine';
  gain.gain.setValueAtTime(0.3, ctx.currentTime);
  gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + duration);
  osc.start(); osc.stop(ctx.currentTime + duration);
}
```

Ambient engine hum uses a looping `AudioBufferSourceNode` with filtered noise.
See `_site/assets/main.js` for the full implementation.

---

## Future Phases

- [ ] Phase 2 — Space background canvas engine (starfield, nebula, planets)
- [ ] Phase 3 — Liquid glass refraction upgrade (SVG feTurbulence)
- [ ] Phase 4 — LCARS chrome (heavy elbows, shoulders, chins)
- [ ] Phase 5 — Blog, Store, Landing pages fully wired to CMS
- [ ] Phase 6 — User-controlled glass settings (in-site slider saved to localStorage)
- [ ] Phase 7 — Trek sounds (button beeps + ambient hum)

---

*TroyCars · lcars.t3d.ing · Built with ✦ and Antonio Bold*
