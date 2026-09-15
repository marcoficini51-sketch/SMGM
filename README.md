# Super Mario Galaxy Multiplayer

> ⚠️ **PRE-ALPHA** – this project is **not finished**. Expect bugs, desyncs and the occasional crash. 
A mod for playing **Super Mario Galaxy** online with friends on **Dolphin** (via Riivolution). Everyone plays their own copy of the game and sees the other players running around the same galaxy in real time – as **Mario or Luigi**.

> Based on **Headpenguin**'s multiplayer mod ([SMGNetworkMultiplayer](https://github.com/Headpenguin/SMGNetworkMultiplayer)). This project adds a Windows server and client with control panels, plus a set of new features listed below.

> The control panels are partly in Italian; button names are given below as they appear, with a translation.

---

## Features

### Players
- **See the other players** – up to 8 at once, with position, facing and animations.
- **Play as Mario or Luigi** – each player picks their character; others see the real Luigi model.
- **Friendly fire** (on/off from the server, even mid-game)
  - ground pound on another player: 1 damage
  - spin attack: knocks them down, no damage

### Shared game
- **Shared stars** – when someone grabs a Power Star, everyone in the same galaxy gets it.
- **Shared coins and star bits** – coins and star bits collected by anyone are added to everyone's counter.
- **Shared items** – items picked up by another player are collected by you too.
- **Shared enemies** – an enemy defeated by one player disappears for everyone.

### Menus and connection
- **Fast start** – press **A + B** on the title screen to jump straight to the save file (**Host save**).
- **Faster menu animations** and a quick **Back** to the title screen.
- **Connection lost** window – if the server closes or the connection drops, a message appears and you go back to the title screen.
- **Custom title logo** – you can tell at a glance that the mod is active.

### Control panels
- **Server panel** – start/stop, connection log, connected players list, friendly fire, Luigi toggle, teleport, volume slider.
- **Client panel** – server address with test button, Mario/Luigi button, one-click install/update, volume slider.

---

## 🐞 Known bugs

- **Bosses:** your hits often don't reach the other player's boss.
- **Bosses:** when the boss dies on your screen, it stays alive on the other player's screen.
- **Items:** items collected by the other player can sometimes appear or disappear strangely.
- **Coins and star bits:** the shared counter may count twice or miss some.
- **Luigi:** as seen by other players, Luigi's lighting can look a bit off.
- **Dolphin:** with MMU disabled, "Invalid write" error windows may pop up instead of a crash.
- **Connection:** if the connection drops, you go back to the title screen and lose the current session.

## Not supported (yet)

- Star selection and the galaxy chosen in the Dome are not synced between players.
- Moving platforms, switches and other level objects are not synced.
- A Linux server package is not included in this release.

---

## Requirements

- **Windows** PC
- **Dolphin** (recent version)
- **Super Mario Galaxy, USA version** – disc ID **RMGE01**. The European version (RMGP01) **will not work**: the patches use memory addresses from the US executable.
- A copy of the game **that you own**. This project does not contain or distribute any game files.
- To play over the internet without opening router ports: **Radmin VPN** or similar.

---

## 🖥️ Tutorial: hosting a server (Windows)

1. Download **`server-windows.zip`** and extract it.
2. Double-click **`Avvia server.bat`** ("start server"). The server panel opens.
3. Press **Avvia** ("start"). The log shows the server is running.
4. Press **Copia** ("copy") to copy your address and send it to your friends.
   With Radmin VPN, everyone must be in the same Radmin network.
5. Optional, in the panel:
   - **Friendly fire** – players can hurt each other
   - **Luigi** – play as Mario or Luigi
   - **Teletrasporta tutti da me** ("teleport everyone to me")
   - **Volume** slider – panel sounds
   - **Players** list – who is connected right now
6. Want to play on the same PC? Use **`Connetti al server.bat`** in the same folder (see the client tutorial) with address `127.0.0.1:5029`.

Default port: **UDP 5029**. Keep the panel open while playing.

