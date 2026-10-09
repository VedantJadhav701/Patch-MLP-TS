# Patch-MLP-TS — Results, Claims, and Paper Table Plan

## Purpose

This document defines exactly what experimental results should be included in the Patch-MLP-TS paper, which claims are currently supported, which claims must be avoided, and the tables/figures required for a publication-quality manuscript.

**Important:** The uploaded result files contain multiple Patch-MLP-TS experimental generations with materially different results and parameter counts. They must **not** be mixed into one final paper. Before submission, select and reproduce one canonical implementation/configuration and regenerate the affected experiments with the same codebase.

---

# 1. Recommended Paper Positioning

## Proposed paper direction

> **Patch-MLP-TS: An Efficient Attention-Free Architecture for Long-Term Time-Series Forecasting**

The paper should be positioned as an **efficiency-oriented, attention-free forecasting architecture**, not as a universal replacement for PatchTST or DLinear.

### Central research question

> Can a patch-based MLP architecture provide competitive long-term forecasting accuracy while reducing the computational cost associated with attention-based forecasting models?

### Secondary research questions

1. Does channel-independent processing outperform explicit channel mixing for the proposed architecture?
2. Does temporal patching consistently improve forecasting performance?
3. How does patch size affect long-horizon forecasting?
4. How does the proposed architecture trade forecasting accuracy against parameter count, training time, and inference latency?
5. Why does the architecture behave differently on low-dimensional ETT datasets versus the high-dimensional Electricity dataset?

---

# 2. Current Experimental Evidence

## Main benchmark

The main ETT benchmark contains:

- ETTh1
- ETTh2
- ETTm1
- ETTm2

with horizons:

- 96
- 192
- 336
- 720

and models:

- Linear
- NLinear
- DLinear
- PatchTST
- PatchMLPTS

The aggregate ETT results currently show that PatchMLPTS is **competitive with PatchTST**, but does not universally outperform Linear or DLinear.

Approximate average ETT MSE across the 16 dataset/horizon configurations:

| Model | Average MSE |
|---|---:|
| DLinear | **0.3398** |
| Linear | 0.3408 |
| PatchTST | 0.3457 |
| **PatchMLPTS** | **0.3460** |
| NLinear | 0.3553 |

This supports a **competitive accuracy** claim, not a **state-of-the-art** claim.

---

# 3. Dataset-Level Interpretation

## ETTh1

Current aggregate result:

| Model | Average MSE |
|---|---:|
| PatchTST | **0.5086** |
| DLinear | 0.5267 |
| PatchMLPTS | 0.5284 |
| Linear | 0.5296 |
| NLinear | 0.5502 |

PatchMLPTS is behind PatchTST on aggregate ETTh1 performance.

However, at H=720 the current main benchmark reports approximately:

| Model | MSE |
|---|---:|
| PatchMLPTS | **0.643276** |
| PatchTST | 0.643283 |

This is effectively a tie.

### Paper interpretation

Use:

> PatchMLPTS remains competitive with PatchTST on ETTh1, including an effectively tied result at the longest evaluated horizon.

Do not use:

> PatchMLPTS outperforms PatchTST on ETTh1.

---

## ETTh2

Current aggregate result:

| Model | Average MSE |
|---|---:|
| DLinear | **0.2081** |
| Linear | 0.2105 |
| PatchMLPTS | **0.2148** |
| PatchTST | 0.2193 |
| NLinear | 0.2263 |

PatchMLPTS performs better than PatchTST in several longer-horizon settings.

At H=720:

| Model | MSE |
|---|---:|
| PatchMLPTS | **0.258908** |
| DLinear | 0.261329 |
| PatchTST | 0.265287 |

### Paper interpretation

Use:

> PatchMLPTS is competitive on ETTh2 and improves over PatchTST at the longest evaluated horizon.

Avoid:

> PatchMLPTS consistently outperforms all baselines on ETTh2.

---

## ETTm1

Current aggregate result:

