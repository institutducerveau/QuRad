# Example Application

This page follows one image from cell detection to feature extraction, visualization in QuPath and export.

## Workflow Overview

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Prepare image  │ -> │  Run QuRad      │ -> │  Visualize      │ -> │  Export &       │
│  with objects   │    │  (menu/script)  │    │  in QuPath      │    │  analyze        │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
```

1. **Prepare**: Load an image with cell detections or annotations in QuPath
2. **Extract**: Run QuRad from the Extensions menu or the script editor to compute 103 features per object (119 with the optional legacy shape class)
3. **Visualize**: Use measurement maps to explore spatial patterns
4. **Export**: Save the CSV for further analysis (classification, clustering, etc.)

---

## Step 1: Prepare Your Image

Start with an image in QuPath that has detections or annotations. These can come from:

- QuPath's built-in cell detection
- StarDist extension
- Cellpose extension
- Imported annotations (GeoJSON)

In this example, we use a mouse glioblastoma H&E image:

![Raw H&E image](assets/raw_he_image.png)

*Mouse glioblastoma H&E image loaded in QuPath.*

After running cell detection (using StarDist, Cellpose, or QuPath's built-in detection):

![Cell segmentations](assets/cell_segmentations.png)

*Cell segmentations overlaid on the H&E image.*

### Importing External Annotations

If you have annotations from external tools:

1. Go to **File → Import objects**
2. Select your GeoJSON file
3. The objects appear in the image as annotations or detections, as defined in the file

## Step 2: Run QuRad

QuRad can be run from the **extension menu** (recommended) or the **script editor**. Both compute the same features (103 by default).

### Option A: Extension menu

1. Go to **Extensions → QuRad → Extract radiomics features…**
2. In the settings dialog, set the bin width, choose which objects to process (detections, annotations, or selected only), select the feature classes to compute, and choose the output (add to the measurement table and/or export a CSV).
3. Click **OK**. A notification reports progress and confirms when extraction is complete.

### Option B: Script editor

1. Open **Automate → Script editor**
2. Load `QuPath_Radiomics_v3.groovy`
3. Configure settings if needed (see [Getting Started: Configuration](getting-started.md#configuration)):

```groovy
def processDetections = true
def exportCSV = true
def addToMeasurements = true
```

4. Click **Run**. The script processes all objects and prints progress to the console:

```
================================================================================
QuPath Radiomics Extraction - QuRad 0.4.0
================================================================================
  ✓ firstorder
  ...
Processing 5000 objects

Processed 5000/5000 (892.3 objects/sec)

================================================================================
Complete
================================================================================
Processed: 5000 objects
Skipped: 0 objects
...
Total radiomics features: 103
```

## Step 3: Visualize in QuPath

### Measurement Maps

Color cells by any radiomics feature:

1. Go to **Measure → Show measurement maps**
2. Select a feature (e.g., `firstorder_Energy`)
3. Cells are colored by feature value

![Measurement map showing cells colored by firstorder_Energy](assets/measurement_map.png)

*Measurement map visualization: cells colored by `firstorder_Energy`. Dark violet indicates cells with lower energy values, yellow indicates cells with higher energy values.*

Measurement maps show spatial patterns, for example regions of high texture complexity, clusters of cells with similar morphology or gradients across a tissue region.

### Histogram View

View the distribution of any feature:

1. Open **Measure → Show measurement maps**
2. The histogram appears below the dropdown
3. Adjust the color scale with min/max sliders

![Histogram of firstorder_Entropy distribution](assets/histogram.png)

*Histogram showing the distribution of `firstorder_Entropy` across all detected cells.*

## Step 4: Export Data

### CSV Export

The CSV file is automatically saved to your project's `radiomics` folder:

```
project/
└── radiomics/
    ├── image_radiomics_20260904_143052.csv
    └── image_radiomics_20260904_143052_settings.json
```

### Export Measurements Table

You can also export via QuPath's built-in export:

1. Go to **Measure → Export measurements**
2. Select output format (CSV, TSV)
3. Choose which measurements to include

## What Next?

The features can be used to classify cells (for example tumor cells versus lymphocytes), to characterize tissue compartments (for example invasive tumor, stroma and healthy glands), to compare tissue quality or staining across slides, or as input to UMAP, clustering and machine-learning models. The two application notebooks in the [GitHub repository](https://github.com/institutducerveau/QuRad) reproduce the cell and tissue classifications of the article, and the [Feature Reference](features.md) defines every feature.
