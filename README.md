<h1 align="center">synthstrip gpu</h1>

<p align="center">
  Skull-strips a T2-weighted image with FreeSurfer SynthStrip (mri_synthstrip, deep learning, GPU or CPU) and outputs the brain-extracted image and a binary brain mask.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.875"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.875-blue.svg" alt="Run on brainlife.io"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Skull-strips a T2-weighted image with FreeSurfer SynthStrip (mri_synthstrip, deep learning, GPU or CPU) and outputs the brain-extracted image and a binary brain mask.**

Runs mri_synthstrip from the freesurfer/synthstrip 1.7 container on a single T2-weighted NIfTI. By default it runs on a GPU (the -gpu container with Singularity --nv); set use_gpu to false to run on CPU. Optionally excludes CSF from the mask (no_csf). Use it as a robust, contrast-agnostic brain extraction step before registration or segmentation.

| | |
|---|---|
| **Inputs** | `neuro/anat/t2w` |
| **Outputs** | `neuro/anat/t2w` · `neuro/mask` |
| **Container** | `freesurfer/synthstrip:1.7-gpu` |
| **Resources** | 1 CPUs · 2 h walltime |
| **Run it** | [doi.org/10.25663/brainlife.app.875](https://doi.org/10.25663/brainlife.app.875) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `t2` | `neuro/anat/t2w` | yes | T2-weighted anatomical image (NIfTI) to skull-strip. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `t2_brain` | `neuro/anat/t2w` | Stripped T2w image (brain) |
| `mask` | `neuro/mask` | Brain mask |

<details>
<summary><b>Parameters</b> (2)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `no_csf` | boolean | `false` | If you would like to compute the immediate boundary of the brain, excluding surrounding CSF, use the no_csf flag. |
| `use_gpu` | boolean | `true` | You can disable GPU usage and fall back to the CPU instead; this may cause processing times to increase anywhere from slightly to considerably. |

</details>

<!-- brainlife-app:end -->

## Versions on brainlife.io

This repository hosts several Apps, each built from its own branch.

| App | Branch | Run it | Status |
|---|---|---|---|
| SynthStrip (GPU) T2w | [`main`](https://github.com/gamorosino/app-synthstrip-gpu/tree/main) | [doi:10.25663/brainlife.app.875](https://doi.org/10.25663/brainlife.app.875) | current |
| SynthStrip (GPU) T1w | [`main`](https://github.com/gamorosino/app-synthstrip-gpu/tree/main) | [doi:10.25663/brainlife.app.862](https://doi.org/10.25663/brainlife.app.862) | available |

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.875](https://doi.org/10.25663/brainlife.app.875), choose `neuro/anat/t2w` from your project, set the parameters and press **Execute**. The results are saved to your project as `neuro/anat/t2w`, `neuro/mask`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `freesurfer/synthstrip:1.7-gpu`), [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository and check out the App's branch (`git checkout main`).
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "t2": "/path/to/t2/t2",
       "no_csf": false,
       "use_gpu": true
   }
   ```

3. Run `./main`.

## Troubleshooting

- **Datatype not supported**: Provide exactly one anatomical input (t1 or t2).

## Container & Implementation Notes

* **CPU image:** `freesurfer/synthstrip:1.7`
* **GPU image:** `freesurfer/synthstrip:1.7-gpu`
* **Runner:** `singularity exec -e docker://<image> ...`
---

## Troubleshooting

* **“Datatype not supported”** — Ensure **exactly one** of `t1` or `t2` is non-null.
* **Empty or partial masks** — Try rerunning **without** `no_csf` first; if contrast is atypical, verify modality is correctly assigned.
* **GPU not detected** — Confirm a GPU node, drivers, and Singularity GPU passthrough; try CPU mode as a control.

---

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- SynthStrip (GPU) T2w. brainlife.io App. [doi:10.25663/brainlife.app.875](https://doi.org/10.25663/brainlife.app.875)
- Hoopes A, Mora JS, Dalca AV, Fischl B, Hoffmann M. SynthStrip: skull-stripping for any brain image. NeuroImage 260:119474 (2022).
- Kelley W, Ngo N, Dalca AV, Fischl B, Zöllei L, Hoffmann M. Boosting skull-stripping performance for pediatric brain images. 2024 IEEE ISBI, pp. 1-5 (2024).
- Hoopes, A., Mora, J. S., Dalca, A. V., Fischl, B., & Hoffmann, M. (2022). **SynthStrip: skull-stripping for any brain image**. *NeuroImage*, 260, 119474.
- Kelley, W., Ngo, N., Dalca, A. V., Fischl, B., Zöllei, L., & Hoffmann, M. (2024, May). **Boosting skull-stripping performance for pediatric brain images**. In *2024 IEEE ISBI* (pp. 1–5). IEEE.
- Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. **brainlife.io: a decentralized and open-source cloud platform to support neuroscience research.** Nat Methods 21, 809–813 (2024). ---
- Fischl. FreeSurfer. *NeuroImage* 62:774-781 (2012). [doi:10.1016/j.neuroimage.2012.01.021](https://doi.org/10.1016/j.neuroimage.2012.01.021)
- Hoopes et al. SynthStrip: skull-stripping for any brain image. *NeuroImage* 260:119474 (2022). [doi:10.1016/j.neuroimage.2022.119474](https://doi.org/10.1016/j.neuroimage.2022.119474)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

**Gabriele Amorosino**  
(email:

---