| Model | Average MSE |
|---|---:|
| Linear | **0.4537** |
| DLinear | 0.4547 |
| NLinear | 0.4722 |
| PatchMLPTS | 0.4725 |
| PatchTST | 0.4821 |

PatchMLPTS is not the best model on aggregate ETTm1.

However, at H=336 it is better than PatchTST:

| Model | MSE |
|---|---:|
| PatchMLPTS | **0.483508** |
| DLinear | 0.489448 |
| PatchTST | 0.499578 |

### Paper interpretation

Use:

> PatchMLPTS remains competitive with PatchTST on selected ETTm1 horizons, although linear baselines remain stronger on average.

This is a useful limitation to report.

---

## ETTm2

This is currently the strongest ETT dataset for PatchMLPTS.

| Model | Average MSE |
|---|---:|
| **PatchMLPTS** | **0.1685** |
| Linear | 0.1694 |
| DLinear | 0.1696 |
| NLinear | 0.1724 |
| PatchTST | 0.1727 |

At H=720:

| Model | MSE |
|---|---:|
| **PatchMLPTS** | **0.220080** |
| Linear | — |
| DLinear | — |
| PatchTST | 0.242978 |

The PatchMLPTS improvement over PatchTST at this configuration is approximately 9.4% MSE.

### Paper interpretation

Use:

> PatchMLPTS achieves its strongest relative ETT performance on ETTm2, including a substantial improvement over PatchTST at H=720.

---

# 4. Electricity Results

Electricity must be treated as a major limitation of the current model.

Current results:

## H=96

| Model | MSE |
|---|---:|
| Linear | 0.195541 |
| NLinear | 0.198472 |
| DLinear | 0.196043 |
| **PatchTST** | **0.163033** |
| PatchMLPTS | 0.752234 |

## H=720

| Model | MSE |
|---|---:|
| Linear | 0.242365 |
| NLinear | 0.253054 |
| DLinear | 0.241383 |
| **PatchTST** | **0.229562** |
| PatchMLPTS | 0.795801 |

### Required paper interpretation

State explicitly:

> The Electricity experiments expose an important limitation of the current Patch-MLP-TS configuration. Although the model provides substantial computational savings, its forecasting error is considerably higher than PatchTST and the linear baselines on this high-dimensional dataset.

Do **not** hide Electricity.

Do **not** claim:

> PatchMLPTS performs competitively across all datasets.

Instead claim:

> PatchMLPTS demonstrates competitive performance across the ETT benchmarks, while the Electricity dataset reveals a clear limitation that motivates further investigation of high-dimensional multivariate forecasting.

---

# 5. Ablation Results

The current ablation evidence supports two important observations.

## 5.1 Channel-independent vs channel-mixing

The latest ablation results show that explicit channel mixing performs substantially worse than the proposed channel-independent configuration across the evaluated ETTh1 and ETTh2 settings.

Example:

### ETTh1 H=96

| Variant | MSE | MAE |
|---|---:|---:|
| PatchMLPTS | 0.8141 | 0.6481 |
| No Patching | **0.4246** | **0.4493** |
| Channel Mixing | 0.9221 | 0.7104 |

### ETTh2 H=96

| Variant | MSE | MAE |
|---|---:|---:|
| PatchMLPTS | 0.2097 | 0.3228 |
| No Patching | **0.1730** | **0.2846** |
| Channel Mixing | 0.3744 | 0.4727 |

This pattern continues across the latest ETTh1/ETTh2 ablation settings.

### Supported claim

> Explicit channel mixing consistently degraded forecasting performance across the evaluated ETTh1 and ETTh2 ablation configurations.

### Avoid

> Channel independence is universally superior for multivariate forecasting.

The experiment only supports the narrower claim.

---

# 6. Patching Ablation

The latest ablation results show:

> **No Patching outperforms the current full PatchMLPTS configuration across all evaluated ETTh1 and ETTh2 settings.**

This is scientifically important but currently problematic for the proposed architecture.

### Supported claim

