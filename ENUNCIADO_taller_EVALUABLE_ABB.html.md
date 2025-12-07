---
title: "ENUNCIADO taller en grupo Mat3 GIN 2025-2026"
author: "Taller"
lang: es
format:
  html:
    theme: superhero
    toc: true
    toc_depth: 4
    html-math-method: katex
    code-tools: true
    code-fold: true
    collapse: true
    keep-md: true
    code-overflow: wrap
---




# Instrucciones para el taller

Se entrega en grupos que deben de estar constituidos en la actividad de grupos. Los grupos son de 2 o 3 ESTUDIANTES, loa caso especiales consultadlos con el profesor para que los autorice.

**Enlaces y Bibliografía**

-   [R for data science, Hadley Wickham, Garret Grolemund.](https://r4ds.had.co.nz/)
-   [Fundamentos de ciencia de datos con R.](https://cdr-book.github.io/)
-   [Tablas avanzadas: kable, KableExtra.](https://haozhu233.github.io/kableExtra/awesome_table_in_html.html)
-   [Geocomputation with R, Robin Lovelace, Jakub Nowosad, Jannes Muenchow](https://r.geocompx.org/)
-   Apuntes de R-basico y tidyverse moodel MAT3.

## Objetivo MALLORCA

Leeremos los siguientes datos de la zona de etiqueta `mallorca` con el código siguiente:


::: {.cell}

```{.r .cell-code}
load("clean_data/mallorca/listing_common0.RData")
ls()
```

::: {.cell-output .cell-output-stdout}

```
[1] "listings_common0"
```


:::

```{.r .cell-code}
listings0 = listings_common0 %>%
  select(id, scrape_id, listing_url,
         neighbourhood_cleansed, price,
         number_of_reviews,
         review_scores_rating,
         review_scores_rating,
         review_scores_cleanliness,
         review_scores_location,
         review_scores_value,
         number_of_reviews,
         accommodates,
         bathrooms_text,
         bedrooms,
         beds,
         minimum_nights,
         description,
         latitude,
         longitude,
         property_type,
         room_type)
```
:::



**listings**

Generamos la  tibble `listings0` con datos DE  8 periodos  DE  apartamentos de inside Airbnb de Mallorca Y  seleccionando cuantas variables nos parecen más interesantes.

Separararemos la fecha del scrapping que es en la que se observaron  los datos de cada apartamento  nos quedaremos con los apartamentos que aparecen en las 8 periodos "scrapeados".


::: {.cell}

```{.r .cell-code}
listings0= listings0 %>% 
  mutate(date=as.Date(substr(
    as.character(scrape_id),1,8),
    format="%Y%m%d"),
    .after=id)
```
:::


Ahora  analizamos  las fechas de los scrapings y el número de veces que aparecen 
 cada  apartamentos.




::: {.cell}

```{.r .cell-code}
table(listings0$date)
```

::: {.cell-output .cell-output-stdout}

```

2023-12-17 2024-03-23 2024-06-19 2024-09-13 2024-12-14 2025-03-07 2025-06-15 
      9197       9197       9197       9197       9197       9197       9197 
2025-09-21 
      9197 
```


:::
:::

Hay 8 periodos de scrapping y vamos a quedarnos con los apartamentos que aparecen en todos los periodos


Vemos que cada apartamento aparece 8 veces una por periodo.



::: {.cell}

```{.r .cell-code}
table(table(listings0$id))
```

::: {.cell-output .cell-output-stdout}

```

   8 
9197 
```


:::
:::




Notemos que cada apartamento:

-   queda identificado por id y por date que nos da el periodo en la que apareció el dato.
-   así que cada apartamento aparece 8 veces ya que hemos elegido solo los apartamentos que aparecen en las 8 muestras.
-   Las muestras son 2023-12-17, 2024-03-23, 2024-06-19, 2024-09-13, 2024-12-14, 2025-03-07, 2025-06-15, 2025-09-21,


::: {.cell}

```{.r .cell-code}
unique(listings0$date)
```

::: {.cell-output .cell-output-stdout}

```
[1] "2023-12-17" "2024-03-23" "2024-06-19" "2024-09-13" "2024-12-14"
[6] "2025-03-07" "2025-06-15" "2025-09-21"
```


:::
:::


**reviews**

Estos datos necesitan leerse de forma adecuada, las columnas 1, 2 y 4 deben ser de tipo `character` las otras son correctas


::: {.cell}

```{.r .cell-code}
reviews=read_csv("data/mallorca/2025-09-21/reviews.csv.gz")
str(reviews)
```

::: {.cell-output .cell-output-stdout}

```
spc_tbl_ [398,782 × 6] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ listing_id   : num [1:398782] 69998 69998 69998 69998 69998 ...
 $ id           : num [1:398782] 881474 4007103 4170371 4408459 4485779 ...
 $ date         : Date[1:398782], format: "2012-01-24" "2013-04-02" ...
 $ reviewer_id  : num [1:398782] 1595616 3868130 5730759 5921885 810469 ...
 $ reviewer_name: chr [1:398782] "Jean-Pierre" "Jo And Mike" "Elizabeth" "Jone" ...
 $ comments     : chr [1:398782] "This place was charming! Lorenzo himself is a very warm and engaging host and made us feel very welcome. \r<br/"| __truncated__ "We had a four night stay at this gorgeous apartment and it was absolutely perfect. It's really pretty, beautifu"| __truncated__ "Lor's apartment looks exactly like the pictures! It is perfectly located for historic Palma - close to the Cath"| __truncated__ "Wonderful place! 10/10. Charming, spacious and comfortable. Looks even more splendid than in the pictures. The "| __truncated__ ...
 - attr(*, "spec")=
  .. cols(
  ..   listing_id = col_double(),
  ..   id = col_double(),
  ..   date = col_date(format = ""),
  ..   reviewer_id = col_double(),
  ..   reviewer_name = col_character(),
  ..   comments = col_character()
  .. )
 - attr(*, "problems")=<externalptr> 
```


:::

```{.r .cell-code}
head(reviews)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 6
  listing_id      id date       reviewer_id reviewer_name comments              
       <dbl>   <dbl> <date>           <dbl> <chr>         <chr>                 
1      69998  881474 2012-01-24     1595616 Jean-Pierre   "This place was charm…
2      69998 4007103 2013-04-02     3868130 Jo And Mike   "We had a four night …
3      69998 4170371 2013-04-15     5730759 Elizabeth     "Lor's apartment look…
4      69998 4408459 2013-05-03     5921885 Jone          "Wonderful place! 10/…
5      69998 4485779 2013-05-07      810469 Andrea        "My boyfriend and I, …
6      69998 4619699 2013-05-15     3318059 Devii         "We had a very last m…
```


:::
:::


**neighbourhoods.csv**

Son dos columnas y la primera es una agrupación de municipios (están NA) y la segunda es el nombre del municipio


::: {.cell}

```{.r .cell-code}
municipios=read_csv("data/mallorca/2025-09-21/neighbourhoods.csv")
str(municipios)
```

::: {.cell-output .cell-output-stdout}

```
spc_tbl_ [53 × 2] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ neighbourhood_group: logi [1:53] NA NA NA NA NA NA ...
 $ neighbourhood      : chr [1:53] "Alaró" "Alcúdia" "Algaida" "Andratx" ...
 - attr(*, "spec")=
  .. cols(
  ..   neighbourhood_group = col_logical(),
  ..   neighbourhood = col_character()
  .. )
 - attr(*, "problems")=<externalptr> 
```


:::

```{.r .cell-code}
head(municipios)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 2
  neighbourhood_group neighbourhood
  <lgl>               <chr>        
1 NA                  Alaró        
2 NA                  Alcúdia      
3 NA                  Algaida      
4 NA                  Andratx      
5 NA                  Ariany       
6 NA                  Artà         
```


:::
:::


**neighbourhoods.geojson**

Es el mapa de Mallorca, o podemos leer así:


::: {.cell}

```{.r .cell-code}
library(sf)
library(tmap)

# Leer el archivo GeoJSON
geojson_sf <- sf::st_read("data/mallorca/2025-09-21/neighbourhoods.geojson")
```

::: {.cell-output .cell-output-stdout}

```
Reading layer `neighbourhoods' from data source 
  `D:\UIB\Segundo\Estadistica\tallerMat3_25_26\data\mallorca\2025-09-21\neighbourhoods.geojson' 
  using driver `GeoJSON'
Simple feature collection with 53 features and 2 fields
Geometry type: MULTIPOLYGON
Dimension:     XY
Bounding box:  xmin: 2.303195 ymin: 39.26403 xmax: 3.479028 ymax: 39.96236
Geodetic CRS:  WGS 84
```


:::

```{.r .cell-code}
# Crear un mapa

# interactivo
tmap_mode("plot") # Cambiar a modo  view/plot   que es interactivo/estático
tm_shape(geojson_sf) +
  tm_polygons(col = "cyan", alpha = 0.6) +
  tm_layout(title = "Mapa - GeoJSON Mallorca con municipios")
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-8-1.png){width=672}
:::
:::


Tenéis que consultar en la documentación de inside Airbnb para saber que significa cada variable. Os puede ser útil leer los ficheros [DATA_ABB_modelo_de_datos.html](DATA_ABB_modelo_de_datos.html) y [DATA_ABB_modelo_de_datos.pdf](DATA_ABB_modelo_de_datos.html) en los que se explica el modelo de datos de inside Airbnb y como se cargan en el espacio de trabajo.

Responder las siguientes preguntas con formato Rmarkdown (.Rmd) o quarto (.qmd) y entregad la fuente un fichero en formato html como salida del informe. Se puntúa la claridad de la respuesta, la calidad de la redacción y la corrección de la respuesta.

## Pregunta 1 (**1punto**)

Del fichero con los datos de listings `listings0` calcula los estadísticos descriptivos de las variable `price` y de la variable `number_of_reviews` agrupados por municipio y por periodo.

Presenta los resultados con una tabla de kableExtra.

### Solución Pregunta 1

::: {.cell}

```{.r .cell-code}
# Librerias
library(dplyr)
library(kableExtra)

# Calculamos las estadísticas descriptivas por municipio y periodo
res_p1 <- listings0 %>%
  group_by(neighbourhood_cleansed, date) %>%
  summarise(
    mean_price    = mean(price, na.rm = TRUE),
    sd_price      = sd(price, na.rm = TRUE),
    min_price     = min(price, na.rm = TRUE),
    max_price     = max(price, na.rm = TRUE),

    mean_reviews  = mean(number_of_reviews, na.rm = TRUE),
    sd_reviews    = sd(number_of_reviews, na.rm = TRUE),
    min_reviews   = min(number_of_reviews, na.rm = TRUE),
    max_reviews   = max(number_of_reviews, na.rm = TRUE),
    .groups = "drop"
  ) %>%
  arrange(neighbourhood_cleansed, date)

# Creamos una tabla de estilo blanco + gris suave + texto negro
res_p1 %>%
  kbl(
    caption = "Estadistica descriptiva de 'price' y 'number_of_reviews' por municipio y periodo",
    digits = 2,
    align = "lcccccccc",
    booktabs = TRUE
  ) %>%
  kable_styling(
    full_width = TRUE,
    bootstrap_options = c("hover"),
    position = "center",
    font_size = 14
  ) %>%
  row_spec(0, bold = TRUE, background = "#FFFFFF", color = "black") %>%         # Encabezado
  row_spec(1:nrow(res_p1), background = c("#FFFFFF", "#F5F5F5"), color = "black") %>% # Filas alternas
  scroll_box(height = "500px", width = "100%")
```

::: {.cell-output-display}
`````{=html}
<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:500px; overflow-x: scroll; width:100%; "><table class="table table-hover" style="font-size: 14px; margin-left: auto; margin-right: auto;">
<caption style="font-size: initial !important;">Estadistica descriptiva de 'price' y 'number_of_reviews' por municipio y periodo</caption>
 <thead>
  <tr>
   <th style="text-align:left;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> neighbourhood_cleansed </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> date </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> mean_price </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> sd_price </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> min_price </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> max_price </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> mean_reviews </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> sd_reviews </th>
   <th style="text-align:center;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> min_reviews </th>
   <th style="text-align:left;font-weight: bold;color: black !important;background-color: rgba(255, 255, 255, 255) !important;position: sticky; top:0; background-color: #FFFFFF;"> max_reviews </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 425.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 884.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 5000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 433 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 400.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 771.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 5000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 457 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 439.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 831.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 5000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 71.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 478 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 599.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1496.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 503 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 567.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1487.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 78.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 524 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 537.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1321.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 541 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 724.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1795.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 561 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alaró </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 851.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2020.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 572 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 210.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 164.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1758 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 348 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 170.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1758 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 349 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 272.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 196.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1658 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 408 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 284.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 485.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 419 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 583.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 431 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 753.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2151.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 431 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 909.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2309.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 455 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Alcúdia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 816.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2158.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10053 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 474 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 279.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 402.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3125 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 183 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 255.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 261.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1994 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 188 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 313.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 397.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 108 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3085 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 201 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 310.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 403.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 108 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3125 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 207 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 300.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 301.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2188 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 213 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1372.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3014.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 218 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1536.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3190.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 231 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Algaida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1380.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2952.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 238 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 586.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1058.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 6711 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 361 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 584.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 933.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4756 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 366 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 752.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1253.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 8403 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 377 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 795.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1415.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 387 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 693.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1350.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 392 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 930.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1866.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 398 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1020.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1844.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 415 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Andratx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1120.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2070.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 61.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 423 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 218.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 154.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 689 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 8.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 11.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 211.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 150.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 675 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 8.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 11.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 256.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 195.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 987 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 179.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 987 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 11.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 222.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 122.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 579 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 73 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2705.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4159.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 73 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3069.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4268.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 76 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ariany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2861.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4136.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 244.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 226.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1590 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 231 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 248.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 230.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1610 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 234 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 247.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 132.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1036 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 750.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 256 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 291.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 757.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 267 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1611.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3306.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 273 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2329.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3843.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10018 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 285 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Artà </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1954.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3570.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10016 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 294 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 213.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 161.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 871 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 84.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 312 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 219.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 168.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 66.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 325 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 258.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 233.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 327 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 257.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 875 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 333 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 244.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 206.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1159 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 78.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 96.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 340 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 666.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2033.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 99.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 345 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 660.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1849.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 102.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 352 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Banyalbufar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 647.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1849.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 91.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 107.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 368 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 194.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 977 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 167 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 264.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 193.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 963 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 281.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 198.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 963 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 174 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 294.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 221.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1277 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 180 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 281.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 256.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1350 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 969.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2428.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2372.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9143 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 190 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Binissalem </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1124.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2600.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 195 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 288.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 230.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1257 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 197 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 223.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1300 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 202 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 354.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 269.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1400 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 215 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 350.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 269.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1400 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 225 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 316.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 271.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1400 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 236 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 752.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1832.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 239 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 786.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1845.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 57.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 247 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Bunyola </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 769.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1823.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 255 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 254.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 146.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 772 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 144 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 252.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 693 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 11.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 297.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 171.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 861 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 11.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 151 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 305.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 182.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 861 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 161 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 283.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 168.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1037 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 166 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 851.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2223.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 166 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 718.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1838.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Búger </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 784.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 182 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 462.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 600.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3700 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 91.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 974 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 448.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 543.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3125 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 96.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1025 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 508.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 490.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3146 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1174 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 510.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 533.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3305 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 122.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1311 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 451.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 510.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3125 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 129.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1355 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 740.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1731.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1364 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 791.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1538.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 139.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1403 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Calvià </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 833.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1760.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 146.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1461 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 195.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 123.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1032 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 160 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 200.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 154.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1483 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 163 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 244.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 207.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2088 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 170 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 240.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 204.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1993 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 177 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 220.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1079 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1423.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3129.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1365.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2983.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 111 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 206 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campanet </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1299.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2918.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 223 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 263.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2171 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 137 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 260.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 216.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1650 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 141 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 293.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 230.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2150 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 153 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 322.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 599.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 165 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 323.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 609.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1171.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2777.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 179 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1455.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3029.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 180 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Campos </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1346.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2927.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 188 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 222.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 114 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 224.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 257.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 119 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 282.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 126 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 262.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 287.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 241.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 289.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 135 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2503.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3937.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10013 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 135 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2879.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4080.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10013 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 143 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Capdepera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2850.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4079.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 150 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 253.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 135.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 516 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 105 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 253.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 125.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 490 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 106 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 288.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 518 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 111 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 260.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 498 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 115 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 252.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 600 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 122 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1622.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3695.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 122 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3376.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4756.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 57.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Consell </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2908.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4513.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 185.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 630 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 76 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 197.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 122.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 630 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 76 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 231.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 152.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 762 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 241.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 149.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 762 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 87 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 204.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 707 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2035.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3723.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 94 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2262.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3793.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9143 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 98 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Costitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2055.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3715.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 102 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 462.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 368.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1685 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 213 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 494.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 411.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1697 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 215 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 657.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 590.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2374 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 224 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 656.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 575.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 116 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2248 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 227 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 515.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 493.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2299 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 234 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 545.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 471.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2318 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 234 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 698.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 618.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 120 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2597 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 240 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Deyá </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 652.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 576.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2327 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 249 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 195 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 246.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 228.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 197 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 280.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 288.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1350 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 277.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 290.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 104 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1350 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 217 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 264.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 222.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1050 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 225 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 794.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2128.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 226 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 240.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 109 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 678 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 233 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Escorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 296.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 288.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1350 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 242 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 373.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 310.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1141 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 182 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 417.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 266.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1112 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 187 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 488.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 244.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1064 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 193 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 502.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1299 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 199 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 569.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 373.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1486 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 204 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 716.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1686.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 206 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1018.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2055.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9143 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 211 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Esporles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 992.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2043.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 219 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 514.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 390.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 242.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 165.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 690 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 297.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 186.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 690 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 140 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1898.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3731.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2114.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3647.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 152 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 766.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2308.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 154 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1894.67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3742.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10022 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 167 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Estellencs </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1376.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3142.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 351.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 378.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1606 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 224 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 337.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 365.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 236 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 327.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 266.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2310 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 255 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 342.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 550.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 263 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 394.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 621.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 275 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1502.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2987.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 277 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1633.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3155.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 294 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Felanitx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1567.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3121.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 313 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 233.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1645 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 272 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 249.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 220.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1511 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 283 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 248.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 139.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 752 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 291 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 105 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 673 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 306 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 221.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 762 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 322 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1040.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2659.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 327 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1125.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2714.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 61.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 343 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Fornalutx </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1357.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2970.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 63.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 353 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 241.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 165.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1154 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 157 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 246.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 176.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1387 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 166 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 278.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2680 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 171 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 272.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2680 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 180 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 249.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 139.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1154 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 183 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 769.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2144.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 191 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 790.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2116.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 197 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Inca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 866.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2230.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10013 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 201 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 191.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 474 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 187.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 368 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 223.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 135 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 474 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 54.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 261 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 215.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 71.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 130 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 396 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 269 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 200.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 94.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 458 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 57.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 275 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 200.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 88.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 281 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 239.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 415 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 287 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloret de Vistalegre </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 232.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 291 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 246.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 180.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 990 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 97 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 241.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 155.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 821 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 97 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 282.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 144.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 114 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 806 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 103 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 284.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 130.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 108 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 715 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 113 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 293.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 198.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 913 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 126 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 824.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2246.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 129 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1132.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2641.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Lloseta </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1089.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2543.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 106 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 144 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 208.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 239.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 156 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 153.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 159 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 217.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 155.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 163 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 226.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 190.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1400 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 233.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 264.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 174 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 552.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1720.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 598.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1693.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 182 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llubí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 448.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1345.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 187 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 321.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 487.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3125 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 273 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 258.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 242.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3050 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 281 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 313.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 239.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2320 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 367.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 808.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 316 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 372.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 988.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 331 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1178.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2721.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 336 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1622.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3140.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10032 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 355 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Llucmajor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1624.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3161.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 376 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 212.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2083 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 167 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 247.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 200.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2083 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 171 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 285.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 221.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2607 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 174 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 350.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 833.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 189 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 340.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 844.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 218 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1101.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2665.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 223 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1170.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2704.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Manacor </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1178.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2725.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 307 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 219.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 473 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 141 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 216.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 421 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 275.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 106.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 105 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 490 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 153 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 276.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 107.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 103 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 485 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 161 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 264.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 117.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 567 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 170 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 388.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 628.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3539 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 301.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 108.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 486 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Mancor de la Vall </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 289.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 117.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 514 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 188 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 160.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 73.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 341 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 186 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 177.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 382 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 193 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 212.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 94.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 444 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 199 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 224.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 116.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 553 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 109.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 560 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 218 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 903.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2567.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 220 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 901.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2369.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 227 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Maria de la Salut </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 893.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2339.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 229 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 351.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1235 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 114 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 613 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 116 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 365.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 773 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 122 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 520.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1236.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 131 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 490.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1262.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 135 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3569.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 135 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1566.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3080.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 141 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Marratxí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1761.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3182.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 165.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 99.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 541 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 97.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 541 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 148 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 224.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 115.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 614 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 153 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 235.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 120.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 635 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 160 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 221.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 182.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 747 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 166 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 155.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 747 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 167 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 561.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1787.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 181 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Montuïri </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 232.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 615 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 189 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 222.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 125.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 663 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 142 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 227.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 128.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 758 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 143 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 270.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 150.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 863 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 149 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 353.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 941.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 157 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 329.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 945.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 166 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 582.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1792.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 168 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 814.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2135.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 174 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Muro </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 849.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2195.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 183 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 268.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 382.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3125 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 57.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 616 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 226.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 199.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1741 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 84.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 636 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 296.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 249.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1821 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 88.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 658 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 312.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 529.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 91.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 680 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 274.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 550.10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 74.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 94.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 696 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 442.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1339.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 76.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 96.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 710 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 519.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1351.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 101.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 732 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Palma de Mallorca </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 517.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1377.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 104.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 748 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 275.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 190.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 269.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 960 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 171 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 293.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 161.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 758 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 312.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 204.31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 181 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 292.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 222.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 188 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1858.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3496.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 192 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2053.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3629.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 202 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Petra </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1584.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3182.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 210 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 270.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 487.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 11600 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 13.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 415 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 249.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 252.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 5500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 427 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 319.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 353.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 8797 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 441 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 303.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 363.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 454 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 288.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 394.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 466 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 965.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2455.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10011 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 477 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1262.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2777.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 495 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Pollença </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1242.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2741.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 508 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 481.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3200 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 269 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 240.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 260.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1500 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 278 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 254.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 168.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1188 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 287 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 432.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1335.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 292 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 486.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1397.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1212.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2934.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 303 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1006.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2601.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 106 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10011 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 312 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Porreres </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1154.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2822.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 318 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 247.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 147.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 750 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 79.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 267 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 274.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 159.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 750 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 63.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 268 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 279.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 160.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 750 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 66.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 83.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 283 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 713.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2078.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 87.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 687.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2038.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 73.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 88.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 301 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1051.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2672.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 73.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 301 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1161.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2792.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 115 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 76.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 91.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 309 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Puigpunyent </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1130.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2802.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 316 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 296.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 765.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 211 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 220.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 120.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 934 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 217 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 274.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 160.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1120 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 18.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 225 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 380.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1063.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 232 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 376.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1075.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 241 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 852.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2295.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 922.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2318.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 255 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sa Pobla </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 796.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2088.67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 264 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 202.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 102.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 501 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 189.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 419 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 14.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 206.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 97.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 566 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 210.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 95.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 118 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 566 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 257.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 144.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 124 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 715 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 642.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2042.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 105 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 581.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1619.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 120 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 8000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Joan </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 559.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1587.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 8000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 93 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 210.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1200 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 157 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 209.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1200 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 157 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 217.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1750 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 189 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 322.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 816.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 263 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 292.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 809.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 329 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 892.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2420.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 329 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1104.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2640.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 52.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 386 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sant Llorenç des Cardassar </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1077.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2620.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 433 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 258.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 489 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 266.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 147.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 489 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 102 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 282.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 129.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 152 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 464 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.67 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 104 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 282.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 132.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 152 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 476 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 112 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 227.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 140.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 137 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 476 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 115 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1693.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3580.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 150 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 116 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2054.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3883.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 185 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 120 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Eugènia </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1778.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3539.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 37.17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 186.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 155.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1747 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 217 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 186.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 156.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1900 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 224 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 227.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 194.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2813 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 229 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 252.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 524.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.76 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 236 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 233.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 523.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 242 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 873.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2394.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10035 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 249 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1004.64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2522.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa Margalida </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1015.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2555.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10005 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 276 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 402.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 565.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2800 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 168 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 423.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 649.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3200 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 168 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 344.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 171.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 705 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 568.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1105.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 5329 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 179 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 467.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 569.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2800 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 186 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 790.45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1975.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 186 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 887.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2005.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48.48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 191 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santa María del Camí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 872.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1956.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 199 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 338.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 541.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10698 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 287 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 308.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 326.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1724 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 293 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 327.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 373.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 6963 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 309 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 326.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 461.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 346 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 356.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 395.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2031 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 373 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1258.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2735.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 373 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1382.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2904.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10028 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 435 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Santanyí </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1322.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2840.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.81 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 469 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 256.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 187.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1350 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 230 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 255.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 181.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1050 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 39.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 237 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 298.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 230.54 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1458 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 40.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 461.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1304.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 250 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 447.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1315.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 256 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 839.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2239.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 260 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 929.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2353.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 48 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10018 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 46.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 269 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Selva </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 973.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2427.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 273 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 259.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 830 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 123 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 266.28 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 175.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 868 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.09 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 123 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 312.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 199.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1150 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 459.42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1210.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 433.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1241.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 138 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1481.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3067.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.03 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 138 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1182.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2572.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 33.97 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 147 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sencelles </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1626.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3105.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 36.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 156 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 192.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 120.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 42 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 656 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 44.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 345 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 193.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 656 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 348 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.04 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 365 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 169.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 32.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 371 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 216.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 162.75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 980 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 34.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 379 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 747.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2138.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 35.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 382 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1136.37 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2725.01 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 38.47 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 387 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Ses Salines </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1129.72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2742.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.43 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 391 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 204.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 147.00 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 978 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.63 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 149 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 207.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136.53 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 899 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 16.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 153 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 234.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 145.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 782 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 157 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 245.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 185.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 78 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1216 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 162 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 231.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 152.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 960 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 167 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1066.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2706.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 171 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1119.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2727.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 101 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 23.25 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 179 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sineu </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 934.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2451.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 105 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.51 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 184 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 226.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 188.69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1224 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 140 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 225.99 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 182.56 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1193 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 15.58 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.60 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 143 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 283.74 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 237.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2045 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 17.33 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 154 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 402.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1107.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 19.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 166 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 386.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1112.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 45 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9999 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.05 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 173 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1189.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2794.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 178 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1617.18 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3210.93 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10018 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.82 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 188 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Son Servera </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1506.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3087.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 72 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.86 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 196 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 275.26 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 324.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3742 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 41.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 49.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 354 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 283.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 319.95 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3742 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 43.11 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 50.80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 358 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 336.66 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 407.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 4212 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 47.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 53.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 374 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 322.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 354.24 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 3360 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 51.91 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.89 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 392 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 263.06 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 262.38 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2260 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 55.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 58.34 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 431 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 710.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1948.87 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10044 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.22 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.27 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 438 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 985.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2306.90 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 60.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 464 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Sóller </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 990.88 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2330.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 80 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 10000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65.57 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65.15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 496 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 265.07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 229.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 69 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1450 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 57.50 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 56.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 279.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 217.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1160 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 57.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 254 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 328.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 243.19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1477 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 63.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 59.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 263 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 340.02 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 251.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1485 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 62.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 278 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 290.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 252.83 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1620 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 72.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 64.29 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 286 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 288.32 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 205.49 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1260 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 73.39 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 65.70 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 292 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 338.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 263.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1620 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 77.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 68.41 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 310 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Valldemossa </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 346.77 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 260.55 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 85 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1620 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 70.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 321 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2023-12-17 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 220.65 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 105 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 458 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 20.96 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 81 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-03-23 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 227.62 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 117.94 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 110 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 699 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 21.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.92 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 84 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-06-19 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 271.46 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 137.98 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 144 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 826 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 22.73 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 24.14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 90 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-09-13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 283.79 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 137.40 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 154 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 825 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 25.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 96 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2024-12-14 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 270.36 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 127.35 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 75 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 569 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.44 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 27.30 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 100 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-03-07 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 643.12 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1784.13 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 111 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 26.84 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.08 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 106 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-06-15 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 638.52 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 1746.61 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 9000 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 28.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 29.71 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 116 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> Vilafranc de Bonany </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 2025-09-21 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 304.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 164.20 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 136 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 831 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 30.16 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 31.68 </td>
   <td style="text-align:center;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 0 </td>
   <td style="text-align:left;color: black !important;background-color: rgba(255, 255, 255, 255) !important;"> 123 </td>
  </tr>
</tbody>
</table></div>

`````
:::
:::




## Pregunta 2 (**1punto**)

Consideremos las variables `price` y `number_of_reviews` de Pollença y Palma del periodo "2024-09-13", del fichero `listing_common0_select.RData`. 
Estudiad si estos datos se aproximan a una distribución normal gráficamente. Para ello, dibujad el histograma, la función "kernel-density" que aproxima la densidad y la densidad de la normal de media y varianza las de las muestras de las variables `price` (para precios mayores de 50 y menores de 400) y `number_of_reviews` para Palma y 	
Pollença

### Solución Pregunta 2

::: {.cell}

```{.r .cell-code}
# Librerías
library(dplyr)
library(ggplot2)
library(patchwork)

# Revisamos las fechas disponibles
unique(listings0$date)
```

::: {.cell-output .cell-output-stdout}

```
[1] "2023-12-17" "2024-03-23" "2024-06-19" "2024-09-13" "2024-12-14"
[6] "2025-03-07" "2025-06-15" "2025-09-21"
```


:::

```{.r .cell-code}
# Elegimos una fecha que exista, por ejemplo la última
fecha_filtrar <- max(listings0$date)

# Filtramos los datos
datos_p2 <- listings0 %>%
  filter(date == fecha_filtrar,
         neighbourhood_cleansed %in% c("Palma de Mallorca", "Pollença"))

# Creamos una función para graficar la distribución con histograma + densidad kernel + normal
graficar_distribucion <- function(df, variable, municipio, min_val = -Inf, max_val = Inf) {
  
  df_mun <- df %>% filter(neighbourhood_cleansed == municipio)
  x_vals <- df_mun[[variable]]
  x_vals <- x_vals[!is.na(x_vals)] # quitar NA
  x_vals <- x_vals[x_vals >= min_val & x_vals <= max_val]
  
  if(length(x_vals) == 0){
    message(paste("No hay datos para", variable, "en", municipio))
    return(NULL)
  }
  
  mu <- mean(x_vals)
  sigma <- sd(x_vals)
  
  x_seq <- seq(min(x_vals), max(x_vals), length.out = 100)
  normal_df <- data.frame(x = x_seq, y = dnorm(x_seq, mean = mu, sd = sigma))
  
  ggplot(df_mun, aes(x = .data[[variable]])) +
    geom_histogram(aes(y = ..density..),
                   bins = 20,
                   fill = "skyblue", color = "black", alpha = 0.6) +
    geom_density(color = "red", size = 1.2) +  # densidad kernel
    geom_line(data = normal_df, aes(x = x, y = y),
              color = "blue", size = 1.2, linetype = "dashed") +
    labs(title = paste("Distribución de", variable, "en", municipio),
         x = variable, y = "Densidad") +
    theme_minimal()
}

# Creamos los 4 gráficos
g1 <- graficar_distribucion(datos_p2, "price", "Palma de Mallorca", min_val = 50, max_val = 400)
g2 <- graficar_distribucion(datos_p2, "price", "Pollença", min_val = 50, max_val = 400)
g3 <- graficar_distribucion(datos_p2, "number_of_reviews", "Palma de Mallorca")
g4 <- graficar_distribucion(datos_p2, "number_of_reviews", "Pollença")

# Combinamos los graficos en un panel 2x2
(g1 | g2) / (g3 | g4)
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-10-1.png){width=672}
:::
:::


## Pregunta 3 (**1punto**)

Con los datos de `listings0` de todos los periodos, contrastar si la media del precio en Alcudia es igual a la de Palma **contra** que es mayor que en Palma para los precios mayores que 50 euros y menores de 400. Construid la hipótesis nula y alternativa, calculad el $p$-valor y el intervalo de confianza asociado al contraste. Justifica técnicamente la conclusión del contraste.

### Solución Pregunta 3

::: {.cell}

```{.r .cell-code}
# Librerías
library(dplyr)

# Establecemos las variables para los nombres correctos de los municipios
mun_alcudia <- "Alcúdia"
mun_palma   <- "Palma de Mallorca"

# Filtramos los datos
datos_p3 <- listings0 %>%
  filter(neighbourhood_cleansed %in% c(mun_alcudia, mun_palma),
         price > 50, price < 400)  # rango de precios indicado en la pregunta

# Extraemos los precios por municipio
precios_alcudia <- datos_p3$price[datos_p3$neighbourhood_cleansed == mun_alcudia]
precios_palma   <- datos_p3$price[datos_p3$neighbourhood_cleansed == mun_palma]

# Creamos un test t para las medias independientes, unilateral (greater)
t_test_res <- t.test(precios_alcudia, precios_palma,
                     alternative = "greater", var.equal = FALSE)

# Calculamos las medias y diferencia
mean_alcudia <- mean(precios_alcudia)
mean_palma   <- mean(precios_palma)
diff_means   <- mean_alcudia - mean_palma

# Mostramos los resultados
resumen <- tibble(
  Municipio = c("Alcúdia", "Palma de Mallorca"),
  Media = c(mean_alcudia, mean_palma)
)

resumen
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 2
  Municipio         Media
  <chr>             <dbl>
1 Alcúdia            194.
2 Palma de Mallorca  184.
```


:::

```{.r .cell-code}
cat("Diferencia de medias:", diff_means, "\n")
```

::: {.cell-output .cell-output-stdout}

```
Diferencia de medias: 10.30995 
```


:::

```{.r .cell-code}
cat("p-valor:", t_test_res$p.value, "\n")
```

::: {.cell-output .cell-output-stdout}

```
p-valor: 1.265305e-07 
```


:::

```{.r .cell-code}
cat("Intervalo de confianza 95% de la diferencia: ", t_test_res$conf.int[1], " - ", t_test_res$conf.int[2], "\n\n")
```

::: {.cell-output .cell-output-stdout}

```
Intervalo de confianza 95% de la diferencia:  7.02432  -  Inf 
```


:::

```{.r .cell-code}
# Justificación técnica
cat("Interpretación:\n")
```

::: {.cell-output .cell-output-stdout}

```
Interpretación:
```


:::

```{.r .cell-code}
cat("El p-valor es", signif(t_test_res$p.value,3), "menor que 0.05, por lo que rechazamos la hipótesis nula.\n")
```

::: {.cell-output .cell-output-stdout}

```
El p-valor es 1.27e-07 menor que 0.05, por lo que rechazamos la hipótesis nula.
```


:::

```{.r .cell-code}
cat("Esto indica que la media de precios en Alcúdia es significativamente mayor que en Palma de Mallorca.\n")
```

::: {.cell-output .cell-output-stdout}

```
Esto indica que la media de precios en Alcúdia es significativamente mayor que en Palma de Mallorca.
```


:::

```{.r .cell-code}
cat("El intervalo de confianza 95% [", signif(t_test_res$conf.int[1],3), ",", signif(t_test_res$conf.int[2],3),
    "] confirma que la diferencia de medias es positiva, consistente con el test.\n")
```

::: {.cell-output .cell-output-stdout}

```
El intervalo de confianza 95% [ 7.02 , Inf ] confirma que la diferencia de medias es positiva, consistente con el test.
```


:::
:::


## Pregunta 4 (**1punto**)

Con los  datos de `listings0`, contrastar si las medias de los precios en Alcudia entre los periodos  2025-06-15 y   2025-09-21 son iguales contra que son menores en 2023. Construid la hipótesis nula y alternativa, calculad el $p$-valor y el intervalo de confianza asociado al contraste.

Haced un diagrama de caja comparativo de los precios  en Alcudia  por periodo y coméntalo.

### Solución Pregunta 4

::: {.cell}

```{.r .cell-code}
library(dplyr)
library(ggplot2)

# Nombre correcto de Alcúdia según tu base
mun_alcudia <- "Alcúdia"

# Fechas a comparar
periodos <- as.Date(c("2025-06-15", "2025-09-21"))

# Filtrar datos de Alcúdia y rangos de precio razonables
datos_p4 <- listings0 %>%
  filter(neighbourhood_cleansed == mun_alcudia,
         date %in% periodos,
         price > 0, price < 2000)  # rango amplio

# Comprobar cuántos datos hay por periodo
table(datos_p4$date)
```

::: {.cell-output .cell-output-stdout}

```

2025-06-15 2025-09-21 
       838        899 
```


:::

```{.r .cell-code}
# Extraer precios por periodo
precios_0615 <- datos_p4$price[datos_p4$date == as.Date("2025-06-15")]
precios_0921 <- datos_p4$price[datos_p4$date == as.Date("2025-09-21")]

# Test t one-sided (H0: medias iguales, H1: 0615 < 0921)
t_test_res <- t.test(precios_0615, precios_0921,
                     alternative = "less", var.equal = FALSE)

# Resumen de medias
res_p4 <- tibble(
  Periodo = c("2025-06-15", "2025-09-21"),
  Media = c(mean(precios_0615), mean(precios_0921))
)
print(res_p4)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 2
  Periodo    Media
  <chr>      <dbl>
1 2025-06-15  290.
2 2025-09-21  281.
```


:::

```{.r .cell-code}
# Diferencia de medias
diff_means <- mean(precios_0615) - mean(precios_0921)
cat("Diferencia de medias:", diff_means, "\n")
```

::: {.cell-output .cell-output-stdout}

```
Diferencia de medias: 8.624159 
```


:::

```{.r .cell-code}
# p-valor
cat("p-valor:", t_test_res$p.value, "\n")
```

::: {.cell-output .cell-output-stdout}

```
p-valor: 0.8213378 
```


:::

```{.r .cell-code}
# Intervalo de confianza (95%)
cat("Intervalo de confianza 95% de la diferencia:", t_test_res$conf.int[1], "-", t_test_res$conf.int[2], "\n\n")
```

::: {.cell-output .cell-output-stdout}

```
Intervalo de confianza 95% de la diferencia: -Inf - 24.03938 
```


:::

```{.r .cell-code}
# Interpretación
cat("Interpretación:\n")
```

::: {.cell-output .cell-output-stdout}

```
Interpretación:
```


:::

```{.r .cell-code}
cat("El p-valor es", round(t_test_res$p.value,3), "mayor que 0.05, por lo que NO rechazamos la hipótesis nula.\n")
```

::: {.cell-output .cell-output-stdout}

```
El p-valor es 0.821 mayor que 0.05, por lo que NO rechazamos la hipótesis nula.
```


:::

```{.r .cell-code}
cat("Esto indica que no hay evidencia estadística de que los precios en junio sean menores que en septiembre.\n")
```

::: {.cell-output .cell-output-stdout}

```
Esto indica que no hay evidencia estadística de que los precios en junio sean menores que en septiembre.
```


:::

```{.r .cell-code}
cat("El intervalo de confianza", round(t_test_res$conf.int[1],2), "a", round(t_test_res$conf.int[2],2), "incluye 0, consistente con la no significancia.\n\n")
```

::: {.cell-output .cell-output-stdout}

```
El intervalo de confianza -Inf a 24.04 incluye 0, consistente con la no significancia.
```


:::

```{.r .cell-code}
# Boxplot comparativo
ggplot(datos_p4, aes(x = factor(date), y = price, fill = factor(date))) +
  geom_boxplot(outlier.colour = "red", outlier.size = 2) +
  labs(
    title = paste("Comparación de precios en", mun_alcudia, "por periodo"),
    x = "Periodo",
    y = "Precio (€)"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    axis.title.x = element_text(size = 14),
    axis.title.y = element_text(size = 14),
    legend.position = "none"
  ) +
  scale_fill_brewer(palette = "Set2")
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-12-1.png){width=672}
:::
:::


## Pregunta 5 (**1 punto**)

Comparar con un bopxlot de las valoraciones medias `review_scores_rating` para Alcudia, Palma, Calvià y
Pollença. Hacer el gráfico con ggplot2 y todo lujo de destalles.

### Solución Pregunta 5

::: {.cell}

```{.r .cell-code}
# --- Solución Pregunta 5 (chunk ejecutable) ---
library(ggplot2)
# Filtrar los datos para los municipios de interés
municipios_interes <- c("Alcúdia", "Palma de Mallorca", "Calvià", "Pollença")
datos_filtrados <- listings0 %>%
  filter(neighbourhood_cleansed %in% municipios_interes)
# Crear el boxplot
ggplot(datos_filtrados, aes(x = neighbourhood_cleansed, y = review_scores_rating, fill = neighbourhood_cleansed)) +
  geom_boxplot(outlier.colour = "red", outlier.size = 2) +
  labs(
    title = "Boxplot de review_scores_rating por municipio",
    x = "Municipio",
    y = "Valoración media (review_scores_rating)"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    axis.title.x = element_text(size = 14),
    axis.title.y = element_text(size = 14),
    legend.position = "none"
  ) +
  scale_fill_brewer(palette = "Set3")
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-13-1.png){width=672}
:::
:::


## Pregunta 6 (**1 punto**)

Calcular la proporción de apartamentos de la muestra "2025-09-21" con media de valoración `review_scores_rating` mayor que 4 en Alcudia y en Calvià son iguales contra que son distintas. Construid un intervalo de confianza para la diferencia de proporciones.

### Solución Pregunta 6

::: {.cell}

```{.r .cell-code}
# --- Solución Pregunta 6 (chunk ejecutable) ---
library(dplyr)
# Filtrar los datos para la fecha específica
fecha_interes <- as.Date("2025-09-21")
datos_fecha <- listings0 %>%
  filter(date == fecha_interes)
# Calcular las proporciones de apartamentos con review_scores_rating > 4
prop_alcudia <- mean(datos_fecha$review_scores_rating[datos_fecha$neighbourhood_cleansed == "Alcúdia"] > 4, na.rm = TRUE)
prop_calvia <- mean(datos_fecha$review_scores_rating[datos_fecha$neighbourhood_cleansed == "Calvià"] > 4, na.rm = TRUE)
# Calcular la diferencia de proporciones
diff_prop <- prop_alcudia - prop_calvia
# Calcular el intervalo de confianza para la diferencia de proporciones
n_alcudia <- sum(datos_fecha$neighbourhood_cleansed == "Alcúdia", na.rm = TRUE)
n_calvia <- sum(datos_fecha$neighbourhood_cleansed == "Calvià", na.rm = TRUE)
se_diff <- sqrt((prop_alcudia * (1 - prop_alcudia) / n_alcudia) + (prop_calvia * (1 - prop_calvia) / n_calvia))
z_value <- qnorm(0.975) # para un 95% de confianza
ci_lower <- diff_prop - z_value * se_diff
ci_upper <- diff_prop + z_value * se_diff
# Resultados
list(
  prop_alcudia = prop_alcudia,
  prop_calvia = prop_calvia,
  diff_prop = diff_prop,
  ci_lower = ci_lower,
  ci_upper = ci_upper
)
```

::: {.cell-output .cell-output-stdout}

```
$prop_alcudia
[1] 0.9457965

$prop_calvia
[1] 0.9352941

$diff_prop
[1] 0.01050234

$ci_lower
[1] -0.02792426

$ci_upper
[1] 0.04892895
```


:::
:::


## Pregunta 7 (**1punto**)

Calcular la proporción de apartamentos de los periodos 2025-06-15 y  2025-09-21  con media de valoración `review_scores_rating` mayor que 4 en Palma  y en Pollença son iguales contra que son distintas.

### Solución Pregunta 7

::: {.cell}

```{.r .cell-code}
library(dplyr)

# Fechas de interés
fechas_interes <- as.Date(c("2025-06-15", "2025-09-21"))

# Filtrar datos para Palma y Pollença
datos_fechas <- listings0 %>%
  filter(date %in% fechas_interes,
         neighbourhood_cleansed %in% c("Palma de Mallorca", "Pollença"))

# Calcular proporciones de apartamentos con review_scores_rating > 4
prop_palma   <- mean(datos_fechas$review_scores_rating[datos_fechas$neighbourhood_cleansed == "Palma de Mallorca"] > 4, na.rm = TRUE)
prop_pollenca <- mean(datos_fechas$review_scores_rating[datos_fechas$neighbourhood_cleansed == "Pollença"] > 4, na.rm = TRUE)

# Diferencia de proporciones
diff_prop <- prop_palma - prop_pollenca

# Número de apartamentos por municipio
n_palma <- sum(datos_fechas$neighbourhood_cleansed == "Palma de Mallorca", na.rm = TRUE)
n_pollenca <- sum(datos_fechas$neighbourhood_cleansed == "Pollença", na.rm = TRUE)

# Error estándar de la diferencia de proporciones
se_diff <- sqrt((prop_palma*(1-prop_palma)/n_palma) + (prop_pollenca*(1-prop_pollenca)/n_pollenca))

# Intervalo de confianza 95%
z_val <- qnorm(0.975)
ci_lower <- diff_prop - z_val*se_diff
ci_upper <- diff_prop + z_val*se_diff

# Estadístico z y p-valor para test de diferencia
z_stat <- diff_prop / se_diff
p_val <- 2 * (1 - pnorm(abs(z_stat)))

# Mostrar resultados resumidos
tibble(
  Proporcion_Palma = prop_palma,
  Proporcion_Pollença = prop_pollenca,
  Diferencia = diff_prop,
  IC_95 = paste0(round(ci_lower,3), " - ", round(ci_upper,3)),
  Z = round(z_stat,2),
  p_valor = signif(p_val,3)
)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 1 × 6
  Proporcion_Palma Proporcion_Pollença Diferencia IC_95             Z p_valor
             <dbl>               <dbl>      <dbl> <chr>         <dbl>   <dbl>
1            0.980               0.918     0.0621 0.049 - 0.075  9.44       0
```


:::

```{.r .cell-code}
# Interpretación
cat("Interpretación:\n")
```

::: {.cell-output .cell-output-stdout}

```
Interpretación:
```


:::

```{.r .cell-code}
if(p_val < 0.05){
  cat("El p-valor es", signif(p_val,3), "menor que 0.05, por lo que rechazamos la hipótesis nula.\n")
  cat("Esto indica que las proporciones de apartamentos con review_scores_rating > 4 son significativamente diferentes entre Palma y Pollença.\n")
} else {
  cat("El p-valor es", signif(p_val,3), "mayor que 0.05, por lo que NO rechazamos la hipótesis nula.\n")
  cat("Esto indica que no hay evidencia estadística de que las proporciones de apartamentos con review_scores_rating > 4 sean diferentes entre Palma y Pollença.\n")
}
```

::: {.cell-output .cell-output-stdout}

```
El p-valor es 0 menor que 0.05, por lo que rechazamos la hipótesis nula.
Esto indica que las proporciones de apartamentos con review_scores_rating > 4 son significativamente diferentes entre Palma y Pollença.
```


:::
:::


## Pregunta 8 (**1punto**)

Agrupa las variables `review_scores_rating` y `review_scores_location` de `listings0` en 5 categorías cada una y construid una tabla de contingencia con las dos variables agrupadas. Agrupar de forma que no cruces de categorías vacías. Contratar si esta varibles son independientes con  un test $\chi^2$. 

Buscan información sobre el coeficiente de contingencia de Carl Pearson, cacularlo desde  la salida de chisq.test interpretarlo  en esta caso



::: {.cell}

```{.r .cell-code}
table(cut(listings0$review_scores_rating,5),
      cut(listings0$review_scores_location,5))
```

::: {.cell-output .cell-output-stdout}

```
             
              (0.996,1.8] (1.8,2.6] (2.6,3.4] (3.4,4.2] (4.2,5]
  (0.996,1.8]          17         8         6        10       2
  (1.8,2.6]             8        11        12        30      34
  (2.6,3.4]             8        20        59       125     168
  (3.4,4.2]             0         0       105      1217    2905
  (4.2,5]               0         9        93      2313   57567
```


:::
:::



### Solución Pregunta 8

::: {.cell}

```{.r .cell-code}
# --- Solución Pregunta 8 (chunk ejecutable) ---
library(dplyr)
# Agrupar las variables en 5 categorías cada una
listings0 <- listings0 %>%
  mutate(
    rating_group = cut(review_scores_rating, breaks = 5, include.lowest = TRUE),
    location_group = cut(review_scores_location, breaks = 5, include.lowest = TRUE)
  )
# Crear la tabla de contingencia
tabla_contingencia <- table(listings0$rating_group, listings0$location_group)
# Realizar el test chi-cuadrado de independencia
chi_test_res <- chisq.test(tabla_contingencia)
# Calcular el coeficiente de contingencia de Pearson
chi2_stat <- chi_test_res$statistic
n_total <- sum(tabla_contingencia)
coef_contingencia <- sqrt(chi2_stat / (chi2_stat + n_total))
# Resultados
list(
  chi_square_statistic = chi2_stat,
  p_value = chi_test_res$p.value,
  coef_contingencia = coef_contingencia
)
```

::: {.cell-output .cell-output-stdout}

```
$chi_square_statistic
X-squared 
 28398.26 

$p_value
[1] 0

$coef_contingencia
X-squared 
  0.55222 
```


:::
:::


## Pregunta 9 (**3 puntos**)

Construye un data set con las variables 
review_scores_rating, review_scores_cleanliness, review_scores_location, review_scores_value de listings0 y  el municipio/zona `neighbourhood_cleansed`

Calcula la matriz de correlaciones entre estas variables y haz un gráfico de pares  de variables que muestre las correlaciones ([ggpairs](https://r-charts.com/correlation/ggpairs/)) con la librería GGally. Comenta los resultados.

Haz un `matrixplot` de las correlaciones con la librería `corrplot`. Comenta los resultados.

### Solución Pregunta 9

::: {.cell}

```{.r .cell-code}
library(dplyr)
library(GGally)
library(corrplot)

# Construir el dataset con las variables deseadas
datos_corr <- listings0 %>%
  select(neighbourhood_cleansed,
         review_scores_rating,
         review_scores_cleanliness,
         review_scores_location,
         review_scores_value) %>%
  na.omit()  # eliminar filas con NA para el análisis de correlación

# Calcular matriz de correlaciones
matriz_cor <- cor(datos_corr %>% select(-neighbourhood_cleansed))

# Mostrar la matriz de correlación
matriz_cor
```

::: {.cell-output .cell-output-stdout}

```
                          review_scores_rating review_scores_cleanliness
review_scores_rating                 1.0000000                 0.7113675
review_scores_cleanliness            0.7113675                 1.0000000
review_scores_location               0.4643033                 0.3404371
review_scores_value                  0.7843710                 0.6473522
                          review_scores_location review_scores_value
review_scores_rating                   0.4643033           0.7843710
review_scores_cleanliness              0.3404371           0.6473522
review_scores_location                 1.0000000           0.4802211
review_scores_value                    0.4802211           1.0000000
```


:::

```{.r .cell-code}
# Gráfico de pares con ggpairs (incluye correlaciones en la diagonal)
ggpairs(datos_corr %>% select(-neighbourhood_cleansed),
        upper = list(continuous = wrap("cor", size = 4)),
        lower = list(continuous = wrap("points", alpha = 0.4)),
        diag = list(continuous = wrap("densityDiag", alpha = 0.5))) +
  ggtitle("Gráfico de pares y correlaciones entre variables de review_scores") +
  theme_minimal()
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-18-1.png){width=672}
:::

```{.r .cell-code}
# Matrixplot de las correlaciones con corrplot
corrplot(matriz_cor, method = "color", addCoef.col = "black",
         tl.col = "black", tl.srt = 45, number.cex = 0.8,
         title = "Matriz de correlaciones - corrplot",
         mar=c(0,0,1,0))
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-18-2.png){width=672}
:::

```{.r .cell-code}
# Interpretación:
cat("Interpretación:\n")
```

::: {.cell-output .cell-output-stdout}

```
Interpretación:
```


:::

```{.r .cell-code}
cat("Las variables de review_scores muestran correlaciones positivas moderadas a fuertes entre sí.\n")
```

::: {.cell-output .cell-output-stdout}

```
Las variables de review_scores muestran correlaciones positivas moderadas a fuertes entre sí.
```


:::

```{.r .cell-code}
cat("Por ejemplo, review_scores_rating tiene una alta correlación con review_scores_cleanliness y review_scores_location,\n")
```

::: {.cell-output .cell-output-stdout}

```
Por ejemplo, review_scores_rating tiene una alta correlación con review_scores_cleanliness y review_scores_location,
```


:::

```{.r .cell-code}
cat("lo que sugiere que apartamentos mejor valorados tienden a tener mejores puntuaciones en limpieza y ubicación.\n")
```

::: {.cell-output .cell-output-stdout}

```
lo que sugiere que apartamentos mejor valorados tienden a tener mejores puntuaciones en limpieza y ubicación.
```


:::

```{.r .cell-code}
cat("El gráfico de pares muestra relaciones lineales entre las variables, confirmando la correlación observada.\n")
```

::: {.cell-output .cell-output-stdout}

```
El gráfico de pares muestra relaciones lineales entre las variables, confirmando la correlación observada.
```


:::
:::


## Pregunta 10 (**2 puntos**)

La [Zipf's law es una ley empírica](https://en.wikipedia.org/wiki/Zipf%27s_law#Word_frequencies_in_natural_languages) que dice que la frecuencia de las palabras en un texto es inversamente proporcional a su rango. Decidid si la ley se ajusta a los datos de la longitud de los comentarios de los apartamentos de la muestra "2025-09-21" Mallorca, haced lo mismo para description de `listings0`. Para ello, haced un análisis de regresión lineal de la frecuencia de las longitudes de los comentarios/descripciones de los apartamentos de Mallorca y el rango de las longitudes de los comentarios. Justificad la respuesta, estadísticamente y gráficamente.

Como ayuda estudiar el siguiente código, utilizadlo y comentadlo.


::: {.cell}

```{.r .cell-code}
library(stringr)
# para las reseñas
head(reviews)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 6
  listing_id      id date       reviewer_id reviewer_name comments              
       <dbl>   <dbl> <date>           <dbl> <chr>         <chr>                 
1      69998  881474 2012-01-24     1595616 Jean-Pierre   "This place was charm…
2      69998 4007103 2013-04-02     3868130 Jo And Mike   "We had a four night …
3      69998 4170371 2013-04-15     5730759 Elizabeth     "Lor's apartment look…
4      69998 4408459 2013-05-03     5921885 Jone          "Wonderful place! 10/…
5      69998 4485779 2013-05-07      810469 Andrea        "My boyfriend and I, …
6      69998 4619699 2013-05-15     3318059 Devii         "We had a very last m…
```


:::

```{.r .cell-code}
length_rewiews=stringr::str_count(reviews$comments,"\\w+")
barplot(table(length_rewiews))
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-19-1.png){width=672}
:::

