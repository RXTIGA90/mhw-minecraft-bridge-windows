# Minecraft × Monster Hunter: World — Windows Passthrough Mod

**English** | [한국어](README.ko.md)

> ⚠️ **This is a work-in-progress test build (0.1.0-dev).** It is published so it can be tried on other PCs, and things may break.
> - **Back up your Monster Hunter: World saves before installing** (the whole Steam `userdata\<account>\582010\remote` folder).
> - Offline only. Do not use it online or with SOS flares.
> - If something goes wrong, restore everything with `Uninstall.ps1` and let us know (GitHub Issues).

## Quick start (3 steps)
1. Download `mhw-minecraft-bridge-windows-0.1.0-dev.zip` from **[Releases](../../releases)**, unzip it, and back up your
   MHW save folder (Steam `userdata\<account>\582010\remote`).
2. Close MHW and the Minecraft Launcher, then double-click **`Setup.bat`**. It installs everything:
   the MHW side (borderless full screen), Fabric for Minecraft 1.21.1, Fabric API, this mod, and a launcher profile
   **"MHW Bridge"** with its own folder (your other Minecraft worlds and mods are not touched).
   You only need Monster Hunter: World (Steam) and Minecraft Java Edition (official launcher, logged in once). No controller or driver.
3. To play: put Steam in **offline mode** → double-click **`Play.bat`** (starts MHW) → enter the hub offline →
   in the Minecraft Launcher choose **"MHW Bridge"** → Play. Finish with **F12** in Minecraft while in the hub.

To remove everything: **`Uninstall.bat`**. Details below.
## What it is
Play real Minecraft 1.21.1 (Fabric) inside Monster Hunter: World (PC, offline). Minecraft's image is composited into
MHW's frame (blocks behind MHW terrain and characters are hidden), and Steve walks on MHW's terrain, builds, and fights
MHW's monsters.