> Under the current architecture and training configuration, temporal patching did not provide a consistent forecasting benefit; the no-patching variant achieved lower error across all evaluated ETTh1 and ETTh2 ablation settings.

### Avoid

> Patching improves forecasting.

The current evidence contradicts that.

---

# 7. Critical Experimental Consistency Issue

There are multiple experimental generations in the uploaded files.

For example, ETTh1 H=96 PatchMLPTS appears in different result generations at approximately:

- 0.445950 MSE
- 0.435328 MSE
- 0.814089 MSE

Parameter counts also differ substantially between result generations.

Therefore:

## Do not combine these files into one paper table.

The paper must have:

> **One canonical PatchMLPTS implementation + one canonical training configuration + reproducible results.**

Before submission:

1. Identify the exact model implementation corresponding to the intended method.
2. Fix the model/data/training configuration.
3. Re-run the affected experiments.
4. Use the same implementation for main benchmark and ablations.
5. Run at least 3 seeds.
6. Report mean ± standard deviation.

This is a **must-fix publication issue**, not a cosmetic issue.

---

# 8. Suspected Architectural Issue to Verify

The current implementation should be inspected for patch aggregation such as:

```python
e = e.mean(dim=1)
```

after patch embedding/MLP processing.

If this operation averages across the patch dimension before forecasting, it can remove information about patch ordering.

This could explain why:

> No Patching > Patching

in the latest ablation.

The canonical architecture should preserve temporal/patch structure through an explicit temporal mixing operation.

A recommended MLP structure is:

```text
Input
  ↓
Normalization
  ↓
Temporal Patching
  ↓
Patch Embedding
  ↓
Temporal / Patch Mixing MLP
  ↓
Feature Mixing MLP
  ↓
Forecast Head
  ↓
Prediction
```

The final implementation should be verified before generating publication results.

---

# 9. Efficiency Results

The current efficiency measurements show a meaningful computational advantage over PatchTST.

## ETTh1

| Metric | PatchMLPTS | PatchTST |
|---|---:|---:|
| Parameters | 409,952 | 416,224 |
| Training time / epoch | ~1.14 s | ~2.53 s |
| Inference latency | ~2.55 ms | ~3.14 ms |

## Electricity

| Metric | PatchMLPTS | PatchTST |
|---|---:|---:|
| Training time / epoch | ~54.47 s | ~161.74 s |
| Inference latency | ~14.19 ms | ~32.21 ms |

Approximate Electricity efficiency differences:

- Training time: ~66% lower
- Inference latency: ~56% lower

### Supported claim

> PatchMLPTS substantially reduces training and inference cost relative to PatchTST in the reported timing experiments.

### Important qualification

Efficiency measurements must report:

- GPU
- CPU
- batch size
- precision
- sequence length
- forecast horizon
- number of warm-up iterations
- number of timing repetitions
- whether data loading is included
- whether synchronization is used for GPU timing

Otherwise the timing comparison is difficult to reproduce.

---

# 10. Parameter-Count Claims

Do not use a single claim such as:

> "PatchMLPTS uses 48% fewer parameters."

The current canonical benchmark numbers do not support one universal 48% reduction.

Current main-benchmark examples:

## H=96

```text
PatchMLPTS = 497,576
PatchTST   = 695,904
```

Approximate reduction:

**28.5%**

## H=720

```text
PatchMLPTS = 1,137,176
PatchTST   = 1,335,504
```

Approximate reduction:

**14.9%**

Use the exact parameter counts for each configuration in the final table.

---

# 11. Required Main Benchmark Table

The primary paper table should contain:

### Table 1 — Main long-term forecasting benchmark

Rows:

- Linear
- NLinear
- DLinear
- PatchTST
- PatchMLPTS

Columns:

```text
Dataset
Horizon
Linear MSE
Linear MAE
NLinear MSE
NLinear MAE
DLinear MSE
DLinear MAE
PatchTST MSE
PatchTST MAE
PatchMLPTS MSE
PatchMLPTS MAE
```

Recommended organization:

