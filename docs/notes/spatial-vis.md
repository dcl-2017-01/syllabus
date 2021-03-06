Spatial visualisation
================

Setup
-----

Since sf is so new, support for it in ggplot2 is also very new. That means you'll need to install the development version of ggplot2 from GitHub. That's easy to do using the devtools package:

``` r
# install.packages("devtools")
devtools::install_github("tidyverse/ggplot2")
```

Check that the install has succeeded by loading the tidyverse and then running `?geom_sf`. If you can't find the documentation for `geom_sf` something has gone wrong.

Next we'll load the tidyverse, sf, and create a couple of sample datasets.

``` r
library(tidyverse)
library(sf)
library(maps)

nc <- sf::st_read(system.file("shape/nc.shp", package = "sf"), quiet = TRUE)
states <- sf::st_as_sf(map("state", plot = FALSE, fill = TRUE))
```

`geom_sf()`
-----------

The easiest way to get started is to supply an sf object to `geom_sf()`:

``` r
ggplot() +
  geom_sf(data = nc)
```

![](spatial-vis_files/figure-markdown_github/unnamed-chunk-3-1.png)

You can supply other aesthetics: for polygons, `fill` is most useful:

``` r
ggplot() +
  geom_sf(aes(fill = AREA), data = nc, colour = "white")
```

![](spatial-vis_files/figure-markdown_github/unnamed-chunk-4-1.png)

Note that ggplot2 takes care of setting the aspect ratio correctly.

When you include multiple layers, ggplot2 will take care of ensuring that they all have a common CRS.

``` r
ggplot() +
  geom_sf(data = states) + 
  geom_sf(data = nc)
```

![](spatial-vis_files/figure-markdown_github/unnamed-chunk-5-1.png)

You can also combine with regular layers. `x` and `y` positions are assumed be in the same CRS as the sf object.

``` r
ggplot() +
  geom_sf(data = nc) +
  annotate("point", x = -80, y = 35, colour = "red", size = 4)
```

![](spatial-vis_files/figure-markdown_github/unnamed-chunk-6-1.png)

`coord_sf()`
------------

You'll need to use `coord_sf()` for two reasons:

-   You want to zoom into a specified region of the plot by using `xlim` and `ylim`

    ``` r
    ggplot() +
      geom_sf(data = nc) +
      annotate("point", x = -80, y = 35, colour = "red", size = 4) + 
      coord_sf(xlim = c(-81, -79), ylim = c(34, 36))
    ```

    ![](spatial-vis_files/figure-markdown_github/unnamed-chunk-7-1.png)

-   You want to override to use a specific projection. If you don't specify the `crs` argument, it just uses the one provided in the first layer. The following example uses "USA\_Contiguous\_Albers\_Equal\_Area\_Conic", which has EPSG ID

1.  ``` r
    ggplot() +
      geom_sf(data = states) +
      coord_sf(crs = st_crs(102003))
    ```

    ![](spatial-vis_files/figure-markdown_github/unnamed-chunk-8-1.png "fig:")
