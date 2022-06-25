
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ggblanket <img src="man/figures/logo.png" align="right" width="120" />

<!-- badges: start -->

[![CRAN
status](https://www.r-pkg.org/badges/version/ggblanket)](https://CRAN.R-project.org/package=ggblanket)
[![CRAN RStudio mirror
downloads](https://cranlogs.r-pkg.org/badges/grand-total/ggblanket?color=lightgrey)](https://r-pkg.org/pkg/ggblanket)
[![CRAN RStudio mirror
downloads](https://cranlogs.r-pkg.org/badges/last-month/ggblanket?color=lightgrey)](https://r-pkg.org/pkg/ggblanket)
[![CRAN RStudio mirror
downloads](https://cranlogs.r-pkg.org/badges/last-week/ggblanket?color=lightgrey)](https://r-pkg.org/pkg/ggblanket)
[![CRAN RStudio mirror
downloads](https://cranlogs.r-pkg.org/badges/last-day/ggblanket?color=lightgrey)](https://r-pkg.org/pkg/ggblanket)
<!-- badges: end -->

## Overview

{ggblanket} is a package of wrapper functions around the amazing
{ggplot2} package.

The objective is to **simplify beautiful {ggplot2} visualisation**.

With this in mind, the {ggblanket} package:

1.  uses `gg_*` functions that each wrap a `ggplot2::ggplot` call with a
    single `ggplot2::geom_*` function
2.  merges col and fill aesthetics into a single col aesthetic
3.  provides colour customisation via pal and alpha arguments
4.  treats faceting as an aesthetic
5.  provides good-looking default x and y scales
6.  provides prefixed arguments for customisable scale adjustment
7.  arranges horizontal geom y and col labels etc to be in correct order
8.  converts unspecified titles to snakecase::to_sentence by default
9.  provides access to all of the relevant geom arg’s through the dots
    argument
10. supports ggplotly use.

## Installation

Install either from CRAN with:

``` r
install.packages("ggblanket")
```

Or install the development version with:

``` r
# install.packages("devtools")
devtools::install_github("davidhodge931/ggblanket")
```

## Website

Click [here](https://davidhodge931.github.io/ggblanket/) for the
{ggblanket} website.

## Examples

``` r
library(dplyr)
library(ggplot2)
library(ggblanket)
library(snakecase)
library(palmerpenguins)
```

1.  {ggblanket} uses `gg_*` functions that each wrap a `ggplot2::ggplot`
    call with a single `ggplot2::geom_*` function.

``` r
iris %>%
  gg_point(x = Sepal.Width, y = Sepal.Length, col = Species)
```

![](man/figures/README-unnamed-chunk-3-1.png)<!-- -->

2.  {ggblanket} merges col and fill aesthetics into a single col
    aesthetic.

``` r
penguins %>% 
  gg_histogram(x = body_mass_g, col = species) 
```

![](man/figures/README-unnamed-chunk-4-1.png)<!-- -->

3.  {ggblanket} provides colour customisation via pal and alpha
    arguments.

``` r
penguins %>% 
  gg_density(x = body_mass_g, col = species, 
             pal = pals::brewer.dark2(3), 
             alpha = 0.5)
```

![](man/figures/README-unnamed-chunk-5-1.png)<!-- -->

4.  {ggblanket} treats faceting as an aesthetic.

``` r
penguins %>% 
  tidyr::drop_na() %>% 
  gg_violin(x = sex, y = body_mass_g, facet = species)
```

![](man/figures/README-unnamed-chunk-6-1.png)<!-- -->

5.  {ggblanket} provides good-looking default x and y scales.

For where:

-   x categorical and y numeric/date: y_limits default to min/max of
    y_breaks with y_expand of c(0, 0)
-   y categorical and x numeric/date: x_limits default to min/max of
    x_breaks with x_expand of c(0, 0)
-   x numeric/date and y numeric/date: y_limits default to min/max of
    y_breaks with y_expand of c(0, 0), and x_limits default to NULL
    (i.e. min/max of x variable) and x_expand of c(0.025, 0.025)

``` r
storms %>%
  group_by(year) %>%
  filter(between(year, 1980, 2020)) %>%
  summarise(wind = mean(wind, na.rm = TRUE)) %>%
  gg_col(
    x = year,
    y = wind,
    y_include = 0,
    title = "Storm wind speed",
    subtitle = "USA average storm wind speed, 1980\u20132020",
    x_title = "Year",
    y_title = "Wind speed (knots)",
    caption = "Source: NOAA",
    theme = gg_theme(y_grid = TRUE)
  ) 
```

![](man/figures/README-unnamed-chunk-7-1.png)<!-- -->

5.  {ggblanket} provides prefixed arguments for customisable scale
    adjustment.

This is designed to work with the Rstudio autocomplete to help you find
the adjustment you need.

Available arguments are `*_limits`, `*_expand`, `*_labels`, `*_breaks`,
`*_include`, and `col_intervals` arguments.

``` r
penguins %>%
  gg_jitter(
    x = species,
    y = body_mass_g,
    col = flipper_length_mm,
    position = position_jitter(width = 0.2, height = 0, seed = 123), 
    col_intervals = ~ santoku::chop_quantiles(.x, probs = seq(0, 1, 0.25)),
    col_legend_place = "b",
    y_breaks = scales::breaks_width(1500), 
    y_labels = scales::label_number()
    )
```

![](man/figures/README-unnamed-chunk-8-1.png)<!-- -->

7.  {ggblanket} arranges horizontal geom y and col labels etc to be in
    correct order.

``` r
penguins %>%
  tidyr::drop_na() %>% 
  group_by(species, sex, island) %>%
  summarise(body_mass_kg = mean(body_mass_g) / 1000) %>%
  gg_col(x = body_mass_kg, y = species, col = sex, facet = island,
    position = "dodge", 
    col_legend_place = "b", 
    col_labels = to_sentence_case)
```

![](man/figures/README-unnamed-chunk-9-1.png)<!-- -->

8.  {ggblanket} converts unspecified titles to snakecase::to_sentence by
    default.

``` r
penguins %>%
  group_by(species, sex) %>%
  summarise(across(body_mass_g, ~ round(mean(.x, na.rm = TRUE)), 0)) %>% 
  gg_tile(sex, species, col = body_mass_g, 
          x_labels = to_sentence_case,
          pal = pals::brewer.blues(9),
          width = 0.9, 
          height = 0.9, 
          title = "Average penguin body mass",
          subtitle = "Palmer Archipelago, Antarctica",
          theme = gg_theme(pal_axis = "#ffffff", pal_ticks = "#ffffff")) +
  geom_text(aes(label = body_mass_g), col = "#232323") 
```

![](man/figures/README-unnamed-chunk-10-1.png)<!-- -->

9.  {ggblanket} provides access to all of the relevant geom arg’s
    through the dots argument.

``` r
penguins %>%
  tidyr::drop_na() %>% 
  gg_smooth(
    x = bill_length_mm,
    y = flipper_length_mm,
    col = species,
    level = 0.99
    ) 
```

![](man/figures/README-unnamed-chunk-11-1.png)<!-- -->

10. {ggblanket} supports ggplotly use.

`ggplotly` won’t work in all situations, and with all functions and
arguments. But it does work a lot of the time.

``` r
iris %>% 
  add_tooltip_text() %>% 
  gg_point(x = Sepal.Width, 
           y = Sepal.Length, 
           col = Species, 
           text = text, 
           theme = gg_theme("helvetica", y_grid = TRUE)) %>% 
  plotly::ggplotly(tooltip = "text")
```

![](man/figures/ggplotly_screenshot.png)
