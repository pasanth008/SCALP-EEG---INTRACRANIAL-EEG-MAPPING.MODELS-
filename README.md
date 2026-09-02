# SCALP-EEG---INTRACRANIAL-EEG-MAPPING.MODELS-
# Synthetic scEEG–iEEG Dataset Generation and Deep Learning-Based EEG-to-EEG Translation

Summer project on generating a realistic synthetic scalp-EEG (scEEG) / intracranial-EEG
(iEEG) dataset with interictal epileptiform discharge (IED) events, and training deep
learning models to map scEEG to iEEG.

> Repository accompanies a summer project report and presentation.
> % TODO: add report / slides links here once uploaded, e.g.
> **Report:** `report/summer_project_report.pdf` · **Slides:** `slides/summer_project.pdf`

---

## Overview

Intracranial EEG (iEEG), recorded via foramen ovale (FO) electrodes, is the clinical
gold standard for detecting interictal epileptiform discharges (IEDs), but it requires an
invasive procedure. Scalp EEG (scEEG) is non-invasive but only captures a small fraction
of IEDs — published concurrent scEEG–iEEG recordings show roughly 18.8% scalp visibility
([Abdi-Sargezeh et al., *Sensors* 2025](https://doi.org/10.3390/s25020494)).

This project has two stages:

1. **Synthetic dataset generation** — build a physiologically realistic, simultaneously
   recorded scEEG + iEEG dataset with IED events for 18 virtual subjects, and validate
   its realism against a 31-point checklist derived from clinical/literature targets.
2. **scEEG → iEEG mapping models** — train and evaluate two architectures (VAE-cGAN and
   CNN-BiLSTM) that translate scEEG into estimated iEEG, across three train/test split
   strategies of increasing strictness.

---

## Pipeline

```
MRI (fsaverage) → source anatomy → connectivity → state-space activity
   → IED injection & propagation → forward model → scEEG (20 ch) + FO iEEG (12 ch)
   → realism validation (31 checks)
   → scEEG-to-iEEG mapping models (VAE-cGAN / CNN-BiLSTM)
   → evaluation (subject-dependent / pooled / leave-one-subject-out)
```

---

## Repository structure

```
.
├── 01_dataset_generation_21_final.ipynb     # synthetic scEEG/iEEG + IED dataset generator
├── REALISM_VALIDATION_21_final.ipynb        # 31-point realism validation suite
├── vae-cgan_subject_dependent_bd_28.ipynb   # VAE-cGAN, subject-dependent split
├── vae-cgan_pooled_split_bd_28.ipynb        # VAE-cGAN, pooled/group split
├── vae-cgan_leave_one_out_bd_28.ipynb       # VAE-cGAN, leave-one-subject-out split
├── CNN_BILSTM_subject_dependent_bd_28.ipynb # CNN-BiLSTM, subject-dependent split
├── CNN_BILSTM_pooled_split_bd_28.ipynb      # CNN-BiLSTM, pooled/group split
├── CNN_BILSTM_leave_one_out_bd_28.ipynb     # CNN-BiLSTM, leave-one-subject-out split
├── report/                                   # LaTeX report (Overleaf-ready)
├── slides/                                   # presentation
└── README.md
```

---

## 1. Synthetic Dataset Generation

`01_dataset_generation_21_final.ipynb` generates 18 virtual subjects of simultaneous
scEEG (20 channels) and FO iEEG (12 channels, 6 per hemisphere) at 200 Hz, with IED
events, through an anatomically-informed state-space pipeline:

- **Source anatomy**: sampled from a real MRI template (FreeSurfer `fsaverage`), across
  5 regions × 2 hemispheres — amygdala, hippocampus, temporal pole, orbitofrontal cortex,
  insula.
- **Connectivity**: a single anatomically-informed connectivity matrix drives both
  background activity correlation and IED propagation.
- **IED generation**: a shared waveform template is injected at the epileptogenic focus,
  then propagated to other regions with connectivity-dependent delay.
- **Forward model**: projects source activity to scalp and FO sensors, followed by
  realistic noise, filtering, and amplitude calibration.
- **Outputs**: continuous recordings and segmented (320 ms, class-balanced and
  class-imbalanced) datasets.

## 2. Realism Validation

`REALISM_VALIDATION_21_final.ipynb` checks the generated dataset against 31 physiological
targets, e.g. IED FWHM, scalp/FO amplitude ratio, FO left/right lateralisation, scalp
visibility rate and AUC, SNR, spectral (1/f) slope, alpha power, inter-channel
correlation, ERSP gain, and inter-spike-interval statistics. The current configuration
passes **26/31** checks. % TODO: confirm after running the notebook

## 3. Mapping Models

**VAE-cGAN** (`vae-cgan_*_bd_28.ipynb`) — based on
[Abdi-Sargezeh et al., *Sensors* 2025](https://doi.org/10.3390/s25020494): an encoder
maps scEEG to a latent Gaussian space; a SPADE-conditioned generator maps the latent
code + scEEG to estimated iEEG; a patch discriminator provides adversarial training
(hinge + KL + L1 + feature-matching loss).

**CNN-BiLSTM** (`CNN_BILSTM_*_bd_28.ipynb`) — CNN layers extract local
spatial/temporal spike features; a BiLSTM adds full-segment (past + future) context;
trained with a composite MSE + L1 + (1 − Pearson) + (1 − Cosine) loss and per-channel
z-score standardization.

Each model is trained and evaluated under three splits:

| Split | Description |
|---|---|
| Subject-Dependent | 70/10/20 split within each subject; one model per subject |
| Pooled / Group | 70/10/20 split across all subjects, no subject leakage |
| Leave-One-Subject-Out | Each subject held out entirely as test, in turn |

**Metrics**: MSE, Pearson correlation (PCORR), cosine similarity (COSSIM) — reported for
IED, non-IED, and combined segments, alongside a shuffle-control diagnostic.

---

## Results

% TODO: fill in once notebooks are executed

| Model | Split | MSE | PCORR | COSSIM |
|---|---|---|---|---|
| VAE-cGAN | Subject-Dependent | – | – | – |
| VAE-cGAN | Pooled/Group | – | – | – |
| VAE-cGAN | Leave-One-Subject-Out | – | – | – |
| CNN-BiLSTM | Subject-Dependent | – | – | – |
| CNN-BiLSTM | Pooled/Group | – | – | – |
| CNN-BiLSTM | Leave-One-Subject-Out | – | – | – |

---

## Reference

B. Abdi-Sargezeh, S. Shirani, A. Valentin, G. Alarcon, S. Sanei, "EEG-to-EEG:
Scalp-to-Intracranial EEG Translation Using a Combination of Variational Autoencoder and
Generative Adversarial Networks," *Sensors*, 25(2), 494, 2025.
[https://doi.org/10.3390/s25020494](https://doi.org/10.3390/s25020494)

---

## Author

% TODO: your name, institution, contact
