# Which Past? Anchor Choice and Deviation Levels in ST-SSDL Traffic Forecasting

Project of Group 27 for the course Urban Computing at Leiden University (2026 to 2027).

**Status: planning stage.** This repository has no code and no results yet. We add the files week by week, and we update this page when they exist.

## The question

ST-SSDL is a traffic forecasting model from the paper "How Different from the Past? Spatio-Temporal Time Series Forecasting with Self-Supervised Deviation Learning" (Gao et al., NeurIPS 2025). It compares the current hour with a historical anchor, which is the average of the same weekday and time of day over past weeks. We test the model with new anchors that only use the past, and we measure it per deviation level (how different an hour is from the usual pattern). Our research questions are:

1. RQ1. Does the gain of ST-SSDL over its backbone without deviation learning hold in every deviation bin (low, medium, high), and is it largest in the high bin?
2. RQ2. Which anchor gives the lowest error: the fixed mean of the paper, the last week, a rolling three-week mean, an expanding mean or a time-of-day mean? Does the best anchor differ between low and high deviation windows, and between flow data (PEMS08) and speed data (METRLA)?
3. RQ3. How does a zero-shot time series foundation model (Chronos) compare with ST-SSDL per deviation bin on the same windows? Does giving it the anchor as a covariate close the gap?

## The plan

1. Reproduce: run the released ST-SSDL code unchanged on PEMS08 (traffic flow) and METRLA (traffic speed) and compare with the paper.
2. Anchors: write our own data preparation script for the four past-only anchors (same slot last week, rolling mean of three weeks, expanding mean of all past weeks, expanding time-of-day mean), next to the fixed anchor of the released code.
3. Train: six configurations per dataset (ST-SSDL with five anchors, and the backbone without deviation learning), three seeds each, so 36 runs on the ALICE cluster. We keep the authors' hyperparameters and we do not tune.
4. Evaluate: a deviation score that depends neither on a trained model nor on one of the compared anchors (distance from the training-days median of the same weekly slot), three bins of equal size, and MAE, RMSE and MAPE per bin at 15, 30 and 60 minutes. We use a paired Wilcoxon test on non-overlapping windows. The ACF and Moran's I are two short extra analyses.
5. Foundation model: run Chronos-Bolt and Chronos-2 zero-shot on PEMS08 (inference only). Chronos-2 gets the anchor in two ways: over the context only, and for the next hour as a known future covariate. We compare on the same windows and bins.
6. Changes to the released code (a flag for the backbone without deviation learning, controlled seeds, checkpoints with resume, saving per-window predictions) are kept as patch files in `patches/`.
7. Dates: the project starts on Monday 2 November 2026, the presentation is on Wednesday 9 December 2026 and the report is due on Tuesday 5 January 2027.

## Repository structure

```
patches/           our changes to the authors' ST-SSDL code
data_prep/         anchor preparation script and anchor tests
jobs/              Slurm job scripts for ALICE
envs/              conda environment files
evaluation/        deviation score, bins, metrics, statistical tests, ACF, Moran's I
foundation_model/  Chronos-Bolt and Chronos-2 inference
results/           result tables
figures/           figures for the report and the slides
```

Each folder has a short README that says what goes in it.

## Data and code sources

All links come from our proposal (checked on 8 October 2026).

* Paper: https://arxiv.org/abs/2510.04908 and the NeurIPS 2025 page https://neurips.cc/virtual/2025/poster/117831
* Authors' code and data (MIT licence): https://github.com/Jimmy-7664/ST-SSDL. We pin commit `a98331d` of 9 March 2026. It holds the model, the training script and the six datasets. We use PEMS08 (flow) as the main set and METRLA (speed) as the second set.
* PEMS08 comes from the Caltrans Performance Measurement System (https://pems.dot.ca.gov/) and was released with ASTGCN (https://doi.org/10.1609/aaai.v33i01.3301922). METRLA holds loop detector data from highways of Los Angeles County and was released with DCRNN (https://arxiv.org/abs/1707.01926).
* Foundation model (Apache 2.0 licence): https://huggingface.co/amazon/chronos-bolt-small and https://huggingface.co/amazon/chronos-2, with the package at https://github.com/amazon-science/chronos-forecasting and the Chronos-2 paper at https://arxiv.org/abs/2510.15821
* Compute: the ALICE cluster of Leiden University (https://pubappslu.atlassian.net/wiki/spaces/HPCWIKI/overview), with DSLab (https://rel.liacs.nl/) as fallback.

## How to run

There is nothing to run yet, because our code does not exist yet. The first step starts from the authors' code (not ours):

```
git clone https://github.com/Jimmy-7664/ST-SSDL
cd ST-SSDL
git checkout a98331d
```

The authors' code needs Python 3.9, PyTorch 1.13.1, numpy 1.21.5 and pandas 1.5.1. We will build this environment with Miniconda on ALICE with the CUDA 11.7.0 module. Chronos needs Python 3.10 or newer, so it gets a second environment. When our scripts exist, this page will list the exact commands to rebuild every table of the report.

## Team

Group 27: Mohammadreza AhmadiTeshnizi and Haowen Ran.
