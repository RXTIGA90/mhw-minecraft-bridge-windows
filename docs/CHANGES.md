# Changes from upstream (justbustin/minecraft-crossover-bridge @ d1723873)

## Windows port
- Runtime files in `%TEMP%\mhwmc` instead of `/tmp/mhwmc` (both sides).
- Frame size limit raised from 1920x1200 to 2560x1600 (`bridge_protocol.h`, `FramePassthrough`).
- Normal mode wrote no camera/control block once frames were captured (passthrough never started):
  `FramePassthrough.publish` writes the pose again.
- Scene depth detection accepts render-scaled depth buffers (same aspect, 50–200% of the back buffer);
  the old rule picked shadow maps at 1080p.
- Compositor stops drawing when Minecraft's control block stops changing (Minecraft closed/hung).

## Playability
- `padinject.cpp`: Minecraft keys drive the hunter through XInput (MHW reads pads even unfocused).
  Keys R (interact = pad A), V (MHW menu mode, `MhwMenuScreen`), M (map). A stand-in pad on slot 0 only
  while Minecraft runs.
- Terrain: horizontal wall probes in both directions (one-sided MHW faces), ledge guard for drops > 8
  blocks, zone changes wait until the area has loaded, unloaded columns keep Steve's landing pad,
  the hunter only follows a grounded Steve, respawn never targets the void.
- Arrows: lower gravity in the bridge world (MHW fights happen at long range), hit feedback on screen.
- `MobBridge`: hostile mobs target nearby MHW monsters; monster bodies hurt touching mobs (not creepers);
  undead don't burn under the pinned noon sky.
- Craters: `CraterManager` + `CraterMixin` dig MHW terrain voxels on block-breaking explosions, build a
  dirt/stone bowl, send the crater list (shm `0x2A0000`) to the DLL; the compositor rebuilds MHW world
  positions from its reversed-infinite depth and lets Minecraft show through inside craters.
- `MonsterExposureMixin`: explosions next to a monster are not blocked by the terrain voxels its hitbox
  sits in.

## 4K and experimental blockers (0.1.0 update)
- `FramePassthrough`: frames larger than a frames.shm slot (2560x1600) are shrunk on the GPU
  (`glBlitFramebuffer`; world color + depth nearest, HUD layer linear) and sent at the largest size that
  fits with the same aspect (3840x2160 -> 2560x1440). The compositor samples by UV, so MHW stretches
  them back over its frame. No DLL change needed.
- `Overlay`: on Windows a window exactly covering a monitor is treated as fullscreen and its
  transparency is ignored (a stale Minecraft frame showed over borderless MHW); the overlay window is
  made one pixel shorter in that case.
- `Install.ps1 -Borderless`: borderless full screen at the desktop size (read DPI-aware); any window
  size is accepted now.
- Experimental, off by default (F4): `BlockerBridge` + DLL `apply_blockers` (shm `0x2A1000`) hold a
  large monster at its last position clear of player-built blocks (rotation kept). Live: hold and release
  work (Ancient Forest, Anjanath); a block placed over a monster pins it until removed; a charge into a
  wall was not observed yet.

