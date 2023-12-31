Viz Part 1
================
2023-10-03

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggridges)
```

knitr::opts_chunk\$set( fig.width = 6, fig.asp = .6, out.width = “90%” )

Get the data for plotting today

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/victoriaschliep/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-10-03 16:30:07.135344 (8.525)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/victoriaschliep/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-10-03 16:30:13.878957 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/victoriaschliep/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-10-03 16:30:16.554318 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

Let’s make a plot!

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Pipes and stuff. Because it’s tidyverse, you can take whatever dataset
you have and then pipe it into ggplot and define the aesthetics, as long
as the first thing you are putting in is a dataframe.

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

![](viz_part_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
ggp_nyc_weather = 
  weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_nyc_weather
```

![](viz_part_1_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

\##Fancy plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = 0.3) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Plot with facets

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.3) +
  geom_smooth() +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Trying again on a different plot.

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = .3) + 
  geom_smooth() +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values (`geom_point()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

assigning specific colors. Almost never assign colors by hand. Ggplot
assigns some colors by default based on how many options you have.

``` r
weather_df |> 
  filter(name != "CentralPark_NY") |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = .7, size = .5)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

hex plot :-)

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex(alpa = .3)
```

    ## Warning in geom_hex(alpa = 0.3): Ignoring unknown parameters: `alpa`

    ## Warning: Removed 17 rows containing non-finite values (`stat_binhex()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## univariate plotting, for when you just want to look at one variable at a time

histogram…. it is ALWAYS the SAME!! GGplot.. then dataframe.. then what
variable maps onto what axis… then what kind of graph you want !

avoid stacking up bars in histrogram with “dodge”

``` r
ggplot(weather_df, aes(x= tmax, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

let;s use a density plot

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_density(alpha = .3, adjust = .5)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

using boxplots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

violin plots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

ridge plot… I feel like this is on NYTimes a lot …

``` r
ggplot(weather_df, aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

![](viz_part_1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

## saving and embedding plots

``` r
ggplot_weather = 
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax))
```

ggplot_weather

ggsave(“results/ggplotweather.pdf”, ggplot_weather)
