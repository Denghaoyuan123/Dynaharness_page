# DynaHarness project website

## Preview locally

```sh
cd website
python3 -m http.server 8000
# open http://localhost:8000
```

Opening `index.html` directly from the file system also works, except that the
KaTeX equations and web fonts need network access.

## Deploy

Copy the `website/` folder to any static host (GitHub Pages, Netlify, an
institutional server). For GitHub Pages: push the folder as the repository root
of a `gh-pages` branch, or point Pages at `/website`.

The page names the authors, affiliations and the code repository
(github.com/Denghaoyuan123/DynaHarness); the PDF under `assets/` is still the
anonymized submission.

## Layout

```
index.html                 the page
assets/css/style.css       design tokens and components
assets/js/data.js          every number shown on the page, copied from the paper
assets/js/hero.js          the interactive workcell in the hero (four tasks, two modes, swap perturbation)
assets/js/charts.js        SVG chart library (bars, dumbbell, heatmap, radar, ...)
assets/js/main.js          section logic (latching demo, architecture, scrubber, evidence-store explorer, results, baselines)
assets/js/baselines.js     baseline diagnostic evidence (cases, markers, numbers) from Baseline/ run records
assets/js/bl_special.js    the system-specific interactives of the baseline section
assets/data/gs5_s22_events.json  the full event store of the filmed goal_swap[5] seed-22 episode, compacted
assets/img/logo.png        figures/log.png (transparent background)
assets/img/fig/            paper figures rendered from the PDFs at 220 dpi
assets/img/cases/          case-study frames from paper/figures/cases
assets/img/baselines/      frames from the baselines' records (ENPIRE single-frame rollouts, PhyAgentOS final frames)
assets/video/              web-encoded videos (H.264, faststart, no audio): real-robot trials, the rendered evidence scroll
assets/video/sim/          LIBERO-Pro rollouts for the episode scrubber (not delivered yet; see VIDEO_REQUEST.md)
assets/video/baselines/    the baselines' own rollouts, re-encoded (Zetta pairs, Harness VLA, ENPIRE, CaP-Agent0, PhyAgentOS)
assets/DynaHarness_paper.pdf
```

`website_media.zip` is the experiment agent's delivery (already unpacked into the
assets above); it is not needed by the page and can be left out of a deployment.

## Updating numbers

Edit `assets/js/data.js` only after the paper has changed. Each block names the
table or figure it copies. Charts and tables re-render from that file.

## Adding videos

Encode with

```sh
ffmpeg -i in.mp4 -c:v libx264 -preset slow -crf 26 -pix_fmt yuv420p -movflags +faststart -an out.mp4
```

and place the file under `assets/video/`. The real-robot videos are plain
`<video>` tags in the "Real-world experiments" section of `index.html`: ring
placement (external + wrist, run circle_20260924T022938Z), cup stacking
(cup_20260928T212411), bread into the toaster (bread_ui_20260929T161045Z) and
the drawer refusal clip; their captions quote the runs' own decision traces.
The episode scrubber plays `assets/video/sim/<case>_dh.mp4` and
`<case>_pi05.mp4` when they exist (one frame per environment step, 20 fps) and
shows the keyframes otherwise; `VIDEO_REQUEST.md` specifies them. Baseline
videos are referenced from `assets/js/baselines.js` by path, with their markers
in video seconds.

## Evidence-store explorer

`assets/data/gs5_s22_events.json` is built from the exported event store
(`sim_gs5_s22_dh_events.jsonl`) by keeping every row's seq, wall-clock offset,
type and running environment step, the full payload of decision rows, and a
compact payload for the housekeeping rows (context snapshots, masks,
observations, lease continuations). It loads over HTTP; opening `index.html`
from the file system shows a note instead.
