# EcoVision AI

**An Integrated Artificial Intelligence Framework for Brick Kiln Classification and Environmental Pattern Analysis Using Multi-Spectral Satellite Imagery**

**Author:** Suravi Poudel  
**Institution:** St. Xavier's College, Maitighar, Kathmandu, Nepal  
**Research Conference:** 3rd International Conference of St. Xavier's College (ICSXC 2026)  
**Conference Dates:** September 24–25, 2026  
**Publication:** Official ICSXC 2026 Conference Abstract Book, Page 48

---

## Overview

EcoVision AI is a research proof of concept exploring how machine learning and freely available Sentinel-2 satellite imagery can support brick kiln classification and environmental pattern analysis in the Kathmandu Valley, Nepal.

The project combines two research components:

1. **Cross-regional brick kiln classification:** Evaluating whether a machine learning model trained on South Asian brick kiln imagery from Bangladesh can generalize to a reference dataset in the Kathmandu Valley without local fine-tuning.
2. **Environmental pattern analysis:** Investigating differences in vegetation indices around kiln and non-kiln locations and examining how these indicators vary with distance from known brick kiln locations.

The study explores the potential of AI-assisted satellite monitoring for environmental screening. It also highlights the challenges of transferring machine learning models across geographic regions.

The research abstract was published in the official ICSXC 2026 Conference Abstract Book, and the work was presented at the conference in September 2026.

## Research Questions

**RQ1 — Cross-Regional Transferability**

How well does a brick kiln classifier trained on Bangladesh imagery generalize to a manually reviewed reference dataset in the Kathmandu Valley?

**RQ2 — Environmental Pattern Analysis**

How do the Normalized Difference Vegetation Index (NDVI) and Enhanced Vegetation Index (EVI) differ between kiln and non-kiln reference locations, and how do these indicators vary with distance from known kilns?

## Methodology

### 1. Dataset and Image Classification

The classification workflow uses the `m-brick-kiln` dataset from GEO-Bench.

