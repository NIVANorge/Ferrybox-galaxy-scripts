# Ferrybox R scripts

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/NIVANorge/Ferrybox-galaxy-scripts/main)


The following scripts extract ferrybox measurements (https://thredds.niva.no/thredds/dodsC/datasets/nrt/color_fantasy.nc) and river logger measurements (https://thredds.niva.no/thredds/dodsC/datasets/[...]

All available scripts are located in the "all_functions" folder.
**First** run the script **"netcdf_extract_fb_data.R"** to exctract ferrybox measurements. Available paramaters are [salinity, chlorophyll, turbidity, fdom, temperature, oxygen_sat]. The ferrybox meas[...]

**Second** run the script **"netcdf_logger_extract.R"** to extract river logger data from Baterod river located inside Glomma in the south-eastern part of Oslofjorden. Available parameters are [temp_w[...]

From here you can run the **"netcdf_assessment_area.R"**, where you can provide an assessment waterbody area that you want to work with. Example from https://karteksport.miljodirektoratet.no/ you can [...]

The **"netcdf_join_dataframes.R"** joins the ferrybox and logger csv data frame (or other if provided) by a specific parameter from the joined dataframes and calculates daily mean values. 

**"netcdf_scatter_datax_vs_datay.R"** creates a scatterplot from the joined dataframes from the parameters specfied and **"netcdf_scatter_station_plot.R"** creates scatterplot from a single dataframes[...]
**"netcdf_tile_plot.R"** creates a Hovmöller style plot of ferrybox measurements. The plot illustrates the latitude and y-axis, data at x-axis and plot fill is the measurement value of specified para[...]

# GALAXY
The workflow is published on the Galaxy platform and a data-to-knowledge package explaining how the workflow runs is available at https://zenodo.org/records/22868890.

## Docker

The environment for the R scripts can also be created using docker

```bash
today=$(date '+%Y%m%d')
docker build . -t ferry-rscripts:${today}

# Better:
# This includes the git commit hash, so please
# make sure all your changes are committed/stashed:
githash=$(git rev-parse --short HEAD)
docker build \
  --build-arg GIT_COMMIT=${githash} \
  -t ferry-rscripts:${today}-${githash} .
```

To run an interactive session to execute several scripts:

```bash
docker run -it --entrypoint /bin/bash ferry-rscripts${today}
```

To run a single script, with input parameters:

(When removing the trailing comments, make sure to remove all trailing whitespace, so that the backslash is the last character on the line. Otherwise subsequent lines will not be passed on to the dock[...]

```bash
# Example: netcdf_extract_fb_data.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_extract_fb_data.R' \
  ferry-rscripts:${today} \
  'https://thredds.niva.no/thredds/dodsC/datasets/nrt/color_fantasy.nc' \
  '/out/myferryboxtest.csv' \
  'temperature,salinity,chlorophyll,turbidity' \
  '2023-01-01' \
  '2023-12-31' \
  'null' 'null' 'null' 'null'
```

For example commands for all contained scripts, and an explanation of their input
parameters, please see below.


### netcdf_extract_fb_data.R

```bash
# netcdf_extract_fb_data.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_extract_fb_data.R' \
  ferry-rscripts:${today} \
  'https://thredds.niva.no/thredds/dodsC/datasets/nrt/color_fantasy.nc' \
  '/out/myferryboxtest.csv' \
  'temperature,salinity,chlorophyll,turbidity' \
  '2023-01-01' \
  '2023-12-31' \
  'null' 'null' 'null' 'null'

# Explanation of the parameters:
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_extract_fb_data.R' \
  ferry-rscripts:${today} \

  # Thredds link to FerryBox data
  'https://thredds.niva.no/thredds/dodsC/datasets/nrt/color_fantasy.nc' \

  # Output CSV (if only a directory is given, defaults to ferrybox.csv)
  '/out/myferryboxtest.csv' \

  # Parameters (NULL = ALL)
  'temperature,salinity,chlorophyll,turbidity' \

  # Start date
  '2023-01-01' \

  # End date
  '2023-12-31' \

  # Bounding box (minLon maxLon minLat maxLat) or 'null' 'null' 'null' 'null'
  'null' 'null' 'null' 'null'

```


### netcdf_logger_extract.R

```bash
# netcdf_logger_extract.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_logger_extract.R' \
  ferry-rscripts:${today} \
  'https://thredds.niva.no/thredds/dodsC/datasets/loggers/glomma/baterod.nc' \
  '/out/data/myloggertest.csv' \
  'NULL' \
  '2023-01-01' \
  '2023-12-31'

# Explanation of the parameters:
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_logger_extract.R' \
  ferry-rscripts:${today} \

  #
  'https://thredds.niva.no/thredds/dodsC/datasets/loggers/glomma/baterod.nc' \

  # Output CSV (if only a directory is given, defaults to logger.csv)
  '/out/data/myloggertest.csv' \

  # Parameters (NULL = ALL)
  'NULL' \

  # Start date
  '2023-01-01' \

  # End date
  '2023-12-31'

```

### netcdf_assessment_area.R

```bash
# netcdf_assessment_area.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_assessment_area.R' \
  ferry-rscripts:${today} \
  '/out/data/myferryboxtest.csv' \
  '/out/plots/mypositionplottest.png' \
  '/out/data/myloggertest.csv' \
  'NULL'

# Explanation of the parameters:
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_assessment_area.R' \
  ferry-rscripts:${today} \

  # Input FerryBox CSV
  '/out/data/myferryboxtest.csv' \

  # Output plot (if only a directory is given, defaults to assessment_area.png)
  '/out/plots/mypositionplottest.png' \

  # Input river/logger CSV
  '/out/data/myloggertest.csv' \

  # Waterbodies shapefile (NULL if none)
  'NULL'

```

### netcdf_scatter_station_plot.R

```bash
# netcdf_scatter_station_plot.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_scatter_station_plot.R' \
  ferry-rscripts:${today} \
  '/out/data/myferryboxtest.csv' \
  '/out/plots/myscatterplottest.png' \
  'chlorophyll' \
  'salinity'

# Explanation of the parameters:
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_scatter_station_plot.R' \
  ferry-rscripts:${today} \

  # Input FerryBox CSV
  '/out/data/myferryboxtest.csv' \

  # Output plot (if only a directory is given, defaults to ferrybox_scatter.png)
  '/out/plots/myscatterplottest.png' \

  # Parameter X
  'chlorophyll' \

  # Parameter Y
  'salinity'

```

### netcdf_join_dataframes.R

```bash
# netcdf_join_dataframes.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_join_dataframes.R' \
  ferry-rscripts:${today} \
  '/out/data/myferryboxtest.csv' \
  '/out/data/myloggertest.csv' \
  'turbidity' \
  'turbidity_avg' \
  'station_name' \
  'Baterod' \
  'datetime' \
  'datetime' \
  '/out/data/myjoinedtest.csv'

# Explanation of the parameters:
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_join_dataframes.R' \
  ferry-rscripts:${today} \

  # FerryBox dataframe CSV
  '/out/data/myferryboxtest.csv' \

  # River/logger dataframe CSV
  '/out/data/myloggertest.csv' \

  # Parameter from first dataframe
  'turbidity' \

  # Parameter from second dataframe
  'turbidity_avg' \

  # Station column name in second dataframe
  'station_name' \

  # Station ID/name to filter in second dataframe
  'Baterod' \

  # Time column in first dataframe
  'datetime' \

  # Time column in second dataframe
  'datetime' \

  # Output joined CSV (if only a directory is given, defaults to joined.csv)
  '/out/data/myjoinedtest.csv'

```

### netcdf_scatter_datax_vs_datay.R
```bash
# Case 1: Filter by latitude range (simplest test)
docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_scatter_datax_vs_datay.R' \
  ferry-rscripts:local \
  'https://aquainfra.ogc.igb-berlin.de/exampledata/niva/netcdf_join_dataframes/joined.csv' \
  '/out/scatter_lat.png' \
  'null' \
  'null' \
  'null' \
  '59.1' \
  '59.3' \
  'null'

# Case 2: Filter by waterbody
docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_scatter_datax_vs_datay.R' \
  ferry-rscripts:local \
  'https://aquainfra.ogc.igb-berlin.de/exampledata/niva/netcdf_join_dataframes/joined.csv' \
  '/out/scatter_waterbody.png' \
  'https://github.com/NIVANorge/niva-aquainfra/raw/refs/heads/main/Ferrybox%20scripts/test_data/Vannforekomster_202604091250.zip' \
  'Torbjørnskjær,Færder' \
  'navn' \
  'null' \
  'null' \
  'VannforekomstKyst'

```

### netcdf_tile_plot.R

```bash
# netcdf_tile_plot.R
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_tile_plot.R' \
  ferry-rscripts:${today} \
  '/out/data/myferryboxtest.csv' \
  '/out/plots/mytileplottest.png' \
  '2023-01-01' \
  '2023-12-31' \
  'salinity,chlorophyll' \
  'null' 'null' \
  '2023-08-08'

# Explanation of the parameters:
date; docker run \
  -v './testresults:/out:rw' \
  -e 'SCRIPT=netcdf_tile_plot.R' \
  ferry-rscripts:${today} \

  # Input FerryBox CSV
  '/out/data/myferryboxtest.csv' \

  # Output plot (if only a directory is given, defaults to ferrybox_tile.png)
  '/out/plots/mytileplottest.png' \

  # Start date
  '2023-01-01' \

  # End date
  '2023-12-31' \

  # Parameters (comma-separated)
  'salinity,chlorophyll' \

  # Latitude filter (minLat maxLat) or 'null' 'null'
  'null' 'null' \

  # Storm date (or 'null')
  '2023-08-08'

```


## Pygeoapi / OGC HTTP API

These scripts can be exposed as OGC API Processes via Pygeoapi, allowing remote access to ferrybox data extraction and analysis functions through HTTP endpoints.

### Setup Instructions

To deploy these scripts as OGC API Processes on your Pygeoapi instance, follow these steps:

#### 1. Prerequisites
- A Pygeoapi instance installed and running (see [Pygeoapi documentation](https://pygeoapi.io/))
- Docker image built with your scripts (see Docker section above)
- Example: [AquaINFRA Pygeoapi instance](https://example-server-url/pygeoapi)

#### 2. Create Process Files

For **each script**, you need to create:
- **Python process file** (e.g., `netcdf_extract_fb_data.py`) in `pygeoapi/plugin/process/`
- **JSON metadata file** (e.g., `netcdf_extract_fb_data.json`) with OGC API Process metadata

Example structure for `netcdf_extract_fb_data.py`:

```python
import subprocess
from pygeoapi.process.base import BaseProcessor

class NetcdfExtractFbDataProcessor(BaseProcessor):
    """Extracts ferrybox measurements from THREDDS server"""
    
    def __init__(self):
        super().__init__()
        self.metadata = {
            'version': '1.0.0',
            'title': 'FerryBox Data Extraction',
            'description': 'Extract ferrybox measurements (temperature, salinity, etc.) from THREDDS',
            'keywords': ['ferrybox', 'oceanography', 'extraction'],
            'links': [
                {'type': 'text/html', 
                 'rel': 'canonical',
                 'title': 'Information',
                 'href': 'https://github.com/NIVANorge/Ferrybox-galaxy-scripts'}
            ],
            'inputs': {
                'url_thredds': {
                    'title': 'THREDDS URL',
                    'description': 'URL to the THREDDS dataset',
                    'schema': {'type': 'string'},
                    'minOccurs': 1,
                    'maxOccurs': 1
                },
                'output_csv': {
                    'title': 'Output CSV path',
                    'description': 'Path for output CSV file',
                    'schema': {'type': 'string'},
                    'minOccurs': 1,
                    'maxOccurs': 1
                },
                'parameters': {
                    'title': 'Parameters',
                    'description': 'Comma-separated list (temperature,salinity,chlorophyll,turbidity,fdom,oxygen_sat)',
                    'schema': {'type': 'string'},
                    'minOccurs': 0,
                    'maxOccurs': 1
                },
                'start_date': {
                    'title': 'Start date',
                    'description': 'Start date (YYYY-MM-DD)',
                    'schema': {'type': 'string', 'format': 'date'},
                    'minOccurs': 1,
                    'maxOccurs': 1
                },
                'end_date': {
                    'title': 'End date',
                    'description': 'End date (YYYY-MM-DD)',
                    'schema': {'type': 'string', 'format': 'date'},
                    'minOccurs': 1,
                    'maxOccurs': 1
                },
                'bbox': {
                    'title': 'Bounding box',
                    'description': 'Bounding box [minLon, maxLon, minLat, maxLat] or null',
                    'schema': {'type': 'array', 'items': {'type': 'number'}},
                    'minOccurs': 0,
                    'maxOccurs': 1
                }
            },
            'outputs': {
                'result': {
                    'title': 'CSV output',
                    'description': 'Extracted data as CSV',
                    'schema': {'type': 'object', 'contentMediaType': 'text/csv'}
                }
            }
        }

    def execute(self, data):
        """Execute the process"""
        # Build Docker command from inputs
        cmd = [
            'docker', 'run', '-v', './testresults:/out:rw',
            '-e', 'SCRIPT=netcdf_extract_fb_data.R',
            'ferry-rscripts:latest',
            data['url_thredds'],
            data['output_csv'],
            data.get('parameters', 'null'),
            data.get('start_date', ''),
            data.get('end_date', ''),
        ]
        
        # Add bbox if provided
        if data.get('bbox'):
            cmd.extend(data['bbox'])
        else:
            cmd.extend(['null', 'null', 'null', 'null'])
        
        # Execute and return results
        subprocess.run(cmd, check=True)
        return {'result': data['output_csv']}

    def __repr__(self):
        return f'<NetcdfExtractFbDataProcessor> {self.metadata["title"]}'
```

Example `netcdf_extract_fb_data.json` metadata:

```json
{
  "title": "FerryBox Data Extraction",
  "description": "Extract ferrybox measurements (temperature, salinity, chlorophyll, turbidity, fdom, oxygen_sat) from THREDDS server",
  "version": "1.0.0",
  "keywords": ["ferrybox", "oceanography", "data extraction", "THREDDS"],
  "links": [
    {
      "type": "text/html",
      "rel": "canonical",
      "title": "Repository",
      "href": "https://github.com/NIVANorge/Ferrybox-galaxy-scripts"
    }
  ],
  "inputs": {
    "url_thredds": {
      "title": "THREDDS URL",
      "description": "URL to the THREDDS FerryBox dataset",
      "schema": {"type": "string", "default": "https://thredds.niva.no/thredds/dodsC/datasets/nrt/color_fantasy.nc"}
    },
    "output_csv": {
      "title": "Output CSV Path",
      "description": "Path where output CSV will be saved",
      "schema": {"type": "string", "default": "/out/ferrybox.csv"}
    },
    "parameters": {
      "title": "Parameters",
      "description": "Comma-separated parameter names (temperature, salinity, chlorophyll, turbidity, fdom, oxygen_sat)",
      "schema": {"type": "string", "default": "temperature,salinity,chlorophyll"}
    },
    "start_date": {
      "title": "Start Date",
      "description": "Start date in YYYY-MM-DD format",
      "schema": {"type": "string", "format": "date"}
    },
    "end_date": {
      "title": "End Date",
      "description": "End date in YYYY-MM-DD format",
      "schema": {"type": "string", "format": "date"}
    },
    "bbox": {
      "title": "Bounding Box",
      "description": "Optional bounding box as [minLon, maxLon, minLat, maxLat]",
      "schema": {"type": "array", "items": {"type": "number"}, "minItems": 4, "maxItems": 4}
    }
  },
  "outputs": {
    "result": {
      "title": "CSV Result",
      "description": "Extracted ferrybox data as CSV file",
      "schema": {"type": "object", "contentMediaType": "text/csv"}
    }
  }
}
```

#### 3. Register Processes in Pygeoapi Config

Add to `pygeoapi-config.yml`:

```yaml
resources:
  ferrybox-extract-data:
    type: process
    processor:
      name: NetcdfExtractFbDataProcessor

  ferrybox-extract-logger:
    type: process
    processor:
      name: NetcdfLoggerExtractProcessor

  ferrybox-assessment-area:
    type: process
    processor:
      name: NetcdfAssessmentAreaProcessor

  ferrybox-join-dataframes:
    type: process
    processor:
      name: NetcdfJoinDataframesProcessor

  ferrybox-scatter-plot:
    type: process
    processor:
      name: NetcdfScatterPlotProcessor

  ferrybox-tile-plot:
    type: process
    processor:
      name: NetcdfTilePlotProcessor
```

#### 4. Register Processors in `pygeoapi/plugin.py`

```python
'process': {
    'NetcdfExtractFbDataProcessor': 'pygeoapi.process.niva.netcdf_extract_fb_data.NetcdfExtractFbDataProcessor',
    'NetcdfLoggerExtractProcessor': 'pygeoapi.process.niva.netcdf_logger_extract.NetcdfLoggerExtractProcessor',
    'NetcdfAssessmentAreaProcessor': 'pygeoapi.process.niva.netcdf_assessment_area.NetcdfAssessmentAreaProcessor',
    'NetcdfJoinDataframesProcessor': 'pygeoapi.process.niva.netcdf_join_dataframes.NetcdfJoinDataframesProcessor',
    'NetcdfScatterPlotProcessor': 'pygeoapi.process.niva.netcdf_scatter_plot.NetcdfScatterPlotProcessor',
    'NetcdfTilePlotProcessor': 'pygeoapi.process.niva.netcdf_tile_plot.NetcdfTilePlotProcessor',
}
```

#### 5. Install and Deploy

```bash
# Install pygeoapi with your plugins
source venv/bin/activate
cd pygeoapi
pip install -e .

# Generate OpenAPI spec
export PYGEOAPI_CONFIG=pygeoapi-config.yml
export PYGEOAPI_OPENAPI=pygeoapi-openapi.yml
pygeoapi openapi generate $PYGEOAPI_CONFIG --output-file $PYGEOAPI_OPENAPI

# Restart pygeoapi
sudo systemctl restart pygeoapi  # or your deployment method
```

#### 6. Test the API

```bash
# List available processes
curl https://your-pygeoapi-instance.com/pygeoapi/processes

# Execute a process
curl -X POST https://your-pygeoapi-instance.com/pygeoapi/processes/ferrybox-extract-data/execution \
  --header 'Content-Type: application/json' \
  --data '{
    "inputs": {
      "url_thredds": "https://thredds.niva.no/thredds/dodsC/datasets/nrt/color_fantasy.nc",
      "output_csv": "/out/ferrybox_data.csv",
      "parameters": "temperature,salinity,chlorophyll",
      "start_date": "2023-01-01",
      "end_date": "2023-12-31",
      "bbox": [58.5, 9.5, 59.9, 11.9]
    }
  }'

# Async request (recommended for long-running processes)
curl -i -X POST https://your-pygeoapi-instance.com/pygeoapi/processes/ferrybox-extract-data/execution \
  --header 'Content-Type: application/json' \
  --header 'Prefer: respond-async' \
  --data '{...}'
```

### Web API Service URL

Once deployed, update the URL below with your Pygeoapi instance:

**Web API Service:** *(To be added after deployment)*
- Base URL: `https://your-pygeoapi-instance.com/pygeoapi`
- Processes endpoint: `https://your-pygeoapi-instance.com/pygeoapi/processes`

### References

- [OGC API Processes Specification](https://ogcapi.ogc.org/processes/)
- [Pygeoapi Documentation](https://docs.pygeoapi.io/)
- [Example: AquaINFRA Pygeoapi Instance](https://github.com/NIVANorge/niva-aquainfra)