Based on [justbustin/minecraft-crossover-bridge](https://github.com/justbustin/minecraft-crossover-bridge)
(macOS/CrossOver, MIT). This package makes that code run natively on Windows and adds features.

## Requirements
- Windows 10/11, Steam Monster Hunter: World **15.23.00 (build 421810)** + Iceborne
- Minecraft Java 1.21.1, [Fabric Loader](https://fabricmc.net/use/) 0.16 or newer, [Fabric API](https://modrinth.com/mod/fabric-api) for 1.21.1
- MHW in a window (default 2560x1440) or borderless full screen (4K works). Screens larger than 2560x1600 get Minecraft's
  image sent scaled down and stretched back by MHW
- In-game texts follow Minecraft's language setting (English and Korean included)
- Minecraft in a **window**, not full screen (F11 toggles it; full-screen Minecraft is not composited into MHW)
- No controller or extra driver: while Minecraft runs, the mod gives MHW a built-in stand-in pad for the R/V/M keys and the F12 save.
  Optional: the [ViGEmBus](https://github.com/nefarius/ViGEmBus/releases) driver + Python + `pip install vgamepad` lets
  `Save-And-Quit.bat` save MHW even when Minecraft is not running
- For the F12 save only: MHW's text language set to **Korean** and the Korean language (with its OCR part) added in Windows
  (Settings → Time & language → Language & region). Without these, save MHW by hand (see "Play" step 5)

## Install
Run **`Setup.bat`** (MHW and the Minecraft Launcher closed). What it does:
1. MHW side (`Install.ps1 -Borderless`): copies `dinput8.dll` and `mhwbridge\`, backs up `graphics_option.ini`, sets
   DirectX 11 and borderless full screen at your desktop resolution. It stops if another `dinput8.dll` mod loader
   (e.g. Stracker's Loader) is present, or if MHW was never started (start and close it once first).
   A window instead: `Setup.bat -Window` (2560x1440 window). Other MHW folder: `Setup.bat -GameDir "D:\...\Monster Hunter World"`.
2. Minecraft side: the latest Fabric Loader for 1.21.1 (from meta.fabricmc.net), Fabric API for 1.21.1 (from Modrinth,
   hash checked) and this mod go into `%APPDATA%\.minecraft\mhw-bridge`, with first-run settings (window mode, render
   distance 12, your Windows language). A launcher profile "MHW Bridge" points there. The launcher's profile file is
   backed up first (`launcher_profiles.json.mhwbridge-backup`).

Running `Setup.bat` again is safe (it updates the same files).

Manual install (instead of Setup.bat): `Install.ps1 [-Borderless]`, then the [Fabric installer](https://fabricmc.net/use/installer/)
for Minecraft **1.21.1**, and `bin\minecraft-mod\mhw-bridge-0.1.0-windows.jar` + [Fabric API](https://modrinth.com/mod/fabric-api)
(the `...+1.21.1` file) into the Fabric profile's `mods` folder.
## Play
0. Put Steam in **offline mode** (Steam menu → Go Offline). Online, MHW shows the "Join a session" menu after character select.
1. Run `Play.bat`: it starts MHW and the F12 quit watcher (keep its window open; a note about a missing virtual pad is fine).
2. Enter the hub or a field **offline** in MHW. (No online play / SOS: this mod changes monster HP.)
3. In the Minecraft Launcher pick the profile **"MHW Bridge"** and press Play (confirm the "modded" warning). If MHW covers the whole screen, Alt+Tab to the launcher. The "MHW Bridge" world opens automatically and the Minecraft window sits on top of MHW and takes the input.
   Minecraft must run **in a window**: if it opens full screen, press F11 (full-screen Minecraft shows up as a separate screen, not inside MHW).
4. Going to a field: M (map) → F (world map) → Q/E (low/high rank) → WASD (region) → Enter.
5. To finish press **F12** in Minecraft (or run `Save-And-Quit.bat`): saves the Minecraft world → brings MHW to the front and saves it
   through its system menu → closes MHW. The mod stays installed; next time start again from step 1. Nothing ever rolls your saves back.
   - The MHW save reads the menu on screen, so it **only works with MHW's text language set to Korean**, the Windows Korean OCR
     language installed (included in Korean Windows). Don't touch the mouse or keyboard while it runs.
   - **MHW is only closed after the save file was actually updated.** If the save can't be done (other language, no OCR/pad, in a field
     or quest, loading screen), Minecraft still quits but MHW stays open: save by hand (start menu → System → Save) and quit MHW
     yourself. `%TEMP%\mhwmc\exit.log` says what happened (`saved through the menu (save file updated)` on success).
   - MHW can only be saved manually in the hub, so press F12 in the hub.
   - After a crash or power loss: Minecraft keeps its last autosave (about every 5 minutes), MHW its last auto/manual save.

| Key | Action |
|---|---|
| R | Hunter interaction (talk, gather, carve; pad A). The Minecraft window must be in front |
| V | MHW menu mode on/off: WASD cursor, arrow keys select, Enter confirm, Esc back, Q/E tabs, F = X, C = Y, Tab map, P start menu |
| M | Open the MHW map + menu mode |
| F6 | Composited inside MHW ⇄ separate window |
| F7 | Camera: Minecraft drives MHW's camera ⇄ Minecraft follows MHW's camera |
| F8 | Hand keyboard and mouse to the hunter (F8 in the MHW window to come back) |
| F9 | Debug view (state, hitboxes, HP) |
| F10 | Hunter stand-in on/off (a hidden hunter under Steve takes MHW's attacks for him) |
| F12 | Save both games and quit (while the `Play.bat` window is open) |
| F4 | Settings: ledge guard, arrow assist, blast assist, mobs vs monsters, explosion craters, [experimental] monsters blocked by buildings (off by default). Each on/off, saved in `config\mhwbridge.properties` |

Menu mode (V/M) ends with V or M again, or with a **mouse click**. Switching windows (Alt+Tab) releases any held keys.

Everything else is normal Minecraft. Travel between areas through the MHW map with V/M.

**If M/V/R do nothing in MHW:** wait a few seconds after Minecraft attaches (the mod then wakes up MHW's pad input), keep the mouse off
the MHW window (MHW switches to keyboard prompts and may ignore the pad), and have only one controller connected.

## Where things are saved
- Minecraft world: `saves\mhw-bridge` in the Fabric profile (includes the crater list `mhwbridge-craters.txt` and area anchors)
- MHW saves: Steam `userdata\<account>\582010\remote` (synced by Steam Cloud)
- Graphics settings backup made by the installer: `mhwbridge-backup\` in the MHW folder
- Logs: `%TEMP%\mhwmc\mhw-bridge.log` (MHW side), `exit.log` (quit), Minecraft `logs\latest.log`

## Features
- MHW terrain collision (approximated with invisible blocks), walls, ledge guard for drops over 8 blocks
- Block placing; TNT and creeper explosions dig craters into MHW's ground (dirt/stone bowl, MHW ground removed on screen)
- Swords, bows, TNT and creepers hurt MHW monsters; monster attacks hurt Steve
- Hostile Minecraft mobs attack nearby MHW monsters. MHW monsters hurt Minecraft mobs with charges/body slams (when moving fast)
  and with projectiles (fireballs, breath). Standing next to a monster does no harm. Bites and tail swipes in place don't reach mobs yet

## Uninstall
Double-click **`Uninstall.bat`**: removes the mod files from the game folder, restores the graphics settings and removes the
"MHW Bridge" launcher profile. The Minecraft folder `%APPDATA%\.minecraft\mhw-bridge` (its world and settings) is kept;
delete it yourself if you like.
## Known limits
- MHW monsters and the hunter don't see craters and keep walking on the original ground (engine limit).
- Terrain is approximated in whole blocks, so slopes and steps snap to one-block steps.
- At 4K (3840x2160, `-Borderless`) Minecraft's image is sent at 2560x1440 and stretched (MHW itself stays 4K).
  Measured in Seliana on the developer PC with Minecraft's render distance 12: 1080p window ~144 fps, 1440p window
  ~120–127 fps, 4K ~90 fps, compositing 1–2.4 ms per frame. Another test with render distance 32 got about 60–65 fps at
  1440p and 4K: lower Minecraft's render distance (Video Settings) if MHW feels slow.
- [Experimental, unreliable] Monsters blocked by buildings (off by default): MHW's collision and pathfinding don't know Minecraft blocks,
  so a monster is pushed back the moment it moves into built blocks. In tests monsters were held briefly but often broke through
  in the end. It is inactive for 5 s after an area change and while Minecraft restarts.
- MHW's AI doesn't see Minecraft mobs (body contact damage stands in).
- **Back up your saves.** The mod changes game state such as monster HP.

## Folder layout
- `Setup.bat` one-click install, `Play.bat` start a session, `Uninstall.bat` remove everything, `Save-And-Quit.bat` same as F12
- `bin\` binaries (MHW DLLs, Minecraft mod jar)
- `source\` full source (mhw-bridge: C++/MinHook, mc-bridge: Fabric mod)
- `tools\` setup/session/quit scripts, optional virtual pad, core build script
- `docs\CHANGES.md` changes compared with the original

## License
MIT (see `LICENSE`). Original work © justbustin; Windows port and additions © RXTIGA90. Third-party notices in `THIRD_PARTY_NOTICES.md`.
