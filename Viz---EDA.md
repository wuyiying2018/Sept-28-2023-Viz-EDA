Viz & EDA
================
Yiying Wu
2023-09-28

``` r
library(tidyverse)
library(ggridges)
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

![](Viz---EDA_files/figure-gfm/unnamed-chunk-4-1.png)<!-- --> Pipes and
stuff

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz---EDA_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

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

![](Viz---EDA_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
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

![](Viz---EDA_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

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

![](Viz---EDA_files/figure-gfm/unnamed-chunk-8-1.png)<!-- --> Let’s try
a different plot.

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

![](Viz---EDA_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

try assigning a specific color

``` r
weather_df |>
  filter(name=="CentralPark_NY") |>
  ggplot(aes(x=date,y=tmax))+
  geom_point(color="blue") 
```

![](Viz---EDA_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
weather_df |>
  filter(name!="CentralPark_NY") |>
  ggplot(aes(x=date,y=tmax,color=name))+
  geom_point(alpha=.7,size=.5) 
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz---EDA_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->
