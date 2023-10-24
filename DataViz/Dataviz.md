---
author:
- Camila Pacheco
date: 2024-11-10
editor: visual
title: DataViz2
toc-title: Table of contents
---

# Vegetation of magical lands

## Data visualisation tutorial

## Load libraries

::: cell
``` {.r .cell-code}
library(tidyverse)
```

::: {.cell-output .cell-output-stderr}
    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
:::
:::

## Read in data

::: cell
``` {.r .cell-code}
#|{.nowarn}
magic_veg <- read_csv("magic_veg.csv")
```

::: {.cell-output .cell-output-stderr}
    New names:
    Rows: 5898 Columns: 8
    ── Column specification
    ──────────────────────────────────────────────────────── Delimiter: "," chr
    (3): land, species, id dbl (5): ...1, plot, year, abundance, height
    ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    Specify the column types or set `show_col_types = FALSE` to quiet this message.
    • `` -> `...1`
:::
:::

We will first explore our dataset using the str() function, which shows
what type each variable is. What is the dataset made of?

::: cell
``` {.r .cell-code}
str(magic_veg)
```

::: {.cell-output .cell-output-stdout}
    spc_tbl_ [5,898 × 8] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
     $ ...1     : num [1:5898] 1 2 3 4 5 6 7 8 9 10 ...
     $ land     : chr [1:5898] "Narnia" "Narnia" "Narnia" "Narnia" ...
     $ plot     : num [1:5898] 1 1 1 1 1 1 1 1 1 1 ...
     $ year     : num [1:5898] 1999 1999 1999 1999 1999 ...
     $ species  : chr [1:5898] "DEGVAG" "YALRET" "YALRET" "XXXothermoss" ...
     $ abundance: num [1:5898] 2 2 2 2 2 3 2 2 3 3 ...
     $ height   : num [1:5898] 36 3 1.5 0 6 4.5 6.5 16.5 5.5 4 ...
     $ id       : chr [1:5898] "1999HE111" "1999HE112" "1999HE113" "1999HE114" ...
     - attr(*, "spec")=
      .. cols(
      ..   ...1 = col_double(),
      ..   land = col_character(),
      ..   plot = col_double(),
      ..   year = col_double(),
      ..   species = col_character(),
      ..   abundance = col_double(),
      ..   height = col_double(),
      ..   id = col_character()
      .. )
     - attr(*, "problems")=<externalptr> 
:::
:::

-   land - the location within the land of magic (two possible lands:
    Narnia and Hogsmeade)

-   plot - the plot number within each land

-   year - the year the measurement was taken

-   species - the species name (or code), Note that these are fake
    species!

-   height - the imaginary canopy height at that point

-   id - the id of each observation

    # Customise histograms in `ggplot2`

Let us first calculate how many species there are in each plot.

::: cell
``` {.r .cell-code}
species_counts <- magic_veg %>%
  group_by(land, plot) %>%
  summarise(Species_number = length(unique(species)))
```

::: {.cell-output .cell-output-stderr}
    `summarise()` has grouped output by 'land'. You can override using the
    `.groups` argument.
:::
:::

::: cell
``` {.r .cell-code}
(hist <- ggplot(species_counts, aes(x = plot)) +
  geom_histogram())
```

::: {.cell-output .cell-output-stderr}
    `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-5-1.png)
:::
:::

**Note that putting your entire ggplot code in brackets () creates the
graph and then shows it in the plot viewer. Uh, oh... That's a weird
histogram!**

This is the common way of making a histogram, when you have one
observation per row and the histogram tallies them for you. But you can
immediately see that it doesn't look right, because we are working with
summarised data. You therefore need to tell R that you *already
know* how many species are in each plot. You do that by specifying
the `stat` argument:

::: cell
``` {.r .cell-code}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number)) +
    geom_histogram(stat = "identity"))
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity"): Ignoring unknown parameters:
    `binwidth`, `bins`, and `pad`
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-6-1.png)
:::

``` {.r .cell-code}
# Note: an equivalent alternative is to use geom_col (for column), which takes a y value and displays it
(col <- ggplot(species_counts, aes(x = plot, y = Species_number)) +
   geom_col()
   )
```

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-6-2.png)
:::
:::

That looks a bit better, but it still seems to have far too many
species. That's because plots from each land are being grouped together.
We can separate them by introducing a colour code, and make a stacked
bar plot like this:

::: cell
``` {.r .cell-code}
#|{.nowarn}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number, fill = land)) +
  geom_histogram(stat = "identity"))
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity"): Ignoring unknown parameters:
    `binwidth`, `bins`, and `pad`
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-7-1.png)
:::

``` {.r .cell-code}
# Remember that any aesthetics that are a function of your data (like fill here) need to be INSIDE the aes() brackets.
```
:::

And if we want to make the columns to appear side by side rather than
being stacked, you add `position = "dodge"` to the `geom`'s arguments.

::: cell
``` {.r .cell-code}
#|{.nowarn}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number, fill = land)) +
    geom_histogram(stat = "identity", position = "dodge"))
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity", position = "dodge"): Ignoring
    unknown parameters: `binwidth`, `bins`, and `pad`
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-8-1.png)
:::
:::

