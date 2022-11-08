List Columns
================
Lectured by Jeff Goldsmith
2022-11-01

## *Lists*

In R, vectors are limited to a single data class - all elements are
characters, or all numeric, or all logical.

``` r
vec_numeric = 5:8
vec_char = c("My", "name", "is", "Jeff")
vec_logical = c(TRUE, TRUE, TRUE, FALSE)
```

**Lists provide a way to store anything we want.**

Let’s create a list.

``` r
# l is a list of different variables...
l = list(
  vec_numeric = 5:8,
  mat         = matrix(1:8, 2, 4),
  vec_char    = c("My", "name", "is", "Jeff"),
  vec_logical = c(TRUE, TRUE, TRUE, FALSE),
  summary     = summary(rnorm(1000))
)

l
```

    ## $vec_numeric
    ## [1] 5 6 7 8
    ## 
    ## $mat
    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    3    5    7
    ## [2,]    2    4    6    8
    ## 
    ## $vec_char
    ## [1] "My"   "name" "is"   "Jeff"
    ## 
    ## $vec_logical
    ## [1]  TRUE  TRUE  TRUE FALSE
    ## 
    ## $summary
    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    ## -2.65848 -0.65218  0.01797  0.02690  0.72776  3.62195

Different ways to access list items:

``` r
# 1) Use the dollar sign $  - Show me all the values for the variable "vec_numeric" in the list "l".
l$vec_numeric
```

    ## [1] 5 6 7 8

``` r
# 2) Show me the 3rd variable in the list "l".
l[[3]]
```

    ## [1] "My"   "name" "is"   "Jeff"

``` r
# 3) Show me the values for the variable "mat" in the list "l".
l[["mat"]]
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    3    5    7
    ## [2,]    2    4    6    8

## *`for` loops*

This is the basic structure of `for` *loops*:

``` r
input = list(...)
output = list(...)
 for (i in 1:n) {
   output[[i]] = f(input[[i]])
 }
```

``` r
list_norms = 
  list(
    a = rnorm(20, 3, 1),
    b = rnorm(20, 0, 5),
    c = rnorm(20, 10, .2),
    d = rnorm(20, -3, 1)
  )