## 🕹️ Tutorial: joining as a player (client)

1. Download **`client.zip`** and extract it.
2. Double-click **`Connetti al server.bat`** ("connect to server"). The client panel opens.
3. Enter the server address (for example `10.0.0.2:5029`) and press **Prova** ("test"): it tells you right away whether the server answers.
4. Click the character button until it says **You are playing as Mario** (red) or **You are playing as Luigi** (green).
5. Press **Installa / aggiorna** ("install / update"): it copies the mod files into the right Dolphin folders.
   **Press it again every time you download a new version.**
6. In Dolphin: right-click the game → **Start with Riivolution Patches** → set **Syati Loader** to **Enabled** → **Start**.
7. If the title screen says **SUPER MARIO GALAXY MULTIPLAYER**, the mod is active.
8. Press **A + B** on the title screen, enter a galaxy, and the other players will show up on their own.

---

## Downloads

| File | What's inside |
|---|---|
| `client.zip` | Client panel and mod files – for players |
| `server-windows.zip` | Server panel, server, tools and a client for the host |
| `strumenti.zip` | Debug tools: `Sniff`, `TestPlayers`, `StarSpawn`, `ConnTest` |
| `sorgenti.zip` | Source code: patch scripts, emulator tests, server source |

### Server command line

```bash
SMGServer.exe 5029 0.0.0.0 --friendly-fire=off --controllo friendlyfire.txt --tp tp.txt
```

| Option | What it does |
|---|---|
| `--friendly-fire=on\|off` | initial friendly fire state |
| `--controllo FILE` | file containing `1`/`0`, re-read every second: changes friendly fire without restarting |
| `--tp FILE` | write a player slot number into it and everyone gets teleported to that player |
| `--players FILE` | written every second with the list of connected players |
| `--bye FILE` | when this file appears, the server tells every player it is closing |

---

## How it works (for the curious)

The original client mod can only be compiled with CodeWarrior for PowerPC (Gekko license), which I didn't have. So all the new features are **hand-written PowerPC assembly patches** applied directly to `CustomCode_USA.bin` (Kamek format):

- the new code is appended after the original and hooked in with branches at carefully checked spots
- a purpose-built PowerPC emulator (`simulate.py`) runs the new code against fake game functions and checks every behaviour before each release
- enemy vtables are found in memory at startup by signature
- the position packet grew from 56 to 124 bytes (the limit is 128) but stays compatible: older versions only read the first 56
- Luigi is drawn by loading the real Luigi model and copying the other player's pose onto it

The server only relays packets. It strips the attack flags when friendly fire is off, sets the host and teleport flags, filters out connection tests so they don't take player slots, and re-accepts players who were still connected when the server was restarted.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `SYA_ERR - Binary too small` | Press **Installa / aggiorna** again in the client panel |
| Normal title logo | The patch isn't active: start with Riivolution and set Syati Loader to **Enabled** |
| "Server not reachable" | Is the server started? Is Radmin VPN connected? Press **Prova** |
| I can't see anyone | Are you **inside** a galaxy? Don't use Dolphin save states: restart the game |
| Changed the address but nothing happens | The game only reads the address at startup: restart it |
| Crashes or weird sync | Everyone must have **the same version**: press **Installa / aggiorna** |
| "Connection lost" | The server closed or the connection dropped: press OK to go back to the title screen |

---

## Credits

- **Headpenguin** – original Super Mario Galaxy multiplayer mod
  - mod: https://github.com/Headpenguin/SMGNetworkMultiplayer
  - server: https://github.com/Headpenguin/SMGMultiplayerServer
- **Syati / Kamek** – custom code loading (https://github.com/Treeki/Kamek)
- **Petari** (https://github.com/SMGCommunity/Petari) and **Bussun**'s US symbol maps (https://github.com/SMGCommunity/Bussun) – for understanding how the game works inside

Super Mario Galaxy is a trademark of Nintendo. Fan project, not affiliated with or endorsed by Nintendo.