| Dataset | Horizon | Linear | NLinear | DLinear | PatchTST | PatchMLPTS |
|---|---:|---:|---:|---:|---:|---:|
| ETTh1 | 96 | MSE/MAE | MSE/MAE | MSE/MAE | MSE/MAE | **MSE/MAE** |
| ETTh1 | 192 | ... | ... | ... | ... | ... |
| ETTh1 | 336 | ... | ... | ... | ... | ... |
| ETTh1 | 720 | ... | ... | ... | ... | ... |
| ETTh2 | 96 | ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... | ... | ... |
| ETTm2 | 720 | ... | ... | ... | ... | ... |

Best MSE and MAE should be bolded **within each row**.

Do not bold PatchMLPTS merely because it is your method.

---

# 12. Required Electricity Table

### Table 2 — High-dimensional Electricity benchmark

Use a separate table because the dataset behaves very differently.

| Horizon | Linear MSE | NLinear MSE | DLinear MSE | PatchTST MSE | PatchMLPTS MSE |
|---:|---:|---:|---:|---:|---:|
| 96 | 0.195541 | 0.198472 | 0.196043 | **0.163033** | 0.752234 |
| 720 | 0.242365 | 0.253054 | 0.241383 | **0.229562** | 0.795801 |

Also report MAE in a corresponding table or second metric column.

This table should be accompanied by a short discussion of the model's high-dimensional limitation.

---

# 13. Required Ablation Table

### Table 3 — Component ablation

Rows:

- Full PatchMLPTS
- No Patching
- Channel Mixing

Columns:

```text
Dataset
Horizon
Full MSE
No Patching MSE
Channel Mixing MSE
Full MAE
No Patching MAE
Channel Mixing MAE
```

Use all evaluated ETTh1/ETTh2 horizons.

Example:

| Dataset | H | Full | No Patch | Channel Mixing |
|---|---:|---:|---:|---:|
| ETTh1 | 96 | 0.8141 | **0.4246** | 0.9221 |
| ETTh1 | 192 | 0.8580 | **0.4748** | 1.1063 |
| ETTh1 | 336 | 0.8981 | **0.5154** | 1.1562 |
| ETTh1 | 720 | 0.9993 | **0.6698** | 1.2153 |
| ETTh2 | 96 | 0.2097 | **0.1730** | 0.3744 |
| ETTh2 | 192 | 0.2562 | **0.2187** | 0.5279 |
| ETTh2 | 336 | 0.2424 | **0.2164** | 0.5253 |
| ETTh2 | 720 | 0.2784 | **0.2606** | 0.7590 |

**These exact values must be regenerated after the canonical implementation is finalized.**

---

# 14. Required Patch-Length Ablation

This experiment is currently missing and should be added.

### Table 4 — Patch-length sensitivity

Evaluate:

```text
Patch length = 4
Patch length = 8
Patch length = 12
Patch length = 16
Patch length = 24
Patch length = 32
```

Recommended datasets:

- ETTh2
- ETTm2
- Electricity

Recommended horizons:

- 96
- 720

Table structure:

| Dataset | H | P=4 | P=8 | P=12 | P=16 | P=24 | P=32 |
|---|---:|---:|---:|---:|---:|---:|---:|
| ETTh2 | 96 | ... | ... | ... | ... | ... | ... |
| ETTh2 | 720 | ... | ... | ... | ... | ... | ... |
| ETTm2 | 96 | ... | ... | ... | ... | ... | ... |
| ETTm2 | 720 | ... | ... | ... | ... | ... | ... |
| Electricity | 96 | ... | ... | ... | ... | ... | ... |
| Electricity | 720 | ... | ... | ... | ... | ... | ... |

This is important because the current no-patching result suggests that the patch representation may be losing useful temporal information.

---

# 15. Required Multi-Seed Table

After fixing the architecture, rerun the important experiments with at least 3 seeds.

### Table 5 — Statistical stability

