# NanoDet-Plus ONNX video benchmark (Google Colab)

Compare four pretrained NanoDet-Plus object detection models on the **same video** in Google Colab. The notebook downloads ONNX models and COCO labels, reads a direct video URL, produces annotated MP4 files, measures processing speed, displays all four results, and can upload the models and results to Hugging Face.

## Models

| Model | Input | Published COCO mAP (0.5:0.95) |
| --- | ---: | ---: |
| NanoDet-Plus-m | 320 × 320 | 27.0 |
| NanoDet-Plus-m | 416 × 416 | 30.4 |
| NanoDet-Plus-m-1.5x | 320 × 320 | 29.9 |
| NanoDet-Plus-m-1.5x | 416 × 416 | 34.1 |

The accuracy numbers are from the [NanoDet project](https://github.com/RangiLyu/nanodet); they are **not** measured on your sample video. The models predict 80 COCO classes. The notebook displays `person`, `car`, and `motorcycle` by default (COCO class IDs 0, 2, and 3).

## Run in Colab

1. Open the accompanying `.ipynb` notebook in [Google Colab](https://colab.research.google.com/). A CPU runtime works; the notebook uses ONNX Runtime's `CPUExecutionProvider` for all four models.
2. Run the install and model download cell. It obtains four ONNX files and creates `labels.txt` from NanoDet's configuration.
3. Set `VIDEO_URL` to a direct HTTP(S) video URL and run the download cell. Signed CDN or object storage URLs work while they are valid and accessible to Colab. A browser-local `blob:` URL will not work; use the underlying HTTP(S) URL instead.
4. Set `MAX_FRAMES = 300` for an initial test, or `None` to process the full video. Run the inference cell, then the comparison cell.
5. Review the four annotated videos and the FPS table. Run the analytics cell for a chart and CSV. The optional Hugging Face cells publish models, labels, and example results using your own write token.

```python
VIDEO_URL = "https://your-cdn.example.com/sample-video.mp4"
MAX_FRAMES = 300  # Set to None for the complete video
TARGET_CLASSES = {"person", "car", "motorcycle"}  # None for all 80 classes
SCORE_THRESHOLD = 0.35
NMS_THRESHOLD = 0.60
```

## Outputs

| File | Description |
| --- | --- |
| `models/*.onnx` | Four downloaded pretrained models |
| `labels.txt` | COCO names, one per line, zero-based |
| `outputs/*_annotated.mp4` | One H.264 video per model with boxes and class scores |
| `outputs/fps_comparison.csv` | Per-model frame count, FPS, and prediction count |
| `outputs/model_analytics.csv` | Extended speed and prediction statistics |
| `outputs/model_analytics.png` | Comparison charts |

The notebook measures:

- **Inference-only FPS:** ONNX Runtime execution time divided into the number of processed frames.
- **Processing FPS:** includes reading, preprocessing, inference, decoding, drawing, and writing frames; excludes the final H.264 conversion.
- **Source FPS:** playback rate recorded in the input video. Output MP4 files retain this playback rate.
- **Real-time ratio:** processing FPS divided by source FPS. This is an offline throughput comparison, not a live RTSP latency measurement.

Prediction counts show how many boxes each model produced at the selected threshold. They do **not** measure precision, recall, or mAP. Those metrics require ground-truth annotations for the video. Colab CPU allocation and video resolution can change measured FPS, so compare models from the same run and input video.

## Publish to Hugging Face

The optional notebook cells create one model repository with each ONNX file beside its `labels.txt`, then upload the sample video, annotated results, CSVs, and chart under `benchmarks/sample-video-01/`. Provide a Hugging Face write token through `notebook_login()`; never commit a token to GitHub. Review footage before publishing it in a public repository.

## Attribution

Models and model configuration: [RangiLyu/NanoDet](https://github.com/RangiLyu/nanodet), licensed under [Apache-2.0](https://github.com/RangiLyu/nanodet/blob/main/LICENSE). This notebook uses the authors' pretrained models and does not claim to train them.
