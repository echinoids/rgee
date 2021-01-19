<h1 align="center">
  <br>
  <a href="https://r-spatial.github.io/rgee/"><img src="https://raw.githubusercontent.com/r-spatial/rgee/master/man/figures/logo.png" alt="Markdownify" width="187"></a>
  &nbsp;
  &nbsp;
  &nbsp;
  <a href="http://r-earthengine.github.io/"><img src="https://user-images.githubusercontent.com/16768318/105011770-a0127080-5a3d-11eb-961c-623b23399b01.png" alt="Markdownify" width="200"></a>
  <br>
  Google Earth Engine for R
  <br>
</h1>

<h4 align="center">An R binding package for calling <a href="https://developers.google.com/earth-engine/" target="_blank">Google Earth Engine API</a> from within R. <a href="https://r-spatial.github.io/rgee/reference/index.html" target="_blank">Several functions</a> have been implemented to make simple the connection with the R spatial ecosystem. The current version of rgee has been built considering the <a href="https://pypi.org/project/earthengine-api/0.1.246/" target="_blank">earthengine-api 0.1.247</a>.</h4>

<p align="center">
<a href="https://colab.research.google.com/github/r-spatial/rgee/blob/examples/rgee_colab.ipynb"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open in Colab" title="Open and Execute in Google Colaboratory"></a>
<a href="https://github.com/r-spatial/rgee/actions"><img src="https://github.com/r-spatial/rgee/workflows/R-CMD-check/badge.svg" alt="R build status"></a>
<a href="https://www.repostatus.org/#active"><img src="https://www.repostatus.org/badges/latest/active.svg" alt="Project Status: Active – The project has reached a stable, usable
state and is being actively
developed."></a>
<a href="https://codecov.io/gh/r-spatial/rgee"><img src="https://codecov.io/gh/r-spatial/rgee/branch/master/graph/badge.svg" alt="codecov"></a>
<a href="https://opensource.org/licenses/Apache-2.0"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="License"></a>
<a href="https://www.tidyverse.org/lifecycle/#maturing"><img src="https://img.shields.io/badge/lifecycle-maturing-blue.svg" alt="lifecycle"></a>
<a href="https://joss.theoj.org/papers/aea42ddddd79df480a858bc1e51857fc"><img src="https://joss.theoj.org/papers/aea42ddddd79df480a858bc1e51857fc/status.svg" alt="status"></a>
<a href="https://cran.r-project.org/package=rgee"><img src="https://www.r-pkg.org/badges/version/rgee" alt="CRAN
status"></a>
<a href="https://doi.org/10.5281/zenodo.3945409"><img src="https://zenodo.org/badge/DOI/10.5281/zenodo.3945409.svg" alt="DOI"></a>
</p>

<p align="center">  
  • 
  <a href="#installation">Installation</a> &nbsp;•  
  <a href="#hello-world">Hello World</a> &nbsp;•
  <a href="#how-does-rgee-work">How does rgee work?</a> &nbsp;•
  <a href="#quick-start-users-guide-for-rgee">Guides</a> &nbsp;•
  <a href="#contributing-guide">Contributing</a> &nbsp;•
  <a href="#share-the-love">Citation</a> &nbsp;•
  <a href="#credits">Credits</a>  
</p>

## What is Google Earth Engine?

