# Literature notes

Project: Forecasting AI Data Center Power Demand: A Benchmark of Classical, Foundation, and Hybrid Quantum Models with Uncertainty Quantification and Grid-Aware Control

Each entry follows the same structure: Idea, Method, Results, Weaknesses / Notes. Items marked with a warning sign in the Persian summary files were not verified against the full text; check them before citing.

| # | Paper | Role in the project |
|---|---|---|
| 1 | MIT Supercloud Dataset | Data |
| 2 | Alberta, STLF for AI data center | Classical baseline |
| 3 | Grid impacts of AI data centers (review) | Motivation |
| 4 | Bowles et al., Better than classical? | Fair QML benchmarking rules |
| 5 | Kaseb et al., QNN for power flow | Template for hybrid QCNN |
| 6 | Quantum Reservoir Computing for STLF | Template for QRC model |
| 7 | Physics-aware GPU power forecasting (PI-DLinear) | Closest prior work |
| 8 | Meyer et al., TSFM information leakage | Validity of zero-shot foundation models |
| 9 | Cerezo et al., barren plateaus and simulability | Cautious claims, entanglement ablation |
| 10 | Gibbs and Candès, Adaptive Conformal Inference | Method for RQ3 |

---

## 1. The MIT Supercloud Dataset (Samsi et al., HPEC 2021)

**Link:** https://arxiv.org/abs/2108.02037

**Idea:** Introduces a public, anonymized monitoring dataset from the MIT Supercloud TX-Gaia cluster to enable AI/ML research on datacenter operations (failure prediction, scheduling, power optimization). Not a method paper.

**Method:** Data collected from 224 GPU nodes (2x NVIDIA V100 32GB each, 448 GPUs total) plus 480 CPU-only nodes. Sources: Slurm scheduler logs, CPU time series (10 s, Slurm hdf5 plugin), GPU time series via nvidia-smi (100 ms), node data (5 min), Lustre logs, and facility sensors (EcoPOD). GPU recording starts/stops with each job (Slurm prolog/epilog) and covers only GPUs allocated to a job. Outliers and incomplete data removed; job/user/node IDs hashed with salted SHA-256 while preserving cross-table linkage.

**Results:** Median GPU job runtime ~4 min; 85% of jobs use a single GPU; median SM utilization of single-GPU jobs is 23%; many 2-GPU and multi-node jobs show near-zero utilization (development/debugging). Per-job average GPU power has high Spearman correlation with SM utilization (0.92), memory utilization (0.95), and GPU/memory temperature (0.92/0.93), but low with memory used (0.2).

**Weaknesses / Notes for this project:**
- Our target series is job-attributed GPU power, not total facility power: idle GPUs, CPUs, network, storage, and cooling are excluded (inferred from job-scoped collection; to verify on data in Week 2).
- GPU `timestamp` appears absolute, unlike CPU `ElapsedTime` (relative to job start) — verify on a real file.
- Short jobs (median ~4 min) imply frequent start/stop transitions, a likely driver of the ramp events we target; active/queued job counts from slurm-log are candidate features.
- Utilization and temperature are strong covariates but must be used only with lags to avoid leakage.
- Outlier-removal criteria are not documented.
- Small scale (~45 kW peak, V100 era) vs. modern MW-scale AI datacenters; generalization to be discussed and tested on PM100.
- Facility sensor data (total power, PUE) is described in the paper but was not in the public AWS listing we inspected.

---

## 2. Short-Term Load Forecasting for AI-Data Center (Alberta, IEEE PES GM 2025)

**Link:** https://arxiv.org/abs/2503.07756

**Idea:** AI-centric datacenters exhibit fast, large power fluctuations that challenge grid operation; explicit physical modeling is hard, so the authors propose a data-driven three-stage workflow (data collection/pre-processing → model training → forecasting/application) for short-term GPU power forecasting. Accepted at IEEE PES GM 2025.

