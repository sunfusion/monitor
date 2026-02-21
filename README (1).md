# ☀️ SunFusion Live Energy Monitor v7.5.0

**Real-time solar energy monitoring for Sol-Ark inverter systems**
Built by SunFusion Energy Systems Inc. · CA License #1053778 · (858) 217-8861

---

## What This Is

A complete web app that shows live solar production, battery status, grid usage, and home load from your Sol-Ark inverter through Solar Assistant. It connects over MQTT WebSocket and works on any device with a browser — phone, tablet, PC, wall-mounted display, or embedded in a website.

Three user roles:
- **Admin** — Full fleet management, edit everything, SSH references, setup tools
- **Customer** — View their own system, change their IP/port/zip
- **Kiosk** — Locked-down display mode for websites and wall-mounted screens

---

## Files & Folders

```
sunfusion-app/
│
│── CORE APP ─────────────────────────────────────────────
│
├── monitor.html             ← Live energy dashboard (the main app)
│                               Power flow diagram, battery SOC, grid status,
│                               weather alerts, 25-year history, CSV export,
│                               detail panels for PV/Battery/Grid/Load/Sol-Ark
│
├── fleet.html               ← Admin fleet manager (password: ess123)
│                               Add/edit/delete systems, map view, list view,
│                               CSV import/export (30,000+ systems), search/filter,
│                               setup guide, access codes, SSH references,
│                               Cloud Proxy (Site ID) field per system
│
├── customer.html            ← Customer portal
│                               6-digit access code login, view system info,
│                               edit IP/port/zip, read-only admin fields,
│                               "Remember my code" option, auto-lookup
│
├── kiosk.html               ← Website/wall display (no lock, loads immediately)
│                               Edit 5 config values at top of file,
│                               loads monitor.html in full-screen iframe
│
│── SQUARESPACE ──────────────────────────────────────────
│
├── squarespace-embed.html   ← Code to paste into Squarespace Code Block
│                               Edit 5 config values, copy/paste into site
│
├── SQUARESPACE_INSTALL.md   ← Step-by-step Squarespace setup instructions
│
│── PWA (INSTALLABLE APP) ───────────────────────────────
│
├── manifest.json            ← PWA manifest (app name, icons, colors)
├── sw.js                    ← Service worker (offline caching, auto-update)
│
│── DOCUMENTATION ────────────────────────────────────────
│
├── README.md                ← This file
├── MQTT_Setup_Guide.pdf     ← How to enable MQTT WebSocket on Solar Assistant
│
│── ICONS ────────────────────────────────────────────────
│
└── icons/
    ├── icon.svg             ← Source vector icon
    ├── favicon-32x32.png    ← Browser tab icon
    ├── apple-touch-icon.png ← iPhone/iPad home screen
    ├── icon-72x72.png       ← Android small
    ├── icon-96x96.png       ← Android medium
    ├── icon-128x128.png     ← Windows small
    ├── icon-144x144.png     ← Windows medium
    ├── icon-152x152.png     ← iPad
    ├── icon-192x192.png     ← Android/Chrome install
    ├── icon-384x384.png     ← Splash screen
    └── icon-512x512.png     ← Splash screen large
```

---

## How It All Connects

```
Solar Assistant (Raspberry Pi)
  │
  │  MQTT over WebSocket (port 9001)
  │
  ▼
monitor.html (runs in browser)
  │
  ├── Opened from fleet.html (admin clicks a system)
  ├── Opened from customer.html (customer enters access code)
  ├── Opened from kiosk.html (auto-loads, no login)
  └── Opened from Squarespace (iframe embed)
```

---

## Quick Start — Local Use

### 1. Enable MQTT WebSocket on Solar Assistant

SSH into your Solar Assistant and run this one command:

```bash
sudo bash -c 'cat >> /etc/mosquitto/conf.d/solar-assistant.conf << EOF

listener 9001
protocol websockets
allow_anonymous true
EOF
systemctl restart mosquitto'
```

Verify it worked:
```bash
sudo ss -tlnp | grep 9001
```
You should see `LISTEN 0.0.0.0:9001`

> Full guide with screenshots: see `MQTT_Setup_Guide.pdf`

### 2. Open the App

**Simplest way:** Double-click `customer.html` in your browser.

**For admin access:** Open `fleet.html` → password is `ess123`

**For just the monitor:** Open `monitor.html` → enter your Solar Assistant IP → click Connect

### 3. What You Need to Know