| Dataset | Horizon | Model | Seed 42 | Seed 123 | Seed 2026 | Mean ± Std |
|---|---:|---|---:|---:|---:|---:|
| ETTh1 | 96 | PatchMLPTS | ... | ... | ... | ... |
| ETTh1 | 720 | PatchMLPTS | ... | ... | ... | ... |
| ETTh2 | 96 | PatchMLPTS | ... | ... | ... | ... |
| ETTh2 | 720 | PatchMLPTS | ... | ... | ... | ... |
| ETTm2 | 96 | PatchMLPTS | ... | ... | ... | ... |
| ETTm2 | 720 | PatchMLPTS | ... | ... | ... | ... |
| Electricity | 96 | PatchMLPTS | ... | ... | ... | ... |
| Electricity | 720 | PatchMLPTS | ... | ... | ... | ... |

At minimum, use the key configurations rather than rerunning every model/horizon if compute is constrained.

---

# 16. Required Efficiency Table

### Table 6 — Computational efficiency

| Dataset | Horizon | Model | Params | Train / Epoch | Inference | GPU Memory |
|---|---:|---|---:|---:|---:|---:|
| ETTh1 | 96 | PatchMLPTS | ... | ... | ... | ... |
| ETTh1 | 96 | PatchTST | ... | ... | ... | ... |
| ETTh1 | 720 | PatchMLPTS | ... | ... | ... | ... |
| ETTh1 | 720 | PatchTST | ... | ... | ... | ... |
| Electricity | 96 | PatchMLPTS | ... | ... | ... | ... |
| Electricity | 96 | PatchTST | ... | ... | ... | ... |
| Electricity | 720 | PatchMLPTS | ... | ... | ... | ... |
| Electricity | 720 | PatchTST | ... | ... | ... | ... |

Include:

- parameter count
- train time/epoch
- inference latency
- peak GPU memory

If possible, also report throughput:

```text
samples / second
```

---

# 17. Recommended Additional Baseline

Add:

> **TSMixer**

This is strongly recommended because it is directly relevant to the MLP-family forecasting literature.

Final model comparison should ideally be:

```text
Linear
NLinear
DLinear
TSMixer
PatchTST
PatchMLPTS
```

This makes the proposed architecture's relationship to existing MLP approaches much clearer.

---

# 18. Recommended Figures

## Figure 1 — Architecture

Show:

```text
Input historical sequence
        ↓
Normalization
        ↓
Patch extraction
        ↓
Patch embedding
        ↓
Temporal/Patch MLP
        ↓
Feature MLP
        ↓
Forecast head
        ↓
Future sequence
```

Highlight channel-independent processing.

---

## Figure 2 — Accuracy vs efficiency

Scatter plot:

```text
x-axis = inference latency
y-axis = MSE
bubble size = parameter count
```

Compare:

- Linear
- DLinear
- PatchTST
- PatchMLPTS

This figure can communicate the central paper contribution better than a raw table.

---

## Figure 3 — Horizon scaling

Plot MSE versus horizon:

```text
96 → 192 → 336 → 720
```

for:

- PatchMLPTS
- PatchTST
- DLinear

Use separate plots for ETTh2 and ETTm2.

---

## Figure 4 — Patch-length sensitivity

Plot:

```text
Patch length
      vs
MSE
```

for ETTh2/ETTm2.

---

## Figure 5 — Channel-mixing ablation

Show the relative error of:

```text
Channel Independent
vs
Channel Mixing
```

This should visually demonstrate the channel-independence finding.

---

# 19. Claims You CAN Make

## Claim A — Competitive ETT accuracy

> PatchMLPTS achieves competitive long-term forecasting performance across the evaluated ETT benchmarks, with aggregate performance close to PatchTST and competitive with established linear baselines.

Supported.

---

## Claim B — Attention-free architecture

> PatchMLPTS achieves competitive forecasting accuracy without self-attention.

Supported if the final implementation contains no attention mechanism.

---

## Claim C — Efficiency

> PatchMLPTS substantially reduces training and inference cost relative to PatchTST in the evaluated timing experiments.

Supported by current measurements, but timing methodology must be standardized.

---

## Claim D — Channel independence

