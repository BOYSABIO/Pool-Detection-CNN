
# Pool Detection CNN

A computer vision pipeline for detecting swimming pools in aerial imagery using YOLOv8 with Oriented Bounding Boxes (OBB).

---

## Project Structure

```
Pool-Detection-CNN/
├── datasets/           # Downloaded datasets from Roboflow
├── models/             # Trained YOLOv8-OBB models and logs
├── notebooks/          # Jupyter notebooks for training and evaluation
├── references/         # Research papers and related materials
├── images/             # GroundingDINO input/output
├── requirements.txt    # Python dependencies
└── README.md           # Project documentation
```

---

## Getting Started

### Prerequisites

- Python 3.8+
- pip
- Git
- Roboflow API key (set in `.env`)

### Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/BOYSABIO/Pool-Detection-CNN.git
   cd Pool-Detection-CNN
   ```

2. **Create and activate a virtual environment (optional but recommended):**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**

   ```bash
   pip install -r requirements.txt
   ```

4. **Set up environment variables:**

   Create a `.env` file in the root directory and add your Roboflow API key:

   ```env
   ROBOFLOW_API_KEY=your_api_key_here
   ```

---

## Dataset Management

Datasets are managed via Roboflow.

### Downloading a Dataset

```python
from roboflow import Roboflow
import os
from dotenv import load_dotenv

load_dotenv()
rf = Roboflow(api_key=os.getenv("ROBOFLOW_API_KEY"))
project = rf.workspace("spencer-kmi6v").project("pool-detect-cnn")
version = project.version(5)
dataset = version.download("yolov8-obb", location="datasets/")
```

This will download the dataset to the `datasets/` directory.

---

## Model Training

### Training with YOLOv8-OBB

Ensure you have YOLOv8 installed (version ≤ 8.3.40 for OBB support):

```bash
pip install "ultralytics<=8.3.40"
```

Train the model using the following script:

```python
from ultralytics import YOLO

model = YOLO("yolov8n-obb.pt")
results = model.train(
    data="datasets/Pool-Detect-CNN-5/data.yaml",
    epochs=100,
    imgsz=640,
    project="models/obb",
    name="train"
)
```

This will save the training results in `models/obb/train/`.

---

## Evaluation & Inference

### Validation

Evaluate the trained model on the validation set:

```bash
yolo task=detect mode=val model=models/obb/train/weights/best.pt data=datasets/Pool-Detect-CNN-5/data.yaml project=models/obb name=val
```

### Inference

Run inference on test images:

```bash
yolo task=detect mode=predict model=models/obb/train/weights/best.pt conf=0.25 source=datasets/Pool-Detect-CNN-5/test/images save=True project=models/obb name=predict
```

Results will be saved in `models/obb/predict/`.

---

## Versioning & Dataset Management

To manage different versions of your dataset in Roboflow:

1. **Before making changes**, create a new version in Roboflow to preserve the current state.
2. **Modify annotations** (e.g., change bounding box orientations) in the new version.
3. **Download the new version** using the dataset management script provided above.

This approach ensures reproducibility and allows you to revert to previous versions if needed.

---

## Model Performance

After training, performance metrics such as precision, recall, and mAP are logged in the `models/obb/train/` directory.

You can visualize predictions using:

```python
from IPython.display import Image

Image(filename='models/obb/train/val_batch0_pred.jpg', width=600)
```

---

## Troubleshooting

- **Dataset Not Downloading:** Ensure the `location` parameter in the `download()` function is set correctly and that the specified directory exists.
- **Missing Images Error:** Verify that the paths in your `data.yaml` file are correct and that the image files are present in the specified directories.
- **Training Logs Location:** Use the `project` and `name` parameters in the `train()` function to specify where training logs and results should be saved.


---

## 🙌 Acknowledgments

- [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics)
- [Roboflow](https://roboflow.com/)
