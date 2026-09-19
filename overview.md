# Assignment 2 — Interpolation-Based Super-Resolution

> **How to use this file:** Part A is the assignment exactly as handed out (nothing omitted).
> Part B is an implementation spec derived from Part A — it is *not* part of the original
> handout, but it fixes every ambiguity so the code can be written without guessing.
> If Part A and Part B ever disagree, **Part A wins**.

---

# PART A — The assignment, as given

## Prelude: Nearest-Neighbour Interpolation (Unmarked)

Nearest-neighbour interpolation is the simplest super-resolution method. For an enlargement
factor of *s*, each pixel value is copied into an *s × s* block. No new pixel intensity is
estimated.

Worked example from the handout (2× nearest-neighbour interpolation):

Low-resolution image (2 × 2):

| 40  | 120 |
|-----|-----|
| 180 | 220 |

Super-resolved image (4 × 4):

| 40  | 40  | 120 | 120 |
|-----|-----|-----|-----|
| 40  | 40  | 120 | 120 |
| 180 | 180 | 220 | 220 |
| 180 | 180 | 220 | 220 |

For 2× super-resolution, each pixel becomes a 2 × 2 constant block. For 4× and 8×
super-resolution, each pixel becomes a 4 × 4 and 8 × 8 constant block, respectively.

Nearest-neighbour interpolation is a **zeroth-order** technique: it assumes that the intensity
remains constant over the region represented by a pixel. It is computationally very fast, but
produces blocky or staircase artefacts.

**This prelude is unmarked.**

## Task

Take any four images from the dataset provided in Assignment 1.

1. Convert every selected image to grayscale. The original images are 512 × 512.
2. Downsample every image to:
   - 64 × 64
   - 128 × 128
   - 256 × 256
3. Super-resolve each downsampled image back to 512 × 512.
4. Perform interpolation using zeroth-order, first-order, second-order, third-order, etc.
   methods.
5. For every super-resolved image, compute PSNR and SSIM with respect to the original
   grayscale 512 × 512 image.
6. Report the runtime of each interpolation method.

