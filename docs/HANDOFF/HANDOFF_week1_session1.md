# Handoff — Week 1, Session 1

**Project:** Forecasting AI Data Center Power Demand: A Benchmark of Classical, Foundation, and Hybrid Quantum Models with Uncertainty Quantification and Grid-Aware Control
**Date of session:** 30 Sep – 1 Oct 2026
**Purpose of this file:** a complete record of what was done in this chat, so the work can continue in a new chat without reading the original conversation. Read together with the project handoff `Project_Context_FA.txt` (v1.0) and the companion guide `GUIDE_week1_continue.md`.

---

## 1. How to work with this student (important for the next assistant)

| Preference | Detail |
|---|---|
| Chat language | Persian (Farsi) for all explanations in chat |
| Notebook text | **English** for any Markdown/explanations written into notebooks |
| Pace | **One step at a time.** Give exactly one cell or one action per message, wait for the output, then continue. Do not send multi-cell batches unless asked. |
| Files | Answer in chat by default. Create files only when the student explicitly asks (as for paper summaries and these handoff files). |
| Runtime | **Local Jupyter on the student's own Mac**, not Colab. Everything should be runnable from inside Jupyter cells (`!`, `%pip`, `conda run`). |
| Git | The student handles Git personally. Do not give Git instructions unless asked; only remind about `.gitignore` for data and papers. |
| Learning | The student wants to understand, not just copy. Explain outputs at an intermediate level when asked. |
| Week 1 end | Explicitly announce when all Week 1 tasks are complete. |

---

## 2. Environment (verified)

### Machine and paths
- macOS (MacBook Air), user `aryaneuse`.
- Miniconda at `/opt/miniconda3` (base Python 3.13).
- **Project root:** `~/Desktop/datacenter-power-forecasting/` (moved from `~/projects/` by the student).
  - Caution given: if iCloud "Desktop & Documents" sync is on, large files in `data/` will upload to iCloud.
- Notebook convention for paths: `PROJECT_DIR = Path.cwd().parent` (notebooks live in `notebooks/`).

### Network (critical)
- Router DNS `192.168.1.1` returns **SERVFAIL** for `files.pythonhosted.org` (pip downloads fail without VPN).
- Direct DNS to `8.8.8.8:53` times out; DNS-over-HTTPS (dns.google, cloudflare-dns.com) also blocked.
- **With VPN on, pip, AWS S3, and GitHub all work.** Keep VPN on for installs and downloads; restart Jupyter after toggling VPN.
- **The VPN truncates any single HTTP download at exactly 8 MiB (8,388,608 bytes).** Workarounds: HTTP `Range` requests in chunks < 8 MB, or download via browser (possibly with VPN off).

### Python environment
- Conda env **`dcpower`**, Python 3.11, created with `conda-forge --override-channels` (the default Anaconda channel failed silently because of Terms-of-Service acceptance in non-interactive mode).
- Jupyter kernel registered as **`Python (dcpower)`** (`~/Library/Jupyter/kernels/dcpower`).
- Verified versions: **torch 2.14.1, pennylane 0.45.1, polars 1.44.2**.
- `requirements.txt` (top-level deps) and `requirements.lock` (`pip freeze`, **154 packages**) are in the project root.
- `requirements.txt` contents: numpy, pandas, polars, duckdb, pyarrow, matplotlib, statsmodels, awscli, torch, pennylane, wandb, jupyterlab, ipykernel.
- Useful patterns: `%pip install ...` inside the kernel; `!conda run --no-capture-output -n dcpower pip install --progress-bar on ...` from the base kernel.

### Weights & Biases
- Account: `aryan-faghirollahy`, entity `aryan-faghirollahy-uok`, project **`datacenter-power-forecasting`**.
- Runs logged: `setup-test` (dummy loss, 20 steps) and `pennylane-sine-warmup` (see §5).
- The first API key was pasted into the chat; the student was told to revoke it and generate a new one, then re-logged in with `wandb.login(relogin=True)`. Credentials are stored in `~/.netrc`.
- W&B creates `notebooks/wandb/` locally; `wandb/` must be in `.gitignore`.

---

