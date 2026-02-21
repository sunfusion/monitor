# SunFusion Kiosk — Squarespace Installation Guide

---

## FILE STRUCTURE

```
sunfusion-app/
│
├── customer.html          ← Customer portal (code lookup + edit IP)
├── fleet.html             ← Admin fleet manager (password protected)
├── monitor.html           ← Live energy dashboard (the main monitor)
├── kiosk.html             ← Locked kiosk wrapper (standalone use)
├── squarespace-embed.html ← Code to paste into Squarespace
│
├── sw.js                  ← Service worker (PWA offline support)
├── manifest.json          ← PWA manifest
├── INSTALL.md             ← PWA install instructions
├── MQTT_Setup_Guide.pdf   ← MQTT setup guide for technicians
│
└── icons/                 ← PWA icons (10 sizes)
    ├── icon.svg
    ├── favicon-32x32.png
    ├── icon-72x72.png
    ├── icon-96x96.png
    ├── icon-128x128.png
    ├── icon-144x144.png
    ├── icon-152x152.png
    ├── icon-192x192.png
    ├── icon-384x384.png
    ├── icon-512x512.png
    └── apple-touch-icon.png
```

---

## WHAT GOES WHERE

| File | Purpose | Goes on Squarespace? |
|------|---------|---------------------|
| `squarespace-embed.html` | Code you paste INTO Squarespace | YES — paste contents |
| `monitor.html` | The actual live dashboard | NO — stays on your server |
| `kiosk.html` | Standalone kiosk (for local use) | NO — local use only |
| `customer.html` | Customer portal | NO — local use only |
| `fleet.html` | Admin fleet manager | NO — local use only |

---

## SQUARESPACE INSTALLATION — STEP BY STEP

### Step 1: Open squarespace-embed.html in a text editor

Open `squarespace-embed.html` in **Notepad** (Windows) or **TextEdit** (Mac).

You will see a CONFIG section near line 77:

```javascript
const KIOSK_PASS   = 'sunfusion';
const SYSTEM_NAME  = 'SunFusion HQ';
const SYSTEM_IP    = '192.168.0.165';
const SYSTEM_PORT  = 9001;
const SYSTEM_ZIP   = '92081';
const MONITOR_URL  = 'https://YOUR-DOMAIN.com/sunfusion-app/monitor.html';
```

### Step 2: Edit the CONFIG values

Change each value to match YOUR system:

```
KIOSK_PASS   → Your unlock password (currently disabled, but set it anyway)
SYSTEM_NAME  → Customer name or site name (shows in monitor header)
SYSTEM_IP    → The Solar Assistant local IP (from SA → Settings → Network)
SYSTEM_PORT  → Leave as 9001 unless you changed it
SYSTEM_ZIP   → Customer's zip code (for weather)
MONITOR_URL  → Where you will host monitor.html (see Step 3)
```

### Step 3: Host monitor.html somewhere

Squarespace **cannot host HTML files**. You need to put `monitor.html` on a web server. Options:

**Option A — Your own domain (recommended)**
If you have hosting at sunfusion.us:
1. Upload `monitor.html` to your server
2. Set MONITOR_URL to: `https://sunfusion.us/monitor.html`

**Option B — GitHub Pages (free)**
1. Create a GitHub repo
2. Upload `monitor.html` to it
3. Enable GitHub Pages in repo settings
4. Set MONITOR_URL to: `https://yourusername.github.io/repo-name/monitor.html`

**Option C — Any web host**
Upload `monitor.html` to any web hosting service and use that URL.

### Step 4: Copy the code for Squarespace

In `squarespace-embed.html`, copy **everything** from line 62 to the end of the file.

That's the section starting with:
```html
<style>
  /* Hide Squarespace page content — kiosk takes over */
```

All the way down to the closing:
```html
</script>
```

### Step 5: Log into Squarespace

1. Go to **squarespace.com** and log in
2. Open your site editor

### Step 6: Create a new page (or use existing)

1. In the left sidebar, click **Pages**
2. Click the **+** to add a new page
3. Choose **Blank Page**
4. Name it something like "Live Monitor" or "Energy Dashboard"

### Step 7: Open Page Settings

1. Hover over the page name in the sidebar
2. Click the **gear icon** (⚙) to open Page Settings
3. Click the **Advanced** tab

### Step 8: Paste the code

1. In the **Page Header Code Injection** box, paste everything you copied in Step 4
2. Click **Save**

### Step 9: Done!

Navigate to that page. You should see:
- The SunFusion branded lock screen (currently disabled — goes straight to monitor)
- The live energy dashboard in a full-page iframe
- A hidden toolbar at the bottom edge (hover to see RELOAD / FULLSCREEN / LOCK)

---

## IMPORTANT NOTES

### Network Limitation
The monitor connects to Solar Assistant via WebSocket on port 9001.
This **ONLY works** when the person viewing the page is on the **same local network** as the Solar Assistant device.

Internet visitors CANNOT reach 192.168.x.x addresses.

### For Remote Viewing
To let people view the monitor from outside the local network:
1. Set up **port forwarding** on the customer's router (forward port 9001 to the Solar Assistant IP)
2. Use the router's **public IP** or a **dynamic DNS** address as the SYSTEM_IP
3. Or use a **VPN tunnel** to the customer's network

### Squarespace Plan Requirement
Code Injection requires a **Squarespace Business plan** or higher.
The Basic/Personal plans do not support code injection.

---

## QUICK CHECKLIST

```
[ ] Edited CONFIG values in squarespace-embed.html
[ ] Hosted monitor.html on a web server
[ ] Set MONITOR_URL to the correct hosted URL
[ ] Created a page in Squarespace
[ ] Pasted code into Page Settings → Advanced → Page Header Code Injection
[ ] Tested on the same network as Solar Assistant
[ ] Verified live data appears in the monitor
```

---

## TROUBLESHOOTING

| Problem | Fix |
|---------|-----|
| Blank page | Check MONITOR_URL is correct and accessible in a browser |
| "Connecting..." forever | Verify you're on the same network as Solar Assistant |
| Squarespace shows normal content too | Make sure the CSS hiding rules are included (the `<style>` block at the top) |
| No Code Injection option | You need Squarespace Business plan or higher |
| Works locally but not on Squarespace | CORS issue — monitor.html must be served with proper headers. Try GitHub Pages. |

---

SunFusion Energy Systems Inc.
CA License #1053778
(858) 217-8861
sunfusion.us