> *Note on the source PDF:* the OCR ran items 1 and 2 together ("The original images are 512 ×
> 512. 2. Downsample every image to:"). The numbering above is the intended reading — "the
> originals are 512 × 512" belongs to step 1.

The super-resolution settings are:

| Input size | Output size | Enlargement factor |
|------------|-------------|--------------------|
| 64 × 64    | 512 × 512   | 8×                 |
| 128 × 128  | 512 × 512   | 4×                 |
| 256 × 256  | 512 × 512   | 2×                 |

Use the **same four images** and the **same downsampling procedure** throughout the study.

## Report

The report must include:

- the four selected original grayscale images;
- the three downsampled versions of every image;
- super-resolved results obtained using the interpolation methods;
- PSNR, SSIM, and runtime results;
- clearly labelled tables for comparison;
- a brief discussion of the results.

In the discussion, comment on:

- how quality changes from 2× to 4× and 8× super-resolution;
- the visual differences among the interpolation methods;
- blocking, blur, ringing, overshoot, or staircase artefacts, wherever present;
- the relation between reconstruction quality and runtime;
- whether the observations depend on image content such as edges, smooth regions, or texture.

## Deliverables

Submit:

- a report in **PDF** format;
- **Python code or a Python notebook**.

The code or notebook must be demonstrated to the TAs.

## Dataset

USC-SIPI image database, **Miscellaneous** volume:
<https://sipi.usc.edu/database/database.php?volume=misc>

Take any 4 images.

---

# PART B — Implementation spec (derived, not in the handout)

## B.1 Image selection

Originals must be **512 × 512** (the handout says so). The misc volume mixes 256×256, 512×512
and 1024×1024, so pick only from the 512 × 512 entries. Files are `.tiff`.

Recommended four, chosen to span different content types (this matters for the last discussion
point):

| File           | Common name        | Type               | Content character                  |
|----------------|--------------------|--------------------|------------------------------------|
| `4.2.03.tiff`  | Mandrill / Baboon  | 512×512, 24bpp     | Dense high-frequency fur texture   |
| `4.2.05.tiff`  | Airplane (F-16)    | 512×512, 24bpp     | Strong clean edges + flat sky      |
| `4.2.07.tiff`  | Peppers            | 512×512, 24bpp     | Large smooth shaded regions        |
| `5.2.10.tiff`  | Stream and bridge  | 512×512, 8bpp gray | Mixed texture + structure          |

Alternatives if any of these are awkward: `5.2.09` (aerial), `7.1.03` (tank, 8bpp gray),
`boat.512.tiff`. `4.2.04` is the classic Lenna image — many courses and venues now discourage
it, so prefer the ones above.

Download each from the misc volume page linked in Part A (click through to the image, then save
the `.tiff`). Store them under `data/originals/`. Do not commit a resized copy — the pipeline
must start from the true 512 × 512 file.

## B.2 Pipeline (exact order of operations)

```
original .tiff (512×512, colour or gray)
    └─> grayscale  (only if colour)                 -> I_ref  (512×512, uint8)   [GROUND TRUTH]
            ├─> downsample to 64×64    -> LR_8x
            ├─> downsample to 128×128  -> LR_4x
            └─> downsample to 256×256  -> LR_2x
                    └─> for each LR, for each interpolation method:
                            upsample back to 512×512 -> SR
                            PSNR(SR, I_ref), SSIM(SR, I_ref), runtime
```

Hard rules:

- **Grayscale first, then downsample.** Never downsample the colour image and grayscale later —
  results would not be comparable across images.
- `I_ref` is the *only* reference for every metric. Never compare SR against the LR image.
- One single downsampling function used everywhere (same interpolation, same anti-aliasing),
  for all four images and all three sizes. State which one you used in the report.
- All images kept as `uint8` in [0, 255] when metrics are computed; use `data_range=255`
  explicitly so PSNR/SSIM are not silently wrong.
- 512/64, 512/128, 512/256 are exact powers of two, so no fractional-pixel alignment mess —
  but still pass explicit output shapes rather than float scale factors.

Grayscale conversion: use ITU-R BT.601 luma (`cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)` or
PIL `.convert("L")`) and use the *same* one everywhere. Images that are already 8bpp gray are
passed through untouched.

Downsampling: use area-averaging (`cv2.INTER_AREA`, equivalently `PIL.Image.BOX`/`LANCZOS`), which
is the standard low-pass-then-decimate choice and avoids aliasing that would unfairly punish
every SR method. Mention in the report that plain decimation was rejected for this reason.

## B.3 Interpolation methods ("zeroth-order, first-order, second-order, third-order, etc.")

Implement at least these six. The first four map one-to-one onto the orders named in the task;
the last two are the "etc." and make the quality-vs-runtime discussion much better.

| # | Order         | Method              | OpenCV flag           | scikit-image / scipy `order=` |
|---|---------------|---------------------|-----------------------|-------------------------------|
| 1 | Zeroth        | Nearest neighbour   | `INTER_NEAREST`       | `order=0`                     |
| 2 | First         | Bilinear            | `INTER_LINEAR`        | `order=1`                     |
| 3 | Second        | Biquadratic         | — (not in OpenCV)     | `order=2`                     |
| 4 | Third         | Bicubic             | `INTER_CUBIC`         | `order=3`                     |
| 5 | Fourth/Fifth  | Biquartic/Biquintic | — (not in OpenCV)     | `order=4` / `order=5`         |
| 6 | Windowed sinc | Lanczos-4           | `INTER_LANCZOS4`      | —                             |

Notes that will save debugging time:

- OpenCV has **no** second-order or fourth/fifth-order resize. Get orders 0–5 from
  `skimage.transform.resize(..., order=k, anti_aliasing=False, preserve_range=True)` or
  `scipy.ndimage.zoom(..., order=k, grid_mode=True, mode='grid-constant')`.
- Mixing libraries across methods makes the runtime column meaningless — **use one library for
  the whole order-0..5 family** (skimage or scipy), and if you also include OpenCV Lanczos, say
  so in the table caption and treat its timing as a separate note.
- `anti_aliasing` must be **off** for upsampling. It is a downsampling-only concern and skimage
  will warn/alter results otherwise.
- Orders ≥ 2 can overshoot outside [0, 255] (ringing/halo near edges). Clip to [0, 255] and cast
  back to `uint8` *before* computing metrics — and mention the overshoot in the discussion, since
  the handout explicitly asks about ringing and overshoot.
- Optional extra credit-ish: `cv2.INTER_AREA` as an upsampler degenerates to nearest — don't use
  it for SR.

## B.4 Metrics

```python
from skimage.metrics import peak_signal_noise_ratio as psnr
from skimage.metrics import structural_similarity as ssim

p = psnr(I_ref, SR, data_range=255)
s = ssim(I_ref, SR, data_range=255)   # single-channel, default 7×7 window
```

- PSNR in dB, report to 2 decimals. SSIM in [0, 1], report to 4 decimals.
- Both computed on the full 512 × 512 frame, uint8 vs uint8.
- Sanity checks: 2× should beat 4× should beat 8× for every method on every image; nearest should
  be the worst (or near-worst) at every scale. If not, the pipeline has a bug.

## B.5 Runtime measurement

- Time **only** the interpolation/resize call — not file I/O, not metric computation, not plotting.
- Use `time.perf_counter()`.
- One call is far too short and noisy. Run each (image, scale, method) resize **N = 10** times
  after **1 warm-up call**, and report the **mean** (optionally ± std) in **milliseconds**.
- Report per-method runtime at each enlargement factor; also give a mean over the four images,
  since runtime is essentially content-independent (worth stating as an observation).
- Note in the report that timings reflect the specific library implementation (vectorised C under
  NumPy), not the intrinsic arithmetic cost of the kernel — a fair point for the quality-vs-runtime
  discussion.

## B.6 Experiment matrix

4 images × 3 enlargement factors (2×, 4×, 8×) × 6 methods = **72 super-resolved images** and
**72 metric rows**. Persist everything:

```
results/
  originals/        {name}_gray.png                       (4)
  downsampled/      {name}_64.png / _128.png / _256.png   (12)
  sr/               {name}_{factor}x_{method}.png         (72)
  figures/          ...                                   (for the report)
  metrics.csv       image,factor,input_size,method,order,psnr,ssim,runtime_ms_mean,runtime_ms_std
```

`metrics.csv` is the single source of truth — every table in the report is generated from it, not
typed by hand.

## B.7 Tables required in the report

All "clearly labelled" with captions, units and bold best-per-column:

1. **Table 1 — Dataset:** the four images, SIPI file id, common name, native bit depth.
2. **Table 2 — PSNR (dB):** rows = method, columns = 2× / 4× / 8×, one sub-table (or one block)
   per image, **plus** a mean-over-four-images summary table.
3. **Table 3 — SSIM:** same layout as Table 2.
4. **Table 4 — Runtime (ms):** rows = method, columns = 2× / 4× / 8×, mean ± std over repeats.
5. **Table 5 — Quality vs cost summary:** per method, mean PSNR, mean SSIM, mean runtime, so the
   trade-off is readable at a glance.

## B.8 Figures required in the report

1. The four original grayscale 512 × 512 images, labelled.
2. For each image: the three downsampled versions (64/128/256), displayed at their true relative
   scale so the size difference is visible.
3. For each image: a **method × factor grid** of SR outputs (6 × 3), all displayed at 512 × 512.
4. **Zoomed crops** (e.g. 64 × 64 patch blown up) on a hard region — the F-16 wing edge, mandrill
   fur, a pepper's smooth highlight — comparing nearest / bilinear / bicubic / Lanczos at 8×.
   This is what actually shows blocking, blur, ringing and staircase artefacts; the full-frame
   views mostly don't.
5. Optional but strong: PSNR-vs-runtime scatter (one point per method, per factor), and
   a bar chart of PSNR by method grouped by factor.
6. Optional: absolute-difference/error maps `|SR − I_ref|` at 8×, which localise where each method
   fails (edges for bilinear blur, edge halos for bicubic/Lanczos ringing).

Use `cmap='gray'`, `vmin=0, vmax=255`, `interpolation='nearest'` in every `imshow` — otherwise
matplotlib silently re-interpolates the display and the artefacts you are trying to show are
matplotlib's, not yours. This is the single most common way this assignment goes wrong.

## B.9 Discussion — points that must be covered

The handout lists five; each needs to be backed by a number from `metrics.csv` or a visible crop,
not asserted:

1. **2× → 4× → 8×.** Quality falls monotonically; quantify the PSNR/SSIM drop per step. Explain
   why: at 8× only 1/64 of the pixels survive downsampling, so 63/64 of the output is invented,
   and the high-frequency content destroyed by the low-pass stage is unrecoverable by any
   interpolation — these methods estimate, they do not restore.
2. **Visual differences among methods.** Nearest = hard blocks/staircase edges; bilinear = smooth
   but blurred, loses texture; bicubic = sharper than bilinear, mild overshoot; Lanczos = sharpest,
   most ringing. Higher order ≠ monotonically better past order 3 — say so if your numbers show it.
3. **Artefacts.** Point at specific crops: blocking/staircase (nearest, worst at 8×), blur
   (bilinear), ringing/overshoot halos near strong edges (bicubic, Lanczos — check pixel values
   exceeding the local range before clipping).
4. **Quality vs runtime.** Nearest is fastest and worst. Bilinear buys a large PSNR gain for a
   small cost. Bicubic → Lanczos costs noticeably more for diminishing returns. Conclude where the
   knee of the curve is, i.e. which method is the sensible default.
5. **Content dependence.** Absolute PSNR is much higher on smooth images (Peppers) than on texture
   (Mandrill) for *every* method, and the *gap between* methods is widest on edge-heavy images and
   narrowest on smooth ones — on a flat region every interpolator agrees. Mandrill at 8× is near
   hopeless for all of them: the fur frequencies are gone. Support with the per-image tables.

Also worth one line each: PSNR and SSIM do not always rank methods identically (SSIM punishes blur
more than PSNR does); and PSNR is computed on clipped uint8 output, so clipping itself slightly
changes the score for the overshooting methods.

## B.10 Environment

```
numpy
opencv-python
scikit-image
scipy
matplotlib
pandas
tifffile        # or Pillow, for reading the SIPI .tiff files
```

Some SIPI TIFFs are unusual enough to trip naive readers; if `cv2.imread` returns `None`, fall
back to `tifffile.imread` or `PIL.Image.open`. Always assert the loaded array is `(512, 512)`
(or `(512, 512, 3)`) before proceeding.

## B.11 Suggested code structure

Notebook is fine (it must be demoed to TAs), but keep the logic in functions so it is
re-runnable top-to-bottom:

```
config           # image list, sizes, methods, N_REPEATS, paths
load_gray(path)             -> (512,512) uint8
downsample(img, size)       -> (size,size) uint8      # one method, used everywhere
upsample(img, method, out)  -> (512,512) uint8        # dispatch table of methods
time_upsample(...)          -> (SR, mean_ms, std_ms)
evaluate(SR, ref)           -> (psnr, ssim)
run_all()                   -> writes every PNG + metrics.csv
make_tables(df) / make_figures(df)
```

A single `METHODS` dict — `{"Nearest (order 0)": fn, "Bilinear (order 1)": fn, ...}` — driving
both the runs and the table row order keeps naming consistent everywhere and avoids the classic
mismatch between what the table says and what was actually run.

Set a fixed ordering (images, then factors 2/4/8, then methods by order) so tables are stable
across reruns. There is no randomness in this pipeline, so results should be bit-identical on
re-run — if they are not, something is non-deterministic and should be found.

## B.12 Definition of done

- [ ] 4 originals downloaded, all verified 512 × 512
- [ ] Grayscale conversion done once, saved
- [ ] 12 downsampled images saved
- [ ] 72 SR images saved
- [ ] `metrics.csv` with 72 rows, no NaNs
- [ ] Sanity checks pass (2× > 4× > 8×; nearest worst)
- [ ] 5 tables generated from the CSV
- [ ] All required figures, including zoomed crops, with `interpolation='nearest'`
- [ ] Discussion covering all five handout bullets, each backed by numbers
- [ ] Report exported to PDF
- [ ] Notebook runs clean top-to-bottom from a fresh kernel