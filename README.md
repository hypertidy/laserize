
<!-- README.md is generated from README.Rmd. Please edit that file -->

# laserize

<!-- badges: start -->

[![R-CMD-check](https://github.com/hypertidy/laserize/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/hypertidy/laserize/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

The goal of laserize is to rasterize without materializing any pixel
values.

WARNING: does not do anything yet, it’s just me learning C++. Do not
use, VERY VERY WIP

This is an expression of my WISHUE “cell abstraction”
[fasterize/issues/11](https://github.com/ecohealthalliance/fasterize/issues/11).

-   [x] copy logic from fasterize, and remove Armadillo array handling
-   [x] remove use of raster objects, in favour of input extent and
    dimension
-   [x] remove all trace of the raster package
-   [x] implement return of the ‘yline, xpix’ and polygon ID info to
    user (see below)
-   [ ] make return of ylin,xpix structure efficient (currently growing
    the vector)
-   [ ] move to cpp11
-   [ ] rasterize lines and points
    [fasterize/issues/30](https://github.com/ecohealthalliance/fasterize/issues/30)
-   [ ] consider formats other than sf (wk, geos, grd, rct, triangles
    etc.)

## Outputs

-   two options, record presence of polygon OR ID of polygon
-   a row-indexed (*yline*) set of edge instances (start, end *xpix*)
    along scanlines with the two options
-   tools to format this meaningfully, and plot lazily
-   tools to materialize as actual raster data

For the record, I wanted this facility before I read this issue - but
here’s a real world example, discussed in PROJ for very fast lookup for
large non-materialized (highly compressed) grids by Thomas Knudsen:

<https://github.com/OSGeo/PROJ/issues/1461#issuecomment-491501992>

## Installation

You can install the development version of laserize like so:

``` r
# FILL THIS IN! HOW CAN PEOPLE INSTALL YOUR DEV PACKAGE?
```

## Example

This is a basic example, currently the CPP code is slow because it grows
the output vectors, and the matrix orientation used below is not the
intended end point. But, just shows this works. See the todo list above.

``` r
pdata <-
  structure(list(sfg_id = c(1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3),
                 polygon_id = c(1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3),
                 linestring_id = c(1, 1, 1, 1, 1, 2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1),
                 x = c(-180, -140, 10, -140, -180, -150, -100, -110, -150, -10, 140, 160, 140, -10, -125, 0, 40, 15, -125),
                 y = c(-20, 55, 0, -60, -20, -20, -10, 20, -20, 0, 60, 0, -55, 0, 0, 60, 5, -45, 0)),
            class = "data.frame", row.names = c(NA, 19L))

pols <- sfheaders::sf_polygon(pdata, x= "x", y = "y", polygon_id = "polygon_id", linestring_id = "linestring_id")
## define a raster (xmin, xmax, ymin, ymax), (ncol, nrow)
ext <- c(range(pdata$x), range(pdata$y))
dm <- c(50, 40)
r <- laserize:::laserize(pols, extent = ext,
                               dimension = dm)

## our index is pairs of y, x where the polygon edge was detected - 
## this essentially an rle by scanline of start,end polygon coverage
index <- matrix(r, ncol = 3L, byrow = TRUE)

str(index)
#>  int [1:125, 1:3] 6 5 5 5 5 5 4 4 4 4 ...
## see tests for current code, there's still investigation of an out by one thing 
```

## Code of Conduct

Please note that the laserize project is released with a [Contributor
Code of
Conduct](https://contributor-covenant.org/version/2/1/CODE_OF_CONDUCT.html).
By contributing to this project, you agree to abide by its terms.
