ggplot 2
================

## create the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/shaoanran/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-10-06 15:44:42

    ## file min/max dates: 1869-01-01 / 2019-10-31

    ## file path:          /Users/shaoanran/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-10-06 15:45:08

    ## file min/max dates: 1965-01-01 / 2019-10-31

    ## file path:          /Users/shaoanran/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-10-06 15:45:13

    ## file min/max dates: 1999-09-01 / 2019-10-31

recode: rename the station code. cache: save the outcome and don’t have
to be re downloaded again.

## create a gglot

basic scatter plots

``` r
ggplot(weather_df,aes(x = tmin, y =tmax)) + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

## alternate way of making this plot

start with a data, then do others, such as ggplot, filter…..

Advanced Scatter plots

``` r
weather_df %>% 
  ggplot(aes(x =tmin, y = tmax)) + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

saving initial plots, mostly don’t do this

``` r
weather_df %>% 
  filter(name == "CentralPark_NY")
```

    ## # A tibble: 365 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows

scatter plot

``` r
scatterplot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
scatterplot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

adding color

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = 0.4 )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- --> alpha: degree
of transparent (0-1).

why do `aes` positions matter?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = 0.4 ) + 
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
 ##   geom_smooth(se = false) is used to turn off the line 
 ##  ggplot(aes(x = tmin, y = tmax, color = name)), if i put the color under gglot, both geom_point and geom_smooth will have the same color.(the color will be applied everywhere.)
```

the central line is used to describe the trend.

facet\! separate into three figures\!\!\!\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(aes(color = name), alpha = 0.4 ) + 
  geom_smooth(se = FALSE) +
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
  ## separate into three figures!!!!
```

temperature over the day of year in three locations, with precipitation.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = 0.4) +
  ## we can ignore this step if we wanna have only geom_smooth
  geom_smooth(se = FALSE, size = 2)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

2d density

``` r
# install. packages("hexbin") for geom_hex 
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_bin2d() +
  ## same things, but little squares. 
  facet_grid(~name)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_bin2d).

![](Viz2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## more kinds of plots\!\!

univariate plots

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) + 
  geom_histogram() + 
  facet_grid(~name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](Viz2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

density plots

``` r
weather_df %>% 
  ggplot(aes(x = tmax, color = name, fill = name)) + 
  geom_density(alpha = 0.3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](Viz2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmax)) + 
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](Viz2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmax)) + 
  geom_violin(fill = "yellow")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](Viz2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

ridge plots\!\!\!

``` r
weather_df %>% 
  ggplot(aes(x = tmax, y = name)) + 
  ggridges::geom_density_ridges()
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](Viz2_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

## saving a plot

``` r
ggp_ridge_temp = 
  weather_df %>% 
  ggplot(aes(x = tmax, y = name)) + 
  geom_density_ridges()

ggsave("ggplot_temp_ridge.pdf", ggp_ridge_temp)
```

    ## Saving 7 x 5 in image

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

there is difference between how r is creating a plot and how that get
embeded inside here Setting the width of figure and fit them into the
reports\!\!\!\!\!\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(aes(color = name), alpha = 0.4 ) + 
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

## making new plots

start with the old plots

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point (alpha = 0.5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

add label:

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point (alpha = 0.5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (c)",
    y = "Maximum Temp (c)",
    caption = "data from noaa via rnoaa package"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

x axis tick marks etc.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point (alpha = 0.5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (c)",
    y = "Maximum Temp (c)",
    caption = "data from noaa via rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15,-5,20),
    labels = c("-15C", "-5 still too cold)", "20")
    ) + 
    scale_y_continuous(
       trans = "sqrt", 
       position = "right"
    )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
  ## scale_y_sqrt() does the same thing. 
```

ggplot have some default things. we can do something on scales
ourselves.

## colors

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point (alpha = 0.5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (c)",
    y = "Maximum Temp (c)",
    caption = "data from noaa via rnoaa package"
  )+
  scale_color_hue(
  name = "Weather Station",
  h = c(1, 250)
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-21-1.png)<!-- --> (100,300) is
the color hue range.

viridis: better than built-in.

``` r
ggp_base =
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point (alpha = 1) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (c)",
    y = "Maximum Temp (c)",
    caption = "data from noaa via rnoaa package"
  )+
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )

ggp_base
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

``` r
ggp_base + 
  theme_bw() +
  theme(legend.position =  "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

``` r
ggp_base + 
  theme_minimal() +
  theme(legend.position =  "bottom")  
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-24-1.png)<!-- --> order matters.

where is the warning coming from? check the missing values\!\!\!\!

