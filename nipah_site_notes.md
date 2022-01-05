Nipah sites notebook
================

-   [India](#india)
    -   [ind_88](#ind_88)
-   [Bangladesh](#bangladesh)
    -   [bgd_87](#bgd_87)

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

What I’ve tried to do here is first group incidents by country, then do
an individual section for each event. You’ll see that each section is
headlined by the ISO 3-digit country code and the unique identifier I
added to the Metabiota dataset in Google Sheets.

For general administrative boundaries, I’m using [the GADM
database](https://gadm.org/). When I start a new country, I download
that country’s geopackage into the directory
“./input_data/country/GADM/”, where I extract the file to that directory
instead of letting the unzipper create a new directory.

Because these files are large, you’ll want to use [Github’s Large File
Storage](https://git-lfs.github.com/) to track them. I would do the
following commands:

-   git lfs track “\*.zip”
-   git lfs track “\*.gpkg”

As of now, my goal is to have each incident with the following fields:

-   unique identifier
-   polygon geometry, if used
-   point geometry

We may want to consider adding a memo field to contain important
information about each incident but for now I’ve not done that.

What you might do is create a new R notebook and just start the document
like below (i.e. ignore all the stuff above here). Then when you’re
done, we can just append it to this doc.

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

As you can see here, the select and mutate function calls are what
create the end product mentioned in the intro section.

For precision, I’ve used “centroid” and “precise” — with “precise” being
the case when we are able to identify the exact location.

``` r
ind_88 <- india_gadm_3 %>%
  filter(str_detect(name_3, "Siliguri")) %>%
  select(poly_geom = geom) %>% # this right here is where we start building the end dataset
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid") %>%
  mutate(reuters_id = 88,
         country_iso = "BGD")
```

## Bangladesh

Get GADM data.

``` r
st_layers("./input_data/Bangladesh/gadm/gadm36_BGD_gpkg/gadm36_BGD.gpkg")
```

    ## Driver: GPKG 
    ## Available layers:
    ##     layer_name geometry_type features fields
    ## 1 gadm36_BGD_0 Multi Polygon        1      2
    ## 2 gadm36_BGD_1 Multi Polygon        7     10
    ## 3 gadm36_BGD_2 Multi Polygon       64     13
    ## 4 gadm36_BGD_3 Multi Polygon      545     16
    ## 5 gadm36_BGD_4 Multi Polygon     5158     14

``` r
bgd_gadm_4 <- st_read("./input_data/Bangladesh/gadm/gadm36_BGD_gpkg/gadm36_BGD.gpkg", layer="gadm36_BGD_4") %>%
  janitor::clean_names()
```

    ## Reading layer `gadm36_BGD_4' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\Bangladesh\gadm\gadm36_BGD_gpkg\gadm36_BGD.gpkg' 
    ##   using driver `GPKG'
    ## Simple feature collection with 5158 features and 14 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 88.01057 ymin: 20.74111 xmax: 92.67366 ymax: 26.63407
    ## Geodetic CRS:  WGS 84

### bgd_87

[This paper identifies an
outbreak](https://onlinelibrary.wiley.com/doi/full/10.1002/rmv.2010) in
Meherpur, Bangladesh from April-May 2001.

“The first NiV outbreak was reported in April 2001 from a village in
district Meherpur, Bangladesh with 13 confirmed cases and 9 (69.2%)
deaths.”

What we don’t know which Meherpur, though. There’s Meherpur at levels 2,
3 and 4.

``` r
bgd_gadm_4 %>%
  filter(str_detect(name_3, "Naogaon"))
```

    ## Simple feature collection with 13 features and 14 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 88.78811 ymin: 24.72055 xmax: 89.00752 ymax: 24.91384
    ## Geodetic CRS:  WGS 84
    ## First 10 features:
    ##    gid_0     name_0   gid_1   name_1     gid_2  name_2       gid_3
    ## 1    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 2    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 3    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 4    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 5    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 6    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 7    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 8    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 9    BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ## 10   BGD Bangladesh BGD.5_1 Rajshahi BGD.5.3_1 Naogaon BGD.5.3.6_1
    ##           name_3          gid_4             name_4 varname_4 type_4 engtype_4
    ## 1  Naogaon Sadar  BGD.5.3.6.1_1         Baktiarpur            Union     Union
    ## 2  Naogaon Sadar  BGD.5.3.6.2_1            Balihar            Union     Union
    ## 3  Naogaon Sadar  BGD.5.3.6.3_1           Barshail            Union     Union
    ## 4  Naogaon Sadar  BGD.5.3.6.4_1             Boalia            Union     Union
    ## 5  Naogaon Sadar  BGD.5.3.6.5_1          Chandipur            Union     Union
    ## 6  Naogaon Sadar  BGD.5.3.6.6_1          Dubalhati            Union     Union
    ## 7  Naogaon Sadar  BGD.5.3.6.7_1            Hapania            Union     Union
    ## 8  Naogaon Sadar  BGD.5.3.6.8_1        Hashaighari            Union     Union
    ## 9  Naogaon Sadar  BGD.5.3.6.9_1           Kirtipur            Union     Union
    ## 10 Naogaon Sadar BGD.5.3.6.10_1 Naogaon Paurashava            Union     Union
    ##        cc_4                           geom
    ## 1  50646013 MULTIPOLYGON (((88.91621 24...
    ## 2  50646014 MULTIPOLYGON (((88.78811 24...
    ## 3  50646021 MULTIPOLYGON (((88.8957 24....
    ## 4  50646029 MULTIPOLYGON (((88.96669 24...
    ## 5  50646043 MULTIPOLYGON (((88.96239 24...
    ## 6  50646051 MULTIPOLYGON (((88.85676 24...
    ## 7  50646058 MULTIPOLYGON (((88.91621 24...
    ## 8  50646065 MULTIPOLYGON (((88.85122 24...
    ## 9  50646073 MULTIPOLYGON (((88.89196 24...
    ## 10 50646099 MULTIPOLYGON (((88.91116 24...

The above paper leads us to [this
paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3323384/pdf/04-0701.pdf),
which says:

“Surveys took place in the villages of Chandpur (population 604), where
persons who died or were hospitalized had resided…”

So that’s a clue.
