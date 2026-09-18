### Table 1 — Selected images (USC-SIPI, Miscellaneous volume)

| SIPI id | Name | Native format | Content character |
|---|---|---|---|
| 4.2.03 | Mandrill / Baboon | 24 bpp colour | Dense high-frequency fur texture |
| 4.2.05 | Airplane (F-16) | 24 bpp colour | Strong clean edges, flat sky |
| 4.2.07 | Peppers | 24 bpp colour | Large smooth shaded regions |
| 5.2.10 | Stream and bridge | 8 bpp gray | Mixed texture and structure |

All converted to 8-bit grayscale, 512 × 512, before any processing.


### Table 2 — PSNR (dB)

**mandrill**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 23.35 | 20.85 | 19.70 |
| Bilinear (1st) | 23.17 | 20.89 | 19.80 |
| Biquadratic (2nd) | **23.87** | **21.11** | **19.94** |
| Bicubic (3rd) | 23.81 | 21.08 | 19.94 |
| Biquintic (5th) | 23.79 | 21.07 | 19.94 |
| Lanczos-4 | 23.80 | 21.07 | 19.94 |

**airplane**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 28.87 | 24.70 | 21.73 |
| Bilinear (1st) | 30.11 | 25.49 | 22.22 |
| Biquadratic (2nd) | 31.68 | 26.38 | 22.73 |
| Bicubic (3rd) | 31.77 | 26.42 | 22.73 |
| Biquintic (5th) | **31.91** | **26.49** | **22.76** |
| Lanczos-4 | 31.90 | 26.48 | 22.75 |

**peppers**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 29.86 | 26.01 | 22.85 |
| Bilinear (1st) | 30.98 | 27.21 | 23.88 |
| Biquadratic (2nd) | 31.96 | 28.05 | 24.71 |
| Bicubic (3rd) | 31.99 | 28.09 | 24.75 |
| Biquintic (5th) | **32.04** | **28.15** | **24.81** |
| Lanczos-4 | 32.04 | 28.14 | 24.81 |

**stream**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 25.68 | 22.27 | 20.28 |
| Bilinear (1st) | 25.85 | 22.67 | 20.58 |
| Biquadratic (2nd) | **26.84** | 23.14 | **20.92** |
| Bicubic (3rd) | 26.80 | 23.14 | 20.91 |
| Biquintic (5th) | 26.83 | **23.16** | 20.91 |
| Lanczos-4 | 26.83 | 23.16 | 20.91 |

**Mean over all four images**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 26.94 | 23.46 | 21.14 |
| Bilinear (1st) | 27.53 | 24.07 | 21.62 |
| Biquadratic (2nd) | 28.59 | 24.67 | 22.08 |
| Bicubic (3rd) | 28.59 | 24.68 | 22.08 |
| Biquintic (5th) | **28.64** | **24.72** | **22.11** |
| Lanczos-4 | 28.64 | 24.71 | 22.10 |

Bold = best per column. Higher is better.


### Table 3 — SSIM

**mandrill**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 0.7546 | 0.4839 | 0.2974 |
| Bilinear (1st) | 0.7006 | 0.4512 | 0.2978 |
| Biquadratic (2nd) | **0.7676** | 0.4940 | **0.3154** |
| Bicubic (3rd) | 0.7640 | 0.4921 | 0.3143 |
| Biquintic (5th) | 0.7667 | 0.4943 | 0.3150 |
| Lanczos-4 | 0.7669 | **0.4945** | 0.3150 |

**airplane**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 0.9278 | 0.8042 | 0.6517 |
| Bilinear (1st) | 0.9327 | 0.8191 | 0.6843 |
| Biquadratic (2nd) | 0.9525 | **0.8450** | **0.6990** |
| Bicubic (3rd) | 0.9524 | 0.8443 | 0.6974 |
| Biquintic (5th) | **0.9527** | 0.8438 | 0.6958 |
| Lanczos-4 | 0.9527 | 0.8438 | 0.6947 |

**peppers**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 0.8845 | 0.7736 | 0.6133 |
| Bilinear (1st) | 0.8861 | 0.8032 | 0.6906 |
| Biquadratic (2nd) | **0.9010** | **0.8226** | **0.7105** |
| Bicubic (3rd) | 0.8998 | 0.8220 | 0.7097 |
| Biquintic (5th) | 0.8990 | 0.8211 | 0.7077 |
| Lanczos-4 | 0.8992 | 0.8208 | 0.7068 |

**stream**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 0.8069 | 0.5550 | 0.3329 |
| Bilinear (1st) | 0.7804 | 0.5414 | 0.3457 |
| Biquadratic (2nd) | 0.8353 | 0.5887 | **0.3683** |
| Bicubic (3rd) | 0.8338 | 0.5877 | 0.3663 |
| Biquintic (5th) | 0.8363 | 0.5901 | 0.3664 |
| Lanczos-4 | **0.8364** | **0.5904** | 0.3664 |

**Mean over all four images**

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 0.8435 | 0.6542 | 0.4738 |
| Bilinear (1st) | 0.8249 | 0.6537 | 0.5046 |
| Biquadratic (2nd) | **0.8641** | **0.6876** | **0.5233** |
| Bicubic (3rd) | 0.8625 | 0.6865 | 0.5219 |
| Biquintic (5th) | 0.8637 | 0.6873 | 0.5212 |
| Lanczos-4 | 0.8638 | 0.6874 | 0.5207 |

Bold = best per column. Higher is better.


### Table 4 — Runtime (ms, mean of 10 runs after warm-up)

| Method | 2× | 4× | 8× |
|---|---|---|---|
| Nearest (0th) | 1.108 ± 0.026 | 1.028 ± 0.045 | 1.023 ± 0.040 |
| Bilinear (1st) | 2.665 ± 0.267 | 2.429 ± 0.087 | 2.474 ± 0.077 |
| Biquadratic (2nd) | 5.221 ± 0.347 | 4.583 ± 0.583 | 4.231 ± 0.143 |
| Bicubic (3rd) | 7.621 ± 0.330 | 6.755 ± 0.193 | 6.724 ± 0.213 |
| Biquintic (5th) | 16.002 ± 0.532 | 14.646 ± 0.476 | 14.360 ± 0.480 |
| Lanczos-4 | 0.215 ± 0.005 | 0.167 ± 0.007 | 0.139 ± 0.007 |

Times the resize call only. Orders 0–5 are scikit-image (float64 spline filtering); Lanczos-4 is OpenCV (SIMD C++). The Lanczos figure is therefore **not** comparable to the spline family as a measure of kernel cost.


### Table 5 — Quality vs cost summary (averaged over all images and factors)

| Method | Mean PSNR (dB) | Mean SSIM | Mean runtime (ms) |
|---|---|---|---|
| Nearest (0th) | 23.85 | 0.6572 | 1.053 |
| Bilinear (1st) | 24.40 | 0.6611 | 2.523 |
| Biquadratic (2nd) | 25.11 | 0.6917 | 4.678 |
| Bicubic (3rd) | 25.12 | 0.6903 | 7.033 |
| Biquintic (5th) | 25.16 | 0.6907 | 15.002 |
| Lanczos-4 | 25.15 | 0.6906 | 0.174 |