
# Advanced Data Viz

## Intended Learning Outcomes {#ilo-intro}

By the end of this chapter you should be able to:

* Transform simple data sets from wide-form to long-form
* Create and customize advanced types of plots

## Reading

Before working through this chapter, read [Data Types in R](https://statsandr.com/blog/data-types-in-r/).

## Sources

For the final chapter, we're going to do an adapted version of materials taken from:

> [Nordmann, E., McAleer, P., Toivo, W., Paterson, H. & DeBruine, L. (accepted). Data visualisation using R, for researchers who don't use R. Advances in Methods and Practices in Psychological Science.](https://psyteachr.github.io/introdataviz/)

And:

> * [Applied Data Skils: Customising visualisations](https://psyteachr.github.io/ads-v1/custom.html) (from the PsyTeachR team)

You may find it particularly helpful to read both of these sources in full as a further resource.

## Set-up

Open your course project and do the following:

* Create and save a new R Markdown document named `chapter_5.Rmd`, get rid of the default template text from line 11 onwards.
* Add the below code to the set-up chunk and then run the code to load the packages and data.


```r
library(tidyverse)
library(patchwork)
library(gapminder)
library(plotly)
library(gganimate)
gapminder_wide <- read_csv("https://raw.githubusercontent.com/emilynordmann/AllOfUs-intro-to-r/main/book/data/gapminder_wide.csv")
```

## Modified Gapminder dataset

The dataset you have loaded is a modified version of the <code class='package'>gapminder</code> dataset we've used in previous chapters. First, we've reduced the amount of data. The original data set has data every five years from 1952 - 2007 and includes life expectancy, population, and GDP per capita for each country. We've reduced this to just life expectancy from 2002 - 2007 to make it easier to work with as we begin some basic wrangling.

The second change is that the data is structured in <a class='glossary' target='_blank' title='A data format where all of the observations about one subject are in the same row' href='https://psyteachr.github.io/glossary/w#wide'>wide</a> format. Traditionally, researchers have been taught data skills using wide-format data. Wide-format data typically has one row of data for each participant/entity (in this case each country), with separate columns for each score or variable. For repeated-measures variables, the dependent variable is split across different columns. For between-groups variables, a separate column is added to encode the group to which a participant or observation belongs. 

The modified Gapminder data is now in wide format (see Table\ \@ref(tab:wide-data)), where each country's life expectancy is split across two columns for the repeated-measures factor (year). This data structure is typically intuitive for humans to read and enter data into, but the same is not true for a programming language. 

<table>
<caption>(\#tab:wide-data)Data in wide format.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> country </th>
   <th style="text-align:left;"> continent </th>
   <th style="text-align:right;"> lifeExp_2002 </th>
   <th style="text-align:right;"> lifeExp_2007 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:right;"> 42.13 </td>
   <td style="text-align:right;"> 43.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:right;"> 75.65 </td>
   <td style="text-align:right;"> 76.42 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:right;"> 70.99 </td>
   <td style="text-align:right;"> 72.30 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Angola </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:right;"> 41.00 </td>
   <td style="text-align:right;"> 42.73 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Argentina </td>
   <td style="text-align:left;"> Americas </td>
   <td style="text-align:right;"> 74.34 </td>
   <td style="text-align:right;"> 75.32 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Australia </td>
   <td style="text-align:left;"> Oceania </td>
   <td style="text-align:right;"> 80.37 </td>
   <td style="text-align:right;"> 81.24 </td>
  </tr>
</tbody>
</table>

The <code class='package'>tidyverse</code> and functions like `ggplot()` require data to be in <a class='glossary' target='_blank' title='A data format where each observation is on a separate row' href='https://psyteachr.github.io/glossary/l#long'>long</a> format. Moving from using wide-format to long-format data sets can require a conceptual shift on the part of the researcher and one that usually only comes with practice and repeated exposure. 

Rather than different observations of the same measurement variable being split across columns, there is a single column for the measurement life expectancy, and a single column for the measurement of GDP. Each country however, has multiple rows of data, one for each observation (i.e., for each country there will be as many rows as there are levels of the repeated-measures variable, in this case year). Although there is some repetition of country and continent, each row is unique when looking at the combination of measures.



<table>
<caption>(\#tab:long)Data in the correct format for visualization.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> country </th>
   <th style="text-align:left;"> continent </th>
   <th style="text-align:left;"> year </th>
   <th style="text-align:right;"> lifeExp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 42.13 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 43.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 75.65 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 76.42 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 70.99 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 72.30 </td>
  </tr>
</tbody>
</table>

It can be initially difficult to figure out how many variables are in your data set and how the long-form structure should look however, a useful rule of thumb when working with data in R for visualization is that *anything that shares an axis should probably be in the same column*. For example, a simple boxplot showing life expectancy by year (collapsing across all countries) would display the variable `year` on the x-axis with bars representing `2002` and `2007` data, and `lifeExp` on the y-axis. Therefore, all the data relating to `year` should be in one column, and all the data relating to `lifeExp` should be in a separate single column, rather than being split like in wide-format data.

### Wide to long format

In this course we have adapted a data set to have a single measurement variable to simplify the data wrangling. In the next course, we'll increase the complexity of the data sets we'll tidy, however, it is worth normalizing that trial-and-error is part of the process of learning how to apply these functions to new data sets and structures. Data visualization can be a useful way to scaffold learning these data transformations because they can provide a concrete visual check as to whether you have done what you intended to do with your data.

#### `pivot_longer()`

The `pivot_longer()` function comes from the <code class='package'>tidyverse</code> package <code class='package'>tidyr</code> and is used to transform data from wide-to-long.

-   As with the other tidyverse functions, the first argument specifies the dataset to use as the base, in this case `gapminder_wide`. This argument name is often dropped in examples.

-   `cols` specifies all the columns you want to transform. The easiest way to visualise this is to think about which columns would be the same in the new long-form dataset and which will change. If you refer back to Table\ \@ref(tab:wide-data), you can see that `country` and `continent` remain, while the columns that contain the measurements change. The colon notation `first_column:last_column` is used to select all variables from the first column specified to the last (we only have two columns to select so this doesn't save us any time here but it's useful to know). In our code, `cols` specifies that the columns we want to transform are `lifeExp_2002` to `lifeExp_2007`.

-   `names_to` specifies the name of the new column that will be created. This column will contain the names of the selected existing columns, so we'll call it `year`.

-   Finally, `values_to` names the new column that will contain the values in the selected columns. In this case we'll call it `lifeExp`. 

At this point you may find it helpful to go back and compare `dat` and `long` again to see how each argument matches up with the output of the table.


```r
gapminder_long <- pivot_longer(gapminder_wide, 
                     cols = lifeExp_2002:lifeExp_2007, 
                     names_to = "year", 
                     values_to = "lifeExp")
```


<table>
<caption>(\#tab:long1-example)Data in long format with messy labels.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> country </th>
   <th style="text-align:left;"> continent </th>
   <th style="text-align:left;"> year </th>
   <th style="text-align:right;"> lifeExp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> lifeExp_2002 </td>
   <td style="text-align:right;"> 42.13 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> lifeExp_2007 </td>
   <td style="text-align:right;"> 43.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> lifeExp_2002 </td>
   <td style="text-align:right;"> 75.65 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> lifeExp_2007 </td>
   <td style="text-align:right;"> 76.42 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> lifeExp_2002 </td>
   <td style="text-align:right;"> 70.99 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> lifeExp_2007 </td>
   <td style="text-align:right;"> 72.30 </td>
  </tr>
</tbody>
</table>

#### Step 2: `pivot_longer()` adjusted

The problem with the above long-format data-set is that the values `year` have been taken from the prior column names and they're a bit messy. There's a few different ways we could deal with this, but the option we'll choose is to adjust the arguments to `pivot_longer()`.

First, instead of specifying one name to `names_to`, we're going to tell it to create two columns, `label` and `year`. The additional argument `names_sep` tells R how to split the information between the two columns. This is when taking care to name your variables consistently and meaningfully pays off. Because the word to the left of the separator (`_`) is always the DV label and the word to the right is always the condition of the repeated-measures IV, it is easy to automatically split the columns.


```r
gapminder_long <- pivot_longer(data = gapminder_wide, 
                     cols = lifeExp_2002:lifeExp_2007, 
                     names_to = c("label", "year"),
                     names_sep = "_", 
                     values_to = "lifeExp")
```

<table>
<caption>(\#tab:long-example)Data in long format with additional name columns</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> country </th>
   <th style="text-align:left;"> continent </th>
   <th style="text-align:left;"> label </th>
   <th style="text-align:left;"> year </th>
   <th style="text-align:right;"> lifeExp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> lifeExp </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 42.13 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> lifeExp </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 43.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> lifeExp </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 75.65 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> lifeExp </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 76.42 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> lifeExp </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 70.99 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> lifeExp </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 72.30 </td>
  </tr>
</tbody>
</table>

We can then use `%>%` to add on the `select()` function to drop the column `label` as it's unnecessary.


```r
gapminder_long <- pivot_longer(data = gapminder_wide, 
                     cols = lifeExp_2002:lifeExp_2007, 
                     names_to = c("label", "year"),
                     names_sep = "_", 
                     values_to = "lifeExp") %>%
  select(-label)
```

<table>
<caption>(\#tab:long-example2)Data in final long format</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> country </th>
   <th style="text-align:left;"> continent </th>
   <th style="text-align:left;"> year </th>
   <th style="text-align:right;"> lifeExp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 42.13 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 43.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 75.65 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 76.42 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> 2002 </td>
   <td style="text-align:right;"> 70.99 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:left;"> 2007 </td>
   <td style="text-align:right;"> 72.30 </td>
  </tr>
</tbody>
</table>

We're almost ready for analysis now. One final note, be careful not to calculate demographic descriptive statistics from long-form data sets. Because the process of transformation has introduces some repetition, the wide-format dataset where one row equals one country should be used for demographic information. Now that we have the experimental data in the right form, we can begin to create some useful visualizations. 

## Grouped plots

In the long-form dataset, because each variable has its own column, it's much easier to specify that you want to create grouped plots.

For example, we can create grouped density plots by adding `fill = year` because all the information about `year` is in a single variable, rather than being split across columns.


```r
ggplot(gapminder_long, aes(x = lifeExp, fill = year)) +
  geom_density(alpha = 0.75)
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-4-1.png" width="100%" style="display: block; margin: auto;" />

Or we can create a grouped scatterplot by adding mapping `year` against `lifeExp` and adding `color = continent`. 


```r
ggplot(gapminder_long, aes(x = lifeExp, y = year, color = continent)) +
  geom_jitter(height = .1, width = 0) 
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-5-1.png" width="100%" style="display: block; margin: auto;" />

## Accessible color schemes

One of the drawbacks of using `ggplot2` for visualization is that the default color scheme is not accessible (or visually appealing). The red and green default palette is difficult for color-blind people to differentiate, and also does not display well in greyscale. You can specify exact custom colors for your plots, but one easy option is to use a custom color palette. These take the same arguments as their default `scale` sister functions for updating axis names and labels, but display plots in contrasting colors that can be read by color-blind people and that also print well in grey scale. For categorical colors, the "Set2", "Dark2" and "Paired" palettes from the `brewer` scale functions are colorblind-safe (but are hard to distinguish in grey scale). For continuous colors, such as when color is representing the magnitude of a correlation in a tile plot, the `viridis` scale functions provide a number of different colorblind and grey scale-safe options.


```r
ggplot(gapminder_long, aes(x = lifeExp, y = year, color = continent)) +
  geom_jitter(height = .1, width = 0) +
  scale_color_brewer(palette = "Dark2",
                     name = "Continent") +
  theme_minimal()
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-6-1.png" width="100%" style="display: block; margin: auto;" />

### Grouped violin-boxplots

As with previous plots, another variable can be mapped to `fill` for the violin-boxplot and we can also use `stat_summary()` to add in the mean and error bars. However, simply adding `fill` to the mapping causes the different components of the plot to become misaligned because they have different default positions:


```r
ggplot(gapminder_long, aes(x = continent, y= lifeExp, fill = year)) +
  geom_violin() +
  geom_boxplot(width = .2, 
               fatten = NULL) +
  stat_summary(fun = "mean",  geom = "point") +
  stat_summary(fun.data = "mean_se", 
               geom = "errorbar", 
               width = .1) +
  scale_fill_brewer(palette = "Dark2") +
  theme(legend.position = "bottom")
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-7-1.png" width="100%" style="display: block; margin: auto;" />

To rectify this we need to adjust the argument `position` for each of the misaligned layers. `position_dodge()` instructs R to move (dodge) the position of the plot component by the specified value; finding what value looks best can sometimes take trial and error.  We can also set the `alpha` values to make it easier to distinguish each layer of the plot.


```r
ggplot(gapminder_long, aes(x = continent, y= lifeExp, fill = year)) +
  geom_violin(position = position_dodge(0.9)) +
  geom_boxplot(width = .2, 
               fatten = NULL,
               position = position_dodge(0.9)) +
  stat_summary(fun = "mean",  geom = "point",
               position = position_dodge(0.9)) +
  stat_summary(fun.data = "mean_se", 
               geom = "errorbar", 
               width = .1,
               position = position_dodge(0.9)) +
  scale_fill_brewer(palette = "Dark2") +
  theme(legend.position = "bottom")
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-8-1.png" width="100%" style="display: block; margin: auto;" />

### Complex layer plots

A more complex plot can be produced that takes advantage of the layers to visualize not only the overall interaction, but the change across repeated-measures conditions.

This code is more complex than all prior code because it does not use a universal mapping of the plot aesthetics. In our code so far, the aesthetic mapping (`aes`) of the plot has been specified in the first line of code because all layers used the same mapping. However, is is also possible for each layer to use a different mapping -- we encourage you to build up the plot by running each line of code sequentially to see how it all combines.

-   The first call to `ggplot()` sets up the default mappings of the plot that will be used unless otherwise specified - the `x`, `y` and `group` variable. Note the addition  of `shape`, which will vary the shape of the geom according to the `year` variable.
-   `geom_point()` overrides the default mapping by setting its own `colour` to draw the data points from each `continent` in a different color. `alpha` is set to a low value to aid readability. 
-   Similarly, `geom_line()` overrides the default grouping variable so that a line is drawn to connect the individual data points for each *country* (`group = country`) rather than each `year`, and also sets the colors.  
-   Finally, the calls to `stat_summary()` remain largely as they were, with the exception of setting `colour = "black"` and `size = 2` so that the overall means and error bars can be more easily distinguished from the individual data points. Because they do not specify an individual mapping, they use the defaults (e.g., the lines are connected by `continent`). For the error bars, the lines are again made solid.


```r
ggplot(gapminder_long, aes(x = year, y = lifeExp, 
                     group = continent, shape = continent)) +
  # adds raw data points in each condition
  geom_point(aes(colour = continent),alpha = .4) +
  # add lines to connect each participant's data points across conditions
  geom_line(aes(group = country, colour = continent), alpha = .6) +
  # add data points representing cell means
  stat_summary(fun = "mean", geom = "point", size = 2, colour = "black") +
  # add lines connecting cell means by condition
  stat_summary(fun = "mean", geom = "line", colour = "black") +
  # add errorbars to cell means
  stat_summary(fun.data = "mean_se", geom = "errorbar", 
               width = .1, colour = "black") +
  # change colours and theme
  scale_color_brewer(palette = "Paired") +
  theme_minimal() +
  theme(legend.position = "bottom") 
```

<div class="figure" style="text-align: center">
<img src="06-custom-viz_files/figure-html/figure30-1.png" alt="Complex layered plot with by-country data." width="100%" />
<p class="caption">(\#fig:figure30)Complex layered plot with by-country data.</p>
</div>

## Facets

There are situations in which it may be useful to create separate plots for each level of a variable using facets. This can also help with accessibility when used instead of or in addition to group colors. The below code is an adaptation of the code used to produce the histogram in which it may be easier to see how the relationship changes when the data are not overlaid.

Additionally, previously when we edited the main axis labels we used the `scale_*` functions. These functions are useful to know because they allow you to customize many aspects of the scale, such as the breaks and limits. However, if you only need to change the main axis `name`, there is a quicker way to do so using `labs()` which can be used to add or remove (by specifying `NULL`) for the axis you want to rename:


```r
ggplot(gapminder_long, aes(x = lifeExp)) +
  geom_histogram(colour = "black") +
  facet_wrap(~continent, nrow = 5) +
  guides(fill = "none")+
  theme_minimal() +
  labs(y = NULL, title = "Life Expectancy by Continent")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<div class="figure" style="text-align: center">
<img src="06-custom-viz_files/figure-html/figure31-1.png" alt="Faceted histogram" width="100%" />
<p class="caption">(\#fig:figure31)Faceted histogram</p>
</div>

## Redundant aesthetics

So far when we have produced plots with colors, the colors were the only way that different levels of a variable were indicated, but it is sometimes preferable to indicate levels with both color and other means, such as facets or x-axis categories.

The code below adds `fill = continent` to the histograms and use the Viridis color palette to customize the colors. Specifying a `fill` variable means that by default, R produces a legend for that variable. 


```r
ggplot(gapminder_long, aes(x = lifeExp, fill = continent)) +
  geom_histogram(colour = "black") +
  facet_wrap(~continent, nrow = 5) +
  scale_fill_viridis_d() +
  theme_minimal() +
  labs(y = NULL, title = "Life Expectancy by Continent")
```

<div class="figure" style="text-align: center">
<img src="06-custom-viz_files/figure-html/unnamed-chunk-9-1.png" alt="Faceted histogram with redundant legend" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-9)Faceted histogram with redundant legend</p>
</div>

However, the use of color is redundant with the facet labels, so you can remove this legend with the `guides` function to make better use of the plot space:


```r
ggplot(gapminder_long, aes(x = lifeExp, fill = continent)) +
  geom_histogram(colour = "black") +
  facet_wrap(~continent, nrow = 5) +
  scale_fill_viridis_d() +
  guides(fill = "none")+
  theme_minimal() +
  labs(y = NULL, title = "Life Expectancy by Continent")
```

<div class="figure" style="text-align: center">
<img src="06-custom-viz_files/figure-html/unnamed-chunk-10-1.png" alt="Faceted histogram with redundant legend removed" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-10)Faceted histogram with redundant legend removed</p>
</div>

## Advanced Plots

For the advanced plots, we will use some custom functions: `geom_split_violin()` and `geom_flat_violin()`, which you can access through the `introdataviz` package. These functions are modified from [@raincloudplots].


```r
# how to install the introdataviz package to get split and half violin plots
devtools::install_github("psyteachr/introdataviz")

# if you get the error "there is no package called "devtools" run:
# install.packages("devtools") 
```

### Split-violin plots

Split-violin plots remove the redundancy of mirrored violin plots and make it easier to compare the distributions between multiple conditions. For this plot we'll also use the function `filter()` to just keep the data from Africa and Asia. The `%in%` notation means that it will retain any values that match one of the possibilities presented: 


```r
gapminder_long %>%
  filter(continent %in% c("Africa", "Asia")) %>%
  ggplot(aes(x = year, y= lifeExp, fill = continent)) +
  introdataviz::geom_split_violin(alpha = .4) +
  geom_boxplot(width = .2, position = position_dodge(.4)) +
  scale_fill_brewer(palette = "Paired") +
  theme(legend.position = "bottom") +
  labs(fill = NULL) +
  theme_minimal() +
  theme(legend.position = "bottom")
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-12-1.png" width="100%" style="display: block; margin: auto;" />

### Raincloud plots

Raincloud plots combine a density plot, boxplot, raw data points, and any desired summary statistics for a complete visualization of the data. They are so called because the density plot plus raw data is reminiscent of a rain cloud. The point and line in the center of each cloud represents its mean and 95% CI. The rain represents individual data points.

This is an incredibly advanced plot and we don't expect that you would be able to create this on your own from scratch at this point in your training. However, hopefully you should now have a sense of how the plot has built up and how the layers are working together.


```r
rain_height <- .1

gapminder_long %>%
  filter(continent %in% c("Americas", "Asia")) %>%
  ggplot(aes(x = "", y = lifeExp, fill = year)) +
  # clouds
  introdataviz::geom_flat_violin(alpha = 0.6,
    position = position_nudge(x = rain_height+.05)) +
  # rain
  geom_point(aes(colour = year), size = 2, alpha = .7, 
              position = position_jitter(width = rain_height, height = 0)) +
  # boxplots
  geom_boxplot(width = rain_height, alpha = 0.4, 
               position = position_nudge(x = -rain_height*2)) +
  # mean and SE point in the cloud
  stat_summary(fun.data = mean_cl_normal, 
               mapping = aes(color = year), 
               position = position_nudge(x = rain_height * 3)) +
  # adjust layout
  scale_x_discrete(name = "", expand = c(rain_height*3, 0, 0, 0.7)) +
  coord_flip() +
  facet_wrap(~continent, nrow = 2) +
  # custom colours and theme
  scale_fill_brewer(palette = "Accent", name = "Continent") +
  scale_colour_brewer(palette = "Accent") +
  theme_minimal() +
  theme(panel.grid.major.y = element_blank(),
        legend.position = c(0.2, 0.8),
        legend.background = element_rect(fill = "white", color = "white")) +
  guides(fill = "none")
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-13-1.png" width="100%" style="display: block; margin: auto;" />

### Interactive plots

The <code class='package'>plotly</code> package can be used to make interactive graphs. Assign your ggplot to a variable and then use the function `ggplotly()` on the plot object. Note that interactive plots only work in HTML files, not PDFs or Word files and that some advanced types of plots won't render well (e.g., it struggles with the raincloud plot).

For example, let's create the grouped scatterplot of life expectancy by year and continent again:


```r
point_plot <- ggplot(gapminder_long, aes(x = lifeExp, 
                                         y = year, 
                                         color = continent)) +
  geom_jitter(height = .1, width = 0) 

ggplotly(point_plot)
```

<div class="figure" style="text-align: center">

```{=html}
<div id="htmlwidget-ce0b1aeb300d93f1ed88" style="width:100%;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-ce0b1aeb300d93f1ed88">{"x":{"data":[{"x":[70.994,72.301,41.003,42.731,54.406,56.728,46.634,50.728,50.65,52.295,47.36,49.58,49.856,50.43,43.308,44.741,50.525,50.651,62.974,65.152,44.966,46.462,52.97,55.322,46.832,48.328,53.373,54.791,69.806,71.338,49.348,51.579,55.24,58.04,50.725,52.947,56.761,56.735,58.041,59.448,58.453,60.022,53.676,56.007,45.504,46.388,50.992,54.11,44.593,42.592,43.753,45.678,72.737,73.952,57.286,59.443,45.009,48.303,51.818,54.467,62.247,64.164,71.954,72.801,69.615,71.164,44.026,42.082,51.479,52.906,54.496,56.867,46.608,46.859,75.744,76.442,43.413,46.242,64.337,65.528,61.6,63.062,41.012,42.568,45.936,48.159,53.365,49.339,56.369,58.556,43.869,39.613,49.651,52.517,57.561,58.42,73.042,73.923,47.813,51.542,39.193,42.384,39.989,43.487],"y":[0.995973340142518,2.08635395374149,1.04835107531399,2.02303223051131,1.05002135345712,1.99117441927083,1.08116230024025,1.9131263661664,0.910565622989088,1.97561591868289,0.951988277165219,2.04055173792876,1.01272339895368,1.97689606235363,1.09232838652097,1.96993106170557,1.02200713986531,1.9185909062624,0.905023279460147,1.94148160829209,0.991040861094371,2.00321234893054,0.935301561187953,2.08052539513446,0.959713347721845,2.05287331598811,0.961897308891639,2.07320956955664,1.02621815470047,1.93304706141353,1.05057493215427,2.08966169655323,1.07865994614549,2.06525230882689,1.03953496357426,1.93231920674443,1.06729154787026,2.04514344418421,1.04736574888229,2.02242897455581,1.01514913998544,1.92035900116898,1.01074475133792,1.94224162045866,1.04997389134951,1.91763445078395,1.08102297750302,1.94540037801489,0.913030921295285,1.93550406638533,0.951997865689918,2.08980938624591,0.979383523669094,2.00776786967181,0.961382188135758,2.02036174582317,0.976677647186443,2.07105303835124,1.06576206791215,1.96109282821417,1.03681811406277,2.04677988840267,0.94566860194318,1.99427914484404,0.937930760905147,1.95814521680586,1.07000679667108,2.01292173261754,0.960438295081258,1.91289470996708,0.929997259937227,1.90294378334656,0.994751964556053,2.01848047398962,1.00215294416994,2.06263420190662,1.05241671712138,2.04143072068691,0.947755601722747,2.04334695003927,0.914254285069182,2.0848060939461,0.989910556701943,2.07875369680114,1.08809615187347,1.91088600084186,0.930127572687343,1.94302534731105,0.918520227540284,1.93583557782695,0.990194280585274,1.9316551156342,0.954673184547573,1.92695844769478,1.03972504213452,2.08041987111792,1.0899004212115,2.00076324543916,0.99112027627416,1.90286498707719,0.931484150560573,1.98655203813687,1.06390436193906,2.05511567490175],"text":["lifeExp: 70.994<br />year: 2002<br />continent: Africa","lifeExp: 72.301<br />year: 2007<br />continent: Africa","lifeExp: 41.003<br />year: 2002<br />continent: Africa","lifeExp: 42.731<br />year: 2007<br />continent: Africa","lifeExp: 54.406<br />year: 2002<br />continent: Africa","lifeExp: 56.728<br />year: 2007<br />continent: Africa","lifeExp: 46.634<br />year: 2002<br />continent: Africa","lifeExp: 50.728<br />year: 2007<br />continent: Africa","lifeExp: 50.650<br />year: 2002<br />continent: Africa","lifeExp: 52.295<br />year: 2007<br />continent: Africa","lifeExp: 47.360<br />year: 2002<br />continent: Africa","lifeExp: 49.580<br />year: 2007<br />continent: Africa","lifeExp: 49.856<br />year: 2002<br />continent: Africa","lifeExp: 50.430<br />year: 2007<br />continent: Africa","lifeExp: 43.308<br />year: 2002<br />continent: Africa","lifeExp: 44.741<br />year: 2007<br />continent: Africa","lifeExp: 50.525<br />year: 2002<br />continent: Africa","lifeExp: 50.651<br />year: 2007<br />continent: Africa","lifeExp: 62.974<br />year: 2002<br />continent: Africa","lifeExp: 65.152<br />year: 2007<br />continent: Africa","lifeExp: 44.966<br />year: 2002<br />continent: Africa","lifeExp: 46.462<br />year: 2007<br />continent: Africa","lifeExp: 52.970<br />year: 2002<br />continent: Africa","lifeExp: 55.322<br />year: 2007<br />continent: Africa","lifeExp: 46.832<br />year: 2002<br />continent: Africa","lifeExp: 48.328<br />year: 2007<br />continent: Africa","lifeExp: 53.373<br />year: 2002<br />continent: Africa","lifeExp: 54.791<br />year: 2007<br />continent: Africa","lifeExp: 69.806<br />year: 2002<br />continent: Africa","lifeExp: 71.338<br />year: 2007<br />continent: Africa","lifeExp: 49.348<br />year: 2002<br />continent: Africa","lifeExp: 51.579<br />year: 2007<br />continent: Africa","lifeExp: 55.240<br />year: 2002<br />continent: Africa","lifeExp: 58.040<br />year: 2007<br />continent: Africa","lifeExp: 50.725<br />year: 2002<br />continent: Africa","lifeExp: 52.947<br />year: 2007<br />continent: Africa","lifeExp: 56.761<br />year: 2002<br />continent: Africa","lifeExp: 56.735<br />year: 2007<br />continent: Africa","lifeExp: 58.041<br />year: 2002<br />continent: Africa","lifeExp: 59.448<br />year: 2007<br />continent: Africa","lifeExp: 58.453<br />year: 2002<br />continent: Africa","lifeExp: 60.022<br />year: 2007<br />continent: Africa","lifeExp: 53.676<br />year: 2002<br />continent: Africa","lifeExp: 56.007<br />year: 2007<br />continent: Africa","lifeExp: 45.504<br />year: 2002<br />continent: Africa","lifeExp: 46.388<br />year: 2007<br />continent: Africa","lifeExp: 50.992<br />year: 2002<br />continent: Africa","lifeExp: 54.110<br />year: 2007<br />continent: Africa","lifeExp: 44.593<br />year: 2002<br />continent: Africa","lifeExp: 42.592<br />year: 2007<br />continent: Africa","lifeExp: 43.753<br />year: 2002<br />continent: Africa","lifeExp: 45.678<br />year: 2007<br />continent: Africa","lifeExp: 72.737<br />year: 2002<br />continent: Africa","lifeExp: 73.952<br />year: 2007<br />continent: Africa","lifeExp: 57.286<br />year: 2002<br />continent: Africa","lifeExp: 59.443<br />year: 2007<br />continent: Africa","lifeExp: 45.009<br />year: 2002<br />continent: Africa","lifeExp: 48.303<br />year: 2007<br />continent: Africa","lifeExp: 51.818<br />year: 2002<br />continent: Africa","lifeExp: 54.467<br />year: 2007<br />continent: Africa","lifeExp: 62.247<br />year: 2002<br />continent: Africa","lifeExp: 64.164<br />year: 2007<br />continent: Africa","lifeExp: 71.954<br />year: 2002<br />continent: Africa","lifeExp: 72.801<br />year: 2007<br />continent: Africa","lifeExp: 69.615<br />year: 2002<br />continent: Africa","lifeExp: 71.164<br />year: 2007<br />continent: Africa","lifeExp: 44.026<br />year: 2002<br />continent: Africa","lifeExp: 42.082<br />year: 2007<br />continent: Africa","lifeExp: 51.479<br />year: 2002<br />continent: Africa","lifeExp: 52.906<br />year: 2007<br />continent: Africa","lifeExp: 54.496<br />year: 2002<br />continent: Africa","lifeExp: 56.867<br />year: 2007<br />continent: Africa","lifeExp: 46.608<br />year: 2002<br />continent: Africa","lifeExp: 46.859<br />year: 2007<br />continent: Africa","lifeExp: 75.744<br />year: 2002<br />continent: Africa","lifeExp: 76.442<br />year: 2007<br />continent: Africa","lifeExp: 43.413<br />year: 2002<br />continent: Africa","lifeExp: 46.242<br />year: 2007<br />continent: Africa","lifeExp: 64.337<br />year: 2002<br />continent: Africa","lifeExp: 65.528<br />year: 2007<br />continent: Africa","lifeExp: 61.600<br />year: 2002<br />continent: Africa","lifeExp: 63.062<br />year: 2007<br />continent: Africa","lifeExp: 41.012<br />year: 2002<br />continent: Africa","lifeExp: 42.568<br />year: 2007<br />continent: Africa","lifeExp: 45.936<br />year: 2002<br />continent: Africa","lifeExp: 48.159<br />year: 2007<br />continent: Africa","lifeExp: 53.365<br />year: 2002<br />continent: Africa","lifeExp: 49.339<br />year: 2007<br />continent: Africa","lifeExp: 56.369<br />year: 2002<br />continent: Africa","lifeExp: 58.556<br />year: 2007<br />continent: Africa","lifeExp: 43.869<br />year: 2002<br />continent: Africa","lifeExp: 39.613<br />year: 2007<br />continent: Africa","lifeExp: 49.651<br />year: 2002<br />continent: Africa","lifeExp: 52.517<br />year: 2007<br />continent: Africa","lifeExp: 57.561<br />year: 2002<br />continent: Africa","lifeExp: 58.420<br />year: 2007<br />continent: Africa","lifeExp: 73.042<br />year: 2002<br />continent: Africa","lifeExp: 73.923<br />year: 2007<br />continent: Africa","lifeExp: 47.813<br />year: 2002<br />continent: Africa","lifeExp: 51.542<br />year: 2007<br />continent: Africa","lifeExp: 39.193<br />year: 2002<br />continent: Africa","lifeExp: 42.384<br />year: 2007<br />continent: Africa","lifeExp: 39.989<br />year: 2002<br />continent: Africa","lifeExp: 43.487<br />year: 2007<br />continent: Africa"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"Africa","legendgroup":"Africa","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.34,75.32,63.883,65.554,71.006,72.39,79.77,80.653,77.86,78.553,71.682,72.889,78.123,78.782,77.158,78.273,70.847,72.235,74.173,74.994,70.734,71.878,68.978,70.259,58.137,60.916,68.565,70.198,72.047,72.567,74.902,76.195,70.836,72.899,74.712,75.537,70.755,71.752,69.906,71.421,77.778,78.746,68.976,69.819,77.31,78.242,75.307,76.384,72.766,73.747],"y":[0.965228631813079,1.90687516690232,0.911125574447215,1.92265397231095,1.02135369889438,2.00621731360443,1.01653037420474,2.05994267379865,0.927793504437432,1.9599069006741,0.917205481929705,1.98781228573062,1.06830322220922,2.0911289144773,1.09329653563909,2.06680916068144,0.996554503729567,2.03840765552595,1.0318286605645,1.90899012652226,0.935754675744101,2.06596458940767,1.00114556970075,2.07670658822171,0.965407538227737,1.92076810714789,0.975319949537516,1.9427523589693,1.08989315694198,1.91437904872,0.99414101052098,1.93016288997605,1.04092445075512,1.97104198285379,0.904348473297432,1.98303250353783,1.08124420801178,2.04407185041346,0.946524690743536,1.95542614618316,1.02868924685754,2.03835026877932,0.991748783737421,2.05523537849076,0.977892626216635,2.00940947169438,0.919224836071953,1.96064686584286,0.994346625497565,2.00198562205769],"text":["lifeExp: 74.340<br />year: 2002<br />continent: Americas","lifeExp: 75.320<br />year: 2007<br />continent: Americas","lifeExp: 63.883<br />year: 2002<br />continent: Americas","lifeExp: 65.554<br />year: 2007<br />continent: Americas","lifeExp: 71.006<br />year: 2002<br />continent: Americas","lifeExp: 72.390<br />year: 2007<br />continent: Americas","lifeExp: 79.770<br />year: 2002<br />continent: Americas","lifeExp: 80.653<br />year: 2007<br />continent: Americas","lifeExp: 77.860<br />year: 2002<br />continent: Americas","lifeExp: 78.553<br />year: 2007<br />continent: Americas","lifeExp: 71.682<br />year: 2002<br />continent: Americas","lifeExp: 72.889<br />year: 2007<br />continent: Americas","lifeExp: 78.123<br />year: 2002<br />continent: Americas","lifeExp: 78.782<br />year: 2007<br />continent: Americas","lifeExp: 77.158<br />year: 2002<br />continent: Americas","lifeExp: 78.273<br />year: 2007<br />continent: Americas","lifeExp: 70.847<br />year: 2002<br />continent: Americas","lifeExp: 72.235<br />year: 2007<br />continent: Americas","lifeExp: 74.173<br />year: 2002<br />continent: Americas","lifeExp: 74.994<br />year: 2007<br />continent: Americas","lifeExp: 70.734<br />year: 2002<br />continent: Americas","lifeExp: 71.878<br />year: 2007<br />continent: Americas","lifeExp: 68.978<br />year: 2002<br />continent: Americas","lifeExp: 70.259<br />year: 2007<br />continent: Americas","lifeExp: 58.137<br />year: 2002<br />continent: Americas","lifeExp: 60.916<br />year: 2007<br />continent: Americas","lifeExp: 68.565<br />year: 2002<br />continent: Americas","lifeExp: 70.198<br />year: 2007<br />continent: Americas","lifeExp: 72.047<br />year: 2002<br />continent: Americas","lifeExp: 72.567<br />year: 2007<br />continent: Americas","lifeExp: 74.902<br />year: 2002<br />continent: Americas","lifeExp: 76.195<br />year: 2007<br />continent: Americas","lifeExp: 70.836<br />year: 2002<br />continent: Americas","lifeExp: 72.899<br />year: 2007<br />continent: Americas","lifeExp: 74.712<br />year: 2002<br />continent: Americas","lifeExp: 75.537<br />year: 2007<br />continent: Americas","lifeExp: 70.755<br />year: 2002<br />continent: Americas","lifeExp: 71.752<br />year: 2007<br />continent: Americas","lifeExp: 69.906<br />year: 2002<br />continent: Americas","lifeExp: 71.421<br />year: 2007<br />continent: Americas","lifeExp: 77.778<br />year: 2002<br />continent: Americas","lifeExp: 78.746<br />year: 2007<br />continent: Americas","lifeExp: 68.976<br />year: 2002<br />continent: Americas","lifeExp: 69.819<br />year: 2007<br />continent: Americas","lifeExp: 77.310<br />year: 2002<br />continent: Americas","lifeExp: 78.242<br />year: 2007<br />continent: Americas","lifeExp: 75.307<br />year: 2002<br />continent: Americas","lifeExp: 76.384<br />year: 2007<br />continent: Americas","lifeExp: 72.766<br />year: 2002<br />continent: Americas","lifeExp: 73.747<br />year: 2007<br />continent: Americas"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(163,165,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"Americas","legendgroup":"Americas","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[42.129,43.828,74.795,75.635,62.013,64.062,56.752,59.723,72.028,72.961,81.495,82.208,62.879,64.698,68.588,70.65,69.451,70.964,57.046,59.545,79.696,80.745,82,82.603,71.263,72.535,66.662,67.297,77.045,78.623,76.904,77.588,71.028,71.993,73.044,74.241,65.033,66.803,59.908,62.069,61.34,63.785,74.193,75.64,63.61,65.483,70.303,71.688,71.626,72.777,78.77,79.972,70.815,72.396,73.053,74.143,76.99,78.4,68.564,70.616,73.017,74.249,72.37,73.422,60.308,62.698],"y":[1.08958359393291,2.05521609964781,0.934350432595238,1.90474253119901,1.07342923185788,1.91882288032211,0.937618540087715,2.08681549169123,0.993543061707169,2.08984424425289,1.01951073356904,2.02904903432354,1.01055035740137,1.94886964512989,1.09496865519322,1.96523125837557,0.924786194693297,2.03328903242946,0.995580186275765,2.0459612917155,1.09646325120702,2.02048158491962,1.02365753371269,1.97479162481613,0.913470799289644,2.09996880288236,0.952218502992764,2.05127158127725,1.02109782295302,2.01343633285724,0.902625067904592,2.04710280951112,1.03692819983698,2.09400603822432,0.94589459169656,1.93477591238916,0.922562722349539,2.08030726853758,1.00921246600337,2.0760741582606,0.903684136876836,2.06581368730403,1.08684644582681,1.96430005910806,1.03499299073592,1.90366181116551,1.04189905580133,1.93579979417846,0.934884092211723,2.08694563950412,0.912455360870808,2.09804606568068,1.03851128374226,2.00417595757172,0.985680208355188,2.02502565896139,1.08218253292143,1.96834679399617,0.985580316046253,2.0974485732615,0.907477445574477,2.02193835047074,0.95755749726668,2.05806043618359,0.992932177335024,1.93248591865413],"text":["lifeExp: 42.129<br />year: 2002<br />continent: Asia","lifeExp: 43.828<br />year: 2007<br />continent: Asia","lifeExp: 74.795<br />year: 2002<br />continent: Asia","lifeExp: 75.635<br />year: 2007<br />continent: Asia","lifeExp: 62.013<br />year: 2002<br />continent: Asia","lifeExp: 64.062<br />year: 2007<br />continent: Asia","lifeExp: 56.752<br />year: 2002<br />continent: Asia","lifeExp: 59.723<br />year: 2007<br />continent: Asia","lifeExp: 72.028<br />year: 2002<br />continent: Asia","lifeExp: 72.961<br />year: 2007<br />continent: Asia","lifeExp: 81.495<br />year: 2002<br />continent: Asia","lifeExp: 82.208<br />year: 2007<br />continent: Asia","lifeExp: 62.879<br />year: 2002<br />continent: Asia","lifeExp: 64.698<br />year: 2007<br />continent: Asia","lifeExp: 68.588<br />year: 2002<br />continent: Asia","lifeExp: 70.650<br />year: 2007<br />continent: Asia","lifeExp: 69.451<br />year: 2002<br />continent: Asia","lifeExp: 70.964<br />year: 2007<br />continent: Asia","lifeExp: 57.046<br />year: 2002<br />continent: Asia","lifeExp: 59.545<br />year: 2007<br />continent: Asia","lifeExp: 79.696<br />year: 2002<br />continent: Asia","lifeExp: 80.745<br />year: 2007<br />continent: Asia","lifeExp: 82.000<br />year: 2002<br />continent: Asia","lifeExp: 82.603<br />year: 2007<br />continent: Asia","lifeExp: 71.263<br />year: 2002<br />continent: Asia","lifeExp: 72.535<br />year: 2007<br />continent: Asia","lifeExp: 66.662<br />year: 2002<br />continent: Asia","lifeExp: 67.297<br />year: 2007<br />continent: Asia","lifeExp: 77.045<br />year: 2002<br />continent: Asia","lifeExp: 78.623<br />year: 2007<br />continent: Asia","lifeExp: 76.904<br />year: 2002<br />continent: Asia","lifeExp: 77.588<br />year: 2007<br />continent: Asia","lifeExp: 71.028<br />year: 2002<br />continent: Asia","lifeExp: 71.993<br />year: 2007<br />continent: Asia","lifeExp: 73.044<br />year: 2002<br />continent: Asia","lifeExp: 74.241<br />year: 2007<br />continent: Asia","lifeExp: 65.033<br />year: 2002<br />continent: Asia","lifeExp: 66.803<br />year: 2007<br />continent: Asia","lifeExp: 59.908<br />year: 2002<br />continent: Asia","lifeExp: 62.069<br />year: 2007<br />continent: Asia","lifeExp: 61.340<br />year: 2002<br />continent: Asia","lifeExp: 63.785<br />year: 2007<br />continent: Asia","lifeExp: 74.193<br />year: 2002<br />continent: Asia","lifeExp: 75.640<br />year: 2007<br />continent: Asia","lifeExp: 63.610<br />year: 2002<br />continent: Asia","lifeExp: 65.483<br />year: 2007<br />continent: Asia","lifeExp: 70.303<br />year: 2002<br />continent: Asia","lifeExp: 71.688<br />year: 2007<br />continent: Asia","lifeExp: 71.626<br />year: 2002<br />continent: Asia","lifeExp: 72.777<br />year: 2007<br />continent: Asia","lifeExp: 78.770<br />year: 2002<br />continent: Asia","lifeExp: 79.972<br />year: 2007<br />continent: Asia","lifeExp: 70.815<br />year: 2002<br />continent: Asia","lifeExp: 72.396<br />year: 2007<br />continent: Asia","lifeExp: 73.053<br />year: 2002<br />continent: Asia","lifeExp: 74.143<br />year: 2007<br />continent: Asia","lifeExp: 76.990<br />year: 2002<br />continent: Asia","lifeExp: 78.400<br />year: 2007<br />continent: Asia","lifeExp: 68.564<br />year: 2002<br />continent: Asia","lifeExp: 70.616<br />year: 2007<br />continent: Asia","lifeExp: 73.017<br />year: 2002<br />continent: Asia","lifeExp: 74.249<br />year: 2007<br />continent: Asia","lifeExp: 72.370<br />year: 2002<br />continent: Asia","lifeExp: 73.422<br />year: 2007<br />continent: Asia","lifeExp: 60.308<br />year: 2002<br />continent: Asia","lifeExp: 62.698<br />year: 2007<br />continent: Asia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,125,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"Asia","legendgroup":"Asia","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[75.651,76.423,78.98,79.829,78.32,79.441,74.09,74.852,72.14,73.005,74.876,75.748,75.51,76.486,77.18,78.332,78.37,79.313,79.59,80.657,78.67,79.406,78.256,79.483,72.59,73.338,80.5,81.757,77.783,78.885,80.24,80.546,73.981,74.543,78.53,79.762,79.05,80.196,74.67,75.563,77.29,78.098,71.322,72.476,73.213,74.002,73.8,74.663,76.66,77.926,79.78,80.941,80.04,80.884,80.62,81.701,70.845,71.777,78.471,79.425],"y":[0.949213364627212,1.96819168454967,1.03822396053001,1.9090679498855,1.08138148500584,2.03583154939115,0.997829749062657,1.9873283111956,1.02679826710373,2.07746247272007,0.923712840210646,2.04834417048842,1.08214549687691,1.93363463804126,1.00179599169642,2.06048678187653,1.0317256832961,1.91125275534578,1.05620742351748,1.92001803386956,1.04588511614129,2.09090566909872,1.02145096599124,1.99911200911738,0.970445058541372,1.95882581090555,1.08636604323983,1.90637357584201,1.09641348021105,2.03079804913141,0.983927865233272,2.05244918605313,1.05825657695532,1.94075355269015,1.09504096484743,1.98869701204821,0.935094423266128,1.9614641319029,0.98635051692836,2.0315098320134,1.04956897464581,2.00167297394946,0.946902077738196,2.08086987058632,0.976161310169846,2.05374133787118,1.09001631503925,1.93305146321654,1.01398072163574,1.98486046046019,1.00381583902054,2.07544515901245,0.953110052226111,2.03633883474395,1.04803456920199,2.08465058756992,0.9977568869479,2.05899979430251,1.00434566172771,2.09971838761121],"text":["lifeExp: 75.651<br />year: 2002<br />continent: Europe","lifeExp: 76.423<br />year: 2007<br />continent: Europe","lifeExp: 78.980<br />year: 2002<br />continent: Europe","lifeExp: 79.829<br />year: 2007<br />continent: Europe","lifeExp: 78.320<br />year: 2002<br />continent: Europe","lifeExp: 79.441<br />year: 2007<br />continent: Europe","lifeExp: 74.090<br />year: 2002<br />continent: Europe","lifeExp: 74.852<br />year: 2007<br />continent: Europe","lifeExp: 72.140<br />year: 2002<br />continent: Europe","lifeExp: 73.005<br />year: 2007<br />continent: Europe","lifeExp: 74.876<br />year: 2002<br />continent: Europe","lifeExp: 75.748<br />year: 2007<br />continent: Europe","lifeExp: 75.510<br />year: 2002<br />continent: Europe","lifeExp: 76.486<br />year: 2007<br />continent: Europe","lifeExp: 77.180<br />year: 2002<br />continent: Europe","lifeExp: 78.332<br />year: 2007<br />continent: Europe","lifeExp: 78.370<br />year: 2002<br />continent: Europe","lifeExp: 79.313<br />year: 2007<br />continent: Europe","lifeExp: 79.590<br />year: 2002<br />continent: Europe","lifeExp: 80.657<br />year: 2007<br />continent: Europe","lifeExp: 78.670<br />year: 2002<br />continent: Europe","lifeExp: 79.406<br />year: 2007<br />continent: Europe","lifeExp: 78.256<br />year: 2002<br />continent: Europe","lifeExp: 79.483<br />year: 2007<br />continent: Europe","lifeExp: 72.590<br />year: 2002<br />continent: Europe","lifeExp: 73.338<br />year: 2007<br />continent: Europe","lifeExp: 80.500<br />year: 2002<br />continent: Europe","lifeExp: 81.757<br />year: 2007<br />continent: Europe","lifeExp: 77.783<br />year: 2002<br />continent: Europe","lifeExp: 78.885<br />year: 2007<br />continent: Europe","lifeExp: 80.240<br />year: 2002<br />continent: Europe","lifeExp: 80.546<br />year: 2007<br />continent: Europe","lifeExp: 73.981<br />year: 2002<br />continent: Europe","lifeExp: 74.543<br />year: 2007<br />continent: Europe","lifeExp: 78.530<br />year: 2002<br />continent: Europe","lifeExp: 79.762<br />year: 2007<br />continent: Europe","lifeExp: 79.050<br />year: 2002<br />continent: Europe","lifeExp: 80.196<br />year: 2007<br />continent: Europe","lifeExp: 74.670<br />year: 2002<br />continent: Europe","lifeExp: 75.563<br />year: 2007<br />continent: Europe","lifeExp: 77.290<br />year: 2002<br />continent: Europe","lifeExp: 78.098<br />year: 2007<br />continent: Europe","lifeExp: 71.322<br />year: 2002<br />continent: Europe","lifeExp: 72.476<br />year: 2007<br />continent: Europe","lifeExp: 73.213<br />year: 2002<br />continent: Europe","lifeExp: 74.002<br />year: 2007<br />continent: Europe","lifeExp: 73.800<br />year: 2002<br />continent: Europe","lifeExp: 74.663<br />year: 2007<br />continent: Europe","lifeExp: 76.660<br />year: 2002<br />continent: Europe","lifeExp: 77.926<br />year: 2007<br />continent: Europe","lifeExp: 79.780<br />year: 2002<br />continent: Europe","lifeExp: 80.941<br />year: 2007<br />continent: Europe","lifeExp: 80.040<br />year: 2002<br />continent: Europe","lifeExp: 80.884<br />year: 2007<br />continent: Europe","lifeExp: 80.620<br />year: 2002<br />continent: Europe","lifeExp: 81.701<br />year: 2007<br />continent: Europe","lifeExp: 70.845<br />year: 2002<br />continent: Europe","lifeExp: 71.777<br />year: 2007<br />continent: Europe","lifeExp: 78.471<br />year: 2002<br />continent: Europe","lifeExp: 79.425<br />year: 2007<br />continent: Europe"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,176,246,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"Europe","legendgroup":"Europe","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[80.37,81.235,79.11,80.204],"y":[0.915851632319391,2.08021557969041,0.946454036189243,2.05636606030166],"text":["lifeExp: 80.370<br />year: 2002<br />continent: Oceania","lifeExp: 81.235<br />year: 2007<br />continent: Oceania","lifeExp: 79.110<br />year: 2002<br />continent: Oceania","lifeExp: 80.204<br />year: 2007<br />continent: Oceania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(231,107,243,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(231,107,243,1)"}},"hoveron":"points","name":"Oceania","legendgroup":"Oceania","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":26.2283105022831,"r":7.30593607305936,"b":40.1826484018265,"l":48.9497716894977},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[37.0225,84.7735],"tickmode":"array","ticktext":["40","50","60","70","80"],"tickvals":[40,50,60,70,80],"categoryorder":"array","categoryarray":["40","50","60","70","80"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"lifeExp","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,2.6],"tickmode":"array","ticktext":["2002","2007"],"tickvals":[1,2],"categoryorder":"array","categoryarray":["2002","2007"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"year","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(51,51,51,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"title":{"text":"continent","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"40a04ee12672":{"x":{},"y":{},"colour":{},"type":"scatter"}},"cur_data":"40a04ee12672","visdat":{"40a04ee12672":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

<p class="caption">(\#fig:plotly)Interactive graph using plotly</p>
</div>

::: {.info data-latex=""}
Hover over the data points above and click on the legend items.
:::

However, we can also add in some extra information to take advantage of the interactive plot. We'll add `fill = country` to the aesthetic mapping. We don't actually want to display this in the main plot, we just want `plotly` to have this variable available to it so we'll also add `guides(fill = "none")` to avoid creating a huge legend. The static version of this plot looks identical but if you pass it it to `ggplotly()` you'll now see that you can identify not only the continent but the country of each data point.


```r
country_plot <- ggplot(gapminder_long, aes(x = lifeExp, 
                                         y = year, 
                                         color = continent,
                                         fill = country)) +
  geom_jitter(height = .1, width = 0) +
  guides(fill = "none")

ggplotly(country_plot)
```

```{=html}
<div id="htmlwidget-a4d3971db97795a8105c" style="width:100%;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-a4d3971db97795a8105c">{"x":{"data":[{"x":[70.994,72.301],"y":[0.926402595592663,1.98441629903391],"text":["lifeExp: 70.994<br />year: 2002<br />continent: Africa<br />country: Algeria","lifeExp: 72.301<br />year: 2007<br />continent: Africa<br />country: Algeria"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(245,122,95,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Algeria)","legendgroup":"(Africa,Algeria)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[41.003,42.731],"y":[0.980852248333395,2.03816330097616],"text":["lifeExp: 41.003<br />year: 2002<br />continent: Africa<br />country: Angola","lifeExp: 42.731<br />year: 2007<br />continent: Africa<br />country: Angola"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(243,124,88,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Angola)","legendgroup":"(Africa,Angola)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[54.406,56.728],"y":[0.926377739664167,2.05142862461507],"text":["lifeExp: 54.406<br />year: 2002<br />continent: Africa<br />country: Benin","lifeExp: 56.728<br />year: 2007<br />continent: Africa<br />country: Benin"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(227,136,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Benin)","legendgroup":"(Africa,Benin)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[46.634,50.728],"y":[1.09224524777383,1.93269807677716],"text":["lifeExp: 46.634<br />year: 2002<br />continent: Africa<br />country: Botswana","lifeExp: 50.728<br />year: 2007<br />continent: Africa<br />country: Botswana"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(219,142,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Botswana)","legendgroup":"(Africa,Botswana)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[50.65,52.295],"y":[0.990277364430949,2.065032408759],"text":["lifeExp: 50.650<br />year: 2002<br />continent: Africa<br />country: Burkina Faso","lifeExp: 52.295<br />year: 2007<br />continent: Africa<br />country: Burkina Faso"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(210,147,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Burkina Faso)","legendgroup":"(Africa,Burkina Faso)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[47.36,49.58],"y":[0.981372207403183,1.9990740084555],"text":["lifeExp: 47.360<br />year: 2002<br />continent: Africa<br />country: Burundi","lifeExp: 49.580<br />year: 2007<br />continent: Africa<br />country: Burundi"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(207,148,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Burundi)","legendgroup":"(Africa,Burundi)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[49.856,50.43],"y":[1.031192249991,1.90078968689777],"text":["lifeExp: 49.856<br />year: 2002<br />continent: Africa<br />country: Cameroon","lifeExp: 50.430<br />year: 2007<br />continent: Africa<br />country: Cameroon"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(200,152,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Cameroon)","legendgroup":"(Africa,Cameroon)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[43.308,44.741],"y":[1.00345166763291,2.02264105975628],"text":["lifeExp: 43.308<br />year: 2002<br />continent: Africa<br />country: Central African Republic","lifeExp: 44.741<br />year: 2007<br />continent: Africa<br />country: Central African Republic"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(193,155,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Central African Republic)","legendgroup":"(Africa,Central African Republic)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[50.525,50.651],"y":[1.03469473384321,1.98434714698233],"text":["lifeExp: 50.525<br />year: 2002<br />continent: Africa<br />country: Chad","lifeExp: 50.651<br />year: 2007<br />continent: Africa<br />country: Chad"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(190,156,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Chad)","legendgroup":"(Africa,Chad)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[62.974,65.152],"y":[0.946955904923379,2.07855138811283],"text":["lifeExp: 62.974<br />year: 2002<br />continent: Africa<br />country: Comoros","lifeExp: 65.152<br />year: 2007<br />continent: Africa<br />country: Comoros"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(174,162,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Comoros)","legendgroup":"(Africa,Comoros)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[44.966,46.462],"y":[1.01305180042982,1.97969175255857],"text":["lifeExp: 44.966<br />year: 2002<br />continent: Africa<br />country: Congo, Dem. Rep.","lifeExp: 46.462<br />year: 2007<br />continent: Africa<br />country: Congo, Dem. Rep."],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(169,164,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Congo, Dem. Rep.)","legendgroup":"(Africa,Congo, Dem. Rep.)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[52.97,55.322],"y":[1.05813251887448,1.96797753777355],"text":["lifeExp: 52.970<br />year: 2002<br />continent: Africa<br />country: Congo, Rep.","lifeExp: 55.322<br />year: 2007<br />continent: Africa<br />country: Congo, Rep."],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(165,165,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Congo, Rep.)","legendgroup":"(Africa,Congo, Rep.)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[46.832,48.328],"y":[0.969517316482961,2.01961702881381],"text":["lifeExp: 46.832<br />year: 2002<br />continent: Africa<br />country: Cote d'Ivoire","lifeExp: 48.328<br />year: 2007<br />continent: Africa<br />country: Cote d'Ivoire"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(155,168,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Cote d'Ivoire)","legendgroup":"(Africa,Cote d'Ivoire)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[53.373,54.791],"y":[1.07910574730486,2.00793816451915],"text":["lifeExp: 53.373<br />year: 2002<br />continent: Africa<br />country: Djibouti","lifeExp: 54.791<br />year: 2007<br />continent: Africa<br />country: Djibouti"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(127,174,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Djibouti)","legendgroup":"(Africa,Djibouti)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[69.806,71.338],"y":[1.05454836622812,1.95180515339598],"text":["lifeExp: 69.806<br />year: 2002<br />continent: Africa<br />country: Egypt","lifeExp: 71.338<br />year: 2007<br />continent: Africa<br />country: Egypt"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(106,177,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Egypt)","legendgroup":"(Africa,Egypt)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[49.348,51.579],"y":[1.06996148251928,2.0291150346864],"text":["lifeExp: 49.348<br />year: 2002<br />continent: Africa<br />country: Equatorial Guinea","lifeExp: 51.579<br />year: 2007<br />continent: Africa<br />country: Equatorial Guinea"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(88,179,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Equatorial Guinea)","legendgroup":"(Africa,Equatorial Guinea)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[55.24,58.04],"y":[0.929066136060283,1.97441153707914],"text":["lifeExp: 55.240<br />year: 2002<br />continent: Africa<br />country: Eritrea","lifeExp: 58.040<br />year: 2007<br />continent: Africa<br />country: Eritrea"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(78,180,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Eritrea)","legendgroup":"(Africa,Eritrea)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[50.725,52.947],"y":[0.977238592412323,2.09625615836121],"text":["lifeExp: 50.725<br />year: 2002<br />continent: Africa<br />country: Ethiopia","lifeExp: 52.947<br />year: 2007<br />continent: Africa<br />country: Ethiopia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(66,181,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Ethiopia)","legendgroup":"(Africa,Ethiopia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[56.761,56.735],"y":[1.05913746068254,2.0339840545319],"text":["lifeExp: 56.761<br />year: 2002<br />continent: Africa<br />country: Gabon","lifeExp: 56.735<br />year: 2007<br />continent: Africa<br />country: Gabon"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,184,23,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Gabon)","legendgroup":"(Africa,Gabon)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[58.041,59.448],"y":[1.01961779012345,2.05411622165702],"text":["lifeExp: 58.041<br />year: 2002<br />continent: Africa<br />country: Gambia","lifeExp: 59.448<br />year: 2007<br />continent: Africa<br />country: Gambia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,185,40,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Gambia)","legendgroup":"(Africa,Gambia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[58.453,60.022],"y":[1.08555898722261,2.01709730220027],"text":["lifeExp: 58.453<br />year: 2002<br />continent: Africa<br />country: Ghana","lifeExp: 60.022<br />year: 2007<br />continent: Africa<br />country: Ghana"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,186,63,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Ghana)","legendgroup":"(Africa,Ghana)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[53.676,56.007],"y":[0.928095575561747,1.94237627848051],"text":["lifeExp: 53.676<br />year: 2002<br />continent: Africa<br />country: Guinea","lifeExp: 56.007<br />year: 2007<br />continent: Africa<br />country: Guinea"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,188,88,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Guinea)","legendgroup":"(Africa,Guinea)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[45.504,46.388],"y":[0.933211109694093,1.94766002567485],"text":["lifeExp: 45.504<br />year: 2002<br />continent: Africa<br />country: Guinea-Bissau","lifeExp: 46.388<br />year: 2007<br />continent: Africa<br />country: Guinea-Bissau"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,189,95,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Guinea-Bissau)","legendgroup":"(Africa,Guinea-Bissau)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[50.992,54.11],"y":[1.02837247489952,2.09956289855763],"text":["lifeExp: 50.992<br />year: 2002<br />continent: Africa<br />country: Kenya","lifeExp: 54.110<br />year: 2007<br />continent: Africa<br />country: Kenya"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,183,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Kenya)","legendgroup":"(Africa,Kenya)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[44.593,42.592],"y":[0.94052115236409,1.91302491342649],"text":["lifeExp: 44.593<br />year: 2002<br />continent: Africa<br />country: Lesotho","lifeExp: 42.592<br />year: 2007<br />continent: Africa<br />country: Lesotho"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,190,205,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Lesotho)","legendgroup":"(Africa,Lesotho)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[43.753,45.678],"y":[1.06660330663435,1.97872485816479],"text":["lifeExp: 43.753<br />year: 2002<br />continent: Africa<br />country: Liberia","lifeExp: 45.678<br />year: 2007<br />continent: Africa<br />country: Liberia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,189,209,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Liberia)","legendgroup":"(Africa,Liberia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.737,73.952],"y":[0.932147459732369,2.00814455715008],"text":["lifeExp: 72.737<br />year: 2002<br />continent: Africa<br />country: Libya","lifeExp: 73.952<br />year: 2007<br />continent: Africa<br />country: Libya"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,189,213,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Libya)","legendgroup":"(Africa,Libya)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[57.286,59.443],"y":[0.938736349483952,1.93002806589939],"text":["lifeExp: 57.286<br />year: 2002<br />continent: Africa<br />country: Madagascar","lifeExp: 59.443<br />year: 2007<br />continent: Africa<br />country: Madagascar"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,188,217,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Madagascar)","legendgroup":"(Africa,Madagascar)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[45.009,48.303],"y":[0.989503311272711,2.03464908460155],"text":["lifeExp: 45.009<br />year: 2002<br />continent: Africa<br />country: Malawi","lifeExp: 48.303<br />year: 2007<br />continent: Africa<br />country: Malawi"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,187,220,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Malawi)","legendgroup":"(Africa,Malawi)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[51.818,54.467],"y":[1.06929649128579,2.02348529053852],"text":["lifeExp: 51.818<br />year: 2002<br />continent: Africa<br />country: Mali","lifeExp: 54.467<br />year: 2007<br />continent: Africa<br />country: Mali"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,185,228,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Mali)","legendgroup":"(Africa,Mali)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[62.247,64.164],"y":[0.94086266993545,2.03864432023838],"text":["lifeExp: 62.247<br />year: 2002<br />continent: Africa<br />country: Mauritania","lifeExp: 64.164<br />year: 2007<br />continent: Africa<br />country: Mauritania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,184,231,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Mauritania)","legendgroup":"(Africa,Mauritania)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.954,72.801],"y":[0.983565939357504,2.08658481216989],"text":["lifeExp: 71.954<br />year: 2002<br />continent: Africa<br />country: Mauritius","lifeExp: 72.801<br />year: 2007<br />continent: Africa<br />country: Mauritius"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,182,234,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Mauritius)","legendgroup":"(Africa,Mauritius)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[69.615,71.164],"y":[1.00378175526857,1.99116377187893],"text":["lifeExp: 69.615<br />year: 2002<br />continent: Africa<br />country: Morocco","lifeExp: 71.164<br />year: 2007<br />continent: Africa<br />country: Morocco"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,176,246,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Morocco)","legendgroup":"(Africa,Morocco)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[44.026,42.082],"y":[0.975110468314961,1.90566555936821],"text":["lifeExp: 44.026<br />year: 2002<br />continent: Africa<br />country: Mozambique","lifeExp: 42.082<br />year: 2007<br />continent: Africa<br />country: Mozambique"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,175,248,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Mozambique)","legendgroup":"(Africa,Mozambique)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[51.479,52.906],"y":[0.908713027322665,2.04207750777714],"text":["lifeExp: 51.479<br />year: 2002<br />continent: Africa<br />country: Namibia","lifeExp: 52.906<br />year: 2007<br />continent: Africa<br />country: Namibia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,171,253,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Namibia)","legendgroup":"(Africa,Namibia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[54.496,56.867],"y":[0.95240559973754,2.06353456424549],"text":["lifeExp: 54.496<br />year: 2002<br />continent: Africa<br />country: Niger","lifeExp: 56.867<br />year: 2007<br />continent: Africa<br />country: Niger"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(69,160,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Niger)","legendgroup":"(Africa,Niger)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[46.608,46.859],"y":[1.09890355658717,2.00943694454618],"text":["lifeExp: 46.608<br />year: 2002<br />continent: Africa<br />country: Nigeria","lifeExp: 46.859<br />year: 2007<br />continent: Africa<br />country: Nigeria"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(87,158,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Nigeria)","legendgroup":"(Africa,Nigeria)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[75.744,76.442],"y":[1.01683701067232,2.00924957147799],"text":["lifeExp: 75.744<br />year: 2002<br />continent: Africa<br />country: Reunion","lifeExp: 76.442<br />year: 2007<br />continent: Africa<br />country: Reunion"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(190,128,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Reunion)","legendgroup":"(Africa,Reunion)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[43.413,46.242],"y":[0.923512565344572,1.93547840062529],"text":["lifeExp: 43.413<br />year: 2002<br />continent: Africa<br />country: Rwanda","lifeExp: 46.242<br />year: 2007<br />continent: Africa<br />country: Rwanda"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(202,123,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Rwanda)","legendgroup":"(Africa,Rwanda)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[64.337,65.528],"y":[0.917694759694859,1.96705065425485],"text":["lifeExp: 64.337<br />year: 2002<br />continent: Africa<br />country: Sao Tome and Principe","lifeExp: 65.528<br />year: 2007<br />continent: Africa<br />country: Sao Tome and Principe"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(207,120,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Sao Tome and Principe)","legendgroup":"(Africa,Sao Tome and Principe)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[61.6,63.062],"y":[1.0050418368075,2.06783958496526],"text":["lifeExp: 61.600<br />year: 2002<br />continent: Africa<br />country: Senegal","lifeExp: 63.062<br />year: 2007<br />continent: Africa<br />country: Senegal"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(217,115,252,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Senegal)","legendgroup":"(Africa,Senegal)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[41.012,42.568],"y":[1.01189670260064,1.98005899069831],"text":["lifeExp: 41.012<br />year: 2002<br />continent: Africa<br />country: Sierra Leone","lifeExp: 42.568<br />year: 2007<br />continent: Africa<br />country: Sierra Leone"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(225,111,247,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Sierra Leone)","legendgroup":"(Africa,Sierra Leone)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[45.936,48.159],"y":[0.926169027620926,1.99044247320853],"text":["lifeExp: 45.936<br />year: 2002<br />continent: Africa<br />country: Somalia","lifeExp: 48.159<br />year: 2007<br />continent: Africa<br />country: Somalia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(239,103,235,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Somalia)","legendgroup":"(Africa,Somalia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[53.365,49.339],"y":[1.00685216439888,1.93476346437819],"text":["lifeExp: 53.365<br />year: 2002<br />continent: Africa<br />country: South Africa","lifeExp: 49.339<br />year: 2007<br />continent: Africa<br />country: South Africa"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(242,102,232,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,South Africa)","legendgroup":"(Africa,South Africa)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[56.369,58.556],"y":[1.04485799241811,2.07336182422005],"text":["lifeExp: 56.369<br />year: 2002<br />continent: Africa<br />country: Sudan","lifeExp: 58.556<br />year: 2007<br />continent: Africa<br />country: Sudan"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(248,98,221,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Sudan)","legendgroup":"(Africa,Sudan)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[43.869,39.613],"y":[1.0720594198443,1.95762613625266],"text":["lifeExp: 43.869<br />year: 2002<br />continent: Africa<br />country: Swaziland","lifeExp: 39.613<br />year: 2007<br />continent: Africa<br />country: Swaziland"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(250,98,217,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Swaziland)","legendgroup":"(Africa,Swaziland)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[49.651,52.517],"y":[1.08173781400546,2.09804554511793],"text":["lifeExp: 49.651<br />year: 2002<br />continent: Africa<br />country: Tanzania","lifeExp: 52.517<br />year: 2007<br />continent: Africa<br />country: Tanzania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,97,196,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Tanzania)","legendgroup":"(Africa,Tanzania)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[57.561,58.42],"y":[1.09575534085743,1.99599109487608],"text":["lifeExp: 57.561<br />year: 2002<br />continent: Africa<br />country: Togo","lifeExp: 58.420<br />year: 2007<br />continent: Africa<br />country: Togo"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,98,187,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Togo)","legendgroup":"(Africa,Togo)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.042,73.923],"y":[0.926579909818247,1.95932468469255],"text":["lifeExp: 73.042<br />year: 2002<br />continent: Africa<br />country: Tunisia","lifeExp: 73.923<br />year: 2007<br />continent: Africa<br />country: Tunisia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,100,177,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Tunisia)","legendgroup":"(Africa,Tunisia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[47.813,51.542],"y":[1.07145063746721,1.91891080648638],"text":["lifeExp: 47.813<br />year: 2002<br />continent: Africa<br />country: Uganda","lifeExp: 51.542<br />year: 2007<br />continent: Africa<br />country: Uganda"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,102,167,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Uganda)","legendgroup":"(Africa,Uganda)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[39.193,42.384],"y":[1.00191334360279,2.09531498076394],"text":["lifeExp: 39.193<br />year: 2002<br />continent: Africa<br />country: Zambia","lifeExp: 42.384<br />year: 2007<br />continent: Africa<br />country: Zambia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,108,145,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Zambia)","legendgroup":"(Africa,Zambia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[39.989,43.487],"y":[0.985996183566749,2.04201517966576],"text":["lifeExp: 39.989<br />year: 2002<br />continent: Africa<br />country: Zimbabwe","lifeExp: 43.487<br />year: 2007<br />continent: Africa<br />country: Zimbabwe"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,108,145,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"(Africa,Zimbabwe)","legendgroup":"(Africa,Zimbabwe)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.34,75.32],"y":[0.970194800244644,1.99685596968047],"text":["lifeExp: 74.340<br />year: 2002<br />continent: Americas<br />country: Argentina","lifeExp: 75.320<br />year: 2007<br />continent: Americas<br />country: Argentina"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(241,126,80,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Argentina)","legendgroup":"(Americas,Argentina)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[63.883,65.554],"y":[0.917372055677697,1.95955997132696],"text":["lifeExp: 63.883<br />year: 2002<br />continent: Americas<br />country: Bolivia","lifeExp: 65.554<br />year: 2007<br />continent: Americas<br />country: Bolivia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(225,138,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Bolivia)","legendgroup":"(Americas,Bolivia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.006,72.39],"y":[1.04477433101274,1.95887705045752],"text":["lifeExp: 71.006<br />year: 2002<br />continent: Americas<br />country: Brazil","lifeExp: 72.390<br />year: 2007<br />continent: Americas<br />country: Brazil"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(216,143,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Brazil)","legendgroup":"(Americas,Brazil)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[79.77,80.653],"y":[0.976504437811673,2.0173299820628],"text":["lifeExp: 79.770<br />year: 2002<br />continent: Americas<br />country: Canada","lifeExp: 80.653<br />year: 2007<br />continent: Americas<br />country: Canada"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(197,153,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Canada)","legendgroup":"(Americas,Canada)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.86,78.553],"y":[0.94108247673139,2.04188261316158],"text":["lifeExp: 77.860<br />year: 2002<br />continent: Americas<br />country: Chile","lifeExp: 78.553<br />year: 2007<br />continent: Americas<br />country: Chile"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(186,158,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Chile)","legendgroup":"(Americas,Chile)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.682,72.889],"y":[1.02294316953048,1.99519332465716],"text":["lifeExp: 71.682<br />year: 2002<br />continent: Americas<br />country: Colombia","lifeExp: 72.889<br />year: 2007<br />continent: Americas<br />country: Colombia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(178,161,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Colombia)","legendgroup":"(Americas,Colombia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.123,78.782],"y":[1.00497636948712,1.94747961196117],"text":["lifeExp: 78.123<br />year: 2002<br />continent: Americas<br />country: Costa Rica","lifeExp: 78.782<br />year: 2007<br />continent: Americas<br />country: Costa Rica"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(160,166,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Costa Rica)","legendgroup":"(Americas,Costa Rica)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.158,78.273],"y":[0.948509838664904,2.04625075194053],"text":["lifeExp: 77.158<br />year: 2002<br />continent: Americas<br />country: Cuba","lifeExp: 78.273<br />year: 2007<br />continent: Americas<br />country: Cuba"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(145,170,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Cuba)","legendgroup":"(Americas,Cuba)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.847,72.235],"y":[1.0125142596662,1.96805320051499],"text":["lifeExp: 70.847<br />year: 2002<br />continent: Americas<br />country: Dominican Republic","lifeExp: 72.235<br />year: 2007<br />continent: Americas<br />country: Dominican Republic"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(120,175,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Dominican Republic)","legendgroup":"(Americas,Dominican Republic)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.173,74.994],"y":[1.0717781055253,1.91669497080147],"text":["lifeExp: 74.173<br />year: 2002<br />continent: Americas<br />country: Ecuador","lifeExp: 74.994<br />year: 2007<br />continent: Americas<br />country: Ecuador"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(113,176,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Ecuador)","legendgroup":"(Americas,Ecuador)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.734,71.878],"y":[1.0862432344351,2.06551564638503],"text":["lifeExp: 70.734<br />year: 2002<br />continent: Americas<br />country: El Salvador","lifeExp: 71.878<br />year: 2007<br />continent: Americas<br />country: El Salvador"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(98,178,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,El Salvador)","legendgroup":"(Americas,El Salvador)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[68.978,70.259],"y":[0.973953146208078,1.94509661928751],"text":["lifeExp: 68.978<br />year: 2002<br />continent: Americas<br />country: Guatemala","lifeExp: 70.259<br />year: 2007<br />continent: Americas<br />country: Guatemala"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,188,80,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Guatemala)","legendgroup":"(Americas,Guatemala)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[58.137,60.916],"y":[0.944300387566909,2.03053041915409],"text":["lifeExp: 58.137<br />year: 2002<br />continent: Americas<br />country: Haiti","lifeExp: 60.916<br />year: 2007<br />continent: Americas<br />country: Haiti"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,189,102,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Haiti)","legendgroup":"(Americas,Haiti)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[68.565,70.198],"y":[0.987214792845771,2.04313808842562],"text":["lifeExp: 68.565<br />year: 2002<br />continent: Americas<br />country: Honduras","lifeExp: 70.198<br />year: 2007<br />continent: Americas<br />country: Honduras"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,190,108,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Honduras)","legendgroup":"(Americas,Honduras)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.047,72.567],"y":[1.08598993630148,1.95541739799082],"text":["lifeExp: 72.047<br />year: 2002<br />continent: Americas<br />country: Jamaica","lifeExp: 72.567<br />year: 2007<br />continent: Americas<br />country: Jamaica"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,193,169,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Jamaica)","legendgroup":"(Americas,Jamaica)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.902,76.195],"y":[1.06119833695702,2.08869357295334],"text":["lifeExp: 74.902<br />year: 2002<br />continent: Americas<br />country: Mexico","lifeExp: 76.195<br />year: 2007<br />continent: Americas<br />country: Mexico"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,181,237,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Mexico)","legendgroup":"(Americas,Mexico)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.836,72.899],"y":[0.923772907350212,1.9504828824196],"text":["lifeExp: 70.836<br />year: 2002<br />continent: Americas<br />country: Nicaragua","lifeExp: 72.899<br />year: 2007<br />continent: Americas<br />country: Nicaragua"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(44,162,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Nicaragua)","legendgroup":"(Americas,Nicaragua)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.712,75.537],"y":[0.945768746640533,2.01898939856328],"text":["lifeExp: 74.712<br />year: 2002<br />continent: Americas<br />country: Panama","lifeExp: 75.537<br />year: 2007<br />continent: Americas<br />country: Panama"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(136,147,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Panama)","legendgroup":"(Americas,Panama)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.755,71.752],"y":[0.982093267282471,2.07144181882031],"text":["lifeExp: 70.755<br />year: 2002<br />continent: Americas<br />country: Paraguay","lifeExp: 71.752<br />year: 2007<br />continent: Americas<br />country: Paraguay"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(145,145,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Paraguay)","legendgroup":"(Americas,Paraguay)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[69.906,71.421],"y":[0.992922730045393,2.05166663872078],"text":["lifeExp: 69.906<br />year: 2002<br />continent: Americas<br />country: Peru","lifeExp: 71.421<br />year: 2007<br />continent: Americas<br />country: Peru"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(154,142,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Peru)","legendgroup":"(Americas,Peru)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.778,78.746],"y":[1.0250881482847,1.99773295391351],"text":["lifeExp: 77.778<br />year: 2002<br />continent: Americas<br />country: Puerto Rico","lifeExp: 78.746<br />year: 2007<br />continent: Americas<br />country: Puerto Rico"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(184,131,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Puerto Rico)","legendgroup":"(Americas,Puerto Rico)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[68.976,69.819],"y":[1.08018548567779,2.09286123923957],"text":["lifeExp: 68.976<br />year: 2002<br />continent: Americas<br />country: Trinidad and Tobago","lifeExp: 69.819<br />year: 2007<br />continent: Americas<br />country: Trinidad and Tobago"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,99,182,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Trinidad and Tobago)","legendgroup":"(Americas,Trinidad and Tobago)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.31,78.242],"y":[1.01743306568824,2.05066778371111],"text":["lifeExp: 77.310<br />year: 2002<br />continent: Americas<br />country: United States","lifeExp: 78.242<br />year: 2007<br />continent: Americas<br />country: United States"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,105,156,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,United States)","legendgroup":"(Americas,United States)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[75.307,76.384],"y":[1.06771536981687,1.99535851338878],"text":["lifeExp: 75.307<br />year: 2002<br />continent: Americas<br />country: Uruguay","lifeExp: 76.384<br />year: 2007<br />continent: Americas<br />country: Uruguay"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,106,151,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Uruguay)","legendgroup":"(Americas,Uruguay)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.766,73.747],"y":[0.906505781691521,1.98336783335544],"text":["lifeExp: 72.766<br />year: 2002<br />continent: Americas<br />country: Venezuela","lifeExp: 73.747<br />year: 2007<br />continent: Americas<br />country: Venezuela"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,108,145,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(163,165,0,1)"}},"hoveron":"points","name":"(Americas,Venezuela)","legendgroup":"(Americas,Venezuela)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[42.129,43.828],"y":[1.01498438650742,1.99997173263691],"text":["lifeExp: 42.129<br />year: 2002<br />continent: Asia<br />country: Afghanistan","lifeExp: 43.828<br />year: 2007<br />continent: Asia<br />country: Afghanistan"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Afghanistan)","legendgroup":"(Asia,Afghanistan)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.795,75.635],"y":[1.02426346647553,1.96929959324189],"text":["lifeExp: 74.795<br />year: 2002<br />continent: Asia<br />country: Bahrain","lifeExp: 75.635<br />year: 2007<br />continent: Asia<br />country: Bahrain"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(234,131,50,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Bahrain)","legendgroup":"(Asia,Bahrain)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[62.013,64.062],"y":[0.934877921780571,1.95079980008304],"text":["lifeExp: 62.013<br />year: 2002<br />continent: Asia<br />country: Bangladesh","lifeExp: 64.062<br />year: 2007<br />continent: Asia<br />country: Bangladesh"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(232,133,37,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Bangladesh)","legendgroup":"(Asia,Bangladesh)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[56.752,59.723],"y":[0.925028312625363,2.09704958912916],"text":["lifeExp: 56.752<br />year: 2002<br />continent: Asia<br />country: Cambodia","lifeExp: 59.723<br />year: 2007<br />continent: Asia<br />country: Cambodia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(204,150,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Cambodia)","legendgroup":"(Asia,Cambodia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.028,72.961],"y":[1.09678428652696,2.07620790326037],"text":["lifeExp: 72.028<br />year: 2002<br />continent: Asia<br />country: China","lifeExp: 72.961<br />year: 2007<br />continent: Asia<br />country: China"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(182,159,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,China)","legendgroup":"(Asia,China)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[81.495,82.208],"y":[0.911677972460166,1.92965329252183],"text":["lifeExp: 81.495<br />year: 2002<br />continent: Asia<br />country: Hong Kong, China","lifeExp: 82.208<br />year: 2007<br />continent: Asia<br />country: Hong Kong, China"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,190,115,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Hong Kong, China)","legendgroup":"(Asia,Hong Kong, China)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[62.879,64.698],"y":[0.994215292856097,1.90586767666973],"text":["lifeExp: 62.879<br />year: 2002<br />continent: Asia<br />country: India","lifeExp: 64.698<br />year: 2007<br />continent: Asia<br />country: India"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,132,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,India)","legendgroup":"(Asia,India)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[68.588,70.65],"y":[1.02020584032871,1.95501039745286],"text":["lifeExp: 68.588<br />year: 2002<br />continent: Asia<br />country: Indonesia","lifeExp: 70.650<br />year: 2007<br />continent: Asia<br />country: Indonesia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,138,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Indonesia)","legendgroup":"(Asia,Indonesia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[69.451,70.964],"y":[1.04037446845323,1.90647654826753],"text":["lifeExp: 69.451<br />year: 2002<br />continent: Asia<br />country: Iran","lifeExp: 70.964<br />year: 2007<br />continent: Asia<br />country: Iran"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,143,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Iran)","legendgroup":"(Asia,Iran)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[57.046,59.545],"y":[0.98319431305863,1.97653673612513],"text":["lifeExp: 57.046<br />year: 2002<br />continent: Asia<br />country: Iraq","lifeExp: 59.545<br />year: 2007<br />continent: Asia<br />country: Iraq"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,149,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Iraq)","legendgroup":"(Asia,Iraq)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[79.696,80.745],"y":[1.06875051259995,2.01340684248134],"text":["lifeExp: 79.696<br />year: 2002<br />continent: Asia<br />country: Israel","lifeExp: 80.745<br />year: 2007<br />continent: Asia<br />country: Israel"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,193,159,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Israel)","legendgroup":"(Asia,Israel)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[82,82.603],"y":[0.903871871856973,1.96610029567964],"text":["lifeExp: 82.000<br />year: 2002<br />continent: Asia<br />country: Japan","lifeExp: 82.603<br />year: 2007<br />continent: Asia<br />country: Japan"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,193,174,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Japan)","legendgroup":"(Asia,Japan)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.263,72.535],"y":[0.943399564502761,1.90506205195561],"text":["lifeExp: 71.263<br />year: 2002<br />continent: Asia<br />country: Jordan","lifeExp: 72.535<br />year: 2007<br />continent: Asia<br />country: Jordan"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,178,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Jordan)","legendgroup":"(Asia,Jordan)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[66.662,67.297],"y":[1.0706059135031,1.96721437266096],"text":["lifeExp: 66.662<br />year: 2002<br />continent: Asia<br />country: Korea, Dem. Rep.","lifeExp: 67.297<br />year: 2007<br />continent: Asia<br />country: Korea, Dem. Rep."],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,192,188,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Korea, Dem. Rep.)","legendgroup":"(Asia,Korea, Dem. Rep.)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.045,78.623],"y":[0.994474506564438,2.01038748044521],"text":["lifeExp: 77.045<br />year: 2002<br />continent: Asia<br />country: Korea, Rep.","lifeExp: 78.623<br />year: 2007<br />continent: Asia<br />country: Korea, Rep."],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,192,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Korea, Rep.)","legendgroup":"(Asia,Korea, Rep.)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[76.904,77.588],"y":[0.998919131513685,1.93416237323545],"text":["lifeExp: 76.904<br />year: 2002<br />continent: Asia<br />country: Kuwait","lifeExp: 77.588<br />year: 2007<br />continent: Asia<br />country: Kuwait"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,196,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Kuwait)","legendgroup":"(Asia,Kuwait)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.028,71.993],"y":[0.986337502161041,1.9445678381715],"text":["lifeExp: 71.028<br />year: 2002<br />continent: Asia<br />country: Lebanon","lifeExp: 71.993<br />year: 2007<br />continent: Asia<br />country: Lebanon"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,201,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Lebanon)","legendgroup":"(Asia,Lebanon)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.044,74.241],"y":[0.968718695407733,1.92911521201022],"text":["lifeExp: 73.044<br />year: 2002<br />continent: Asia<br />country: Malaysia","lifeExp: 74.241<br />year: 2007<br />continent: Asia<br />country: Malaysia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,186,224,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Malaysia)","legendgroup":"(Asia,Malaysia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[65.033,66.803],"y":[1.03719062465243,2.07120339577086],"text":["lifeExp: 65.033<br />year: 2002<br />continent: Asia<br />country: Mongolia","lifeExp: 66.803<br />year: 2007<br />continent: Asia<br />country: Mongolia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,179,240,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Mongolia)","legendgroup":"(Asia,Mongolia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[59.908,62.069],"y":[0.997435011761263,1.94960972280242],"text":["lifeExp: 59.908<br />year: 2002<br />continent: Asia<br />country: Myanmar","lifeExp: 62.069<br />year: 2007<br />continent: Asia<br />country: Myanmar"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,173,251,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Myanmar)","legendgroup":"(Asia,Myanmar)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[61.34,63.785],"y":[0.989242076128721,2.01796568133868],"text":["lifeExp: 61.340<br />year: 2002<br />continent: Asia<br />country: Nepal","lifeExp: 63.785<br />year: 2007<br />continent: Asia<br />country: Nepal"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,169,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Nepal)","legendgroup":"(Asia,Nepal)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.193,75.64],"y":[0.998776734014973,2.04401513459161],"text":["lifeExp: 74.193<br />year: 2002<br />continent: Asia<br />country: Oman","lifeExp: 75.640<br />year: 2007<br />continent: Asia<br />country: Oman"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(114,153,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Oman)","legendgroup":"(Asia,Oman)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[63.61,65.483],"y":[0.915009867819026,1.93773202896118],"text":["lifeExp: 63.610<br />year: 2002<br />continent: Asia<br />country: Pakistan","lifeExp: 65.483<br />year: 2007<br />continent: Asia<br />country: Pakistan"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(125,150,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Pakistan)","legendgroup":"(Asia,Pakistan)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.303,71.688],"y":[0.995132113806903,2.01737719778903],"text":["lifeExp: 70.303<br />year: 2002<br />continent: Asia<br />country: Philippines","lifeExp: 71.688<br />year: 2007<br />continent: Asia<br />country: Philippines"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(162,139,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Philippines)","legendgroup":"(Asia,Philippines)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.626,72.777],"y":[1.00363153163344,1.97415727791376],"text":["lifeExp: 71.626<br />year: 2002<br />continent: Asia<br />country: Saudi Arabia","lifeExp: 72.777<br />year: 2007<br />continent: Asia<br />country: Saudi Arabia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(212,118,254,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Saudi Arabia)","legendgroup":"(Asia,Saudi Arabia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.77,79.972],"y":[1.01552790231071,2.02517293323763],"text":["lifeExp: 78.770<br />year: 2002<br />continent: Asia<br />country: Singapore","lifeExp: 79.972<br />year: 2007<br />continent: Asia<br />country: Singapore"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(229,108,245,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Singapore)","legendgroup":"(Asia,Singapore)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.815,72.396],"y":[1.03347866674885,1.93867755262181],"text":["lifeExp: 70.815<br />year: 2002<br />continent: Asia<br />country: Sri Lanka","lifeExp: 72.396<br />year: 2007<br />continent: Asia<br />country: Sri Lanka"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(246,99,225,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Sri Lanka)","legendgroup":"(Asia,Sri Lanka)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.053,74.143],"y":[0.95987755828537,1.90253920415416],"text":["lifeExp: 73.053<br />year: 2002<br />continent: Asia<br />country: Syria","lifeExp: 74.143<br />year: 2007<br />continent: Asia<br />country: Syria"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(254,97,205,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Syria)","legendgroup":"(Asia,Syria)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[76.99,78.4],"y":[0.907392618898302,1.90635761283338],"text":["lifeExp: 76.990<br />year: 2002<br />continent: Asia<br />country: Taiwan","lifeExp: 78.400<br />year: 2007<br />continent: Asia<br />country: Taiwan"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,97,201,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Taiwan)","legendgroup":"(Asia,Taiwan)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[68.564,70.616],"y":[0.90398964965716,2.0696188212838],"text":["lifeExp: 68.564<br />year: 2002<br />continent: Asia<br />country: Thailand","lifeExp: 70.616<br />year: 2007<br />continent: Asia<br />country: Thailand"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,98,192,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Thailand)","legendgroup":"(Asia,Thailand)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.017,74.249],"y":[1.09112789952196,1.94574417537078],"text":["lifeExp: 73.017<br />year: 2002<br />continent: Asia<br />country: Vietnam","lifeExp: 74.249<br />year: 2007<br />continent: Asia<br />country: Vietnam"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,108,145,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Vietnam)","legendgroup":"(Asia,Vietnam)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.37,73.422],"y":[0.976997219957411,2.04711130275391],"text":["lifeExp: 72.370<br />year: 2002<br />continent: Asia<br />country: West Bank and Gaza","lifeExp: 73.422<br />year: 2007<br />continent: Asia<br />country: West Bank and Gaza"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,108,145,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,West Bank and Gaza)","legendgroup":"(Asia,West Bank and Gaza)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[60.308,62.698],"y":[0.96177007262595,2.09668843783438],"text":["lifeExp: 60.308<br />year: 2002<br />continent: Asia<br />country: Yemen, Rep.","lifeExp: 62.698<br />year: 2007<br />continent: Asia<br />country: Yemen, Rep."],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,108,145,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,191,125,1)"}},"hoveron":"points","name":"(Asia,Yemen, Rep.)","legendgroup":"(Asia,Yemen, Rep.)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[75.651,76.423],"y":[0.963230920163915,2.03487153789029],"text":["lifeExp: 75.651<br />year: 2002<br />continent: Europe<br />country: Albania","lifeExp: 76.423<br />year: 2007<br />continent: Europe<br />country: Albania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(246,120,102,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Albania)","legendgroup":"(Europe,Albania)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.98,79.829],"y":[1.04803347992711,2.00932976980694],"text":["lifeExp: 78.980<br />year: 2002<br />continent: Europe<br />country: Austria","lifeExp: 79.829<br />year: 2007<br />continent: Europe<br />country: Austria"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(237,129,61,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Austria)","legendgroup":"(Europe,Austria)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.32,79.441],"y":[0.934704443905503,2.03331899130717],"text":["lifeExp: 78.320<br />year: 2002<br />continent: Europe<br />country: Belgium","lifeExp: 79.441<br />year: 2007<br />continent: Europe<br />country: Belgium"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(230,135,15,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Belgium)","legendgroup":"(Europe,Belgium)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.09,74.852],"y":[1.0145863942802,1.90991969867609],"text":["lifeExp: 74.090<br />year: 2002<br />continent: Europe<br />country: Bosnia and Herzegovina","lifeExp: 74.852<br />year: 2007<br />continent: Europe<br />country: Bosnia and Herzegovina"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(222,140,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Bosnia and Herzegovina)","legendgroup":"(Europe,Bosnia and Herzegovina)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.14,73.005],"y":[0.983965647034347,2.04274298413657],"text":["lifeExp: 72.140<br />year: 2002<br />continent: Europe<br />country: Bulgaria","lifeExp: 73.005<br />year: 2007<br />continent: Europe<br />country: Bulgaria"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(213,145,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Bulgaria)","legendgroup":"(Europe,Bulgaria)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.876,75.748],"y":[0.972481701290235,2.04095933567733],"text":["lifeExp: 74.876<br />year: 2002<br />continent: Europe<br />country: Croatia","lifeExp: 75.748<br />year: 2007<br />continent: Europe<br />country: Croatia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(150,169,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Croatia)","legendgroup":"(Europe,Croatia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[75.51,76.486],"y":[0.936578186461702,1.94885688000359],"text":["lifeExp: 75.510<br />year: 2002<br />continent: Europe<br />country: Czech Republic","lifeExp: 76.486<br />year: 2007<br />continent: Europe<br />country: Czech Republic"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(139,171,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Czech Republic)","legendgroup":"(Europe,Czech Republic)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.18,78.332],"y":[0.949586808681488,1.99542863941751],"text":["lifeExp: 77.180<br />year: 2002<br />continent: Europe<br />country: Denmark","lifeExp: 78.332<br />year: 2007<br />continent: Europe<br />country: Denmark"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(133,173,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Denmark)","legendgroup":"(Europe,Denmark)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.37,79.313],"y":[0.937743838177994,1.97776417862624],"text":["lifeExp: 78.370<br />year: 2002<br />continent: Europe<br />country: Finland","lifeExp: 79.313<br />year: 2007<br />continent: Europe<br />country: Finland"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(51,182,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Finland)","legendgroup":"(Europe,Finland)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[79.59,80.657],"y":[0.993354806723073,1.99671142650768],"text":["lifeExp: 79.590<br />year: 2002<br />continent: Europe<br />country: France","lifeExp: 80.657<br />year: 2007<br />continent: Europe<br />country: France"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(28,183,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,France)","legendgroup":"(Europe,France)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.67,79.406],"y":[1.04743328629993,1.91178709659725],"text":["lifeExp: 78.670<br />year: 2002<br />continent: Europe<br />country: Germany","lifeExp: 79.406<br />year: 2007<br />continent: Europe<br />country: Germany"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,185,53,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Germany)","legendgroup":"(Europe,Germany)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.256,79.483],"y":[1.05966044059023,2.04751614280976],"text":["lifeExp: 78.256<br />year: 2002<br />continent: Europe<br />country: Greece","lifeExp: 79.483<br />year: 2007<br />continent: Europe<br />country: Greece"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,187,72,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Greece)","legendgroup":"(Europe,Greece)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[72.59,73.338],"y":[0.985514190234244,1.90175741608255],"text":["lifeExp: 72.590<br />year: 2002<br />continent: Europe<br />country: Hungary","lifeExp: 73.338<br />year: 2007<br />continent: Europe<br />country: Hungary"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,121,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Hungary)","legendgroup":"(Europe,Hungary)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[80.5,81.757],"y":[0.910367924859747,2.0878308376763],"text":["lifeExp: 80.500<br />year: 2002<br />continent: Europe<br />country: Iceland","lifeExp: 81.757<br />year: 2007<br />continent: Europe<br />country: Iceland"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,126,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Iceland)","legendgroup":"(Europe,Iceland)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.783,78.885],"y":[0.952695762459189,1.92258771089837],"text":["lifeExp: 77.783<br />year: 2002<br />continent: Europe<br />country: Ireland","lifeExp: 78.885<br />year: 2007<br />continent: Europe<br />country: Ireland"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,193,154,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Ireland)","legendgroup":"(Europe,Ireland)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[80.24,80.546],"y":[1.06644695089199,1.90377420252189],"text":["lifeExp: 80.240<br />year: 2002<br />continent: Europe<br />country: Italy","lifeExp: 80.546<br />year: 2007<br />continent: Europe<br />country: Italy"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,193,164,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Italy)","legendgroup":"(Europe,Italy)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.981,74.543],"y":[0.953468616632745,1.95986663834192],"text":["lifeExp: 73.981<br />year: 2002<br />continent: Europe<br />country: Montenegro","lifeExp: 74.543<br />year: 2007<br />continent: Europe<br />country: Montenegro"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,178,243,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Montenegro)","legendgroup":"(Europe,Montenegro)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.53,79.762],"y":[0.935974230803549,1.9066234018188],"text":["lifeExp: 78.530<br />year: 2002<br />continent: Europe<br />country: Netherlands","lifeExp: 79.762<br />year: 2007<br />continent: Europe<br />country: Netherlands"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,167,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Netherlands)","legendgroup":"(Europe,Netherlands)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[79.05,80.196],"y":[0.998426835332066,1.91386600430123],"text":["lifeExp: 79.050<br />year: 2002<br />continent: Europe<br />country: Norway","lifeExp: 80.196<br />year: 2007<br />continent: Europe<br />country: Norway"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(102,155,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Norway)","legendgroup":"(Europe,Norway)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[74.67,75.563],"y":[0.995360208163038,2.03761481004767],"text":["lifeExp: 74.670<br />year: 2002<br />continent: Europe<br />country: Poland","lifeExp: 75.563<br />year: 2007<br />continent: Europe<br />country: Poland"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(170,136,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Poland)","legendgroup":"(Europe,Poland)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[77.29,78.098],"y":[0.930546476319432,1.95595686808228],"text":["lifeExp: 77.290<br />year: 2002<br />continent: Europe<br />country: Portugal","lifeExp: 78.098<br />year: 2007<br />continent: Europe<br />country: Portugal"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(177,134,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Portugal)","legendgroup":"(Europe,Portugal)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[71.322,72.476],"y":[1.03753744857386,1.90770065225661],"text":["lifeExp: 71.322<br />year: 2002<br />continent: Europe<br />country: Romania","lifeExp: 72.476<br />year: 2007<br />continent: Europe<br />country: Romania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(196,125,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Romania)","legendgroup":"(Europe,Romania)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.213,74.002],"y":[1.0565608706791,2.0965729622636],"text":["lifeExp: 73.213<br />year: 2002<br />continent: Europe<br />country: Serbia","lifeExp: 74.002<br />year: 2007<br />continent: Europe<br />country: Serbia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(221,113,250,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Serbia)","legendgroup":"(Europe,Serbia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[73.8,74.663],"y":[0.910192164732143,1.99593226453289],"text":["lifeExp: 73.800<br />year: 2002<br />continent: Europe<br />country: Slovak Republic","lifeExp: 74.663<br />year: 2007<br />continent: Europe<br />country: Slovak Republic"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(233,106,242,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Slovak Republic)","legendgroup":"(Europe,Slovak Republic)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[76.66,77.926],"y":[0.930974889919162,2.05492229457013],"text":["lifeExp: 76.660<br />year: 2002<br />continent: Europe<br />country: Slovenia","lifeExp: 77.926<br />year: 2007<br />continent: Europe<br />country: Slovenia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(236,105,239,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Slovenia)","legendgroup":"(Europe,Slovenia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[79.78,80.941],"y":[0.993354937620461,2.04117298251949],"text":["lifeExp: 79.780<br />year: 2002<br />continent: Europe<br />country: Spain","lifeExp: 80.941<br />year: 2007<br />continent: Europe<br />country: Spain"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(244,100,229,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Spain)","legendgroup":"(Europe,Spain)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[80.04,80.884],"y":[1.00771638127044,2.05253562694415],"text":["lifeExp: 80.040<br />year: 2002<br />continent: Europe<br />country: Sweden","lifeExp: 80.884<br />year: 2007<br />continent: Europe<br />country: Sweden"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(252,97,213,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Sweden)","legendgroup":"(Europe,Sweden)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[80.62,81.701],"y":[0.981155067682266,2.07862368249334],"text":["lifeExp: 80.620<br />year: 2002<br />continent: Europe<br />country: Switzerland","lifeExp: 81.701<br />year: 2007<br />continent: Europe<br />country: Switzerland"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(253,97,209,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Switzerland)","legendgroup":"(Europe,Switzerland)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[70.845,71.777],"y":[0.994474390801042,2.06107844649814],"text":["lifeExp: 70.845<br />year: 2002<br />continent: Europe<br />country: Turkey","lifeExp: 71.777<br />year: 2007<br />continent: Europe<br />country: Turkey"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,101,172,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,Turkey)","legendgroup":"(Europe,Turkey)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[78.471,79.425],"y":[1.05955283902586,1.9735745721031],"text":["lifeExp: 78.471<br />year: 2002<br />continent: Europe<br />country: United Kingdom","lifeExp: 79.425<br />year: 2007<br />continent: Europe<br />country: United Kingdom"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(255,104,162,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,176,246,1)"}},"hoveron":"points","name":"(Europe,United Kingdom)","legendgroup":"(Europe,United Kingdom)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[80.37,81.235],"y":[0.988810167461634,1.93558185440488],"text":["lifeExp: 80.370<br />year: 2002<br />continent: Oceania<br />country: Australia","lifeExp: 81.235<br />year: 2007<br />continent: Oceania<br />country: Australia"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(239,127,71,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(231,107,243,1)"}},"hoveron":"points","name":"(Oceania,Australia)","legendgroup":"(Oceania,Australia)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[79.11,80.204],"y":[0.981024888483807,1.92542773308232],"text":["lifeExp: 79.110<br />year: 2002<br />continent: Oceania<br />country: New Zealand","lifeExp: 80.204<br />year: 2007<br />continent: Oceania<br />country: New Zealand"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,165,255,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(231,107,243,1)"}},"hoveron":"points","name":"(Oceania,New Zealand)","legendgroup":"(Oceania,New Zealand)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":26.2283105022831,"r":7.30593607305936,"b":40.1826484018265,"l":48.9497716894977},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[37.0225,84.7735],"tickmode":"array","ticktext":["40","50","60","70","80"],"tickvals":[40,50,60,70,80],"categoryorder":"array","categoryarray":["40","50","60","70","80"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"lifeExp","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,2.6],"tickmode":"array","ticktext":["2002","2007"],"tickvals":[1,2],"categoryorder":"array","categoryarray":["2002","2007"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"year","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(51,51,51,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"title":{"text":"continent","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"40a0509f1f56":{"x":{},"y":{},"colour":{},"fill":{},"type":"scatter"}},"cur_data":"40a0509f1f56","visdat":{"40a0509f1f56":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

### Animated Plots

Finally, animated plots are a great way to add a wow factor to your reports, but they can be complex to make, distracting, and not very accessible, so use them sparingly and only for data visualization where the animation really adds something. The package <code class='package'><a href='https://gganimate.com/' target='_blank'>gganimate</a></code> has many functions for animating ggplots.

Here, we'll use the original Gapminder data that has life expectancy for every five years from 1952, not just 2002 and 2007.


```r
data("gapminder")
```

Let's make an animated plot showing how life expectancy in each region changes with year. First, make a static plot. Filter the data to the most recent year so you can see what a single frame of the animation will look like.


```r
gapminder %>%
  filter(year == 2007) %>%
  ggplot(aes(x = continent, y = lifeExp, fill = continent)) +
  stat_summary(fun = "mean", geom = "bar") +
  scale_fill_viridis_d() +
  scale_x_discrete(name = "", 
                   guide = guide_axis(n.dodge=2)) +
  ggtitle('Year: 2007')
```

<img src="06-custom-viz_files/figure-html/unnamed-chunk-16-1.png" width="100%" style="display: block; margin: auto;" />

To convert this to an animated plot that shows the data from multiple years:

* Remove the filter and add `transition_time(year)`. 
* Use the `{}` syntax to include the `frame_time` in the title. 
* Use `anim_save()` to save the animation to a GIF file and set this code chunk to `eval = FALSE` because creating an animation takes a long time and you don't want to have to run it every time you knit your report.



```r
anim <- gapminder %>%
  ggplot(aes(x = continent, y = lifeExp, fill = continent)) +
  stat_summary(fun = "mean", geom = "bar") +
  scale_fill_viridis_d() +
  scale_x_discrete(name = "",
                   guide = guide_axis(n.dodge=2))+ 
  ggtitle('Year: {frame_time}') +
  transition_time(year)
  
dir.create("images", FALSE) # creates an images directory if needed

anim_save(filename = "book/images/gganim-demo.gif",
          animation = anim,
          width = 8, height = 5, units = "in", res = 150)
```

You can show your animated gif in an html report (animations don't work in Word or a PDF) using `include_graphics()`, or include the GIF in a dynamic document like PowerPoint.


```r
knitr::include_graphics("images/gganim-demo.gif")
```

<div class="figure" style="text-align: center">
<img src="images/gganim-demo.gif" alt="Animated gif." width="100%" />
<p class="caption">(\#fig:anim-demo)Animated gif.</p>
</div>

::: {.warning data-latex=""}
There are actually not many plots that are really improved by animating them. The plot below gives the same information at a single glance.

<img src="06-custom-viz_files/figure-html/anim-alternative-1.png" width="100%" style="display: block; margin: auto;" />

:::

## End of workbook

And we're done with the workbook! Our aim with this course isn't for you to be fully proficient in R at this point. If you're new to coding, it's entirely unrealistic and unhelpful for any course to claim that you can fully learn to code in the space of a couple of weeks. Instead, we hope that we have demystified some of the many unwritten (or badly explained) rules and conventions you need to know to use R so that you have the tools to continue learning R more independently.

### Next steps

To complete the course, please attend the second office hour session and complete the completion quiz.

In this course, we focused heavily on data visualization because it provides a concrete, tangible output for your efforts. This is great for beginners because it helps map the code to the output, but also if you're anything like Emily, making pretty graphs is motivating. Our second course "Data wrangling and analysis" (due to run in Feb 2023) will build on the skills you've learned here and go deeper into how to clean, wrangle, and process your data, as well as how to perform some basic regression analyses.  

In the meantime, if you'd like to continue learning we'd suggest the following steps:

* Review the initial chapters of this book. Now you have hands-on experience with R, some of the information about functions and packages is likely to make a lot more sense.
* Review the further resources provided at the end of each chapter. In particular, if you would like another online book to work through, we'd highly recommend [R for Data Science](https://r4ds.had.co.nz/), [R for Health Data Science](https://argoshare.is.ed.ac.uk/healthyr_book/), and the collection of materials provided by the [PsyTeachR team](https://psyteachr.github.io/).
* Practice the skills and functions used in this course with different data sets. These could either be your own data sets or open data you find online or from R packages. If you have to do a little bit of wrangling in Excel to get the data in the format you need, don't feel bad - whatever gets you coding is a good thing and eventually you'll be able to have your entire workflow in R.

## Further Resources {#resources-viz}

* [Applied Data Skils: Data visualisation](https://psyteachr.github.io/ads-v1/viz.html) (from the PsyTeachR team)
* [Applied Data Skils: Customising visualisations](https://psyteachr.github.io/ads-v1/custom.html) (from the PsyTeachR team)
* [ggplot2 cheat sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-visualization.pdf)
* [Data visualisation using R, for researchers who don't use R](https://psyteachr.github.io/introdataviz/)
* [Chapter 3: Data Visualisation](http://r4ds.had.co.nz/data-visualisation.html) of *R for Data Science*
* [ggplot2 FAQs](https://ggplot2.tidyverse.org/articles/)
* [ggplot2 documentation](https://ggplot2.tidyverse.org/reference/)
* [Chapter 28: Graphics for communication](http://r4ds.had.co.nz/graphics-for-communication.html) of *R for Data Science*
* [gganimate](https://gganimate.com/): A package for making animated plots
* [The R Graph Gallery](http://www.r-graph-gallery.com/) (this is really useful)
* [Look at Data](https://socviz.co/lookatdata.html) from [Data Vizualization for Social Science](http://socviz.co/)
* [Graphs](http://www.cookbook-r.com/Graphs) in *Cookbook for R*
* [R Graphics Cookbook](http://www.cookbook-r.com/Graphs/) by Winston Chang
* [ggplot extensions](https://exts.ggplot2.tidyverse.org/)
* [plotly](https://plot.ly/ggplot2/) for creating interactive graphs
