
<!-- README.md is generated from README.Rmd. Please edit that file -->

# dataui

<!-- badges: start -->

[![CRAN
status](https://www.r-pkg.org/badges/version/dataui)](https://CRAN.R-project.org/package=dataui)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![Travis build
status](https://travis-ci.org/timelyportfolio/dataui.svg?branch=master)](https://travis-ci.org/timelyportfolio/dataui)
<!-- badges: end -->

`dataui` brings the beautiful interactive visualizations of
[`data-ui`](https://williaster.github.io/data-ui) based on
[`vx`](https://vx-demo.now.sh/) to **R**. Currently the package nearly
fully supports
[`sparkline`](https://williaster.github.io/data-ui/?selectedKind=sparkline&selectedStory=Kitchen%20sink&full=0&addons=0&stories=1&panelRight=0)
and
[`histogram`](https://williaster.github.io/data-ui/?selectedKind=histogram&selectedStory=Playground&full=0&addons=0&stories=1&panelRight=0),
and no you do **not** need to know `React`. You will be writing `React`
without even knowing it.

Yes, I know `data-ui` is in [repo
freeze](https://github.com/williaster/data-ui#repo-freeze-), but I could
not wait for [\#201](https://github.com/williaster/data-ui/issues/201).
The author, now also on the `vx` team, has done an incredible job, so I
am willing (slightly crazy) to invest the time to write this package.
Plus, I sort of “need” it for a live project. Our other
[sparkline](https://omnipotent.net/jquery.sparkline/#s-about) in R is a
little old and tired (but I still very much appreciate the library) and
has been frozen/unmaintained for many years.

## Credits

Thanks so much to Dr. Ken Steif at [Urban
Spatial](http://urbanspatialanalysis.com/about/) for sponsoring the
first phase of this project and inspiring its inception.

## Help

I would love for you to join me on the journey, and I will make sure
that this is a friendly and welcoming place. If money is more abundant
than time for you, then let me know as well.

## Installation

Far from CRAN-worthy but hopefully there eventually,
`remotes::install_github()` for now.

``` r
remotes::install_github("timelyportfolio/dataui")
```

## Articles and Vignettes

I have prioritized vignettes and articles over proper documentation for
now.

[Replicate data-ui
Examples](https://timelyportfolio.github.io/dataui/articles/dataui_replicate_examples.html)

[dataui and
reactable](https://timelyportfolio.github.io/dataui/articles/dataui_reactable.html)

[Tufte in R with
dataui](https://timelyportfolio.github.io/dataui/articles/replicate_tufte_example.html)

## Examples

#### Sparklines

This is is the shortest code example I could create.

    library(dataui)
    dui_sparkline(
      data = rnorm(50),
      components = list(dui_sparklineseries())
    )

I was able to nearly fully replicate all the `data-ui` sparkline
examples. See the
[vignette](https://timelyportfolio.github.io/dataui/articles/dataui_replicate_examples.html).

#### Histogram

Currently there are some examples in
[experiments.R](https://github.com/timelyportfolio/dataui/blob/master/inst/examples/experiments.R).
I should also note that `dataui` histograms also work very well as
sparklines.

Below is a quick example.

    library(dataui)
    
    rn <- rnorm(1000)
    ri <- runif(1000)
    
    dui_histogram(
      binCount = 25,
      components = list(
        dui_barseries(rawData = rn, fill="#000"),
        dui_densityseries(rawData = rn, stroke = "#000"),
        dui_barseries(rawData = ri),
        dui_densityseries(rawData = ri),
        dui_xaxis(),
        dui_yaxis()
      )
    )

#### reactable

And thanks to the fine work of Greg Lin, `dataui` just works with
[`reactable`](https://glin.github.io/reactable/). I would still like to
tighten up the integration though, since the data gets duplicated in
multiple spots. Here is a quick example to prove it. `dataui` should
also work relatively well with other `R` table libraries.

    library(dataui)
    library(dplyr)
    library(reactable)
    
    data <- chickwts %>%
      group_by(feed) %>%
      summarise(weight = list(weight))
    
    binValues = hist(chickwts$weight, breaks=15, plot = FALSE)$breaks
    
    fillColors = scales::brewer_pal(type="qual", palette="Set2")(8)
    
    reactable(
      data,
      columns = list(
        feed = colDef(maxWidth = 100),
        weight = colDef(
          minWidth = 400,
          cell = function(values, index) {
            dui_histogram(
              height = 200,
              width = 400,
              binValues = binValues,
              renderTooltip = htmlwidgets::JS(htmltools::HTML('
    function (_ref) {
      var event = _ref.event,
          datum = _ref.datum,
          data = _ref.data,
          color = _ref.color;
      return React.createElement(
        "div",
        null,
        React.createElement(
          "strong",
          { style: { color: color } },
          datum.bin0,
          " to ",
          datum.bin1
        ),
        React.createElement(
          "div",
          null,
          React.createElement(
            "strong",
            null,
            "count "
          ),
          datum.count
        ),
        React.createElement(
          "div",
          null,
          React.createElement(
            "strong",
            null,
            "cumulative "
          ),
          datum.cumulative.toFixed(0)
        ),
        React.createElement(
          "div",
          null,
          React.createElement(
            "strong",
            null,
            "density "
          ),
          datum.density.toFixed(0)
        )
      );
    }
              ')),
    # reactR::babel_transform equivalent of the above
    #            htmlwidgets::JS(reactR::babel_transform("
    #{({ event, datum, data, color }) => (
    #  <div>
    #    <strong style={{ color }}>{datum.bin0} to {datum.bin1}</strong>
    #    <div><strong>count </strong>{datum.count}</div>
    #    <div><strong>cumulative </strong>{datum.cumulative.toFixed(0)}</div>
    #    <div><strong>density </strong>{datum.density.toFixed(0)}</div>
    #  </div>
    #)}
    #          ")),
              components = list(
                dui_barseries(rawData = values, fill = fillColors[index]),
                dui_densityseries(rawData = values, stroke = fillColors[index], fill = fillColors[index]),
                dui_xaxis()
              )
            )
          }
        )
      ),
      width = 600
    )

If you use `dataui` in the wild, let me know and I will save a spot here
for you.

## Code of Conduct

Please note that the ‘dataui’ project is released with a [Contributor
Code of
Conduct](https://github.com/timelyportfolio/dataui/blob/master/CODE_OF_CONDUCT.md).
By contributing to this project, you agree to abide by its terms.
