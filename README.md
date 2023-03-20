
<!-- README.md is generated from README.Rmd. Please edit that file -->

# rasterior

<!-- badges: start -->
<!-- badges: end -->

The goal of rasterior is to do what Python’s rasterio does in R, or at
least show analogues …

Just me sticking python examples here.

The plots aren’t always working, but the code below is right.

## Installation

You can install the development version of rasterior like so:

``` r
# FILL THIS IN! HOW CAN PEOPLE INSTALL YOUR DEV PACKAGE?
```

## Example

Get data from out there, write it to file.

``` r
reticulate::use_python("/usr/bin/python3.8")
#rasterio <- reticulate::import("rasterio")

gdal <- reticulate::import("osgeo.gdal")

dsn = "vrt:///vsicurl/https://public.services.aad.gov.au/datasets/science/GEBCO_2021_GEOTIFF/GEBCO_2021.tif?ovr=5"
src_ds = gdal$Open(dsn)
unlink("file.tif")
ds <- gdal$Translate("file.tif", src_ds)
rm(ds)
whatarelief:::imfun(vapour:::gdal_raster_data("file.tif"))
#> Loading required namespace: ximage
#> Warning in min(x): no non-missing arguments to min; returning Inf
#> Warning in max(x): no non-missing arguments to max; returning -Inf
```

<img src="man/figures/README-gdal.Open-1.png" width="100%" />

    #> [1] "/vsicurl/https://public.services.aad.gov.au/datasets/science/GEBCO_2021_GEOTIFF/GEBCO_2021.tif"

Get that data into R, and create a new data set, then write that to
file.

``` r
dsn = "vrt:///vsicurl/https://public.services.aad.gov.au/datasets/science/GEBCO_2021_GEOTIFF/GEBCO_2021.tif?ovr=5"
src_ds = gdal$Open(dsn)
band <- src_ds$GetRasterBand(1L)
ar <- band$ReadAsArray()
ar <- matrix(as.numeric(ar), dim(ar)[1L])
dm <- dim(ar)
ds <- gdal$GetDriverByName("MEM")$Create("", dm[2], dm[1],  
                                            1L, ## 1 band
                                            gdal$GDT_Float64)
ds$SetGeoTransform(affinity::extent_dim_to_gt(c(-180, 180, -90, 90), dm[2:1]))
#> [1] 0
ds$SetProjection("OGC:CRS84")
#> [1] 0
## notice this transpose
ds$GetRasterBand(1L)$WriteArray(ar)
#> [1] 0
unlink("file2.tif")
gdal$Translate("file2.tif", ds)
#> <osgeo.gdal.Dataset; proxy of <Swig Object of type 'GDALDatasetShadow *' at 0x7f074fa9a6c0> >
whatarelief:::imfun(vapour:::gdal_raster_data("file2.tif"))
```

<img src="man/figures/README-gdal.Translate-1.png" width="100%" />

    #> [1] "/vsicurl/https://public.services.aad.gov.au/datasets/science/GEBCO_2021_GEOTIFF/GEBCO_2021.tif"
