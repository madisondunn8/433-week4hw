week 4 hw
================
Madison Dunn
9/30/2021

``` r
library(nycflights13)
```

    ## Warning: package 'nycflights13' was built under R version 4.0.5

``` r
library(ggplot2)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

What time of day should you fly if you want to avoid delays as much as
possible? Does this choice depend on anything? Season? Weather? Airport?
Airline? Find three patterns (“null results” are ok\!). Write your
results into Rmarkdown. Include a short introduction that summarizes the
three results. Then, have a section for each finding. Support each
finding with data summaries and visualizations. Include your code when
necessary. This shouldn’t be long, but it might take some time to find
the things you want to talk about and lay them out in an orderly way.

``` r
flights %>%
  group_by(hour) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(hour, arr_delay)) +
  geom_jitter()
```

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
#You should fly earlier in the day because as the day goes on, average arrival delays increase. 

flights %>%
  group_by(carrier) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(x = reorder(carrier, arr_delay), y = arr_delay)) +
  geom_bar(stat='identity')
```

![](README_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

``` r
flights %>%
  group_by(month) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(x=month,y=arr_delay)) +
    geom_bar(stat='identity')
```

![](README_files/figure-gfm/unnamed-chunk-2-3.png)<!-- -->

``` r
left_join(flights, weather) %>%
  group_by(wind_speed) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(wind_speed, arr_delay)) +
    geom_jitter() +
    geom_smooth()
```

    ## Joining, by = c("year", "month", "day", "origin", "hour", "time_hour")

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).
    
    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-2-4.png)<!-- -->