**Method:** MIT Supercloud GPU power (Feb–Oct 2021, 448 V100 GPUs, aggregated peak ~45 kW), aggregated by job and node at **1-second** resolution, Min-Max normalized. Multi-step forecasting with a **300-step look-back** and a **90-second horizon**, trained with MSE over the horizon. Chronological 70/15/15 train/validation/test split. Models: LSTM (with fully connected output), GRU, 1D-CNN.

**Results:** LSTM best on all metrics: RMSE 0.5624, MAE 0.2916, sMAPE 2.26%, R² 0.9639; GRU R² 0.9634 (slight negative bias), 1D-CNN R² 0.9618 (noticeable positive bias). Qualitatively, all models lag behind sudden spikes and dips. The authors argue a 90 s horizon suits primary (2–10 s) and secondary/AGC (10–30 s) control, and suggest BESS-based peak shaving without implementing it.

**Weaknesses / Notes for this project:**
- No naive/persistence or statistical baseline — at 1 s resolution a persistence model may already achieve very high R²; the value of the deep models is therefore unverified.
- Single run, no seeds, no variance, no significance testing; differences between models (R² 0.9618–0.9639) are likely not significant.
- Missing reproducibility details: architecture sizes, epochs, optimizer, learning rate; no code released.
- Metric units (kW vs. normalized) and whether metrics are averaged over the 90-step horizon are unspecified; unclear whether the scaler was fit on training data only.
- Ramp/spike error is acknowledged qualitatively but not measured — our ramp-specific metrics address this directly.
- No uncertainty quantification (our RQ3) and no implemented control application (our RQ4).
- Settings differ from our protocol (1 s / 300 / 90 s vs. 1 min / 60 / 1–5–15 min): their numbers are **not directly comparable** to ours. Plan: reproduce under their settings on a subset, including a naive baseline, and discuss with supervisor in Meeting 1.
- Intro frames the problem around LLM training, but the MIT workload is mostly vision CNNs, U-Nets, and small BERT fine-tuning.

---

## 3. Electricity Demand and Grid Impacts of AI Data Centers (review)

**Link:** https://arxiv.org/abs/2509.07218

**Idea:** Comprehensive review of AI data center infrastructure, electricity demand patterns across the AI lifecycle (preparation, training, fine-tuning, inference), grid challenges across three timescales (long-term planning/interconnection; short-term operation/markets; real-time dynamics/stability), and solutions from grid, data center, and end-user perspectives. Review paper, no original forecasting experiments (arXiv v6, Aug 2026).

**Method:** Literature and industry synthesis, plus illustrative analyses: FFT of measured GPU power traces across lifecycle stages, and PSCAD EMT simulations of an open-source AI data center model (Texas A&M/ERCOT) to compare device-level IT power with facility grid-side power.

**Results:** AI data centers are characterized by high power density (30–100+ kW/rack vs. 7–10 kW), fast and large variability, power-electronic grid interfaces, and geographic concentration. IT accounts for >60% of AI data center load. Training shows sustained high load with large compute/communication swings; fine-tuning and inference are bursty. Grid impacts include higher reserve needs, capacity price increases (PJM: $28.92 → $329.17/MW-day), and correlated load-trip risks (e.g., 60 of 200+ Virginia data centers disconnecting in 2024; simulated 2,700 MW trip raising ERCOT frequency to 60.4 Hz). EMT simulations show grid-side power closely follows IT power for slower variations; fast fluctuations depend on converter control. Hybrid storage roles: supercapacitors (ms–s), flywheels (s–min), batteries (min–h). Forecasting should be workload-aware and collaborative, sharing aggregated forecasts, ramp-rate estimates, and uncertainty bands.

