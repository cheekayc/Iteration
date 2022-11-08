Writing Functions
================
Lectured by Jeff Goldsmith
2022-10-27

``` r
library(tidyverse)
library(rvest)

set.seed(1) 
```

# Write Functions

If we use the same code more than twice, write a function.

### Let’s get some Z-scores!

We know that Z-score = (x - mean)/sd.

``` r
# First create a variable that contains a list of numeric values.
x_vec = rnorm(25, mean = 7, sd = 4)

# Then we compute the Z-scores of this set of numbers.
(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1] -0.83687228  0.01576465 -1.05703126  1.50152998  0.16928872 -1.04107494
    ##  [7]  0.33550276  0.59957343  0.42849461 -0.49894708  1.41364561  0.23279252
    ## [13] -0.83138529 -2.50852027  1.00648110 -0.22481531 -0.19456260  0.81587675
    ## [19]  0.68682298  0.44756609  0.78971253  0.64568566 -0.09904161 -2.27133861
    ## [25]  0.47485186

Suppose we want to do this often. I.e. what if we want to calculate the
Z-scores for different set of variables? We would have to copy & paste
the code so many times! So we should write a function.

``` r
# z_scores = function(ARGUMENTS) {
  
#  BODY OF FUNCTION

#  }

z_scores = function(x) {       # z_scores is a function of (x) that do the following:
  z = (x - mean(x)) / sd(x)    # calculate z by the equation stated.
  z                            # show the output of z. If we don't do this, the output will not be shown.
}
```

Now let’s check if the function works.

``` r
z_scores(x = x_vec)  

z_scores(x = 1:10)

z_scores(x = rbinom(1000, 1, 0.6))

z_scores(x = 3) # Returned "NA". Can't do this because if x is only one number, R can't compute the sd of just one number.

z_scores(x = "My name is Jeff") # Can't do this because R cannot do mathematical computations with non-numeric variable.
```

When the variable “x” only contain one number, or the variable “x” is a
character vector, R will give us an error warning. So how do we fix
this?

``` r
z_scores = function(x) {
  if (!is.numeric(x)) {   # meaning if R encounter something that is NOT numeric, stop the function and tell me...
    stop("Argument x should be numeric")}
  else if (length(x) == 1) {  # if the number of values in "x" is less than 2, stop the function and tell me...
    stop("Z scores cannot be computed for length 1 vectors")}
  
  z = mean(x) / sd(x)
  
  z
}
```

Now try…

``` r
z_scores(x = 3)

z_scores(x = "My name is Jeff")
```

## Let’s have multiple outputs

Say, instead of just showing the output of Z-scores, I want an output
that gives me the mean, standard deviation and Z-scores.

``` r
mean_sd_z = function(x) {
  # make sure we define specific conditions before we define the function.
  if (!is.numeric(x)) {   
    stop("Argument x should be numeric")}
  else if (length(x) == 1) { 
    stop("Z scores cannot be computed for length 1 vectors")}
  mean_x = mean(x)
  sd_x = sd(x)
  z_scores = (16 - mean(x)) / sd(x)  # here I just simply assign a fix number "16" for no reason.
  
#How do I return all of them at once? Create a tibble!
  tibble(
    mean = mean(x),
    sd = sd_x,
    z_score = z_scores)
}

mean_sd_z(x = x_vec)
```

    ## # A tibble: 1 × 3
    ##    mean    sd z_score
    ##   <dbl> <dbl>   <dbl>
    ## 1  7.67  3.80    2.19

``` r
mean_sd_z(x = 1:10)
```

    ## # A tibble: 1 × 3
    ##    mean    sd z_score
    ##   <dbl> <dbl>   <dbl>
    ## 1   5.5  3.03    3.47

``` r
mean_sd_z(x = rbinom(50, 1, 0.5))
```

    ## # A tibble: 1 × 3
    ##    mean    sd z_score
    ##   <dbl> <dbl>   <dbl>
    ## 1  0.42 0.499    31.2

## *Simulations*

What if I want to repeat the function I just created so many times for
different samples or even different sample sizes?

``` r
# I am going to create a set of values for x:
x_vec = rnorm(n = 25, mean = 7, sd = 4)

# Then, I'll create a dataframe that gives me the mean and sd of the vectors I just created.
tibble(
  mean = mean(x_vec),
  sd = sd(x_vec))
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  7.66  4.42

``` r
# If we run this code chunk many times, we will get different values every time.
```