is.list(list_norms) # Is list_norms a list?
```

    ## [1] TRUE

Now I want to take the mean and sd of the four items (a, b, c, d) in the
list above.

``` r
mean_and_sd = function(x) {
  mean_x = mean(x)
  sd_x = sd(x)

  tibble(
    mean = mean_x, 
    sd = sd_x)
}
```

``` r
mean_and_sd(list_norms[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.99  1.08

``` r
mean_and_sd(list_norms[[2]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.74  6.18

``` r
mean_and_sd(list_norms[["c"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.96 0.209

``` r
mean_and_sd(list_norms[[4]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.94 0.990

``` r
# I think we can do a loop for this...
```

Instead of copy and paste the same argument over and over again, and
change the number of list_norms every time, we can do a `for` *loop*.

``` r
mean_sd = vector("list", length = 4) # The vector type is a list and it has 4 lists.

for(i in 1:4) {
 mean_sd[[i]] = mean_and_sd(list_norms[[i]]) 
}

mean_sd
```

    ## [[1]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.99  1.08
    ## 
    ## [[2]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.74  6.18
    ## 
    ## [[3]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.96 0.209
    ## 
    ## [[4]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.94 0.990

### *`map`*

Goal of *map* is to clarify the *loop* process. If I use `map` I don’t
need to define the output.

This is a basic structure of a `map` function:

``` r
output = map(input, f) # f means function
```

In the examples below, I can use one line of code to get exactly the
same thing as I did with the `for` *loop* above.

``` r
map(list_norms, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.99  1.08
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.74  6.18
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.96 0.209
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.94 0.990

What if I want to use other functions?

``` r
map(list_norms, summary)
```

    ## $a
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   1.349   1.964   2.778   2.988   4.097   4.563 
    ## 
    ## $b
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##  -5.978  -3.666   1.640   1.736   3.794  16.499 
    ## 
    ## $c
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   9.525   9.850   9.956   9.955  10.037  10.443 
    ## 
    ## $d
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -4.7983 -3.4084 -3.0575 -2.9431 -2.1629 -0.9013

``` r
# I can get a summary of mathematical computation.
```

**map variants**…

``` r
map_dbl(list_norms, median) # show all the results in one line instead of showing them one by one separately.
```

    ##         a         b         c         d 
    ##  2.778497  1.640052  9.955560 -3.057483

``` r
df = map_df(list_norms, mean_and_sd) #show the results like a dataframe

df
```

    ## # A tibble: 4 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.99 1.08 
    ## 2  1.74 6.18 
    ## 3  9.96 0.209
    ## 4 -2.94 0.990

``` r
# `map2` is helpful when our function has two arguments.
# Eg.  map2(.x = input_1, .y = input_2, ~func(arg_1 = .x, arg_2 = .y))
```

## List columns

Now that I create a new dataframe called *listcol_df*, and it consists
of two variables. One variable called `name` with four “values” *a*,
*b*, *c*, *d*. Another variable called `samp` that is a list (In this
case, I am using the list which we created above - `list_norms`).

Then I map the `mean_and_sd` function over the list, but how do I save
the results into the same dataframe `listcol_df`?

``` r
listcol_df = 
  tibble(
  name = c("a", "b", "c", "d"),
  samp = list_norms)

listcol_df[["samp"]]
```

    ## $a
    ##  [1] 3.855558 4.119531 2.644479 1.923068 1.348676 3.004401 1.909446 2.870814
    ##  [9] 1.814699 2.686179 4.089031 2.589024 4.324986 4.125742 4.312477 1.977865
    ## [17] 4.563380 1.617882 2.004345 3.976620
    ## 
    ## $b
    ##  [1]  3.4752406 16.4988290  4.6448187  0.3045390  9.0462300  3.0154722
    ##  [7]  1.1268462 14.1708883 -4.6014651 -3.5299240  2.1532573 -2.3165340
    ## [13]  3.5402163 -4.0738347 -5.9781623  3.6702096  4.1643792 -0.9248241
    ## [19] -5.0943281 -4.5640740
    ## 
    ## $c
    ##  [1]  9.949594  9.650935  9.961526 10.055933 10.103820  9.831222  9.855879
    ##  [8] 10.443439  9.944662  9.981929  9.525461  9.895457  9.900262  9.701293
    ## [15] 10.030696  9.833871 10.133267 10.011000  9.974992 10.314927
    ## 
    ## $d
    ##  [1] -1.7868911 -3.0716893 -2.1633583 -3.4472408 -3.1852309 -4.4926582
    ##  [7] -3.3775801 -2.9501457 -3.0432763 -2.7776201 -4.7982965 -3.8178939
    ## [13] -0.9012542 -1.6311747 -3.8791553 -2.1617038 -3.3730999 -3.3953961
    ## [19] -2.9860605 -1.6219507

``` r
map_df(listcol_df[["samp"]], mean_and_sd)
```

    ## # A tibble: 4 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.99 1.08 
    ## 2  1.74 6.18 
    ## 3  9.96 0.209
    ## 4 -2.94 0.990

Use `mutate`!

``` r
listcol_df = 
  listcol_df %>% 
  mutate(
    m_sd = map_df(samp, mean_and_sd)) %>% 
  select(-samp)
```

What about something more realistic?

## Nested data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2022-09-29 10:29:25 (8.401)

    ## file min/max dates: 1869-01-01 / 2022-09-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-29 10:29:32 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-29 10:29:35 (0.95)

    ## file min/max dates: 1999-09-01 / 2022-09-30

Let’s nest within weather stations.

``` r
weather_nest = 
  weather_df %>% 
  nest(data = date:tmin) # Collapsed everything from date to tmin and put them into a new variable "data".
```

Really is a list column!

``` r
weather_nest[["data"]]
```

    ## [[1]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0   8.9   4.4
    ##  2 2017-01-02    53   5     2.8
    ##  3 2017-01-03   147   6.1   3.9
    ##  4 2017-01-04     0  11.1   1.1
    ##  5 2017-01-05     0   1.1  -2.7
    ##  6 2017-01-06    13   0.6  -3.8
    ##  7 2017-01-07    81  -3.2  -6.6
    ##  8 2017-01-08     0  -3.8  -8.8
    ##  9 2017-01-09     0  -4.9  -9.9
    ## 10 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows
    ## 
    ## [[2]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0  26.7  16.7
    ##  2 2017-01-02     0  27.2  16.7
    ##  3 2017-01-03     0  27.8  17.2
    ##  4 2017-01-04     0  27.2  16.7
    ##  5 2017-01-05     0  27.8  16.7
    ##  6 2017-01-06     0  27.2  16.7
    ##  7 2017-01-07     0  27.2  16.7
    ##  8 2017-01-08     0  25.6  15  
    ##  9 2017-01-09     0  27.2  15.6
    ## 10 2017-01-10     0  28.3  17.2
    ## # … with 355 more rows
    ## 
    ## [[3]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01   432  -6.8 -10.7
    ##  2 2017-01-02    25 -10.5 -12.4
    ##  3 2017-01-03     0  -8.9 -15.9
    ##  4 2017-01-04     0  -9.9 -15.5
    ##  5 2017-01-05     0  -5.9 -14.2
    ##  6 2017-01-06     0  -4.4 -11.3
    ##  7 2017-01-07    51   0.6 -11.5
    ##  8 2017-01-08    76   2.3  -1.2
    ##  9 2017-01-09    51  -1.2  -7  
    ## 10 2017-01-10     0  -5   -14.2
    ## # … with 355 more rows

``` r
# Or we can also do this:
weather_nest %>% pull(data)
```

    ## [[1]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0   8.9   4.4
    ##  2 2017-01-02    53   5     2.8
    ##  3 2017-01-03   147   6.1   3.9
    ##  4 2017-01-04     0  11.1   1.1
    ##  5 2017-01-05     0   1.1  -2.7
    ##  6 2017-01-06    13   0.6  -3.8
    ##  7 2017-01-07    81  -3.2  -6.6
    ##  8 2017-01-08     0  -3.8  -8.8
    ##  9 2017-01-09     0  -4.9  -9.9
    ## 10 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows
    ## 
    ## [[2]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0  26.7  16.7
    ##  2 2017-01-02     0  27.2  16.7
    ##  3 2017-01-03     0  27.8  17.2
    ##  4 2017-01-04     0  27.2  16.7
    ##  5 2017-01-05     0  27.8  16.7
    ##  6 2017-01-06     0  27.2  16.7
    ##  7 2017-01-07     0  27.2  16.7
    ##  8 2017-01-08     0  25.6  15  
    ##  9 2017-01-09     0  27.2  15.6
    ## 10 2017-01-10     0  28.3  17.2
    ## # … with 355 more rows
    ## 
    ## [[3]]
    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01   432  -6.8 -10.7
    ##  2 2017-01-02    25 -10.5 -12.4
    ##  3 2017-01-03     0  -8.9 -15.9
    ##  4 2017-01-04     0  -9.9 -15.5
    ##  5 2017-01-05     0  -5.9 -14.2
    ##  6 2017-01-06     0  -4.4 -11.3
    ##  7 2017-01-07    51   0.6 -11.5
    ##  8 2017-01-08    76   2.3  -1.2
    ##  9 2017-01-09    51  -1.2  -7  
    ## 10 2017-01-10     0  -5   -14.2
    ## # … with 355 more rows

### Unnesting

We can also unnest data!

``` r
weather_nest %>% 
  unnest(data) 
```

    ## # A tibble: 1,095 × 6
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
    ## # … with 1,085 more rows

## Linear regression

Suppose we want to fit a simple linear regression for `tmax` and `tmin`
for each station-specific dataframe.

``` r
# I just want to do linear regression using the tmin & tmax variables nested inside the "data" list column, 
# and only the first list.
weather_nest[["data"]][[1]]
```

    ## # A tibble: 365 × 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0   8.9   4.4
    ##  2 2017-01-02    53   5     2.8
    ##  3 2017-01-03   147   6.1   3.9
    ##  4 2017-01-04     0  11.1   1.1
    ##  5 2017-01-05     0   1.1  -2.7
    ##  6 2017-01-06    13   0.6  -3.8
    ##  7 2017-01-07    81  -3.2  -6.6
    ##  8 2017-01-08     0  -3.8  -8.8
    ##  9 2017-01-09     0  -4.9  -9.9
    ## 10 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows

``` r
lm(tmax ~ tmin, data = weather_nest[["data"]][[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather_nest[["data"]][[1]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039

``` r
lm(tmax ~ tmin, data = weather_nest[["data"]][[2]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather_nest[["data"]][[2]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     20.0966       0.4509

``` r
lm(tmax ~ tmin, data = weather_nest[["data"]][[3]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather_nest[["data"]][[3]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.499        1.221

``` r
#very tedious, let's write a function...
```

Let’s write a function.

``` r
# I'll name my function "weather_lm" and when I put in a dataframe (df), run the following function..
weather_lm = function(df) {
  lm(tmax ~ tmin, data = df)
}

# Now test my function:
weather_lm(weather_nest[["data"]][[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039

``` r
# Instead of repeating the function many times for different set of list, use map:
map(weather_nest[["data"]], weather_lm)
```

    ## [[1]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039  
    ## 
    ## 
    ## [[2]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     20.0966       0.4509  
    ## 
    ## 
    ## [[3]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.499        1.221

How do we store the output of the linear regressions in the same
dataframe? **Mutate**!

``` r
weather_nest = 
  weather_nest %>% 
  mutate(
    model = map(data, weather_lm)) 
    # don't need to specify which dataset because I'm piping from that dataset. Just indicate the column name "data".

weather_nest
```

    ## # A tibble: 3 × 4
    ##   name           id          data               model 
    ##   <chr>          <chr>       <list>             <list>
    ## 1 CentralPark_NY USW00094728 <tibble [365 × 4]> <lm>  
    ## 2 Waikiki_HA     USC00519397 <tibble [365 × 4]> <lm>  
    ## 3 Waterhole_WA   USS0023B17S <tibble [365 × 4]> <lm>

## Revisiting Napoleon review

1)  Create a function first.

``` r
read_page_reviews = function(url) {
  
  html = read_html(url)
  
  review_titles = 
    html %>%
    html_nodes(".a-text-bold span") %>%
    html_text()
  
  review_stars = 
    html %>%
    html_nodes("#cm_cr-review_list .review-rating") %>%
    html_text() %>%
    str_extract("^\\d") %>%
    as.numeric()
  
  review_text = 
    html %>%
    html_nodes(".review-text-content span") %>%
    html_text() %>% 
    str_replace_all("\n", "") %>% 
    str_trim() %>% 
    str_subset("The media could not be loaded.", negate = TRUE) %>% 
    str_subset("^$", negate = TRUE)
  
  tibble(
    title = review_titles,
    stars = review_stars,
    text = review_text)
}
```

2)  Then, create a variable that read the url.

``` r
url_base = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber="
vec_urls = str_c(url_base, 1:5)
```

3)  Map the function to the all the url pages. These two options won’t
    show which page each review comes from.

``` r
# Option 1
output = vector("list", 5)

for (i in 1:5) {
  output[[i]] = read_page_reviews(vec_urls[[i]])
}

dynamite_reviews_1 = bind_rows(output)

# Option 2
dynamite_reviews_2 = map_df(vec_urls, read_page_reviews)
```

4)  Option 3: Use dataframe and list columns.

``` r
dynamite_reviews_3 = 
  tibble(
    page = 1:5,
    urls = str_c(url_base, page)) %>% 
  mutate(reviews = map(urls, read_page_reviews)) 

dynamite_reviews_3 %>% 
  select(-urls) %>% 
  unnest(reviews)
```

    ## # A tibble: 50 × 4
    ##     page title                                      stars text                  
    ##    <int> <chr>                                      <dbl> <chr>                 
    ##  1     1 Lol hey it’s Napoleon. What’s not to love…     5 Vote for Pedro        
    ##  2     1 Still the best                                 5 Completely stupid, ab…
    ##  3     1 70’s and 80’s Schtick Comedy                   5 …especially funny if …
    ##  4     1 Amazon Censorship                              5 I hope Amazon does no…
    ##  5     1 Watch to say you did                           3 I know it's supposed …
    ##  6     1 Best Movie Ever!                               5 We just love this mov…
    ##  7     1 Quirky                                         5 Good family film      
    ##  8     1 Funny movie - can't play it !                  1 Sony 4k player won't …
    ##  9     1 A brilliant story about teenage life           5 Napoleon Dynamite del…
    ## 10     1 HUHYAH                                         5 Spicy                 
    ## # … with 40 more rows
