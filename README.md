# EEG Emotion Recognition
*Experimental BSc Thesis in Biomedical Engineering, University of Pisa, Academic Year 2024/2025*

**Original title:** *Riconoscimento automatico dello stato emotivo tramite analisi di segnali elettroencefalografici*

**Candidate:** Erica Ginevri
**Supervisor:** Ing. Vincenzo Catrambone, Research Center E. Piaggio

## 📄 Documents

* Thesis: [`EEGEmotionRecognitionThesis.pdf`](EEGEmotionRecognitionThesis.pdf)
* Presentation: [`EEGEmotionRecognitionPresentation.pdf`](EEGEmotionRecognitionPresentation.pdf)

Both documents are in Italian.

## Goal

Can we tell which emotion a person is feeling from their brain activity alone?

This work tackles a **multiclass classification** problem: recognising whether a subject is looking at a **positive**, **neutral** or **negative** image from the EEG recorded at the same time. Emotions are described with the two-dimensional **valence–arousal** model.

## Dataset

| | |
|---|---|
| Subjects | 35 (mean age 22.5 ± 2.4) |
| Stimuli | 72 images per subject from the International Affective Picture System (IAPS) |
| Categories | Positive (high arousal), neutral (low arousal), negative (high arousal) |
| Trial | 3 s fixation cross → 6 s image → 6–8 s fixation cross |
| EEG | 32-electrode cap, international 10–20 system, 1000 Hz |
| Valid trials | 2,468 of 2,520 (52 removed because of artefacts) |

The experiment was approved by the local ethics committee. The raw data are not included in this repository.

## Pipeline

**1. Preprocessing** (MATLAB R2023b and EEGLAB)
* downsampling to 500 Hz
* re-referencing to the mastoids
* band-pass filter, 4–45 Hz
* artefact removal with Independent Component Analysis (ICA)

**2. Feature extraction**
* Power Spectral Density per channel, Hamming window, 2 s sliding window with 0.25 s step
* mean power in four bands: θ (4–8 Hz), α (8–12 Hz), β (12–30 Hz), γ (30–45 Hz)

**3. Data organisation**
* 3D matrix 2468 × 4 × 30 (trials × bands × channels)
* reshaped into a 2D matrix 2468 × 120, plus a target vector (1 positive, 2 neutral, 3 negative)

**4. Classification**
* Fine KNN (k = 1, Euclidean distance)
* 5-fold cross-validation
* comparison with and without PCA (50 principal components)

## Results

| Model | Accuracy |
|---|---|
| Fine KNN, no PCA | **44.9%** |
| Fine KNN, with PCA (50 components) | 42.8% |
| Chance level | 33.3% |

Both models perform above chance. Neutral images are the hardest to recognise and are often confused with positive or negative ones. PCA did not help: the useful information seems spread across the original features, and a linear reduction does not keep it.

## Limitations and future work

**Limitations:** the noisy nature of EEG signals, strong variability between subjects, and a small dataset.

**Next steps:**
* **Leave-One-Subject-Out (LOSO)** validation, to test how well the model generalises to new people
* non-linear dimensionality reduction and more advanced classifiers, such as SVM or deep learning
* larger, more balanced and more varied datasets
