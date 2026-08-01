# ATLAS

ATLAS is a Python-based land-cover data analysis tool I developed for my NASA STEM Enhancement in Earth Science (SEES) Earth System Explorers project. It compares ground-truth land-cover observations against classifications produced using ESA WorldCover data.

The main purpose of ATLAS is to make classification error easier to understand. Overall accuracy alone does not always tell the full story, especially when some land-cover classes appear far more often than others. As such, ATLAS analyzes performance both across the complete dataset and within each Primary Sampling Unit (PSU).

The outputs currently shown in `ATLAS.ipynb` were generated using the sample `allData.csv` provided in this repository. If you replace that file with your own properly formatted data and rerun the notebook, all displayed and exported results will update based on your dataset.

## What ATLAS Does

ATLAS currently calculates:

* Total ground-truth and model instances for each class
* A full confusion matrix
* Overall accuracy
* User's Accuracy, or precision
* Producer's Accuracy, or recall
* F1 score
* Ground-truth counts for each class
* Ground-truth and model class percentages for every PSU
* Primary and secondary land-cover classes for every PSU
* Percent error between ground-truth and model hierarchy counts

The notebook then exports each part of the analysis into separate CSV files.

## Data Formatting

ATLAS v1 was built around the structure of my original SEES dataset. The Area of Interest contains 36 PSUs, with 100 observations in each PSU. This produces 3,600 total data points.

Your CSV must contain exactly 3,600 data rows and the following two columns:

```text
crosswalked label (student data)
Model label (worldcover)
```

Other columns are allowed, but ATLAS will ignore them.

The observations must also be ordered by PSU:

```text
Rows 1-100       -> PSU 1
Rows 101-200     -> PSU 2
Rows 201-300     -> PSU 3
...
Rows 3501-3600   -> PSU 36
```

ATLAS currently assumes that the data already follows this order. It does not use a separate PSU-number column.

## Accepted Classes

Class names are case-sensitive and must exactly match one of the following:

```text
Tree cover
Grassland
Shrubland
Bare/sparse vegetation
Permanent water bodies
Built-up
Cropland
Herbaceous Wetland
```

Before beginning the analysis, ATLAS checks the total number of rows and every class label in the two required columns. If the file contains an unexpected class, missing value, capitalization difference, or incorrect number of observations, the notebook will raise an error instead of continuing with incomplete data. If your classifications utilize a different labeling system, crosswalk your current labels to the ones used by ATLAS.

## How to Run ATLAS

1. Download `ATLAS.ipynb`.
2. Format your CSV using the requirements above.
3. Rename the CSV exactly `allData.csv`.
4. Place `allData.csv` in the same directory as `ATLAS.ipynb`.
5. Open the notebook in Jupyter Notebook, JupyterLab, or Google Colab.
6. Run every cell from top to bottom.

If you are using Google Colab, upload both `ATLAS.ipynb` and `allData.csv` to the Colab session before running the notebook.

ATLAS only reads the input CSV; it does not modify it. Running the notebook with new data will replace the displayed sample outputs locally and overwrite files with the same names in the local `results` folder. It will not change anything on this GitHub repository.

## Dependencies

ATLAS requires Python 3 and the following packages:

```text
pandas
numpy
scikit-learn >= 1.3
```

You can install them using:

```bash
pip install pandas numpy "scikit-learn>=1.3"
```

These packages are normally already available in Google Colab.

## Output Files

Running the final cell creates a `results` folder containing:

| File                              | Output                                                                 |
| --------------------------------- | ---------------------------------------------------------------------- |
| `all_SSUs_Classes.csv`            | Total ground-truth and model counts for every class                    |
| `confusion_matrix.csv`            | Complete confusion matrix                                              |
| `confusion_matrix_metrics.csv`    | User's Accuracy, Producer's Accuracy, F1 score, and ground-truth count |
| `ground_PSU_breakdown.csv`        | Ground-truth class percentages for every PSU                           |
| `model_PSU_breakdown.csv`         | Model class percentages for every PSU                                  |
| `ground_data_class_hierarchy.csv` | Ground-truth primary and secondary classes                             |
| `model_data_class_hierarchy.csv`  | Model primary and secondary classes                                    |
| `hierarchy_analysis.csv`          | Comparison of ground-truth and model hierarchy counts                  |

Because each PSU contains exactly 100 observations, the class counts within each PSU are numerically equal to percentages.

## Understanding the Metrics

**User's Accuracy** measures how often a model prediction for a given class is correct. A low User's Accuracy means the model frequently assigns that class to points that belong to something else.

**Producer's Accuracy** measures how many ground-truth observations from a class the model successfully identifies. A low Producer's Accuracy means the model misses much of that class.

**F1 Score** balances User's and Producer's Accuracy. This is useful when the class distribution is uneven.

**Ground-Truth Count** shows how many ground observations were used to evaluate each class. This provides context because a score based on thousands of observations should not be treated exactly the same as one based on only a few.

A value of `NaN` means that the metric is mathematically undefined, generally because that class has no ground-truth observations, model predictions, or both.

## Current Limitations

ATLAS v1 is designed specifically around the format of the original dataset. It currently assumes:

* 36 PSUs
* 100 observations per PSU
* 3,600 total observations
* Consecutive PSU ordering
* The eight classes listed above
* A 10% minimum threshold for secondary PSU classifications

If two classes are tied for the largest percentage of a PSU, ATLAS currently selects the primary class based on the existing column order.

The hierarchy percent error compares how frequently each class appears as primary or secondary across the complete AOI. It does not determine whether those classifications occurred in the correct individual PSUs.

## Future Work

I plan to expand ATLAS with:

* Spatial ground-truth and model classification maps
* A 6-by-6 PSU accuracy heatmap
* Normalized confusion-matrix visualizations
* Direct PSU-by-PSU agreement metrics
* Support for different PSU and AOI dimensions
* Comparisons between multiple land-cover models

ATLAS v1 establishes the main evaluation workflow. Future versions will focus on making the analysis more spatial, flexible, and applicable to datasets beyond the original AOI.