## Monster shells and blocker rule (0.1.0 update 2)
- Monster shells were never found: `publish_hazards` walked only sUnit line 18 (the hunter's slinger).
  A unit census (diagnostic build, `-DMHMC_UNIT_CENSUS`) showed monster shells in line 16
  (`uShellGrassWind` and fire `uShellBase` of Anjanath, Rathalos' fireballs). Both lines are walked now.
  Live: "Rathalos shell hits Pig, 0 HP left"; fire shells also reach the TNT lighting.
- Blockers: a move is undone only when it pushes the box deeper into player-built blocks (sum of how far
  each block sits inside the box). Sliding along or backing off a wall stays free, and a turn in place
  is accepted, so a monster no longer stays pinned for good. Live (Great Jagras, ring of radius 9):
  confined inside the ring, held about half the time while it kept moving along the walls.

## Interaction and exit (0.1.0 update 3)
- `padinject.cpp`: Minecraft's pad buttons (R = interact) are merged into every connected XInput slot,
  not only slot 0. With a second (virtual) pad connected the hunter listened to that pad, so R reached
  menus but did not start an NPC conversation. Verified: R starts and advances NPC dialogue in Seliana.
- Exit script ends with `exit 0` (it reported failure after a successful save and quit).
- 1440p window re-checked after the 4K change: overlay 2560x1440, ~120 fps in Seliana, pose sync 300/300.

## Blocker safety (0.1.0 update 4)
- MHW reuses a freed monster's address for the next monster after a zone change; the Minecraft proxy
  now takes the new kind (`EntityBridge`), and blockers ignore placeholder positions (0,0,0), never undo
  a step longer than 3 m, and stay off for 5 s after a zone change. (Seen once: a small monster was put
  back to the origin right after arriving in the Ancient Forest.)
- Blocker rule now uses the distance from the box centre to the nearest built block. Live in daylight
  (Anjanath, ring R=7 H=9): held ~27% of samples, still got out. Kept experimental and off by default.

## Languages (0.1.0-dev)
- In-game texts (F4 settings, menu-mode hint, quit message, key names, death messages) moved to
  lang files: `en_us.json` and new `ko_kr.json`. One jar; Minecraft's language setting picks the text.
- READMEs: `README.md` (English) and `README.ko.md` (Korean).

## Release test fixes (0.1.0-dev, after download test)
- Loader (`dinput8.dll`) reserves 256 KB next to the exe at process start; the core frees it just before
  hooking. One 4K start had every game hook fail with MH_ERROR_MEMORY_ALLOC (MHW's heaps had filled the
  space MinHook needs within 2 GB of the code), so the hunter was never seen.
- `Start-Session.bat` / quit script: pick a real Python that has vgamepad, skipping the Microsoft Store
  "python" stub (it only opens the Store, so the virtual pad never started).
- Quit script: presses LB until the System tab is read (a press during the menu animation was lost), and
  reads the menu as white-on-black text (bright scenery made the "Save" row unreadable at 1920x1080).
- Tested from the downloaded package on one PC: 1920x1080 window ~144 fps, 2560x1440 window ~120 fps,
  3840x2160 borderless ~90 fps; composite worked in all three. (F12 save-and-quit was later found to fail
  with Minecraft started from the official launcher; see the next section.)

## Safe quit (0.1.0-dev, after independent review)
- An independent test found F12 / `Save-And-Quit.bat` closing MHW **without** saving: Minecraft from the
  official launcher runs as `javaw.exe`, which the quit script did not look for, and the virtual pad's
  start/LB presses never reached MHW (MHW was not in front, and MHW had stopped polling XInput once
  Minecraft and its stand-in pad were gone). The script then answered "quit without saving?" with Yes.
- `Exit-Both.ps1`: finds `java.exe` and `javaw.exe`; brings the MHW window to the front before any pad
  press; reconnects the virtual pad and checks in `mhw-bridge.log` that MHW polls it again.
- MHW is closed **only when the save file was updated by the menu save**. Otherwise (MHW text language not
  Korean, no Windows Korean OCR, no vgamepad, save file not found, menu not recognised, any error) MHW is
  left open and `exit.log` says to save by hand.
- READMEs: Minecraft must run in a window (F11); F12 save needs MHW in Korean, the Windows Korean OCR
  language and ViGEmBus + vgamepad.
- Live (3840x2160 borderless, Seliana): official-launcher Minecraft + F12, and `Save-And-Quit.bat` with
  Minecraft closed, both logged `saved through the menu (save file updated)` and closed MHW. The
  left-open path (save not possible) was checked in code only.

## Review follow-up (0.1.0-dev, second round)
- Quit script: menu presses now go through the bridge's own pad channel (bridge.shm 0x80, merged by the DLL
  into every connected XInput slot - the path Minecraft's M/R keys use). Presses through the virtual pad
  were often ignored, and back-to-back Down presses got lost. The virtual pad stays connected so MHW keeps
  polling. The save question is read several times, filtered and unfiltered. Tested from the package:
  F12 saved through the menu and closed both games at 1920x1080, 2560x1440 and 3840x2160.
- M (map) worked in every repeat here (4K and 1440p, with one and with two virtual pads, and with MHW in
  keyboard mode); the review's failure could not be reproduced. README gained a short troubleshooting note.
- `Install.ps1`: stops with a clear message when `graphics_option.ini` is missing (MHW never started).
- `Uninstall.ps1`: when `dinput8.dll` differs from the package, keeps `mhwbridge-backup` so the install is
  still recognised.
- DLLs rebuilt with `tools/build-core.sh` (no work-in-progress internal names). `fabric.mod.json` lists
  RXTIGA90 as author. THIRD_PARTY_NOTICES mentions the statically linked LLVM/mingw-w64 runtime.
- README (both): Fabric installer steps, launcher "modded" warning, Alt+Tab when MHW covers the screen,
  run MHW once before installing, same `-GameDir` for uninstall, FPS numbers with the render distance used,
  Korean README "소개" heading.
- Borderless full screen (`Install.ps1 -Borderless`) tested at desktop resolutions 1920x1080 (~144 fps) and
  2560x1440 (~128 fps) besides 3840x2160 (~90 fps); README now recommends it. TNT crater checked again in
  Seliana: 93 blocks dug, MHW ground cut out on screen, Steve walked down into the hole (3-4 blocks deep).

## No virtual pad needed (0.1.0-dev)
- Found on a "plain user" run (no ViGEmBus/Python pad): MHW stops reading XInput once no pad is connected,
  so Minecraft's R/V/M did nothing. The DLL now posts a device-change message to MHW while Minecraft runs and
  MHW isn't polling; MHW then finds the DLL's stand-in pad on slot 0 (`padinject_watchdog`). Verified with no
  virtual pad: M opened the MHW map.
- F12: MHW is saved first (while Minecraft still runs, so the stand-in pad is there) and Minecraft quits last.
  Verified with no virtual pad: saved through the menu, MHW closed, Minecraft saved and closed. The save
  question match tolerates one misread syllable. ViGEmBus/vgamepad are now optional (README).

## One-click setup (0.1.0-dev)
- `Setup.bat` (tools/Setup.ps1): MHW side (`Install.ps1 -Borderless`), latest stable Fabric Loader for 1.21.1
  (version file from meta.fabricmc.net, like the Fabric installer), Fabric API for 1.21.1 (Modrinth, SHA-512
  checked), this mod, first-run options (window, render distance 12, Windows language) in
  `%APPDATA%\.minecraft\mhw-bridge`, and a launcher profile "MHW Bridge" using that folder (launcher_profiles.json
  backed up; other profiles untouched). A folder outside `.minecraft` looked empty to the Microsoft Store
  launcher (no mods loaded), hence the subfolder.
- `Play.bat` starts MHW and the F12 watcher; `Uninstall.bat` also removes the launcher profile.
- DLL: when Minecraft attaches while borderless MHW is minimized (it minimizes when the launcher takes the
  focus), MHW's window is restored without taking the focus. Quit script nudges MHW to re-scan pads first.
- Tested on the developer PC with the official (Microsoft Store) launcher: Setup -> Play.bat -> hub -> profile
  "MHW Bridge" -> composited -> F12 saved MHW through the menu and closed both games.

## Icon
- Project icon (`assets\`): desktop shortcut "MHW Bridge Play" made by Setup.bat (removed by Uninstall.bat), the launcher profile icon, the mod's icon in Minecraft, and the README logo.