| Info | Where to Find It | Example |
|------|-------------------|---------|
| Solar Assistant IP | SA app → Settings → Network | `192.168.0.165` |
| Cloud Proxy (Site ID) | SA app → Settings → Remote Access | `sunfusionenergysystems.us.solar-assistant.io` |
| MQTT Port | You set this in Step 1 | `9001` |
| SSH Password | Default | `solar123` |
| Admin Password | Fleet Manager login | `ess123` |

---

## Setup for Each Role

### Admin (fleet.html)

1. Open `fleet.html` in browser
2. Enter password: **ess123** (8-hour session)
3. Click **+ ADD SYSTEM**
4. Fill in:
   - **System Name** — customer name or address
   - **IP Address** — Solar Assistant local IP (from SA → Settings → Network)
   - **Port** — 9001 (default)
   - **Cloud Proxy (Site ID)** — from SA → Settings → Remote Access (optional but recommended)
   - **Zip Code** — for weather data and map location
   - **Group** — region or installer name (optional, for filtering)
   - **Customer Name** — customer's name (optional)
   - **Phone** — customer phone (optional)
   - **Address** — street address (optional)
   - **Notes** — equipment details, install date, etc. (optional)
5. Click **SAVE SYSTEM**
6. A 6-digit **Access Code** auto-generates — give this to the customer

**Admin can edit ALL fields** for any system. When editing, you also see:
- **SSH Quick Reference** with copy buttons for local SSH, remote SSH, cloud URL, and password
- **Access Code** with regenerate and copy buttons

**Other admin features:**
- 📖 **SETUP GUIDE** button — in-app 6-step walkthrough for new installs
- 📥 **IMPORT CSV** — bulk import thousands of systems
- 📤 **EXPORT CSV** — download all systems as spreadsheet
- 🗺 **MAP/LIST** toggle — view all systems on a map or sortable table
- 🔍 **Search** — filter by name, IP, group, or status
- Click any system to open its live monitor

### Customer (customer.html)

1. Open `customer.html`
2. Enter the 6-digit access code your installer gave you
3. You see your system info + **OPEN MY MONITOR** button
4. Below that, **⚙ EDIT MY CONNECTION** section where you can change:
   - **Solar Assistant IP Address** — if your SA gets a new IP
   - **Port** — if you changed the MQTT port
   - **Zip Code** — for weather data
5. Click **💾 SAVE CHANGES**
6. Read-only fields show: System Name, Customer, Access Code, Group, Phone

**Customer CANNOT change:** system name, access code, group, phone, or notes (admin only)

### Kiosk (kiosk.html)

For wall-mounted displays, lobby screens, or website embeds. No login, no settings, no buttons — just the live dashboard.

1. Open `kiosk.html` in a text editor (Notepad, TextEdit)
2. Edit these 5 lines near the top:

```javascript
const SYSTEM_NAME = 'SunFusion HQ';     // Name shown in header
const SYSTEM_IP   = '192.168.0.165';    // Solar Assistant IP
const SYSTEM_PORT = 9001;               // MQTT WebSocket port
const SYSTEM_ZIP  = '92081';            // Zip code for weather
const MONITOR_PATH = 'monitor.html';    // Leave this alone
```

3. Save and open in browser — monitor loads instantly

---

## Squarespace Website Setup

Squarespace can't host HTML files. You need to host `monitor.html` somewhere else (GitHub Pages is free), then embed it with an iframe.

### Step 1 — Host monitor.html on GitHub Pages (free, 5 minutes)

1. Go to **github.com** → sign up or log in
2. Click **+** (top right) → **New repository**
3. Name it: **sunfusion-monitor**
4. Set to **Public**
5. Check ✅ **Add a README file**
6. Click **Create repository**
7. Click **Add file** → **Upload files**
8. Drag `monitor.html` onto the page (rename it to `index.html` first)
9. Click **Commit changes**
10. Click **Settings** tab → **Pages** in left sidebar
11. Under Branch: change **None** to **main**, folder stays **/ (root)**
12. Click **Save**
13. Wait 2 minutes, refresh — you'll see: `https://YOURUSERNAME.github.io/sunfusion-monitor/`

### Step 2 — Add to Squarespace

1. Open `squarespace-embed.html` in Notepad
2. Edit the 5 config lines:

```javascript
const SF_NAME  = 'SunFusion HQ';          // System name
const SF_IP    = '192.168.0.165';          // Solar Assistant IP
const SF_PORT  = 9001;                     // Port
const SF_ZIP   = '92081';                  // Zip code
const SF_URL   = 'https://YOURUSERNAME.github.io/sunfusion-monitor/';
```

