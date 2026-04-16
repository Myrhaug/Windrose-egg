# 🏴‍☠️ Windrose Dedicated Server — Pelican/Pterodactyl Egg

A working egg for running a [Windrose](https://store.steampowered.com/app/3041230/Windrose/) dedicated server on [Pelican Panel](https://pelican.dev/) or [Pterodactyl Panel](https://pterodactyl.io/).

> Tested and working as of launch day — April 14, 2026.

---

## Requirements

- Pelican Panel or Pterodactyl Panel
- At least **16 GB RAM** allocated to the server
- Two port allocations (game port + query port)

---

## Installation

### Import via URL (recommended)

1. In your panel, go to **Admin → Nests → Import Egg**
2. Paste the following raw URL:
   ```
   https://raw.githubusercontent.com/Myrhaug/Windrose-egg/main/egg-windrose.json
   ```
3. Click **Import**

### Manual import

1. Download `egg-windrose.json` from this repo
2. Go to **Admin → Nests → Import Egg** and upload the file

---

## Configuration

After creating a server with this egg, you can configure the following variables:

| Variable | Description | Default |
|---|---|---|
| `SRV_NAME` | Server display name | `Windrose Server` |
| `MAX_PLAYERS` | Max simultaneous players (1–10) | `4` |
| `SRV_PW` | Join password (leave blank for none) | *(empty)* |
| `WORLD_DIFFICULTY` | Difficulty preset: `Easy`, `Medium`, `Hard`, `Custom` | `Medium` |
| `AUTO_UPDATE` | Update server on startup | `1` |

---

## Technical details

- **Docker image (runtime):** `ghcr.io/pelican-eggs/steamcmd:proton_8`
  - Uses Proton 8 to avoid a crash caused by Xalia in newer GE-Proton versions
- **Docker image (installer):** `ghcr.io/parkervcp/installers:debian`
- **Steam App ID:** `4129620` (Windrose Dedicated Server — free on Steam)
- **Server binary:** `R5/Binaries/Win64/WindroseServer-Win64-Shipping.exe`

### Startup command

```bash
find /home/container -name "xalia.exe" -delete 2>/dev/null
find /home/container -name "xalia.dll" -delete 2>/dev/null
rm -f ./R5/Saved/Logs/R5.log
PROTON_NO_ESYNC=1 PROTON_NO_FSYNC=1 proton run ./R5/Binaries/Win64/WindroseServer-Win64-Shipping.exe \
  -log -nullrhi -MULTIHOME=0.0.0.0 -PORT={{SERVER_PORT}} -QUERYPORT={{SERVER_PORT_2}} \
  & WR_PID=$! ; sleep 30 ; tail -n0 -F ./R5/Saved/Logs/R5.log & wait $WR_PID
```

The Xalia files are deleted on each startup because they cause a crash in headless Docker containers.

---

## Finding your invite code

Windrose uses invite codes instead of IP:port for connections. Your invite code is found in:

```
/home/container/R5/ServerDescription.json
```

Or look for `InviteCode` in the server console log after startup.

Players connect via: **Play → Connect to Server → enter invite code**

---

## Ports

| Port | Protocol | Description |
|---|---|---|
| `SERVER_PORT` | UDP | Game port |
| `SERVER_PORT_2` | UDP | Query port |

Both ports must be allocated in the panel.

---

## Save data

Save data is stored at:
```
/home/container/R5/Saved/SaveProfiles/Default/RocksDB/
```

Back up this folder before reinstalling or resetting the server.