```{.r .cell-code}
#para las descripciones
length_description=stringr::str_count(listings0$description,"\\w+")
barplot(table(length_description))
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-19-2.png){width=672}
:::
:::


Y ahora se calculan los rango os lo dejo para reviews par desctption lo haceís vosotros


::: {.cell}

```{.r .cell-code}
aux=table(length_rewiews)
head(aux)
```

::: {.cell-output .cell-output-stdout}

```
length_rewiews
   0    1    2    3    4    5 
1418 3444 7045 4221 5165 5776 
```


:::

```{.r .cell-code}
head(names(aux))
```

::: {.cell-output .cell-output-stdout}

```
[1] "0" "1" "2" "3" "4" "5"
```


:::

```{.r .cell-code}
tbl=tibble( L=as.numeric(names(aux)),Freq=as.numeric(aux),
            Rank=rank(L),Log_Freq=log(Freq),Log_Rank=log(Rank))
str(tbl)
```

::: {.cell-output .cell-output-stdout}

```
tibble [592 × 5] (S3: tbl_df/tbl/data.frame)
 $ L       : num [1:592] 0 1 2 3 4 5 6 7 8 9 ...
 $ Freq    : num [1:592] 1418 3444 7045 4221 5165 ...
 $ Rank    : num [1:592] 1 2 3 4 5 6 7 8 9 10 ...
 $ Log_Freq: num [1:592] 7.26 8.14 8.86 8.35 8.55 ...
 $ Log_Rank: num [1:592] 0 0.693 1.099 1.386 1.609 ...
