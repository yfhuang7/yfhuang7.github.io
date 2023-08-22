---
title: Basic Data Analysis (in R)
date: 2019-01-31 23:11:05
tags: [data analysis, R]
---

Note: This piece is a quick view for data analysis,my experience of data analysis, and summary of R packages for water analysis. It might not cover everything, if you have more questions, other interests, or any suggestion, I welcome and appreciate.

### What is data analysis?

*Data analysis is a process of inspecting, cleansing, transforming, and modeling data with the goal of discovering useful information, informing conclusions, and supporting decision-making. - [Wikipedia](https://en.wikipedia.org/wiki/Data_analysis)*


**Data analysis is like cooking:**

* knowing what you want to cook (goal or final product) 
* deciding recipe (math, model, and algorithms) 
* preparing ingredients (raw data) and tools (package or library for cleaning data and processing data)
* cooking (running the code)
* seasoning (writing comment or note)
* garnishing (data vidulization)
* reflecting - is the meal/dish what we want? (evaluation or validation)

![The figure of data science process from Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/Data_visualization_process_v1.png/350px-Data_visualization_process_v1.png)


### The most frequent "question" and "error" in programming 

##### Make sure your environent
* Check your path `getwd()`
* Make sure your global environment is empty `rm(list= ls())` or the exist variables are not what you're going to use `ls()`

##### Check your data
* Check your data type (character, numeric, integer, logical, complex) and structure (atomic vector, list, matrix, data frame, factors) `typeof(), class(), str()`
* Check your data dimensions  `dim(), nrow(), ncol(), length()`
* Check your data `head(), tail(), summary()`
* Make sure the month and day are correct after it is read
* Make sure you know if you have any missing data `anyNA(), is.na(), summary()`

An example:
{% codeblock %}

## Example of data type matter
library(dataRetrieval) # load USGS library

# download successfully
siteNumber <- 16240500
startDate <- "2010-01-01"
endDate <- "2018-12-31"
readNWISpeak(siteNumber, startDate, endDate)  # Get peakflow data

# download successfully
siteNumber <- 16240500
startDate <- "01/01/2010"
endDate <- "12/31/2018"
readNWISpeak(siteNumber, startDate, endDate)

# download with wrong day and month
startDate <- "18/12/2010" 
endDate <- "12/31/2018"
readNWISpeak(siteNumber, startDate, endDate)

# download unsuccessfully
siteNumber <- 16240500
startDate <- as.factor("2010/01/01")
endDate <- as.factor("2018/12/31")
readNWISpeak(siteNumber, startDate, endDate)
{% endcodeblock %}


##### Import data and input data
* Make sure your file format, ex: .txt, .csv, .xlsx, .html, etc.
* `read.table()` and `read.csv(), read.delim()`
* Import excel data `install.packages("XLConnect"), or install.packages("xlsx")`. *Note: Install Java before head. XLConnect is a Java-based solution, so it is cross platform and returns satisfactory results. For large data sets it may be very slow.*
* Import JSON data `install.packages("rjson")`
* Import XML data `install.packages("XML")`
* Read SPSS, Stata, Systat, Minitab file `install.packages("foreign")`
* Read SAS file `install.packages("sas7bdat")`
* ... (A lot of packages out there for different data format!)
* __Make sure your input to a function is as the same format the function needs.__
* Use `strsplit()` to split strings


An example for reading excel file:
{% codeblock %}
library(xlsx)
# Read excel data with name of sheet
Action_cam <- read.xlsx("Pearl Harbor Data.xlsx", sheetName = "Action Cam")
str(Action_cam)
summary(Action_cam)

# read excel data with the number of sheet
Action_cam2 <- read.xlsx("excel_data.xlsx", sheetName = 6)
{% endcodeblock %}


An example for reading a text file with multiple commented lines:
{% codeblock %}
# Mannual delete the commented line (remember to have a copy of original file)
data_mannual <- read.table("USGS_Waihi_WQ_plots_modified.txt", sep = "\t", header = T)

# Remove the commented line in R (not changing the original file)
data_first <- read.table("USGS_Waihi_WQ_plots.txt", header =F, sep ='\t', comment.char = '#', stringsAsFactors = F)
data_first <- data_first[-grep("#", data_first$V1),]
colnames(data_first) <- as.character(data_first[1,])
data_first <- data_first[-c(1,2),]
{% endcodeblock %}



### Where to find help for R? Or learn more?
* Uncle google of course
* [stackoverflow](https://stackoverflow.com/)
* [R-help email list](https://stat.ethz.ch/mailman/listinfo/r-help)
* [R-bloggers](https://www.r-bloggers.com/)
* [R Weekly](https://rweekly.org/)
* ... (A lot out there for you to discover!)


### Packages

#### Some usefull packages 

* __devtools__: get package from github
* __reshape2__: reshape data
* __zoo__: package for time series
* __xts__: package for time series
* __dataRetrieval__: download USGS gage data
* __markwh/streamstats__: API for USGS [StreamStats](https://streamstats.usgs.gov/ss/)
* __sp__: spatial ploting tool
* __rgdal__: pakcage for raster data
* __ggplot2__: package for plotting nice figures


An example for streamstats:
{% codeblock %}
#devtools::install_github("markwh/streamstats") #An R package for using the USGS Streamstats API
library(streamstats)

#Get basin characteristics and download the watershed shapefiles ---------------
singleInfo <- 
  delineateWatershed(-156.679722, 20.946, rcode = "HI",
                     includeparameters = "true", includefeatures = "true", crs = 4326)
singleInfo.unlist <- data.frame(matrix(unlist(singleInfo[[3]]), nrow = 57, byrow = F))
singleInfo.unlist <- singleInfo.unlist[,c(-1,-2)]
colnames(singleInfo.unlist) <- c("Description","Par","Unit","Value")
write.csv(singleInfo.unlist, "delineation_par_test.csv")  # Write all the parameter to a file

downloadGIS(singleInfo$workspaceID, "delineation_test.zip",format = "shapefile")  # Sownload the delineated watershed
{% endcodeblock %}

An exmaple for ggplot2:
{% codeblock %}
# install.package("ggplot2")
library(ggplot2)

# Read excel data with name of sheet
library(xlsx)
Action_cam <- read.xlsx("excel_data.xlsx", sheetName = "Action Cam")
# ggplot of how many time the camera saw each genus
ggplot(data = Action_cam) + 
  geom_bar(aes(x = Genus))


## What is the count for each genus?
Genus <- levels(Action_cam$Genus)
sum(Action_cam$Count, na.rm = T)

# 
genus_dataframe <- data.frame(Genus = as.factor(Genus), Count = NA)  # 
for(i in 1:length(Genus)){
  genus_dataframe$Count[i] <- sum(Action_cam$Count[Action_cam$Genus %in% Genus[i]], na.rm = T)
}
# ggplot
ggplot(data = genus_dataframe) +
  geom_bar(aes(x=Genus, y = Count, fill = Genus), stat = "identity")
{% endcodeblock %}



#### Useless but fun
{% codeblock %}
#install.packages("fortunes") # if you don't already have it
library(fortunes)
fortune()
fortune("memory")
{% endcodeblock %}
{% codeblock %}
#install.packages("cowsay")
library(cowsay)
say("Meow! Sis! I love R and \"debug\", you know?", by = "bigcat")
{% endcodeblock %}



#### Collection of R packages for water
(From: https://github.com/ropensci/hydrology)

##### **Data Retrieval**

**Hydrological data sources (surface water/groundwater quantity and quality)**

  - [dataRetrieval](http://cran.rstudio.com/web/packages/dataRetrieval/index.html): Collection of functions to help retrieve U.S. Geological Survey (USGS) and U.S. Environmental Protection Agency (EPA) water quality and hydrology data from web services.

  - [dbhydroR](http://cran.rstudio.com/web/packages/dbhydroR/index.html): Client for programmatic access to the South Florida Water Management District's [DBHYDRO database](https://www.sfwmd.gov/science-data/dbhydro), with functions for accessing hydrologic and water quality data.

  - [hddtools](http://cran.rstudio.com/web/packages/hddtools/index.html): Hydrological Data Discovery Tools. Facilitates discovery and handling of hydrological data, access to catalogues and databases.

  - [hydrolinks](http://cran.rstudio.com/web/packages/hydrolinks/index.html): Tools to link geographic data with hydrologic network, including lakes, streams and rivers. Includes automated download of U.S. National Hydrography Network and other hydrolayers.

  - [hydroscoper](http://cran.rstudio.com/web/packages/hydroscoper/index.html): R interface to the [Greek National Data Bank for Hydrological and Meteorological Information](http://www.hydroscope.gr/). It covers Hydroscope's data sources and provides functions to transliterate, translate and download them into tidy dataframes (tibbles).

  - [<span class="GitHub">kiwisR</span>](https://github.com/rywhale/kiwisR/): Wrapper for retrieving data from [KISTERS WISKI databases](https://www.kisters.net/NA/products/wiski/) via the KiWIS API. GitHub only package.

  - [rnrfa](http://cran.rstudio.com/web/packages/rnrfa/index.html): Utility functions to retrieve data from the [UK National River Flow Archive](http://nrfa.ceh.ac.uk/). There are functions to retrieve stations falling in a bounding box, to generate a map and extracting time series and general information.

  - [sbtools](http://cran.rstudio.com/web/packages/sbtools/index.html): Tools for interacting with [U.S. Geological Survey ScienceBase](https://www.sciencebase.gov) data cataloging and collaborative data management platform. Functions included for querying ScienceBase, and creating and fetching datasets.

  - [tidyhydat](http://cran.rstudio.com/web/packages/tidyhydat/index.html): Provides functions to access historical and real-time national 'hydrometric' data from Water Survey of Canada data sources ( <http://dd.weather.gc.ca/hydrometric/csv/> and <http://collaboration.cmc.ec.gc.ca/cmc/hydrometrics/www/>) and then applies tidy data principles.

  - [washdata](http://cran.rstudio.com/web/packages/washdata/index.html): Urban water and sanitation survey dataset from survey conducted in Dhaka, Bangladesh, part of a series of surveys to be conducted in various cities including Accra, Ghana; Nakuru, Kenya; Antananarivo, Madagascar; Maputo, Mozambique; and, Lusaka, Zambia.

  - [waterData](http://cran.rstudio.com/web/packages/waterData/index.html): Imports U.S. Geological Survey (USGS) daily hydrologic data from [USGS web services](https://waterservices.usgs.gov/), plots the data, addresses some common data problems, and calculates and plots anomalies.

  - [WaterML](http://cran.rstudio.com/web/packages/WaterML/index.html): Lets you connect to any of the Consortium of Universities for the Advancement of Hydrologic Sciences, Inc. ('CUAHSI') Water Data Center 'WaterOneFlow' web services and read any 'WaterML' hydrological time series data file.

**Meteorological data (precipitation, radiation, temperature, etc - including both measurements and reanalysis)**

  - [bomrang](http://cran.rstudio.com/web/packages/bomrang/index.html): Provides functions to interface with Australian Government Bureau of Meteorology (BOM) data, fetching data and returning a tidy data frame of précis forecasts, historical and current weather data from stations, agriculture bulletin data, BOM 0900 or 1500 weather bulletins or a raster stack object of satellite imagery from GeoTIFF files.

  - [countyweather](http://cran.rstudio.com/web/packages/countyweather/index.html): Interacts with NOAA data sources (including the [NCDC API](http://www.ncdc.noaa.gov/cdo-web/webservices/v2) and ISD data) using functions from the 'rnoaa' package to obtain and compile weather time series for U.S. counties.

  - [getMet](http://cran.rstudio.com/web/packages/getMet/index.html): Functions for sourcing, formatting, and editing meteorological data for hydrologic models.

  - [GSODR](http://cran.rstudio.com/web/packages/GSODR/index.html): Provides automated downloading, parsing, cleaning, unit conversion and formatting of Global Surface Summary of the Day (GSOD) weather data from the from the USA National Centers for Environmental Information (NCEI) for use in R.

  - [MODISTools](http://cran.rstudio.com/web/packages/MODISTools/index.html): Programmatic Interface to the MODIS Land Products Subsets [Web Services](https://modis.ornl.gov/data/modis_webservice.html). Allows for easy downloads of 'MODIS' time series.

  - [rdwd](http://cran.rstudio.com/web/packages/rdwd/index.html): Handle climate data from the German DWD (['Deutscher Wetterdienst'](https://www.dwd.de/EN/climate_environment/cdc/cdc.html)).

  - [RNCEP](http://cran.rstudio.com/web/packages/RNCEP/index.html): Contains functions to retrieve, organize, and visualize weather data from the [NCEP/NCAR Reanalysis](http://www.esrl.noaa.gov/psd/data/gridded/data.ncep.reanalysis.html) and [NCEP/DOE Reanalysis II](http://www.esrl.noaa.gov/psd/data/gridded/data.ncep.reanalysis2.html) datasets.

  - [rnoaa](http://cran.rstudio.com/web/packages/rnoaa/index.html): Client for many NOAA data sources including the [NCDC climate API](https://www.ncdc.noaa.gov/cdo-web/webservices/v2), with functions for each of the API endpoints: data, data categories, data sets, data types, locations, location categories, and stations. Includes interface NOAA sea ice data, severe weather inventory, Historical Observing Metadata Repository ('HOMR'), storm data via 'IBTrACS', tornado data via the NOAA storm prediction center, and more.

  - [rpdo](http://cran.rstudio.com/web/packages/rpdo/index.html): Get Monthly Pacific Decadal Oscillation (PDO) index values from January 1900 to present. See also [rsoi](http://cran.rstudio.com/web/packages/rsoi/index.html) for downloading Southern Oscillation Index, Oceanic Nino Index and North Pacific Gyre Oscillation data.

  - [rwunderground](http://cran.rstudio.com/web/packages/rwunderground/index.html): Tools for getting historical weather information and forecasts from wunderground.com. Historical weather and forecast data includes, but is not limited to, temperature, humidity, windchill, wind speed, dew point, heat index. Additionally, the weather underground weather API also includes information on sunrise/sunset, tidal conditions, satellite/webcam imagery, weather alerts, hurricane alerts and historical high/low temperatures.

  - [smapr](http://cran.rstudio.com/web/packages/smapr/index.html): Acquisition and Processing of NASA Soil Moisture Active-Passive (SMAP) Data. Facilitates programmatic access to search for, acquire, and extract NASA Soil Moisture Active Passive (SMAP) data.

  - [weathercan](http://cran.rstudio.com/web/packages/weathercan/index.html): Provides means for downloading historical weather data from the [Environment and Climate Change Canada website](http://climate.weather.gc.ca/historical_data/search_historic_data_e.html). Data can be downloaded from multiple stations and over large date ranges and automatically processed into a single dataset. Tools are also provided to identify stations either by name or proximity to a location.

  - [worldmet](http://cran.rstudio.com/web/packages/worldmet/index.html): Functions to import data from more than 30,000 surface meteorological sites around the world managed by the National Oceanic and Atmospheric Administration (NOAA) [Integrated Surface Database (ISD)](https://www.ncdc.noaa.gov/isd).

##### **Data Analysis**

**Data tidying (gap-filling, data organization, QA/QC, etc)**

  - [driftR](http://cran.rstudio.com/web/packages/driftR/index.html): A tidy implementation of equations that correct for instrumental drift in continuous water quality monitoring data using one or two standard reference values. The equations implemented are from [Hasenmueller (2011)](http://doi.org/10.7936/K7N014KS).

  - [<span class="GitHub">fasstr</span>](https://github.com/bcgov/fasstr/) Functions to tidy, summarize, analyze, trend, and visualize streamflow data. This package summarizes continuous daily mean streamflow data into various daily, monthly, annual, and long-term statistics, completes annual trends and frequency analyses, in both table and plot formats. GitHub only package.

  - [climdex.pcic](http://cran.rstudio.com/web/packages/climdex.pcic/index.html): PCIC Implementation of Climdex Routines PCIC's implementation of Climdex routines for computation of extreme climate indices.

  - [climatol](http://cran.rstudio.com/web/packages/climatol/index.html): Functions for the quality control, homogenization and missing data infilling of climatological series and to obtain climatological summaries and grids from the results. Also functions to draw wind-roses and Walter\&Lieth climate diagrams.

  - [getMet](http://cran.rstudio.com/web/packages/getMet/index.html): Functions for sourcing, formatting, and editing meteorological data for hydrologic models.

**Hydrograph analysis (functions for working with streamflow data, e.g. flow statistics, trends, biological indices, etc.)**

  - [biotic](http://cran.rstudio.com/web/packages/biotic/index.html): Calculates a range of UK freshwater invertebrate biotic indices including BMWP, Whalley, WHPT, Habitat-specific BMWP, AWIC, LIFE and PSI.

  - [EcoHydRology](http://cran.rstudio.com/web/packages/EcoHydRology/index.html): This package provides a flexible foundation for scientists, engineers, and policy makers to base teaching exercises as well as for more applied use to model complex eco-hydrological interactions, including some SWAT calibration functions.

  - [ecoval](http://cran.rstudio.com/web/packages/ecoval/index.html): Functions for evaluating and visualizing ecological assessment procedures for surface waters containing physical, chemical and biological assessments in the form of value functions.

  - [<span class="GitHub">EflowStats</span>](https://github.com/USGS-R/EflowStats/): Calculates a suite of ecological flow statistics and fundamental properties of daily streamflow for a given set of data. GitHub only package.

  - [EGRET](http://cran.rstudio.com/web/packages/EGRET/index.html): Exploration and Graphics for RivEr Trends (EGRET): analysis of long-term changes in water quality and streamflow, including the water-quality method Weighted Regressions on Time, Discharge, and Season (WRTDS).

  - [EGRETci](http://cran.rstudio.com/web/packages/EGRETci/index.html): A bootstrap method for estimating uncertainty of water quality trends.

  - [FAdist](http://cran.rstudio.com/web/packages/FAdist/index.html): Probability distributions that are sometimes useful in hydrology.

  - [FlowScreen](http://cran.rstudio.com/web/packages/FlowScreen/index.html): Screens daily streamflow time series for temporal trends and change-points. This package has been primarily developed for assessing the quality of daily streamflow time series. It also contains tools for plotting and calculating many different streamflow metrics.

  - [hydrostats](http://cran.rstudio.com/web/packages/hydrostats/index.html): Calculates a suite of hydrologic indices for daily time series data that are widely used in hydrology and stream ecology.

  - [hydroTSM](http://cran.rstudio.com/web/packages/hydroTSM/index.html): Functions for management, analysis, interpolation and plotting of time series used in hydrology and related environmental sciences. In particular, this package is highly oriented to hydrological modelling tasks.

  - [lfstat](http://cran.rstudio.com/web/packages/lfstat/index.html): Functions to compute and plot statistics described in the "Manual on Low-flow Estimation and Prediction", published by the World Meteorological Organisation (WMO).

**Meteorology (functions for working with meteorological and climate data)**

  - [Evapotranspiration](http://cran.rstudio.com/web/packages/Evapotranspiration/index.html): Functions to calculate potential evapotranspiration (PET) and actual evapotranspiration (AET) from 21 different formulations including Penman, Penman-Monteith FAO 56, Priestley-Taylor and Morton models.

  - [humidity](http://cran.rstudio.com/web/packages/humidity/index.html): Functions for calculating saturation vapor pressure (hPa), partial water vapor pressure (Pa), relative humidity (%), absolute humidity (kg/m^3), specific humidity (kg/kg), and mixing ratio (kg/kg) from temperature (K) and dew point (K). Conversion functions between humidity measures are also provided.

  - [MBC](http://cran.rstudio.com/web/packages/MBC/index.html): Multivariate Bias Correction of Climate Model Outputs. Calibrate and apply multivariate bias correction algorithms for climate model simulations of multiple climate variables.

  - [meteoland](http://cran.rstudio.com/web/packages/meteoland/index.html): Functions to estimate weather variables at any position of a landscape.

  - [musica](http://cran.rstudio.com/web/packages/musica/index.html): Multiscale Climate Model Assessment. Provides function to compare and analyse time series.

  - [qmap](http://cran.rstudio.com/web/packages/qmap/index.html): Empirical adjustment of the distribution of variables originating from (regional) climate model simulations using quantile mapping.

  - [<span class="GitHub">Rainmaker</span>](https://github.com/USGS-R/Rainmaker/): Instantaneous rainfall data processing for defining event periods, determination of antecedent rainfall conditions and X-hr intensities. GitHub only package.

**Other**

  - [berryFunctions](http://cran.rstudio.com/web/packages/berryFunctions/index.html): Draw horizontal histograms, color scattered points by 3rd dimension, enhance date- and log-axis plots, zoom in X11 graphics, trace errors and warnings, use the unit hydrograph in a linear storage cascade, convert lists to data.frames and arrays, fit multiple functions.

  - [GWSDAT](http://cran.rstudio.com/web/packages/GWSDAT/index.html): Shiny application for the analysis of groundwater monitoring data, designed to work with simple time-series data for solute concentration and ground water elevation, but can also plot non-aqueous phase liquid (NAPL) thickness if required.

  - [hydrogeo](http://cran.rstudio.com/web/packages/hydrogeo/index.html): Contains one function for drawing Piper diagrams (also called Piper-Hill diagrams) of water analyses for major ions.

  - [kitagawa](http://cran.rstudio.com/web/packages/kitagawa/index.html): Provides tools to calculate the theoretical hydrodynamic response of an aquifer undergoing harmonic straining or pressurization. There are two classes of models here: (1) for sealed wells, based on the model of Kitagawa et al (2011), and (2) for open wells, based on the models of Cooper et al (1965), Hsieh et al (1987), Rojstaczer (1988), and Liu et al (1989).

  - [<span class="GitHub">MBSStools</span>](https://github.com/leppott/MBSStools/): Suite of tools for data manipulation and calculations for Maryland DNR MBSS program. GitHub only package.

  - [MODIStsp](http://cran.rstudio.com/web/packages/MODIStsp/index.html): Suite of tools to automate the Download and Preprocessing of MODIS Land Products Data. Allows automating the creation of time series of rasters derived from MODIS Satellite Land Products data. It performs several typical preprocessing steps such as download, mosaicking, reprojection and resize of data acquired on a specified time period.

  - [lulcc](http://cran.rstudio.com/web/packages/lulcc/index.html): Classes and methods for spatially explicit land use change modelling.

  - [wql](http://cran.rstudio.com/web/packages/wql/index.html): Functions to assist in the processing and exploration of data from environmental monitoring programs. Intended for programs that sample approximately monthly, quarterly or annually at discrete stations, a feature of many legacy data sets. Most of the functions should be useful for analysis of similar-frequency time series regardless of the subject matter.

  - [WRTDStidal](http://cran.rstudio.com/web/packages/WRTDStidal/index.html): An adaptation for estuaries (tidal waters) of weighted regression on time, discharge, and season to evaluate trends in water quality time series.

**Spatial data processing**

The CRAN [Spatial](Spatial.html) Task View gives an overview of packages to be used in R to read, visualise, and analyse spatial data. See also the ROpenSci [MapTools Listing](https://github.com/ropensci/maptools).

  - [hydrolinks](http://cran.rstudio.com/web/packages/hydrolinks/index.html): Tools to link geographic data with hydrologic network, including lakes, streams and rivers. Includes automated download of U.S. National Hydrography Network and other hydrolayers.

  - [<span class="GitHub">lumpR</span>](https://github.com/tpilz/lumpR/): Functions for a semi-automated approach of the delineation and description of landscape units and partition into terrain components. It can be used for the pre-processing of semi-distributed large-scale hydrological and erosion models using catena-representation (WASA-SED, CATFLOW). GitHub only package.

  - [lakemorpho](http://cran.rstudio.com/web/packages/lakemorpho/index.html): Lake morphometry metrics are used by limnologists to understand, among other things, the ecological processes in a lake. The 'lakemorpho' package provides the tools to calculate a typical suite of these metrics from an input elevation model and lake polygon.

  - [Watersheds](http://cran.rstudio.com/web/packages/Watersheds/index.html): Methods for watersheds aggregation and spatial drainage network analysis.

##### **Modeling**

**Process-based modeling (scripts for preparing inputs/outputs and running process-based models)**

See also the [RHydro project](https://r-forge.r-project.org/R/?group_id=411) on R-forge.

  - [airGR](http://cran.rstudio.com/web/packages/airGR/index.html): Hydrological modelling tools developed at Irstea-Antony (HYCAR Research Unit, France). The package includes several conceptual rainfall-runoff models (GR4H, GR4J, GR5J, GR6J, GR2M, GR1A), a snow accumulation and melt model (CemaNeige) and the associated functions for their calibration and evaluation.

  - [airGRteaching](http://cran.rstudio.com/web/packages/airGRteaching/index.html): Add-on package to the 'airGR' package that simplifies its use and is aimed at being used for teaching hydrology.

  - [bigleaf](http://cran.rstudio.com/web/packages/bigleaf/index.html): Calculation of physical (e.g. aerodynamic conductance, surface temperature), and physiological (e.g. canopy conductance, water-use efficiency) ecosystem properties from eddy covariance data and accompanying meteorological measurements. Calculations assume the land surface to behave like a 'big-leaf' and return bulk ecosystem/canopy variables.

  - [boussinesq](http://cran.rstudio.com/web/packages/boussinesq/index.html): Collection of functions for the One-Dimensional Boussinesq Equation (ground-water).

  - [dynatopmodel](http://cran.rstudio.com/web/packages/dynatopmodel/index.html): Implementation and enhancement of the Dynamic TOPMODEL semi-distributed hydrological model. Includes some preprocessing, utility and routines for displaying outputs. See also [topmodel](http://cran.rstudio.com/web/packages/topmodel/index.html).

  - [Ecohydmod](http://cran.rstudio.com/web/packages/Ecohydmod/index.html): Simulates the soil water balance (soil moisture, evapotranspiration, leakage and runoff), rainfall series by using the marked Poisson process and the vegetation growth through the normalized difference vegetation index (NDVI). See [Souza et al. (2016)](http://doi.org/10.1002/hyp.10953).

  - [EcoHydRology](http://cran.rstudio.com/web/packages/EcoHydRology/index.html): Flexible foundation for scientists, engineers, and policy makers to base teaching exercises as well as for more applied use to model complex eco-hydrological interactions, including some SWAT calibration functions.

  - [geotopbricks](http://cran.rstudio.com/web/packages/geotopbricks/index.html): An R Plug-in for the Distributed Hydrological Model [GEOtop](https://github.com/geotopmodel). The package analyzes raster maps and other information as input/output files from the Hydrological Distributed Model GEOtop.

  - [<span class="GitHub">hydromad</span>](https://github.com/floybix/hydromad/): Hydrological Model Assessment and Development - [website](http://hydromad.catchment.org). GitHub only package.

  - [hydroPSO](http://cran.rstudio.com/web/packages/hydroPSO/index.html): Particle Swarm Optimisation (PSO) algorithm for the calibration of environmental and other real-world models that need to be executed from the system console. hydroPSO is model-independent, allowing the user to easily interface any computer simulation model with the PSO calibration engine.

  - [kwb.hantush](http://cran.rstudio.com/web/packages/kwb.hantush/index.html): Calculation groundwater mounding beneath an infiltration basin based on the [Hantush (1967)](http://doi.org/10.1029/WR003i001p00227) equation. The correct implementation is shown with a verification example based on a USGS report ([page 25](http://pubs.usgs.gov/sir/2010/5102/support/sir2010-5102.pdf)).

  - [<span class="GitHub">loadflex</span>](https://github.com/USGS-R/loadflex/): Models and Tools for Watershed Flux Estimates. See [paper](http://dx.doi.org/10.1890/ES14-00517.1). GitHub only package.

  - [reservoir](http://cran.rstudio.com/web/packages/reservoir/index.html): Tools for Analysis, Design, and Operation of Water Supply Storages. Measure single-storage water supply system performance using resilience, reliability, and vulnerability metrics; assess storage-yield-reliability relationships; determine no-fail storage with sequent peak analysis; optimize release decisions for water supply, hydropower, and multi-objective reservoirs using deterministic and stochastic dynamic programming; generate inflow replicates using parametric and non-parametric models; evaluate inflow persistence using the Hurst coefficient.

  - [RHMS](http://cran.rstudio.com/web/packages/RHMS/index.html): Hydrologic modelling system is an object oriented tool which enables R users to simulate and analyze hydrologic events. The package proposes functions and methods for construction, simulation, visualization, and calibration of hydrologic systems.

  - [RSAlgaeR](http://cran.rstudio.com/web/packages/RSAlgaeR/index.html): Builds Empirical Remote Sensing Models of Water Quality Variables and Analyzes Long-Term Trends. Assists in processing reflectance data, developing empirical models using stepwise regression and a generalized linear modeling approach, cross- validation, and analysis of trends in water quality conditions (specifically chl-a) and climate conditions using the Theil-Sen estimator.

  - [<span class="GitHub">streamDepletr</span>](https://github.com/szipper/streamDepletr/): Package for assessing the impacts of groundwater pumping on streams. GitHub only package.

  - [<span class="GitHub">streamMetabolizer</span>](https://github.com/USGS-R/streamMetabolizer/): Estimate aquatic photosynthesis and respiration (collectively, metabolism) from time series data on dissolved oxygen, water temperature, depth, and light via inverse modeling. The package assists with data preparation, handles data gaps during modeling, and provides tabular and graphical reports of model outputs. GitHub only package.

  - [SWATmodel](http://cran.rstudio.com/web/packages/SWATmodel/index.html): The Soil and Water Assessment Tool (SWAT) is a river basin or watershed scale model developed by Dr. Jeff Arnold for the USDA-ARS.

  - [topmodel](http://cran.rstudio.com/web/packages/topmodel/index.html): Set of hydrological functions including the hydrological model TOPMODEL, which is based on the 1995 FORTRAN version by Keith Beven. From version 0.7.0, the package is put into maintenance mode. See also [dynatopmodel](http://cran.rstudio.com/web/packages/dynatopmodel/index.html).

  - [TUWmodel](http://cran.rstudio.com/web/packages/TUWmodel/index.html): Lumped Hydrological Model for Education Purposes: a lumped conceptual rainfall-runoff model, following the structure of the HBV model. The model runs on a daily or shorter time step and consists of a snow routine, a soil moisture routine and a flow routing routine.

  - [wasim](http://cran.rstudio.com/web/packages/wasim/index.html): Helpful tools for data processing and visualisation of results of the hydrological model WASIM-ETH.

  - [water](http://cran.rstudio.com/web/packages/water/index.html): Tools and functions to calculate actual Evapotranspiration using surface energy balance models.

  - [WRSS](http://cran.rstudio.com/web/packages/WRSS/index.html): Water resources system simulator is a tool for simulation and analysis of large-scale water resources systems. 'WRSS' proposes functions and methods for construction, simulation and analysis of primary water resources features (e.g. reservoirs, aquifers, and etc.) based on Standard Operating Policy (SOP).

**Statistical modeling (hydrology-related statistical models)**

The [Environmetrics](Environmetrics.html): Task View gives an overview of packages used in the analysis of environmental data, encompassing hydrological data, including many statistical approaches used in the ecological sciences. Additionally, packages that help model datasets with extreme values are discussed in the [ExtremeValue](ExtremeValue.html) Task View.

  - [CityWaterBalance](http://cran.rstudio.com/web/packages/CityWaterBalance/index.html): Retrieves data and estimates unmeasured flows of water through the urban network. Any city may be modeled with preassembled data, but data for US cities can be gathered via web services using this package and dependencies [geoknife](http://cran.rstudio.com/web/packages/geoknife/index.html) and [dataRetrieval](http://cran.rstudio.com/web/packages/dataRetrieval/index.html).

  - [<span class="Rforge">dream</span>](https://R-Forge.R-project.org/projects/dream/): DiffeRential Evolution Adaptive Metropolis (DREAM). Efficient global MCMC even in high-dimensional spaces. R-Forge only package.

  - [<span class="GitHub">fuse</span>](https://github.com/cvitolo/fuse/): An R package implementing the Framework for Understanding Structural Errors [cvitolo.github.io/fuse/](https://cvitolo.github.io/fuse/). GitHub only package.

  - [hydroApps](http://cran.rstudio.com/web/packages/hydroApps/index.html): Package providing tools for hydrological applications and models developed for regional analysis in Northwestern Italy focused on Flood Frequency Analysis.

  - [hydroGOF](http://cran.rstudio.com/web/packages/hydroGOF/index.html): S3 functions implementing both statistical and graphical goodness-of-fit measures between observed and simulated values, mainly oriented to be used during the calibration, validation, and application of hydrological models.

  - [HydroMe](http://cran.rstudio.com/web/packages/HydroMe/index.html): Estimates the parameters in infiltration and water retention models by curve-fitting method. The models considered are those that are commonly used in soil science.

  - [hyfo](http://cran.rstudio.com/web/packages/hyfo/index.html): Focuses on data processing and visualization in hydrology and climate forecasting. Main function includes data extraction, data downscaling, data resampling, gap filler of precipitation, bias correction of forecasting data, flexible time series plot, and spatial map generation. It is a good pre- processing and post-processing tool for hydrological and hydraulic modellers.

  - [IDF](http://cran.rstudio.com/web/packages/IDF/index.html): Functions to read precipitation data from German weather service (DWD) files and Berlin station data from and additionally Intensity-duration-frequency (IDF) parameters can be estimated from a given data.frame containing a precipitation time series. IDF parameters are estimated on the basis of a duration-dependent generalised extreme value distribution and IDF curves based on these estimated parameters can be plotted.

  - [<span class="GitHub">NEON-stream-discharge</span>](https://github.com/NEONScience/NEON-stream-discharge/): NEON Stage-Discharge Rating Curve. Instructions to set up a docker container which calculates the stage-discharge rating curve for a site and water year, developed using a Bayesian modeling technique. GitHub only package.

  - [LPM](http://cran.rstudio.com/web/packages/LPM/index.html): Apply Univariate Long Memory Models, Apply Multivariate Short Memory Models To Hydrological Dataset, Estimate Intensity Duration Frequency curve to rainfall series.

  - [meteo](http://cran.rstudio.com/web/packages/meteo/index.html): Spatio-temporal geostatistical mapping of meteorological data.

  - [nsRFA](http://cran.rstudio.com/web/packages/nsRFA/index.html): A collection of statistical tools for objective (non-supervised) applications of the Regional Frequency Analysis methods in hydrology.

  - [RMAWGEN](http://cran.rstudio.com/web/packages/RMAWGEN/index.html): Functions for spatial multi-site stochastic generation of daily time series of temperature and precipitation.

  - [rtop](http://cran.rstudio.com/web/packages/rtop/index.html): Interpolation of Data with Variable Spatial Support Geostatistical interpolation of data with irregular spatial support such as runoff related data or data from administrative units.

  - [SCI](http://cran.rstudio.com/web/packages/SCI/index.html): Functions for generating Standardized Climate Indices (SCI). SCI is a transformation of (smoothed) climate (or environmental) time series that removes seasonality and forces the data to take values of the standard normal distribution. SCI was originally developed for precipitation. In this case it is known as the Standardized Precipitation Index (SPI).

  - [soilwater](http://cran.rstudio.com/web/packages/soilwater/index.html): Implements parametric formulas of soil water retention or conductivity curve. At the moment, only Van Genuchten (for soil water retention curve) and Mualem (for hydraulic conductivity) were implemented.

  - [SPEI](http://cran.rstudio.com/web/packages/SPEI/index.html): A set of functions for computing potential evapotranspiration and several widely used drought indices including the Standardized Precipitation-Evapotranspiration Index (SPEI).

  - [swmmr](http://cran.rstudio.com/web/packages/swmmr/index.html): Functions to connect the widely used [Storm Water Management Model (SWMM)](https://www.epa.gov/water-research/storm-water-management-model-swmm) of the United States Environmental Protection Agency (US EPA) to R.


#### Packages in Python for hydrology
(Please see: http://abouthydrology.blogspot.com/2016/11/python-resources-for-hydrologists.html)
