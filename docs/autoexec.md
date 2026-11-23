# autoexec.cfg

[`cfg/autoexec.cfg`](../cfg/autoexec.cfg) holds my settings as console commands, so a fresh install gets them on the first launch.

Version 1, generated 2026-11-23 from `Apex/local/settings.cfg` and `Apex/profile/profile.cfg` (Season 31, build `R5pc_r5-310`). **Not yet tested in game.**

## Install

1. Copy `cfg\autoexec.cfg` to `C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\cfg\autoexec.cfg`.
2. Add `+exec autoexec` to the Steam launch options (see [launch-options.md](launch-options.md)).

The game doesn't run an autoexec by itself: the word `autoexec` isn't in the exe. The `exec` command is, next to the strings `cfg/%s` and `.cfg`, so `exec autoexec` loads `autoexec.cfg` from the game's `cfg` folder. The [DominicKlmNL repo](https://github.com/DominicKlmNL/apex-legends-config) (Sep 2026) uses the same setup.

## What's in it

- **111 settings** from `settings.cfg` and `profile.cfg`, in sections: mouse, latency, field of view and camera, gameplay, HUD, visual, audio, voice and chat, subtitles, other.
- **All 68 keyboard and mouse binds**, grouped by purpose. They come after `unbindall_ignoreGamepad`, which clears keyboard and mouse binds first, so no leftover default binds survive on a fresh install.
- Lines are copied unchanged, including the trailing `0` or `1` on bind lines. That number is part of the format the game writes (`%s "%s" "%s" %i` in the exe); what it means is unknown.

## What's left out, and why

| Left out | Why |
|---|---|
| Video settings | They live in `videoconfig.txt`, which also holds values the menu can't set, like `csm_cascade_res 16`. Copy that file instead (see [videoconfig.md](videoconfig.md)). The [Voltris guide](https://www.voltrisoptimizer.com/en/guides/apex-legends-config-autoexec-superglide) says Respawn blocked most graphics commands in config files in 2021 |
| Master volume | It's `setting.sound_volume` in `videoconfig.txt` |
| Controller settings and binds (56 settings, 31 binds) | No controller in this setup |
| Game-managed state: EULA, intro and first-time flags, cached player level, telemetry dates, chat-block timers | Not settings. Forcing them could confuse the game |
| Values tied to this PC or account: `name`, audio and microphone devices, HDR screenshot folder | Differ per PC |
| Engine leftovers with no menu option: `lookspring`, `lookstrafe`, `func_break_max_pieces`, spectator camera speed | Defaults |
| FPS cap | Set by the launch options or RTSS |

## Things to know

- **It runs at every launch.** A setting you change only in the menu goes back to the autoexec value at the next launch. After a session, `git diff Apex/` shows which values changed; copy those lines into the autoexec.
- **Load order is unknown.** If the game loads `settings.cfg` and `profile.cfg` after the autoexec, their values win. Today both hold the same values, so it doesn't matter yet.
- **Binds may be filtered.** The exe has a `bind_exec_sanitize` command, which suggests binds coming from config files get cleaned up. Two binds contain `;`: E (`+use; +use_long`) and F9 (`miles_reboot; miles_stop_all`). Check that both still work.
- **Existing isn't the same as allowed.** Every name in the file exists in the exe, but that doesn't prove a config may set it. The game saves all of these values itself, so they're the most likely to work.

## First launch check

1. Install it as above.
2. To confirm it runs, temporarily change one harmless line only in the installed copy, for example `sound_volume_music_lobby "0"`. A silent lobby means it ran. Change it back afterwards.
3. Check E (interact, hold for the wheel) and F9 (restart audio).
4. Quit, then run `git diff Apex/`. Nothing should change except what you changed on purpose. If binds are missing, delete the `unbindall_ignoreGamepad` line and try again.

## Candidates for later

These exist in the exe and aren't saved by the menu. Each is untested, so add one at a time and keep it only if it visibly does something.

| Command | What it does | Note |
|---|---|---|
| `sssss_enable "0"` | Turns off screen-space subsurface scattering, the skin-lighting effect on characters | Possible small FPS gain; changes how characters look |
| `stream_drop_unused "1"` | Frees unused textures sooner | Unknown effect on stutter |
| `telemetry_client_enable "0"`, `telemetry_client_sendInterval "0"` | Client telemetry | Privacy only; no evidence of a performance effect |

Leave alone: `miles_occlusion "0"`. Older configs use it to stop walls from muffling sound, which changes what you hear in fights.

## Commands from community autoexecs that don't exist

The [DominicKlmNL autoexec](https://github.com/DominicKlmNL/apex-legends-config) marks every line "(Works)". Older configs ([rikarsen](https://github.com/rikarsen/apex-config), [kretz1xD](https://github.com/kretz1xD/Apex-Legends-Tweaks), [missoutpc](https://missoutpc.com/en/optimization/apex/)) use similar lines. None of these names are in the r5-310 exe, so they do nothing:

`mat_queue_mode`, `m_rawinput`, `m_filter`, `m_customaccel`, `snd_mixahead`, `snd_headphone_pan_exponent`, `shadow_always_update`, `r_drawmodeldecals`, `r_decalstaticprops`, `r_forcecheapwater`, `r_waterforcereflectentities`, `cl_ejectbrass`, `hud_setting_enableModWheel`, `player_setting_enable_heartbeat_sounds`, `mat_depthfeather_enable`, `mat_bumpmap`, `mat_specular`, `voice_forcemicrecord`, `mat_letterbox_aspect_threshold`, `cl_interp`, `cl_interp_ratio`, `host_writeconfig`, `mat_disable_bloom`, `cl_forcepreload`

To check a name after a game update, use the command in [launch-options.md](launch-options.md#how-an-option-was-checked).
