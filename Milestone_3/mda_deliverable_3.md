Mini Data-Analysis Deliverable 3
================

# Welcome to your last milestone in your mini data analysis project!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 40 points (compared to the usual 30
points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

**Research Questions**: In Milestone 2, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
suppressMessages(library(tidyverse))
library(broom)
```

From Milestone 2, you chose two research questions. What were they? Put
them here.

<!-------------------------- Start your work below ---------------------------->

**Two research questions from Milestone 2**

1.  *Which heating type is the most common among very tall buildings?*
2.  *Is there a change in the number of storeys built over the years in
    relation to window type?*
    <!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

**Plot to be used for subsequent tasks**

``` r
# A subset of the apt_buildings dataset
(apt_buildings_sst <- apt_buildings %>% 
   select(id, heating_type, no_of_storeys, year_built, window_type) %>%
   filter(!is.na(heating_type), !is.na(no_of_storeys), !is.na(year_built),!is.na(window_type)) %>%
   mutate(age_of_building = case_when(year_built < 1850 ~ "ancient", 
                                      year_built < 1900 ~ "very old", 
                                      year_built < 1950 ~ "old", 
                                      year_built < 2000 ~ "recent", 
                                      TRUE ~ "more recent")) %>% 
   mutate(across(where(is.character), tolower)) %>% 
   mutate(age_of_building = factor(age_of_building)))
```

    ## # A tibble: 3,361 × 6
    ##       id heating_type no_of_storeys year_built window_type age_of_building
    ##    <dbl> <chr>                <dbl>      <dbl> <chr>       <fct>          
    ##  1 10359 hot water               17       1967 double pane recent         
    ##  2 10360 hot water               14       1970 double pane recent         
    ##  3 10361 hot water                4       1927 double pane old            
    ##  4 10362 hot water                5       1959 double pane recent         
    ##  5 10363 hot water                4       1943 double pane old            
    ##  6 10364 hot water                4       1952 single pane recent         
    ##  7 10365 hot water                4       1959 double pane recent         
    ##  8 10366 hot water                7       1971 single pane recent         
    ##  9 10367 electric                32       1969 double pane recent         
    ## 10 10368 hot water                4       1972 thermal     recent         
    ## # … with 3,351 more rows

``` r
# A plot containing factor to be reordered
ggplot(apt_buildings_sst, aes(age_of_building, no_of_storeys)) + 
  geom_boxplot() + scale_y_log10() +
  labs(x = "Age of building", y = "Number of storeys")
```

![](mda_deliverable_3_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)
        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.
    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).
        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number: 1**

I will go ahead and reorder the plot below so that the `age of building`
will be rearranged by the `average number of storeys`

``` r
# Reorder age_of_building by average number of storeys
apt_buildings_sst %>% 
  mutate(age_of_building = factor(age_of_building)) %>% 
  mutate(age_of_building = fct_reorder(age_of_building, no_of_storeys, mean)) %>% ggplot(aes(age_of_building, no_of_storeys)) + 
  geom_boxplot() + 
  scale_y_log10() + 
  labs(x = "Age of building", y = "Number of storeys")
```

![](mda_deliverable_3_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

I reordered the `age_of_building` factor based on the average number of
storeys in each group using the `fct_reorder` function which takes the
factor you want to reorder, another variable you want to reorder by, and
the function you want to call on the values in the second argument in
the `fct_reorder` function.

I chose to reorder the factor `age_of_building` because the plot before
the ordering is hard to understand; the number of storeys in each
`age_of_building` group is hard to read.

The resultant plot is much easier to read; at a glance, one can easily
see the minimum, maximum, and median number of storeys for each
age_of_building category in increasing order.
<!----------------------------------------------------------------------------->

<!-------------------------- Start your work below ---------------------------->

**Task Number: 2**

I will also produce a new plot that groups `ancient` and `very old`
factor levels together into an “other” category (or something similar)

``` r
# Group ancient and very old buildings into "other"
(collapse_to_other <- apt_buildings_sst %>% 
  mutate(age_of_building = fct_collapse(age_of_building, other = c("ancient", "very old"))) %>% 
  mutate(age_of_building = fct_reorder(age_of_building, no_of_storeys, mean)) %>%
  ggplot(aes(age_of_building, no_of_storeys)) + 
  geom_boxplot(aes(colour = age_of_building), show.legend = FALSE) + 
  scale_y_log10() +
  labs(x = "Age of building", y = "Number of storeys"))
