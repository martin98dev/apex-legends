# videoconfig.txt reference

Researched 2026-11-23 for Season 31 "Cipher", build `R5pc_r5-310`.
Live file: `Apex/local/videoconfig.txt`. Draft copy: `staging/local/videoconfig.txt`.

## Sources and how far to trust them

| Source | What it gives | Trust |
|---|---|---|
| Game exe `r5apex_dx12.exe` (r5-310) | The exact list of keys the game reads, the video menu's control names and option labels, which cvars exist | High for names. Gives no ranges and no descriptions |
| [note.com videoconfig guide](https://note.com/ebi_suuuuuu/n/nd51daeac3755?hl=en) (Feb 2026, revised Nov 2026) | Values for each menu tier, a description of each key | Medium. The author judges by eye ("I did not feel any effect"). Has typos (`mat_vsync_mod`, `10000000`), and its `nowindowborder` text contradicts itself |
| [NVIDIA Titanfall 2 tweaking guide](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/) (2016) | Tiers and measured FPS costs on the same engine family | Low to medium. Old engine and old hardware |
| GitHub configs, 2019–2026 (list at the bottom) | What other people use | Low. Rarely explained, and most predate configversion 10 |
| Official patch notes, Seasons 29–30 | Engine changes | High. The Season 31 notes returned 403 and weren't read |

Reddit blocks the research tool and YouTube videos can't be watched, so neither was used.

## Keys the game reads

The exe contains exactly **46** `setting.*` keys. Values come from the [note.com guide](https://note.com/ebi_suuuuuu/n/nd51daeac3755?hl=en) unless another source is linked. "Mine" is my current value.

### Shadows and lighting

| Key | Mine | Values | What it does |
|---|---|---|---|
| `csm_enabled` | 0 | 0 / 1 | Sun shadows (cascaded shadow maps). The menu has no Off option and always writes 1. The guide suspects 0 is a placebo now |
| `csm_coverage` | 0 | 0 / 1 Low / 2 High | Menu: Sun Shadow Coverage. The guide says 0 probably has no effect |
| `csm_cascade_res` | 16 | 0 / 512 Low / 1024 High | Resolution of the sun shadow maps. 512 is the lowest the menu writes. [ApexDt](https://github.com/ApexDt/Apex-Legends/blob/master/videoconfig.txt) and [Voltris](https://www.voltrisoptimizer.com/en/guides/apex-legends-config-autoexec-superglide) use 16 as the "minimum possible" |
| `shadow_enable` | 0 | 0 / 1 | Spot shadows (sun and spot, per the guide) |
| `shadow_depth_dimen_min` | 0 | 0 / 128 / 256 / 512 | Spot shadow resolution. Menu: Spot Shadow Detail |
| `shadow_depth_upres_factor_max` | 0 | 0 / 2 / 3 | Spot shadow upscaling. Part of Spot Shadow Detail |
| `shadow_maxdynamic` | 0 | 0 / 4 | Menu: Dynamic Spot Shadows (moving lights). Turning them off gained 13 FPS in Titanfall 2 ([NVIDIA](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)) |
| `new_shadow_settings` | 0 | 0 / 1 | Unknown. The game writes 1 whenever it saves |
| `ssao_quality` | 0 | 0 off / 1 Low / 2 High / 3 Very High / 4 Ultra | Menu: Ambient Occlusion Quality. Rebuilt in Season 29 with specular occlusion ([patch notes](https://www.ea.com/games/apex-legends/apex-legends/news/overclocked-patch-notes)) |
| `ssao_enabled` | — | 0 / 1 | Pre-Season 29 key. The game removes it when it upgrades the file |
| `ssao_downsample` | — | 0–3 | Pre-Season 29 key. The game removes it when it upgrades the file |
| `volumetric_lighting` | 0 | 0 / 1 | Light shafts through dust |
| `volumetric_fog` | 0 | 0 / 1 | Light shafts through fog |

### Model detail, clutter and effects

| Key | Mine | Values | What it does |
|---|---|---|---|
| `r_lod_switch_scale` | **2.0** | 0.6 min / 0.8 / 1.0 Medium / 2.0 High | Menu: Model Detail. The distance multiplier for swapping models to lower-detail versions. The guide says 0.6 is the current minimum; old configs used 0.2–0.4. In Titanfall 2, each step above 1 cost about 1–3 FPS ([NVIDIA](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)) |
| `fadeDistScale` | 1.0 | 0 and up. 0.6 / 0.8 / 1.0 default | Distance at which small props (pebbles, grass, trash, decorations) fade out. Lower means they vanish closer to you |
| `map_detail_level` | 1 | 1 Low / 2 High | Menu: Map Detail. The [DominicKlmNL config](https://github.com/DominicKlmNL/apex-legends-config) (Sep 2026) uses 0, below the menu minimum |
| `set_dress_level` | — | Unknown. A [2024 Steam config](https://steamcommunity.com/app/1172470/discussions/0/4766584846449919679/) used 0 | Sets the cvar `mat_set_dress_level`. "Set dressing" means decorative level props. The game doesn't write this key on PC today, but the exe still reads it |
| `r_decals` | 0 | 0 / 256 | Impact marks on the world. Titanfall 2 allowed raising it to 1024 ([NVIDIA](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)) |
| `r_createmodeldecals` | 0 | 0 / 1 | Impact marks on characters |
| `cl_particle_fallback_base` | 3 | 0 off / 3 on | Threshold at which effects get simplified. Menu: Effects Detail |
| `cl_particle_fallback_multiplier` | 2 | 1 High / 1.75 Medium / 2 Low | How hard effects get simplified. 2019–2023 configs ([ApexDt](https://github.com/ApexDt/Apex-Legends/blob/master/videoconfig.txt), [Zazzop](https://github.com/Zazzop/Apex-Legends/blob/master/videoconfig.txt), [240hz](https://github.com/240hz/ApexConfigs/blob/master/videoconfig.txt)) used ±999999; untested on current builds |
| `particle_cpu_level` | 0 | 0 / 1 / 2 | CPU-side detail for particle effects |
| `cl_ragdoll_maxcount` | 0 | 0 Low / 4 Medium / 8 High | Number of bodies shown at once. Titanfall 2 used the same 0/4/8 ([NVIDIA](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)) |
| `cl_ragdoll_self_collision` | 0 | 0 / 1 | Physics collisions between body parts |
| `cl_gib_allow` | 0 | 0 / 1 | Gore and debris |

### Textures

| Key | Mine | Values | What it does |
|---|---|---|---|
| `stream_memory` | 3000000 | 0 None / 160000 Very Low / 300000 Low / 600000 Medium / 1000000 High / 2000000 Very High / 3000000 top | Menu: Texture Streaming Budget. The exe has an "Ultra" label, so 3000000 is probably Ultra. No FPS cost with 16 GB of VRAM |
| `mat_picmip` | 0 | 0 high / 1 / 2. The guide gives −10 to 8 as the working range | Texture resolution. Old configs used 4 with `stream_memory 0` |
| `mat_forceaniso` | 16 | 1 / 2 / 4 / 8 / 16 | Anisotropic filtering level |
| `mat_mip_linear` | 1 | 0 bilinear / 1 trilinear | Mipmap blending. Together with `mat_forceaniso` this is the menu's Texture Filtering (Bilinear, Trilinear, 2×–16×). 16× vs bilinear cost 0.7 FPS in Titanfall 2 ([NVIDIA](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)) |
| `dynamic_streaming_budget` | 1 | 0 / 1 (the guide lists up to 4) | DX12. 1 lets texture loading yield to rendering, which avoids stutter |

### Display

| Key | Mine | Values | What it does |
|---|---|---|---|
| `mat_antialias_mode` | 12 | 0 none / 12 TSAA | Anti-aliasing. TSAA cost 5 FPS on 2016 hardware ([NVIDIA](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)). The exe also has a `-mat_antialias_mode` launch switch |
| `mat_vsync_mode` | 0 | 0 off / 1 double / 2 triple / 3 adaptive / 4 adaptive ½ | V-Sync. The exe's menu labels also include "Dynamic" |
| `mat_backbuffer_count` | 1 | 0 / 1 / 2 | Number of queued back buffers. The guide recommends 0 with V-Sync off; configs are split between 0 and 1 |
| `fullscreen` | 1 | 0 / 1 | 1 = fullscreen. The exe's menu labels are Fullscreen, Windowed, Borderless Window |
| `nowindowborder` | 0 | 0 / 1 | With `fullscreen 0`, 1 gives borderless windowed. In Season 31 the game wrote 1 even with `fullscreen 1` |
| `defaultres` / `defaultresheight` | 2560 / 1440 | Pixels | Render resolution. Stretched resolutions also need `+mat_letterbox_aspect_min 1.0`. The exe's aspect labels are 16:9, 16:10, 21:9, Legacy, Tall |
| `last_display_width` / `last_display_height` | 2560 / 1440 | Pixels | Resolution at the last launch |
| `dvs_enable` | 0 | 0 / 1 | Menu: Adaptive Resolution |
| `dvs_gpuframetime_min` / `_max` | 15000 / 16500 | Microseconds | Frame-time targets for adaptive resolution. Unused while it's off |
| `gamma` | 1.0 | 0.25–1.75. 1.0 = 50% | Brightness, about 0.015 per 1%. Respawn advised the default after reducing bloom ([Season 29 midseason notes](https://www.ea.com/games/apex-legends/apex-legends/news/overclocked-midseason-patch-notes)) |

### Other

| Key | Mine | Values | What it does |
|---|---|---|---|
| `sound_volume` | 0.6 | 0–1 | Master volume. It's stored in this file, so it doesn't save while the file is read-only |
| `configversion` | 10 | — | File format version. Season 29 moved it from 9 to 10 |
| `cl_fovScale` | — | 1.0–1.55 (1.55 = 110) | In the exe's key table, but probably for consoles. On PC, FOV is saved in `profile.cfg` ([1.55 = 110](https://mouse-sensitivity.com/forums/topic/6892-apex-110-fov-multiplier-155-vs-1571429)) |

## Keys from old configs that do nothing

None of these names appear anywhere in the r5-310 exe, checked with the test in [launch-options.md](launch-options.md#how-an-option-was-checked). The game ignores them:

`mat_depthfeather_enable`, `modeldecals_forceAllowed`, `dvs_supersample_enable`, `ao_enable`, `bnao_enabled`, `dx_version_check_timestamp`, `mat_disable_bloom`, `mat_specular`, `mat_bumpmap`

## What this repo has observed

- **Version upgrade.** The file was locked at configversion 9 from 2025-11-24 onwards. Season 29 (2026-05-05) rebuilt ambient occlusion. Unlocked on 2026-11-23, the game upgraded the file to 10 and replaced `ssao_enabled` / `ssao_downsample` with `ssao_quality`.
- **Values the game overwrites.** On the same save the game set `csm_enabled 1`, `csm_coverage 1`, `csm_cascade_res 512`, `new_shadow_settings 1` and `nowindowborder 1`. The menu can't go below these, so lower values have to be set in the file and kept read-only.
- **File format when the game saves.** LF line endings, plus a NUL byte after the final `}`.
- **Keys the game no longer writes.** It didn't write `set_dress_level` or `cl_fovScale`, although the exe reads both.

## Ideas to test, ranked

Change one thing at a time in `staging/`, copy it over, lock the file, test, then keep it or revert it with `git checkout`.

| # | Change | Why | How to check |
|---|---|---|---|
| 1 | Settle `csm_enabled` vs `csm_cascade_res`: try 0+16, 1+16 and 1+512 | Shows which key actually removes sun shadows. If `csm_enabled 0` is a placebo, `csm_cascade_res 16` (a 16×16 shadow map) is what does the work | Firing Range, your own shadow and a rock's shadow in sunlight |
| 2 | `fadeDistScale` 1.0 → 0.75 → 0.5 | Small clutter fades sooner: less to draw, less visual noise. [ApexDt](https://github.com/ApexDt/Apex-Legends/blob/master/videoconfig.txt) uses 0.75 | Grassy and junk-filled areas. Check that props used as cover don't disappear |
| 3 | `r_lod_switch_scale` 2.0 → 1.0 → 0.6 | You run High model detail while everything else is at minimum | Frame times in busy fights. Outside fights you sit at the FPS cap, so differences won't show |
| 4 | Add `"setting.set_dress_level" "0"` | The only key the game reads that you don't set. It may remove decorative props, or it may be console-only | Same cluttered spot, before and after screenshots |
| 5 | `map_detail_level` 1 → 0 | Below the menu minimum; one current config uses it. Effect unknown | Screenshots |
| 6 | `mat_backbuffer_count` 1 → 0 | A possible latency change with V-Sync off | Frame times / latency in PresentMon or CapFrameX |
| 7 | `mat_antialias_mode` 12 → 0 | Sharper image, but more shimmer on distant edges. Personal preference | Look at distant enemies and fences |

Leave as they are:
- **Textures** (`stream_memory`, `mat_picmip`, aniso 16×): no FPS cost at 16 GB of VRAM. Lowering them is purely a matter of taste.
- **`new_shadow_settings`, `dynamic_streaming_budget`:** no evidence either way.
- **±999999 particle values:** from 2019–2023 configs and untested on current builds. They might hide effects you need to see.

## Mapping menu tiers to values yourself

1. Run `attrib -r Apex\local\videoconfig.txt`.
2. In the game, change one Video option to one tier and apply.
3. Quit, then run `git diff Apex/local/videoconfig.txt` (add `--text` if the NUL byte makes Git call it binary).
4. Write down which values that tier wrote.
5. Revert with `git checkout -- Apex/local/videoconfig.txt`, then repeat for the next tier.
6. Run `attrib +r Apex\local\videoconfig.txt` when done.

## Re-checking keys after a game update

This lists every key the installed exe knows. On 2026-11-23 it printed 46.

```
python -c "import re,sys;d=open(sys.argv[1],'rb').read();k=sorted(set(m.decode() for m in re.findall(rb'setting\.[A-Za-z0-9_]+',d)));print(len(k));print('\n'.join(k))" "C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\r5apex_dx12.exe"
```

A new key means a new video setting, which is the time to update this file. The exe also has a menu control named `SwchGlobalIlluminationQuality`, but there's no matching key or cvar and no patch note mentions it. It's probably unreleased or console-only. If a Global Illumination option shows up in the Video menu, check this list again.

## Sources

- [note.com: The Most Detailed Guide to APEX LEGENDS videoconfig.txt Settings (2026)](https://note.com/ebi_suuuuuu/n/nd51daeac3755?hl=en)
- [NVIDIA: Titanfall 2 Graphics, Performance & Tweaking Guide](https://www.nvidia.com/en-us/geforce/news/titanfall-2-graphics-performance-and-tweaking-guide/)
- [Overclocked patch notes (Season 29)](https://www.ea.com/games/apex-legends/apex-legends/news/overclocked-patch-notes)
- [Overclocked midseason patch notes](https://www.ea.com/games/apex-legends/apex-legends/news/overclocked-midseason-patch-notes)
- [Marked patch notes (Season 30)](https://www.ea.com/games/apex-legends/news/marked-patch-notes)
- [Steam: FPS drop fix after update, 2024 config with set_dress_level](https://steamcommunity.com/app/1172470/discussions/0/4766584846449919679/)
- [Voltris guide, csm_cascade_res 16](https://www.voltrisoptimizer.com/en/guides/apex-legends-config-autoexec-superglide)
- [mouse-sensitivity.com: cl_fovScale 1.55 = 110](https://mouse-sensitivity.com/forums/topic/6892-apex-110-fov-multiplier-155-vs-1571429)
- Configs: [DominicKlmNL](https://github.com/DominicKlmNL/apex-legends-config) (Sep 2026, configversion 10), [kretz1xD](https://github.com/kretz1xD/Apex-Legends-Tweaks) (Aug 2026, configversion 10), [ApexDt](https://github.com/ApexDt/Apex-Legends/blob/master/videoconfig.txt) (2020), [Zazzop](https://github.com/Zazzop/Apex-Legends/blob/master/videoconfig.txt) (2023), [V3nilla](https://github.com/V3nilla/Apex-Legends-Config-And-Tweaks/blob/main/videoconfig.txt) (2024), [rikarsen](https://github.com/rikarsen/apex-config) (2022), [240hz](https://github.com/240hz/ApexConfigs/blob/master/videoconfig.txt) (2019)
