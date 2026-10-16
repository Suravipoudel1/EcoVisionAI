EcoVision AI
An Integrated Artificial Intelligence Framework for Brick Kiln Classification and Environmental Pattern Analysis Using Multi-Spectral Satellite Imagery
Author: Suravi Poudel
Institution: BSc CSIT, St. Xavier's College, Maitighar, Kathmandu, Nepal
Research venue: 3rd International Conference of St. Xavier's College (ICSXC 2026), September 24–25, 2026
Publication: Abstract published in the official ICSXC 2026 Conference Abstract Book, p. 48
Overview
EcoVision AI is a research prototype exploring how machine learning and freely available Sentinel-2 satellite imagery can support brick kiln classification and environmental pattern analysis in the Kathmandu Valley, Nepal.
The project has two linked components:
1. Cross-regional classification: evaluate whether a brick-kiln classifier trained on the GEO-Bench South Asian brick-kiln dataset (Bangladesh data) transfers to a manually reviewed Kathmandu Valley reference set without Kathmandu-specific fine-tuning.
2. Environmental pattern analysis: compare the Normalized Difference Vegetation Index (NDVI) and Enhanced Vegetation Index (EVI) at known kiln locations, at non-kiln reference points, and across distance bands around kilns.
The study is a proof of concept for satellite-based screening. Its findings show a notable cross-region performance drop and a strong spatial association between kiln locations and lower vegetation-index values. The observational analysis does not establish that kilns caused the observed vegetation differences.
Research questions
- RQ1 — Transferability: How well does a classifier trained on Bangladesh brick-kiln imagery generalize to unseen locations in the Kathmandu Valley?
- RQ2 — Environmental pattern: How do NDVI and EVI differ between kiln locations and non-kiln reference points, and how do these indices vary with distance from known kilns?
Workflow
1. Training data and model
The notebook downloads the m-brick-kiln classification dataset from GEO-Bench, then loads the default train, validation, and test splits.
- Training set: 15,063 image patches, including 5,063 kiln examples
- Validation set: 999 image patches
- Test set: 999 image patches, including 336 kiln examples
- Original image patches: 64 × 64 pixels
- Selected bands during data loading: red, green, blue, and near-infrared (NIR)
- Classifier input: RGB only; NIR is not used by the classification model
The classification model uses an ImageNet-pretrained EfficientNetB0 backbone with its base layers frozen, followed by a 64-unit ReLU dense layer, dropout, and a sigmoid output for binary kiln/non-kiln classification. The notebook uses percentile-based RGB normalization, resizing to 128 × 128 pixels, class weighting, and binary cross-entropy.
2. Kathmandu Valley external evaluation
The notebook uses brick-kiln locations from a published Kathmandu brick-kiln emissions spreadsheet, reviews locations against satellite imagery, corrects or removes questionable coordinate entries, and retains 108 kiln reference locations. It creates 108 non-kiln reference points inside the Kathmandu Valley and outside 300 m exclusion zones around the retained kiln locations.
Sentinel-2 Surface Reflectance Harmonized imagery is sampled for 1 December 2025 to 1 May 2026, using a median composite and a cloud-percentage filter. Each point is represented by a 64 × 64 patch sampled at 10 m scale and resized to 128 × 128 for classification.
The external evaluation uses the trained model without Kathmandu-specific fine-tuning. It is a geographically external evaluation set, rather than a random split from the Bangladesh training data.
3. Environmental indicators
Using Google Earth Engine and Sentinel-2 bands B2 (blue), B4 (red), and B8 (NIR), the notebook calculates NDVI and EVI. It compares indicator values between the 108 kiln locations and 108 non-kiln reference points, then examines vegetation indices in four distance bands around known kilns:
- 0–100 m
- 100–250 m
- 250–500 m
- 500–1,000 m
The notebook uses descriptive statistics, independent-samples t-tests, Cohen's d, repeated-measures ANOVA across distance bands, and a prediction-based sensitivity analysis. These are observational comparisons and should be interpreted as associations, not evidence of causation.
Results
Classification performance
Evaluation set	Accuracy	Kiln precision	Kiln recall	Kiln F1
GEO-Bench held-out test set (n = 999)	95%	91%	93%	0.92
Kathmandu Valley external reference set (n = 216)	69%	82%	49%	0.61


The external evaluation demonstrates a substantial drop in accuracy relative to the held-out test set. In Kathmandu, the model identifies some kiln sites with reasonable precision, but its 49% kiln recall means it misses many of the reference kiln locations. This is an important limitation for any monitoring use case.
By kiln type in the Kathmandu reference set, the observed classification rates were approximately 54% for Zig-Zag kilns (n = 63) and 40% for FCBTK kilns (n = 42). Hoffman and VSBK categories were excluded from type-level comparison because their sample sizes were too small (n = 2 and n = 1, respectively).
Vegetation-index comparison
For the reference points, the notebook reports:
Reference group	Mean NDVI	Mean EVI	n
Non-kiln points	0.517	0.320	108
Kiln locations	0.222	0.142	108


The differences were statistically significant in the notebook's independent-samples tests (NDVI p = 1.412 × 10⁻³⁷; EVI p = 1.34 × 10⁻³³). The reported standardized effect sizes were large (Cohen's d = 2.14 for NDVI; 1.97 for EVI, using the non-kiln group relative to the kiln group).
Distance-band pattern
Mean vegetation-index values increased across the distance bands from the kiln locations:
Distance from kiln	Mean NDVI	Mean EVI
0–100 m	0.222	0.142
100–250 m	0.269	0.174
250–500 m	0.301	0.195
500–1,000 m	0.319	0.205