3. Copy EVERYTHING from the `<style>` tag to the closing `</script>` tag
4. In Squarespace → open your page → add a **Code Block**
5. Paste it in → make sure **Display Source** is OFF
6. Save

> ⚠️ **Network Note:** The monitor connects to Solar Assistant on port 9001. This only works when the viewer is on the SAME local network as the Solar Assistant device. Internet visitors cannot reach 192.168.x.x addresses. For remote access you need port forwarding or a VPN.

---

## Monitor Features (monitor.html)

### Main Screen — Power Flow Diagram
- Animated power flow lines show energy direction in real-time
- **Solar (PV)** → Sol-Ark → Battery / Grid / Load
- Flow direction reverses correctly:
  - Battery: dashes flow TOWARD battery when charging, AWAY when discharging
  - Grid: dashes flow TOWARD house when importing, AWAY when exporting
- Click any node (PV, Battery, Grid, Load, Sol-Ark) to open detail panel

### Detail Panels (5 full-screen overlays)
- **PV Panel** — current/peak production, daily energy, string voltages
- **Battery Panel** — SOC, voltage, current, temperature, charge/discharge status, tabs for up to 15 modules
- **Grid Panel** — import/export power, frequency, voltage, daily import/export energy
- **Load Panel** — current consumption, daily usage
- **Sol-Ark Panel** — inverter status, fault codes, firmware, tabs for up to 12 inverter units

### Data & History
- **25-year IndexedDB history** — auto-records every 5 seconds
- **Full-screen history viewer** — scroll back through days/weeks/months
- **CSV export** — download history data as spreadsheet
- **Sparkline graphs** — mini trend lines in detail panels

### Weather & Alerts
- Live weather from OpenWeather API (uses zip code)
- **PV production loss alerts** — warns if clouds are reducing solar output
- Connection status alerts (15-second grace period for reconnections)

### Modes
- **Admin mode** — full access, fleet back button, settings gear
- **Customer mode** (`?mode=customer`) — no fleet access, no settings, LOGOUT button
- **Kiosk mode** (`?mode=kiosk`) — no buttons at all, just the dashboard

### URL Parameters
Open monitor.html with these URL parameters to skip the splash screen:

```
monitor.html?host=192.168.0.165&port=9001&name=Smith%20Residence&zip=92081&mode=kiosk
```

| Parameter | Required | Example | Description |
|-----------|----------|---------|-------------|
| `host` | Yes | `192.168.0.165` | Solar Assistant IP |
| `port` | No | `9001` | MQTT WebSocket port (default: 9001) |
| `name` | No | `Smith%20Residence` | System name in header |
| `zip` | No | `92081` | Zip code for weather |
| `mode` | No | `kiosk` | `admin`, `customer`, or `kiosk` |
| `code` | No | `A3X9K2` | Customer access code |
| `prefix` | No | `solar_assistant` | MQTT topic prefix (default: solar_assistant) |
| `user` | No | | MQTT username (if auth enabled) |
| `pass` | No | | MQTT password (if auth enabled) |

---

## Fleet Manager Features (fleet.html)

- **Password protected** — admin password: `ess123` (8-hour session)
- **Add/Edit/Delete systems** — all fields including Cloud Proxy (Site ID)
- **Map view** — Leaflet.js with canvas-rendered markers, zoom to fit all systems
- **List view** — sortable table with name, IP, group, status, access code
- **Search & filter** — by name, IP, group, or status
- **CSV import** — bulk import 30,000+ systems from spreadsheet
- **CSV export** — download all systems with all fields
- **Access codes** — auto-generated 6-character codes, regenerate, copy
- **SSH Quick Reference** — shows when editing, copy buttons for all SSH commands
- **📖 Setup Guide** — in-app 6-step walkthrough for new client installs
- **Auto-geocoding** — zip code → lat/lng via OpenWeather API for map placement

### CSV Format for Import

```csv
name,ip,port,zip,group,code,siteid,customer,phone,address,notes
Smith Residence,192.168.0.165,9001,92081,San Diego,,smithhome.us.solar-assistant.io,John Smith,858-555-1234,123 Main St,Sol-Ark 15K 2x ECHO
```

All columns except `name` and `ip` are optional. Access codes auto-generate if blank.

---

## MQTT Setup (Solar Assistant)