**Weaknesses / Notes for this project:**
- Review only; cite primary sources (IEA, EPRI, NERC) for key statistics; some figures (e.g., GPT-4 training energy) are third-party estimates.
- Explicitly lists MIT Supercloud and PM100 as available datasets but notes they are lab-scale/GPU-level rather than full commercial operational data — supports our limitations section.
- EMT result justifies using aggregated GPU power as a proxy for grid-side demand at minute resolution.
- Storage timescale mapping justifies a battery model for our 1-minute MPC (RQ4).
- Call for uncertainty bands and ramp-rate estimates directly motivates RQ3 and our ramp-specific metrics; workload-aware forecasting motivates using slurm-log features (active/queued jobs).

---

## 4. Better than classical? The subtle art of benchmarking quantum machine learning models (Bowles, Ahmed, Schuld, 2024)

**Link:** https://arxiv.org/abs/2403.07059

**Idea:** Benchmarks in QML are highly sensitive to experimental design and subject to a systematic positivity bias (of 55 arXiv papers claiming "outperform", ~40% claim quantum beats classical; only 3 report otherwise). The authors run a large, carefully designed benchmark to find robust signals about which QML design ideas matter, rather than to crown a winner.

**Method:** 12 influential QML models (quantum neural networks, quantum kernels, quantum CNNs), selected via a randomized, bias-aware procedure from ~3500 papers, compared with out-of-the-box classical models (MLP, Gaussian-kernel SVM, simple CNN) on 160 synthetic binary classification datasets (6 generators, 10 benchmarks). Full grid-search hyperparameter tuning with 5-fold CV for every model. Ablations remove entanglement (separable variants). PennyLane + JAX + scikit-learn; up to 18 qubits. Code: github.com/XanaduAI/qml-benchmarks.

**Results:** Out-of-the-box classical models systematically outperform the quantum models; rankings are consistent across benchmarks. The best quantum model is the hybrid DressedQuantumCircuitClassifier (classical NN → quantum circuit → classical NN), which closely tracks the MLP. Removing entanglement often yields equal or better performance (no drop for the dressed circuit; drops for data re-uploading and QBM). Amplitude-encoding models perform worst; almost all quantum models fail on linearly separable data, plausibly due to the periodic inductive bias of angle embedding. Trainable input scaling and trainable embedding are key to data re-uploading's performance. Hyperparameters swing accuracy from chance to near-perfect.

**Weaknesses / Notes for this project:**
- Scope limited to binary classification on small synthetic data; no regression or time series — our application tests whether the findings transfer.
- Authors explicitly reject parameter-matched classical baselines as not meaningful. Our protocol keeps both: (1) strong, equally tuned classical baselines (LightGBM, LSTM) for competitiveness, and (2) a same-architecture classical control (circuit replaced by a classical layer) to isolate the circuit's contribution — mirroring their Sec. VI.A. Parameter counts are reported, not used as the sole criterion. Discuss with supervisor in Meeting 1.
- Our hybrid QCNN belongs to the dressed-circuit family: expect performance close to its classical host.
- Adopt: equal hyperparameter budget across all models; log every quantum variant tried (guards against positivity bias); entanglement ablation as a standard experiment; treat input scaling as a hyperparameter (start at [-π/2, π/2]); consider data re-uploading with trainable input scaling; avoid amplitude encoding.
- Frame RQ2 as "how does performance change with training-data fraction and qubit count, relative to the matched classical model?" rather than "is quantum better?".

---

## 5. Kaseb et al. — Quantum neural networks for power flow analysis (EPSR 2024)

**Link:** https://doi.org/10.1016/j.epsr.2024.110677

**Idea.** First systematic study of quantum and hybrid quantum–classical neural networks as surrogates for AC power flow (inputs p, q per bus; outputs voltage magnitude and angle, labels from Newton–Raphson). Compares a classical NN, a pure QNN and a hybrid QCNN on five criteria: generalization, robustness to noisy training data, training-set size needed, training error, and training stability.

