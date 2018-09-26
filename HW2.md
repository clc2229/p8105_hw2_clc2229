Homework 2
================
Christopher Crowe
September 25, 2018

Problem 1
---------

The piping in the following code chunk:

-   reads in the data
-   cleans the variable names
-   chooses a selection of variables of interest
-   converts the entry variable from character to logical

``` r
subway_data = read_csv("./data/NYC_Transit_Subway_Entrance_And_Exit_Data.csv", col_types = "cccddcccccccccccccccccccccccddcc") %>%
  janitor::clean_names() %>%
  select(line, station_name, station_latitude, station_longitude, route1:route11, entry, vending, entrance_type, ada) %>%
  mutate(entry = recode(entry, "YES" = TRUE, "NO" = FALSE))
```

As a result of the above code chunk, the data frame `subway_data` contains the following variables: `line`, `station_name`, `station_latitude`, `station_longitude`, `route1`, `route2`, `route3`, `route4`, `route5`, `route6`, `route7`, `route8`, `route9`, `route10`, `route11`, `entry`, `vending`, `entrance_type`, and `ada`. The data has been cleaned using the `janitor::clean_names()` function, which transforms all variable names to a format that is more preferable for data science purposes (e.g., `station_name` instead of `Station Name`). At this point, the data frame `subway_data` has the following dimensions: rows = 1868 and columns = 19. While the data have been somewhat cleaned, they are not tidy because we have the route variable spanning multiple columns.

Using this data, we can gather the following information:

``` r
subway_data %>% 
  distinct(line, station_name) %>% 
  nrow()
```

    ## [1] 465

``` r
subway_data %>%
  select(line, station_name, ada) %>% 
  filter(ada == TRUE) %>% 
  distinct(line, station_name) %>% 
  nrow()
```

    ## [1] 84

``` r
subway_data %>% 
  select(line, station_name, vending, entry) %>% 
  filter(vending == "NO") %>% 
  summarize(round(mean(entry), digits = 2))
```

    ## # A tibble: 1 x 1
    ##   `round(mean(entry), digits = 2)`
    ##                              <dbl>
    ## 1                             0.38

-   There are 465 distinct stations.
-   84 stations are ADA compliant.
-   0.38 of all entrances/exits without vending allow entrance.