## 3. Data access (MIT Supercloud) — verified

Command (from Jupyter, VPN on): `!aws s3 ls --no-sign-request s3://mit-supercloud-dataset/`

Top level: `2022-hpca/`, `challenges/`, `datacenter-challenge/`.

Inside `datacenter-challenge/` (the student may have descended one more sub-folder; **confirm and record the exact prefix**):

| Item | Size | Use |
|---|---|---|
| `gpu/` | 100 sub-folders, one CSV per job | **Main data** |
| `cpu/` | 100 sub-folders | Not used for now |
| `slurm-log.csv` | ~103 MB | Job start/end times, active/queued job counts |
| `labelled_jobids.csv` | ~82 KB | Job → model type (ResNet, BERT, U-Net, …) |
| `labelled_job_stats.csv`, `tres-mapping.txt`, `README.md`, `LICENSE` | small | Reference |
| `node-data.csv` | ~2.27 GB | **Do not download** (5-min node data, not needed) |

**Nothing has been downloaded yet.** Download is a Week 2 task.

Key facts learned about the data (from the official repo README and the dataset paper):
- GPU data is **per job**: each CSV holds all GPUs of all nodes assigned to one job, sampled every 100 ms with `nvidia-smi`. Recording starts/stops with the job (Slurm prolog/epilog).
- Consequence: the aggregate series must be built by summing all job files over time; **idle GPUs are likely absent** (to verify in Week 2).
- GPU `timestamp` appears to be absolute, whereas CPU `ElapsedTime` is relative to job start (to verify on a real file in Week 2).
- License CC BY-NC-ND 4.0: never commit raw or processed data.

---

## 4. Repository structure created

```text
datacenter-power-forecasting/
├── configs/
├── data/{raw,interim,processed}/      # local only, must be in .gitignore
├── docs/
│   ├── literature.md                  # template with 6 entries (see §7)
│   ├── papers/                        # PDF archive, must be in .gitignore
│   ├── weekly/
│   └── meetings/
├── experiments/
├── notebooks/
│   ├── 00_setup_check.ipynb
│   ├── 01_pennylane_warmup.ipynb
│   └── wandb/                         # auto-created, ignore
├── src/{data,models,eval,control}/
├── requirements.txt
└── requirements.lock
```

The student manages Git. Required `.gitignore` entries (reminded): `data/`, `*.parquet`, `*.csv`, `*.csv.gz`, `docs/papers/`, `wandb/`, `mlruns/`, `.ipynb_checkpoints/`, `__pycache__/`, `.env`, `.DS_Store`.

---

## 5. Notebooks

### `00_setup_check.ipynb`
- English Markdown intro: project, data, the four pillars (RQ1 classical/foundation benchmark, RQ2 hybrid quantum, RQ3 conformal UQ, RQ4 battery MPC), guiding principle.
- Cell creating the directory structure.
- AWS CLI install and S3 listing.
- Conda env creation (`conda-forge`), `requirements.txt` writing, install with progress bar, `pip freeze | tee requirements.lock`, kernel registration.
- Final check printing `sys.executable` and package versions.

### `01_pennylane_warmup.ipynb` (kernel: Python (dcpower))
1. 4-qubit QNode on `default.qubit`: `AngleEmbedding` + `StronglyEntanglingLayers` (2 layers → weights shape (2, 4, 3) = 24 parameters) + `PauliZ` expectations. Drawn at template and device level (RX encodings, 8 Rot gates, 8 CNOTs: ring range 1 in layer 1, range 2 in layer 2).
2. Converted to `qml.qnn.TorchLayer` (`interface="torch"`, argument must be named `inputs`); batch output shape (2, 4); 24 trainable params.
3. **Hybrid model** `Linear(1,4) → TorchLayer → Linear(4,1)` (37 params: 8 + 24 + 5), trained on sin(x), x ∈ [−π, π], 100 points, Adam lr 0.05, 100 epochs, seed 42, logged to W&B as `pennylane-sine-warmup`. **Loss 0.629 → ~0.0000.**
   - Interpretation given to the student: this is a pipeline sanity check only, not evidence of quantum advantage.

