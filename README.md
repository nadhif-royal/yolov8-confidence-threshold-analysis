# YOLOv8 Confidence Threshold Analysis

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/nadhif-royal/yolov8-confidence-threshold-analysis/blob/main/YOLOv8_Confidence_Threshold_235150201111074.ipynb)

**Comparative Analysis of Multi-Object Detection Results from a Pre-trained YOLOv8 Model Using Varying Confidence Thresholds**

A Google Colab study that runs the pre-trained `yolov8n.pt` model on five multi-object images at two confidence thresholds (**0.25** and **0.60**), visualizes the detections, and compares the two settings quantitatively and qualitatively.

| | |
|---|---|
| **Author** | Nadhif Rif'at Rasendriya |
| **Student ID (NIM)** | 235150201111074 |
| **Program** | Informatics Engineering, Faculty of Computer Science (FILKOM), Universitas Brawijaya |
| **Course** | CIF61339 - Digital Image Processing and Computer Vision |

## Objectives

1. Run a pre-trained YOLO model on several multi-object images in Google Colab.
2. Display each original image next to its detection results (bounding box, class name, confidence score).
3. Repeat the detection with two confidence thresholds, 0.25 and 0.60.
4. Compare the two runs and analyze the precision-recall trade-off.

## Method

| Setting | Value |
|---|---|
| Model | `yolov8n.pt` (Ultralytics, pre-trained on COCO, 80 classes) |
| Confidence thresholds | 0.25 and 0.60 (the only variable) |
| NMS IoU threshold | 0.7 (fixed) |
| Input size | 640 (fixed) |
| Environment | Google Colab, T4 GPU, Ultralytics 8.4.191, PyTorch 2.11.0 |

The notebook is organized step by step: setup, configuration, dataset preparation, inference, side-by-side visualization, per-detection table, per-image comparison, evaluation against a manual ground truth, written analysis, and export.

## Dataset

Five multi-object images from the internet (not self-captured):

| File | Scene | Source |
|---|---|---|
| `gambar1.jpg` | Kitchen and cooking activity | [COCO val2017 #397133](http://images.cocodataset.org/val2017/000000397133.jpg) |
| `gambar2.jpg` | Two cats on a sofa | [COCO val2017 #39769](http://images.cocodataset.org/val2017/000000039769.jpg) |
| `gambar3.jpg` | City street at dusk | [Unsplash](https://images.unsplash.com/photo-1458169495136-854e4c39548a) |
| `gambar4.jpg` | Workspace with laptop | [Unsplash](https://images.unsplash.com/photo-1498050108023-c5249f4df085) |
| `gambar5.jpg` | Baseball field | [COCO train2017 #411975](http://images.cocodataset.org/train2017/000000411975.jpg) |

- COCO images originate from Flickr and carry per-image licenses (see the COCO annotation `license` field). Unsplash images are covered by the [Unsplash License](https://unsplash.com/license).
- Unsplash photographer credits: _add names here_.
- All images remain the property of their respective owners and are included only for academic purposes.

## Results

### Detections per image

| Image | Detections @0.25 | Detections @0.60 | Lost | % lost |
|---|---|---|---|---|
| gambar1 | 9 | 4 | 5 | 55.6% |
| gambar2 | 5 | 3 | 2 | 40.0% |
| gambar3 | 20 | 10 | 10 | 50.0% |
| gambar4 | 7 | 3 | 4 | 57.1% |
| gambar5 | 7 | 5 | 2 | 28.6% |
| **Total** | **48** | **25** | **23** | **47.9%** |

### Evaluation against manual ground truth

Count-level matching per class (not IoU-based), micro-averaged over the five images:

| Threshold | Precision | Recall | F1 | TP / FP / FN |
|---|---|---|---|---|
| 0.25 | 0.792 | 0.927 | 0.854 | 38 / 10 / 3 |
| 0.60 | 1.000 | 0.610 | 0.758 | 25 / 0 / 16 |

### Key findings

- On these five images, **0.25 gave higher recall and F1**, and its F1 was higher than or equal to that of 0.60 on every image.
- **0.60 gave perfect precision (no false positives)** but removed 13 correct detections along with 10 false ones, missing about 39% of the ground-truth objects.
- Many correct but difficult objects (occluded, truncated, dim, or small background objects) have mid-range confidence (0.26-0.56), so a high threshold discards them.
- Most false positives had confidence below 0.35 (for example hanging herbs read as `potted plant`, bokeh lights read as `traffic light`), but correct and incorrect detections overlap in the 0.26-0.35 range, so no threshold separates them cleanly.
- The average confidence rises at 0.60 only because low-confidence detections are removed (a selection effect), not because the model becomes more certain.

The full discussion is in Step 11 of the notebook.

## Repository structure

```
.
├── YOLOv8_Confidence_Threshold_235150201111074.ipynb   # Main Colab notebook (with outputs)
├── YOLOv8_Confidence_Threshold_235150201111074.pdf     # PDF export of the executed notebook
├── dataset/                                            # The 5 input images
└── hasil_235150201111074/                              # Results: annotated images (conf025, conf060),
                                                        # figures, and CSV tables
```

## How to run

1. Click the **Open in Colab** badge above (or upload the notebook to [Google Colab](https://colab.research.google.com)).
2. Select **Runtime > Run all**. A GPU runtime is optional; `yolov8n` runs on CPU for five images.
3. The notebook downloads the images automatically. If a download fails, it asks you to upload the files manually.
4. To use your own photos, set `MODE_SUMBER = "upload"` in Step 3 and update `GROUND_TRUTH` accordingly.

## Limitations

- Only five images, one model (the smallest `yolov8n`), two thresholds, and a single run; conclusions apply to this dataset and should not be generalized.
- The ground truth was annotated manually and revised after viewing the detection results, so it carries a risk of bias. Counts of small, blurry background objects (especially in `gambar3`) depend on visual judgment.
- Evaluation is count-based per class and does not check box locations (no IoU or mAP).
- `gambar5` comes from COCO `train2017`, which was used to train the model, so its confidence may be optimistic (possible data leakage).
- The images come from the internet rather than being self-captured.

## References

- Redmon, J., Divvala, S., Girshick, R., & Farhadi, A. (2016). *You Only Look Once: Unified, Real-Time Object Detection*. IEEE CVPR.
- Jocher, G., Chaurasia, A., & Qiu, J. (2023). *Ultralytics YOLOv8*. https://github.com/ultralytics/ultralytics
- Lin, T.-Y., et al. (2014). *Microsoft COCO: Common Objects in Context*. ECCV. https://cocodataset.org
- Sari, Y. A. (2025). *YOLO* [Lecture slides]. CIF61339, Faculty of Computer Science, Universitas Brawijaya.

## License

The notebook and written analysis are provided for academic purposes. Images keep their original licenses (see [Dataset](#dataset)). The Ultralytics library is licensed under AGPL-3.0.
