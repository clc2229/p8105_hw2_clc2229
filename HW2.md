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

Using this data, the following code can be used to gather the following information:

``` r
subway_data %>% 
  distinct(line, station_name) %>% 
  nrow()
```

-   There are 465 distinct stations.

``` r
subway_data %>%
  select(line, station_name, ada) %>% 
  filter(ada == TRUE) %>% 
  distinct(line, station_name) %>% 
  nrow()
```

-   84 stations are ADA compliant.

``` r
subway_data %>% 
  select(line, station_name, vending, entry) %>% 
  filter(vending == "NO") %>% 
  summarize(round(mean(entry), digits = 2))
```

-   0.38 of all entrances/exits without vending allow entrance.

The following code chunks reformat the data frame into a more tidy format.

``` r
formatted_subway_data = subway_data %>% 
  gather(key = route_number, value = route_name, route1:route11)
```

``` r
formatted_subway_data %>% 
  select(line, station_name, route_name) %>% 
  filter(route_name == "A") %>% 
  distinct(line, station_name) %>% 
  nrow()
```

-   There are 60 distinct stations that serve the A train.

``` r
formatted_subway_data %>% 
  select(line, station_name, route_name, ada) %>% 
  filter(route_name == "A" & ada == TRUE) %>% 
  distinct(line, station_name) %>% 
  nrow()
```

-   Of these stations, 17 are ADA compliant.

Problem 2
---------

The piping in the following code chunk:

-   reads in the Mr. Trash Wheel sheet
-   omits columns with notes
-   cleans the variable names
-   omits rows without dumpster-specific data
-   rounds the number of sports balls to the nearest integer and converts it to an interger variable

``` r
trash_wheel = readxl::read_excel("./data/HealthyHarborWaterWheelTotals2017-3.xlsx", "Mr. Trash Wheel", range = cellranger::cell_cols("A:N")) %>% 
  janitor::clean_names() %>% 
  filter(!is.na(dumpster)) %>% 
  mutate(sports_balls = as.integer(round(sports_balls)))
```

The piping in the following code chunk:

-   reads in the 2016 Precipitation and 2017 Precipitation sheets
-   cleans the variable names
-   omits rows without precipitation data
-   adds a variable `year`

``` r
precipitation_16 = readxl::read_excel("./data/HealthyHarborWaterWheelTotals2017-3.xlsx", "2016 Precipitation", range = cellranger::cell_cols("A:B")) %>% 
  janitor::clean_names() %>% 
  filter(!is.na(precipitation_in) & !is.na(x_1) & precipitation_in != "Month") %>% 
  mutate(year = "2016")

precipitation_17 = readxl::read_excel("./data/HealthyHarborWaterWheelTotals2017-3.xlsx", "2017 Precipitation", range = cellranger::cell_cols("A:B")) %>% 
  janitor::clean_names() %>% 
  filter(!is.na(precipitation_in) & !is.na(x_1) & precipitation_in != "Month") %>% 
  mutate(year = "2017")

precipitation_16_17 = bind_rows(precipitation_16, precipitation_17) %>% 
  mutate(precipitation_in = month.name[as.integer(precipitation_in)])
```

In the dataset for 2016, there are 12 observations. In the dataset for 2017, there are 3 observations.
