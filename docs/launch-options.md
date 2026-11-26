# Launch options and FPS cap

Checked against `r5apex_dx12.exe`, build `R5pc_r5-310` (Season 31), on 2026-11-23. Updated 2026-11-26.

## How an option was checked

An option counts as existing if its exact name appears in the exe as a separate word, not as part of a longer name. The same test finds all 196 cvars the game saves in `settings.cfg` and `profile.cfg`, so a name it can't find almost certainly isn't in the game. A name that exists can still do nothing in retail builds or be blocked.

```
python -c "import re,sys;d=open(sys.argv[2],'rb').read();print(bool(re.search(rb'(?<![A-Za-z0-9_])'+re.escape(sys.argv[1].encode())+rb'(?![A-Za-z0-9_])',d)))" -novid "C:\Program Files (x86)\Steam\steamapps\common\Apex Legends\r5apex_dx12.exe"
```

It prints `True` or `False`. Replace `-novid` with any switch or cvar name; switches keep their leading `-`, cvars are checked without the `+`.

## My launch options

```
+fps_max 0 +lobby_max_fps 0 +m_acceleration 0 -fullscreen -novid -dev -nojoy -nodiscord +exec autoexec
```

| Option | In exe | What it does | Evidence |
|---|---|---|---|
| `+fps_max 0` | yes | No in-game frame cap | The exe stores `fps_absolute_max` with `300.0` beside it, so 300 FPS is the engine maximum whatever `fps_max` says |
| `+lobby_max_fps 0` | yes | No lobby frame cap | Default is 60, from the patch that added it ([Steam thread](https://steamcommunity.com/app/1172470/discussions/0/5653759001583358562)) |
| `+m_acceleration 0` | yes | Mouse acceleration off | Same value is saved in `settings.cfg` |
| `-fullscreen` | yes | Fullscreen | Listed with `-windowed`, `-noborder`, `-width` and `-height` in the exe |
| `-novid` | yes | Skips the startup videos | Sits next to `media/startupvids.txt` in the exe |
| `-dev` | yes | Skips the EA intro, according to guides ([DominicKlmNL](https://github.com/DominicKlmNL/apex-legends-config), [rikarsen](https://github.com/rikarsen/apex-config)) | In the exe it sits among the EA backend environment strings (`.R5dev`, `.R5prod`), so it may do more |
| `-nojoy` | yes | Controller input off *(testing)* | Sits next to `use_sony_pad_library`, `gamepad_trigger_threshold`, `-gamepad_ignore_local`, and `XInput*` calls in the exe. Currently in testing phase |
| `-nodiscord` | yes | Discord integration / Rich Presence off *(testing)* | Sits next to `discord_smallImage`, `discord_updatePresence`, and Apex's Discord App ID (`475006012840083466`) in the exe. Currently in testing phase |
| `+exec autoexec` | yes (`exec`) | Runs `cfg\autoexec.cfg` *(testing)* | `exec` looks in the game's `cfg/` folder and adds `.cfg`. See [autoexec.md](autoexec.md). Currently in testing phase |

Dropped from the old string: `-forcenovsync` and `+m_rawinput 1`. Neither is in the exe: `setting.mat_vsync_mode "0"` in `videoconfig.txt` already turns V-Sync off, and the exe calls the Windows Raw Input API directly (`RegisterRawInputDevices`, `GetRawInputData`, `GetRawInputBuffer`), so raw input is always on.

## Options guides recommend that aren't in this build

| Option | What it did in other Source-engine games | Here |
|---|---|---|
| `+m_rawinput 1` | Enable raw mouse input | Not in the exe. The exe calls Windows Raw Input directly (`RegisterRawInputDevices`, `GetRawInputData`, `GetRawInputBuffer`), so raw input is always on |
| `-preload`, `+cl_forcepreload 1` | Loaded all map resources while connecting instead of on demand ([csdb.gg](https://csdb.gg/es/command/cl-forcepreload/)) | Not in the exe |
| `+mat_queue_mode 2` | Material system threading: -1 auto, 0 single thread, 2 multithreaded ([csdb.gg](https://csdb.gg/command/mat-queue-mode/)) | Not in the exe. The DX12 renderer has its own job system (`r_enable_rendering_jobs`, `rhi_dx12_*` switches) |
| `-threads N` | Worker thread count | Not in the exe |
| `-refresh N`, `-freq N` | Force a refresh rate. The [Voltris guide](https://www.voltrisoptimizer.com/en/guides/apex-legends-config-autoexec-superglide) still recommends `-refresh 144` | Not in the exe |
| `-forcenovsync` | Force V-Sync off | Not in the exe |
| `-eac_launcher_settings SettingsDX12.json` | Selected the DX12 beta | Not needed. The game ships only `r5apex_dx12.exe`; DX11 was removed ([EA forums](https://forums.ea.com/discussions/apex-legends-technical-issues-en/is-dx11-still-available/12182263)) |

## `-high`

`-high` exists; the exe reads it at engine start, next to `-noasync` and `-tools`. It starts the game at high CPU priority. Guides disagree:

- **Against:** [Frozentweaks](https://frozentweaks.com/guides/apex-legends-fps-boost) (Nov 2026) says it causes audio and input stutter on modern schedulers. [Techbloat](https://www.techbloat.com/apex-legends-top-10-launch-options.html) says it can make Discord, mouse software and audio drivers less responsive. [PCGamingWiki](https://www.pcgamingwiki.com/wiki/Apex_Legends) reports it can trigger a false Origin update prompt (older issue).
- **For:** [Missoutpc](https://missoutpc.com/en/optimization/apex/) recommends it, without measurements.

Not worth adding. The 9800X3D has 8 cores and the game isn't short of CPU time. To test it anyway, compare frame times in CapFrameX with and without it, and listen for audio crackle.

## Other switches in the exe

None of these were tested. The purpose column is a reading of the name, not documentation.

| Switch | Purpose (from the name) | Advice |
|---|---|---|
| `-nomenuvid` | Menu background video off | Untested |
| `-noborder`, `-forceborder`, `-windowed`, `-startwindowed`, `-width`, `-height` | Window mode and size | Only for windowed play |
| `-mat_vsync`, `-mat_antialias_mode` | V-Sync and anti-aliasing override | `videoconfig.txt` already sets both |
| `-safe`, `-autoconfig` | Start with safe or auto-detected video settings | Troubleshooting only. Can replace your video settings |
| `-disableECores` | Keep the game off Intel efficiency cores | Not relevant: the 9800X3D has none |
| `-numworkerthreads`, `-numreservedcores`, `-forcedAffinity`, `-swapcores` | Thread layout | Don't touch without measurements |
| `-render_on_input_thread`, `-no_render_on_input_thread` | Allow/block render job-type bit (`0x4`) on the `IssueDeferredCalls` thread (`RVA 0x01cd3488`) | Leave both out on the 9800X3D: default already blocks it when `g_numWorkerThreads >= 5` (`RVA 0x0925f908`). `-no_render_on_input_thread` is a no-op; `-render_on_input_thread` forces it back on (troubleshooting only). If both are set, `-no_render_on_input_thread` wins |
| `-new_pso_cache`, `-no_pso_caching`, `-numPSOThreads` | DX12 shader (PSO) cache | Only for troubleshooting shader stutter |
| `-stablePowerState` | D3D12 profiling mode that fixes GPU clocks well below normal, system-wide ([Microsoft](https://learn.microsoft.com/en-us/windows/win32/api/d3d12/nf-d3d12-id3d12device-setstablepowerstate)) | Never use |
| `-dxdebug`, `-renderdoc`, `-dx12_instrumented`, `-gpuhang_dred` | Graphics debugging | Never use; slower |

## FPS cap

### Limits

- The engine stops at 300 FPS (`fps_absolute_max`, `300.0` in the exe).
- **Now:** RTSS caps Apex at 265. The profile is `C:\Program Files (x86)\RivaTuner Statistics Server\Profiles\r5apex_dx12.exe.cfg` with `Limit=265`; the global RTSS limit is 350. In-game `fps_max` is 0.

### Slide jumps, slide hops and the FPS bug

All from the [Apex Movement Wiki](https://apexmovement.tech/wiki/tech/General_Tech%3EFundamentals%3ESlide%3EFundamental_Slide_Tech_Article) unless marked:

- **Slide:** crouch while sprinting. You get a speed boost of about 150 hu/s, capped at 400 hu/s, with a 2-second cooldown.
- **Slide jump:** jump soon after starting a slide, so the slide boost and the jump add up. The wiki gives a window of about 0.24 s after the slide starts.
- **Slide hop:** bunny hopping with crouch held, so each landing starts a slide and you jump straight out of it again. It keeps your speed up and your body low, and the time spent in the air also reduces the slowdown from taking damage.
- **The bug:** at some frame rates the jump out of a slide hop doesn't happen. You stay "magnetized" to the floor in a dead slide. Bands confirmed on flat ground: **65–85, 140–160, 205–230 and 275–300 FPS**, roughly multiples of 75 ± 15. Even shallow slopes widen them.
- **A single frame is enough** ([note.com](https://note.com/legit_raven3477/n/n680dfd7764dd?hl=en), Apr 2026, one PC, measured with CapFrameX):
  - cap 75: failed every time
  - cap 150: failed often
  - caps 85 and 275: failed about 10% of the time
  - caps 202 and 207: never failed
  - The in-game and RTSS counters average over 0.5–1 s, so they hide single-frame dips; look at the 1% and 0.1% lows instead. This RTSS profile averages over 1 s (`FramerateAveragingInterval=1000`).
- **Gaps between the bands:** 90–135, 161–204 and 231–274 FPS ([note.com](https://note.com/legit_raven3477/n/n680dfd7764dd?hl=en)).

Neither the Season 29 nor the Season 30 patch notes mention a fix; Season 31's notes couldn't be read. Both sources date from before Season 31.

### What to set

| Cap | Frame time = superglide window | Slide-hop bands |
|---|---|---|
| 200 | 5.0 ms | In a gap (161–204) |
| **265 (now)** | **3.8 ms** | **In a gap (231–274), 10 FPS below the next band** |
| 270 | 3.7 ms | In a gap; the wiki's example (`+fps_max 270`), 5 FPS below the next band |
| 280 (old) | 3.6 ms | **In the 275–300 band** |
| 300 | 3.3 ms | In the 275–300 band |

- **Recommendation: 265.** The wiki advises capping 5–10 FPS below a band because slopes widen it, so 265 keeps the full margin.
- **Fallback: 200.** Use it if your 1% low in real fights falls under 231. A dip to 225 lands in the 205–230 band no matter where the cap is.
- **Superglide:** the crouch has to come one frame after the jump, in the last 0.15 s of a mantle ([setup.gg](https://www.setup.gg/game/apex-legends/how-to-super-glide/)). A lower cap makes that frame longer, so superglides are easier at 200 than at 265. Only relevant if you superglide.

**Test:**

1. Set the cap.
2. In the Firing Range, chain about 20 slide hops on flat ground and count how often you get stuck.
3. Record a few real fights in CapFrameX and check that the 1% low stays at 231 or above.

### In-game cap or RTSS

Blur Busters measured that an in-game limiter usually adds less input lag than an external one like RTSS, which can add up to one frame ([Blur Busters](https://www.blurbusters.com/gsync/gsync101-input-lag-tests-and-settings/11/)). Some forum tests disagree ([Blur Busters forum](https://forums.blurbusters.com/viewtopic.php?p=44278)), and none of these tests used Anti-Lag 2.

- **Lower lag:** `+fps_max 265` in the launch options, and `Limit=0` in the RTSS Apex profile.
- **Current setup:** RTSS `Limit=265`, and `+fps_max 0` stays.
