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

Git ignores everything else in `Apex/`: shader cache, store assets and the Steam cloud file.

## Steam launch options

Steam → Library → right-click Apex Legends → Properties → General → Launch Options:

```
+fps_max 0 +lobby_max_fps 0 +m_rawinput 1 -forcenovsync -fullscreen -novid -dev
```

| Option | Effect |
|---|---|
| `+fps_max 0` | Removes the in-game frame cap. The engine still stops at 300 FPS (community-reported). |
| `+lobby_max_fps 0` | Removes the lobby frame cap (default 60). |
| `+m_rawinput 1` | Raw mouse input, bypassing Windows pointer settings. |
| `-forcenovsync` | Forces V-Sync off. `setting.mat_vsync_mode "0"` already does this. |
| `-fullscreen` | Exclusive fullscreen. `setting.fullscreen "1"` already does this. |
| `-novid` | Skips the intro video. |
| `-dev` | Skips the EA intro. |

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
- Git doesn't store the read-only flag. After a fresh clone, run `attrib +r Apex\local\videoconfig.txt` again.
- `.gitattributes` keeps every file CRLF on disk, matching what the game writes.
