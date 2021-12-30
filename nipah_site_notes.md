Nipah sites notebook
================

-   [India](#india)
    -   [ind_88](#ind_88)

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.5     v dplyr   1.0.7
    ## v tidyr   1.1.4     v stringr 1.4.0
    ## v readr   2.0.2     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(sf)
```

    ## Linking to GEOS 3.9.1, GDAL 3.2.1, PROJ 7.2.1

## India

### ind_88

According to [this 2006
paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3373078/): “No
definitive information about the possible index case exists.”

So, for now, let’s use the GADM layer to at least find the Siliguri area
polygon.

First, let’s check the layers.

``` r
st_layers("./input_data/India/gadm36_IND.gpkg")
```

    ## Driver: GPKG 
    ## Available layers:
    ##     layer_name geometry_type features fields
    ## 1 gadm36_IND_3 Multi Polygon     2340     16
    ## 2 gadm36_IND_2 Multi Polygon      666     13
    ## 3 gadm36_IND_1 Multi Polygon       36     10
    ## 4 gadm36_IND_0 Multi Polygon        1      2

So I think the best strategy for using this data is to start at the
lowest level. Then you can also search across multiple geographies in a
single table.

``` r
india_gadm_3 <- st_read("./input_data/India/gadm36_IND.gpkg", layer="gadm36_IND_3") %>%
  janitor::clean_names()
```

    ## Reading layer `gadm36_IND_3' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\India\gadm36_IND.gpkg' 
    ##   using driver `GPKG'
    ## Simple feature collection with 2340 features and 16 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 68.18625 ymin: 6.754256 xmax: 97.41516 ymax: 35.50133
    ## Geodetic CRS:  WGS 84

Here we find what appears to be the correct polygon for Siliguri.

``` r
ind_88 <- india_gadm_3 %>%
  filter(str_detect(name_3, "Siliguri")) %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```
