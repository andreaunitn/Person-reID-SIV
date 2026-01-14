# Person Re-Identification: A Strong Baseline & Bag of Tricks

**Course:** Signal, Image and Video | [cite_start]**University:** University of Trento [cite: 3]  
[cite_start]**Authors:** Andrea Tomasoni and Michele Lamon [cite: 2]

## 📝 Abstract

[cite_start]This project explores the field of Person Re-Identification (ReID), the task of correctly matching individuals across different camera views[cite: 7]. [cite_start]The primary goal was to replicate the state-of-the-art techniques presented by Luo et al. in *Bag of Tricks and a Strong Baseline for Deep Person Re-identification*[cite: 9, 10].

[cite_start]Starting from a baseline Open-ReID library, we progressively implemented six specific training "tricks" to enhance performance[cite: 11, 13]. [cite_start]Additionally, we developed a real-time application integrating the ReID model with YOLOv7 for identifying people in live video feeds[cite: 17].


## 🚀 Methodology

[cite_start]Our model uses a ResNet50 backbone initialized with ImageNet weights[cite: 52]. We implemented the following pipeline modifications to achieve our results:

1.  [cite_start]**Warm-up Learning Rate:** Linearly increasing learning rate for the first 10 epochs to improve generalization[cite: 68].
2.  [cite_start]**Random Erasing Augmentation (REA):** Randomly occluding parts of the image to increase robustness against partial occlusions[cite: 77].
3.  [cite_start]**Label Smoothing (LS):** Preventing overfitting by making the model less confident on training classification tasks[cite: 92].
4.  [cite_start]**Last Stride = 1:** Removed spatial downsampling in the final layer to capture more detailed features ($16 \times 8$ feature map)[cite: 106].
5.  [cite_start]**BNNeck:** Adding a Batch Normalization layer between features and the classifier to balance Triplet Loss and Cross-Entropy Loss[cite: 127].
6.  [cite_start]**Center Loss:** Minimizing intra-class variation to encourage feature clustering[cite: 140].

## 📊 Results

[cite_start]We evaluated our model on the **Market1501** and **DukeMTMC-reID** datasets[cite: 15].

| Metric | Market1501 | DukeMTMC-reID |
| :--- | :--- | :--- |
| **Rank-1 Accuracy** | [cite_start]**94.8%** [cite: 179] | [cite_start]71.6% [cite: 180] |
| **mAP** | [cite_start]**84.0%** [cite: 179] | [cite_start]72.0% [cite: 180] |

[cite_start]*Note: Rank-1 accuracy on DukeMTMC-reID was lower than the reference paper, potentially due to dataset characteristics interacting with our specific architecture[cite: 171, 172].*

## 🛠️ Environment Setup

We recommend using a virtual environment to manage dependencies.

### 1. Create the Environment
```bash
# Install virtualenv
python3 -m pip install virtualenv

# Create environment (replace /path/to/ with your desired location)
python3 -m venv /path/to/env_name
```

### 2. Activate the Environment
* **Unix/macOS:**
    ```bash
    source env_name/bin/activate
    ```
* **Windows:**
    ```bash
    source env_name/Scripts/activate
    ```
To deactivate the environment later, simply run:
```bash
deactivate
```

### 3. Install Dependencies
Select the requirements file based on your hardware configuration:

* **NVIDIA GPU (CUDA):**
    ```bash
    python3 -m pip install -r requirementsCUDA.txt
    ```
* **Apple Silicon:**
    ```bash
    python3 -m pip install -r requirementsMAC.txt
    ```

*Note: Hardware acceleration (GPU) is highly recommended. [cite_start]The code requires Torch with hardware acceleration enabled to function correctly[cite: 1].*

### 4. Install the Library
You must install the open-source library with our custom modifications. **Important:** This step must be repeated every time you modify the source code to ensure changes are applied.
```bash
python3 setup.py install
```

## 📂 Datasets

