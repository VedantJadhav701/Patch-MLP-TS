# PatchMLPTS Research Project — Full Context

## Project
Research on **Patch-based Multi-Layer Perceptron for Time Series (PatchMLPTS)** for multivariate long-term time-series forecasting.

### Core idea
Past time-series data is divided into small patches. PatchMLPTS learns:
- temporal relationships between patches
- relationships between different features
- future values using MLP-based mixing blocks

The main research question is:

> Can a patch-based MLP architecture compete with strong lightweight forecasting baselines such as Linear and DLinear, without relying on Transformer self-attention?

---

## Data and task

Datasets:
- ETTh1
- ETTh2
- ETTm1
- ETTm2

Each ETT dataset has 7 features.

Current controlled lookback:
- 96

Forecast horizons:
- 96
- 192
- 336
- 720

The standard benchmark therefore contains:

4 datasets × 4 horizons = 16 forecasting tasks.

The expanded benchmark compares 5 models:

1. Linear
2. NLinear
3. DLinear
4. PatchMLPTS
5. PatchTST

Total:
4 × 4 × 5 = 80 experiments.

Output file:
`expanded_benchmark_results.csv`

---

## PatchMLPTS architecture

Current main configuration:
- lookback = 96
- patch length = 12
- embedding dimension = 128
- number of MLP blocks = 3
- dropout = 0.1

96 history points / 12 points per patch = 8 patches.

Conceptual flow:

Historical sequence
→ split into patches
→ patch embeddings
→ temporal mixing
→ feature mixing
→ repeated MLP blocks
→ future prediction

The important architectural correction was preserving individual patch information instead of aggressively averaging/mean-pooling patches. The earlier implementation lost temporal-position information and produced very poor results. The corrected architecture explicitly mixes information across time patches and features while retaining patch information.

---

## Notebook / implementation status

The notebook contains:
- imports/environment
- configuration and seed setup
- ETT data loading
- train/validation/test split
- training-only normalization
- sliding-window dataset creation
- Linear model
- NLinear model
- DLinear model
- PatchMLPTS model
- PatchTST model
- training loop
- validation
- AdamW optimization
- learning-rate scheduling
- early stopping
- restoration of best validation model
- MSE and MAE evaluation
- benchmark runner
- result pivot tables
- winner analysis
- relative percentage differences
- model summaries
- parameter counts
- CSV exports

The data loader creates historical windows and future targets using the configured lookback and horizon.

---

## Initial corrected experiment

ETTh1:
- lookback = 96
- horizon = 96

One corrected run:

DLinear:
- MSE = 0.4403
- MAE = 0.4442

PatchMLPTS:
- MSE = 0.4195
- MAE = 0.4475

Interpretation:
- PatchMLPTS had lower MSE.
- DLinear had slightly lower MAE.
- This showed the corrected PatchMLPTS implementation was much better than the earlier implementation.

Another run produced:

DLinear:
- MSE = 0.441026
- MAE = 0.445585

PatchMLPTS:
- MSE = 0.428118
- MAE = 0.453225

Again PatchMLPTS had lower MSE but higher MAE.

These small differences across runs mean repeated seeds are needed for publication-level claims.

---

## Early two-model benchmark

The first full comparison used only:
- DLinear
- PatchMLPTS

Across:
- 4 datasets
- 4 horizons

= 32 model runs.

The earliest PatchMLPTS results were much worse, for example ETTh1 horizon 96 had PatchMLPTS MSE around 0.815. This was before the architecture correction and should NOT be treated as the final model result.

After correction, PatchMLPTS became substantially more competitive.

---

# Expanded benchmark — CURRENT IMPORTANT RESULT

The final expanded benchmark compares:

Linear
NLinear
DLinear
PatchMLPTS
PatchTST

across all 16 dataset/horizon combinations.

## Approximate overall averages

DLinear:
- Average MSE ≈ 0.3398
- Average MAE ≈ 0.3874

Linear:
- Average MSE ≈ 0.3408
- Average MAE ≈ 0.3880

PatchTST:
- Average MSE ≈ 0.3457
- Average MAE ≈ 0.3970

PatchMLPTS:
- Average MSE ≈ 0.3460
- Average MAE ≈ 0.3980

NLinear:
- Average MSE ≈ 0.3553
- Average MAE ≈ 0.3921

### Current interpretation

DLinear is currently the strongest overall baseline.

Linear is extremely competitive.

PatchMLPTS is competitive but is NOT the overall winner.

PatchTST is also competitive.

Therefore, the project must NOT claim:

> PatchMLPTS beats DLinear overall.

The current evidence does not support that.

---

# Expanded benchmark — selected detailed results

## ETTh1

Horizon 96:
- Linear: 0.4390 / 0.4440
- NLinear: 0.4444 / 0.4461
- DLinear: 0.4382 / 0.4444
- PatchMLPTS: 0.4353 / 0.4533
- PatchTST: 0.4136 / 0.4487