**Method.** Qiskit Aer simulator only. QNN: ZFeatureMap (H + Rz with arctan scaling) → RealAmplitudes ansatz (Ry–CNOT–Ry, 4n parameters) → Z measurements → linear rescaling to physical ranges. QCNN: linear encoder → PQC → linear decoder. 4-bus main case, 33-bus supplementary. 512 synthetic samples (25/25/50 split, so 128 training points), 1000 epochs, Adam, MSE, 6 qubits (chosen from a 1–7 sweep), 1024 shots (chosen by convergence), hardware-noise sweep 0–10% on the QNN. The classical NN (7 hidden layers) was tuned by sensitivity analysis over depth, learning rate, weight decay and dropout.

**Results.** With 128 noisy training points, test MSE is 41% (QNN) and 52% (QCNN) lower than the NN; training MSE is 48% and 54% lower. The NN needs about 4x more data to approach the QCNN and is still worse. The NN degrades by up to 2x as training noise grows. On the 33-bus system under extreme voltage conditions the QCNN reaches a maximum test MSE of 0.011 p.u. versus 0.032 p.u. for the NN.

**Weaknesses.** Synthetic i.i.d. data, tiny systems, simulator only (authors say NISQ hardware is impractical today and that added value needs further study). Tuning effort is documented for the NN but not for the quantum models; parameter counts and a same-architecture classical control are not reported; no multiple seeds or significance tests are mentioned; the quantum output is bounded by a physical-range rescaling that may act as a prior the NN lacks; no entanglement ablation; the "stability" metric is within-run loss variation, not variance across seeds.

**Use in our project.** Template for the hybrid QCNN (linear encoder–PQC–linear decoder), the qubit-count sweep, the shot-convergence check, the noise sweep and the data-fraction experiment. We add what it lacks: a same-architecture classical control, an entanglement ablation, an equal tuning budget, three seeds, and Diebold–Mariano tests. It is a design template, not evidence of advantage.

---

## 6. Quantum Reservoir Computing for Short-Term Load Forecasting (arXiv 2606.12806)

**Link:** https://arxiv.org/abs/2606.12806

**Idea:** Hardware-efficient Quantum Reservoir Computing (QRC) for short-term load forecasting aimed at memory-constrained edge deployment: a fixed, untrained quantum circuit maps input windows to features, and only a classical Elastic Net readout is trained, then compressed by post-training fixed-point quantization. The question is resource efficiency and noise robustness, not quantum-vs-classical superiority.

**Method:** Hourly Tetouan (~8.4k samples, 11 features) and Spain (~35k samples, 13 features) load data with cyclical time features and lags (t−1, t−24); 24-step windows, one-step-ahead target; chronological 70/10/20 split, train-only scaling. Per time step: RZ(πx)·RY(πx) encoding with layer-dependent shift, fixed Haar-random Rot gates, brickwork CNOT–RZ(κ)–CNOT entanglers; measure ⟨X⟩,⟨Y⟩,⟨Z⟩ and nearest-neighbor ⟨ZZ⟩,⟨XX⟩ (5N observables). Temporal aggregation with 5 exponential-decay kernels + terminal state + drift (245 features for N=7). Elastic Net readout (5-fold CV). Reservoir configuration chosen by a small genetic algorithm. Readout quantized to 8/6/4/3/2 bits with ridge residual refinement. Evaluated under exact statevector, 512 shots, and IBM FakeTorino/FakeMarrakesh noise models (1024 shots, inference only). PennyLane lightning.qubit on Colab A100; 2 seeds.

**Results:** 6-bit readout preserves full-precision accuracy with 81.2% readout memory reduction; below 6 bits, degradation is dataset-dependent (Tetouan more sensitive). A readout trained on noiseless reservoir states transfers to noisy hardware-model states without retraining.

