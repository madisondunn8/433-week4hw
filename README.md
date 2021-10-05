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
#You should fly earlier in the day because as the day goes on, average arrival delays increase - therefore to find other variables that affect time of day, compare plots of different variables  

flights %>%
  group_by(hour, carrier) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(x=hour, y = arr_delay)) +
  geom_point()+
    facet_wrap(~carrier)+
  ylim(NA,40) +
  geom_hline(yintercept=0)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the `.groups` argument.

    ## Warning: Removed 4 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

``` r
#For flights in the first half of the day, avoid carrier OO, FL, and F9 as they tend to have higher delays, 

flights %>%
  group_by(hour, month) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(x=hour, y = arr_delay)) +
  geom_jitter()+
    facet_wrap(~month) +
 scale_y_continuous() +
    geom_hline(yintercept=0)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the `.groups` argument.

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-2-3.png)<!-- -->

``` r
#For flights in the first half of the day, month also seems to have some kind of relationship. Flights around the holidays (November, December, January) seem to have more arrival delays than flights during the rest of the year. Therefore month of the year could affect delays - fly in the mornings not during the holiday months if possible to avoid delays. 


left_join(flights, weather) %>%
  filter(hour<12) %>% 
  group_by(visib) %>%
  summarise(arr_delay = mean(arr_delay, na.rm = TRUE),avg_visib=mean(visib)) %>%
  arrange(arr_delay) %>% 
  ggplot(aes(avg_visib, arr_delay)) +
    geom_jitter() +
    geom_smooth() 
```

    ## Joining, by = c("year", "month", "day", "origin", "hour", "time_hour")

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).
    
    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-2-4.png)<!-- -->

``` r
#Higher average visibility appears to have a negative relationship with average arrival delays. As visibility increases, flights tend to have lower average arrival delays - this means that as visibility increases, average flight delays tend to decrease. Therefore, to avoid delays, fly in the mornings when visibility is high. 
```
