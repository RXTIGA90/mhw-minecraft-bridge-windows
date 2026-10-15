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
  3840x2160 borderless ~90 fps; composite + F12 save-and-quit worked in all three.