**Simulation** can help us run the function so many times with different
mean, sd, and sample sizes.

``` r
# I should create a function first.
# In this function, I am setting the default value for true mean and true_sd. 
sim_mean_sd = function(n_obs, true_mean = 7, true_sd = 4) {
  # I am creating a x-variable inside this function and use it inside this function.
  x = rnorm(n = n_obs, mean = true_mean, sd = true_sd)
  # If I specify the n = 1000, it will only run 1000 observations. But if I don't specify the n, it will run whatever the sample size is.
  tibble(
    mean = mean(x),
    sd = sd(x))
}
```

Now check if the function works.

``` r
# If I don't specify the true_mean & true_sd, it will just assume the default true_mean & true_sd (recall when we create the function, we set a default values for true_mean & true_sd)
sim_mean_sd(n_obs = 30)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.97  3.18

``` r
# Now that I specified the true_mean & true_sd it will not use the default values but will use the one I specified here.
sim_mean_sd(n_obs = 25, true_mean = 10, true_sd = 5)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.8  3.78

``` r
# We can also not name out the argument, just put the numbers in. R will assume the first value is n_obs, the second is true_mean, and the third is true_sd.
sim_mean_sd(2500, 10, 7)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.86  7.27

``` r
# I can also name out the argument and don't put it in order. It will still work because I named out the arguments.
sim_mean_sd(true_sd = 2, n_obs = 3000, true_mean = 165)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  165.  2.02

## Fixing bad stuff

In the previous lecture, we selected reviews of *Napoleon Dynamite* from
only one page. If we want all the reviews from tens or hundreds of page,
we would have to copy and paste the same function hundreds of time, and
each time we would have to change the page number…That’s a lot of work…

We can do the following instead! Save our lives!

First, write a function to get reviews.

``` r
# I am going to name my function "read_reviews". The only argument I need in this function is the url because it will read the pages from the url I specified.
read_reviews = function(url) {
  # I need to create a variable to read the url.
  napoleon_html = read_html(url)
  # Then I create different variables to do different things so that I can create a tibble later.
  review_titles = 
    napoleon_html %>%
    html_nodes(".a-text-bold span") %>%
    html_text()

  review_stars = 
    napoleon_html %>%
    # Ask Jeff why 'cm_cr-review_list'?
    html_nodes("#cm_cr-review_list .review-rating") %>%
    html_text() %>%
    str_extract("^\\d") %>% # extract anything that is a digit.
    as.numeric()

  review_text = 
    napoleon_html %>%
    html_nodes(".review-text-content span") %>%
    html_text() %>% 
    # replace all characters "\n" with " " a blank space.
    str_replace_all("\n", "") %>% 
    str_trim() %>% 
# Page 3 doesn't work with this code because there is a picture in one of the comment. 
    # remove entries that has embedded images.
    str_subset("The media could not be loaded.", negate = TRUE) %>% # negate = TRUE return non-matching element.
    str_subset("^$", negate = TRUE) # Ask Jeff what does this mean

  reviews = tibble(
    title = review_titles,
    stars = review_stars,
    text = review_text)
}
```

-   `html_nodes` is more general, just like `html_elements`.

-   `str_trim()` removes whitespace from start and end of string.

-   `\d`: matches any digit. The complement, `\D`, matches any character
    that is not a decimal digit.

Now let’s see if this function works:

``` r
base_url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber="
# Remember to remove the page number.

# I'm creating a variable 
vec_urls = str_c(base_url, c(1:5))

dynamite_reviews = 
  bind_rows(
    read_reviews(vec_urls[1]),
    read_reviews(vec_urls[2]),
    read_reviews(vec_urls[3]),
    read_reviews(vec_urls[4]),
    read_reviews(vec_urls[5])
  )

dynamite_reviews
```

    ## # A tibble: 50 × 3
    ##    title                                      stars text                        
    ##    <chr>                                      <dbl> <chr>                       
    ##  1 Lol hey it’s Napoleon. What’s not to love…     5 Vote for Pedro              
    ##  2 Still the best                                 5 Completely stupid, absolute…
    ##  3 70’s and 80’s Schtick Comedy                   5 …especially funny if you ha…
    ##  4 Amazon Censorship                              5 I hope Amazon does not cens…
    ##  5 Watch to say you did                           3 I know it's supposed to be …
    ##  6 Best Movie Ever!                               5 We just love this movie and…
    ##  7 Quirky                                         5 Good family film            
    ##  8 Funny movie - can't play it !                  1 Sony 4k player won't even r…
    ##  9 A brilliant story about teenage life           5 Napoleon Dynamite delivers …
    ## 10 HUHYAH                                         5 Spicy                       
    ## # … with 40 more rows

