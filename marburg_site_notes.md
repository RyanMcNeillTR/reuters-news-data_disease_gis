Marburg sites notebook
================

-   [Uganda](#uganda)
    -   [uga_x6](#uga_x6)
    -   [uga_21](#uga_21)
    -   [uga_40](#uga_40)
    -   [uga_53](#uga_53)
    -   [uga_59](#uga_59)
    -   [uga_60](#uga_60)
-   [Angola](#angola)
    -   [ago_70](#ago_70)
-   [Put it all together](#put-it-all-together)

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

``` r
library(arcpullr)
```

    ## Warning: package 'arcpullr' was built under R version 4.1.2

## Uganda

We will need this GADM layer, so let’s go ahead and load it.

``` r
uga_gadm_4 <- st_read("./input_data/Uganda/gadm/gadm36_UGA.gpkg", layer="gadm36_UGA_4") %>%
  janitor::clean_names()
```

    ## Reading layer `gadm36_UGA_4' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\Uganda\gadm\gadm36_UGA.gpkg' 
    ##   using driver `GPKG'
    ## Simple feature collection with 5341 features and 14 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 29.5715 ymin: -1.48214 xmax: 35.00027 ymax: 4.234466
    ## Geodetic CRS:  WGS 84

### uga_x6

Let’s start with the 2017 outbreak in Kween. [According to this
paper](https://researchonline.lshtm.ac.uk/id/eprint/4653965/1/Marburg-virus-disease-outbreak.pdf):

“The initial probable index case was a 35-year-old male herdsman (cattle
and goats) from Tulwo village, Kamwam Parish, Kaproron Sub-county of
Kween District who undertook frequent hunting forays to the Kaptum
grazing grounds in Kween District, which include a cave inhabited by
bats of Rousettus species. The investigation established that he was
also a crop farmer. He lived in an area close to a cave, inhabited by
Rousettus species fruit bats, on the slopes of Mount Elgon. Contacts
reported he frequented the interior of the cave with chil- dren to mine
rock salt for livestock and collect bat droppings for manure for his
crop farm.”

So I was able to track it down to Kamwam parish. I could not identify
the exact location of Tulwo village, so there is room for improvement
here.

``` r
uga_x6 <- uga_gadm_4 %>%
  filter(str_detect(name_4, "Kamwam")) %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### uga_21

Next up is a 2014 outbreak in Kampala.

<https://www.who.int/emergencies/disease-outbreak-news/item/10-october-2014-marburg-en>
<https://pubmed.ncbi.nlm.nih.gov/28518032/>
<https://wwwnc.cdc.gov/eid/article/23/6/17-0047_article>
<https://academic.oup.com/trstmh/article/109/6/366/2461644>

In particular, the last link points us to the Mawokota district of
Mpigi.

``` r
uga_21 <- uga_gadm_4 %>%
  filter(str_detect(name_2, "Mawokota")) %>%
  select(name_2) %>%
  group_by(name_2) %>%
  summarise(geom = st_union(geom)) %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### uga_40

<https://pubmed.ncbi.nlm.nih.gov/26209681/>

The closest I can get is Ibanda district.

``` r
uga_40 <- uga_gadm_4 %>%
  filter(str_detect(name_2, "Ibanda")) %>%
  select(name_2) %>% 
  group_by(name_2) %>%
  summarise(geom = st_union(geom)) %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### uga_53

This should be one of two spillover events linked to Python cave. One is
an American, the other involved Dutch travelers. This one is for the
Dutch travelers.

<https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2815969/>

In Nyakarahuka, et. al., they published the locations of Marburg cases
but no metadata. So the data are simply lats/longs and a species.

With some deductive reasoning, we can sort some locations out.

``` r
nyakarahuka <- readxl::read_xlsx("./input_data/Marburg/Nyakarahuka/S1.xlsx") %>%
  janitor::clean_names() %>%
  mutate(latitude_subbed = str_replace(latitude, "S", "0")) %>%
  mutate(latitude_clean = as.numeric(latitude_subbed)) %>%
  select(species, latitude = latitude_clean, longitude)
```

``` r
nyakarahuka_marburg <- nyakarahuka %>%
  filter(species=="Marburg case") %>%
  st_as_sf(coords=c("longitude", "latitude"), crs=4326)
```

``` r
hits <- st_join(nyakarahuka_marburg, uga_gadm_4) 
```

There’s two hits within Queen Elizabeth NP territory. Did some
additional sleuthing and pretty confident these represent the Python
Cave. Obviously we want to check it out.

``` r
uga_53 <- hits %>%
  filter(str_detect(name_4, "Queen")) %>%
  select(geometry) %>%
  distinct() %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### uga_59

Based on this article, it sounds like a company called Simba has the
rights for the location of the Kitka mine.

Uganda has its mining cadastral on a Trimble site, which means it’s on
an arcgis rest server, which means we can use arcpullr to extract it.
After some investigating, I figured out this is the url we want:

``` r
uganda_active_mining <- arcpullr::get_spatial_layer("https://miningcadastre.minerals.go.ug/arcgis/rest/services/Uganda_Licenses/MapServer/2") %>%
  janitor::clean_names()
```

    ## Warning in CPL_crs_from_input(x): GDAL Message 1: +init=epsg:XXXX syntax is
    ## deprecated. It might return a CRS with a non-EPSG compliant axis order.

``` r
uga_59 <- uganda_active_mining %>%
  filter(str_detect(parties, "Simba")) %>%
  select(poly_geom = geoms) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### uga_60

We can just repeat the data we extracted earlier from uga_53.

``` r
uga_60 <- hits %>%
  filter(str_detect(name_4, "Queen")) %>%
  select(geometry) %>%
  distinct() %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

## Angola

We only have one known Marburg event in Angola.

### ago_70

According to [this
paper](https://journals.asm.org/doi/10.1128/JVI.00069-06), the index
case was never determined. For now, we’ll use the centroid of the
province where the outbreak occurred.

Let’s get the layers of the Angola GADM data.

``` r
st_layers("./input_data/Angola/GADM/gadm36_AGO.gpkg")
```

    ## Driver: GPKG 
    ## Available layers:
    ##     layer_name geometry_type features fields
    ## 1 gadm36_AGO_0 Multi Polygon        1      2
    ## 2 gadm36_AGO_1 Multi Polygon       18     10
    ## 3 gadm36_AGO_2 Multi Polygon      163     13
    ## 4 gadm36_AGO_3 Multi Polygon      527     16

``` r
angola_gadm <- st_read("./input_data/Angola/GADM/gadm36_AGO.gpkg", layer = "gadm36_AGO_1") %>%
  janitor::clean_names()
```

    ## Reading layer `gadm36_AGO_1' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\Angola\GADM\gadm36_AGO.gpkg' 
    ##   using driver `GPKG'
    ## Simple feature collection with 18 features and 10 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 11.66847 ymin: -18.04208 xmax: 24.08212 ymax: -4.372591
    ## Geodetic CRS:  WGS 84

``` r
uige_province <- angola_gadm %>%
  filter(gid_1 == "AGO.17_1")
```

``` r
ago_70 <- uige_province %>%
  select(poly_geom = geom) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

# Put it all together

Let’s combine the individual records.

``` r
marburg_points <- bind_rows(ago_70, uga_x6, uga_21, uga_40, uga_53, uga_59, uga_60)
```

This command effectively tells R which geometry column is the operative
one.

``` r
st_geometry(marburg_points) <- "point_geom"
```

Export the data.

``` r
marburg_points %>%
  st_write("./testing/marburg_dots.gpkg", append=FALSE)
```

    ## Warning in clean_columns(as.data.frame(obj), factorsAsCharacter): Dropping
    ## column(s) poly_geom of class(es) sfc_GEOMETRY;sfc

    ## Deleting layer `marburg_dots' using driver `GPKG'
    ## Writing layer `marburg_dots' to data source 
    ##   `./testing/marburg_dots.gpkg' using driver `GPKG'
    ## Writing 7 features with 1 fields and geometry type Point.