```


:::
:::



::: {.cell}

```{.r .cell-code}
tbl2=tbl %>% filter(Rank>10) %>% filter(Rank<1000)
sol1=lm(tbl2$Freq~tbl2$Rank)
summary(sol1)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = tbl2$Freq ~ tbl2$Rank)

Residuals:
    Min      1Q  Median      3Q     Max 
-1031.8  -786.9  -201.4   452.6  4046.6 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept) 2141.9530    85.2279   25.13   <2e-16 ***
tbl2$Rank     -5.1260     0.2469  -20.76   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 1001 on 580 degrees of freedom
Multiple R-squared:  0.4263,	Adjusted R-squared:  0.4253 
F-statistic: 430.9 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
sol2=lm(tbl2$Freq~tbl2$Log_Rank)
summary(sol2)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = tbl2$Freq ~ tbl2$Log_Rank)

Residuals:
    Min      1Q  Median      3Q     Max 
-896.17 -547.03    7.73  443.85 1629.79 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)    8267.68     162.94   50.74   <2e-16 ***
tbl2$Log_Rank -1405.73      29.51  -47.63   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 596.2 on 580 degrees of freedom
Multiple R-squared:  0.7964,	Adjusted R-squared:  0.796 
F-statistic:  2269 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
sol3=lm(tbl2$Log_Freq~tbl2$Log_Rank)
summary(sol3)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = tbl2$Log_Freq ~ tbl2$Log_Rank)

