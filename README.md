# apex-legends

Backup of my Apex Legends (Steam, PC) config. The repo root is `%USERPROFILE%\Saved Games\Respawn`.

Last reviewed: Season 31 "Cipher", build `R5pc_r5-310` (2026-11-23).

Setup: Radeon RX 9070 XT, Ryzen 7 9800X3D, 32 GB RAM, 2560×1440 at 360 Hz (Alienware AW2725DF).

## Files

| File | Holds | Who writes it |
|---|---|---|
| `Apex/local/videoconfig.txt` | Video settings, display mode, resolution, **master volume** | Read-only, so the game can't overwrite it |
| `Apex/local/settings.cfg` | Key binds, mouse sensitivity, Anti-Lag 2 / Reflex, voice chat | The game, on exit |
| `Apex/profile/profile.cfg` | FOV, HUD, gameplay options, audio volumes, controller | The game, on exit |
| `staging/` | Drafts of the three files above, same layout as `Apex/` | Me |
| [`docs/videoconfig.md`](docs/videoconfig.md) | Every videoconfig key: values, what it does, ideas to test | Research notes |
| [`docs/autoexec.md`](docs/autoexec.md) | Which autoexec commands still exist, for a possible future autoexec | Research notes |

Git ignores everything else in `Apex/`: shader cache, store assets and the Steam cloud file.

## Steam launch options

Steam → Library → right-click Apex Legends → Properties → General → Launch Options:

```
+fps_max 0 +lobby_max_fps 0 +m_rawinput 1 -forcenovsync -fullscreen -novid -dev
```

"In exe" means the name appears in `r5apex_dx12.exe` (build r5-310). An option the exe doesn't contain does nothing.

| Option | In exe | Effect |
|---|---|---|
| `+fps_max 0` | yes | No in-game frame cap, so RivaTuner sets it (see below). The engine never goes above 300: `fps_absolute_max` is `300.0` in the exe. |
| `+lobby_max_fps 0` | yes | No lobby cap (default 60). |
| `+m_rawinput 1` | **no** | Does nothing. The cvar doesn't exist; the other mouse cvars (`m_acceleration`, `mouse_sensitivity`) do. |
| `-forcenovsync` | **no** | Does nothing. `setting.mat_vsync_mode "0"` turns V-Sync off. |
| `-fullscreen` | yes | Fullscreen. Redundant with `setting.fullscreen "1"`. |
| `-novid` | yes | Skips the startup videos (stored next to `media/startupvids.txt` in the exe). |
| `-dev` | yes | Skips the EA intro according to guides. In the exe it sits among the EA backend environment strings (`.R5dev`, `.R5prod`), so its full effect is unclear. |

Cleaned up, with the same effect: `+fps_max 0 +lobby_max_fps 0 -novid -dev`

Options that guides recommend but this build doesn't have: `-preload`, `+cl_forcepreload`, `+mat_queue_mode`. `-high` exists, but guides disagree on whether high CPU priority helps or causes stutter.

## FPS cap

I cap at **280 FPS with RivaTuner Statistics Server (RTSS)**. The in-game cap is off (`+fps_max 0`), so RTSS is the only limiter.

Worth testing:

- **In-game cap instead of RTSS:** `+fps_max 280` in place of `+fps_max 0`, with RTSS off. Blur Busters' tests found in-game limiters usually add less input lag than RTSS, which can add up to 1 frame (3.6 ms at 280 FPS). Nobody has tested this together with Anti-Lag 2.
- **Slide-jumps at 280:** one 2026 guide claims slide-jumps clip at 275–300 FPS, along with 65–85, 140–160 and 205–230 FPS. 280 falls in that range. The claim is unverified; check it in the Firing Range.

## Changing configs

1. Close the game first. It rewrites `settings.cfg` and `profile.cfg` on exit, so edits made while it runs are lost.
2. Edit the copy in `staging/`, then compare it with the live file:
   ```
   git diff --no-index Apex/local/videoconfig.txt staging/local/videoconfig.txt
   ```
3. Copy it over. `videoconfig.txt` has to be unlocked first and locked again afterwards:
   ```
   attrib -r Apex\local\videoconfig.txt
   copy staging\local\videoconfig.txt Apex\local\videoconfig.txt
   attrib +r Apex\local\videoconfig.txt
   ```
4. Run `git diff` after a game session to see what the game changed itself.

Notes:

- While `videoconfig.txt` is read-only, the game doesn't save video settings or master volume changed in its menu.
- When the game saves `videoconfig.txt` itself, it uses LF line endings and adds a NUL byte after the final `}`. Git treats the file as binary while that byte is there. Remove it before committing.
- Git doesn't store the read-only flag. After a fresh clone, run `attrib +r Apex\local\videoconfig.txt` again.
- `.gitattributes` makes every file CRLF on checkout. The game reads both CRLF and LF.

Sources: [Blur Busters: in-game vs external FPS limiters](https://www.blurbusters.com/gsync/gsync101-input-lag-tests-and-settings/11/), [PC settings guide with the slide-jump FPS ranges (note.com)](https://note.com/x4mberglow/n/n9303b2c96eca?hl=en).
