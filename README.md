# 🎮 Quick Tutorial

> ⚠️ **Pre-alpha** – the project is not finished, expect bugs.

## What you need
- **Dolphin** (recent version)
- **Super Mario Galaxy USA** (disc ID **RMGE01**) – your own copy
- **Radmin VPN** (or similar) if you play over the internet without opening ports

---

## 🖥️ Hosting a server (Windows)

1. Download **`server-windows.zip`** and extract it.
2. Double-click **`Avvia server.bat`**. The server control panel opens.
3. Press **Avvia** (Start). The log shows the server is running.
4. Press **Copia** (Copy) to copy your address and send it to your friends.
   With Radmin VPN, everyone must be in the same Radmin network.
5. Optional settings in the panel:
   - **Friendly fire** – players can hurt each other
   - **Luigi** button – play as Mario or Luigi
   - **Volume** slider – panel sounds
   - **Players** list – who is connected right now
6. Want to play too? Install the client on the same PC (see below) and use `127.0.0.1:5029` as the address.

Default port: **UDP 5029**. Keep the panel open while playing.

---

## 🕹️ Joining as a player (client)

1. Download **`client.zip`** and extract it.
2. Double-click **`Connetti al server.bat`**. The client panel opens.
3. Paste the server address (for example `10.0.0.2:5029`) and press **Prova** (Test).
   It tells you right away if the server answers.
4. Choose your character: click the button until it says
   **You are playing as Mario** (red) or **You are playing as Luigi** (green).
5. Press **Installa / aggiorna** (Install / update). It copies the mod into Dolphin.
   **Press it again every time you download a new version.**
6. In Dolphin: right-click the game → **Start with Riivolution Patches** →
   set **Syati Loader** to **Enabled** → **Start**.
7. If the title screen says **SUPER MARIO GALAXY MULTIPLAYER**, the mod is active.
8. Press **A + B** on the title screen: you go straight to the save file. Enter a galaxy and the other players appear on their own.

---

## ❓ Something is wrong?

| Problem | Fix |
|---|---|
| `SYA_ERR - Binary too small` | Press **Installa / aggiorna** again |
| Normal title logo | Start with Riivolution and set Syati Loader to **Enabled** |
| "Server not reachable" | Is the server started? Is Radmin VPN connected? Press **Prova** |
| I can't see anyone | You must be **inside a galaxy**. Don't use save states: restart the game |
| Changed the address, nothing happens | The game reads the address only at startup: restart it |
| Game crashes or weird sync | Everyone must have the **same version**: press **Installa / aggiorna** |
| "Connection lost" | The server closed or the connection dropped: press OK, you go back to the title |
