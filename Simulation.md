Simulation
================
Lectured by Jeff Goldsmith
2022-11-03

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

### Quick shorthand

-   `shift` + `option` and then select the big chunk that you want to
    delete.

repeat efgf repeat efgf repeat efgf repeat efgf repeat efgf

-   `ctrl` + `F` find the word that you want to change. Replace with the
    other words.

## Simulation: mean and sd for one ***n***

Generating a sample mean from a normal distribution…

``` r
sim_mean_sd = function(n_obs, mu = 7, sigma = 4) {
  
  sim_data = tibble(
    x = rnorm(n = n_obs, mean = mu, sd = sigma),
  )
  
  sim_data %>% 
    summarize(
      mu_hat = mean(x),
      sigma_hat = sd(x)
    )
}
```

How did we use this before?

``` r
sim_mean_sd(n_obs = 30)
```

    ## # A tibble: 1 × 2
    ##   mu_hat sigma_hat
    ##    <dbl>     <dbl>
    ## 1   6.58      4.19

How can we use this now?

``` r
output = vector("list", length = 100)
for (i in 1:100) {
  output[[i]] = sim_mean_sd(n_obs = 30)
}

bind_rows(output)
```

    ## # A tibble: 100 × 2
    ##    mu_hat sigma_hat
    ##     <dbl>     <dbl>
    ##  1   7.92      3.84
    ##  2   7.17      3.85
    ##  3   6.52      3.13
    ##  4   6.57      3.18
    ##  5   6.77      4.26
    ##  6   7.67      3.72
    ##  7   6.85      4.04
    ##  8   5.85      3.79
    ##  9   6.10      3.66
    ## 10   5.92      3.72
    ## # … with 90 more rows

Let’s use list columns instead.

``` r
sim_results = 
  # I want to create a dataframe that has expanded rows that do the following:
  expand_grid(   # expand_grid is like tibble...
    sample_size = 30,  # I want a column for sample_size
    iteration = 1:100) %>%   # Another column for iteration, and the rows go from 1 to 100
  # Then create a new column that calculate mean and sd using the function we created.
  mutate(
    estimate_df = map(sample_size, sim_mean_sd)) %>% 
  unnest(estimate_df)
```

``` r
sim_results %>% 
  ggplot(aes(x = mu_hat)) + 
  geom_density()
```

![](Simulation_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

#### What about changing the sample size?

I need to input a list

``` r
sim_results_2 = 
  expand_grid(   
    sample_size = c(30, 60, 120, 240), 
    iteration = 1:100) %>%   
  mutate(
    estimate_df = map(sample_size, sim_mean_sd)) %>% 
  unnest(estimate_df)
```

``` r
sim_results_2 %>% 
  mutate(
    sample_size = str_c("N = ", sample_size),
    sample_size = fct_inorder(sample_size)) %>% 
  ggplot(aes(x = sample_size, y = mu_hat)) + 
  geom_violin()
```

![](Simulation_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
sim_results_2 %>% 
  mutate(
    sample_size = str_c("N = ", sample_size),
    sample_size = fct_inorder(sample_size)) %>% 
  group_by(sample_size) %>% 
  summarize(emp_st_err = sd(mu_hat)) %>% 
  ggplot(aes(x = sample_size, y = mu_hat)) + 
  geom_violin()
```

``` r
sim_results_3 = 
  expand_grid(   
    sample_size = c(30, 60, 120, 240), 
    true_sigma = c(3, 6),
    iteration = 1:100) %>%   
  mutate(
    estimate_df = 
      map2(.x = sample_size, .y = true_sigma, ~sim_mean_sd(n_obs = .x, sigma = .y))) %>%  # ~ tells R that .x and .y go into this function.
  unnest(estimate_df)
```

``` r
sim_results_3 %>% 
  mutate(
    sample_size = str_c("n = ", sample_size),
    sample_size = fct_inorder(sample_size)) %>% 
  ggplot(aes(x = sample_size, y = mu_hat, fill = sample_size)) + 
  geom_violin() + 
  facet_grid(. ~ true_sigma)
```

![](Simulation_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

The `cache = TRUE` argument in a code chunk can be helpful: When it run
the code one time, it will save the results. So when we knit the
document, it will not take super long time to rerun the code again. But
we must pay attention to that, especially when we change the code, it
will not recognize the changes.
