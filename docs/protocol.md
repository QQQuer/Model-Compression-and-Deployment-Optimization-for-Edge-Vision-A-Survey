# Experimental protocol and data dictionary

## Platform

TOPEET iTOP-RK3588, approximately 8 GB RAM, Ubuntu 20.04.5, Linux 5.10.226. RKNN-Toolkit2, Lite2 and runtime: 2.3.2; driver: 0.9.8. Batch size: 1; three NPU cores; relevant CPU calculation threads: 1. FP16 and INT8 denote toolchain build configurations; individual operators may use different data types.

The six-model experiment uses optimization level 0 for DeepLabV3 and level 3 for classification and LR-ASPP. Original records also report short DeepLabV3 tests at levels 1/2/3 without an evident latency improvement; the optimization-level difference alone is not established as the cause of its latency.

## Six-model quantization experiment

- Classification: MobileNetV2 and ResNet18. Fixed Imagenette subset, 1,000 validation images, 100 per class; calibration uses 500 training images. Original 1,000-class outputs are retained. Input: 224 x 224 after short-side resize to 256 and center crop.
- Detection: YOLOv8s and YOLOv8n. Complete COCO val2017, 5,000 images; calibration: 500 separate training images. Input: 640 x 640 with aspect-ratio-preserving padding. Confidence threshold 0.001, NMS IoU 0.7, maximum 300 detections per image.
- Segmentation: LR-ASPP and DeepLabV3. Complete VOC2012 segmentation validation, 1,449 images; calibration: 500 separate training images. Input: 512 x 512; 21 classes including background; ignore label 255. Predictions are resized to annotation dimensions for scoring.

`quality` is Top-1 accuracy (classification), COCO AP averaged over IoU 0.50:0.95 (detection), or mIoU (segmentation), all on a 0-100 scale. `secondary` retains auxiliary measurements from source records and is not a fine-tuning result or a primary manuscript metric.

`call_ms`: median model invocation time, milliseconds. `p95_ms`: invocation 95th percentile, milliseconds. `e2e_ms`: end-to-end time, milliseconds. `file_mib`: model file size in MiB. `rss_min` / `rss_max`: observed resident process memory range in MiB. `samples`, `e2e_samples`, and `resource_processes` give measurement counts.

Classification and detection invocation measurements total 2,500 calls per configuration, with 100 warm-ups and 500 timed calls per round. Segmentation totals 100 timed calls, with 5 warm-ups and 20 timed calls per round. End-to-end runs use 60 classification images, all 5,000 detection validation images, and 15 segmentation images. End-to-end timing includes task-specific preprocessing and postprocessing; these sample sets and pipelines differ by task.

## Fine-tuning and pruning comparisons

Models: ResNet18, YOLOv8n, and LR-ASPP-MobileNetV3. Arms: `finetune` and `prune`. Each uses 20 recovery-training epochs and seeds 42, 43, 44, sharing initialization, splits and training settings within each task.

- Classification: ImageNet subset of 20,000 training, 1,000 internal validation and 5,000 final test images; calibration: 1,000 training images.
- Detection: 5,000 training and 500 internal validation images from COCO train2017; evaluation: complete val2017; calibration: 100 training images. Final INT8 evaluation uses six-branch outputs and CPU decoding.
- Segmentation: 1,300 training and 164 internal validation images; evaluation: complete VOC2012 validation; calibration: 500 training images.

Input dimensions match the three task sizes above. Timing uses five rounds of 20 warm-up and 100 timed calls per model. The manuscript reports the mean of per-seed invocation medians.

Accuracy fields in the pruning JSON summaries (`mean_top1`, `mean`, `accuracy`, and `AP`) use a 0-1 scale. Multiply by 100 for percentages. `sample_std` is the sample standard deviation on the same 0-1 scale. Fields ending in `_ms` are milliseconds; `_mib` are MiB; `_bytes` or `bytes` are bytes. MAC counts and parameter counts are absolute counts, and reduction fields ending in `_percent` use a 0-100 scale. Structure records identify task and seed through their relative `source` paths.

`deployment_records.json` preserves the source record status fields, including incomplete or intermediate measurements. Use the task-specific final summaries for reported accuracy and the final six-branch detection file for detection latency. It is not a declaration that every intermediate record is final.
