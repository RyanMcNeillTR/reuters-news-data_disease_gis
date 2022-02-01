Ebola sites notebook
================

-   [Uganda](#uganda)
    -   [uga_89](#uga_89)
-   [Gabon](#gabon)
    -   [gab_86](#gab_86)
    -   [gab_85](#gab_85)
    -   [gab_82](#gab_82)
-   [Republic of the Congo](#republic-of-the-congo)
    -   [cod_84](#cod_84)
    -   [cod_83](#cod_83)

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

sources:
<https://figshare.com/articles/dataset/Human_index_cases_-_Ebola_virus_disease/3423593?backTo=/collections/eLife_Advances_-_Updates_to_the_zoonotic_niche_map_of_Ebola_virus_disease_in_Africa/3267965>

``` r
mylne_polys <- st_read("./input_data/Ebola/Mylne/09EboladatapaperGISpolygonshapefile/historical_spread_polygons.shp")
```

    ## Reading layer `historical_spread_polygons' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\Ebola\Mylne\09EboladatapaperGISpolygonshapefile\historical_spread_polygons.shp' 
    ##   using driver `ESRI Shapefile'
    ## Simple feature collection with 54 features and 11 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: -7.363078 ymin: -26.52629 xmax: 33.37899 ymax: 5.909633
    ## Geodetic CRS:  WGS 84

``` r
mylne_points <- st_read("./input_data/Ebola/Mylne/08EboladatapaperGISpointsshapefile/historical_spread_points.shp") 
```

    ## Reading layer `historical_spread_points' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\Ebola\Mylne\08EboladatapaperGISpointsshapefile\historical_spread_points.shp' 
    ##   using driver `ESRI Shapefile'
    ## Simple feature collection with 63 features and 12 fields
    ## Geometry type: POINT
    ## Dimension:     XY
    ## Bounding box:  xmin: 11.88333 ymin: -5.274989 xmax: 32.73669 ymax: 15.64228
    ## Geodetic CRS:  WGS 84

## Uganda

### uga_89

Excluding because not enough preceding data.

## Gabon

Load the Gabon GADM.

``` r
st_layers("./input_data/Gabon/gadm36_GAB_gpkg/gadm36_GAB.gpkg")
```

    ## Driver: GPKG 
    ## Available layers:
    ##     layer_name geometry_type features fields
    ## 1 gadm36_GAB_0 Multi Polygon        1      2
    ## 2 gadm36_GAB_1 Multi Polygon        9     10
    ## 3 gadm36_GAB_2 Multi Polygon       37     13

``` r
gadm36_GAB_2 <- st_read("./input_data/Gabon/gadm36_GAB_gpkg/gadm36_GAB.gpkg", layer = "gadm36_GAB_2")
```

    ## Reading layer `gadm36_GAB_2' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\Gabon\gadm36_GAB_gpkg\gadm36_GAB.gpkg' 
    ##   using driver `GPKG'
    ## Simple feature collection with 37 features and 13 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 8.699028 ymin: -3.990695 xmax: 14.50235 ymax: 2.315645
    ## Geodetic CRS:  WGS 84

### gab_86

This is a 2001 outbreak in Gabon. The Metabiota data cites it as
Etakangaye.

The MetaBiota data does not have coordinates, but Shapiro cites Mylne
for the location.

We can reference [Mylne’s data
here](https://doi.org/10.6084/m9.figshare.1168886).

From the data notes:

“GE and the digitized article map were used to geo-ref. Because we are
not 100% sure where the village is, a polygon needs to be drawn around
the excel coords. (1.07168, 14.10658)”

We extract the polygon and add the centroid.

``` r
gab_86_poly <- mylne_polys %>%
  filter(uniq_id == 164) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### gab_85

Another 2001 outbreak in Gabon, this time in Ekata.

From the Mylne notes:

“Digitized from Pourrut 2005.” The link to [that paper is
here](https://www.sciencedirect.com/science/article/abs/pii/S1286457905001437?via%3Dihub).

Appears to be an exact site. Have asked Allison for Pourrut paper and
will update when I know more.

0.67705 14.28902

``` r
gab_85_point <- mylne_points %>%
  filter(uniq_id == 162) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### gab_82

One last 2001 outbreak in Gabon, this time in Memdemba.

From the Mylne notes:

“Pourrut 2005 used to locate actual site of index case.”

``` r
gab_82_point <- mylne_points %>%
  filter(uniq_id == 160) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

## Republic of the Congo

### cod_84

This is a 2001 outbreak in the Republic of the Congo. Metabiota
identified it as Entsiami, Abolo, Ambomi.

From the Mylne dataset:

“Entsiami’ digitized from Pourrut 2005 and a 5km radius used to include
the neibouring villages of Abolo & Ambomi (0.09141, 14.21818)”

``` r
cod_84_poly <- mylne_polys %>%
  filter(uniq_id == 161) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cod_83

This is another 2001 outbreak in the Republic of the Congo. Metabiota
listed it as Oloba, Mbomo district.

From the Mylne dataset: “Pourrut 2005 used to locate actual site of
index case.”

``` r
cod_83_point <- mylne_points %>%
  filter(uniq_id == 163) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```
