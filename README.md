# Toronto Lawn & Yard Green Space Map

An interactive web map showing which City of Toronto neighbourhoods have the most private yard green space (lawn, shrubs and tree canopy) on low-density residential lots.

**Live map:** https://serena8886.github.io/cityoftoronto-lawn-map/

The map was built to help target outreach for a fall-leaf and pollinator campaign ("Leave the Leaves for Pollinators") that promotes zero-emission outdoor power equipment (ZEOPE). Its main output is a shortlist of the 15 neighbourhoods with the largest yards, where a mailed native-seed flower card and targeted ads are likely to reach the most homeowners.
---

## What the map shows

- **Neighbourhood colouring:** each of Toronto's 158 neighbourhoods is coloured by one of three measures (switch them in the sidebar):
  - **Yard green space per lot:** median area of grass + shrub + tree canopy on a low-density residential lot. **This measure is used for the ranking.**
  - **Visible lawn per lot:** median area of grass that is *not* under tree canopy
  - **Tree canopy share:** tree canopy as a share of all low-density residential land
- **Top 15 neighbourhoods:** outlined in gold and listed in the sidebar; click a name to zoom in.
- **Lawn, shrub and tree detail:** for the top 15, zooming in loads vector polygons of every grass, shrub and tree patch on residential lots. Each class can be switched on or off, and clicking a patch shows its area.
- **Base maps:** Esri satellite imagery (default) and Esri Light Gray Canvas.
- **Street-level check:** every popup links to Google satellite view and Street View for that spot.

## Method

1. **Residential lots.** Start from all ~500,000 property parcels. Keep regular ("COMMON") parcels whose interior point falls in a low-density residential zone of Zoning By-law 569-2013: RD (detached), RS (semi-detached), RT (townhouse) and R (residential). Drop parcels under 50 m² or over 20,000 m². Result: **377,133 lots in 152 neighbourhoods.**
2. **Land cover per lot.** Overlay the 2018 land cover polygons on every lot and measure the area of each class (tree, grass, shrub, building, etc.).
3. **Neighbourhood summary.** For each neighbourhood, take the **median** yard green space (grass + shrub + tree) per lot. The median is used so that a few very large estates don't skew the result.
4. **Ranking.** Rank neighbourhoods from the largest median yard green space to the smallest. Neighbourhoods with fewer than 30 residential lots (mostly downtown) are not ranked, which leaves 148 ranked neighbourhoods.
5. **Robustness check.** Ranking by the mean instead of the median keeps 12 of the top 15 the same.

**Why tree canopy is included:** the land cover data is a top-down view, so lawn under trees is recorded as tree. In leafy, high-value neighbourhoods (e.g. Bridle Path, St. Andrew-Windfields), most yard space sits under canopy. Ranking by visible lawn alone would push these areas far down the list. Tree canopy is also where fall leaves come from.

## Data sources

All datasets are from the [City of Toronto Open Data Portal](https://open.toronto.ca/).

| Dataset | Used for | Vintage |
|---|---|---|
| [Forest and Land Cover](https://open.toronto.ca/dataset/forest-and-land-cover/) (2018 Tree Canopy Study Geodatabase) | Grass, shrub and tree area on each lot | 2018 |
| [Property Boundaries](https://open.toronto.ca/dataset/property-boundaries/) | Lot outlines | Downloaded October 2026 |
| [Zoning By-law](https://open.toronto.ca/dataset/zoning-by-law/) (Zoning Area layer) | Selecting low-density residential lots | Amendments to June 18, 2023 |
| [Neighbourhoods](https://open.toronto.ca/dataset/neighbourhoods/) (158 neighbourhoods) | Neighbourhood boundaries and summaries | Current 158-neighbourhood structure |

Contains information licensed under the [Open Government Licence – Toronto](https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-licence/).

Base maps: imagery © Esri, Maxar, Earthstar Geographics; Light Gray Canvas © Esri, HERE, Garmin, © OpenStreetMap contributors. Map rendering by [MapLibre GL JS](https://maplibre.org/).

## Limitations

- Land cover is from **2018**; some yards will have changed since.
- The data is a top-down view: **lawn under tree canopy is counted as tree**.
- The 2018 documentation notes some confusion between tree and grass/shrub in the automated classification.
- Only lots covered by Zoning By-law 569-2013 are included. A few areas still under former municipal by-laws (e.g. parts of the waterfront) are under-counted.
- Some lots that contain wooded ravine or bluff slopes (e.g. Scarborough Village) may rank higher than their usable yard space would suggest. Check top-ranked areas against the satellite imagery.
- The map shows where yard green space is, not who maintains it. In areas where landscaping companies do the yard work, messaging may need to reach them as well.

## Repository structure

```
index.html                    The web map (HTML, CSS and JavaScript in one file)
data/
├── neighbourhoods.geojson    158 neighbourhood boundaries with all measures and ranks
├── summary.json              Ranking method and summary figures shown in the sidebar
├── pilots.json               List of neighbourhoods that have detail data (top 15)
└── pilots/<id>.json          Grass / shrub / tree polygons for one neighbourhood,
                              loaded only when the user zooms into it
```

The data files were produced with a Python pipeline (geopandas, pyogrio, shapely) that is not part of this repository. To keep the files small, the detail polygons are clipped to residential lots, dissolved by class, simplified to 1 m, and patches under 4 m² are removed. Total area differs from the source data by less than 1%.

## Author
Serena Xie, University of Toronto, prepared during an ENV440H1 experiential placement. 