Horizon 192:
- Linear: 0.4919 / 0.4834
- NLinear: 0.5009 / 0.4849
- DLinear: 0.4891 / 0.4807
- PatchMLPTS: 0.5052 / 0.5031
- PatchTST: 0.4669 / 0.4827

Horizon 336:
- Linear: 0.5346 / 0.5151
- NLinear: 0.5490 / 0.5167
- DLinear: 0.5313 / 0.5147
- PatchMLPTS: 0.5297 / 0.5215
- PatchTST: 0.5108 / 0.5134

Horizon 720:
- Linear: 0.6529 / 0.5962
- NLinear: 0.7063 / 0.6107
- DLinear: 0.6481 / 0.5926
- PatchMLPTS: 0.6433 / 0.5957
- PatchTST: 0.6433 / 0.5991

Format above is MSE / MAE.

---

## ETTh2

Horizon 96:
- Linear: 0.1687 / 0.2818
- NLinear: 0.1735 / 0.2829
- DLinear: 0.1681 / 0.2805
- PatchMLPTS: 0.1703 / 0.2848
- PatchTST: 0.1721 / 0.2927

Horizon 192:
- Linear: 0.1967 / 0.3069
- NLinear: 0.2035 / 0.3099
- DLinear: 0.1922 / 0.3057
- PatchMLPTS: 0.2136 / 0.3306
- PatchTST: 0.2061 / 0.3135

Horizon 336:
- Linear: 0.2131 / 0.3279
- NLinear: 0.2302 / 0.3316
- DLinear: 0.2108 / 0.3218
- PatchMLPTS: 0.2163 / 0.3338
- PatchTST: 0.2338 / 0.3368

Horizon 720:
- Linear: 0.2635 / 0.3747
- NLinear: 0.2981 / 0.3823
- DLinear: 0.2613 / 0.3728
- PatchMLPTS: 0.2589 / 0.3676
- PatchTST: 0.2653 / 0.3679

Important:
PatchMLPTS wins both MSE and MAE at ETTh2 horizon 720.

---

## ETTm1

Horizon 96:
- Linear: 0.3543 / 0.3845
- NLinear: 0.3646 / 0.3926
- DLinear: 0.3551 / 0.3842
- PatchMLPTS: 0.4116 / 0.4203
- PatchTST: 0.4243 / 0.4234

Horizon 192:
- Linear: 0.4229 / 0.4208
- NLinear: 0.4348 / 0.4283
- DLinear: 0.4255 / 0.4225
- PatchMLPTS: 0.4400 / 0.4397
- PatchTST: 0.4669 / 0.4554

Horizon 336:
- Linear: 0.4886 / 0.4567
- NLinear: 0.5059 / 0.4649
- DLinear: 0.4894 / 0.4557
- PatchMLPTS: 0.4835 / 0.4709
- PatchTST: 0.4996 / 0.4850

Horizon 720:
- Linear: 0.5491 / 0.5005
- NLinear: 0.5836 / 0.5169
- DLinear: 0.5487 / 0.5019
- PatchMLPTS: 0.5548 / 0.5245
- PatchTST: 0.5376 / 0.5122

Important:
PatchMLPTS beats DLinear in MSE at horizon 336, but not MAE.

---

## ETTm2

Horizon 96:
- Linear: 0.1206 / 0.2351
- NLinear: 0.1205 / 0.2324
- DLinear: 0.1205 / 0.2348
- PatchMLPTS: 0.1162 / 0.2303
- PatchTST: 0.1193 / 0.2319

Important:
PatchMLPTS wins both MSE and MAE.

Horizon 192:
- Linear: 0.1491 / 0.2632
- NLinear: 0.1494 / 0.2595
- DLinear: 0.1506 / 0.2664
- Use the saved expanded benchmark CSV for the exact PatchMLPTS value; do not mix values from older runs.

Horizon 336:
- Linear: 0.1809 / 0.2918
- NLinear: 0.1821 / 0.2862
- DLinear: 0.1785 / 0.2884
- PatchMLPTS: 0.1873 / 0.3031
- PatchTST: 0.1808 / 0.2905

Horizon 720:
- Linear: 0.2271 / 0.3256
- NLinear: 0.2376 / 0.3277
- DLinear: 0.2286 / 0.3320
- PatchMLPTS: 0.2201 / 0.3259
- PatchTST: 0.2430 / 0.3387

Important:
PatchMLPTS beats DLinear in MSE at horizon 720.

---

# Parameter counts

Horizon 96:
- Linear: 9,312
- NLinear: 9,312
- DLinear: 18,624
- PatchMLPTS: 497,576
- PatchTST: 695,904