``` r
weather_df %>%   
filter(is.na(tmin))
```

    ## # A tibble: 15 x 6
    ##    name       id          date        prcp  tmax  tmin
    ##    <chr>      <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 Waikiki_HA USC00519397 2017-04-17     5  28.3    NA
    ##  2 Waikiki_HA USC00519397 2017-05-09    NA  NA      NA
    ##  3 Waikiki_HA USC00519397 2017-05-26    NA  NA      NA
    ##  4 Waikiki_HA USC00519397 2017-07-19    NA  NA      NA
    ##  5 Waikiki_HA USC00519397 2017-10-07     0  31.1    NA
    ##  6 Waikiki_HA USC00519397 2017-10-09     0  28.9    NA
    ##  7 Waikiki_HA USC00519397 2017-10-10    10  31.7    NA
    ##  8 Waikiki_HA USC00519397 2017-10-12     0  31.1    NA
    ##  9 Waikiki_HA USC00519397 2017-10-13     0  31.1    NA
    ## 10 Waikiki_HA USC00519397 2017-10-16     5  30      NA
    ## 11 Waikiki_HA USC00519397 2017-10-18     0  29.4    NA
    ## 12 Waikiki_HA USC00519397 2017-10-20    13  30.6    NA
    ## 13 Waikiki_HA USC00519397 2017-10-21     0  30      NA
    ## 14 Waikiki_HA USC00519397 2017-10-22     0  30      NA
    ## 15 Waikiki_HA USC00519397 2017-12-22     0  26.7    NA

general rules applied everywhere in the R Markdown.

## separate into different dataset and make them into different geom

``` r
## dataset 1
central_park = 
weather_df %>% 
  filter(name == "CentralPark_NY")

## dataset 2
waikiki =
  weather_df %>% 
  filter(name == "Waikiki_HA")

ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp)) + 
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-26-1.png)<!-- -->

brief aside about colors

``` r
waikiki  %>% 
  ggplot(aes(x=date, y = tmax)) +
  geom_point(alpha = 0.5, color = "blue")
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](Viz2_files/figure-gfm/unnamed-chunk-27-1.png)<!-- --> when want to
give the points color that is not a variable in the dataset, we need to
put it after geom\_point to specify the color.

## multi-panel plots

``` r
ggp_scatter = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_density =
  weather_df %>% 
  ggplot(aes(x = tmin)) + 
  geom_density()
 
ggp_box =
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color =name)) + 
  geom_boxplot()

(ggp_scatter + ggp_density )/ggp_box
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

    ## Warning: Removed 15 rows containing missing values (stat_boxplot).

![](Viz2_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

## data manipulation

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = fct_relevel(name,"Waikiki_HA", "CentralPark_NY")
  ) %>% 
  ggplot(aes(x = tmin, y = tmax, color =name)) + 
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing missing values (stat_boxplot).

![](Viz2_files/figure-gfm/unnamed-chunk-29-1.png)<!-- -->

reorder instead of relevel

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = fct_reorder(name,tmax)
  ) %>% 
  ggplot(aes(x = tmin, y = tmax, color =name)) + 
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing missing values (stat_boxplot).

![](Viz2_files/figure-gfm/unnamed-chunk-30-1.png)<!-- --> reorder is to
order something based on one variable.

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = fct_relevel(name,"Waterhole_WA","Waikiki_HA", "CentralPark_NY")
  ) %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing missing values (stat_boxplot).

![](Viz2_files/figure-gfm/unnamed-chunk-31-1.png)<!-- -->

## restructure then plot

``` r
weather_df %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperature"
  ) %>% 
  ggplot(aes(x = temperature, fill = observation)) +
  geom_density() +
  facet_grid(~name)
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

![](Viz2_files/figure-gfm/unnamed-chunk-32-1.png)<!-- -->

litter and pups

\#\#\#\#\#\`\``{r} pup_data = read_csv("./data/FAS_pups.csv", col_types
= "ciiiii") %>% janitor::clean_names() %>% mutate(sex = recode(sex,`1`=
"male",`2\` = “female”))

litter\_data = read\_csv(“./data/FAS\_litters.csv”, col\_types =
“ccddiiii”) %\>% janitor::clean\_names() %\>% select(-pups\_survive)
%\>% separate(group, into = c(“dose”, “day\_of\_tx”), sep = 3) %\>%
mutate(wt\_gain = gd18\_weight - gd0\_ weight, day\_of\_tx =
as.numeric(day\_of\_tx))

fas\_data = left\_join(pup\_data, litter\_data, by = “litter\_number”)

fas\_data %\>% select(sex, dose, day\_of\_tx, pd\_ears:pd\_walk) %\>%
pivot\_longer( pd\_ears:pd\_walk, names\_to = “outcome”, values\_to =
“pn\_day”) %\>% drop\_na() %\>% mutate(outcome =
forcats::fct\_reorder(outcome, day\_of\_tx, median)) %\>% ggplot(aes(x =
dose, y = pn\_day)) + geom\_violin() + facet\_grid(day\_of\_tx ~
outcome) \`\`\`\#\#\#