> Channel-independent processing consistently outperformed the evaluated channel-mixing variant across ETTh1 and ETTh2 ablation experiments.

Supported by current ablations.

---

## Claim E — Dataset dependence

> The effectiveness of PatchMLPTS is dataset- and horizon-dependent.

Strongly supported.

---

# 20. Claims You SHOULD NOT Make

Do not claim:

> PatchMLPTS is state-of-the-art.

Not supported.

Do not claim:

> PatchMLPTS universally outperforms PatchTST.

Not supported.

Do not claim:

> PatchMLPTS is more accurate than DLinear.

Not supported overall.

Do not claim:

> Patching always improves forecasting.

Current ablation contradicts this.

Do not claim:

> Channel independence is universally superior.

Your experiments do not establish this universally.

Do not claim:

> PatchMLPTS performs well on Electricity.

Current results contradict this.

Do not claim:

> PatchMLPTS uses 48% fewer parameters.

The current canonical benchmark does not support a universal 48% reduction.

Do not claim:

> PatchMLPTS is a general replacement for Transformers.

Not supported.

---

# 21. Recommended Abstract-Level Claim

A safe current version is:

> We propose Patch-MLP-TS, an attention-free architecture for long-term time-series forecasting that combines temporal patch representations with lightweight MLP-based processing. Across standard ETT benchmarks, Patch-MLP-TS achieves forecasting accuracy competitive with PatchTST while reducing computational cost. Ablation experiments indicate that channel-independent processing is consistently preferable to explicit channel mixing in the evaluated settings. We further analyze the sensitivity of the architecture to temporal patching and patch size, revealing dataset- and horizon-dependent behavior. Experiments on the high-dimensional Electricity dataset expose a limitation of the current architecture, motivating further investigation of scalable multivariate representations.

This is much safer than claiming universal superiority.

---

# 22. Recommended Contribution List

The introduction should eventually have three contributions:

### 1. Architecture

> We introduce Patch-MLP-TS, an attention-free patch-based MLP architecture for long-term time-series forecasting.

### 2. Empirical analysis

> We systematically study temporal patching and channel mixing, showing that their effects are architecture- and dataset-dependent.

### 3. Efficiency

> We provide an accuracy-efficiency evaluation against linear and Transformer-based forecasting baselines, demonstrating substantial reductions in training and inference cost in the evaluated settings.

Do not make "state-of-the-art accuracy" a contribution.

---

# 23. Experimental Setup Requirements

The final paper must clearly report:

```text
Datasets
Dataset sizes
Number of variables
Sampling frequency
Train/validation/test split
Lookback length
Forecast horizons
Patch length
Patch stride
Embedding dimension
MLP hidden dimensions
Number of blocks
Activation
Normalization
Optimizer
Learning rate
Weight decay
Batch size
Epochs
Early stopping
Loss function
Random seeds
Hardware
Software versions
```

For the ETT benchmark, use the **standard fixed train/validation/test splits** if the goal is comparison with published benchmark results.

If using a custom 70/10/20 split, clearly label it as such and do not directly compare its numbers with published standard-split results.

---

# 24. Statistical Reporting

For the final paper:

### Minimum

3 independent seeds.

Report:

```text
Mean ± standard deviation
```

For important comparisons, also report relative improvement:

```text
Relative improvement (%) =
(Baseline - PatchMLPTS) / Baseline × 100
```

For nearly identical results, describe them as ties rather than claiming superiority.

---

# 25. Final Paper Table List

The final manuscript should contain approximately:

| Table | Purpose | Priority |
|---|---|---|
| Table 1 | Main ETT benchmark | **Must have** |
| Table 2 | Electricity benchmark | **Must have** |
| Table 3 | Component ablation | **Must have** |
| Table 4 | Patch-length ablation | **Must have** |
| Table 5 | Multi-seed stability | **Must have** |
| Table 6 | Efficiency / parameters / latency | **Must have** |
| Table 7 | TSMixer comparison | Recommended |
| Table 8 | Dataset statistics | Recommended |
| Table 9 | Hyperparameters | Recommended / appendix |