[Google Earth Engine](https://earthengine.google.com/) is a cloud-based platform that allows users to have an easy access to a petabyte-scale archive of remote sensing data and run geospatial analysis on Google’s infrastructure. Currently, Google offers support only for Python and JavaScript. `rgee` will fill the gap **starting to provide support to R\!**. Below you will find the comparison between the syntax of `rgee` and the two Google-supported client libraries.

<table>
<tr>
<th> JS (Code Editor) </th>
<th> Python </th>
<th> R </th>
</tr>
<tr>
<td>
  
``` javascript
var db = 'CGIAR/SRTM90_V4'
var image = ee.Image(db)
print(image.bandNames())
#> 'elevation'
```

</td>
<td>

``` python
import ee
ee.Initialize()
db = 'CGIAR/SRTM90_V4'
image = ee.Image(db)
image.bandNames().getInfo()
#> [u'elevation']
```

</td>
<td>

``` r
library(rgee)
ee_Initialize()
db <- 'CGIAR/SRTM90_V4'
image <- ee$Image(db)
image$bandNames()$getInfo()
#> [1] "elevation"
```
</td>
</tr>
</table>

**Quite similar, isn’t it?**. However, there are additional smaller changes should consider when using Google Earth Engine with R. Please check the [consideration section](https://r-spatial.github.io/rgee/articles/considerations.html) before you start coding\!

## Installation

Install the `rgee` package from GitHub is quite simple, you just have to run in your R console as follows:

``` r
remotes::install_github("r-spatial/rgee")
```

**`rgee` depends on [reticulate](https://rstudio.github.io/reticulate/articles/package.html)** because it has some Python dependencies (i.e. [numpy](https://pypi.org/project/numpy/) and [ee](https://pypi.org/project/earthengine-api/)), run as follows to install them:

``` r
rgee::ee_install()
```

If you are a Windows user **reticulate requires that uses miniconda/anaconda**. **The use of `rgee::ee_install()` is not mandatory,** you can count on with your own custom installation. This would be also allowed. If you are a Rstudio v.1.4 > user, this [tutorial](https://github.com/r-spatial/rgee/tree/help/rstudio) will help you to properly set a Python Environment with your R session without use `rgee::ee_install()`. Take into account that the Python Environment you set must have installed the Earth Engine Python API and Numpy.

After install `rgee`, you might use the function below for checking the status of rgee.

```r
ee_check() # Check non-R dependencies
```

Also, consider looking at the [setup section](https://r-spatial.github.io/rgee/articles/setup.html) for more information on customizing your Python installation.

## Package Conventions

  - All `rgee` functions have the prefix ee\_. Auto-completion is
    your friend :).
  - Full access to the Earth Engine API with the prefix
    [**ee$…**](https://developers.google.com/earth-engine/).
  - Authenticate and Initialize the Earth Engine R API with
    [**ee\_Initialize**](https://r-spatial.github.io/rgee/reference/ee_Initialize.html). It is necessary once by session!.
  - `rgee` is “pipe-friendly”, we re-exports %\>%, but `rgee` does not require its use.

## Hello World

### 1. Compute the trend of night-time lights ([JS version](https://github.com/google/earthengine-api))

Authenticate and Initialize the Earth Engine R API.

``` r
library(rgee)
ee_Initialize()
```

Adds a band containing image date as years since 1991.

``` r
createTimeBand <-function(img) {
  year <- ee$Date(img$get('system:time_start'))$get('year')$subtract(1991L)
  ee$Image(year)$byte()$addBands(img)
}
```

Map the time band creation helper over the [night-time lights collection](https://developers.google.com/earth-engine/datasets/catalog/NOAA_DMSP-OLS_NIGHTTIME_LIGHTS).

``` r
collection <- ee$
  ImageCollection('NOAA/DMSP-OLS/NIGHTTIME_LIGHTS')$
  select('stable_lights')$
  map(createTimeBand)
```

Compute a linear fit over the series of values at each pixel, visualizing the y-intercept in green, and positive/negative slopes as red/blue.

``` r
col_reduce <- collection$reduce(ee$Reducer$linearFit())
col_reduce <- col_reduce$addBands(
  col_reduce$select('scale'))
ee_print(col_reduce)
```

Create a interactive visualization\! 

``` r
Map$setCenter(9.08203, 47.39835, 3)
Map$addLayer(
  eeObject = col_reduce,
  visParams = list(
    bands = c("scale", "offset", "scale"),
    min = 0,
    max = c(0.18, 20, -0.18)
  ),
  name = "stable lights trend"
)

```

![rgee\_01](https://user-images.githubusercontent.com/16768318/71565699-51e4a500-2aa9-11ea-83c3-9e1d32c82ba6.png)

### 2. Extract precipitation values

Install and load `tidyverse` and `sf` R package, after that, initialize the Earth Engine R API. 

``` r
library(tidyverse)
library(rgee)
library(sf)

ee_Initialize()
```

Read the `nc` shapefile.

``` r
nc <- st_read(system.file("shape/nc.shp", package = "sf"), quiet = TRUE)
```

Map each image from 2001 to extract the monthly precipitation (Pr) from the [Terraclimate
dataset](https://developers.google.com/earth-engine/datasets/catalog/IDAHO_EPSCOR_TERRACLIMATE)

``` r
terraclimate <- ee$ImageCollection("IDAHO_EPSCOR/TERRACLIMATE") %>% 
  ee$ImageCollection$filterDate("2001-01-01", "2002-01-01") %>% 
  ee$ImageCollection$map(function(x) x$select("pr")) %>% # Select only precipitation bands
  ee$ImageCollection$toBands() %>% # from imagecollection to image
  ee$Image$rename(sprintf("%02d",1:12)) # rename the bands of an image
```

Extract monthly precipitation values from the Terraclimate ImageCollection through `ee_extract`. `ee_extract` works
similar to `raster::extract`, you just need to define: the ImageCollection object (x), the geometry (y), and a function to
summarize the values (fun).

``` r
ee_nc_rain <- ee_extract(x = terraclimate, y = nc["NAME"], sf = FALSE)
```

Use ggplot2 to generate a beautiful static plot!

``` r
ee_nc_rain %>%
  pivot_longer(-NAME, names_to = "month", values_to = "pr") %>%
  mutate(month, month=gsub("X", "", month)) %>% 
  ggplot(aes(x = month, y = pr, group = NAME, color = pr)) +
  geom_line(alpha = 0.4) +
  xlab("Month") +
  ylab("Precipitation (mm)") +
  theme_minimal()
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/16768318/81945044-2cbd8280-95c3-11ea-9ef5-fd9f6fd5fe89.png" width=80%>
</p>

### 3. Create an NDVI-animation ([JS version](https://developers.google.com/earth-engine/tutorials/community/modis-ndvi-time-series-animation))

Install and load `sf`, after that, initialize the Earth Engine R API.

``` r
library(magick)
library(rgee)
library(sf)

ee_Initialize()
```

Define the regional bounds of animation frames and a mask to clip the NDVI data by.

``` r
mask <- system.file("shp/arequipa.shp", package = "rgee") %>% 
  st_read(quiet = TRUE) %>% 
  sf_as_ee()
region <- mask$geometry()$bounds()
```

Retrieve the MODIS Terra Vegetation Indices 16-Day Global 1km dataset as an `ee.ImageCollection`
and select the NDVI band.

``` r
col <- ee$ImageCollection('MODIS/006/MOD13A2')$select('NDVI')
```

Group images by composite date

``` r
col <- col$map(function(img) {
  doy <- ee$Date(img$get('system:time_start'))$getRelative('day', 'year')
  img$set('doy', doy)
})
distinctDOY <- col$filterDate('2013-01-01', '2014-01-01')
```

Define a filter that identifies which images from the complete collection match the DOY
from the distinct DOY collection.

``` r
filter <- ee$Filter$equals(leftField = 'doy', rightField = 'doy')
```

Define a join; convert the resulting FeatureCollection to an ImageCollection.

``` r
join <- ee$Join$saveAll('doy_matches')
joinCol <- ee$ImageCollection(join$apply(distinctDOY, col, filter))
```

Apply median reduction among matching DOY collections.

``` r
comp <- joinCol$map(function(img) {
  doyCol = ee$ImageCollection$fromImages(
    img$get('doy_matches')
  )
  doyCol$reduce(ee$Reducer$median())
})
```

Define RGB visualization parameters.

``` r
visParams = list(
  min = 0.0,
  max = 9000.0,
  bands = "NDVI_median",
  palette = c(
    'FFFFFF', 'CE7E45', 'DF923D', 'F1B555', 'FCD163', '99B718', '74A901',
    '66A000', '529400', '3E8601', '207401', '056201', '004C00', '023B01',
    '012E01', '011D01', '011301'
    )
)
```

Create RGB visualization images for use as animation frames.

```r
rgbVis <- comp$map(function(img) {
  do.call(img$visualize, visParams) %>% 
    ee$Image$clip(mask)
})
```

Define GIF visualization parameters.

```r
gifParams <- list(
  region = region,
  dimensions = 600,
  crs = 'EPSG:3857',
  framesPerSecond = 10
)
```

Use ee_utils_gif_* functions to render the GIF animation and add some texts.

```r
animation <- ee_utils_gif_creator(rgbVis, gifParams, mode = "wb")
animation %>% 
  ee_utils_gif_annotate(
    text = "NDVI: MODIS/006/MOD13A2",
    size = 15, color = "white",
    location = "+10+10"
  ) %>% 
  ee_utils_gif_annotate(
    text = dates_modis_mabbr, 
    size = 30, 
    location = "+290+350",
    color = "white", 
    font = "arial",
    boxcolor = "#000000"
  ) # -> animation_wtxt

# ee_utils_gif_save(animation_wtxt, path = "raster_as_ee.gif")
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/16768318/77121867-203e0300-6a34-11ea-97ba-6bed74ef4300.gif">
</p>

## How does rgee work?

`rgee` is **not** a native Earth Engine API like the Javascript or Python client, to do this would be extremely hard, especially considering that the API is in [active development](https://github.com/google/earthengine-api). So, how is it possible to run Earth Engine using R? the answer is [reticulate](https://rstudio.github.io/reticulate/). `reticulate` is an R package designed to allow a seamless interoperability between R and Python. When an Earth Engine **request** is created in R, `reticulate` will transform this piece into Python. Once the Python code is obtained, the `Earth Engine Python API` transform the request to a `JSON` format. Finally, the request is received by the Google Earth Engine Platform thanks to a Web REST API. The **response** will follow the same path. 

![workflow](https://user-images.githubusercontent.com/16768318/71569603-3341d680-2ac8-11ea-8787-4dd1fbba326f.png)

## Quick Start User's Guide for rgee 

<a href="http://amazeone.com.br/barebra/pandora/rgeebookT1eng.pdf"><img src="https://user-images.githubusercontent.com/16768318/88080933-5b1c8880-cb45-11ea-9546-f0640da13997.png" height="99"/></a>
<a href="http://amazeone.com.br/barebra/pandora/rgeebookT1.pdf"><img src="https://user-images.githubusercontent.com/16768318/86457619-8ef45300-bce9-11ea-9f08-7c1ee14071fb.png" height="100"/></a>
<a href="https://barja8.github.io/Handbook_rgee/pdf/vol01.pdf"><img src="https://user-images.githubusercontent.com/16768318/86457622-90be1680-bce9-11ea-92f0-78cfb915c4bc.png" height="101"/></a>

**Created by:**
- EN and POR: Andres Luiz Lima Costa <http://amazeone.com.br/index.php>
- SPA: Antony Barja Ingaruca <https://barja8.github.io/>

## Code of Conduct

Please note that the `rgee` project is released with a [Contributor Code
of Conduct](CODE_OF_CONDUCT.md). By contributing to this project, you agree to abide by its terms.

## Contributing Guide

👍 Thanks for taking the time to contribute\! 🎉👍 Please review our [Contributing Guide](CONTRIBUTING.md).


## Share the love

Think **rgee** is useful? Let others discover it, by telling them in
person via Twitter or a blog post.

Using **rgee** for a paper you are writing? Consider citing it

``` r
citation("rgee")
To cite rgee in publications use:

  C Aybar, Q Wu, L Bautista, R Yali and A Barja (2020) rgee: An R
  package for interacting with Google Earth Engine Journal of Open
  Source Software URL https://github.com/r-spatial/rgee/.

A BibTeX entry for LaTeX users is

  @Article{,
    title = {rgee: An R package for interacting with Google Earth Engine},
    author = {Cesar Aybar and Quisheng Wu and Lesly Bautista and Roy Yali and Antony Barja},
    journal = {Journal of Open Source Software},
    year = {2020},
  }
``` 

## Credits

First off, we would like to offer an **special thanks** :raised_hands: :clap: to [**Justin Braaten**](https://github.com/jdbcode) for his wise and helpful comments in the whole development of **rgee**. As well, we would like to mention the following third-party R/Python packages for contributing indirectly to the develop of rgee:

  - **[gee\_asset\_manager - Lukasz Tracewski](https://github.com/tracek/gee_asset_manager)** 
  - **[geeup - Samapriya Roy](https://github.com/samapriya/geeup)**
  - **[geeadd - Samapriya Roy](https://github.com/samapriya/gee_asset_manager_addon)**  
  - **[cartoee - Kel Markert](https://github.com/KMarkert/cartoee)**
  - **[geetools - Rodrigo E. Principe](https://github.com/gee-community/gee_tools)**
  - **[landsat-extract-gee - Loïc Dutrieux](https://github.com/loicdtx/landsat-extract-gee)**
  - **[earthEngineGrabR - JesJehle](https://github.com/JesJehle/earthEngineGrabR)**
  - **[sf - Edzer Pebesma](https://github.com/r-spatial/sf)**
  - **[stars - Edzer Pebesma](https://github.com/r-spatial/stars)**
  - **[gdalcubes - Marius Appel](https://github.com/appelmar/gdalcubes)**
