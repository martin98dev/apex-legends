# autoexec.cfg notes

Status: **no autoexec in use.** These notes are for building one later. Checked against build `R5pc_r5-310` (Season 31) on 2026-11-23.

## How it would be loaded

- The exe has no `autoexec` string, so the game doesn't run one by itself. The `exec` command does exist.
- The usual setup is a file at `C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\cfg\autoexec.cfg`, loaded with the launch option `+exec autoexec`. That comes from the DominicKlmNL repo; I haven't tested it.
- A cvar existing in the exe doesn't mean a config file may set it. Respawn locked many cvars in 2021 (per the Voltris guide), and DominicKlmNL removed 20 lines in Sep 2026 as "deprecated, server-side blocked". The only real test is to run the line and check its effect in game.
- Most useful settings are already saved by the menu in `profile.cfg` / `settings.cfg` (audio, HUD, FOV, sprint view shake, netgraph) or in `videoconfig.txt`. Putting them in an autoexec only duplicates them.

## Commands from community autoexecs, checked against the exe

From the [DominicKlmNL autoexec](https://github.com/DominicKlmNL/apex-legends-config) (Sep 2026, every line marked "(Works)") and older configs (rikarsen, kretz1xD, missoutpc).

**Not in the exe, so they do nothing:**

`mat_queue_mode`, `m_rawinput`, `m_filter`, `m_customaccel`, `snd_mixahead`, `snd_headphone_pan_exponent`, `shadow_always_update`, `r_drawmodeldecals`, `r_decalstaticprops`, `r_forcecheapwater`, `r_waterforcereflectentities`, `cl_ejectbrass`, `hud_setting_enableModWheel`, `player_setting_enable_heartbeat_sounds`, `mat_depthfeather_enable`, `mat_bumpmap`, `mat_specular`, `voice_forcemicrecord`, `mat_letterbox_aspect_threshold`, `cl_interp`, `cl_interp_ratio`, `host_writeconfig`, `mat_disable_bloom`, `cl_forcepreload`

**In the exe (so they *may* work):**

| Command | Note |
|---|---|
| `fps_max`, `lobby_max_fps` | Same as the launch options |
| `m_acceleration` | Already in `settings.cfg` (0) |
| `sound_num_speakers`, `miles_channels` | 2 = stereo, 6 = 5.1, 8 = 7.1. `settings.cfg` has 2 and 0 |
| `sound_volume_*`, `sound_without_focus` | Already in `profile.cfg` |
| `noise_filter_scale` | Film grain. Already 0 in `profile.cfg` |
| `fov_disableAbilityScaling`, `sprint_view_shake_style`, `net_netGraph2` | Already in `profile.cfg` |
| `particle_cpu_level`, `cl_ragdoll_maxcount`, `r_createmodeldecals` | Already in `videoconfig.txt` |
| `sssss_enable` | Screen-space subsurface scattering on characters (skin rendering). Not saved anywhere yet; a candidate to test |
| `g_ragdoll_fadespeed`, `g_ragdoll_lvfadespeed` | Ragdoll fade speed. Irrelevant with ragdolls at 0 |
| `r_deferred_decals` | World decals. `r_decals 0` in videoconfig already covers them |
| `stream_drop_unused` | Frees unused textures sooner. Unknown effect on stutter |
| `telemetry_client_enable`, `telemetry_client_sendInterval`, `pin_opt_in`, `pin_plat_id` | Telemetry and data surveys. `pin_opt_in` is 1 in `profile.cfg` |
| `projectile_prediction`, `projectile_predictionErrorCorrectTime` | Client-side projectile prediction. Probably server-controlled; don't touch |
| `cl_matchmaking_timeout` | Matchmaking timeout |
| `building_cubemaps`, `mat_letterbox_aspect_goal`, `mat_letterbox_aspect_min` | Only needed for stretched resolutions |
| `miles_occlusion` | Older configs set 0 to turn off sound occlusion through walls. That changes what you hear in fights, so it's a gameplay change; leave it alone |
| `cl_showfps`, `mat_postprocess_enable`, `r_drawparticles`, `rate` | Exist. Respawn probably blocks the visual ones from config files |

## If an autoexec is made

Keep it to lines that exist in the exe and aren't already saved by the menu. Test each line in the Firing Range and delete any that has no visible effect. To check whether a name still exists after a game update (prints `True` or `False`):

```
python -c "import sys;d=open(sys.argv[2],'rb').read();print(b'\0'+sys.argv[1].encode()+b'\0' in d)" sssss_enable "C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\r5apex_dx12.exe"
```