Repeated-measures ANOVA across the four distance bands reported F(3, 321) = 134.88 for NDVI and F(3, 321) = 135.42 for EVI (both p < .001 in the notebook). A sensitivity analysis grouping locations by the model's predictions showed the same direction of association. This does not show that the kiln locations caused the differences; other land-cover, urban, seasonal, and spatial factors may contribute.
Interpretation
The results support two cautious conclusions:
- A model trained on imagery from one region should not be assumed to generalize reliably to a different geographic context. The Kathmandu results highlight a domain-shift problem and the need for local validation or fine-tuning.
- Kiln locations in this reference sample were associated with lower mean NDVI and EVI, and the indices increased across the distance bands examined. The current design establishes spatial association, not causal environmental impact.
Limitations
- The Kathmandu external reference set is relatively small (108 kiln and 108 non-kiln points).
- The classifier was evaluated zero-shot, with no Kathmandu-specific fine-tuning.
- The external evaluation reflects the selected reference points, Sentinel-2 imagery, dates, and sampling strategy; it should not be treated as a complete census of kilns.
- The 300 m exclusion-zone strategy helps avoid sampling negatives immediately beside known kilns, but does not guarantee that all negative points are confirmed non-kiln sites or perfectly matched controls.
- Spatial autocorrelation and potential confounding by land cover, built-up areas, terrain, and other environmental variables were not fully modelled.
- NDVI and EVI are indirect vegetation indicators and cannot, by themselves, identify a causal effect from brick kilns.
- Rare kiln categories have too few examples for reliable type-specific conclusions.
Repository structure
EcoVisionAI/
├── Data/            # Data notes and permitted supporting data/resources
├── Documentation/   # Methodology, data notes, and technical documentation
├── Notebooks/       # Jupyter notebook(s) for the analysis
├── Paper/           # Abstract and research paper materials
├── Presentation/    # ICSXC 2026 presentation materials
├── Results/         # Verified result tables and generated figures
├── README.md
├── LICENSE
└── .gitignore
Large raw datasets, generated arrays, model checkpoints, and files subject to third-party terms may not be included in the repository. See the data-source notes and the applicable terms before downloading or redistributing them.
Running the notebook
The analysis was developed in a Google Colab-style environment and uses Google Earth Engine. To reproduce the workflow:
1. Open the notebook in Google Colab.
2. Install the packages used in the notebook, including earthengine-api, geemap, tensorflow, numpy, pandas, h5py, scikit-learn, geopandas, shapely, rasterio, scipy, statsmodels, matplotlib, Pillow, requests, openpyxl, and tqdm.
3. Authenticate Earth Engine and initialize it with a Google Cloud project for which your account has access. Update the project ID in the notebook if needed.
4. Download the GEO-Bench m-brick-kiln dataset and the referenced Kathmandu kiln spreadsheet, subject to their current terms and availability.
5. Run the notebook cells in order. The workflow also creates intermediate arrays and analysis outputs under /content, so a fresh runtime will need to regenerate these files.
The notebook makes external data requests and may take time to download the datasets, sample satellite imagery, and compute the statistics. Earth Engine authentication, project access, package versions, external data availability, and minor differences in data services may affect reproducibility. Review the notebook's outputs and the limitations above before using the results.
Data sources and references
- GEO-Bench / m-brick-kiln dataset: Hugging Face dataset. See also Lacoste et al. (2023), GEO-Bench: Toward Foundation Models for Earth Monitoring, NeurIPS 36.
- Sentinel-2 Surface Reflectance Harmonized: accessed through the Google Earth Engine data catalog.
- Kathmandu brick-kiln reference data: spreadsheet titled Kathmandu_brick_kiln_emissions.xlsx, downloaded in the notebook from this source file. The notebook reviews satellite imagery and cleans the reference coordinates before analysis.
- Administrative boundaries: Nepal district GeoJSON from manishacharya60/nepal-geojson.
- Eil, A., et al. (2020). Dirty Stacks, High Stakes: An Overview of Brick Sector in South Asia. World Bank.
- Zhong, M., et al. (2019). Nepal Ambient Monitoring and Source Testing Experiment (NAMaSTE): Emissions of Particulate Matter and Sulfur Dioxide from Vehicles and Brick Kilns and Their Impacts on Air Quality in the Kathmandu Valley, Nepal. Atmospheric Chemistry and Physics, 19(12), 8209–8228.
- Lee, J., et al. (2021). Scalable deep learning to identify brick kilns and aid regulatory capacity. PNAS, 118(17).
- Gorelick, N., et al. (2017). Google Earth Engine: Planetary-scale geospatial analysis for everyone. Remote Sensing of Environment, 202, 18–27.
- Mondal, R., et al. (2025). SentinelKilnDB: A Large-Scale Dataset and Benchmark for OBB Brick Kiln Detection in South Asia Using Satellite Imagery. NeurIPS 38.
Research presentation
This research was presented at the 3rd International Conference of St. Xavier's College (ICSXC 2026), held in Kathmandu, Nepal, on September 24–25, 2026. The abstract appears on page 48 of the official ICSXC 2026 Conference Abstract Book.
Research note: EcoVision AI is a research prototype. Results should be independently validated before use in regulatory decisions, environmental attribution, or operational monitoring.
