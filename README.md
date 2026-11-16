# Model Compression and Deployment Optimization for Edge Vision: A Survey

Experimental data accompanying the survey by Xingang Mou, Jianqiu Li, Dongming Wang, and Xiao Zhou.

Corresponding author: Xiao Zhou (zhouxiao@whut.edu.cn).

## Contents

- `data/quantization/`: FP16 and INT8 results for six models (12 configurations).
- `data/pruning/`: standard fine-tuning and structured-pruning comparisons, including three-seed accuracy summaries, model-call timing, and structural costs.
- `docs/protocol.md`: datasets, timing definitions, units, and configuration notes.
- `provenance.json`: SHA-256 checksums of local source records used to prepare this release.

These two experiments use different data subsets and calibration configurations. Compare results within their respective protocols. Distillation arms and exploratory runs that are not part of the current manuscript are not included.

The files contain numerical measurements and summaries, not model weights, dataset images, or per-image predictions. Existing metric names are retained. Training configuration scripts and full raw timing traces are not included in this data release.

## Reading the data

The six-model CSV can be opened directly in a spreadsheet or read with Python's standard `csv` module. JSON files require no additional software dependencies. See the protocol for differences in metric scales.

For detection in the pruning experiment, `detection_raw6_final.json` is the final six-branch INT8 result source. Earlier detection entries in `deployment_records.json` are retained as intermediate records and must not replace the final results. For classification, use `int8_imagenetcal` in the accuracy summary when reproducing the manuscript's pruning comparison.
