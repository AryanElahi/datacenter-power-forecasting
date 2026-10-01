# Handoff: End of Week 1 (complete) and start of Week 2

**Project:** Forecasting AI Data Center Power Demand: A Benchmark of Classical, Foundation, and Hybrid Quantum Models with Uncertainty Quantification and Grid-Aware Control
**Version:** 2.0 (supersedes `HANDOFF_week1_session1.md` and `GUIDE_week1_continue.md`)
**Date:** 1 October 2026 (9 Mehr 1405). Week 1 ran 30 Sep to 2 Oct 2026.
**Purpose:** a self-contained record of everything done in Week 1, every decision made, every verified fact and every open item, so a new chat can start Week 2 without reading any earlier conversation. Read together with `Project_Context_FA.txt` (v1.0, the master project plan in Persian).

---

## 0. How to use this file

| If you need | Go to |
|---|---|
| The status in one minute | Section 1 |
| How to work with this student (rules) | Section 2 |
| The project definition and 8-week structure | Section 3 |
| Environment, network limits, tooling | Sections 4 and 5 |
| Data access facts and what is still unverified | Section 6 |
| Repository, notebooks, W&B state | Sections 7, 8, 9 |
| The paper archive | Section 10 |
| What the ten papers say and what we took from them | Section 11 |
| Fixed protocol and all decisions | Section 12 |
| Proposal and other deliverables | Section 13 |
| What is finished and what is residual | Section 14 |
| The Week 2 plan, step by step | Section 15 |
| Risks, unverified items, watch-outs | Section 16 |
| Problems already solved | Section 17 |
| The opening prompt for the new chat | Section 18 |

---

## 1. Snapshot

- **Week 1 is complete.** Both completion criteria and all deliverables of the plan are met (Section 14).
- **Done:** repository and environment, W&B, S3 access with the exact prefix recorded, 4-qubit PennyLane circuit trained inside PyTorch, paper archive (26 of 28), ten structured literature summaries merged into `docs/literature.md`, bilingual research proposal, bilingual Week 1 report, Persian version of the literature notes, and a check of the foundation-model lineage table.
- **Not started:** any data download or processing (except one 38 KB lineage CSV), any modelling. This is Week 2.
- **Next:** Week 2 = data engineering and exploratory analysis, ending with supervisor Meeting 1 (Section 15).
- **Residual items for the student** (none blocks Week 2): fill the commit and W&B links in the weekly report, push to GitHub, coordinate Meeting 1 with the supervisor (the student declined an email draft), obtain 2 papers pending university access.

---

## 2. Student profile and working rules (important for the next assistant)

**Profile.** Bachelor student in electrical engineering (control track). Goal: a master's in data science. The project must show data skills, use the control background, be topical, and yield a paper, a public repository and thesis chapters. Time box: 8 weeks, supervisor evaluation meeting every 2 weeks. A compressed schedule is acceptable.

| Rule | Detail |
|---|---|
| Chat language | **Persian** for all explanations in chat. |
| Notebook and repo text | **English** for anything written into notebooks, Markdown cells, code comments and docs. |
| Pace | **One step at a time.** One cell or one action per message, then wait for the student's output. No multi-cell batches unless asked. |
| Output format | The student pastes cell outputs back. Sometimes he pastes the code again instead of its output; remind him to paste what is printed **below** the cell. Messages can be very terse, and keyboard slips happen (e.g., a garbled message); just ask again. |
| Files | Answer in chat by default. Create a file or document only when asked or when it is clearly a deliverable. In Week 1 the student explicitly asked for: summaries (`.md` files), the proposal, the weekly report and the Persian literature notes (these three as Claude Docs with an English tab and a Persian tab where bilingual), the literature file, and this handoff. |
| Runtime | **Local Jupyter on the student's own Mac**, not Colab. Everything must run from Jupyter cells (`!`, `%pip`, `conda run`). |
| Git | The student handles Git personally. Do **not** give Git instructions unless asked; only remind about `.gitignore` for data and papers. |
| Learning | The student wants to understand, not just copy. Explain outputs at an intermediate level when asked. |
| Completion | **Announce explicitly** when a week's tasks are complete. |
| Code cells | Make them self-contained and verifiable: use `PROJECT_DIR = Path.cwd().parent` (notebooks live in `notebooks/`), print what was done, and add a guard against double execution when a cell appends to a file (this worked well for the README cell). |
| Honesty | Keep the cautious tone: no quantum-advantage claims, mark unverified points, say plainly when a source could not be read. |

---

## 3. Project definition

**Golden rule:** the title and hypotheses must not depend on the quantum result. The project succeeds even if the quantum model loses.

**Research questions**

| RQ | Question | Pillar |
|---|---|---|
| RQ1 | How well do statistical, ML, deep and time-series foundation models forecast data center GPU power, especially at load ramps? | Classical benchmark |
| RQ2 | Do hybrid quantum–classical models reach competitive accuracy with fewer parameters or less training data? | Fair quantum evaluation |
| RQ3 | Can forecasts carry calibrated prediction intervals, including in ramp periods? | Uncertainty |
| RQ4 | How much does forecast quality reduce the ramp rate delivered to the grid by a battery under MPC? | Grid-aware control |

