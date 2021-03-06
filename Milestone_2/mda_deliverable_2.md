Mini Data Analysis Milestone 2
================

*To complete this milestone, you can either edit [this `.rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are commented out with
`<!--- start your work here--->`. When you are done, make sure to knit
to an `.md` file by changing the output in the YAML header to
`github_document`, before submitting a tagged release on canvas.*

# Welcome back to your mini data analysis project!

This time, we will explore more in depth the concept of *tidy data*, and
hopefully investigate further into your research questions that you
defined in milestone 1.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
suppressMessages(library(tidyverse))
suppressMessages(library(ggridges))
```

# Learning Objectives

By the end of this milestone, you should:

-   Become familiar with manipulating and summarizing your data in
    tibbles using `dplyr` and `tidyr`, with a research question in mind.
-   Understand what *tidy* data is, and how to create it. In milestone
    3, we will explore when this might be useful.
-   Generate a reproducible and clear report using R Markdown.
-   Gain a greater understanding of how to use R to answer research
    questions about your data.

**Things to keep in mind**

-   Remember to document your code, be explicit about what you are
    doing, and write notes in this markdown document when you feel that
    context is required. Create your analysis as if someone else will be
    reading it! **There will be 2.5 points reserved for reproducibility,
    readability, and repo organization.**

-   Before working on each task, you should always keep in mind the
    specific **research question** that you’re trying to answer.

# Task 1: Process and summarize your data (15 points)

From milestone 1, you should have an idea of the basic structure of your
dataset (e.g. number of rows and columns, class types, etc.). Here, we
will start investigating your data more in-depth using various data
manipulation functions.

### 1.1 (2.5 points)

First, write out the 4 research questions you defined in milestone 1
were. This will guide your work through milestone 2:

<!-------------------------- Start your work below ---------------------------->

1.  *In what years was the central air-conditioning system mostly
    installed in buildings?*
2.  *Which heating type is the most common among very tall buildings?*
3.  *Is there a change in the number of storeys built over the years in
    relation to window type?*
4.  *What is the relationship between number of accessible parking
    spaces and number of storeys for each visitor parking category by
    property type?*

*I added categorical variables to question 4 since there’s no exercise
given for investigating the correlation between numerical variables
only*
<!----------------------------------------------------------------------------->

### 1.2 (10 points)

Now, for each of your four research questions, choose one task from
options 1-4 (summarizing), and one other task from 4-8 (graphing). You
should have 2 tasks done for each research question (8 total). Make sure
it makes sense to do them! (e.g. don’t use a numerical variables for a
task that needs a categorical variable.). Comment on why each task helps
(or doesn’t!) answer the corresponding research question.

Ensure that the output of each operation is printed!

**Summarizing:**

1.  Compute the *range*, *mean*, and *two other summary statistics* of
    **one numerical variable** across the groups of **one categorical
    variable** from your data.
2.  Compute the number of observations for at least one of your
    categorical variables. Do not use the function `table()`!
3.  Create a categorical variable with 3 or more groups from an existing
    numerical variable. You can use this new variable in the other
    tasks! *An example: age in years into “child, teen, adult, senior”.*
4.  Based on two categorical variables, calculate two summary statistics
    of your choosing.

**Graphing:**

5.  Create a graph out of summarized variables that has at least two
    geom layers.
6.  Create a graph of your choosing, make one of the axes logarithmic,
    and format the axes labels so that they are “pretty” or easier to
    read.
7.  Make a graph where it makes sense to customize the alpha
    transparency.
8.  Create 3 histograms out of summarized variables, with each histogram
    having different sized bins. Pick the “best” one and explain why it
    is the best.

Make sure it’s clear what research question you are doing each operation
for!

<!------------------------- Start your work below ----------------------------->

**Research Question 1: In what years was the central air-conditioning
system mostly installed in buildings?**

To answer the question, it would be good to determine what the
air-conditioning types are and their frequencies especially that of the
*central air*. Then, we would go on to visualize the frequency of use
throughout the years under review(1805 - 2019).

``` r
# Compute the number of observations for at least one of your categorical variables(air_conditioning)
apt_buildings %>% 
  group_by(air_conditioning) %>% 
  summarize(n = n())
```

    ## # A tibble: 4 × 2
    ##   air_conditioning     n
    ##   <chr>            <int>
    ## 1 CENTRAL AIR        211
    ## 2 INDIVIDUAL UNITS   289
    ## 3 NONE              2870
    ## 4 <NA>                85

``` r
# Make a graph where it makes sense to customize the alpha transparency: determine when central air-conditioning system was mostly installed

apt_buildings %>% 
  filter(!is.na(year_built), !is.na(air_conditioning)) %>% 
  ggplot(aes(year_built, fill = air_conditioning)) + 
  geom_density(alpha = 0.4)
```

![](mda_deliverable_2_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

In trying to answer **research question 1 above**, it made sense to
first determine the total number of `central air` air-conditioning units
installed in buildings. Surprisingly, it was discovered that
`most of the buildings have no air-conditioning installed!`
`Eighty-five` buildings have missing information on air-conditioning.

To further answer the research question, a density plot was used to
determine when central air-conditioning was mostly installed in
buildings. The plot clearly lays out the 3 air-conditioning systems
where it shows that `central air` installation increased after 1950 and
remained the most installed air-conditioning system from around 1975 to
2019.

**Research question 2: Which heating type is the most common among very
tall buildings?**

Let me first check the distribution of the different heating types among
all the buildings. After that, I will go ahead and visualize that
distribution graphically. Hopefully we would be able to see the heating
type that is most common among very tall buildings.

``` r
# Compute the range, mean, median and sum of one numerical variable(no_of_storeys) across the groups of one categorical variable(heating_type) from your data

apt_buildings %>%
  filter(!is.na(heating_type), !is.na(no_of_storeys)) %>%
  group_by(heating_type) %>%
  summarise(storey_range = paste(min(no_of_storeys), "-", max(no_of_storeys)),
            storey_mean = mean(no_of_storeys, na.rm = TRUE),
            storey_median = median(no_of_storeys, na.rm = TRUE),
            n = sum(!is.na(no_of_storeys)))
```

    ## # A tibble: 3 × 5
    ##   heating_type   storey_range storey_mean storey_median     n
    ##   <chr>          <chr>              <dbl>         <dbl> <int>
    ## 1 ELECTRIC       3 - 33              9.74             6   265
    ## 2 FORCED AIR GAS 3 - 43              8.19             5   315
    ## 3 HOT WATER      0 - 51              7.61             5  2789

``` r
# Create a graph out of summarized variables that has at least two geom layers
# Create a boxplot to show range(minimum, maximum), median no_of_storeys across the heating types. Add mean as a second layer 
apt_buildings %>% 
  filter(!is.na(heating_type), !is.na(no_of_storeys)) %>% 
  ggplot(aes(heating_type, no_of_storeys)) + 
  geom_boxplot(aes(fill = heating_type), show.legend = FALSE) + 
  stat_summary(fun = mean, shape = 3, na.rm = TRUE)
```

    ## Warning: Removed 3 rows containing missing values (geom_segment).

![](mda_deliverable_2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

For **research question 2 above**, first it is shown that `HOT WATER` is
the most used heating type, and that 50% of the buildings have 5 - 6
storeys. The average number of storeys to use `ELECTRIC` is
approximately 10, the average number of storeys to use `FORCED AIR GAS`
is approximately 8, while the average number of storeys to use
`HOT WATER` is approximately 8. The tallest buildings to use `ELECTRIC`
heating type have `33 storeys`, the tallest buildings to use
`FORCED AIR GAS` have `43 storeys`, while the tallest building to use
`HOT WATER` has `51 storeys`.

Graphically, it is seen that `HOT WATER` heating type was installed in
the tallest building. However, it is also seen that `FORCED AIR GAS`is
fairly common in very tall buildings as well.

**Research question 3: Is there a change in the number of storeys built
over the years in relation to window type?**

To answer this question, let me first of all group the years in which
the buildings were erected into 5 categories: `ancient(before 1850)`,
`very old(before 1900)`, `old(before 1950)`, `recent(before 2000)`,
`more recent(before 2020)`. After that, geom_density_ridges will be used
to visualize the changes in number of storeys built within the
year-groups in relation to window type.

``` r
# Create a categorical variable(age_of_building) with 5 groups from a numerical variable(year_built)
(apt_buildings_new <- apt_buildings %>% 
   select(year_built, no_of_storeys, window_type) %>% 
   filter(!is.na(year_built), !is.na(no_of_storeys), !is.na(window_type)) %>% 
   mutate(age_of_building = case_when(year_built < 1850 ~ "ancient", 
                                      year_built < 1900 ~ "very old", 
                                      year_built < 1950 ~ "old", 
                                      year_built < 2000 ~ "recent", 
                                      TRUE ~ "more recent")))
```

    ## # A tibble: 3,436 × 4
    ##    year_built no_of_storeys window_type age_of_building
    ##         <dbl>         <dbl> <chr>       <chr>          
    ##  1       1967            17 DOUBLE PANE recent         
    ##  2       1970            14 DOUBLE PANE recent         
    ##  3       1927             4 DOUBLE PANE old            
    ##  4       1959             5 DOUBLE PANE recent         
    ##  5       1943             4 DOUBLE PANE old            
    ##  6       1952             4 SINGLE PANE recent         
    ##  7       1959             4 DOUBLE PANE recent         
    ##  8       1971             7 SINGLE PANE recent         
    ##  9       1969            32 DOUBLE PANE recent         
    ## 10       1972             4 THERMAL     recent         
    ## # … with 3,426 more rows

``` r
# Make a graph where it makes sense to customize the alpha transparency: visualize the changes in number of storeys built over the years in relation to window type

suppressMessages(print(apt_buildings_new %>% 
  ggplot(aes(no_of_storeys, age_of_building)) + 
  geom_density_ridges(aes(fill = window_type), alpha = 0.6, show.legend = FALSE) + 
  facet_wrap(~ window_type)))
```

![](mda_deliverable_2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

I set the alpha transparency of the plot above because some peaks ran
into the ones above them. From the plot it is seen that houses with more
storeys were built within the `recent` and `more recent`
*age_of_building* groups. `ancient` and `very old` buildings(erected
between 1805 and 1850) have `DOUBLE PANE` window type while `recent` and
`more recent` buildings(erected between 1950 and 2020) with more storeys
have `THERMAL` and `SINGLE PANE` window types in addition to
`DOUBLE PANE`.

**Research question 4: What is the relationship between number of
accessible parking spaces and number of storeys for each visitor parking
category by property type?**

``` r
# Compute the number of observations for at least one of your categorical variables (property_type)
apt_buildings %>% 
  group_by(property_type) %>% 
  summarize(n = n())
```

    ## # A tibble: 3 × 2
    ##   property_type      n
    ##   <chr>          <int>
    ## 1 PRIVATE         2888
    ## 2 SOCIAL HOUSING   240
    ## 3 TCHC             327

``` r
# Create a graph of your choosing, make one of the axes logarithmic, and format the axes labels so that they are "pretty" or easier to read
# Visualize the relationship between number of accessible parking spaces and number of storeys for each visitor parking category by property type
apt_buildings %>% 
  filter(!is.na(visitor_parking), 
         !is.na(no_of_storeys), 
         !is.na(no_of_accessible_parking_spaces)) %>% 
  ggplot(aes(no_of_accessible_parking_spaces, no_of_storeys), na.rm = TRUE) + 
  geom_jitter(aes(color = property_type), na.rm = TRUE, width = 0.4, alpha = 0.3) + 
  facet_wrap(~ visitor_parking) + 
  scale_x_log10() + 
  labs(x = "Number of accessible parking spaces", y = "Number of storeys") + 
  theme_minimal()
```

    ## Warning: Transformation introduced infinite values in continuous x-axis

![](mda_deliverable_2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Most of the `FREE` visitor parking is found in `PRIVATE` buildings. Many
`PRIVATE` buildings with the lowest number of storeys lack parking
spaces for visitors whereas buildings in the`TCHC` group have `BOTH`
free and paid `visitor_parking` and also many in the same category have
`PAID` visitor_parking. The least `FREE` visitor parking is found among
TCHC buildings. `SOCIAL HOUSING` buildings have the least `BOTH` visitor
parking. Generally, `TCHC` buildings have the least number of accessible
parking spaces while `PRIVATE` buildings have the highest number of
accessible parking spaces.
<!----------------------------------------------------------------------------->

### 1.3 (2.5 points)

Based on the operations that you’ve completed, how much closer are you
to answering your research questions? Think about what aspects of your
research questions remain unclear. Can your research questions be
refined, now that you’ve investigated your data a bit more? Which
research questions are yielding interesting results?

<!------------------------- Write your answer here ---------------------------->

**Based on the operations completed, all of the research questions have
been answered.** Research question 1 yielded interesting results; it was
discovered that majority of the buildings lack air-conditioning units.
It would be interesting to find out the property types that were built
without air-conditioning system.

``` r
# Determine property types built without air-conditioning units installed
apt_buildings %>%  filter(!is.na(property_type), 
                          !is.na(no_of_storeys), 
                          !is.na(air_conditioning)) %>% 
  ggplot(aes(air_conditioning, no_of_storeys)) + 
  geom_jitter(width = 0.3, alpha = 0.2) + 
  facet_wrap(~ property_type) +
  coord_flip()
```

![](mda_deliverable_2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

The analysis above shows that `PRIVATE` property type has the highest
concentration of buildings without air-conditioning system. It also
reveals that most `TCHC` buildings don’t have air-conditioning system
while the few that have make use of the `CENTRAL AIR`.
`alpha transparency` and `jittering` were used to reveal overlapping
points.

<!----------------------------------------------------------------------------->

# Task 2: Tidy your data (12.5 points)

In this task, we will do several exercises to reshape our data. The goal
here is to understand how to do this reshaping with the `tidyr` package.

A reminder of the definition of *tidy* data:

-   Each row is an **observation**
-   Each column is a **variable**
-   Each cell is a **value**

*Tidy’ing* data is sometimes necessary because it can simplify
computation. Other times it can be nice to organize data so that it can
be easier to understand when read manually.

### 2.1 (2.5 points)

Based on the definition above, can you identify if your data is tidy or
untidy? Go through all your columns, or if you have \>8 variables, just
pick 8, and explain whether the data is untidy or tidy.

<!--------------------------- Start your work below --------------------------->

**I will extract 8 variables for this question**

``` r
# Subset the `apt_buildings` dataset: select 8 variables
(apt_buildings_subvar <- apt_buildings %>% 
   select(id, air_conditioning, 
          heating_type, 
          no_of_storeys, 
          visitor_parking, 
          window_type,
          property_type, 
          year_built)
 )
```

    ## # A tibble: 3,455 × 8
    ##       id air_conditioning heating_type no_of_storeys visitor_parking window_type
    ##    <dbl> <chr>            <chr>                <dbl> <chr>           <chr>      
    ##  1 10359 NONE             HOT WATER               17 PAID            DOUBLE PANE
    ##  2 10360 NONE             HOT WATER               14 FREE            DOUBLE PANE
    ##  3 10361 NONE             HOT WATER                4 UNAVAILABLE     DOUBLE PANE
    ##  4 10362 NONE             HOT WATER                5 UNAVAILABLE     DOUBLE PANE
    ##  5 10363 NONE             HOT WATER                4 UNAVAILABLE     DOUBLE PANE
    ##  6 10364 NONE             HOT WATER                4 UNAVAILABLE     SINGLE PANE
    ##  7 10365 NONE             HOT WATER                4 PAID            DOUBLE PANE
    ##  8 10366 CENTRAL AIR      HOT WATER                7 FREE            SINGLE PANE
    ##  9 10367 NONE             ELECTRIC                32 PAID            DOUBLE PANE
    ## 10 10368 NONE             HOT WATER                4 BOTH            THERMAL    
    ## # … with 3,445 more rows, and 2 more variables: property_type <chr>,
    ## #   year_built <dbl>

The data subset above contains 8 variables and 3455 observations. The
`id` variable uniquely identifies each observation. Each row is a single
observation - contains all values measured on a particular building,
each column is a single variable - contains values that measure the same
underlying attribute, each cell is a single value.

Additionally, considering my research questions, the data is tidy.
<!----------------------------------------------------------------------------->

### 2.2 (5 points)

Now, if your data is tidy, untidy it! Then, tidy it back to it’s
original state.

If your data is untidy, then tidy it! Then, untidy it back to it’s
original state.

Be sure to explain your reasoning for this task. Show us the “before”
and “after”.

<!--------------------------- Start your work below --------------------------->

**First, let me show the `before` state of the `apt_buildings_subvar`
data in its `tidy` form as already explained above**

``` r
# The data in a tidy form
(apt_buildings_subvar <- apt_buildings %>% 
   select(id, air_conditioning, 
          heating_type, 
          no_of_storeys, 
          visitor_parking, 
          window_type, 
          property_type, 
          year_built)
 )
```

    ## # A tibble: 3,455 × 8
    ##       id air_conditioning heating_type no_of_storeys visitor_parking window_type
    ##    <dbl> <chr>            <chr>                <dbl> <chr>           <chr>      
    ##  1 10359 NONE             HOT WATER               17 PAID            DOUBLE PANE
    ##  2 10360 NONE             HOT WATER               14 FREE            DOUBLE PANE
    ##  3 10361 NONE             HOT WATER                4 UNAVAILABLE     DOUBLE PANE
    ##  4 10362 NONE             HOT WATER                5 UNAVAILABLE     DOUBLE PANE
    ##  5 10363 NONE             HOT WATER                4 UNAVAILABLE     DOUBLE PANE
    ##  6 10364 NONE             HOT WATER                4 UNAVAILABLE     SINGLE PANE
    ##  7 10365 NONE             HOT WATER                4 PAID            DOUBLE PANE
    ##  8 10366 CENTRAL AIR      HOT WATER                7 FREE            SINGLE PANE
    ##  9 10367 NONE             ELECTRIC                32 PAID            DOUBLE PANE
    ## 10 10368 NONE             HOT WATER                4 BOTH            THERMAL    
    ## # … with 3,445 more rows, and 2 more variables: property_type <chr>,
    ## #   year_built <dbl>

**Second, below is the state of the `apt_buildings_subvar` data in its
*untidy* form**

``` r
# The data in an untidy form
(apt_buildings_subvar_wider <- apt_buildings_subvar %>% 
   mutate(property_type_val = TRUE) %>%
   pivot_wider(id_cols = c(-property_type, - property_type_val), 
               names_from = property_type, 
               values_from = property_type_val, 
               values_fill = list(property_type_val = FALSE)) %>% 
   select(id, PRIVATE, TCHC, `SOCIAL HOUSING`, everything()))
```

    ## # A tibble: 3,455 × 10
    ##       id PRIVATE TCHC  `SOCIAL HOUSING` air_conditioning heating_type
    ##    <dbl> <lgl>   <lgl> <lgl>            <chr>            <chr>       
    ##  1 10359 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  2 10360 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  3 10361 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  4 10362 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  5 10363 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  6 10364 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  7 10365 TRUE    FALSE FALSE            NONE             HOT WATER   
    ##  8 10366 TRUE    FALSE FALSE            CENTRAL AIR      HOT WATER   
    ##  9 10367 TRUE    FALSE FALSE            NONE             ELECTRIC    
    ## 10 10368 FALSE   TRUE  FALSE            NONE             HOT WATER   
    ## # … with 3,445 more rows, and 4 more variables: no_of_storeys <dbl>,
    ## #   visitor_parking <chr>, window_type <chr>, year_built <dbl>

As can be seen above, the three new columns `PRIVATE`, `TCHC`, and
`SOCIAL HOUSING` are all values that can be grouped under a single
variable. For example, `PRIVATE` makes no meaning as a variable.
Besides, in this format, it is untidy to use to answer my research
questions. The 3 new columns can be conveniently grouped under a
variable that measures the type of property each building belongs to.

**Now, let me revert the `apt_buildings_subvar` data to its original
tidy state!**

``` r
(apt_buildings_subvar_longer <- apt_buildings_subvar_wider %>% 
    pivot_longer(PRIVATE:`SOCIAL HOUSING`, names_to = "property_type", values_to = "property_type_val") %>% 
    filter(property_type_val) %>% 
    select(-property_type_val))
```

    ## # A tibble: 3,455 × 8
    ##       id air_conditioning heating_type no_of_storeys visitor_parking window_type
    ##    <dbl> <chr>            <chr>                <dbl> <chr>           <chr>      
    ##  1 10359 NONE             HOT WATER               17 PAID            DOUBLE PANE
    ##  2 10360 NONE             HOT WATER               14 FREE            DOUBLE PANE
    ##  3 10361 NONE             HOT WATER                4 UNAVAILABLE     DOUBLE PANE
    ##  4 10362 NONE             HOT WATER                5 UNAVAILABLE     DOUBLE PANE
    ##  5 10363 NONE             HOT WATER                4 UNAVAILABLE     DOUBLE PANE
    ##  6 10364 NONE             HOT WATER                4 UNAVAILABLE     SINGLE PANE
    ##  7 10365 NONE             HOT WATER                4 PAID            DOUBLE PANE
    ##  8 10366 CENTRAL AIR      HOT WATER                7 FREE            SINGLE PANE
    ##  9 10367 NONE             ELECTRIC                32 PAID            DOUBLE PANE
    ## 10 10368 NONE             HOT WATER                4 BOTH            THERMAL    
    ## # … with 3,445 more rows, and 2 more variables: year_built <dbl>,
    ## #   property_type <chr>

The `apt_buildings_subvar` dataset named `apt_buildings_subvar_longer`
now has the `property_type` variable measuring the same attribute using
the `PRIVATE`, `TCHC`, and `SOCIAL HOUSING` values. It is now in the
original tidy state.

<!----------------------------------------------------------------------------->

### 2.3 (5 points)

Now, you should be more familiar with your data, and also have made
progress in answering your research questions. Based on your interest,
and your analyses, pick 2 of the 4 research questions to continue your
analysis in milestone 3, and explain your decision.

Try to choose a version of your data that you think will be appropriate
to answer these 2 questions in milestone 3. Use between 4 and 8
functions that we’ve covered so far (i.e. by filtering, cleaning,
tidy’ing, dropping irrelvant columns, etc.).

<!--------------------------- Start your work below --------------------------->

**Two research questions for analysis in milestone 3**

1.  Which heating type is the most common among very tall buildings?
2.  Is there a change in the number of storeys built over the years in
    relation to window type?

I chose the 2 questions above because of their relevance to tasks in
milestone 3 which involve reordering, grouping factors, model-fitting,
etc. They produce plots that have at least 3 groups. All these
activities require tidy data and I know that the data that generated the
plots is tidy.

``` r
# Choose a version of the apt_buildings data appropriate to answer the 2 research questions for milestone 3
(apt_buildings %>% 
   select(id, heating_type, no_of_storeys, year_built, window_type) %>%
   filter(!is.na(heating_type), !is.na(no_of_storeys), !is.na(year_built),!is.na(window_type)) %>%
   mutate(age_of_building = case_when(year_built < 1850 ~ "ancient", 
                                      year_built < 1900 ~ "very old", 
                                      year_built < 1950 ~ "old", 
                                      year_built < 2000 ~ "recent", 
                                      TRUE ~ "more recent")) %>%
   mutate(across(where(is.character), tolower)) %>%
   arrange(desc(year_built))
 )
```

    ## # A tibble: 3,361 × 6
    ##       id heating_type   no_of_storeys year_built window_type age_of_building
    ##    <dbl> <chr>                  <dbl>      <dbl> <chr>       <chr>          
    ##  1 13698 forced air gas            27       2019 double pane more recent    
    ##  2 13705 forced air gas            27       2019 double pane more recent    
    ##  3 12036 hot water                 43       2018 double pane more recent    
    ##  4 12719 forced air gas             7       2018 double pane more recent    
    ##  5 11287 electric                   8       2017 thermal     more recent    
    ##  6 11289 forced air gas            20       2017 double pane more recent    
    ##  7 11310 hot water                 14       2017 thermal     more recent    
    ##  8 12317 hot water                 30       2017 thermal     more recent    
    ##  9 12319 hot water                 37       2017 double pane more recent    
    ## 10 12936 forced air gas            29       2017 double pane more recent    
    ## # … with 3,351 more rows

First, I piped the apt_buildings data into the `select` function where I
selected five variables that will be appropriate for milestone 3. Next,
I piped it into the `filter` function to remove all missing values in
the selected variables. I piped that again into the `mutate` function
where I created a new variable named `age_of_building` which grouped the
years that the buildings were erected into 5 categories. The variable
names are in lowercase but the character values are in uppercase. So, I
cleaned the dataset by converting every character to lowercase for
uniformity. Finally, I rearranged the dataset by arranging the
`year_built` variable in descending order.
<!----------------------------------------------------------------------------->

*When you are done, knit an `md` file. This is what we will mark! Make
sure to open it and check that everything has knitted correctly before
submitting your tagged release.*

### Attribution

Thanks to Victor Yuan for mostly putting this together.