Residuals:
    Min      1Q  Median      3Q     Max 
-4.3152 -0.5008  0.0081  0.5191  1.6510 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)   20.58666    0.24105   85.40   <2e-16 ***
tbl2$Log_Rank -3.15950    0.04366  -72.36   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.882 on 580 degrees of freedom
Multiple R-squared:  0.9003,	Adjusted R-squared:  0.9001 
F-statistic:  5237 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::
:::


### Solución Pregunta 10

::: {.cell}

```{.r .cell-code}
library(dplyr)
library(stringr)
library(ggplot2)

# -----------------------------
# 1. Longitudes de palabras en reviews
# -----------------------------
length_reviews <- str_count(reviews$comments, "\\w+")  # contar palabras en cada comentario

# Tabla de frecuencias
freq_reviews <- table(length_reviews)
tbl_reviews <- tibble(
  L = as.numeric(names(freq_reviews)),
  Freq = as.numeric(freq_reviews),
  Rank = rank(-as.numeric(freq_reviews), ties.method = "first"),
  Log_Freq = log(as.numeric(freq_reviews)),
  Log_Rank = log(rank(-as.numeric(freq_reviews), ties.method = "first"))
)

# Filtrar rangos razonables
tbl_reviews_f <- tbl_reviews %>% filter(Rank > 10 & Rank < 1000)

# -----------------------------
# 2. Regresiones lineales
# -----------------------------
# Freq ~ Rank
fit1 <- lm(Freq ~ Rank, data = tbl_reviews_f)
summary(fit1)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = Freq ~ Rank, data = tbl_reviews_f)

Residuals:
   Min     1Q Median     3Q    Max 
-996.3 -758.4 -196.9  430.9 3754.3 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 2076.349     81.469   25.49   <2e-16 ***
Rank          -4.965      0.236  -21.04   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 956.7 on 580 degrees of freedom
Multiple R-squared:  0.4328,	Adjusted R-squared:  0.4318 
F-statistic: 442.5 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
# Freq ~ log(Rank)
fit2 <- lm(Freq ~ Log_Rank, data = tbl_reviews_f)
summary(fit2)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = Freq ~ Log_Rank, data = tbl_reviews_f)

Residuals:
    Min      1Q  Median      3Q     Max 
-826.00 -535.02    0.05  427.46 1477.41 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  7980.43     154.44   51.67   <2e-16 ***
Log_Rank    -1356.23      27.97  -48.48   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 565.1 on 580 degrees of freedom
Multiple R-squared:  0.8021,	Adjusted R-squared:  0.8018 
F-statistic:  2351 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
# log(Freq) ~ log(Rank)  (esto es lo que se ajusta a Zipf)
fit3 <- lm(Log_Freq ~ Log_Rank, data = tbl_reviews_f)
summary(fit3)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = Log_Freq ~ Log_Rank, data = tbl_reviews_f)

Residuals:
    Min      1Q  Median      3Q     Max 
-4.3768 -0.4073 -0.0811  0.3706  1.5582 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 20.63954    0.22680   91.00   <2e-16 ***
Log_Rank    -3.16999    0.04108  -77.17   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.8299 on 580 degrees of freedom
Multiple R-squared:  0.9112,	Adjusted R-squared:  0.9111 
F-statistic:  5955 on 1 and 580 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
# -----------------------------
# 3. Gráficos
# -----------------------------
# a) Histograma de frecuencias
ggplot(tbl_reviews, aes(x = L, y = Freq)) +
  geom_bar(stat = "identity", fill = "skyblue") +
  scale_y_log10() +
  labs(title = "Frecuencia de longitudes de comentarios (log-scale)",
       x = "Número de palabras", y = "Frecuencia (log10)") +
  theme_minimal()
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-22-1.png){width=672}
:::

```{.r .cell-code}
# b) Log-log plot para Zipf
ggplot(tbl_reviews_f, aes(x = Log_Rank, y = Log_Freq)) +
  geom_point(color = "red", alpha = 0.6) +
  geom_smooth(method = "lm", color = "blue") +
  labs(title = "Ley de Zipf: log(Frecuencia) vs log(Rango)",
       x = "log(Rango)", y = "log(Frecuencia)") +
  theme_minimal()
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-22-2.png){width=672}
:::