**Novelty claim (cautious):** in our searches we found no work evaluating QML for GPU or AI-data-center power forecasting. Nearby work exists (QSegRNN for data center transformer temperature; a variational-circuit data center energy-control paper). Our contribution is the combination: a full classical and foundation benchmark on real AI-cluster power with ramp-focused metrics, a fair hybrid-quantum evaluation with matched controls and ablation, calibrated uncertainty, and translation into an engineering value via MPC (the novelty of RQ4 is the value of forecast quality, oracle versus models, not MPC itself).

**8-week structure** (four 2-week sprints, meeting at the end of weeks 2, 4, 6, 8)

| Sprint | Weeks | Topic | Meeting |
|---|---|---|---|
| 1 | 1–2 | Foundations and data engineering | Meeting 1: approve proposal and protocol |
| 2 | 3–4 | Classical and foundation-model benchmark | Meeting 2: freeze the benchmark |
| 3 | 5–6 | Hybrid quantum models | Meeting 3: paper and venue decision |
| 4 | 7–8 | Uncertainty, control, writing | Meeting 4: full paper draft |

Items labelled **[main]** are core; *[supplementary]* items start only when all main items of that week are done; if behind, supplementary items are dropped first (order: QAOA, dashboard, quantum kernel, fine-tuning a foundation model). The full week-by-week checklists are in `Project_Context_FA.txt`, Section 8.

**Working rules from the project plan**
1. No result enters a report unless logged in W&B or MLflow.
2. At least one commit per day; one half-page weekly report every Friday.
3. No MIT Supercloud data in GitHub.
4. Supplementary items only after the main items of the week.
5. Fixed decisions change only with a reason recorded in the meeting notes.
6. Each sprint's paper and thesis sections are written in that sprint.

**Weekly report template:** (1) work done with commit/W&B links, (2) key number or figure, (3) blockers and decisions, (4) next week's plan. **Meeting minutes template:** date and attendees, what was presented, supervisor feedback, decisions, next steps with deadlines. Minutes go to `docs/meetings/` the same day.

---

## 4. Environment (verified)

- **Machine:** macOS (MacBook Air), user `aryaneuse`. Miniconda at `/opt/miniconda3` (base Python 3.13).
- **Project root:** `~/Desktop/datacenter-power-forecasting/`. Caution given: if iCloud "Desktop & Documents" sync is on, large files in `data/` will upload to iCloud.
- **Conda env `dcpower`**, Python 3.11, created with conda-forge only: `conda create -n dcpower -c conda-forge --override-channels python=3.11`. (The default Anaconda channel failed silently because of terms-of-service acceptance in non-interactive mode.)
- **Jupyter kernel** `Python (dcpower)` registered at `~/Library/Jupyter/kernels/dcpower`. If a notebook raises `ModuleNotFoundError: torch`, the wrong kernel is selected (cell counter keeps increasing): Kernel, Change Kernel, `Python (dcpower)`.
- **Verified versions:** torch 2.14.1, pennylane 0.45.1, polars 1.44.2.
- **Dependency files** (project root): `requirements.txt` (numpy, pandas, polars, duckdb, pyarrow, matplotlib, statsmodels, awscli, torch, pennylane, wandb, jupyterlab, ipykernel) and `requirements.lock` (`pip freeze`, **154 packages**).
- **Install patterns:** `%pip install ...` inside the kernel; or `!conda run --no-capture-output -n dcpower pip install --progress-bar on ...` from the base kernel.
- **Packages not yet installed** (needed later, add with a lock update): statsforecast, lightgbm, optuna, neuralforecast, chronos/timesfm/toto, mapie (optional), shap, cvxpy, seaborn or similar for plots. Add per sprint, then refresh `requirements.lock`.

---

## 5. Network constraints (critical)

- The router DNS `192.168.1.1` returns **SERVFAIL** for `files.pythonhosted.org`, so pip downloads fail without a VPN.
- Direct DNS to `8.8.8.8:53` times out and DNS-over-HTTPS (dns.google, cloudflare-dns.com) is blocked.
- **With the VPN on, pip, AWS S3 and GitHub all work.** Keep the VPN on for installs and downloads; **restart Jupyter after toggling the VPN**.
- **The VPN truncates any single HTTP download at exactly 8 MiB (8,388,608 bytes).** Workarounds: HTTP `Range` requests in chunks below 8 MB (e.g., 4 MiB), or a browser download (possibly with the VPN off). This matters for `slurm-log.csv` (about 103 MB) and any large GPU job file.
- Small files are fine: the 38,434-byte lineage CSV downloaded through Python `urllib` with a `User-Agent` header (GitHub blocks automated fetching in the assistant's own tools, but not on the student's machine).

---

## 6. Data

### 6.1 Access (verified with real listings)

Bucket top level: `2022-hpca/`, `challenges/`, `datacenter-challenge/`. The dataset prefix is:

`s3://mit-supercloud-dataset/datacenter-challenge/202201/`

Command: `!aws s3 ls --no-sign-request s3://mit-supercloud-dataset/datacenter-challenge/202201/`

