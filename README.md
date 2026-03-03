# Indonesian Automatic License Plate Recognition (ALPR)

An end-to-end ALPR system for Indonesian license plates using fine-tuned YOLOv11 for plate detection and PaddleOCR for text recognition, served via Golang + FastAPI.

***

## Overview

    Input Image → YOLOv11 (Plate Detection) → Crop Plate → PaddleOCR → Plate Text

Fine-tuned on ~1,383 Indonesian plate images from Kaggle.

***

## Model Performance

Evaluated on val split (277 images, 20% of dataset):

| Model | mAP50 | mAP50-95 | Precision | Recall |
|---|---|---|---|---|
| Original (GavinAdriel pretrained) | 0.4016 | 0.0747 | 0.5673 | 0.4390 |
| Fine-tuned v1 (50 epochs) | 0.7529 | 0.4866 | 0.7522 | 0.7206 |
| **Final (tuned + augmentation)** | **0.8283** | **0.5375** | **0.8130** | **0.8000** |

### Training Details

- **Base model**: YOLOv11n pretrained on Indonesian plates (GavinAdriel)
- **Dataset**: 1,106 train / 277 val images
- **Epochs**: 74 (early stopping, patience=15)
- **Optimizer**: AdamW (lr0=0.00341, momentum=0.859)
- **Augmentations**: HSV, fliplr, mosaic, mixup, copy-paste

### Loss Curve & Metrics






***

## Download Model

| File | Description |
|---|---|
| `best.pt` | YOLOv11n fine-tuned (PyTorch) |
| `best.onnx` | YOLOv11n fine-tuned (ONNX) |

👉 [Download from Releases](https://github.com/USERNAME/REPO/releases/tag/v1.0.0)

***

## Project Structure

    ├── assets/                  # Training visualizations
    │   ├── final_loss_curve.png
    │   ├── results.png
    │   ├── PR_curve.png
    │   └── confusion_matrix.png
    ├── inference/               # Python FastAPI inference service
    │   ├── main.py
    │   ├── requirements.txt
    │   └── Dockerfile
    ├── api/                     # Golang Gin API gateway
    │   ├── main.go
    │   └── Dockerfile
    ├── training/                # Kaggle training notebook
    │   └── alpr-pretrained.ipynb
    ├── models/                  # Model weights (see Releases)
    │   └── .gitkeep
    ├── docker-compose.yml
    ├── LICENSE
    └── README.md

***

## 🛠️ Tech Stack

| Component | Technology | Status |
|---|---|---|
| Plate Detection | YOLOv11n (Ultralytics) | ✅ Done |
| Text Recognition | PaddleOCR | 🚧 Planned |
| Inference API | Python FastAPI | 🚧 Planned |
| API Gateway | Golang (Gin) | 🚧 Planned |
| Deployment | Docker Compose | 🚧 Planned |

***

## Quick Start

**Prerequisites**: Docker & Docker Compose, model weights from Releases placed in `models/`

**1. Clone & setup**

    git clone https://github.com/USERNAME/REPO.git
    cd REPO
    cp /path/to/best.onnx models/

**2. Run**

    docker compose up --build

**3. Call the API**

    curl -X POST http://localhost:8080/detect -F "image=@car.jpg"

**Response**

    { "plate_text": "B 1234 XYZ", "confidence": 0.91, "bbox":  }[1]

***

## Training Pipeline

Full notebook: `training/alpr-pretrained.ipynb` (Kaggle, Tesla T4 GPU)

1. Convert COCO annotations → YOLO format
2. Train/val split 80/20
3. Fine-tune YOLOv11n (freeze 10 layers, 50 epochs)
4. Hyperparameter tuning via `model.tune()` (20 iterations)
5. Final training with best hyperparameters + augmentation

***

## 📁 Dataset

| Dataset | Source | Format |
|---|---|---|
| Plate Detection | [Indonesian Plate Number - Kaggle](https://www.kaggle.com/datasets/linkgish/indonesian-plate-number-from-multi-sources) | COCO JSON |
| Plate Text (OCR) | Same source (plate_text_dataset) | label.csv |

***

## 🙏 Acknowledgements

- [GavinAdriel](https://www.kaggle.com/models/gavinadriel) — pretrained Indonesian plate model
- [Ultralytics YOLOv11](https://github.com/ultralytics/ultralytics)
- [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR)
- [Indonesian Plate Number Dataset by linkgish](https://www.kaggle.com/datasets/linkgish/indonesian-plate-number-from-multi-sources)

***

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.
