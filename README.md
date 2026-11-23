# apex-legends

Backup and notes for my Apex Legends setup (Steam, PC). The repo root is `%USERPROFILE%\Saved Games\Respawn`.

Last reviewed: Season 31 "Cipher", build `R5pc_r5-310`, 2026-11-23.

## Setup

| PC | |
|---|---|
| CPU | AMD Ryzen 7 9800X3D @ 5.2 GHz |
| GPU | XFX Mercury AMD Radeon RX 9070 XT OC Magnetic Air 16G |
| Motherboard | MSI X870E TOMAHAWK WIFI |
| RAM | Kingston FURY Black Beast EXPO DDR5 32 GB @ 6000 MT/s CL30 |
| PSU | Corsair HX1200i (2025) C14 |
| Cooler | be quiet! Dark Rock Pro 5 |
| Case | Fractal Design Meshify 3 XL Black TG Light Tint |
| SSD 1 | Samsung 9100 PRO 2 TB Heatsink |
| SSD 2 | Samsung 990 EVO Plus 4 TB |
| SSD 3 | Samsung 870 EVO 500 GB |
| HDD | Seagate IronWolf 4 TB 3.5" 5900 RPM |

| Peripherals | |
|---|---|
| Monitor 1 (game) | 27" Dell Alienware AW2725DF, 2560×1440 @ 360 Hz |
| Monitor 2 | 24.5" AOC G2590PX @ 144 Hz |
| Mouse | Endgame Gear XM2we @ 1000 Hz, 800 CPI |
| Mousepad | Endgame Gear MPX390 |
| Keyboard | Endgame Gear KB65HE |
| DAC/amp | FiiO K11 R2R |
| Headphones | Beyerdynamic DT 770 PRO 80 Ω |
| Microphone | HyperX SoloCast |