### Lord of the Ring

We can create a dataset like this:

``` r
fellowship_ring = readxl::read_excel("./Data/LotR_Words.xlsx", range = "B3:D6") %>%
  mutate(movie = "fellowship_ring")

two_towers = readxl::read_excel("./Data/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_king = readxl::read_excel("./Data/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")

lotr_tidy_1 = bind_rows(fellowship_ring, two_towers, return_king) %>%
  janitor::clean_names() %>%
  gather(key = sex, value = words, female:male) %>%
  mutate(race = str_to_lower(race)) %>% 
  select(movie, everything()) 

lotr_tidy_1
```

    ## # A tibble: 18 × 4
    ##    movie           race   sex    words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring elf    female  1229
    ##  2 fellowship_ring hobbit female    14
    ##  3 fellowship_ring man    female     0
    ##  4 two_towers      elf    female   331
    ##  5 two_towers      hobbit female     0
    ##  6 two_towers      man    female   401
    ##  7 return_king     elf    female   183
    ##  8 return_king     hobbit female     2
    ##  9 return_king     man    female   268
    ## 10 fellowship_ring elf    male     971
    ## 11 fellowship_ring hobbit male    3644
    ## 12 fellowship_ring man    male    1995
    ## 13 two_towers      elf    male     513
    ## 14 two_towers      hobbit male    2463
    ## 15 two_towers      man    male    3589
    ## 16 return_king     elf    male     510
    ## 17 return_king     hobbit male    2673
    ## 18 return_king     man    male    2459

We can also write a function to create the same dataset like this:

``` r
lotr_load_and_tidy = function(path, range, movie_name) {
  
  df = readxl::read_excel(path, range = range) %>%
    janitor::clean_names() %>%
    gather(key = sex, value = words, female:male) %>%
    mutate(race = str_to_lower(race),
           movie = movie_name)
  
  df
}

lotr_tidy_2 = 
  bind_rows(
    lotr_load_and_tidy("./Data/LotR_Words.xlsx", "B3:D6", "fellowship_ring"),
    lotr_load_and_tidy("./Data/LotR_Words.xlsx", "F3:H6", "two_towers"),
    lotr_load_and_tidy("./Data/LotR_Words.xlsx", "J3:L6", "return_king")) %>%
  select(movie, everything()) 

lotr_tidy_2
```

    ## # A tibble: 18 × 4
    ##    movie           race   sex    words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring elf    female  1229
    ##  2 fellowship_ring hobbit female    14
    ##  3 fellowship_ring man    female     0
    ##  4 fellowship_ring elf    male     971
    ##  5 fellowship_ring hobbit male    3644
    ##  6 fellowship_ring man    male    1995
    ##  7 two_towers      elf    female   331
    ##  8 two_towers      hobbit female     0
    ##  9 two_towers      man    female   401
    ## 10 two_towers      elf    male     513
    ## 11 two_towers      hobbit male    2463
    ## 12 two_towers      man    male    3589
    ## 13 return_king     elf    female   183
    ## 14 return_king     hobbit female     2
    ## 15 return_king     man    female   268
    ## 16 return_king     elf    male     510
    ## 17 return_king     hobbit male    2673
    ## 18 return_king     man    male    2459

## Variables Scoping and Names

**Variables that we created inside a function stay inside the function.
If we try to search for the variables outside the function, they don’t
exist.**

**But we also don’t want to create a variable outside a function and use
that variable inside a function because then R will step out of the
function to look for that variable. This can potentially cause us
problem.**

## *Functions as arguments*

``` r
# First, create a variable that has a bunch of numbers.
x_var = rnorm(25, 0, 1) # By default, the first number is n, 2nd number is mean, 3rd number is sd.

# Then, create a function.
my_summary = function(x, summ_func) { # when using this function, we must specify which variable, and then what function.
  # summ_func includes many functions, such as mean, IQR, variance, etc.
  summ_func(x)
}

my_summary(x_var, sd)
```

    ## [1] 1.254205

``` r
my_summary(x_var, IQR)
```

    ## [1] 1.23907

``` r
my_summary(x_var, var)
```

    ## [1] 1.573029
