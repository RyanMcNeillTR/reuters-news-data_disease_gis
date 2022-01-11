Nipah sites notebook
================

-   [India](#india)
    -   [ind_88](#ind_88)
-   [Bangladesh](#bangladesh)
    -   [bgd_87](#bgd_87)
    -   [bgd_76](#bgd_76)
-   [bgd_73](#bgd_73)

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

Get GADM data for repeated use.

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

In Bangladesh, the administrative breakdown goes:

-   Central government (level 0)
-   Divisions (level 1)
-   Districts, aka “zila” (level 2)
-   Sub-districts, aka “upazila” (level 3)
-   Union councils, municipalities and city corporations (level 4)

The union councils are rural areas and villages fall beneath those. For
now, we don’t have a good villages dataset.

The above paper leads us to [this
paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3323384/pdf/04-0701.pdf),
which says:

“Surveys took place in the villages of Chandpur (population 604), where
persons who died or were hospitalized had resided…”

So I eventually found Chandpur on Google Maps: 24.63901152049101,
88.53916109886907

``` r
bgd_87_point <- tribble(
  ~latitude, ~longitude,
  24.63901152049101, 88.53916109886907
) %>%
  st_as_sf(coords=c("longitude", "latitude"), crs=4326) 
```

``` r
bgd_87 <- bgd_gadm_4 %>%
  filter(str_detect(name_2, "Meherpur")) %>%
  select(name_2) %>%
  group_by(name_2) %>%
  summarise(geom = st_union(geom)) %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### bgd_76

This outbreak in Naogoan is also profiled in [the same paper used for
bgd_87]((https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3323384/pdf/04-0701.pdf)),
the outbreak in Chandpur village, Meherpur district.

Unfortunately, the paper never tells us where the actual index case
occured. Here’s two relevant passages:

“Surveys were conducted in the adjacent villages of East Chalksita
(population 529) and Biljoania (population 481); suspected deaths and
hospitalizations caused by Nipah virus infection were reported from both
villages.”

and

“The index case occurred in a 12-year-old boy with symptom onset on
January 11, 2003, and the last case occurred in 12-year-old girl on
January 28 (Figure B).”

But they never actually tell us where the index case lived.

In this case, we’re once again left with a large area and unable to
narrow it down. This needs additional reporting.

In [this report from
ICDDR,B](http://dspace.icddrb.org/jspui/bitstream/123456789/4811/1/2003-ICDDRBHealthScienceBulletin-Vol1%285%29-English.pdf),
we learn:

“Between 11 and 28 January 2003, another outbreak of severe illness
including features of encephalitis was reported affecting at least 17
residents (range 4- 42 years) of Chalksita and Biljoania villages
(located 45 km north-east of Rajshahi) in Naogaon district; 8 people
died.”

FYI: The GADM dataset spells it Naogaon.

``` r
bgd_76 <- bgd_gadm_4 %>%
  filter(name_2 == "Naogaon") %>%
  select(name_2) %>%
  group_by(name_2) %>%
  summarise(geom = st_union(geom)) %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

# bgd_73

<http://dspace.icddrb.org/jspui/bitstream/123456789/4816/1/2004-ICDDRBHealthScienceBulletin-Vol2%282%29-English.pdf>