**Both Week 1 technical completion criteria are met:** S3 listing seen; a quantum circuit trained inside PyTorch with decreasing loss.

---

## 6. Paper archive (`docs/papers/`)

Downloaded with a Jupyter script (arXiv PDFs with 3 s pauses; open-access PDFs validated by `%PDF-` header and `%%EOF`). A verification cell checks all expected files. Last check: **26/26 OK, 2 pending.**

| # | File | Source |
|---|---|---|
| 01 | `01_mit_supercloud_dataset_2108.02037.pdf` | arXiv |
| 02 | `02_alberta_ai_datacenter_stlf_2503.07756.pdf` | arXiv |
| 03 | `03_ai_datacenter_grid_impacts_2509.07218.pdf` | arXiv |
| 04 | `04_bowles_benchmarking_qml_2403.07059.pdf` | arXiv |
| 05 | `05_qrc_load_forecasting_2606.12806.pdf` | arXiv |
| 06 | `06_physics_aware_gpu_power_2605.04074.pdf` | arXiv |
| 10 | `10_wiser_eon_hybrid_qml_2605.24252.pdf` | arXiv |
| 11 | `11_pv_forecasting_qml_2312.16379.pdf` | browser (19 MB, > 8 MiB VPN limit) |
| 12 | `12_quantum_smart_grid_review_2609.18847.pdf` | arXiv |
| 13 | `13_qaoa_load_scheduling_2311.10594.pdf` | arXiv |
| 14 | `14_hybrid_qnn_review_2608.01194.pdf` | arXiv |
| 15 | `15_qlstm_2009.01783.pdf` | arXiv |
| 20 | `20_choukse_power_stabilization_2508.14318.pdf` | arXiv |
| 21 | `21_ko_hybrid_ess_supercap_2512.08076.pdf` | arXiv |
| 22 | `22_differentiable_predictive_hess_2606.04869.pdf` | arXiv |
| 23 | `23_adaptive_conformal_inference_2106.00170.pdf` | arXiv |
| 24 | `24_foundation_model_leakage_2510.13654.pdf` | arXiv |
| 30 | `30_sci_reports_2025_quantum_load.pdf` | open access |
| 31 | `31_qsvm_household_jcc_2024.pdf` | open access |
| 32 | `32_cerezo_barren_plateaus_2025.pdf` | open access |
| 33 | `33_qsegrnn_transformer_temp_2025.pdf` | open access |
| 34 | `34_nerc_emerging_large_loads_2025.pdf` | open access |
| 35 | `35_epri_powering_intelligence_2024.pdf` | manual |
| 36 | `36_kaseb_phd_thesis_2026.pdf` | student's local copy (18.1 MB) |
| 37 | `37_iea_energy_and_ai_2025.pdf` | manual |
| 38 | `38_vqc_datacenter_energy_control_2024.pdf` | manual |
| 39 | `39_colangelo_nature_energy_2026.pdf` | **PENDING** (university access) |
| 40 | `40_qmi_forecast_schedule_2026.pdf` | **PENDING** (university access) |

Numbering is grouped by tens: 01–09 core, 10–19 QML for energy, 20–29 grid/control/UQ, 30+ non-arXiv.

---

## 7. Literature work

### `docs/literature.md`
Created with an empty Idea/Method/Results/Weaknesses template for six entries in this order: 1 MIT Supercloud, 2 Alberta, 3 Grid-impacts review, 4 Bowles, 5 Kaseb (EPSR 2024), 6 QRC (optional).
**Note:** the summary files label QRC as section 5 and PI-DLinear as section 6, which differs from the template order. See the guide for how to reconcile.

### Summary files produced (in Persian with an English block for `literature.md`)
| File | Paper |
|---|---|
| `01_mit_supercloud_summary.md` | MIT Supercloud Dataset |
| `02_alberta_stlf_summary.md` | Short-Term Load Forecasting for AI-Data Center (Alberta) |
| `03_grid_impacts_review_summary.md` | Electricity Demand and Grid Impacts of AI Data Centers (review) |
| `04_bowles_benchmarking_summary.md` | Better than classical? (Bowles, Ahmed, Schuld) |
| `05_qrc_summary.md` | Quantum Reservoir Computing for STLF |
| `06_physics_aware_gpu_summary.md` | Physics-Aware GPU Power Forecasting (PI-DLinear) |

