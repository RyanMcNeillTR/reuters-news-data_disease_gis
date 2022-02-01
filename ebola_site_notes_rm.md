Ebola sites notebook
================

-   [Uganda](#uganda)
    -   [uga_89](#uga_89)
-   [Gabon](#gabon)
    -   [gab_86](#gab_86)
    -   [gab_85](#gab_85)
    -   [gab_82](#gab_82)
    -   [gab_78](#gab_78)
-   [Republic of the Congo](#republic-of-the-congo)
    -   [cog_84](#cog_84)
    -   [cog_83](#cog_83)
    -   [cog_80](#cog_80)
    -   [cog_79](#cog_79)
    -   [cog_75](#cog_75)
    -   [cog_74](#cog_74)
    -   [cog_67](#cog_67)
-   [South Sudan](#south-sudan)
    -   [ssd_69](#ssd_69)
-   [Uganda](#uganda-1)
    -   [uga_58](#uga_58)
    -   [uga_43](#uga_43)
    -   [uga_39](#uga_39)
    -   [uga_38](#uga_38)
-   [Democratic Republic of the
    Congo](#democratic-republic-of-the-congo)
    -   [cod_57](#cod_57)
    -   [cod_52](#cod_52)
    -   [cod_37](#cod_37)
    -   [cod_12](#cod_12)
    -   [cod_10](#cod_10)
    -   [cod_9](#cod_9)
    -   [cod_4](#cod_4)
    -   [cod_1](#cod_1)
-   [Guinea](#guinea)
    -   [gin_23](#gin_23)

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

### gab_78

A 2002 outbreak in Gabon. Site: Grand Etoumbi.

Mylne notes: “GE and the digitized article map were used to geo-ref.
Because we are not 100% sure where the village is, a polygon needs to be
drawn around the excel coords. (1.30411, 14.17743)”

From Pigott notes:

The original paper gave the name of the villages and GE and the article
map were used to geo-ref. Ref 100 also states the ‘apparent origin’ and
give coords within the paper. Article 100 lat & long checked and
confirmed to be near Ekata (3.5km north of the GE marker for Ekata). Ref
100 also states that, ‘Occurrences of unknown origin were excluded from
analysis, but when reasonable guesses could be made as to point or
general area of origin they were included. Although the geographic
coordinates may not fix the exposure point precisely, they represent the
review paper’s best guess as to its position and are likely to be
representative of the coarse-scale ecologic conditions’.

``` r
gab_78_poly <- mylne_polys %>%
  filter(uniq_id == 165) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

## Republic of the Congo

### cog_84

This is a 2001 outbreak in the Republic of the Congo. Metabiota
identified it as Entsiami, Abolo, Ambomi.

From the Mylne dataset:

“Entsiami’ digitized from Pourrut 2005 and a 5km radius used to include
the neibouring villages of Abolo & Ambomi (0.09141, 14.21818)”

``` r
cog_84_poly <- mylne_polys %>%
  filter(uniq_id == 161) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cog_83

This is another 2001 outbreak in the Republic of the Congo. Metabiota
listed it as Oloba, Mbomo district.

From the Mylne dataset: “Pourrut 2005 used to locate actual site of
index case.”

``` r
cog_83_point <- mylne_points %>%
  filter(uniq_id == 163) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### cog_80

This is a 2002 outbreak in Republic of the Congo. Sites according to
Metabiota: Site 2 (KÈllÈ district, Cuvette Ouest)

Mylne notes:

“The 2rd index case location was not reported but we are confident it is
around the Lossi Animal reserve. A polygon was drawn around the excel
coords to represent the potential hunting range (0.14963 14.43731)”

``` r
cog_80_poly <- mylne_polys %>%
  filter(uniq_id == 174) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cog_79

Another 2002 outbreak in ROC. Sites: Yembelangoye village (KÈllÈ
district, Cuvette Ouest)

Mylne notes:

No notes included.

Metabiota, sourced to Pigott dataset:

“The outbreak occurred in the districts of Mbomo (0.435209∞, 14.845462∞)
and KÈllÈ (-0.066667∞, 14.500000∞) in Cuvette Ouest DÈpartement’.
Accorinding to Ref 130, ‘The first index case was probably infected
during a hunt near Yembelangoye village on 21 December 2002’. The excel
coords represent the GE marker for Yembelangoye village which is in the
correct area as described by Ref 130.”

``` r
cog_79_point <- mylne_points %>%
  filter(uniq_id == 173) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### cog_75

A 2003 outbreak in ROC. Sites: Mbandza forest near village (Mbomo
district, Cuvette Ouest)

Mylne notes:

“7.5 radium from ‘Mbandza, Cuvette Ouest, Congo’”

Pigott notes:

Coords for ‘Mbandza, Cuvette Ouest, Congo’ were produced in GE and
recorded. Information regarding the index case came from Mbandza
according to Ref 129

``` r
cog_75_poly <- mylne_polys %>%
  filter(uniq_id == 180) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cog_74

Another 2003 outbreak in the ROC, this time in Mvoula camp (KÈllÈ
district, Cuvette Ouest).

Mylne notes:

“The 3rd index case came from the Mvoula camp. Location digitized from
Pourrut 2005.”

``` r
cog_74_point <- mylne_points %>%
  filter(uniq_id == 175) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### cog_67

This is a 2005 outbreak in the ROC. Site: Odzala National Park (Etoumbi)

Mylne notes:

It appears the first zoonotic infection was on a hunting trip into the
Parc d’Odzala. The excel coords represent the centroid of the park,
however it is our belief that the park is also made up of the ‘Reserve
de Faune de la Lekoli-Pandaka’ and the ’Domd

Pigott notes:

It appears the first zoonotic infection was on a hunting trip into the
Parc d’Odzala. The excel coords represent the centroid of the park,
however it is my belief that the park is also made up of the ‘Reserve de
Faune de la Lekoli-Pandaka’ and the ‘Domdine de Chasse de Mboko’ parks
which should also be incluced in the polygon boundry.

``` r
cog_67_poly <- mylne_polys %>%
  filter(uniq_id == 186) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

## South Sudan

### ssd_69

This is a 2004 outbreak in South Sudan, documentred at: Forest 40km
South of Yambio town.

Mylne notes:

The search term, ‘Yambio, South Sudan’ was used in GE and then a forest
in DRC which was 40km away from Yambio was geo-referenced. A polygon was
drawn around this forest area (4.43149 28.7054)

``` r
ssd_69_poly <- mylne_polys %>%
  filter(uniq_id == 183) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

## Uganda

### uga_58

This is a 2007 outbreak in Uganda. Site: Kabango village (Kasitu
subcounty, Bundibugyo district) (index)

Mylne note:

‘The putative index patient was a 26-year-old woman from Kabango
village, Kasitu subcounty, in Bundibugyo district’. GE search term
‘Kabango Senior Secondary School, Uganda’ was used to then find the
town’s coords across the road. The school appears to

(cuts off)

Pigott note:

According to Ref 115, ‘The putative index patient was a 26-year-old
woman from Kabango village, Kasitu subcounty, in Bundibugyo district’.
GE search term ‘Kabango Senior Secondary School, Uganda’ was used to
then find the town’s coords across the road. The school appears to be
within 1km of the village and therfore a point was chosen.

``` r
uga_58_point <- mylne_points %>%
  filter(uniq_id == 195) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### uga_43

Thius is a 2011 outbreak in Uganda. Site: Nakisamata village (Luwero
District)

Mylne note:

UNAble to produce point coords for NAkisamata village as the location
could not be found in GE or GMs. However using the article map to
digitize we were able to estimated the village tlocation

Pigott note:

Unable to produce point coords for Nakisamata village as the location
could not be found in GE or GMs. However using the article map (Ref 109
\[Fig 1\]) we estimated the village to be within 10km from ‘Busika,
Uganda’ and used coords from ‘Busika’ instead. Therefore a polygon
around Busika needs to be drawn to reflect the index case location.
Please see Ref 109 (Fig 1) for more information.

``` r
uga_43_point <- mylne_points %>%
  filter(uniq_id == 207) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### uga_39

This is a 2012 outbreak. Site: Luwero district (index)

Mylne note:

GMs border for Luwero district
(<https://www.google.co.uk/maps/place/Luwero,+Uganda/@0.8720966,32.5649079,11z/data=!3m1!4b1!4m2!3m1!1s0x177b6b8cdd242317:0xfe231cc175c61eb9?hl=en>)

Pigott note:

No geographical information on index case. GE search term ‘Luwero
District, Uganda’ to generate centroid coords. A polygon needs to be
drawn matching the the geographic limits of ‘ Luwero District, Uganda’.

``` r
uga_39_point <- mylne_polys %>%
  filter(uniq_id == 214) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### uga_38

This is another 2012 outbreak in Uganda. Site: Nyanswiga village,
Nyamarunda, Buyanja (Kibaale District) (index)

Mylne note:

Use the following area definition for Nyamarunda sub-county:
<http://www.ikimap.com/map/administrative-area-nyamarunda-uganda>

Pigott note:

No geographical information on index case. GE search term ‘Kibale
district, Uganda’ to generate cenrtroid coords. A polygon need to be
drawn matching the the geographic limits of ‘Kibale district, Uganda’

``` r
uga_38_poly <- mylne_polys %>%
  filter(uniq_id == 211) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

## Democratic Republic of the Congo

We’ll mainly use the Mylne data. But loading the health zone boundaries
here.

``` r
drc_health_zones <- st_read("./input_data/DRC/rdc_zone_de_sante_09092019/RDC_Zone_de_sante_09092019.shp")
```

    ## Reading layer `RDC_Zone_de_sante_09092019' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\DRC\rdc_zone_de_sante_09092019\RDC_Zone_de_sante_09092019.shp' 
    ##   using driver `ESRI Shapefile'
    ## Simple feature collection with 519 features and 20 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 12.20566 ymin: -13.456 xmax: 31.30522 ymax: 5.386098
    ## Geodetic CRS:  WGS 84

And health areas.

``` r
drc_health_areas <- st_read("./input_data/DRC/rdc_aires-de-sante/RDC_Aires de santé.shp")
```

    ## Reading layer `RDC_Aires de santÃ©' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\DRC\rdc_aires-de-sante\RDC_Aires de santÃ©.shp' 
    ##   using driver `ESRI Shapefile'
    ## Simple feature collection with 6773 features and 19 fields (with 3 geometries empty)
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 12.19965 ymin: -13.456 xmax: 31.27671 ymax: 5.386098
    ## Geodetic CRS:  WGS 84

Also load GADM.

``` r
drc_admin_3 <- st_read("./input_data/DRC/gadm36_COD_gpkg/gadm36_COD.gpkg", layer = "gadm36_COD_2")
```

    ## Reading layer `gadm36_COD_2' from data source 
    ##   `C:\Users\u0161776\OneDrive - Thomson Reuters Incorporated\new_projects\r_work\reuters-news-data_disease_gis\input_data\DRC\gadm36_COD_gpkg\gadm36_COD.gpkg' 
    ##   using driver `GPKG'
    ## Simple feature collection with 240 features and 13 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 12.20663 ymin: -13.45568 xmax: 31.30572 ymax: 5.386098
    ## Geodetic CRS:  WGS 84

### cod_57

This is a 2007 outbreak in the DRC. Location: Bamoukamba 2 (index)

Mylne note:

The index case came from Bamoukamba 2 whereby a man came into direct
contact with the blood of the bats he had bought from the Mombo Mounene
2 market reported having mild symptoms (mainly fever and headache)
sometimes during this period. This man was the

(cuts off)

Pigott note:

According to Ref 131 the index case came from Ndongo 2 village howewer a
man came into direct contact with the blood of the bats he had bought
from the Mombo Mounene 2 market reported having mild symptoms (mainly
fever and headache) sometimes during this period. This man was the
father of a 4-year-old girl (patient B), who suddenly fell ill on 12
June and died on 16 June 2007. ‘Mombo Mounene 2 market’ was not stored
in GMs or GE. Therefore we used GE and the article map to decide where
the market was. We picked a location just after the bend in the road.
For accuracy a polygon need to be drawn with a radius of 3km from the
excel coords.

``` r
cod_57_point <- mylne_points %>%
  filter(uniq_id == 189) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "exact")
```

### cod_52

This is a 2008 outbreak in the DRC. Site: Luebo health zone (index)

Mylne note:

IKI border for Luebo
(<http://www.ikimap.com/map/administrative-area-luebo-democratic-republic-congo>)

Pigott note:

The paper says the index case came from Luebo (-5.350000°,
21.416667°)health zones of the Province of Kasai Occidental .Central
coords produced by typing ‘\[health zone\],Kasai Occidental, DRC’ into
GE. They are polygons because the information is not related to the
index case but the outbreak in general. Therefore a polygon needs to be
drawn matching the the geographic limits of Luebo.

``` r
cod_52_poly <- mylne_polys %>%
  filter(uniq_id == 204) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cod_37

This is a 2012 outbreak iin the DRC. Site: Isiro health zone (index)

Mylne note:

Digitized from WHO WER maps

Pigott note:

No geographical information on index case. GE search term ‘Province
Orientale, DRC’ to generate centroid coords. A polygon needs to be drawn
matching the the geographic limits of ‘Province Orientale, DRC’.

``` r
cod_37_poly <- mylne_polys %>%
  filter(uniq_id == 208) %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cod_12

This is a 2017 DRC outbreak. Site: Likati health zone of the province of
Bas Uélé

COME BACK TO THIS ONE. ZAVIS ASKED TWO OF HER PEOPLE TO HELP.

### cod_10

This is a 2018 outbreak at Mangina health area, North Kivu province.

0.57 29.32

According to [this
paper](https://www.sciencedirect.com/science/article/pii/S2590088920300020):

“Neither the primary source nor the index case for the outbreak have
been established, but it is assumed that eating of bush meat and local
family and community spread, through contact with symptomatic cases and
traditional funerals, occurred before the first reported and diagnosed
cases in Mabalako Health Zone of North Kivu province in late July 2018.
Geographically, the outbreak spread from the initial area, southwards to
the cities of Beni and Butembo, and to Katwa and Kanya further south,
and further cases were reported to the north in Ituri province \[18\].
While locations such as Beni and Butembo are “cities” from the size of
their populations, large parts of them are sprawling, crowded areas with
poor housing and infrastructure, high risk factors for urban
transmission.”

So far, we have only been able to source it to this polygon of the
Mabalako health zone.

``` r
cod_10_poly <- drc_health_zones %>%
  filter(Nom == "Mabalako") %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cod_9

This is another 2018 outbreak in Bikoro region of Equateur Province.

According to [this Washington Post
story](https://www.washingtonpost.com/world/first-confirmed-urban-ebola-case-is-a-game-changer-in-congo/2018/05/17/430babce-5890-11e8-9889-07bcc1327f4b_story.html)

“The International Federation of the Red Cross and Red Crescent
Societies (IFRC) said the first suspected victim was a police officer
who died in a health center in the village of Ikoki-Impenge, near
Bikoro. After his funeral, 11 family members got sick and seven died.
All seven had attended the funeral or cared for the man while he was
sick.”

According to [this
paper](https://www.thelancet.com/action/showPdf?pii=S0140-6736%2818%2931387-4),
the epicenter is Ikoko Impenge Health Area.

HOWEVER, IT APPEARS AUTHOR MAY KNOW SPECIFIC VILLAGE. FOLLOW UP.

``` r
cod_9_poly <- drc_health_areas %>%
  filter(AS_ == "IKOKO IMPENGE") %>%
  select(poly_geom = geometry) %>%
  mutate(point_geom = st_centroid(poly_geom),
         precision = "centroid")
```

### cod_4

This is a 2020 outbreak in Mbandaka, Equateur Province.

According to [this briefing
document](https://reliefweb.int/report/democratic-republic-congo/acaps-briefing-note-drc-equateur-ebola-virus-disease-08-june-2020):

“On 31 May, Democratic Republic of Congo (DRC) declared its 11th Ebola
Virus Disease (EVD) outbreak (on record), in Wangata health zone, in the
southern part of Mbandaka city, capital of Equateur province (Social
Science in Humanitarian Action 07/2018; EU CDC 02/06/2020; WHO
01/06/2020) and in the city’s surrounding area, and Bikoro.”

and

“The index case is a 27-year-old woman; she, alongside other deaths, is
believed to have come from the Air Congo Quarter in Mbandaka (WHO
02/06/2020).”

[Google seems to have a map of the Air Zaire
quartier](https://www.google.com/maps/place/Air+Za%C3%AFre,+Mbandaka,+Democratic+Republic+of+the+Congo/@0.0305882,18.2712383,15z/data=!3m1!4b1!4m5!3m4!1s0x10a75856af7d1809:0x1fa4138d81033dcc!8m2!3d0.032113!4d18.2807559),
but I am not sure the source. I manually picked the middle.

``` r
cod_4_point <- tribble(
  ~latitude, ~longitude,
  0.03146801405368925, 18.2797784294778
) %>%
  st_as_sf(coords=c("longitude", "latitude"), crs=4326) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "manual village siting")
```

### cod_1

NOT INCLUDING THIS ONE.

[According to
CDC](https://www.cdc.gov/vhf/ebola/outbreaks/drc/2021-oct.html):

“Sequencing data from the first confirmed case in this outbreak showed a
link to the 2018-2020 outbreak in the same region. This link suggests
that this outbreak was likely caused by a persistent infection in an EVD
survivor that led to either a relapse or sexual transmission of the
virus.”

## Guinea

### gin_23

In past, the site of the index case has been plotted at Gueckendou.
However, it is not believed that the index case was a boy [from the
village of
Meliandou](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4309665/). That
is where we’ll plot this.

``` r
gin_23_point <- tribble(
  ~latitude, ~longitude,
  8.622496517267711, -10.064200678863259
) %>%
  st_as_sf(coords=c("longitude", "latitude"), crs=4326) %>%
  select(point_geom = geometry) %>%
  mutate(precision = "manual village siting")
```

<https://academic.oup.com/jid/article/221/5/701/5426903>