In game the mouse runs at sensitivity 1.18: 800 × 1.18 = 944 eDPI, about **44 cm per 360° turn**. Apex uses the same 0.022° per count as Source and CS2, so cm/360 = 360 ÷ (CPI × sensitivity × 0.022) × 2.54 ([Liquipedia](https://liquipedia.net/apexlegends/Mouse_settings)).

## Files

| Path | Holds | Written by |
|---|---|---|
| `Apex/local/videoconfig.txt` | Video settings, resolution, display mode, master volume | Me. Read-only, so the game can't overwrite it |
| `Apex/local/settings.cfg` | Key binds, mouse, Anti-Lag 2, voice chat | The game, on exit |
| `Apex/profile/profile.cfg` | FOV, HUD, gameplay, audio volumes | The game, on exit. Also synced by Steam Cloud |
| [`cfg/autoexec.cfg`](cfg/autoexec.cfg) | Everything from the two `.cfg` files above, as commands | Me. Copied into the game folder |
| `staging/` | Drafts of the three `Apex/` files, same layout | Me |
| [`docs/launch-options.md`](docs/launch-options.md) | Each launch option checked against the game, the FPS cap, slide-hop bug | Notes |
| [`docs/videoconfig.md`](docs/videoconfig.md) | Every videoconfig key: values, what it does, ideas to test | Notes |
| [`docs/autoexec.md`](docs/autoexec.md) | What the autoexec does, what's left out, how to check it works | Notes |

Git ignores everything else in `Apex/`: shader cache, store assets and the Steam Cloud marker file.

The `profile/` folder contains `steam_autocloud.vdf`, which Steam creates in every folder it syncs for a game ([Steamworks](https://partner.steamgames.com/doc/features/cloud)). So `profile.cfg` follows the Steam account, while the `local/` files stay on this PC. Steam Cloud has also caused resets: in September 2026 players reported settings back at defaults with `profile.cfg` missing ([EA forums](https://forums.ea.com/idea/apex-legends-bug-reports-en/pcsteam-sept-15-settings-reset-missing-profile-cfg-launch-options-ignored/13710671)). This repo is the backup for that case.

## Reinstall or new PC

**Reinstalling Apex:** Steam deletes the install folder, and the autoexec lives there. `Saved Games\Respawn` is outside it, and Steam stores launch options in its own config (`Steam\userdata\<id>\config\localconfig.vdf`). Copy the autoexec back, then check that the launch options are still set:

```
copy cfg\autoexec.cfg "C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\cfg\autoexec.cfg"
```

**New PC or new Windows** (Command Prompt, with the game closed):

```
mkdir "%USERPROFILE%\Saved Games\Respawn"
cd /d "%USERPROFILE%\Saved Games\Respawn"
git init -b main
git remote add origin https://github.com/martin98dev/apex-legends.git
git fetch origin
git checkout -f -t origin/main
attrib +r Apex\local\videoconfig.txt
copy cfg\autoexec.cfg "C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\cfg\autoexec.cfg"
```

`mkdir` complains if the folder already exists; that's fine. `checkout -f` replaces any config files the game already created. Then set the launch options below and the RTSS frame limit.

## Launch options

Steam → Library → right-click Apex Legends → Properties → General → Launch Options:

```
+fps_max 0 +lobby_max_fps 0 +m_rawinput 1 +m_acceleration 0 -fullscreen -novid -dev +exec autoexec
```

This differs from what's in Steam now: it adds `+m_acceleration 0` and `+exec autoexec`, and drops `-forcenovsync`, which the game doesn't have.

| Option | What it does |
|---|---|
| `+fps_max 0` | No in-game cap; RTSS caps instead (see below). The engine never goes above 300 FPS. |
| `+lobby_max_fps 0` | No lobby cap (default 60). |
| `+m_rawinput 1` | Nothing: the cvar doesn't exist. The game reads the mouse through the Windows Raw Input API on its own. Kept anyway; it's harmless. |
| `+m_acceleration 0` | Mouse acceleration off. Same value as in `settings.cfg` and the autoexec. |
| `-fullscreen` | Fullscreen. Redundant while `videoconfig.txt` exists, but covers a first launch without one. |
| `-novid` | Skips the startup videos. |
| `-dev` | Skips the EA intro, according to guides. Its full effect is unclear. |
| `+exec autoexec` | Runs `cfg/autoexec.cfg` from the game folder. |

The evidence for each line, plus `-high`, `-preload`, `+mat_queue_mode` and other options, is in [docs/launch-options.md](docs/launch-options.md).

## FPS cap

**Now:** 280 FPS in RivaTuner Statistics Server (RTSS profile `r5apex_dx12.exe.cfg`, `Limit=280`).

**Recommended: 265.** 280 is inside a known bug band. At 65–85, 140–160, 205–230 and 275–300 FPS, the jump out of a slide hop can fail and leave you stuck to the floor ([Apex Movement Wiki](https://apexmovement.tech/wiki/tech/General_Tech%3EFundamentals%3ESlide%3EFundamental_Slide_Tech_Article)). The wiki advises capping 5–10 FPS below a band.

265 only helps if FPS doesn't dip into the 205–230 band during fights. If your 1% low falls under 231, cap at 200 instead.

Two ways to set it:

- **RTSS:** change `Limit=280` to `Limit=265`. Nothing else changes.
- **In game:** use `+fps_max 265` and set RTSS's limit for Apex to 0. Blur Busters found in-game limiters usually add less input lag than RTSS ([Blur Busters](https://www.blurbusters.com/gsync/gsync101-input-lag-tests-and-settings/11/)).

What slide hops are, how to test for the bug, and the superglide trade-off: [docs/launch-options.md](docs/launch-options.md#fps-cap).

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
4. After a game session, `git diff Apex/` shows what changed. Copy any setting you changed in the menu into `cfg/autoexec.cfg` too. Otherwise the autoexec puts the old value back at the next launch.

Notes:

- While `videoconfig.txt` is read-only, the game doesn't save video settings or master volume changed in its menu.
- When the game saves `videoconfig.txt` itself, it uses LF line endings and adds a NUL byte after the final `}`. Git treats the file as binary while that byte is there. Remove it before committing.
- Git doesn't store the read-only flag. After a fresh clone, run `attrib +r Apex\local\videoconfig.txt` again.
- `.gitattributes` makes every file CRLF on checkout. The game reads both CRLF and LF.