Horizon 192:
- Linear: 18,624
- NLinear: 18,624
- DLinear: 37,248
- PatchMLPTS: 595,976
- PatchTST: 794,304

Horizon 336:
- Linear: 32,592
- NLinear: 32,592
- DLinear: 65,184
- PatchMLPTS: 743,576
- PatchTST: 941,904

Horizon 720:
- Linear: 69,840
- NLinear: 69,840
- DLinear: 139,680
- PatchMLPTS: 1,137,176
- PatchTST: 1,335,504

Important:
PatchMLPTS is currently much larger than Linear and DLinear. Do not call it parameter-efficient until this is improved or the claim is changed.

---

# Current scientific interpretation

The current evidence says:

1. PatchMLPTS is viable.
2. The corrected architecture is dramatically better than the earlier implementation.
3. PatchMLPTS wins some dataset/horizon combinations.
4. PatchMLPTS does not beat DLinear consistently.
5. DLinear remains the strongest overall model in the current benchmark.
6. Linear is surprisingly competitive.
7. PatchMLPTS has a substantial parameter-count disadvantage.
8. PatchMLPTS may have useful dataset/horizon-specific behavior.
9. More experiments are needed before making a strong superiority claim.

The likely research contribution should therefore focus on understanding WHEN patch-based MLP mixing helps, rather than assuming it always helps.

---

# Literature context

Relevant ideas already studied:

## DLinear
Shows that simple linear/decomposition-based models can be extremely strong for long-term forecasting.

## PatchTST
Uses patches as tokens in a Transformer architecture for time-series forecasting and evaluates ETT datasets and standard long-term horizons.

## TSMixer
Shows that all-MLP architectures can effectively mix information across time and variables for multivariate forecasting.

PatchMLPTS combines ideas from patch-based representations and MLP-based temporal/feature mixing, but the project must carefully distinguish its implementation and experimental contribution from existing work.

---

# Current research weaknesses

1. No proper multi-seed statistical analysis yet.
2. Lookback is currently fixed at 96.
3. Patch length is currently fixed at 12.
4. Current model is relatively large.
5. MAE is not consistently better than DLinear.
6. Overall MSE is not better than DLinear.
7. Need ablation studies to prove which component actually helps.
8. Need stronger reproducibility controls.

---

# NEXT STEPS — IMPORTANT

Do NOT randomly modify the model yet.

Recommended order:

## 1. Reproduce benchmark
Verify:
- same splits
- same normalization
- same lookback
- same horizons
- same training configuration
- same evaluation

## 2. Multi-seed experiments
Use 3–5 random seeds for important experiments.

Report:
- mean MSE ± standard deviation
- mean MAE ± standard deviation

## 3. Ablation study
Test:

A. Plain MLP baseline
B. Patch representation only
C. Patch + temporal mixing
D. Patch + feature mixing
E. Patch + temporal mixing + feature mixing

Goal:
Determine which component produces improvements.

## 4. Patch length study
Try:
- 6
- 8
- 12
- 16
- 24
- 32

## 5. Number of blocks
Try:
- 1
- 2
- 3
- 4

## 6. Embedding dimension
Try:
- 32
- 64
- 128
- 256

## 7. Longer lookback
After controlled experiments:
- 96
- 192
- 336
- 512

## 8. Efficiency study
Measure:
- parameter count
- training time
- inference latency
- memory usage
- MSE
- MAE

## 9. Final model
Choose the architecture based on evidence, not the best single run.

## 10. Final benchmark
Run the selected architecture against all baselines with multiple seeds.

---

# Final research framing

A defensible working title:

**Investigating Patch-Based Multi-Layer Perceptrons for Efficient Multivariate Long-Term Time-Series Forecasting**

Core research question:

> Can patch-based temporal and feature mixing provide competitive forecasting accuracy without Transformer self-attention?

Current safe conclusion:

> PatchMLPTS provides competitive forecasting performance on several ETT dataset-horizon combinations, including some cases where it outperforms DLinear in MSE and/or MAE, but DLinear remains stronger overall. Further ablation, multi-seed, patch-size, lookback, and efficiency experiments are required to determine the source and consistency of the observed gains.

---

# Simple explanation

Think of a long line of electricity measurements.

Instead of giving the entire line to the model:

    [lots of numbers]

we cut it into small pieces:

    [piece][piece][piece][piece]...

PatchMLPTS learns:
- patterns inside each piece
- relationships between pieces over time
- relationships between the different measurements

Then it predicts the future.

We compare it with simple models such as Linear and DLinear, and with PatchTST.

The current answer is:

> PatchMLPTS is promising, but it is not yet better overall.

The next scientific task is to find out **why it wins on some tasks, why it loses on others, and whether those differences are statistically reliable.**

---

# Golden rule

For future work on this project:

**Do not assume a more complicated model is better.**

Always ask:

> What does the experiment actually prove?

Then design the next experiment around that question.