**Weaknesses / Notes for this project:**
- No accuracy comparison against an equivalent classical model (Elastic Net on raw features, classical random-feature reservoir/ESN, or LSTM) in the sections read — the quantum contribution is therefore unverified (verify in full results). No entanglement ablation; only 2 seeds; one-step-ahead hourly only.
- "2^N-dimensional feature space" claim is misleading: the readout sees only 5N measured observables.
- Our template for the QRC model (architecture and tooling match our stack). Key efficiency trick: per-time-step reservoir states depend only on that step's input, so compute them once per time step (~330k circuits for our 1-min series) and aggregate over windows (~60× fewer circuit evaluations than per-window computation).
- Fair-comparison plan for our QRC: (1) Elastic Net on raw features, (2) size-matched classical random-feature reservoir with identical aggregation and readout, (3) QRC without entangling gates.
- Their FakeTorino/FakeMarrakesh inference-only protocol can be reused for our hardware-noise experiment; peak-load MAPE parallels our ramp-specific metrics. Readout quantization is optional for us.

---

## 7. A Physics-Aware Framework for Short-Term GPU Power Forecasting of AI Data Centers (PI-DLinear)

**Link:** https://arxiv.org/abs/2605.04074 (also ACM DL, DOI 10.1145/3770855.3818033)

