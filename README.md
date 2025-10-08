
# app-synthstrip-gpu
**An app to run SynthStrip: Skull-Stripping for Any Brain Image**

An automated, containerized workflow that performs robust brain extraction (a.k.a. skull-stripping) on **either a T1-weighted or T2-weighted** anatomical MRI using the Deep Learning FreeSurfer’s tool **SynthStrip** (Hoopes A. et al, 2022)(Kelley W. et al, 2024). Optional flags let you (i) suppress CSF in the mask and (ii) run on GPU nodes for speed.

---

## Author

**Gabriele Amorosino**  
(email: [gabriele.amorosino@utexas.edu](mailto:gabriele.amorosino@utexas.edu))

---

## Description

This app wraps `mri_synthstrip` to produce a brain-extracted image and its binary mask from a single anatomical input.  
- **Inputs:** one of `t1` *or* `t2` (NIfTI `.nii.gz`)  
- **Modes:** CPU (default) or GPU (NVIDIA)  
- **Optional:** `--no-csf` to exclude CSF in the final mask

Under the hood, the app pulls the appropriate FreeSurfer SynthStrip container and runs:

```

mri_synthstrip -i <input> -o <brain.nii.gz> -m <mask.nii.gz> [--no-csf]

````

Execution is portable via **Singularity**; the provided `main` script includes PBS headers for use on HPC clusters.

---

## Requirements

- [Singularity](https://sylabs.io/guides/latest/user-guide/)
- (Optional, GPU) NVIDIA GPUs + drivers available on the host and Singularity GPU passthrough enabled (e.g., `--nv` or appropriate environment on your site)

---

## Inputs

Provide **exactly one** anatomical input in `config.json`:

- `t1` — path to T1w image (`.nii.gz`) **or**
- `t2` — path to T2w image (`.nii.gz`)

Optional switches:

- `no_csf` *(boolean; default: false)* — if `true`, passes `--no-csf` to SynthStrip  
- `use_gpu` *(boolean; default: false)* — if `true`, uses the GPU container

**Config schema**

```json
{
  "t1": null,
  "t2": null,
  "no_csf": false,
  "use_gpu": false
}
````

> Provide either `t1` or `t2` (not both). Leave the unused key as `null`.

---

## Usage

### Running on Brainlife.io

1. Go to [Brainlife.io](https://brainlife.io) and search for the `app-synthstrip-gpu` app.
2. Click **Execute**.
3. Upload one anatomical input (`t1` *or* `t2`) as `.nii.gz`.
4. (Optional) Upload a `config.json` to enable `no_csf` or `use_gpu`.
5. Submit. Outputs will include a brain-extracted volume and a binary mask.

#### Via CLI

1. Install the Brainlife CLI: [https://brainlife.io/docs/cli/](https://brainlife.io/docs/cli/)
2. Login:

   ```bash
   bl login
   ```
3. Run:

   ```bash
   bl app run --id <app_id> --project <project_id> \
     --input t1:<t1_dataset_id> \
     --config config.json
   ```

   Replace `t1:` with `t2:` if you’re providing a T2w image.

---

### Running Locally / On HPC

1. **Clone** and move into the app’s `main/` directory.

2. **Prepare** a `config.json`:

   **Example (T1w, CPU):**

   ```json
   { "t1": "sub-01_T1w.nii.gz", "t2": null, "no_csf": false, "use_gpu": false }
   ```

   **Example (T2w, GPU + no CSF):**

   ```json
   { "t1": null, "t2": "sub-01_T2w.nii.gz", "no_csf": true, "use_gpu": true }
   ```

3. **Run**:

   ```bash
   bash ./main
   ```

> The script is PBS-ready (`#PBS` headers). On clusters using PBS/Torque, you can submit it with `qsub main`. On SLURM or other schedulers, run the script directly or adapt the header.

---

## Outputs

* `t1_brain/t1.nii.gz` **or** `t2_brain/t2.nii.gz` — brain-extracted anatomical volume
* `mask/mask.nii.gz` — binary brain mask aligned to the input

---

## Container & Implementation Notes

* **CPU image:** `freesurfer/synthstrip:1.7`
* **GPU image:** `freesurfer/synthstrip:1.7-gpu`
* **Runner:** `singularity exec -e docker://<image> ...`

**GPU passthrough:** If `use_gpu=true`, ensure your site exposes GPUs to Singularity (e.g., `singularity exec --nv ...` or cluster-specific env). Without GPU passthrough, the GPU container will fall back or fail depending on your environment.

**Heads-up (variable name):** The current `main` checks `gpu` when deciding the container but reads `use_gpu` from `config.json`. If your environment doesn’t set `gpu`, set **both** `use_gpu=true` in `config.json` **and** export `gpu=true` before running, or update the script to check `use_gpu` directly.

---

## Troubleshooting

* **“Datatype not supported”** — Ensure **exactly one** of `t1` or `t2` is non-null.
* **Empty or partial masks** — Try rerunning **without** `no_csf` first; if contrast is atypical, verify modality is correctly assigned.
* **GPU not detected** — Confirm a GPU node, drivers, and Singularity GPU passthrough; try CPU mode as a control.

---

## Citation

If you use this repository, please cite:

* Hoopes, A., Mora, J. S., Dalca, A. V., Fischl, B., & Hoffmann, M. (2022). **SynthStrip: skull-stripping for any brain image**. *NeuroImage*, 260, 119474.
* Kelley, W., Ngo, N., Dalca, A. V., Fischl, B., Zöllei, L., & Hoffmann, M. (2024, May). **Boosting skull-stripping performance for pediatric brain images**. In *2024 IEEE ISBI* (pp. 1–5). IEEE.
* Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. **brainlife.io: a decentralized and open-source cloud platform to support neuroscience research.** Nat Methods 21, 809–813 (2024).
---