```

![](mda_deliverable_3_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

I used the `fct_collapse` function to collapse `ancient` and `very old`
into `other`. They were grouped together because I am only interested in
investigating `old`, `recent`, and `more recent` buildings individually.
`ancient` and `very old` are just too back in years that I decided to
group them under `other`. Besides, only few observations are contained
in the `ancient` and `very old` categories.
<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question: 2**

**Variable of interest**: Y = no_of_storeys

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

**I will use the `lm()` function to fit a linear model to
`no_of_storeys` by `year_built`.** But before I do so, I will filter out
number of storeys built before 1900 from the `apt_buildings_sst`
dataset. This is because there are very few observations between 1805
and 1899, so I do not intend to have the linear model skewed to the
negative y-axis.

Furthermore, I will shift the intercept so that the start of the x-axis
is 1900 instead of 0 since observations before 1900 have been filtered
out.

``` r
# Filter the apt_buildings_sst dataset to only number of storeys built after 1899.

(apt_buildings_sst_fltr <- apt_buildings_sst %>% filter(year_built > 1899))
```

    ## # A tibble: 3,348 × 6
    ##       id heating_type no_of_storeys year_built window_type age_of_building
    ##    <dbl> <chr>                <dbl>      <dbl> <chr>       <fct>          
    ##  1 10359 hot water               17       1967 double pane recent         
    ##  2 10360 hot water               14       1970 double pane recent         
    ##  3 10361 hot water                4       1927 double pane old            
    ##  4 10362 hot water                5       1959 double pane recent         
    ##  5 10363 hot water                4       1943 double pane old            
    ##  6 10364 hot water                4       1952 single pane recent         
    ##  7 10365 hot water                4       1959 double pane recent         
    ##  8 10366 hot water                7       1971 single pane recent         
    ##  9 10367 electric                32       1969 double pane recent         
    ## 10 10368 hot water                4       1972 thermal     recent         
    ## # … with 3,338 more rows

``` r
# Fit a linear model to change in no_of_storeys by year_built

(lm_no_of_storeys <- lm(no_of_storeys ~ I(year_built - 1900), apt_buildings_sst_fltr))
```

    ## 
    ## Call:
    ## lm(formula = no_of_storeys ~ I(year_built - 1900), data = apt_buildings_sst_fltr)
    ## 
    ## Coefficients:
    ##          (Intercept)  I(year_built - 1900)  
    ##              -0.2412                0.1291

``` r
# Plot a graph showing the linear model
apt_buildings_sst_fltr %>% 
  ggplot(aes(year_built, no_of_storeys)) + 
  geom_point(size = 0.8) + 
  geom_smooth(method = "lm", se = FALSE, size = 0.9) +
  labs(x = "Year built", y = "Number of storeys")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](mda_deliverable_3_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

**I will determine the p-value of the fitted model**

``` r
# Determine the p-value of the fitted model
broom::glance(lm_no_of_storeys)
```

    ## # A tibble: 1 × 12
    ##   r.squared adj.r.squared sigma statistic   p.value    df  logLik    AIC    BIC
    ##       <dbl>         <dbl> <dbl>     <dbl>     <dbl> <dbl>   <dbl>  <dbl>  <dbl>
    ## 1     0.140         0.140  5.76      544. 1.12e-111     1 -10612. 21229. 21248.
    ## # … with 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>

<!----------------------------------------------------------------------------->

From the analysis above, the p-value column shows that the
`p-value is 1.12e-111`. This indicates that the relationship between
`no_of_storeys` and `year_built` is statistically significant given the
threshold: (p\< 0.05).

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

``` r
# A summary table from Milestone 2
(summary_air_conditioning <- apt_buildings %>% 
  group_by(air_conditioning) %>% 
  summarize(n = n()))
```

    ## # A tibble: 4 × 2
    ##   air_conditioning     n
    ##   <chr>            <int>
    ## 1 CENTRAL AIR        211
    ## 2 INDIVIDUAL UNITS   289
    ## 3 NONE              2870
    ## 4 <NA>                85

``` r
# Create a folder called output in your repository
dir.create(here::here("output"))

# Write summary_no_of_storeys as a csv file to output
write_csv(summary_air_conditioning, here::here("output", "summary_air_conditioning.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your `output`
folder. Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

**I will now write `lm_no_of-storeys` to an R binary file and load it
again**

``` r
# Write the model object lm_no_of_storeys to an RDS
saveRDS(lm_no_of_storeys, here::here("output", "lm_no_of_storeys.rds"))

# Load the model object
readRDS(here::here("output", "lm_no_of_storeys.rds"))
```

    ## 
    ## Call:
    ## lm(formula = no_of_storeys ~ I(year_built - 1900), data = apt_buildings_sst_fltr)
    ## 
    ## Coefficients:
    ##          (Intercept)  I(year_built - 1900)  
    ##              -0.2412                0.1291

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Exercise 3 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