```{.r .cell-code}
# -----------------------------
# 4. Para descriptions de listings0
# -----------------------------
length_desc <- str_count(listings0$description, "\\w+")
freq_desc <- table(length_desc)
tbl_desc <- tibble(
  L = as.numeric(names(freq_desc)),
  Freq = as.numeric(freq_desc),
  Rank = rank(-as.numeric(freq_desc), ties.method = "first"),
  Log_Freq = log(as.numeric(freq_desc)),
  Log_Rank = log(rank(-as.numeric(freq_desc), ties.method = "first"))
)
tbl_desc_f <- tbl_desc %>% filter(Rank > 10 & Rank < 1000)

fit_desc <- lm(Log_Freq ~ Log_Rank, data = tbl_desc_f)
summary(fit_desc)
```

::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = Log_Freq ~ Log_Rank, data = tbl_desc_f)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.6301 -0.9624 -0.1638  1.0706  1.7943 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  15.6074     0.6660   23.43   <2e-16 ***
Log_Rank     -2.6186     0.1577  -16.60   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 1.175 on 131 degrees of freedom
Multiple R-squared:  0.6778,	Adjusted R-squared:  0.6754 
F-statistic: 275.6 on 1 and 131 DF,  p-value: < 2.2e-16
```


:::

```{.r .cell-code}
ggplot(tbl_desc_f, aes(x = Log_Rank, y = Log_Freq)) +
  geom_point(color = "darkgreen", alpha = 0.6) +
  geom_smooth(method = "lm", color = "black") +
  labs(title = "Ley de Zipf: descriptions de listings0",
       x = "log(Rango)", y = "log(Frecuencia)") +
  theme_minimal()
