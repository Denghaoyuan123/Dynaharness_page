# Video request: LIBERO-Pro rollouts for the episode scrubber

**Delivered 2026-09-30.** All eight files are in `assets/video/sim/` with a
`MANIFEST.csv`. The DynaHarness clips were re-rendered from the event stores
(keyframes pixel-identical to the paper's); the frozen-policy clips are fresh
rollouts of the same seeds, so their frames match the paper's only at step 0,
and the page's frozen keyframes were re-cut from them. The specification below
is kept for reference.

The "Scrub through recorded episodes" section of the project page currently shows
six keyframes per arm. It is wired to play a recorded video of each arm instead
as soon as the files below exist; nothing else on the page needs to change.

## Files wanted

Place the files under `website/assets/video/sim/` with exactly these names:

| File | Episode | Arm | Env steps | Budget |
|---|---|---|---|---|
| `gs5_dh.mp4`   | `libero_goal_swap[5]`, seed 22, "push the plate to the front of the stove" | DynaHarness (filmed store `libero_goal_swap_5.sqlite`, sha256 e0af8458…) | 246 | 300 |
| `gs5_pi05.mp4` | same cell and seed | frozen π0.5 (bare policy) | 300 | 300 |
| `10s9_dh.mp4`  | `libero_10_swap[9]`, seed 21, "put the yellow and white mug in the microwave and close it" | DynaHarness (`libero_10_swap_9.sqlite`, 28ec1f4a…) | 520 | 520 |
| `10s9_pi05.mp4`| same | frozen π0.5 | 520 | 520 |
| `10t7_dh.mp4`  | `libero_10_task[7]`, seed 21, "put both the ketchup and the cream cheese box in the basket" | DynaHarness (`libero_10_task_7.sqlite`, 2c215990…) | 410 | 520 |
| `10t7_pi05.mp4`| same | frozen π0.5 | 520 | 520 |
| `10t6_dh.mp4`  | `libero_10_task[6]`, seed 23, "put the red mug on the plate and the chocolate pudding right of it" | DynaHarness (`libero_10_task_6.sqlite`, fbd35db8…) | 437 | 520 |
| `10t6_pi05.mp4`| same | frozen π0.5 | 520 | 520 |

These are the four filmed scenes of the paper (Table T7 and the case-study
appendix); the keyframes on the page were cut from the same episodes.

## Format

- Agent view (the same camera as the keyframes), one frame per environment
  step, starting at step 0. The page maps slider step `k` to video time
  `k / 20 s`, so the frame count must equal the episode's env steps (246, 300,
  520, …) and the rate must be 20 fps. Do not trim, speed up or add title cards.
- If a per-step frame dump is not available and the episode has to be
  re-rendered from the event store, note that in the manifest; do not re-run
  the episode.
- Encode: `ffmpeg -framerate 20 -i frame_%05d.png -c:v libx264 -preset slow
  -crf 24 -pix_fmt yuv420p -movflags +faststart -an out.mp4` (square frames,
  256 px or larger).
- Wrist view is optional (`<id>_dh_wrist.mp4`); the page does not need it.

## Where the frames were last seen

Per the earlier delivery manifest (`website_media/MANIFEST.csv`), the per-step
agentview PNGs of the four filmed DynaHarness episodes were written by
`DYNA_RECORD_DIR` on branch `rollout-capture` (63f5448) on the host `dungeon`,
under `<record dir>/<cell>-s<seed>/`; `tools/encode_rollout.sh` in the same
delivery encodes them. The bare-policy arm was run by `scripts/run_pure_vla.py`
and wrote rows only, so its frames may have to be re-rendered from the same
seed with recording enabled.

## Deliver with

A `MANIFEST.csv` row per file: source store or frame directory, seed, arm,
success, env steps, budget, fps, and whether frames were recorded live or
re-rendered.