**Dataset:** [GEO-Bench on Hugging Face](https://huggingface.co/datasets/recursix/geo-bench-1.0)

The dataset splits used in the experiment were:

| Dataset Split | Number of Images | Details |
|---|---:|---|
| Training | 15,063 | Includes 5,063 kiln examples |
| Validation | 999 | Used during model development |
| Held-out Test | 999 | Includes 336 kiln examples |

The original image patches are 64 × 64 pixels.

The classification model uses **RGB imagery only**. Although additional spectral information is available in the image data, NIR is not used as a classifier input.

### 2. Transfer Learning Architecture

The classification pipeline uses an ImageNet-pretrained EfficientNetB0 model.

The main processing steps include:

1. RGB image extraction.
2. Percentile-based normalization using the 2nd and 98th percentiles.
3. Resizing image patches to 128 × 128 pixels.
4. Feature extraction using a frozen EfficientNetB0 backbone.
5. A 64-unit ReLU dense layer, dropout, and a sigmoid output layer.
6. Binary classification into kiln and non-kiln categories.

Class weighting and binary cross-entropy were used during model training.

### 3. Kathmandu Valley External Evaluation

The trained model was evaluated on a geographically external reference dataset from the Kathmandu Valley without Kathmandu-specific fine-tuning.

The evaluation included:

- **108 brick kiln reference locations**, reviewed against current satellite imagery, with questionable coordinate entries corrected or removed.
- **108 non-kiln reference points**, sampled outside 300-meter exclusion zones around retained kiln locations.
- A total of **216 reference points** for external evaluation.

Sentinel-2 Surface Reflectance Harmonized imagery was collected for the period **December 1, 2025, to May 1, 2026**. A median composite and cloud-cover filter below 40% were used.

Satellite patches were sampled at 10-meter scale and resized to 128 × 128 pixels for model inference.

This evaluation was designed to examine cross-regional generalization rather than performance on a random split of the original training dataset.

### 4. Environmental Pattern Analysis

Google Earth Engine was used to calculate vegetation indices from Sentinel-2 imagery.

The analysis used the following bands:

- **B8:** Near-infrared (NIR)
- **B4:** Red
- **B2:** Blue

The vegetation indices examined were:

- **NDVI:** Normalized Difference Vegetation Index
- **EVI:** Enhanced Vegetation Index

The study compared vegetation-index values between kiln and non-kiln reference locations and examined patterns across four distance bands around known kiln locations:

- 0–100 meters
- 100–250 meters
- 250–500 meters
- 500–1,000 meters

The statistical analysis included descriptive statistics, independent-samples t-tests, Cohen's effect sizes, repeated-measures ANOVA across distance bands, and a sensitivity analysis based on the model's predictions.

These analyses investigate spatial associations. They do not establish that brick kilns caused the observed environmental differences.

## Results

### 1. Classification Performance

The model's performance differed between the held-out GEO-Bench test set and the Kathmandu Valley external evaluation.

| Evaluation Dataset | Accuracy | Kiln Precision | Kiln Recall | Kiln F1-Score |
|---|---:|---:|---:|---:|
| GEO-Bench held-out test set (n = 999) | 95% | 91% | 93% | 0.92 |
| Kathmandu external reference set (n = 216) | 69% | 82% | 49% | 0.61 |

The model achieved 95% accuracy on the held-out GEO-Bench test set but 69% accuracy on the Kathmandu external reference set.

The decline highlights the challenge of transferring a model trained in one geographic context to another.

Although kiln precision in Kathmandu was 82%, kiln recall was 49%. This means the model missed many of the reference kiln locations, limiting its suitability as a standalone monitoring system.

#### Kathmandu Valley Confusion Matrix

| Actual Class | Predicted Non-Kiln | Predicted Kiln |
|---|---:|---:|
| Non-kiln | 96 | 12 |
| Kiln | 55 | 53 |

#### Classification by Kiln Type

The observed kiln detection rates by type were approximately:

- **Zig-Zag kilns:** 54% (n = 63)
- **FCBTK kilns:** 40% (n = 42)

Hoffman and VSBK categories were excluded from type-level comparison because their sample sizes were too small, with two and one examples respectively.

### 2. Vegetation-Index Comparison

The reference-point analysis showed lower mean NDVI and EVI values at kiln locations than at non-kiln reference points.

| Reference Group | Mean NDVI | Mean EVI | Sample Size |
|---|---:|---:|---:|
| Non-kiln reference points | 0.517 | 0.320 | 108 |
| Kiln locations | 0.222 | 0.142 | 108 |

The independent-samples comparisons reported statistically significant differences:

- **NDVI:** p = 1.412 × 10⁻³⁷; Cohen's d = 2.14
- **EVI:** p = 1.34 × 10⁻³³; Cohen's d = 1.97

These findings indicate a strong observed association between the sampled kiln locations and lower vegetation-index values.

However, the results do not establish that kiln activity caused the differences. Other environmental and spatial factors may contribute.

### 3. Vegetation Indices by Distance from Kilns

Mean NDVI and EVI values increased across the distance bands examined.

| Distance from Kiln | Mean NDVI | Mean EVI |
|---|---:|---:|
| 0–100 m | 0.222 | 0.142 |
| 100–250 m | 0.269 | 0.174 |
| 250–500 m | 0.301 | 0.195 |
| 500–1,000 m | 0.319 | 0.205 |

Repeated-measures ANOVA reported statistically significant differences across distance bands:

- **NDVI:** F(3, 321) = 134.88, p < .001
- **EVI:** F(3, 321) = 135.42, p < .001

The direction of the pattern was also observed in a sensitivity analysis grouping locations by model prediction.

| Model Prediction | Sample Size | Mean NDVI | Mean EVI |
|---|---:|---:|---:|
| Predicted kiln | 65 | 0.235 | 0.146 |
| Predicted non-kiln | 151 | 0.427 | 0.268 |

This sensitivity analysis supports the presence of a similar spatial association but does not demonstrate causality.

## Key Findings

1. **Cross-regional generalization is challenging.** Strong held-out test performance did not translate directly to the Kathmandu Valley, highlighting the importance of geographic validation.

2. **Kiln recall remains a limitation.** The model detected fewer than half of the reference kiln locations in Kathmandu. The current implementation should therefore be treated as a research prototype rather than a complete kiln-monitoring solution.

3. **Vegetation indices displayed a spatial gradient.** Mean NDVI and EVI increased across the distance bands examined around known kiln locations.

4. **Environmental interpretation requires caution.** The observed relationships are associations, not proof of direct environmental impact. Further analysis is needed to account for spatial dependence and potential confounding factors.

## Limitations

- The Kathmandu external reference dataset is relatively small, containing 108 kiln and 108 non-kiln reference points.
- The classifier was evaluated without Kathmandu-specific fine-tuning.
- The reference sample and selected imagery period do not constitute a complete census of brick kilns or environmental conditions.
- Sampling non-kiln locations outside 300-meter exclusion zones does not guarantee that every reference point is a confirmed non-kiln site or a perfectly matched control.
- Spatial autocorrelation and potential confounding by land cover, built-up areas, terrain, and other environmental variables were not fully modelled.
- NDVI and EVI are indirect indicators of vegetation conditions and cannot independently establish causal environmental impacts.
- Rare kiln categories have too few samples for reliable type-specific conclusions.

## Repository Structure

The repository contains folders intended to organize the research materials:

- `Data/` — Data notes and permitted supporting resources.
- `Documentation/` — Methodology and technical documentation.
- `Notebooks/` — Jupyter notebooks and computational workflows.
- `Paper/` — Research abstract and paper materials.
- `Presentation/` — Conference presentation materials.
- `Results/` — Verified evaluation results, tables, and figures.

The main notebook is currently located in the repository root as `Ecovision_Ai (1).ipynb`. For a cleaner structure, it can be renamed to `EcoVision_AI.ipynb` and moved into the `Notebooks/` folder.

## Tools and Technologies

- Python
- TensorFlow and Keras
- EfficientNetB0 and transfer learning
- Google Earth Engine
- Sentinel-2 satellite imagery
- GEO-Bench `m-brick-kiln` dataset
- NumPy and pandas
- scikit-learn and SciPy
- statsmodels
- geemap
- GeoPandas, Shapely, and Rasterio
- Matplotlib

## Reproducing the Analysis

The workflow was developed in a Google Colab-style environment and uses Google Earth Engine.

To explore or reproduce the analysis:

1. Open the project notebook in Google Colab or a compatible Jupyter environment.
2. Install the Python packages imported by the notebook, including TensorFlow/Keras, the Earth Engine API, geemap, NumPy, pandas, scikit-learn, SciPy, statsmodels, Matplotlib, GeoPandas, Shapely, Rasterio, Pillow, requests, h5py, openpyxl, and tqdm.
3. Authenticate Google Earth Engine and initialize an authorized Google Cloud project. Update the project ID in the notebook if necessary.
4. Obtain the GEO-Bench `m-brick-kiln` dataset and the referenced Kathmandu kiln-location source data, subject to their current access terms.
5. Run the notebook cells in order. Intermediate arrays and analysis outputs are generated in the runtime and must be regenerated in a fresh session.

Reproduction may depend on package versions, Earth Engine permissions, data availability, and runtime configuration. Refer to the notebook for the specific imports, data paths, and parameters used.

## References

- Eil, A., et al. (2020). *Dirty Stacks, High Stakes: An Overview of Brick Sector in South Asia*. World Bank.
- Zhong, M., et al. (2019). *Nepal Ambient Monitoring and Source Testing Experiment (NAMaSTE): Emissions of Particulate Matter and Sulfur Dioxide from Vehicles and Brick Kilns and Their Impacts on Air Quality in the Kathmandu Valley, Nepal*. Atmospheric Chemistry and Physics, 19(12), 8209–8228.
- Lee, J., et al. (2021). *Scalable deep learning to identify brick kilns and aid regulatory capacity*. Proceedings of the National Academy of Sciences, 118(17).
- Lacoste, A., et al. (2023). *GEO-Bench: Toward Foundation Models for Earth Monitoring*. NeurIPS 36.
- Gorelick, N., et al. (2017). *Google Earth Engine: Planetary-scale geospatial analysis for everyone*. Remote Sensing of Environment, 202, 18–27.
- Mondal, R., et al. (2025). *SentinelKilnDB: A Large-Scale Dataset and Benchmark for OBB Brick Kiln Detection in South Asia Using Satellite Imagery*. NeurIPS 38.

## Research Presentation

EcoVision AI was presented at the 3rd International Conference of St. Xavier's College (ICSXC 2026), held on September 24–25, 2026.

The research abstract was published on page 48 of the official ICSXC 2026 Conference Abstract Book.

## License and Data Use

This repository includes an MIT License for applicable project code. Third-party datasets, satellite imagery, publications, and other materials remain subject to their respective licenses, terms, and attribution requirements.

Do not redistribute third-party data unless its terms permit it.
