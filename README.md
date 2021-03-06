week 4 hw
================
Madison Dunn
9/30/2021

Github repo: <https://github.com/madisondunn8/433-week4hw>

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

# Introduction

In general, it appears that you should fly earlier in the day if you
want to avoid delays as much as possible. This makes sense intuively, as
once flights begin getting delayed, flights that occur after those
delayed flights will also likely get delayed - somewhat of a domino
effect. After investigating the flights data more, it appears that other
variables have a relationship with when to fly to avoid delays,
including carrier, month of the year, and weather patterns, specifically
average visibility.

``` r
flights %>%
  group_by(hour) %>%
  summarise(avg_arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(avg_arr_delay) %>% 
  ggplot(aes(hour, avg_arr_delay)) +
  geom_jitter()
```

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

# First Finding

The first pattern I noticed was between the time of average arrival
delay and flight carrier. In general, you should still fly earlier in
the day because as the day goes on, average arrival delays increase.
However, some carriers tend to have a different effect on when to fly.
For example, avoid carriers FL, B6, and EV, especially in the later half
of the day because they have higher average arrival delays - almost all
the data points are positive, which means on average they do have
arrival delays.. Carriers VX, US, and AA in the first half of the day
tend to arrive earlier than expected, since they have negative average
arrival delays. These carriers are better to fly through to avoid
arrival delays.

``` r
flights %>%
  group_by(hour, carrier) %>%
  summarise(avg_arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(avg_arr_delay) %>% 
  ggplot(aes(x=hour, y = avg_arr_delay)) +
  geom_point()+
    facet_wrap(~carrier)+
  ylim(NA,40) +
  geom_hline(yintercept=0)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the `.groups` argument.

    ## Warning: Removed 4 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

# Second finding

Another pattern I noticed was the relationship between time of day for
average arrival delays and month of year. Flights around the holidays
(December = 12, January = 1) seem to have longer average arrival delays
than flights during the rest of the year. Therefore month of the year
could affect delays. You should fly in the mornings not during the
holiday months if possible to avoid delays. Flying early in the day in
the fall, such as September (9) and October (10), flights tend to have
negative average arrival delays, meaning they tend to arrive early
rather than be delayed.

``` r
flights %>%
  group_by(hour, month) %>%
  summarise(avg_arr_delay = mean(arr_delay, na.rm = TRUE)) %>%
  arrange(avg_arr_delay) %>% 
  ggplot(aes(x=hour, y = avg_arr_delay)) +
  geom_jitter()+
    facet_wrap(~month) +
 scale_y_continuous() +
    ylim(NA,40) +
    geom_hline(yintercept=0)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the `.groups` argument.

    ## Scale for 'y' is already present. Adding another scale for 'y', which will
    ## replace the existing scale.

    ## Warning: Removed 4 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

# Third finding

The third finding I noticed was the relationship between weather,
specifically visibility, and time of day for average arrival delay of
flights. In general, the pattern appears to persist between time of day
and average arrival day. As the day goes on, average arrival delay
increases as well. However, through looking at weather patterns,
specifically average visability, flights with higher average visability
tend to have a lower average arrival time. This can be seen by the more
light blue colored dots, which correlate to high average visibility.
Darker colored dots, such as those above 75 minute average arrival
delates, tend to have much lower average visability. As though day goes
on, there is still a slight upward pattern of average arrival delay.
Therefore to avoid delays, you would want to fly on days with higher
visibility, in the morning or earlier part of the day.

``` r
left_join(flights, weather) %>%
  group_by(visib, hour) %>%
  summarise(avg_arr_delay = mean(arr_delay, na.rm = TRUE),avg_visib=mean(visib)) %>%
  arrange(avg_arr_delay) %>% 
  ggplot(aes(hour, avg_arr_delay)) +
    geom_jitter(aes(color = avg_visib), size=2)
```

    ## Joining, by = c("year", "month", "day", "origin", "hour", "time_hour")

    ## `summarise()` has grouped output by 'visib'. You can override using the `.groups` argument.

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](README_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
