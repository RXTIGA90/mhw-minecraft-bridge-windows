# Minecraft × Monster Hunter: World — Windows Passthrough Mod

**English** | [한국어](README.ko.md)

> ⚠️ **This is a work-in-progress test build (0.1.0-dev).** It is published so it can be tried on other PCs, and things may break.
> - **Back up your Monster Hunter: World saves before installing** (the whole Steam `userdata\<account>\582010\remote` folder).
> - Offline only. Do not use it online or with SOS flares.
> - If something goes wrong, restore everything with `Uninstall.ps1` and let us know (GitHub Issues).

## Quick start
1. Download `mhw-minecraft-bridge-windows-0.1.0-dev.zip` from **[Releases](../../releases)** and unzip it.
2. Back up your MHW save folder.
3. Install the prerequisites: Minecraft Java 1.21.1 + Fabric Loader + Fabric API, and (for the R/V/M keys and the F12 save)
   the ViGEmBus driver + Python + `pip install vgamepad`.
4. With MHW closed, run `powershell -ExecutionPolicy Bypass -File Install.ps1` in the unzipped folder (add `-Borderless` for full screen, recommended).
5. Put the jar from `bin\minecraft-mod\` into your Fabric profile's `mods` folder.
6. Put Steam in **offline mode** → start MHW → run `Start-Session.bat` → enter the hub offline → start Minecraft (Fabric)
   **in a window** (press F11 if Minecraft opens full screen: full-screen Minecraft is not drawn inside MHW).
7. To finish, press **F12** in Minecraft while in the hub (saves both games and quits; needs MHW in Korean, see "Play" step 5).
   Uninstall with `Uninstall.ps1`.

Details below.

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
- For the R/V/M keys below without a controller **and for the F12 save**: the [ViGEmBus](https://github.com/nefarius/ViGEmBus/releases)
  driver (no longer developed, but the last release works) and Python + `pip install vgamepad`
- For the F12 save only: MHW's text language set to **Korean** and the Korean language (with its OCR part) added in Windows
  (Settings → Time & language → Language & region). Without these, save MHW by hand (see "Play" step 5)

## Install
1. Close MHW and run in this folder:
   ```
   powershell -ExecutionPolicy Bypass -File Install.ps1
   ```
   If the MHW folder isn't found, pass `-GameDir "D:\...\Monster Hunter World"`; pick a size with `-Resolution 1920x1080`.
   **Recommended: `-Borderless`** (borderless full screen at your desktop resolution; tested at 1920x1080, 2560x1440 and 3840x2160).
   The installer copies `dinput8.dll` and `mhwbridge\`, backs up `graphics_option.ini`, then sets DirectX 11, window mode and the
   chosen size. It stops if another `dinput8.dll` mod loader (e.g. Stracker's Loader) is present.
   Start MHW once before installing (it creates `graphics_option.ini`, which the installer needs).
   If you installed with `-GameDir`, uninstall with the same `-GameDir`.
2. Minecraft side (Fabric):
   1. Run the [Fabric installer](https://fabricmc.net/use/installer/) → Client → Minecraft **1.21.1** → Install. The official
      launcher now has a **fabric-loader-1.21.1** profile.
   2. Put `bin\minecraft-mod\mhw-bridge-0.1.0-windows.jar` and [Fabric API](https://modrinth.com/mod/fabric-api) (1.21.1 build)
      into `%APPDATA%\.minecraft\mods` (create the folder if missing).
   3. Start that profile once. The launcher warns that the installation is modded: choose **Play**.

## Play
0. Put Steam in **offline mode** (Steam menu → Go Offline). Online, MHW shows the "Join a session" menu after character select.
1. Start MHW and run `Start-Session.bat` (virtual pad + F12 quit watcher; keep its window open).
2. Enter the hub or a field **offline** in MHW. (No online play / SOS: this mod changes monster HP.)
3. Start Minecraft (Fabric 1.21.1). If MHW covers the whole screen, Alt+Tab to the launcher. The "MHW Bridge" world opens automatically and the Minecraft window sits on top of MHW and takes the input.
   Minecraft must run **in a window**: if it opens full screen, press F11 (full-screen Minecraft shows up as a separate screen, not inside MHW).
4. Going to a field: M (map) → F (world map) → Q/E (low/high rank) → WASD (region) → Enter.
5. To finish press **F12** in Minecraft (or run `Save-And-Quit.bat`): saves the Minecraft world → brings MHW to the front and saves it
   through its system menu → closes MHW. The mod stays installed; next time start again from step 1. Nothing ever rolls your saves back.
   - The MHW save reads the menu on screen, so it **only works with MHW's text language set to Korean**, the Windows Korean OCR
     language installed, and the virtual pad (ViGEmBus + vgamepad). Don't touch the mouse or keyboard while it runs.
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
| F12 | Save both games and quit (while `Start-Session.bat` runs) |
| F4 | Settings: ledge guard, arrow assist, blast assist, mobs vs monsters, explosion craters, [experimental] monsters blocked by buildings (off by default). Each on/off, saved in `config\mhwbridge.properties` |

Menu mode (V/M) ends with V or M again, or with a **mouse click**. Switching windows (Alt+Tab) releases any held keys.

Everything else is normal Minecraft. Travel between areas through the MHW map with V/M.

**If M/V/R do nothing in MHW:** keep `Start-Session.bat` running (MHW only reads pads while one is connected), keep the mouse off
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
```
powershell -ExecutionPolicy Bypass -File Uninstall.ps1
```
Removes the mod files from the game folder and restores the graphics settings. Delete the jar from Minecraft's `mods` yourself.

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
- `bin\` binaries (MHW DLLs, Minecraft mod jar)
- `source\` full source (mhw-bridge: C++/MinHook, mc-bridge: Fabric mod)
- `tools\` virtual pad, session/quit scripts, core build script
- `docs\CHANGES.md` changes compared with the original

## License
MIT (see `LICENSE`). Original work © justbustin; Windows port and additions © RXTIGA90. Third-party notices in `THIRD_PARTY_NOTICES.md`.
