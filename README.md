# ATLAS - Analysis Tool for Land-Cover Accuracy and Statistics

ATLAS is a Python-based land-cover data analysis tool I developed for my NASA STEM Enhancement in Earth Science (SEES) Earth System Explorers project. It compares ground-truth land-cover observations against classifications produced using ESA WorldCover data.

The main purpose of ATLAS is to make classification error easier to understand. Overall accuracy alone does not always tell the full story, especially when some land-cover classes appear far more often than others. As such, ATLAS analyzes performance both across the complete dataset and within each Primary Sampling Unit (PSU).

## Sample Data and Displayed Outputs

The included `allData.csv` is the sample dataset used to demonstrate ATLAS. All tables, metrics, charts, and other saved outputs currently visible in `ATLAS.ipynb` and `ATLAS Visualization.ipynb` on GitHub are example results generated from this sample dataset. They are not fixed results that ATLAS produces for every dataset.

If you replace `allData.csv` with your own properly formatted data and rerun the notebooks, all displayed and exported results will update based on your dataset. This only changes the files and notebook outputs in your own environment; it does not modify the sample files or saved outputs shown in this GitHub repository.

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
* Point-level agreement between ground-truth and model classifications
* Classification accuracy for every PSU

`ATLAS.ipynb` exports each part of the statistical analysis into separate CSV files. `ATLAS Visualization.ipynb` displays the classification and agreement results graphically.

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

For the statistical analysis in `ATLAS.ipynb`, the observations only need to remain grouped into their correct PSUs. For the categorical grids in `ATLAS Visualization.ipynb`, the 100 observations within each PSU must also be ordered row by row as a 10-by-10 grid. The notebook assumes that PSUs 1-6 form the first PSU row, PSUs 7-12 form the second, and so on through PSUs 31-36.

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

To generate the optional figures, also download `ATLAS Visualization.ipynb`, place it in the same directory as `allData.csv`, and run every cell from top to bottom. The visualization notebook reads the input CSV directly, so it does not need the exported result files from the main notebook.

If you are using Google Colab, upload the notebook you want to run and `allData.csv` to the Colab session before running it. To run both notebooks, upload all three files.

ATLAS only reads the input CSV; it does not modify it. Running the notebooks with new data will replace the displayed sample outputs locally. Running `ATLAS.ipynb` will also overwrite files with the same names in the local `results` folder. It will not change anything on this GitHub repository.

## Dependencies

ATLAS requires Python 3 and the following packages:

```text
pandas
numpy
scikit-learn >= 1.3
matplotlib
```

You can install them using:

```bash
pip install pandas numpy "scikit-learn>=1.3" matplotlib
```

These packages are normally already available in Google Colab.

## Output Files

Running the final cell of `ATLAS.ipynb` creates a `results` folder containing:

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

`ATLAS Visualization.ipynb` currently displays its figures within the notebook rather than exporting additional files.

## Visualizations

`ATLAS Visualization.ipynb` produces:

* A 60-by-60 ground-truth classification grid
* A 60-by-60 model-prediction classification grid
* A 60-by-60 point-level classification agreement grid
* A 6-by-6 PSU classification-accuracy chart

Each cell in the classification grids represents one sampled observation. The figures use discrete colors and do not interpolate values between points. The cells are placed next to one another to make classification patterns easier to compare, but they do not represent continuous land-cover coverage or the physical distance between sample locations.

The placement and orientation of the cells are based entirely on CSV row order. These figures should therefore be interpreted as categorical sampling grids rather than geographically scaled maps.

The PSU accuracy chart compares the ground-truth and model label at every point. Within each PSU, a correct match is treated as 1 and an incorrect match as 0. The mean of those 100 values is multiplied by 100 to calculate percentage accuracy.

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
* A row-by-row 10-by-10 point order within each PSU for the visualization notebook
* The eight classes listed above
* A 10% minimum threshold for secondary PSU classifications

If two classes are tied for the largest percentage of a PSU, ATLAS currently selects the primary class based on the existing column order.

The hierarchy percent error compares how frequently each class appears as primary or secondary across the complete AOI. It does not determine whether those classifications occurred in the correct individual PSUs.

The visualization grids are not georeferenced, do not preserve the physical spacing between points or PSUs, and should not be interpreted as continuous maps.

## Future Work

I plan to expand ATLAS with:

* Normalized confusion-matrix visualizations
* Optional figure exporting
* Coordinate-aware spatial visualizations
* Support for different PSU and AOI dimensions
* Comparisons between multiple land-cover models

ATLAS v1 establishes the main evaluation workflow and its first set of categorical visualizations. Future versions will focus on making the analysis more spatial, flexible, and applicable to datasets beyond the original AOI.
