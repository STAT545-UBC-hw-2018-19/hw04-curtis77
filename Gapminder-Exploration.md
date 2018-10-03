Gapminder-Exploration
================

``` r
options(warn = -1) # supresses warnings
suppressPackageStartupMessages(library(tidyverse))
```

    ## Note: the specification for S3 class "difftime" in package 'lubridate' seems equivalent to one from package 'hms': not turning on duplicate class definitions for this class.

``` r
suppressPackageStartupMessages(library(gapminder))
```

R Markdown
----------

``` r
library(gapminder)
library(tidyverse)
```

Data Reshaping Task
-------------------

For the reshaping task, I chose activity number 3. In addition, I chose to compute the median life expectancy for each combination of continent and year. To reformat the data with columns for each of the continents, I used the the spread function.

``` r
df <- gapminder %>%
  group_by(continent, year) %>%
  summarize(medLifeExp = median(lifeExp)) %>% # get median life expectancy for each combination of year and continent
  spread(key = "continent", value = "medLifeExp") # creates a variable for each continent

knitr::kable(df) # print table
```

|  year|   Africa|  Americas|    Asia|   Europe|  Oceania|
|-----:|--------:|---------:|-------:|--------:|--------:|
|  1952|  38.8330|    54.745|  44.869|  65.9000|  69.2550|
|  1957|  40.5925|    56.074|  48.284|  67.6500|  70.2950|
|  1962|  42.6305|    58.299|  49.325|  69.5250|  71.0850|
|  1967|  44.6985|    60.523|  53.655|  70.6100|  71.3100|
|  1972|  47.0315|    63.441|  56.950|  70.8850|  71.9100|
|  1977|  49.2725|    66.353|  60.765|  72.3350|  72.8550|
|  1982|  50.7560|    67.405|  63.739|  73.4900|  74.2900|
|  1987|  51.6395|    69.498|  66.295|  74.8150|  75.3200|
|  1992|  52.4290|    69.862|  68.690|  75.4510|  76.9450|
|  1997|  52.7590|    72.146|  70.265|  76.1160|  78.1900|
|  2002|  51.2355|    72.047|  71.028|  77.5365|  79.7400|
|  2007|  52.9265|    72.899|  72.396|  78.6085|  80.7195|

``` r
ggplot(df, aes(year, Asia)) + 
    geom_line() + # plots a line plot
    ggtitle("Asia Median Life Expectancy versus Year") + 
    xlab("Year") + 
    ylab("Median Life Expectancy")
```

![](Gapminder-Exploration_files/figure-markdown_github/unnamed-chunk-3-1.png)

A very simple task with this new data format is the ability to plot median life expectancy for some given continent versus year. Each continent is now treated as a quantitative variable, so we can use, for example, a line plot or scatterplot. As an example, I choose to plot the median life expectancy versus year for Asia, using a line splot.

Join Task
---------

For this task, I chose activity 1. I also experimented with the merge function. I chose to join the gapminder dataset with a new dataset containing 7 countries from the original gapminder dataset, with their capitols and currencies. I filtered by the year 2007 to ensure I don't have multiple copies of each country in my final dataset.

``` r
olddf <- gapminder %>%
  filter(year == 2007) # keep only data points from 2007 

newdf <- tribble(
  ~country, ~capitol, ~currency,
  'Germany', 'Berlin', 'Euro',
  'United Kingdom', 'London', 'Pound',
  'Italy', 'Rome', 'Euro',
  'France', ' Paris', 'Euro',
  'Japan', 'Tokyo', 'Yen',
  'China', 'Beijing', 'Yuan',
  'Korea, Rep.', 'Seoul', 'Won'
) # creates a new dataset with various countries and their capitols

df <- inner_join(olddf, newdf, by = 'country') # joins both created tables by country
dfMerge <- merge(olddf, newdf, by.x = 'country') # joins tables again, except with different function

knitr::kable(df) # print table created with join
```

| country        | continent |  year|  lifeExp|         pop|  gdpPercap| capitol | currency |
|:---------------|:----------|-----:|--------:|-----------:|----------:|:--------|:---------|
| China          | Asia      |  2007|   72.961|  1318683096|   4959.115| Beijing | Yuan     |
| France         | Europe    |  2007|   80.657|    61083916|  30470.017| Paris   | Euro     |
| Germany        | Europe    |  2007|   79.406|    82400996|  32170.374| Berlin  | Euro     |
| Italy          | Europe    |  2007|   80.546|    58147733|  28569.720| Rome    | Euro     |
| Japan          | Asia      |  2007|   82.603|   127467972|  31656.068| Tokyo   | Yen      |
| Korea, Rep.    | Asia      |  2007|   78.623|    49044790|  23348.140| Seoul   | Won      |
| United Kingdom | Europe    |  2007|   79.425|    60776238|  33203.261| London  | Pound    |

``` r
knitr::kable(dfMerge) # print table created with merge
```

| country        | continent |  year|  lifeExp|         pop|  gdpPercap| capitol | currency |
|:---------------|:----------|-----:|--------:|-----------:|----------:|:--------|:---------|
| China          | Asia      |  2007|   72.961|  1318683096|   4959.115| Beijing | Yuan     |
| France         | Europe    |  2007|   80.657|    61083916|  30470.017| Paris   | Euro     |
| Germany        | Europe    |  2007|   79.406|    82400996|  32170.374| Berlin  | Euro     |
| Italy          | Europe    |  2007|   80.546|    58147733|  28569.720| Rome    | Euro     |
| Japan          | Asia      |  2007|   82.603|   127467972|  31656.068| Tokyo   | Yen      |
| Korea, Rep.    | Asia      |  2007|   78.623|    49044790|  23348.140| Seoul   | Won      |
| United Kingdom | Europe    |  2007|   79.425|    60776238|  33203.261| London  | Pound    |

I was able to get the exact same tables using the inner join and merge functions. Instead of the inner join function, I could have also used the right join function, and gotten the same result, assuming I treat the original gapmidner dataset as the left dataset and the new dataset as the right dataset. This is because the new dataset I created only contained a subset of the countries in the gapminder dataset. Using the left join, however, would have resulted in a dataset with many rows containing an n/a values, unless I treated the new dataset as the left dataset.