| Item | Size | Use |
|---|---|---|
| `gpu/` | 100 sub-folders, one CSV per job | **Main data** |
| `cpu/` | 100 sub-folders | Not used for now |
| `slurm-log.csv` | 103,165,427 bytes (~103 MB) | Job start/end, active and queued job counts |
| `labelled_jobids.csv` | 82,332 bytes | Job to model type (ResNet, BERT, U-Net, …) |
| `labelled_job_stats.csv` | 339 bytes | Reference |
| `node-data.csv` | 2,274,303,873 bytes (~2.27 GB) | **Do not download** (5-minute node data, not needed) |
| `tres-mapping.txt`, `README.md`, `LICENSE` | small | Reference |

The exact prefix is recorded in the project `README.md` under "Data source" (added by a one-time Jupyter cell).

**Licence:** CC BY-NC-ND 4.0. **Never commit raw or processed data**; publish scripts only.

### 6.2 Facts learned from the dataset paper and official README

- 224 GPU nodes × 2 V100 32 GB = 448 GPUs plus 480 CPU-only nodes. GPU time series are **per job**, sampled every 100 ms with `nvidia-smi`; recording starts and stops with the job (Slurm prolog/epilog) and covers only the GPUs allocated to that job. CPU series are every 10 s; node data every 5 minutes.
- Median GPU job runtime about 4 minutes; 85% of jobs use a single GPU; median SM utilization of single-GPU jobs 23%; many multi-GPU jobs idle (debugging).
- Per-job average GPU power correlates strongly with SM utilization (Spearman 0.92), memory utilization (0.95) and GPU/memory temperature (0.92/0.93), weakly with memory used (0.2).
- Dataset statistics for our series (from the PI-DLinear paper at the same 1-minute resolution): **about 330.5k steps, about 238 days, 7 variables** (GPU and memory utilization, GPU and memory temperature, memory used and free, power). Aggregate peak about 45 kW, Feb to Oct 2021.

### 6.3 Not yet verified (Week 2 must confirm on real files)

- The exact `gpu/` folder layout, file names and columns of a job CSV; whether `timestamp` is absolute (GPU) while CPU `ElapsedTime` is relative.
- Whether idle GPUs are absent from the data (inferred from job-scoped collection). The aggregate series is **job-attributed GPU power, not facility power** (idle GPUs, CPUs, network, storage and cooling excluded).
- How to turn per-job per-GPU samples into one aggregate 1-minute series (sum across GPUs and jobs at aligned times, then resample).
- Total size of `gpu/` (use `aws s3 ls --summarize --human-readable --recursive` on a sub-folder first).
- Which columns `slurm-log.csv` has (job start/end for active and queued counts).
- Facility sensor data (total power, PUE) is described in the paper but was not in the public listing.

---

## 7. Repository state