[cite_start]Please download the **Market1501** and **DukeMTMC-reID** datasets [cite: 15] and extract them into your data directory.
[Download Datasets via Google Drive](https://drive.google.com/drive/folders/1pTjMzG4aoc4MgSCrXbQocREQG_HDSMWq?usp=sharing)

## 🏃 Usage

### Training
To train the model, navigate to the `tricks` directory. You can combine specific "tricks" (1-6) as detailed in our report.

**Basic Example (Training with all 6 tricks + validation data):**
```bash
cd tricks
python3 triplet_loss.py -t 6 --combine-trainval
```

**Command Line Arguments:**

| Argument | Description |
| :--- | :--- |
| `-t [1-6]` | Select the number of tricks to apply (cumulative 1-6). |
| `-d [dataset]` | Select dataset (default: `market1501` or `dukemtmc`). |
| `-b [int]` | Batch size (calculated as $P \times K$). |
| `--epochs [int]` | Total number of training epochs. |
| `--num-instances [int]` | Number of images per identity in a batch ($K$). |
| `--combine-trainval` | Use validation images during training (recommended for best results). |
| `--re-ranking` | Enable re-ranking to improve retrieval accuracy. |
| `--cross-domain` | Train on one dataset and evaluate on the other. |
| `--data-dir [path]` | Path to the dataset directory. |
| `--logs-dir [path]` | Path to save training logs. |
| `--evaluate` | Execute evaluation only (skips training). |
| `--resume [path]` | Resume training from a specific checkpoint. |
| `-j [int]` | Number of data loading workers. |
| `--height [int]` | Input image height (default: 256). |
| `--width [int]` | Input image width (default: 128). |

### Real-Time Application
[cite_start]We developed a real-time identification application that integrates **YOLOv7-tiny** (via OpenCV) for person detection and our ReID model for identity feature extraction[cite: 315]. [cite_start]The application processes individual frames from a webcam feed to identify individuals[cite: 320].

* [cite_start]**Detection:** YOLOv7-tiny locates people in the video frame[cite: 315, 320].
* [cite_start]**Identification:** Bounding boxes are cropped and fed into the ReID model to extract feature vectors[cite: 321]. [cite_start]These vectors are compared using cosine similarity; if a match is found below a threshold, the existing ID is retrieved, otherwise a new ID is assigned[cite: 322, 323].
* [cite_start]**Interface:** A **PyQt6** GUI displays the video feed with bounding boxes and ID labels, along with a counter for the total number of people detected[cite: 316, 325, 326].

To run the application (ensure your webcam is connected):
```bash
python3 app/appYolo.py
```
## 📄 Reference

If you use this code or findings, please credit our project report and the original baseline paper:

* [cite_start]**Project Report:** [Signal, Image and Video Project Report](https://github.com/andreaunitn/Signal-Image-and-Video-Project/files/12252926/SIV.pdf) [cite: 1]
* [cite_start]**Original Paper:** *Bag of Tricks and a Strong Baseline for Deep Person Re-identification*, H. Luo et al., CVPR Workshops, 2019. [cite: 10, 439]

**BibTeX:**
```bibtex
@inproceedings{luo2019bag,
  title={Bag of tricks and a strong baseline for deep person re-identification},
  author={Luo, Hao and Gu, Youzhi and Liao, Xingyu and Lai, Shenqi and Jiang, Wei},
  booktitle={Proceedings of the IEEE/CVF conference on computer vision and pattern recognition workshops},
  pages={0--0},
  year={2019}
}
```

## 👨‍💻 Authors & Contact

*   **Andrea Tomasoni** - [GitHub](https://github.com/andreaunitn)
*   **Michele Lamon** - University of Trento - [GitHub](https://github.com/gelsounitn)

For any questions regarding the dataset or the implementation, please open an issue in this repository or contact us at andrea.tomasoni2000@gmail.com - michele.lamon22@gmail.com.
