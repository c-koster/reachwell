# Reachwell Data Mapping
Hello!

This repo contains code, visualizations, and modeling results for a project completed for [Reachwell](https://www.reachwellapp.com/), a non-profit app designed 
to connect families to schools and public entities through multilingual chat and annoucements. The project objective was to identify US counties and school districts 
with a high need for Reachwell based on poverty, population, and multi-lingual indicators, and visualize these communities on a map.

### Getting the Data 💾 

Data for this project was collected from the following census sources:
1. [US census data explorer](https://data.census.gov/)
2. [US geography files](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-geodatabase-file.2022.html#list-tab-1258746043)

A note on the census indicators (1 above) from the ACS Census data [documentation](https://www.census.gov/data/developers/data-sets/ACS-supplemental-data.html):

>The American Community Survey (ACS) is an ongoing survey that provides data every year... The supplemental estimates consist of high-level detailed tables tabulated on the 1-year microdata for geographies with populations of 20,000 or more. The intention of this product is to allow people with smaller populations key estimates that are more current than the 5-Year file.

This meeat that updated data was not available for many school districts and counties. To address this issue, I combined data from both files: I started with the 5-Year file from 2021 and added more current indicators (from 2021 and 2022) when they were available for that geographic region:

![census-download-img](images/download-selection.png)

All census files are avalable in compressed format inside the [data](./data) folder.

### Transforming the Data ⚙️

After downloading the census files, I exctracted a list of indicator variables. I based this list on conversations with Jesse, and from [this](https://nces.ed.gov/Programs/Edge/ACSDashboard/0600014) NCES dashboard of educational indicators. Code for extracting the data from census files (and updating lagged 5-year data with more recent ACS supplements) can be found in [this](notebooks/prepare.ipynb) notebook.

- population number (5 and older)
- total household number
- percent of limited english households
- percent of population speaking english only and (percent of population speaking aother language than english) 
- percent of population below poverty level
- percent of households with children
- percent of households recieving food stamps 
- percent of households with children that are a married family
- percent of kids in school that are enrolled in a private school 
- percent of kids in school that are enrolled in a public school
- median income of households with children (note: for a small number of high and low median incomes this variable will map to `250,000+` and `2,500-`. I set these values to be 250000 and 2500 respectively)

### Modeling 🎰

After extracting the data and the reachwell client list (see [here](notebooks/extract_client_list.ipynb)), I set up a classification problem to 
predict whether a county or school district might have a high need for Reachwell. Specifiicaly, counties and school districts that used either reachwell
or a competitor were tagged with `label=1`, and all other counties and school districts were tagged with `label=0`.

features? <>

I also held on to 20% of the positive examples to evaluate the model's performance on some unseen data. Below is a table the performance metrics. The first four are classification 
metrics that evaluate performance directly. The last two (`Average Precision` and `Precision@K`) are ranking/recommendation metrics, which evaluate the quality of the resulting ordered list.


| Model Name                       | Balanced Acc | Precision | Recall | F1 Score | Average Precision | Precision@500 |
|----------------------------------|--------------|-----------|--------|----------|-------------------|---------------|
| Baseline (always predicts 1)     | 0.500        | 0.006     | 1.000  | 0.013    | 0.007             | 0.006         |
| Logistic Regression with L2 loss | 0.760        | 0.029     | 0.667  | 0.056    | 0.037             | 0.030         |
| k-Nearest Neighors               | 0.676        | 0.015     | 0.611  | 0.030    | 0.019             | 0.016         |
| SVM with 2d Polynomial Kernel    | 0.767        | 0.032     | 0.667  | 0.061    | 0.091             | 0.028         |

The SVM (Support Vector Machine) performed the best (specifically on `Average Precision`), so I used it as my final model for generating predictions for each 
school district and county. Pedictions can be found in the choropleth map (see visualizing the data), and are used to sort the rows of the 
outputted [excel file](outputs/indicators_predictions.xlsx).

### Visualizing the Data

One of the first issues I had with . a full school district geojson is about 500MB uncompressed. also, the 

. i see no impprovement in the insights i get from the two charts.


<> side by side charts here



### References 📚
(some resources that I found useful while working on this project)
- https://web.stanford.edu/class/cs276/handouts/EvaluationNew-handout-6-per.pdf
- https://deck.gl/docs/get-started/using-standalone
- https://deck.gl/docs/api-reference/carto/basemap
- https://plotly.com/python/mapbox-county-choropleth/
- https://github.com/vega/vega-lite/issues/7365 