All summaries were written from the actual paper text fetched online; uncertain points are marked ⚠️.

### Key findings from the six papers
1. **MIT Supercloud:** 224 GPU nodes × 2 V100 = 448 GPUs; GPU traces per job at 100 ms; median job ~4 min; 85% single-GPU; power highly correlated with utilization (Spearman 0.92) and temperature, weakly with memory used (0.2).
2. **Alberta:** LSTM R² 0.9639, sMAPE 2.26% — but at **1-second** resolution, 300-step look-back, 90 s horizon; **no naive baseline**, single run, unspecified units.
3. **Grid review:** supports motivation; IT > 60% of AI data center load; grid-side power follows IT power for slower variations (justifies GPU power as a proxy at minute scale); batteries suit minute–hour scales; calls for forecasts with uncertainty bands and ramp estimates.
4. **Bowles:** out-of-the-box classical models beat 12 QML models on 160 datasets; removing entanglement often does not hurt; the best quantum model is a "dressed" hybrid (same family as our QCNN); authors **reject parameter-matched baselines** as the sole comparison.
5. **QRC:** template for our second quantum model (fixed brickwork reservoir, 5N observables, exponential-kernel aggregation, Elastic Net); **no classical-equivalent comparison** in the sections read; per-time-step states can be cached (~60× fewer circuit runs).
6. **PI-DLinear:** **closest prior work** — same data and **same 1-minute resolution** (~330.5k steps, 238 days; our project's data statistics come from this paper); DLinear beats most transformers; look-back 4–10 h; horizons 5–80 min.

---

## 8. Protocol issues raised (to discuss in Meeting 1)

| # | Issue | Proposal |
|---|---|---|
| 1 | Alberta numbers (1 s / 300 / 90 s) are not comparable to our protocol (1 min / 60 / 1-5-15 min) | Reproduce Alberta settings on a small subset **with a naive baseline**, or report the difference explicitly |
| 2 | No naive baseline in Alberta or PI-DLinear | Naive/seasonal naive already in our plan; emphasize in the paper |
| 3 | Bowles rejects parameter-matched comparisons | Keep two comparisons: (a) strong, equally tuned classical models; (b) same-architecture classical control to isolate the circuit; report parameter counts but do not rely on them alone |
| 4 | DLinear/NLinear missing from our RQ1 list | Add both (available in `neuralforecast`); consider running PI-DLinear if its code is available |
| 5 | Look-back 60 min vs 4–10 h in PI-DLinear | Decide via ACF in Week 2; keep 60 for the main comparison, add a longer look-back side experiment for top classical models |
| 6 | QRC needs fair controls | Elastic Net on raw features; size-matched classical random-feature reservoir; QRC without entanglement |
| 7 | Equal hyperparameter budget and logging of all quantum variants | Adopt as protocol rules (guards against positivity bias) |
| 8 | Feature ideas | Lagged temperature; sustained high utilization (throttling precursor); active/queued job counts from `slurm-log.csv` |

---

## 9. Problems encountered and how they were solved

| Problem | Cause | Solution |
|---|---|---|
| `SyntaxError` on `aws s3 ls` | Shell command in a Python cell | Prefix with `!` or use `%%bash` |
| pip `NewConnectionError` | Router DNS cannot resolve `files.pythonhosted.org`; public DNS and DoH blocked | Turn VPN on; restart Jupyter |
| `conda` env not created | Default channel needs ToS acceptance | `conda create -n dcpower -c conda-forge --override-channels python=3.11` |
| `ModuleNotFoundError: torch` | Kernel not switched (cell counter kept increasing) | Kernel → Change Kernel → Python (dcpower) |
| W&B "Invalid choice" | API key pasted at the menu prompt | Choose option `2`, then paste key; rotate exposed key |
| PDF download fails at 8 MiB | VPN truncation | Browser download or chunked Range requests |
| Kaseb thesis link | Not found online | Used the student's local copy |