The monitor needs MQTT WebSocket enabled on port 9001. Two ways to set this up:

### Quick Command (SSH)

```bash
# Connect via local IP or Site ID:
ssh solar-assistant@192.168.0.165
# or
ssh solar-assistant@yoursiteid.us.solar-assistant.io

# Password: solar123

# Enable WebSocket on port 9001:
sudo bash -c 'cat >> /etc/mosquitto/conf.d/solar-assistant.conf << EOF

listener 9001
protocol websockets
allow_anonymous true
EOF
systemctl restart mosquitto'

# Verify:
sudo ss -tlnp | grep 9001
```

### Full PDF Guide

See `MQTT_Setup_Guide.pdf` — includes screenshots, troubleshooting table, and advanced authentication setup.

---

## Installing as a Phone/Tablet/Desktop App (PWA)

The app can be installed on any device for a native app experience:

### iPhone / iPad
1. Open in **Safari** (must be Safari)
2. Tap **Share** button → **Add to Home Screen**
3. Name it "SunFusion" → tap **Add**

### Android
1. Open in **Chrome**
2. Tap the **Install** banner or ⋮ menu → **Add to Home Screen**

### Windows / Mac
1. Open in **Chrome** or **Edge**
2. Click the 📲 install icon in the address bar
3. Click **Install**

### Wall Display / Kiosk
1. Open `kiosk.html` in Chrome
2. Press **F11** for fullscreen
3. The monitor auto-connects and stays on screen

---

## Dual-Port MQTT Fallback

The monitor automatically tries both MQTT ports:
1. First attempt: your configured port (default 9001), 8-second timeout
2. Fallback: alternate port (1883 if you set 9001, or 9001 if you set 1883)
3. Whichever port connects gets saved as the new default

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| "Connecting..." forever | Verify Solar Assistant IP is correct and you're on the same network |
| Port 9001 not working | SSH in and run the MQTT setup command above, then verify with `ss -tlnp \| grep 9001` |
| MQTT connected but no data | Check topic prefix — default is `solar_assistant`. Open SA web UI to verify data is flowing |
| Battery flow arrows wrong direction | Fixed in v7.5.0 — charging flows toward battery, discharging flows away |
| Customer can't find access code | Admin: open fleet.html → edit the system → access code is shown with copy button |
| Fleet map is empty | Systems need zip codes for geocoding — edit and add zip codes |
| Squarespace shows blank | monitor.html must be hosted on a real server (GitHub Pages), not on Squarespace |
| Squarespace still shows normal page | Make sure the CSS `<style>` block is included — it hides Squarespace's content |
| App won't install on iPhone | Must use Safari. Chrome on iOS can't install PWAs |
| Weather not showing | Check zip code in settings and internet connection |
| Old version stuck | Clear browser cache (Ctrl+Shift+R) or unregister service worker in DevTools |

---

## Defaults & Credentials

| Item | Default Value |
|------|---------------|
| Admin password (fleet) | `ess123` |
| Admin session duration | 8 hours |
| MQTT WebSocket port | `9001` |
| MQTT fallback port | `1883` |
| MQTT topic prefix | `solar_assistant` |
| SSH user (Solar Assistant) | `solar-assistant` |
| SSH password | `solar123` |
| Weather API key | Built-in (OpenWeather) |
| IndexedDB history | 25 years retention |
| Reconnection grace period | 15 seconds |

---

## Version History

| Version | Changes |
|---------|---------|
| v7.5.0 | Kiosk mode, Squarespace embed, battery flow direction fix, copy buttons, Site ID field |
| v7.4.2 | Dual-port MQTT fallback (9001 ↔ 1883) |
| v7.4.0 | Role-based security: admin/customer/kiosk, 6-digit access codes |
| v7.3.2 | Fleet manager bug fix (splash screen overlay) |
| v7.3.0 | Fleet manager: map, list, CSV import/export, 30K+ systems |
| v7.2.1 | Brightened text colors for readability |
| v7.2.0 | UX simplification: 2-step splash, plain English labels |
| v7.1.0 | PWA: installable app, service worker, offline support |
| v7.0.2 | Connection stability fix (wildcard subscription → catch-all) |
| v7.0.0 | Major UI overhaul: power flow diagram, 5 detail panels |

---

## Contact

**SunFusion Energy Systems Inc.**
San Diego County, California
CA C-10 Electrical Contractor · License #1053778
Phone: (858) 217-8861
Web: sunfusion.us
