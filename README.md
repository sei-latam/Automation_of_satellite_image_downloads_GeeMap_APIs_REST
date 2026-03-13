# Automation of Satellite Image Downloads with GEE, geemap, and APIs

This repository contains notebook-based workflows for searching, visualizing, downloading, and packaging satellite and climate raster data for a selected area of interest (AOI). The current notebooks focus on Sentinel-1 and CHIRPS datasets using two different access patterns:

- Google Earth Engine with geemap
- Alaska Satellite Facility (ASF) Search API

The project is organized as a collection of Google Colab-oriented notebooks rather than a Python package. Most workflows install dependencies inside the notebook, authenticate interactively, export GeoTIFF files to the Colab workspace, and then package the results as ZIP files for download.

## Main Workflows

| Notebook | Dataset / source | What it does |
| --- | --- | --- |
| `1. Sentinel/Sentinel1_ASF_API.ipynb` | Sentinel-1 via ASF Search API | Authenticates with NASA Earthdata, uploads an AOI shapefile, searches Sentinel-1 GRD scenes, downloads data from ASF, converts `.tiff` outputs into GeoTIFF, applies a Lee speckle filter, preserves CRS, and creates a ZIP archive. |
| `1. Sentinel/Sentinel1_GEE.ipynb` | `COPERNICUS/S1_GRD` in Google Earth Engine | Authenticates with Earth Engine, visualizes the AOI with geemap, filters Sentinel-1 imagery, applies a Gamma MAP speckle filter, clips scenes to the AOI, exports VV and VH GeoTIFF bands, and compresses the outputs for download. |
| `2. CHIRPS/Automate_Download_Precipitation_CHIRPS.ipynb` | `UCSB-CHC/CHIRPS/V3/PENTAD` in Google Earth Engine | Draws or loads an AOI, filters CHIRPS precipitation imagery, previews recent scenes, exports precipitation rasters to GeoTIFF, and packages them as a ZIP file. |

## Repository Structure

```text
.
|-- README.md
|-- 1. Sentinel/
|   |-- Sentinel1_ASF_API.ipynb
|   `-- Sentinel1_GEE.ipynb
|-- 2. CHIRPS/
|   `-- Automate_Download_Precipitation_CHIRPS.ipynb
|-- 3. Modis/
|   `-- Modis.ipynb
`-- 4. GIS Data/
    `-- CobijaAOI.zip
```

## AOI Data Included

The `4. GIS Data/CobijaAOI.zip` archive contains example GIS inputs for the Cobija area of interest, including:

- ESRI Shapefile components (`.shp`, `.shx`, `.dbf`, `.prj`, and related sidecar files)
- `Cobija.geojson`
- `Cobija.json`

This archive is useful when the notebooks ask for an uploaded AOI file.

## Environment and Prerequisites

These notebooks are designed primarily for Google Colab. Several cells use Colab-specific features such as:

- `!pip install ...`
- `!apt-get install ...`
- `from google.colab import files`
- `files.upload()` and `files.download()`

Before running the workflows, make sure you have:

- A Google account with access to Google Earth Engine for the GEE notebooks
- A NASA Earthdata account for the ASF notebook
- Internet access to install notebook dependencies during execution
- An AOI file ready to upload, or access to the Earth Engine asset referenced by the notebook

## Python and System Dependencies Used

The notebooks install or import the following libraries across the workflows:

- `geemap`
- `earthengine-api`
- `asf_search`
- `geopandas`
- `shapely`
- `rasterio`
- `rioxarray`
- `ipywidgets`
- `requests`
- `numpy`
- `scipy`
- `matplotlib`
- GDAL system packages in the ASF workflow

## How to Use

1. Open the notebook you want to run in Google Colab or a compatible Jupyter environment.
2. Run the installation cells first so the required packages are available.
3. Authenticate when prompted:
   - Use Google Earth Engine authentication for the GEE notebooks.
   - Use NASA Earthdata credentials for the ASF notebook.
4. Provide the AOI:
   - Upload the Cobija shapefile or your own shapefile in the ASF notebook.
   - Draw an AOI or use the default polygon in the CHIRPS notebook.
   - Ensure the referenced Earth Engine asset exists for the Sentinel-1 GEE notebook.
5. Update date ranges, orbit filters, export settings, and output names as needed.
6. Run the export/download cells to generate GeoTIFF outputs and ZIP packages.

## Workflow Notes

### Sentinel-1 via ASF API

The ASF workflow:

- Requests Earthdata credentials interactively
- Reads an uploaded shapefile and converts it to WKT geometry
- Searches Sentinel-1 GRD scenes using filters such as platform, beam mode, polarization, relative orbit, and date range
- Downloads scenes into `ASF_Sentinel1`
- Extracts and converts relevant `.tiff` files into `ASF_Sentinel1/GeoTIFFs`
- Applies a Lee speckle filter and writes filtered copies with `_lee.tif` suffixes
- Compresses processed outputs into `ASF_Sentinel1/Sentinel1_GeoTIFFs.zip`

### Sentinel-1 via Google Earth Engine

The GEE Sentinel-1 workflow:

- Uses `COPERNICUS/S1_GRD`
- Filters scenes by AOI, date, VV/VH polarizations, `IW` mode, `GRD` product type, `Sentinel-1A`, descending pass, and relative orbit number
- Applies a Gamma MAP filter and clips imagery to the AOI
- Visualizes the latest images with geemap
- Exports VV and VH bands into `Sentinel1_Exports`
- Creates `Sentinel1_Exports_ZIP.zip` for download

Important assumption:

- The notebook references the Earth Engine asset `projects/gee-projects-481514/assets/CobijaSHP` for visualization. If you do not have access to that asset, replace it with your own asset path.

### CHIRPS via Google Earth Engine

The CHIRPS workflow:

- Uses the `UCSB-CHC/CHIRPS/V3/PENTAD` image collection
- Allows a user-drawn AOI, with a hard-coded default polygon as fallback
- Clips each image to the AOI
- Visualizes recent precipitation scenes
- Exports precipitation rasters into `CHIRPS_Exports`
- Creates `CHIRPS_Exports_ZIP.zip` for download

## Output Summary

Expected output folders and archives created by the notebooks:

- `ASF_Sentinel1/`
- `ASF_Sentinel1/GeoTIFFs/`
- `ASF_Sentinel1/Sentinel1_GeoTIFFs.zip`
- `Sentinel1_Exports/`
- `Sentinel1_Exports_ZIP.zip`
- `CHIRPS_Exports/`
- `CHIRPS_Exports_ZIP.zip`