```text
datacenter-power-forecasting/
├── README.md                          # now contains the "Data source" section
├── configs/
├── data/{raw,interim,processed}/      # local only, gitignored; raw/tsfm_lineage.csv exists
├── docs/
│   ├── literature.md                  # FINAL: 10 entries (replaced the template)
│   ├── papers/                        # PDF archive, gitignored
│   ├── summaries/                     # the ten summary files (01..10), if the student kept them here
│   ├── weekly/                        # week01_report.md and week01_report_fa.md go here
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

Required `.gitignore` entries: `data/`, `*.parquet`, `*.csv`, `*.csv.gz`, `docs/papers/`, `wandb/`, `mlruns/`, `.ipynb_checkpoints/`, `__pycache__/`, `.env`, `.DS_Store`. The student reports this was checked and everything was pushed.

---

## 8. Notebooks

**`00_setup_check.ipynb`:** English intro (project, data, four pillars, guiding principle); creates the directory structure; AWS CLI install and S3 listing; conda env creation, `requirements.txt`, install with progress bar, `pip freeze | tee requirements.lock`, kernel registration; final check printing `sys.executable` and package versions.

**`01_pennylane_warmup.ipynb`** (kernel Python (dcpower)):
1. 4-qubit QNode on `default.qubit`: `AngleEmbedding` + `StronglyEntanglingLayers` (2 layers, weights shape (2, 4, 3) = 24 parameters) + `PauliZ` expectations; drawn at template and device level.
2. Converted to `qml.qnn.TorchLayer` (`interface="torch"`; the argument must be named `inputs`); batch output shape (2, 4).
3. Hybrid model `Linear(1,4) → TorchLayer → Linear(4,1)` (37 parameters = 8 + 24 + 5) trained on sin(x), x in [−π, π], 100 points, Adam lr 0.05, 100 epochs, seed 42, logged to W&B as `pennylane-sine-warmup`. **Loss 0.629 → about 0.0000.** This is a pipeline sanity check only, not evidence of quantum advantage.

---

## 9. Weights & Biases

- Account `aryan-faghirollahy`, entity `aryan-faghirollahy-uok`, project **`datacenter-power-forecasting`**. Runs logged: `setup-test` (dummy loss, 20 steps) and `pennylane-sine-warmup`.
- The first API key was pasted into a chat; the student was told to revoke it and generate a new one, then re-logged in with `wandb.login(relogin=True)`. Credentials are in `~/.netrc`. **The student reports revocation is done.**
- W&B creates `notebooks/wandb/` locally; `wandb/` must stay in `.gitignore`.

---

## 10. Paper archive (`docs/papers/`)

Downloaded with a Jupyter script (arXiv PDFs with 3-second pauses; open-access PDFs validated by `%PDF-` header and `%%EOF`). Last verification: **26 of 26 expected files OK, 2 pending university access.** Numbering is grouped by tens: 01–09 core, 10–19 QML for energy, 20–29 grid/control/UQ, 30+ non-arXiv.

| # | File | Source |
|---|---|---|
| 01 | `01_mit_supercloud_dataset_2108.02037.pdf` | arXiv |
| 02 | `02_alberta_ai_datacenter_stlf_2503.07756.pdf` | arXiv |
| 03 | `03_ai_datacenter_grid_impacts_2509.07218.pdf` | arXiv |
| 04 | `04_bowles_benchmarking_qml_2403.07059.pdf` | arXiv |
| 05 | `05_qrc_load_forecasting_2606.12806.pdf` | arXiv |
| 06 | `06_physics_aware_gpu_power_2605.04074.pdf` | arXiv |
| 10 | `10_wiser_eon_hybrid_qml_2605.24252.pdf` | arXiv |
| 11 | `11_pv_forecasting_qml_2312.16379.pdf` | browser (19 MB, above the 8 MiB limit) |
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

**Reading set for Week 1 (the "10 papers", all summarized):** 01 MIT Supercloud, 02 Alberta, 03 grid-impacts review, 04 Bowles, 36 Kaseb (journal article EPSR 2024 used, not thesis Chapter 4), 06 PI-DLinear, 05 QRC, 24 leakage, 32 Cerezo, 23 ACI. **Later:** summaries of 20 Choukse and 21 Ko in Week 7 (control). **Numbers only for the motivation section:** 34 NERC, 37 IEA, 35 EPRI. **Citation only, one line each:** 10, 11, 12, 13, 14, 15, 22, 30, 31, 33, 38, 39, 40.

---

## 11. Literature: what each paper gave us

**Where the text lives.** `docs/literature.md` (final, English, 10 entries in this order: 1 MIT, 2 Alberta, 3 Grid review, 4 Bowles, 5 Kaseb, 6 QRC, 7 PI-DLinear, 8 Leakage, 9 Cerezo, 10 ACI). Persian summary files `01_…` to `06_…` were written in session 1 (their numbering labels QRC "5" and PI-DLinear "6", which differs from the final order); `07_kaseb_summary.md`, `08_foundation_model_leakage_summary.md`, `09_cerezo_barren_plateaus_summary.md`, `10_aci_summary.md` were written in session 2. A Persian Claude Doc with the full translation of `literature.md` also exists (Section 13). All summaries were written from fetched paper text; unverified points carry a warning mark.

### 11.1 One-line takeaways and key numbers

| # | Paper | Takeaway for us |
|---|---|---|
| 1 | MIT Supercloud (Samsi et al., HPEC 2021) | Our data. 448 V100; per-job 100 ms GPU traces; job-attributed power only; median job 4 min; power corr. with utilization 0.92. |
| 2 | Alberta (arXiv 2503.07756, PES GM 2025) | Classical baseline. LSTM R² 0.9639, sMAPE 2.26% at **1 s**, 300-step window, **90 s** horizon; **no naive baseline**, single run, units unspecified; not comparable to our protocol. |
| 3 | Grid-impacts review (arXiv 2509.07218) | Motivation. IT > 60% of AI data center load; grid-side power follows IT power for slower variations (justifies GPU power as a minute-scale proxy); batteries suit minutes to hours; calls for forecasts with uncertainty and ramp estimates. |
| 4 | Bowles, Ahmed, Schuld (arXiv 2403.07059) | Out-of-the-box classical models beat 12 QML models on 160 datasets; removing entanglement often does not hurt; best quantum model is a "dressed" hybrid (our QCNN family); authors reject parameter-matched baselines as the sole comparison. |
| 5 | Kaseb et al. (EPSR 2024, DOI 10.1016/j.epsr.2024.110677) | Template for hybrid QCNN. Pure QNN vs QCNN vs NN on 4-bus power flow with 128 training points; test MSE 41% (QNN) and 52% (QCNN) lower than NN; but synthetic i.i.d. data, NN better tuned than quantum, no controls, no seeds. A design template, not evidence of advantage. |
| 6 | QRC for load forecasting (arXiv 2606.12806) | Template for QRC: fixed brickwork reservoir, 5N observables, exponential-kernel aggregation, Elastic Net readout, per-time-step state caching (about 60× fewer circuits); no classical-equivalent comparison seen. |
| 7 | PI-DLinear (arXiv 2605.04074) | **Closest prior work**: same data and same 1-min resolution (about 330.5k steps, 238 days); DLinear beats most transformers; look-back 4 to 10 h; horizons 5 to 80 min; no naive/GBM/LSTM baselines seen. |
| 8 | Meyer et al. (arXiv 2510.13654) | Leakage in TSFM evaluation. Only 6% of 401 datasets never appear in any pre-training corpus; two requirements (novel test data; test period after every model's pre-training cutoff). Our test period (autumn 2021) cannot satisfy the second. |
| 9 | Cerezo et al. (Nat. Commun. 16, 7907, 2025) | Perspective: BP-free structure tends to imply classical simulability. Asymptotic, not about our 4 to 8 qubits; supports cautious claims, shallow circuits with local Z readouts, entanglement ablation. |
| 10 | Gibbs and Candès (arXiv 2106.00170, NeurIPS 2021) | ACI: `α_{t+1} = α_t + γ(α − err_t)`; long-run coverage guarantee without distributional assumptions; guarantee is average-only, silent on width, and needs immediate labels. |

### 11.2 Cross-cutting conclusions

- **The gap we fill:** Alberta and PI-DLinear use the same data but lack naive baselines, ramp-specific metrics, foundation models, quantum models, calibrated uncertainty and control. We cover all of them.
- **Quantum framing:** RQ2 is "how does performance change with training-data fraction and qubit count, relative to the matched classical model", never "is quantum better". Models run on a classical simulator with 4 to 8 qubits; we make no computational-advantage claim.
- **Naming clash:** in Cerezo, "QCNN" means quantum convolutional neural network; in Kaseb and our project it means a hybrid network with classical layers around the circuit. In our paper use one defined name (e.g., "hybrid QNN").
- **Lineage check (done 1 Oct 2026):** the leakage paper's lineage table (401 datasets, 30 model columns, file `data/raw/tsfm_lineage.csv`) has **no** match for supercloud, mit, gpu, hpc, datacenter, slurm, marconi or pm100. The only cluster-workload datasets are Alibaba Cluster Trace 2018 and Borg Cluster Data 2011 (CloudOps, 5-minute frequency): both marked as pre-training data for **Toto and Moirai** (Borg also for **TiRex**), and for **none of Chronos, TimesFM or Chronos-2**. They are other systems and periods with no temporal overlap with our 2021 data, but Toto has seen cluster-type workloads, so its zero-shot results on our series are not from an entirely unseen domain. This is evidence of absence from documented public corpora only; proprietary pre-training data (e.g., Toto's) cannot be checked. This paragraph is already appended to entry 8 of `literature.md`.

---

## 12. Protocol and decisions

### 12.1 Fixed protocol (from the project plan; changes only with a recorded reason)

| Item | Value |
|---|---|
| Main data | MIT Supercloud, aggregate GPU power series |
| Backup data | PM100 (Marconi100), Zenodo record 8129258; also a second dataset to test rank stability |
| Resolution | 1 minute (10 seconds as a supplementary experiment) |
| Input window | 60 steps (60 minutes); longer look-back as a side experiment |
| Horizons | 1, 5 and 15 minutes |
| Split | Chronological 70/15/15, no shuffling |
| Normalization | Min-Max, fitted on training data only |
| Ramp definition | Steps where the absolute change in power exceeds the 95th percentile of the training data |
| Metrics | MAE, RMSE, sMAPE, R²; MAE in ramp periods; ramp-rate error |
| Repetition | At least 3 seeds; report mean and standard deviation |
| Quantum settings | 4 to 8 qubits; 1024 shots in the noise test; `lightning.qubit`; qubit sweep 2, 4, 6, 8 |
| Data fraction | 1%, 5%, 20% and 100% of the training data |
| Uncertainty | Target coverage 90% (α = 0.1) |

**Tool stack:** Polars, DuckDB, Parquet, AWS CLI (data); statsforecast, LightGBM, Optuna; PyTorch, neuralforecast (PatchTST, N-HiTS); Chronos, TimesFM, Toto; PennyLane (lightning.qubit) with Qiskit for noise models; MAPIE (optional), SHAP; cvxpy; Git, GitHub, W&B or MLflow, Docker (optional).

### 12.2 Decisions made in Week 1 (new relative to the project plan)

1. **Zero-shot foundation models** (Chronos, TimesFM, **Toto**, added to the list) are reported as **secondary** results with an explicit leakage caveat and a table of known pre-training sources. PM100 is run as a second dataset to check rank stability (our idea, not from the paper; inconsistent ranks would raise concern, but are not conclusive).
2. **Conformal prediction (RQ3):** report three methods: (a) static split conformal (calibration once on validation), (b) rolling-window split conformal (fixed α), (c) **ACI on top of (b)**. Fix the conformity score **before** looking at test data: a normalized score (absolute error divided by a scale estimate such as a rolling MAD of residuals) or CQR; ACI depends on score stationarity. α = 0.1, γ = 0.005 as the paper's starting point plus a sensitivity check (e.g., 0.001, 0.005, 0.01), chosen on validation. Report overall and rolling local coverage, interval width (the paper reports none), the α_t trajectory, and coverage separately for normal and ramp steps. Implement the one-line update directly and add an assert on Proposition 4.1's bound for the 1-minute horizon (about 0.0036 for T ≈ 50k, γ = 0.005, α = 0.1; own arithmetic). Clip α_t when building intervals to avoid infinite (α_t < 0) or empty (α_t > 1) sets and record this in Methods.
3. **Delayed feedback:** for the 5 and 15-minute horizons labels arrive late. Run **one ACI per horizon** with delayed updates (only errors up to t−h are used). The published guarantee does not cover this; report empirical coverage and do not claim the guarantee. Expectation (a hypothesis, not a result): average coverage near 90% but coverage on ramp steps below 90%. A possible extension, using regime proxies known before t (not the ramp itself), is stratified calibration.
4. **Quantum comparison:** keep both (a) strong, equally tuned classical baselines (LightGBM, LSTM) and (b) a same-architecture classical control (circuit replaced by a small classical layer), plus an **entanglement ablation**; report parameter counts but do not rely on them alone (Bowles rejects parameter matching as the sole criterion). **Equal hyperparameter budget** for all models; **log every quantum variant tried** (guards against positivity bias); treat input scaling as a hyperparameter (start at [−π/2, π/2]); avoid amplitude encoding; consider data re-uploading with trainable input scaling.
5. **QCNN design** (from Kaseb): linear encoder to n qubits, circuit, linear decoder; qubit sweep; shot-convergence check (choose 1024); noise sweep 0 to 10% via noise models; data-fraction experiment. Our architecture differs from Kaseb's (AngleEmbedding and StronglyEntanglingLayers in PennyLane versus ZFeatureMap and RealAmplitudes in Qiskit); state this in Methods. Decide whether the decoder output is bounded or not, and apply the same choice to the classical control (Kaseb's bounded output may act as a prior the plain NN lacked).
6. **Trainability hygiene** (from Cerezo): shallow circuits (2 to 4 layers) with **local Z readouts**; log gradient variance across the qubit sweep (2 to 8) as a cheap diagnostic (our idea); never extrapolate beyond 8 qubits. Suggested Limitations sentence: "Our circuits are small enough to be simulated exactly on classical hardware; we therefore make no claim about computational quantum advantage (cf. Cerezo et al., 2025)."
7. **QRC controls:** (1) Elastic Net on raw features, (2) size-matched classical random-feature reservoir with identical aggregation and readout, (3) QRC without entangling gates. Compute reservoir states once per time step (about 330k circuits) and aggregate over windows. Reuse the FakeTorino and FakeMarrakesh inference-only noise protocol; readout quantization is optional.
8. **Benchmark additions:** add **DLinear and NLinear** (available in neuralforecast); consider running PI-DLinear if its code is available; decide the look-back length after the ACF (keep 60 for the main comparison, add a longer look-back side experiment for the top classical models).
9. **Candidate features:** lagged temperature, a "sustained high utilization" indicator (throttling precursor), active and queued job counts from `slurm-log.csv`; use covariates only with lags to avoid leakage.
10. **Alberta comparison:** reproduce their settings (1 s, 300 steps, 90 s) on a subset **with a naive baseline**, or report the difference explicitly.
11. **Licence:** never commit raw or processed data; publish scripts only.

### 12.3 Agenda for supervisor Meeting 1 (end of Week 2)

From the project plan: approve the four RQs and scope; approve 1-minute resolution and 1/5/15-minute horizons; co-authorship and target venue; thesis format. New: (a) Alberta results not comparable, reproduce on a subset with a naive baseline or report the difference; (b) add DLinear and NLinear (and possibly PI-DLinear); (c) look-back 60 versus 4 to 10 h, decide after the ACF; (d) two kinds of quantum comparison given Bowles's critique of parameter matching; (e) QRC fair controls; (f) equal hyperparameter budget and logging of all quantum variants as protocol rules; (g) extra features; (h) zero-shot results as secondary because of leakage; (i) ACI delayed updates for the 5 and 15-minute horizons, empirical coverage only.

---

## 13. Deliverables and where they live

**Files produced (download folder / repo):**

| File | Content |
|---|---|
| `literature.md` | Final English literature notes, 10 entries plus the lineage check (goes to `docs/literature.md`) |
| `07_kaseb_summary.md`, `08_foundation_model_leakage_summary.md`, `09_cerezo_barren_plateaus_summary.md`, `10_aci_summary.md` | Persian summaries with English blocks; "with your own eyes" lists and likely supervisor questions |
| `week01_report.md`, `week01_report_fa.md` | Weekly report in the four-part template (goes to `docs/weekly/`); two placeholders: commit links and W&B run links |
| `HANDOFF_week1_complete.md` | This file |

**Claude Docs (editable, exportable to Word/PDF/Markdown):**
- Research proposal, tabs English and فارسی: https://claude.ai/code/artifact/1c7f11e9-9c9b-40f6-ad87-6c447798161e
- Weekly report Week 1, tabs English and فارسی: https://claude.ai/code/artifact/43bd3fb4-44f7-41f6-bbb2-ad521fef6980
- Persian literature notes (full translation of `literature.md` including the lineage check): https://claude.ai/code/artifact/7f4b45c1-a6eb-4dfb-ad32-3af630833e6d

**Proposal structure** (nine sections; section 7 is a drawn roadmap): 1 Motivation (IEA 415 to 945 TWh by 2030; NERC load-loss events of 100 to 1,500 MW); 2 Research questions (table); 3 Data; 4 Methods (table); 5 Evaluation protocol (table); 6 Positioning in related work; 7 Timeline (drawn roadmap of four sprints and four meetings; dates not set because the term start is unknown); 8 Expected outputs; 9 Open questions for the supervisor (task list). Open questions include: approval of RQs and scope; resolution and horizons; zero-shot as secondary; ACI with delayed updates; framing of the quantum comparison; co-authorship and venue; thesis format.

---

## 14. Week 1 completion record

| Item | Status |
|---|---|
| Completion criterion: S3 file listing seen | Done |
| Completion criterion: quantum circuit trains in PyTorch, loss decreases | Done |
| `docs/literature.md` with summaries (10 entries, exceeds the planned six) | Done |
| GitHub repository with full structure and `.gitignore` | Done (student pushed) |
| PennyLane test notebook | Done |
| Proposal draft (English and Persian) | Done |
| Exact S3 prefix recorded in README | Done |
| W&B key rotated | Done (student confirmed) |
| Foundation-model lineage check | Done |
| Weekly report (bilingual) | Done, placeholders to fill |

**Residual items (do not block Week 2):**
1. Fill the commit links and W&B run links in the weekly report (Doc and Markdown versions) and place the report in `docs/weekly/`.
2. Obtain the two pending papers (Colangelo, Nature Energy 2026; QMI 2026) through university access; both are citation-only.
3. Coordinate the date of Meeting 1 and send the proposal to the supervisor (the student chose to handle this himself; no email draft was written).
4. Optional: refresh the Persian literature Doc whenever `literature.md` changes (it is a translation, not linked).

---

## 15. Week 2 plan: data engineering and exploratory analysis

**Goal:** a reproducible, documented pipeline from raw data to the final 1-minute series, a deep understanding of load behaviour, and a finalized proposal ready for Meeting 1. **No modelling on the main data this week.** Sprint 1 target by the end of Week 2: proposal ready for approval, one clean series.

**Order of work (one step at a time, each a single Jupyter cell or action):**

1. **Download `slurm-log.csv`** (103,165,427 bytes) in Range chunks below 8 MiB (e.g., 4 MiB) with the VPN on, verify the final size equals 103,165,427 bytes, save to `data/raw/`. Inspect columns and the time range. Also download the small files: `labelled_jobids.csv`, `labelled_job_stats.csv`, `tres-mapping.txt`, `README.md`.
2. **Explore `gpu/`:** list one sub-folder, count files, check sizes (`--summarize --human-readable`). Download **one** GPU job CSV (Range chunks if above 8 MiB). Inspect columns, units, the sampling interval, whether `timestamp` is absolute, whether one file contains all GPUs of all nodes of the job, and what happens at job start and end.
3. **Link the tables:** map job IDs between a GPU file, `slurm-log.csv` and `labelled_jobids.csv`. Establish how to compute active and queued job counts per minute.
4. **Choose the development subset (one to two months):** use the slurm-log time range; estimate the number and size of GPU files needed; download only that subset (keep the full range for the final run).
5. **Write `src/data/build_series.py`** (Polars or DuckDB): read job files, align timestamps, **sum power over all GPUs and jobs**, resample to 1 minute (document the aggregation, e.g., mean power per minute), write Parquet. Record run time and input and output sizes in the README. Decide, with a written reason, how to treat minutes with no active job (zero versus missing); idle GPUs are probably absent from the data.
6. **Extract covariates per minute:** mean utilization, GPU temperature, memory use, number of active jobs (and queued jobs from slurm-log).
7. **Data quality:** find time gaps, negative or non-physical values and artificial jumps; for each, decide interpolate, drop or keep, with a reason; write `docs/data_quality.md`.
8. **EDA notebook** (`notebooks/02_eda.ipynb`, English text): full series with zoom to one day and one hour; daily and weekly profile (mean power by hour and weekday); ACF and PACF; distribution of ΔP and ramp statistics in kW/min; **ramp threshold = 95th percentile of |ΔP| on the training portion only (a single number)**; correlation of covariates with power (simultaneous and lagged). Supplementary: brief comparison with PM100.
9. **Finalize the proposal** and the fixed-decisions table for approval; build the five-minute EDA summary with four to five key plots.
10. **Weekly report 2** (Friday) and Meeting 1 preparation.

**Deliverables:** series-building script, EDA notebook, `docs/data_quality.md`, final proposal. **Completion criterion:** one command builds the whole series from the raw data, and the ramp threshold is a concrete number.

**Meeting 1 packet:** final proposal; four to five key EDA plots; the fixed-decisions table for approval; the data-quality report. Ask: approval of RQs and scope; resolution and horizons; co-authorship and target venue; thesis format. Decide: evaluation protocol final; main dataset (MIT Supercloud or PM100); target venue. After the meeting, record minutes in `docs/meetings/` and update the plan the same day.

**Packages:** `statsmodels` (ACF/PACF), `matplotlib`, `polars`, `duckdb`, `pyarrow` and `awscli` are already installed. If another library is needed (e.g., a plotting helper), install it in the `dcpower` environment and refresh `requirements.lock`.

---

## 16. Risks, unverified items, watch-outs

**Risks (from the plan, with signals and actions)**

| Risk | Signal | Action |
|---|---|---|
| Data access or size | Download or processing exceeds 3 days | Use a smaller subset or switch to PM100 |
| Slow quantum simulation (Sprint 3) | An epoch above about 10 minutes | Fewer qubits or subsample, focus on QRC, `lightning.gpu` on Colab |
| Negative quantum result | Quantum loses everywhere | Report honestly with focus on parameter efficiency and ablation |
| Cannot reproduce Alberta numbers | Large gap to reported values | Document setting differences and continue with our protocol |
| Data leakage | Unrealistically good results | Re-check chronological split and train-only normalization |
| Falling behind | More than 3 days late | Drop supplementary items in the order: QAOA, dashboard, quantum kernel, fine-tuning |

**Unverified items (carry a warning mark in the summaries; do not cite as facts)**
- QRC paper's exact title (listed as "Quantum Reservoir Computing for Short-Term Load Forecasting" from project notes) and whether it has any classical-equivalent comparison in sections not read.
- Grid-impacts review's full title (only "Electricity Demand and Grid Impacts of AI Data Centers" is known).
- PI-DLinear: baselines in the full results table; whether code is available.
- Kaseb: whether the PQC replaces a classical hidden layer or is added to the same count of layers; whether the NN had the same bounded output; learning-rate and tuning budget of the quantum models; thesis Chapter 4 was not compared with the journal article.
- Cerezo: Tables 1 and 2 and the supplement were not read; whether classical data encoding (AngleEmbedding, re-uploading) is covered; the text was cut off in the middle of the caveats section (the rest is known only from the abstract).
- ACI: the improved JMLR 2024 version (online step-size selection) was not read; whether MAPIE implements ACI is unchecked; ACI guarantee under delayed feedback is not proven.
- Lineage check: a reading of the CSV columns (Chronos and TimesFM columns empty for both cluster datasets was confirmed by the printed table).
- All data facts in Section 6.3.

**Watch-outs**
- Numbering mismatch between the old summary files (`05` QRC, `06` PI-DLinear) and the final order in `literature.md` (6 QRC, 7 PI-DLinear).
- Do not add sections or dates to the proposal that were not discussed; the term start date and Meeting 1 date are still unknown.
- Persian Docs are translations; keep them in sync only when asked.
- The assistant cannot read the student's disk; always ask for printed outputs.
- Weekly report convention: one every Friday (Week 1's was due Friday 2 Oct 2026, so Week 2's falls on Friday 9 Oct 2026); use the same four-part template.

---

## 17. Problems encountered and how they were solved

| Problem | Cause | Solution |
|---|---|---|
| `SyntaxError` on `aws s3 ls` | Shell command in a Python cell | Prefix with `!` or use `%%bash` |
| pip `NewConnectionError` | Router DNS cannot resolve `files.pythonhosted.org`; public DNS and DoH blocked | VPN on; restart Jupyter |
| Conda env not created | Default channel needs terms-of-service acceptance | `conda-forge --override-channels` |
| `ModuleNotFoundError: torch` | Wrong kernel (cell counter kept increasing) | Kernel, Change Kernel, `Python (dcpower)` |
| W&B "Invalid choice" | API key pasted at the menu prompt | Choose option `2`, then paste the key; rotate the exposed key |
| PDF download fails at 8 MiB | VPN truncation | Browser download or chunked Range requests |
| Kaseb thesis not found online | Not published at the expected link | Used the student's local copy |
| Lineage CSV not readable by the assistant | GitHub blocks automated access | Student downloaded it with Python `urllib` and a `User-Agent`; output pasted back |
| First attempt at the S3 listing printed only `202201/` | Dataset is one level down | Listed `datacenter-challenge/202201/` and recorded the prefix |

---

## 18. Opening prompt for the new chat (Week 2)

**Upload these files to the new chat:**
1. `Project_Context_FA.txt` (master plan, v1.0)
2. `HANDOFF_week1_complete.md` (this file)
3. Optional: `literature.md` and the two `week01_report*.md` files for reference. (`HANDOFF_week1_session1.md` and `GUIDE_week1_continue.md` are superseded by this file; do not upload them.)

**Paste this as the first message:**

```text
I am continuing my bachelor project at the start of Week 2 (data engineering and exploratory analysis). Read the attached files first: Project_Context_FA.txt (master plan) and HANDOFF_week1_complete.md (everything done in Week 1, all decisions, and the detailed Week 2 plan in Section 15).

Rules:
- Answer me in Persian. Anything written into notebooks, code comments or repo docs must be in English.
- Go ONE STEP AT A TIME: one cell or one action per message, then wait for my pasted output. No multi-cell batches unless I ask.
- Answer in chat; create a file or document only when I ask or when it is clearly a deliverable.
- I run everything in local Jupyter on my Mac (conda env dcpower, kernel "Python (dcpower)"). The VPN must be on for downloads and installs, and it truncates any single download at 8 MiB, so large files need Range chunks below 8 MiB. Restart Jupyter after toggling the VPN.
- I handle Git myself; do not give Git instructions. Just remind me about .gitignore for data.
- Keep claims cautious: mark unverified points, never claim quantum advantage, and tell me when a source could not be read.
- Tell me explicitly when Week 2 is complete.

Week 1 is complete. Start with step 1 of Section 15: download slurm-log.csv (103,165,427 bytes) in Range chunks below 8 MiB from s3://mit-supercloud-dataset/datacenter-challenge/202201/ into data/raw/, verify its size, and inspect its columns. Give me only the first cell.
```
