# Brain Tumor Classification

An experimental Python project that classifies brain MRI images into four categories using handcrafted image features and stacking ensembles.

[![Tests](https://github.com/crisdanrodriguez/brain-tumor-classification/actions/workflows/tests.yml/badge.svg)](https://github.com/crisdanrodriguez/brain-tumor-classification/actions/workflows/tests.yml) [![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/) [![scikit-learn](https://img.shields.io/badge/scikit--learn-1.5%2B-F7931E?logo=scikitlearn&logoColor=white)](https://scikit-learn.org/) [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE) [![Last commit](https://img.shields.io/github/last-commit/crisdanrodriguez/brain-tumor-classification?label=last%20commit)](https://github.com/crisdanrodriguez/brain-tumor-classification/commits/main/)

**Created:** 2021-11-13 | **Last Updated:** 2026-04-19

## Overview

This academic machine learning project explores classification of MRI images labeled `no_tumor`, `glioma_tumor`, `meningioma_tumor`, or `pituitary_tumor`. It uses image statistics and gray-level co-occurrence matrix (GLCM) texture features as an alternative to learning features with a neural network.

The repository includes the training and testing images, a precomputed feature table, a scikit-learn model comparison, a separate manual stacking implementation, exploratory analysis, and a project report. It is intended for studying feature engineering and ensemble methods; the repository does not establish clinical suitability.

## Key Features

- Extracts 12 numeric features per image: six pixel-distribution statistics and six GLCM texture properties.
- Compares k-nearest neighbors, decision tree, Gaussian naive Bayes, and a stacking ensemble using scikit-learn.
- Implements the three base learners and stacking prediction logic separately in Python, with an interactive image-path prompt.
- Includes a precomputed CSV, exploratory notebook, three smoke tests, and a GitHub Actions test workflow.

## Tech Stack

- **Language:** Python 3.10 or newer.
- **Data and imaging:** NumPy, pandas, SciPy, and scikit-image.
- **Models and evaluation:** scikit-learn.
- **Exploration and testing:** Jupyter, Matplotlib, `unittest`, and GitHub Actions.

Minimum package versions are specified in [`pyproject.toml`](pyproject.toml); [`requirements.txt`](requirements.txt) installs the local package with its development extras.

## Methodology

[`feature_extraction.py`](src/brain_tumor_classification/feature_extraction.py) converts each image to grayscale, resizes it to 512 × 512 pixels, and calculates mean, variance, standard deviation, skewness, kurtosis, entropy, contrast, dissimilarity, homogeneity, angular second moment, energy, and correlation. Class directory names provide the numeric labels. The committed [`feature table`](data/brain_tumor_dataset.csv) has 3,206 rows and is built from `data/Training/`.

[`stacking_sklearn.py`](src/brain_tumor_classification/stacking_sklearn.py) compares three base classifiers with a stacking classifier whose final estimator is k-nearest neighbors. It evaluates accuracy with five-fold stratified cross-validation repeated four times. [`stacking_from_scratch.py`](src/brain_tumor_classification/stacking_from_scratch.py) uses repeated stratified 80/20 train/test splits and trains its meta learner on predictions made over the same training rows used to fit the base learners. That design can make its reported scores optimistic and means the two implementations should not be treated as a controlled, like-for-like comparison. The bundled `data/Testing/` images are used by a smoke test but are not the holdout set in either evaluation script.

## Getting Started

### Prerequisites

- Python 3.10 or newer and `pip`.
- The bundled image data if rebuilding the feature table or using image-path prediction.

### Install

From the repository root:

```bash
git clone https://github.com/crisdanrodriguez/brain-tumor-classification.git
cd brain-tumor-classification
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

On Windows, activate the environment with `.venv\Scripts\activate` instead.

### Run tests

```bash
python -m unittest discover -s tests -v
```

The tests check the committed CSV shape, model construction, and feature extraction on one bundled image. They do not validate the reported model accuracies.

## Usage

Run these commands from the repository root after installation:

```bash
# Compare scikit-learn models using the committed feature table.
python -m brain_tumor_classification.stacking_sklearn

# Evaluate the manual implementation, then enter an image path when prompted.
python -m brain_tumor_classification.stacking_from_scratch

# Open the exploratory notebook.
jupyter notebook notebooks/eda.ipynb
```

To regenerate the CSV from `data/Training/`, run the following command. It **overwrites the tracked feature table**; the current extraction code also writes an index column that the model loaders expect.

```bash
python -m brain_tumor_classification.feature_extraction
```

The notebook reads `data/brain_tumor_dataset.csv` relative to the working directory, so launch Jupyter from the repository root. It also imports seaborn, which is not declared in the project dependencies; install it separately if you intend to run the notebook cells.

## Project Structure

```text
brain-tumor-classification/
├── .github/workflows/tests.yml            # Python 3.10–3.12 test matrix
├── data/
│   ├── Training/                          # Images used to generate features
│   ├── Testing/                           # Separate bundled images
│   └── brain_tumor_dataset.csv             # Precomputed training features
├── docs/
│   ├── documentation/                     # Report and implementation diagram
│   └── results/                           # Project presentation
├── notebooks/eda.ipynb                    # Exploratory analysis
├── src/brain_tumor_classification/        # Extraction and model modules
├── tests/test_basic.py                    # Smoke tests
├── pyproject.toml                         # Package metadata and dependencies
└── requirements.txt                       # Editable install with dev extras
```

## Results and Demonstration

The [project presentation](docs/results/btc_presentation.pptx) reports these historical accuracies:

| Model | Manual implementation | scikit-learn implementation |
| --- | ---: | ---: |
| k-nearest neighbors | 58.28% | 59.55% |
| Gaussian naive Bayes | 55.65% | 54.02% |
| Decision tree | 73.21% | 68.75% |
| Stacking | 66.98% | 65.21% |

These are figures from the presentation, not measurements reproduced during this documentation update. The presentation describes 11 features, while the current extraction code produces 12; the current scripts also use different evaluation procedures. The manual stacking workflow trains meta features on in-sample base-model predictions. See the [technical report](docs/documentation/brain_tumor_classification_with_stacking_method.pdf), [implementation diagram](docs/documentation/implementation_diagram.png), [EDA notebook](notebooks/eda.ipynb), and [related article](https://towardsai.net/p/l/stacking-ensemble-method-for-brain-tumor-classification-performance-analysis) for additional project material.

## Project Status

Experimental academic project. The repository provides code, data, and historical presentation material, but its evaluation results have not been independently reproduced here. The latest default-branch commit is a documentation update dated 2026-04-19; this alone does not establish ongoing model development.

## License

Licensed under the [MIT License](LICENSE).
