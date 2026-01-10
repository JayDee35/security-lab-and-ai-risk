# Week 01 — Raspberry Pi 5 Headless Baseline (What we did + why)

## Goal
Get a Raspberry Pi 5 running **headless** (no monitor), reachable via **SSH**, updated, and lightly secured so it’s ready for:
- SSD mounting (logs/data)
- Pi-hole (DNS filtering)
- Tailscale (secure remote admin)
- Logging/telemetry

---

## Hardware setup (what/why)
### What we did
- Unboxed the Pi kit
- Installed the Pi into the case + installed the fan
- Prepared Ethernet + power, but kept setup headless

### Why we did it
- Pi 5 runs warm; case/fan improves stability during installs and updates.
- Headless is “server style” and matches the goal of learning real admin skills.

---

## microSD + OS imaging (what/why)
### What we did
- Used **Raspberry Pi Imager** on Windows
- Selected **Raspberry Pi OS Lite (64-bit)** (no desktop GUI)
- Enabled **SSH**
- Set:
  - Hostname
  - Username
  - Timezone
- Windows asked to “format drive” when microSD inserted → we clicked **Cancel**.

### Why we did it
- Lite = cleaner, faster, less maintenance, perfect for Pi-hole/logging/Tailscale.
- SSH + user setup in Imager makes first boot **headless-ready**.
- Windows can’t read Linux partitions, so the “format” prompt is normal.

---

## First boot (what/why)
### What we did
- Inserted microSD into Pi
- Connected Ethernet to router
- Powered on and waited ~2 minutes

### Why we did it
- Ethernet is the most reliable networking method for first boot headless.
- First boot can take a couple minutes for initial setup.

---

## Connecting from Windows (SSH) — how we found the Pi
### What we tried first
- Tried `HOSTNAME.local` and it failed:
  - “Could not resolve hostname”

### Why it failed
- `.local` relies on mDNS/Bonjour, which often doesn’t work by default on Windows.

### What we did instead (reliable method)
- Couldn’t log into router admin, so we found the Pi by scanning the LAN:
  - Confirmed PC network range using `ipconfig` (IPv4 xxx.xxx.x.xxx)
  - Scanned the network for devices with SSH open
  - Found the Pi
- SSH’d in successfully
- Confirmed login prompt

### Why this works
- The Pi gets a private LAN IP from the router.
- SSH listens on port XX, so scanning for port XX finds it.

---

## Updates + baseline checks (what/why)
### What we did
- Updated and upgraded packages
- Rebooted and reconnected via SSH
- Verified:
  - Hostname
  - IP on ethernet
  - Disk space on microSD:
    - Root filesystem ~118GB, only ~4% used
  - SSH service is active and enabled

### Commands we ran (high level)
- `sudo apt update`
- `sudo apt full-upgrade -y`
- `sudo reboot`
- `hostname`
- `ip a`
- `df -h`
- `uname -a`
- `sudo systemctl status ssh --no-pager`

### Why we did it
- Updates patch security issues and keep the system stable.
- Baseline checks confirm the Pi is healthy and configured correctly.

---

## Made the Pi’s IP stable (static)
### What we did
- Noticed the IP was “dynamic” and could change
- Router admin login wasn’t available, so we set a static IP on the Pi using NetworkManager:
  - Connection name
  - Set IPv4 address
  - Gateway
  - DNS
  - IPv4 method set to manual
- Reconnected after temporarily dropping the connection
- Verified. Is now non-dynamic

### Why we did it
- If the Pi’s IP changes, SSH becomes annoying.
- A stable IP keeps the lab reliable.

---

## Enabled firewall (UFW) safely
### What we did
- Installed UFW
- Allowed SSH
- Enabled UFW
- Verified rules

### Result
- Default inbound: **deny**
- SSH allowed

### Why we did it
- Blocks unsolicited inbound traffic on the LAN.
- Still allows you to manage the Pi via SSH.

---

## Current status (end of today)
✅ Pi is running headless and stable  
✅ SSH working 
✅ Static IP set (won’t randomly change)  
✅ Updated OS  
✅ Firewall enabled with SSH allowed  
✅ Ready for next steps: SSD → Pi-hole → Tailscale

---

## Next session plan (Chunks B, C, D)
- B) Plug in SSD → format + mount to `/mnt/ssd` → create folders for logs/backups/telemetry
- C) Install Pi-hole → validate dashboard → start DNS pointing (per-device if router is locked)
- D) Install Tailscale → connect laptop+phone → remote SSH without port forwarding