---

# 26. Final Figure List

| Figure | Purpose | Priority |
|---|---|---|
| Fig. 1 | PatchMLPTS architecture | **Must have** |
| Fig. 2 | Accuracy-efficiency Pareto/scatter | **Must have** |
| Fig. 3 | Horizon scaling | Recommended |
| Fig. 4 | Patch-size sensitivity | Recommended |
| Fig. 5 | Channel-mixing ablation | Recommended |

---

# 27. Paper Narrative

The paper should follow this narrative:

```text
Problem
  ↓
Transformers are effective but computationally expensive
  ↓
Question:
Can patch-based MLP processing provide competitive forecasting
without attention?
  ↓
Patch-MLP-TS
  ↓
Main ETT benchmark
  ↓
Competitive accuracy
  ↓
Efficiency analysis
  ↓
Large computational advantage
  ↓
Ablation:
channel mixing hurts
  ↓
Patch-size study
  ↓
Patching effect is dataset/horizon dependent
  ↓
Electricity
  ↓
Current architecture struggles on high-dimensional multivariate data
  ↓
Discuss limitation honestly
  ↓
Conclusion:
efficient, competitive, but not universally superior
```

This is a much stronger scientific narrative than:

```text
We made MLP
  ↓
MLP beats Transformer
  ↓
SOTA
```

because your actual results do not support the second narrative.

---

# 28. Submission Readiness Checklist

## Before writing final paper

- [ ] Resolve contradictory PatchMLPTS result generations
- [ ] Freeze one canonical implementation
- [ ] Verify patch aggregation preserves temporal information
- [ ] Standardize data splits
- [ ] Reproduce PatchTST using clearly documented settings
- [ ] Add TSMixer if computationally feasible
- [ ] Run 3 seeds
- [ ] Run patch-size ablation
- [ ] Run channel-mixing ablation
- [ ] Re-run Electricity after architecture verification
- [ ] Measure training time correctly
- [ ] Measure inference latency correctly
- [ ] Measure peak GPU memory
- [ ] Record exact parameter counts
- [ ] Save complete configuration for every experiment
- [ ] Generate mean ± std tables
- [ ] Check all claims against actual numbers

---

# 29. Current Overall Assessment

### Strengths

- Multiple standard ETT datasets
- Multiple long-term horizons
- Comparison against Linear/NLinear/DLinear/PatchTST
- PatchMLPTS is competitive with PatchTST on aggregate ETT MSE
- Strong efficiency potential
- Strong channel-independence ablation signal
- Interesting dataset-dependent behavior
- ETTm2 provides particularly promising results

### Weaknesses

- Multiple inconsistent experimental generations
- Current latest ablation conflicts with the stronger main benchmark
- Electricity performance is poor
- Current evidence appears largely single-seed
- Patching currently does not help in the latest ablation
- Need comparison with MLP-family baseline such as TSMixer
- Need stronger statistical reporting
- Need standardized timing methodology
- Need to verify the temporal patch aggregation mechanism

---

# 30. Bottom-Line Publication Strategy

The paper should **not** be framed as:

> "PatchMLPTS beats PatchTST."

It should be framed as:

> **"PatchMLPTS investigates whether lightweight attention-free patch-based MLP processing can retain competitive long-horizon forecasting accuracy while substantially reducing computational cost."**

The strongest evidence currently supports:

```text
Competitive ETT accuracy
        +
Strong efficiency advantage
        +
Channel-independent processing advantage
        +
Dataset/horizon-dependent behavior
```

The main unresolved issue is:

```text
Why does the latest canonical-looking model
perform dramatically worse than earlier runs,
and why does patching lose to no-patching?
```

Resolve that before submission.

**Publication target after cleanup:** a credible workshop/specialized time-series/applied-ML paper is realistic. A stronger conference submission becomes plausible if the canonical model survives the 3-seed benchmark, patch-size study, TSMixer comparison, and Electricity investigation.