Fixing the scale

::: cell
``` {.r .cell-code}
#|{.nowarn}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number, fill = land)) +
    geom_histogram(stat = "identity", position = "dodge") + 
    scale_x_continuous(breaks = c(1,2,3,4,5,6)) + 
    scale_y_continuous(limits = c(0, 50)))
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity", position = "dodge"): Ignoring
    unknown parameters: `binwidth`, `bins`, and `pad`
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-9-1.png)
:::
:::

## Add titles, subtitles, captions and axis labels

::: cell
``` {.r .cell-code}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number, fill = land)) +
    geom_histogram(stat = "identity", position = "dodge") +
    scale_x_continuous(breaks = c(1,2,3,4,5,6)) + 
    scale_y_continuous(limits = c(0, 50)) +
    labs(title = "Species richness by plot", 
         subtitle = "In the magical lands",
         caption = "Data from the Ministry of Magic", 
         x = "\n Plot number", y = "Number of species \n",# \n adds space before x and after y axis text
           fill = "Land")  # Change the legend title to "Land"
  )     
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity", position = "dodge"): Ignoring
    unknown parameters: `binwidth`, `bins`, and `pad`
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/%7B.nowarn%7D-1.png)
:::
:::

\[Important\] Take Full Control of Your Plot!

You have the power to customize every aspect of your plot, and one way
to do this is by using the `theme()` function in ggplot2. You can adjust
a wide range of visual elements to suit your preferences. We've
previously introduced some theme elements in our tutorials, and here,
we'll focus on changing font sizes for axis labels, axis titles, and the
plot title. But that's just the tip of the iceberg; you can explore even
more customizations like:

-   Font Styles: You can italicize or bold the text using
    `face = 'italic'` or `face = 'bold'`, respectively.
-   Text Alignment: Center the title by specifying `hjust = 0.5`.

## Change the plot background

Adding `theme_bw()` to our plot removes the grey background and replaces
it with a white one. There are various other themes built into RStudio,
but we personally think this is the cleanest one.

::: cell
``` {.r .cell-code}
#|{.nowarn}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number, fill = land)) +
    geom_histogram(stat = "identity", position = "dodge") + 
    scale_x_continuous(breaks = c(1,2,3,4,5,6)) + 
    scale_y_continuous(limits = c(0, 50)) +
    labs(title = "Species richness by plot", 
         x = "\n Plot number", y = "Number of species \n",
         fill = "Land") + 
    theme_bw() +
    theme(panel.grid = element_blank(), 
          axis.text = element_text(size = 12), 
          axis.title = element_text(size = 12), 
          plot.title = element_text(size = 14, hjust = 0.5, face = "bold")))
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity", position = "dodge"): Ignoring
    unknown parameters: `binwidth`, `bins`, and `pad`
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-11-1.png)
:::
:::

## Fix the legend and customise the colours

::: cell
``` {.r .cell-code}
#|{.nowarn}
(hist <- ggplot(species_counts, aes(x = plot, y = Species_number, fill = land)) +
    geom_histogram(stat = "identity", position = "dodge") + 
    scale_x_continuous(breaks = c(1,2,3,4,5,6)) + 
    scale_y_continuous(limits = c(0, 50)) +
    scale_fill_manual(values = c("rosybrown1", "#deebf7"),     # specifying the colours
                      name = "Land of Magic") +                # specifying title of legend
    labs(title = "Species richness by plot", 
         x = "\n Plot number", y = "Number of species \n") + 
    theme_bw() +
    theme(panel.grid = element_blank(), 
          axis.text = element_text(size = 12), 
          axis.title = element_text(size = 12), 
          plot.title = element_text(size = 14, hjust = 0.5, face = "bold"), 
          plot.margin = unit(c(0.5,0.5,0.5,0.5), units = , "cm"), 
      legend.title = element_text(face = "bold"),
          legend.position = "bottom", 
          legend.box.background = element_rect(color = "grey", size = 0.3)))
```

::: {.cell-output .cell-output-stderr}
    Warning in geom_histogram(stat = "identity", position = "dodge"): Ignoring
    unknown parameters: `binwidth`, `bins`, and `pad`
:::

::: {.cell-output .cell-output-stderr}
    Warning: The `size` argument of `element_rect()` is deprecated as of ggplot2 3.4.0.
    ℹ Please use the `linewidth` argument instead.
:::

::: cell-output-display
![](Dataviz_files/figure-markdown/unnamed-chunk-12-1.png)
:::
:::

::: cell
``` {.r .cell-code}
ggsave("magical-sp-rich-hist.png", width = 7, height = 5, dpi = 300)
```
:::
