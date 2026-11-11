# LSPV-Bench: data-scarcity remedies for short-term PV power forecasting

Code for the manuscript **"Which remedy for a new photovoltaic plant? A budget-matched comparison of diffusion augmentation, physics-based pre-training and transfer learning for short-term forecasting"** by A. Jaffar Sadiq Ali.

The notebook compares six training options for 15-min and 60-min-ahead PV power forecasts when a plant has little local data:

| Option | What it does |
|---|---|
| A0 Target-only | Trains on the local history only |
| A1 Replication | Adds 50% copies of local windows |
| A2 Jitter | Adds 50% noisy copies of local windows |
| A3 DDPM | Adds 50% windows from a denoising diffusion model trained on the local history |
| A4 Physics pre-training | Pre-trains on pvlib-simulated power, then fine-tunes on local data |
| A5 Transfer | Pre-trains on a data-rich array, then fine-tunes on local data |

Every option gets the same number of optimiser updates (LSTM) or tuning trials (LightGBM). Skill is measured against smart persistence. The notebook also repeats the base-paper DDPM protocol with and without an equal update budget (hypothesis H4).

## Repository contents

| File | Description |
|---|---|
| `LSPV_Bench_v2.0.5.ipynb` | The full notebook: data intake, checks, models, statistics, figures and results export |
| `README.md` | This file |

## Data

The notebook does not download data. Put the files below into one archive called `general.zip` (or `general.7z`), either in your Google Drive under `MyDrive/RESEARCH/LSPV_Bench/` or uploaded when the notebook asks. File names are matched anywhere inside the archive.

**Required (main benchmark, DKASC Alice Springs):** download from https://dkasolarcentre.com.au/locations/alice-springs

| File | Role |
|---|---|
| `106-Site_DKA-M5_C-Phase.csv` | Target array T1 (BP Solar, 4.95 kW) |
| `68-Site_DKA-M8_C-Phase.csv` | Target array T2 (Sungrid, 5.04 kW) |
| `90-Site_DKA-M3_A-Phase.csv` | Target array T3 (Kyocera, 5.40 kW) |
| `67-Site_DKA-M8_A-Phase.csv` | Transfer source (Evergreen Solar, 4.92 kW) |
| `101-Site_DKA-WeatherStation.csv` | Weather station for the physics simulation |
| `array_metadata.csv` | Rating, tilt and azimuth of each array |

**Optional (base-paper replication, H4):**

| File | Source |
|---|---|
| `5-Site_DG-PV1-DB-DG-M1A.csv` (in a folder whose name contains `yulara`) | DKASC Yulara, Desert Gardens array: https://dkasolarcentre.com.au/source/yulara/yulara-1-fixed |
| `Plant_1_Generation_Data.csv`, `Plant_1_Weather_Sensor_Data.csv`, `Plant_2_Generation_Data.csv`, `Plant_2_Weather_Sensor_Data.csv` | Kaggle, Solar Power Generation Data: https://www.kaggle.com/datasets/anikannal/solar-power-generation-data |

Follow the terms of use on each data provider's page. The notebook records a SHA-256 hash of every file it uses in `data_audit/DATA_PROVENANCE.json`.

## How to run

1. Open `LSPV_Bench_v2.0.5.ipynb` in Google Colab.
2. Choose **Runtime → Change runtime type → T4 GPU**.
3. In cell 1, set the run options:
   - `MODE = "SMOKE_TEST"` checks the pipeline on a small subset (a few minutes).
   - `MODE = "STANDARD_RUN"` is an exploratory pilot.
   - `MODE = "FULL_PAPER_RUN"` is the confirmatory run used in the paper.
   - `REPLICATION_ONLY = True` runs only the base-paper replication (H4); keep it `False` for the main benchmark.
   - `REPRO_REF_ZIP` is left empty except for the reproduction check (see below).
4. Choose **Runtime → Run all**. Allow Google Drive access when asked.

Only a `FULL_PAPER_RUN` whose software check passes is used for the paper. SMOKE and STANDARD results are exploratory.

### Run times measured on a Colab T4 GPU

| Run | Time |
|---|---|
| `FULL_PAPER_RUN`, main benchmark (1,224 tasks) | 494 min |
| `FULL_PAPER_RUN` with `REPLICATION_ONLY = True` | 378 min |

Runs are resumable. Each finished task is saved and copied to Drive as a checkpoint. If a session stops, run the notebook again with the same settings and finished tasks are reused.

### Reproduction check

Set `REPRO_REF_ZIP` to the path of an earlier `FULL_PAPER_RUN` results ZIP. The notebook then reruns the first array, first origin, shortest history and first seed in a clean session and compares the results with the reference. In the paper's run, 26 of 26 checked values agreed within ±2%.

## Outputs

Each run writes a results ZIP, `LSPV_results_<MODE>[_REPLICATION]_v2.0.5.zip`, to `/content/lspv/` and to the Drive folder. It contains:

| Folder or file | Contents |
|---|---|
| `tables/` | Main results, statistical tests (Diebold–Mariano, Holm, bootstrap CIs, equivalence), rankings, subgroups, compute cost, synthetic-data checks, replication tables, research gates, software check |
| `figures/` | 300-dpi PNG and PDF figures |
| `predictions/` | All test-set forecasts, so every metric can be recomputed |
| `data_audit/` | File inventory, hashes, validation, array selection, splits, standby-day log |
| `logs/` | Task ledger, run manifest, hyperparameters, runtime, hardware profile |
| `RESULTS_AUDIT.md` | Which result each claim rests on, and whether the file is present |

## Software

Python 3.13, PyTorch 2.14, LightGBM 4.6, pvlib 0.16.1, NumPy 2.4, pandas 2.2, SciPy 1.16, Matplotlib 3.10. The notebook installs what Colab lacks and writes the exact versions to `requirements.txt` in the results ZIP.

## Contact

A. Jaffar Sadiq Ali, Department of Electrical and Electronics Engineering, Sri Muthukumaran Institute of Technology, Chennai, India. ajaffi1@gmail.com · ORCID 0009-0003-4958-8319