**Idea:** Pure deep models can produce physically inconsistent forecasts and degrade under throttling and abrupt load changes. The authors embed a two-node lumped thermal RC model (GPU and memory nodes, Newton's law of cooling) and a throttling constraint as differentiable loss terms in DLinear, yielding PI-DLinear for 5–80 min ahead GPU power forecasting.

**Method:** MIT Supercloud aggregated GPU power at **1-minute** resolution (~330.5k steps, ~238 days, 7 covariates reduced to 5: GPU/memory utilization, GPU/memory temperature, power), Min-Max scaled on training statistics. Look-back windows 240/360/480/600 min; horizons 5/10/20/40/80 min. 16 TSLib baselines (Transformer variants, PatchTST, TiDE, TSMixer, DLinear, NLinear, etc.); best model (DLinear) extended with physics loss (RC parameters fitted via recursive least squares on training data; ambient fixed at 27 °C) and a throttling loss (no predicted power increase when utilization > ~90%), with self-adaptive loss weights. 5 seeds.

**Results:** Lightweight linear models outperform most transformers. PI-DLinear is best overall, improving (averaged over windows/horizons) MSE by 0.78–39.1%, MAE by 0.99–51.8%, and RMSE by 0.37–22.3% vs. baselines; errors remain stable as look-back grows; better behavior in throttling and transient examples. Observation: throttling-like drops (>15%) correlate with sustained high utilization rather than critical temperatures.

**Weaknesses / Notes for this project:**
- **Closest prior work**: same dataset and same 1-min resolution as our protocol (our dataset statistics match theirs); horizons overlap (5 min). More directly comparable than the Alberta paper.
- No naive/statistical/GBM/LSTM baselines seen in the sections read (verify in full table); wide improvement range suggests small gains over the strongest baselines; no significance testing.
- Lumped RC model applied to aggregated temperatures of 448 GPUs is a strong simplification; fitted R/C are effectively regression parameters.
- No uncertainty quantification, no ramp-specific metrics, no foundation models, no control application — all covered by our RQ1–RQ4.
- Action items: add DLinear/NLinear to our RQ1 benchmark; revisit the 60-min look-back (they use 4–10 h) via ACF and a side experiment; consider temperature lags and sustained-high-utilization features; check code availability to include PI-DLinear as a reference model.

---

## 8. Meyer et al. — Rethinking Evaluation in the Era of Time Series Foundation Models: (Un)known Information Leakage Challenges (arXiv 2510.13654, v3, Feb 2026)

**Link:** https://arxiv.org/abs/2510.13654

**Idea.** Position paper arguing that zero-shot evaluation of time series foundation models (TSFMs) is vulnerable to two kinds of information leakage: (1) direct train–test sample overlap caused by reusing public datasets for both pre-training and testing, and (2) indirect leakage from temporally overlapping but nominally disjoint series that share a common driver.

**Method.** Dataset-lineage analysis of 22 TSFMs and 401 datasets; case studies of documented overlaps (e.g., Electricity and Spain in energy-load benchmarks); a re-analysis of a deliberate Moirai leakage experiment (0.1% leaked data); and a controlled experiment training a small Time-Series Transformer from scratch with or without seven stock indices covering the 2020 COVID crash, tested zero-shot on the S&P 500. Proposes two requirements: test data must be provably novel, and the test period must start after the latest pre-training timestamp of every compared model.

**Results.** Only 6% of the 401 datasets never appear in any model's pre-training or fine-tuning corpus. Documented leakage cases show large apparent gains (e.g., 47–184% lower MSE on leaked datasets versus 0.3–14% advantage on clean ones). In the COVID experiment the leaky model had a lower MAE during the crash period (about 190 vs 217, better in 8 of 10 seeds) and no advantage afterwards (about 108.7 vs 108.3). The Madrid transport example shows about 43% lower MAE from temporally overlapping correlated series.

**Weaknesses.** Position paper; no production TSFM is tested for temporal leakage, and the authors acknowledge the real effect size is unknown. The COVID experiment uses one event and one target series, reports no significance test, and two seeds go the other way. The Moirai experiment is borrowed from prior work. Lineage relies on reading papers and code, so provenance cannot be verified independently. Requirement 2 is hard to satisfy for historical benchmarks and no concrete benchmark is provided. No leakage-detection method is offered. It does not mention data-center power data.

**Use in our project.** Zero-shot Chronos, TimesFM and Toto results are reported as secondary, with an explicit leakage caveat and a table of known pre-training sources (to be verified from the lineage CSV and model cards). Our test period (autumn 2021) precedes every model's pre-training cutoff, so Requirement 2 cannot be met; this is stated as a limitation. Models we train ourselves use a strict chronological split with train-only normalization, which this paper does not call into question. PM100 is used as a second dataset to check rank stability.

**Lineage check (1 Oct 2026).** We downloaded the paper's lineage table (401 datasets, 30 model columns) and searched all columns for supercloud, mit, gpu, hpc, datacenter, slurm, marconi and pm100: no hits. The only cluster-workload datasets listed are Alibaba Cluster Trace 2018 and Borg Cluster Data 2011 (CloudOps, 5-minute frequency). Both are marked as pre-training data for Toto and Moirai (Borg also for TiRex), and for none of Chronos, TimesFM or Chronos-2. They are CPU/memory-type traces from other systems and periods, with no temporal overlap with our 2021 data, but Toto has seen cluster-type workloads, so its zero-shot results on our series are not from an entirely unseen domain. This is evidence of absence from documented public corpora only; proprietary pre-training data (e.g., for Toto) cannot be checked.

---

## 9. Cerezo et al. — Does provable absence of barren plateaus imply classical simulability? (Nature Communications 16, 7907, 2025)

**Link:** https://www.nature.com/articles/s41467-025-63099-6

**Idea.** Perspective article arguing that the structure which lets variational quantum circuits avoid barren plateaus (BPs) can also be used to simulate their loss classically. BPs arise from a curse of dimensionality (the loss is an inner product of two objects in an exponentially large operator space); the known ways to avoid them confine the problem to a polynomially large, classically identifiable subspace, which in turn enables classical simulation, possibly with a one-off round of data collected from a quantum device.

**Method.** Case-by-case analysis of widely used provably BP-free architectures (shallow hardware-efficient ansätze with local measurements, symmetry-constrained circuits, quantum convolutional networks, small-angle initialization, etc.). Defines classical simulation (CSIM), quantum-enhanced classical simulation (QESIM, a one-time quantum data-acquisition phase followed by classical processing) and quantum simulation (QSIM). Two claims: BP-free standard architectures live in polynomial subspaces (Claim 1), and problems in known polynomial subspaces are in CSIM or QESIM (Claim 2). Shallow circuits with a local observable are the worked example (the loss depends only on gates in the measurement's backward light cone).

**Results.** For all BP-free families analyzed, the parametrized circuit does not appear to need to run on quantum hardware to estimate the loss in polynomial time; a quantum device may still be needed once to acquire data. The authors read this as casting doubt on the non-classical information processing of BP-free models, while stressing quantum computers can be essential for data collection.

**Weaknesses.** A perspective rather than a proof: the claims rest on case-by-case evidence, do not cover all learning protocols, and the authors give contrived (cryptography-based) non-concentrated, non-simulable counterexamples and note heuristic surprises could appear on larger devices. Statements are asymptotic and average-case; polynomial-time simulation may still be expensive, leaving room for polynomial advantages. Nothing is said about accuracy, generalization, or time-series data, and the text read treats a fixed input state, so handling of classical data encoding was not checked.

**Use in our project.** Supports a cautious claim structure: our 4–8 qubit hybrid models run on a classical simulator, so we make no claim about computational quantum advantage and interpret any gain over the same-architecture classical control as an inductive-bias or parameter/data-efficiency effect. Motivates keeping circuits shallow with local Z readouts, motivates the entanglement ablation (together with Bowles et al.), and suggests logging gradient variance in the qubit-count sweep as an inexpensive trainability diagnostic.

---

## 10. Gibbs and Candès — Adaptive Conformal Inference Under Distribution Shift (arXiv 2106.00170, NeurIPS 2021)

**Link:** https://arxiv.org/abs/2106.00170

**Idea.** Split conformal prediction guarantees coverage only under exchangeability, which fails for time series with distribution shift. Adaptive conformal inference (ACI) wraps any point predictor or quantile model and tracks a single online parameter, the effective miscoverage level α_t, so that long-run coverage frequency matches the target without assumptions on the data-generating process.

**Method.** After each step, set `α_{t+1} = α_t + γ(α − err_t)`, where `err_t` indicates whether `Y_t` fell outside the prediction set built at level α_t (equivalent to online gradient descent on the pinball loss). The step size γ trades adaptability against stability. Proposition 4.1 gives a distribution-free bound `|(1/T)Σ err_t − α| ≤ (max{α_1, 1−α_1} + γ)/(Tγ)`; Theorems 4.1–4.2 give tighter concentration and approximate marginal coverage under a hidden-Markov model with a fixed quantile function. Experiments: stock-volatility forecasting (GARCH(1,1) refit on the last 1250 days, normalized score, α = 0.1, γ = 0.005) and election-night county vote forecasting (conformalized quantile regression).

**Results.** The non-adaptive baseline (α fixed, calibration window refreshed each step) drifts far from the 90% target during regime changes such as the 2008 crisis and time-zone transitions in the election data; ACI keeps local coverage within the variation of an i.i.d. Bernoulli sequence. Coverage degrades markedly with an unnormalized conformity score, showing the method depends on the score being approximately stationary.

**Weaknesses.** The guarantee is for long-run average coverage only, not local or conditional coverage, and says nothing about interval width (α_t < 0 yields infinite intervals and α_t > 1 empty sets); no width results are reported. The finer theory is limited to a hidden-Markov model with a fixed quantile function. The method requires the label to be revealed at every step (delayed feedback is listed as open). Experiments cover only two domains with one α and one γ, and four of twelve stocks were shown because the baseline failed clearly. No comparison with other adaptive methods is given.

**Use in our project.** Method for RQ3. We report static split conformal, rolling-window split conformal and ACI on top of the rolling variant, with a pre-fixed normalized (or CQR) conformity score and α = 0.1. For horizons of 5 and 15 minutes we run one ACI per horizon with delayed updates (not covered by the proven guarantee) and report empirical coverage, rolling local coverage, interval width, the α_t trajectory, and coverage separately on normal and ramp steps. We expect average coverage to approach 90% but coverage on ramp steps to stay lower, and will report this honestly.
