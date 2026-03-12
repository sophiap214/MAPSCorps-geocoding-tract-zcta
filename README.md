# MAPSCorps Geocoding Project
Assigning 2020 Census tracts and ZCTAs to MAPSCorps locations using US Census geocoding tools and shapefiles, enabling tract- and ZIP-level aggregation.

## Overview

This repository contains a geospatial workflow for converting MAPSCorps point-level business data (latitude/longitude) into **2020 Census tracts** and **ZIP Code Tabulation Areas (ZCTAs)**, and for producing aggregated counts of active businesses by PlaceType.

The primary use case is to support analyses where exact latitude/longitude cannot be shared, and outputs must be reported at the census tract or ZIP level.

---

## Repository Structure

```
.
├── Converting Coords to Tract + Zip.ipynb    # Main Jupyter notebook containing all code
├── Chicago_Data_2019 - Chicago_Data_2019.csv # Original MAPSCorps dataset (input)
├── MAPSCorps_*.csv                           # Final output files (tract- or ZIP-level)
├── split_file_*.csv                          # Split_files (explained more in the ipynb)
└── README.md
```

### Key Files

* **`Converting Coords to Tract + Zip.ipynb`**
  This Jupyter notebook contains *all* data cleaning, spatial joins, and aggregation logic. There is no standalone script — this notebook is the full pipeline.

* **`Chicago_Data_2019 - Chicago_Data_2019.csv`**
  The original MAPSCorps dataset provided for 2019. This file includes latitude/longitude but does not include census tract or ZIP code identifiers.

* **`MAPSCorps_*.csv`**
  These CSV files are the final outputs of the pipeline. They contain aggregated counts of active businesses by PlaceType, reported at either the census tract or ZIP (ZCTA) level, depending on site constraints.

---

## Data Sources

### Census Shapefiles (Required)

This project relies on **2020 Census TIGER/Line shapefiles** for spatial joins.

You must manually download the following shapefiles from the U.S. Census Bureau:

* **2020 ZIP Code Tabulation Areas (ZCTAs)**
  [https://www.census.gov/programs-surveys/geography/guidance/geo-areas/zctas.html](https://www.census.gov/programs-surveys/geography/guidance/geo-areas/zctas.html)

After downloading, unzip the shapefiles in a local directory (e.g., `data/`) and update the file paths in the notebook as needed.

---

## Methodology

1. **Input MAPSCorps data** containing latitude and longitude
2. Clean the original chi_data csv and imput null values for lat and long
3. Use the US census geocoder --> accepts 10,000 records at a time so split the data
4. Apply to each observation in the csv to get tract codes for each long/lat coordinate
5. Concatenate the split csv files to get same size of the original data
6. Load ZCTA shapefiles
7. Convert MAPSCorps points to spatial objects
8. **Spatially join points to census geographies**

   * Assign 2020 Census tract GEOIDs
   * Optionally assign ZCTAs
9. **Filter to active businesses**
10. **Aggregate counts by PlaceType**

   * There are 17 total PlaceType categories
Export tract- or ZIP-level summary tables

---

## Outputs

Depending on data availability at each site, outputs follow one of two formats:

### Tract-Based Output

For sites with census tract data available:

```
census_tract | PlaceType | count_of_Active_PlaceStatus
```

### ZIP-Based Output

For sites without tract data (ZIP/ZCTA only):

```
ZCTA_Zipcode | PlaceType | count_of_Active_PlaceStatus
```

Each MAPSCorps CSV output corresponds to a specific year and geography level.

---

## Notes & Limitations

* A single census tract may intersect multiple ZCTAs, which can introduce ambiguity when converting between tracts and ZIP codes.
* ZCTAs are approximations of ZIP codes, not exact USPS delivery areas.
* This pipeline assumes geocoding is aligned to 2020 Census geography, consistent with most CAP site practices.