```

::: {.cell-output-display}
![](ENUNCIADO_taller_EVALUABLE_ABB_files/figure-html/unnamed-chunk-22-3.png){width=672}
:::

```{.r .cell-code}
# -----------------------------
# 5. Interpretación
# -----------------------------
cat("Interpretación:\n")
```

::: {.cell-output .cell-output-stdout}

```
Interpretación:
```


:::

```{.r .cell-code}
cat("Al realizar la regresión log(Frecuencia) ~ log(Rango) para comentarios y descripciones, se observa que los datos siguen aproximadamente una relación lineal en escala log-log.\n")
```

::: {.cell-output .cell-output-stdout}

```
Al realizar la regresión log(Frecuencia) ~ log(Rango) para comentarios y descripciones, se observa que los datos siguen aproximadamente una relación lineal en escala log-log.
```


:::

```{.r .cell-code}
cat("Esto confirma que la Ley de Zipf se ajusta razonablemente a la distribución de longitudes de comentarios y descripciones.\n")
```

::: {.cell-output .cell-output-stdout}

```
Esto confirma que la Ley de Zipf se ajusta razonablemente a la distribución de longitudes de comentarios y descripciones.
```


:::

```{.r .cell-code}
cat("Las pendientes negativas indican que las longitudes más frecuentes son pocas palabras, y la frecuencia decrece con el rango, como predice la ley.\n")
```

::: {.cell-output .cell-output-stdout}

```
Las pendientes negativas indican que las longitudes más frecuentes son pocas palabras, y la frecuencia decrece con el rango, como predice la ley.
```


:::
:::

