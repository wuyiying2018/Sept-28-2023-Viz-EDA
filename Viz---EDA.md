Viz & EDA
================
Yiying Wu
2023-09-28

``` r
library(tidyverse)
library(ggridges)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

working with NOAA weather data, which is downloaded using
`rnoaa::meteo_pull_monitors` function in the code chunk below; similar
code underlies the weather dataset used elsewhere in the course.

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
      USC00519397 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

``` r
weather_df
```

    ## # A tibble: 2,190 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2021-01-01   157   4.4   0.6
    ##  2 CentralPark_NY USW00094728 2021-01-02    13  10.6   2.2
    ##  3 CentralPark_NY USW00094728 2021-01-03    56   3.3   1.1
    ##  4 CentralPark_NY USW00094728 2021-01-04     5   6.1   1.7
    ##  5 CentralPark_NY USW00094728 2021-01-05     0   5.6   2.2
    ##  6 CentralPark_NY USW00094728 2021-01-06     0   5     1.1
    ##  7 CentralPark_NY USW00094728 2021-01-07     0   5    -1  
    ##  8 CentralPark_NY USW00094728 2021-01-08     0   2.8  -2.7
    ##  9 CentralPark_NY USW00094728 2021-01-09     0   2.8  -4.3
    ## 10 CentralPark_NY USW00094728 2021-01-10     0   5    -1.6
    ## # ℹ 2,180 more rows

## Basic scatterplot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />
Pipes and stuff

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

``` r
ggp_nyc_weather=
  weather_df |>
    filter(name=="CerntralPark_NY")|>
    ggplot(aes(x = tmin, y = tmax)) + 
    geom_point()
```

## Fancy Plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name))
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />
`geom_smooth`: add a smooth curve

`alpha = .5`:make the data points a bit transparent

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

`facet_grid(.~name)`: Plot with facets

``` r
ggplot(weather_df, aes(x = tmin, y = tmax,color = name)) + 
  geom_point(alpha = .3) +
  geom_smooth() +
  facet_grid(.~name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />
Let’s try a different plot.

`size = prcp`: higher `prcp` will have bigger points

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = .5) +
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

try assigning a specific color

``` r
weather_df |>
  filter(name=="CentralPark_NY") |>
  ggplot(aes(x=date,y=tmax))+
  geom_point(color="blue") 
```

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

``` r
weather_df |>
  filter(name!="CentralPark_NY") |>
  ggplot(aes(x=date,y=tmax,color=name))+
  geom_point(alpha=.7,size=.5) 
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-10-2.png" width="90%" />

`geom_hex`: add density -\> the plot will not be messy with too many
points

``` r
weather_df|>
  ggplot(aes(x=tmin,y=tmax))+
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_binhex()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

## univaria plotting

histogram

``` r
ggplot(weather_df, aes(x = tmax)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

``` r
ggplot(weather_df, aes(x = tmax,color=name)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-12-2.png" width="90%" />

``` r
ggplot(weather_df, aes(x = tmax,fill=name)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-12-3.png" width="90%" />

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_histogram(position = "dodge", binwidth = 2)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-12-4.png" width="90%" />

density plots

`adjust` in density plots is similar to the binwidth parameter in
histograms, and it helps to try a few values.

`alpha`: the transparency level

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_density(alpha = .4, adjust = .5, color = "blue")
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_density(alpha = .4, adjust = 2, color = "blue")
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-13-2.png" width="90%" />

Boxplots

``` r
ggplot(weather_df, aes(x = name, y = tmax)) + 
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

violin plots

``` r
ggplot(weather_df, aes(x = name, y = tmax)) + 
  geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />

``` r
ggplot(weather_df, aes(x = name, y = tmax)) + 
  geom_violin(aes(fill = name), alpha = .5) + 
  stat_summary(fun = "median", color = "blue")
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

    ## Warning: Removed 17 rows containing non-finite values (`stat_summary()`).

    ## Warning: Removed 3 rows containing missing values (`geom_segment()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-15-2.png" width="90%" />

ridge plot

``` r
ggplot(weather_df, aes(x = tmax, y = name)) + 
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />

``` r
weather_df|>
  filter(name=="CentralPark_NY")|>
  ggplot(aes(x = date, y = tmax))+
  geom_line(alpha=.5)+
  geom_point(size=.5)
```

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-17-1.png" width="90%" />

## Saving and embedding plots

``` r
ggp_weather = 
  ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) 

ggsave("results/ggp_weather.pdf", ggp_weather, width = 8, height = 5)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

``` r
ggp_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz---EDA_files/figure-gfm/unnamed-chunk-19-1.png" width="90%" />

including the following in a code snippet at the outset of my R Markdown
documents.

    knitr::opts_chunk$set(
      fig.width = 6,
      fig.asp = .6,
      out.width = "90%"
    )
