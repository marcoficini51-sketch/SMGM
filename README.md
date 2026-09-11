# Super Mario Galaxy Multiplayer

A mod for playing **Super Mario Galaxy** online with friends on **Dolphin** (via Riivolution). Everyone plays their own copy of the game and sees the other Marios running around the same galaxy in real time.

> Based on **Headpenguin**'s multiplayer mod ([SMGNetworkMultiplayer](https://github.com/Headpenguin/SMGNetworkMultiplayer)). This project adds a Windows server with a control panel and a set of new features, listed below.

> The in-game tools and the control panel are in Italian; the button names are given below as they appear, with a translation.

---

## Features

- **See the other players** – up to 8 Marios at once, with position, facing and walking animation (no more sliding around).
- **Friendly fire** (turned on and off from the server, even mid-game)
  - ground pound on another player: 1 damage
  - spin attack: knocks them down, no damage
- **Shared stars** – when someone grabs a Power Star, the star appears on top of everyone else in the same galaxy and they collect it together.
- **Shared enemies**
  - an enemy defeated by one player disappears for everyone
  - enemy position and state (idle, chasing, attacking…) come from one "leader" player, so everyone sees the same scene
  - whoever hits an enemy controls it for 2 seconds, so they can finish it off
  - hits are forwarded to the leader, so they count for bosses too
- **Shared bosses** – Topmaniac, Kamella, Bugaboom, Dino Piranha, Bowser, Bouldergeist, Kingfin, Tarantox, Megaleg, Major Burrows, Baron Brrr, Bowser Jr.'s airship. The leader is whoever has seen the most cutscenes in the galaxy, i.e. whoever is furthest into the fight.
- **Teleport** – a button in the server panel brings every Mario to you (like `/tp` in Minecraft).
- **Custom title logo**, so you can tell at a glance that the mod is active.

## Not supported (yet)

- **Coins** are not shared.
- Moving platforms, switches and other level objects are not synced.
- Enemy rotation is not transmitted: an enemy may sometimes face the wrong way.
- A player who is behind in a boss fight doesn't see the cutscene: their boss jumps straight to the next phase.
- Teleport only works between players in the same galaxy.

---

## Requirements

- **Dolphin** (recent version)
- **Super Mario Galaxy, USA version** – disc ID **RMGE01**. The European version (RMGP01) will not work: the patches use memory addresses from the US executable.
- A copy of the game **that you own**. This project does not contain or distribute any game files.
- To play over the internet without opening router ports: **Radmin VPN** or similar (or a server reachable from the internet).

## Installation (player)

1. Extract `client.zip` and double-click **`Connetti al server.bat`** ("connect to server").
2. Enter the server address and press **Prova** ("test"): it tells you right away whether the server responds.
3. Press **Installa / aggiorna** ("install / update"): it copies the mod files into the right Dolphin folders.
4. In Dolphin: right-click the game → **Start with Riivolution Patches** → set *Syati Loader* to **Enabled** → Start.

If the title screen says **SUPER MARIO GALAXY MULTIPLAYER**, the mod is active. Enter a galaxy and the other players will show up on their own.

## Server

### Windows
Extract `server-windows.zip` and run **`Avvia server.bat`**. A control panel opens with:
- start / stop (**Avvia** / **Ferma**) and a connection log
- the address to give your friends, with a *Copia* ("copy") button
- a **Friendly fire** checkbox
- **Stella per tutti** ("star for everyone") – to test the shared star without finishing a level
- **Teletrasporta tutti da me** ("teleport everyone to me")
- **Test player** – fake Marios for testing on your own

### Linux / VPS
`server-linux.zip` contains the source code, a systemd service and an install script.

```bash
./SMGServer 5029 0.0.0.0 --friendly-fire=off --controllo friendlyfire.txt --tp tp.txt
```

| Option | What it does |
|---|---|
| `--friendly-fire=on\|off` | initial friendly fire state |
| `--controllo FILE` | file containing `1`/`0`, re-read every second: changes friendly fire without restarting |
| `--tp FILE` | write a player slot number into it and everyone gets teleported to that player |

Default port: **UDP 5029**.

---

## How it works (for the curious)

The original client mod can only be compiled with CodeWarrior for PowerPC (Gekko license), which I didn't have. So all the new features are **hand-written PowerPC assembly patches** applied directly to `CustomCode_USA.bin` (Kamek format):

- the new code is appended after the original and hooked in with branches at a few carefully checked spots
- a purpose-built PowerPC emulator (`simulate.py`) runs the new code against fake game functions and checks every behaviour before each release
- enemy vtables are not taken from the symbol map: they are found in memory at startup by signature
- the position packet grew from 56 to 124 bytes (the limit is 128) but stays compatible: older versions only read the first 56

The server only relays packets. It strips the attack flags when friendly fire is off, sets the teleport flag, and re-accepts players who were still connected when the server was restarted.

Included tools (`strumenti.zip`): `Sniff` (shows what each player is sending – handy for debugging), `TestPlayers`, `StarSpawn`, `ConnTest`.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `SYA_ERR - Binary too small` | Press *Installa / aggiorna* again in the client panel |
| Normal title logo | The patch isn't active: start with Riivolution and set *Enabled* |
| I can't see anyone | VPN connected? Server running (*Prova*)? Are you **inside** a galaxy? No Dolphin save states: restart the game |
| Changed the address but nothing happens | The game only reads the address at startup: restart it |
| Enemies or walking not synced | Everyone must have **the same** `CustomCode_USA.bin` |

---

## Credits

- **Headpenguin** – original Super Mario Galaxy multiplayer mod
  - mod: https://github.com/Headpenguin/SMGNetworkMultiplayer
  - server: https://github.com/Headpenguin/SMGMultiplayerServer
- **Syati / Kamek** – custom code loading (https://github.com/Treeki/Kamek)
- **Petari** (https://github.com/SMGCommunity/Petari) and **Bussun**'s US symbol maps (https://github.com/SMGCommunity/Bussun) – for understanding how the game works inside

Super Mario Galaxy is a trademark of Nintendo. Fan project, not affiliated with or endorsed by Nintendo.
