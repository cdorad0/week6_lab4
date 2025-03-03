# Lab 4: Importing NOAA Buoy Data from Monterey Bay
Catalina Dorado
2025-03-02

# Part 1: Setup

## GitHub Workflow

Set up your GitHub workflow (either using the method of creating a
repository and using Version Control to set up your project or vice
versa using the `usethis` package commands we have learned).

Use appropriate naming conventions for your project (see Code Style
Guide), e.g. `lab-4-import-data`.

Your project folder should contain the following:

- .Rproj  
- `lab-4-student.qmd`
- “data-raw” folder
  - `buoy_2024.txt`
- “data-clean” folder that contains final data set  
- rendered document  
- rendered document (`.md`)

## Seeking Help

Part of learning to program is learning from a variety of resources.
Thus, I expect you will use resources that you find on the internet.
There is, however, an important balance between copying someone else’s
code and *using their code to learn*. Therefore, if you use external
resources, I want to know about it.

- If you used Google, you are expected to “inform” me of any resources
  you used by **pasting the link to the resource in a code comment next
  to where you used that resource**.

- If you used ChatGPT, you are expected to “inform” me of the assistance
  you received by (1) indicating somewhere in the problem that you used
  ChatGPT (e.g., below the question prompt or as a code comment),
  and (2) downloading and including the `.txt` file containing your
  **entire** conversation with ChatGPT in your repository. ChatGPT can
  we used as a “search engine”, but you should not copy and paste
  prompts from the lab or the code into your lab.

Additionally, you are permitted and encouraged to work with your peers
as you complete lab assignments, but **you are expected to do your own
work**. Copying from each other is cheating, and letting people copy
from you is also cheating. Please don’t do either of those things.

## Lab Instructions

The questions in this lab are noted with numbers and boldface. Each
question will require you to produce code, whether it is one line or
multiple lines.

This document is quite plain, meaning it does not have any special
formatting. As part of your demonstration of creating professional
looking Quarto documents, **I would encourage you to spice your
documents up (e.g., declaring execution options, specifying how your
figures should be output, formatting your code output, etc.).**

## Setup

In the code chunk below, load in the packages necessary for your
analysis. You should only need the `tidyverse` package for this
analysis.

``` r
# Load necessary packages for data analysis
library(tidyverse)
```

# Part 2: Data Context

Throughout the Monterey Bay there are various NOAA stations that capture
data on wind speed, wave height, air temperature and many other data
points. Data such as this can be used to predict weather or tidal event
or used in conjunction with other data, such as population estimates of
sardines or whale migration, to understand the ocean ecosystem.

<div>

<table>
<colgroup>
<col style="width: 70%" />
<col style="width: 30%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: center;"><div id="first-column" width="70.0%"
data-layout-align="center">
<p>For this lab, we will focus on <a
href="https://www.ndbc.noaa.gov/station_history.php?station=46092">Station
46092</a>, which is maintained by MBARI (Monterey Bay Aquarium Research
Institute). which is on the outer edge of the Monterey Bay Canyon. Data
is collected and stored hourly, but sometimes there are issues with
censors not working or other events that lead to missing data.</p>
</div></td>
<td style="text-align: center;"><div id="second-column" width="30.0%"
data-layout-align="center">
<p><img src="images/mbari-station-46092.png"
data-fig-alt="A topographical map of the Monterey Bay, CA region with the buoy 46092 indicated near the Monterey Bay Cayon outer edge."
data-fig.extended="false" /></p>
</div></td>
</tr>
</tbody>
</table>

</div>

We will be looking at a historical file from 2024 to examine air
temperature trends over the last year. Historical files have gone
through post-processing analysis and represent the data sent to the
archive centers. The formats for both are generally the same, with the
major difference being the treatment of missing data. Missing data in
the Historical files are variable number of 9’s, depending on the data
type (for example: 999.0 99.0).

In addition, the data file also stores a “comment” in the second row
that contains the units measured for each variable. Read more about the
[variables stored in the historical files
here](https://www.ndbc.noaa.gov/faq/measdes.shtml)

Finally, notice that the data storage type is `txt` which means we
cannot use `read_csv()` to import the data. Instead

## Step 1: Reading the Data into `R`

Read the data into R. Since it is a `txt` file it is considered a
“whitespace-separated file”. Use the
(readr)\[https://readr.tidyverse.org/index.html\] website to determine
the appropriate `read_XXX()` function.

When you read in the data give it an appropriate name for what the data
represents. The read in file should look like this (note this is just
the first seven columns):

``` r
# Load data txt file website
buoy <- read_table("https://jcanner.github.io/stat_210_2025_website/docs/labs/student/data/buoy_2024.txt")
```

Notice that the data is all read in as a character value and the first
row contains information that is not data, but additional information
about the variables: this is called a “comment” in the data.

## Step 2: Specify the Comment Row

In your data, you will notice the `#` in front of `yr` in the second
row. That is the symbol that indicates a comment in the data set. Using
the cheatsheet or the (readr)\[https://readr.tidyverse.org/index.html\]
website, add an argument to your function that indicates that the
comment row begins with `#`. Your data should now look like this:

``` r
buoy <- read_table("https://jcanner.github.io/stat_210_2025_website/docs/labs/student/data/buoy_2024.txt", 
                   comment = "#")
```

## Step 3: Clean the Variable Names

We want to use snake_case for our variable names. Use the `janitor`
package to clean the variable names. Notice, that because month was
originally coded `MM` and minute was originally coded `mm` that now
month is `mm` and minute is `mm_2` to create a distinction between them.

``` r
# Load necessary package to clean variable names
library(janitor)

# Clean names so that month is `MM' to `mm` and minute is `mm` to `mm_2
buoy <- clean_names(buoy)

buoy
```

    # A tibble: 8,525 × 18
          yy mm    dd    hh    mm_2   wdir  wspd   gst  wvht   dpd   apd   mwd  pres
       <dbl> <chr> <chr> <chr> <chr> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
     1  2024 01    01    00    18       60   2.1    99    99    99    99   999 1019.
     2  2024 01    01    01    18       80   1      99    99    99    99   999 1019.
     3  2024 01    01    02    18      100   1.5    99    99    99    99   999 1019.
     4  2024 01    01    03    18       80   1.5    99    99    99    99   999 1019.
     5  2024 01    01    04    18       60   1.5    99    99    99    99   999 1019.
     6  2024 01    01    05    18      350   4.1    99    99    99    99   999 1019.
     7  2024 01    01    06    18       20   4.1    99    99    99    99   999 1019.
     8  2024 01    01    07    18       30   4.1    99    99    99    99   999 1020.
     9  2024 01    01    08    18       60   2.6    99    99    99    99   999 1020.
    10  2024 01    01    09    18       70   4.1    99    99    99    99   999 1020.
    # ℹ 8,515 more rows
    # ℹ 5 more variables: atmp <dbl>, wtmp <dbl>, dewp <dbl>, vis <dbl>, tide <dbl>

## Step 4: Write the Data Part 1

We will have a bit of cleaning to do on the data, but we have an issue.
The 99 and 999’s in the data are numeric and not character values. In
order for us to specify them as `NA`s we need to do a little work
around.

Write the data to a `csv` file called `buoy_update.csv`. Be sure to
write it to your `data-raw` folder.

``` r
# Write data to a `csv` file
write_csv(buoy, 
          file = "~/Documents/STATS_210_Spring_25/Labs/week6_lab4/data-raw/buoy_update.csv")
```

## Step 5: Dealing with Missing Values

Now we will read the data back in, be sure to assign the it a meaningful
object name, **BUT** we are going to do two things:

1.  Specify all column type *defaults* as character.  
2.  Specify `NA`s as “99” and “999”

Remember that now we are reading back in a data file of type `csv`. Now
when you look at the data we should have `NA` instead of `99` or `999`
in our data, for example:

``` r
# Read in data, specify column type (defaults) as character, specify `NA`s as "99" and "999"
buoy_update <- read_csv("~/Documents/STATS_210_Spring_25/Labs/week6_lab4/data-raw/buoy_update.csv", 
                        col_types = list(.default = col_character()), 
                        na = c("99", "999"))
buoy_update
```

    # A tibble: 8,525 × 18
       yy    mm    dd    hh    mm_2  wdir  wspd  gst   wvht  dpd   apd   mwd   pres 
       <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
     1 2024  01    01    00    18    60    2.1   <NA>  <NA>  <NA>  <NA>  <NA>  1018…
     2 2024  01    01    01    18    80    1     <NA>  <NA>  <NA>  <NA>  <NA>  1018…
     3 2024  01    01    02    18    100   1.5   <NA>  <NA>  <NA>  <NA>  <NA>  1018…
     4 2024  01    01    03    18    80    1.5   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
     5 2024  01    01    04    18    60    1.5   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
     6 2024  01    01    05    18    350   4.1   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
     7 2024  01    01    06    18    20    4.1   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
     8 2024  01    01    07    18    30    4.1   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
     9 2024  01    01    08    18    60    2.6   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
    10 2024  01    01    09    18    70    4.1   <NA>  <NA>  <NA>  <NA>  <NA>  1019…
    # ℹ 8,515 more rows
    # ℹ 5 more variables: atmp <chr>, wtmp <chr>, dewp <chr>, vis <chr>, tide <chr>

This is great! But we created another problem. Now all of our numeric
values are being treated like characters and we don’t want that. There
are a couple of ways to fix this. We can write and reread in the data
setting the column types to `double` or we can use the `readr` parsing
functions.

Here is the code you can use to fix the data without exporting and
importing again (we will learn more about the `mutate()` function.
Insert whatever you called your buoy data on the last import for
`your_file_name`.

``` r
# Write and reread in the data, set column types to `double`
buoy_final <- buoy_update |> 
  mutate(across(.cols = everything(), ~parse_double(.x)))

buoy_final
```

    # A tibble: 8,525 × 18
          yy    mm    dd    hh  mm_2  wdir  wspd   gst  wvht   dpd   apd   mwd  pres
       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
     1  2024     1     1     0    18    60   2.1    NA    NA    NA    NA    NA 1019.
     2  2024     1     1     1    18    80   1      NA    NA    NA    NA    NA 1019.
     3  2024     1     1     2    18   100   1.5    NA    NA    NA    NA    NA 1019.
     4  2024     1     1     3    18    80   1.5    NA    NA    NA    NA    NA 1019.
     5  2024     1     1     4    18    60   1.5    NA    NA    NA    NA    NA 1019.
     6  2024     1     1     5    18   350   4.1    NA    NA    NA    NA    NA 1019.
     7  2024     1     1     6    18    20   4.1    NA    NA    NA    NA    NA 1019.
     8  2024     1     1     7    18    30   4.1    NA    NA    NA    NA    NA 1020.
     9  2024     1     1     8    18    60   2.6    NA    NA    NA    NA    NA 1020.
    10  2024     1     1     9    18    70   4.1    NA    NA    NA    NA    NA 1020.
    # ℹ 8,515 more rows
    # ℹ 5 more variables: atmp <dbl>, wtmp <dbl>, dewp <dbl>, vis <dbl>, tide <dbl>

Now we have our clean, correctly classified, data ready for analysis as
`buoy_final`. Go ahead and write that data as a `csv` file into your
`data-clean` file. Once you’ve done that. Set the data cleaning code
chunks to `eval: false` so that you do not rerun them again when you
render the document.

``` r
# Write data as csv into data-clean folder
write_csv(buoy_final, 
          file = "~/Documents/STATS_210_Spring_25/Labs/week6_lab4/data-clean/buoy_final.csv")
```

# Part 3: Recreate the Following Visualization

Now that are data is “analytic ready”, go ahead and read in your clean
data and use it to try and recreate the following visualization. Do your
best to match the following visualization as close as possible. (Hint:
when specifying color, you may need to use `factor()` to get R to treat
a continuous value as discrete).

``` r
# Read in data with object name
canyon <- read_csv("~/Documents/STATS_210_Spring_25/Labs/week6_lab4/data-clean/buoy_final.csv", 
                   show_col_types = FALSE)

canyon
```

    # A tibble: 8,525 × 18
          yy    mm    dd    hh  mm_2  wdir  wspd gst   wvht  dpd   apd   mwd    pres
       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <lgl> <lgl> <lgl> <lgl> <lgl> <dbl>
     1  2024     1     1     0    18    60   2.1 NA    NA    NA    NA    NA    1019.
     2  2024     1     1     1    18    80   1   NA    NA    NA    NA    NA    1019.
     3  2024     1     1     2    18   100   1.5 NA    NA    NA    NA    NA    1019.
     4  2024     1     1     3    18    80   1.5 NA    NA    NA    NA    NA    1019.
     5  2024     1     1     4    18    60   1.5 NA    NA    NA    NA    NA    1019.
     6  2024     1     1     5    18   350   4.1 NA    NA    NA    NA    NA    1019.
     7  2024     1     1     6    18    20   4.1 NA    NA    NA    NA    NA    1019.
     8  2024     1     1     7    18    30   4.1 NA    NA    NA    NA    NA    1020.
     9  2024     1     1     8    18    60   2.6 NA    NA    NA    NA    NA    1020.
    10  2024     1     1     9    18    70   4.1 NA    NA    NA    NA    NA    1020.
    # ℹ 8,515 more rows
    # ℹ 5 more variables: atmp <dbl>, wtmp <dbl>, dewp <lgl>, vis <lgl>, tide <lgl>

``` r
# View first few rows of the clean dataset
names(canyon)
```

     [1] "yy"   "mm"   "dd"   "hh"   "mm_2" "wdir" "wspd" "gst"  "wvht" "dpd" 
    [11] "apd"  "mwd"  "pres" "atmp" "wtmp" "dewp" "vis"  "tide"

``` r
# Identify the distinct years of the dataset
canyon |>
  distinct(yy)
```

    # A tibble: 1 × 1
         yy
      <dbl>
    1  2024

``` r
# Identify the distinct months of the dataset
canyon |>
  distinct(mm)
```

    # A tibble: 12 × 1
          mm
       <dbl>
     1     1
     2     2
     3     3
     4     4
     5     5
     6     6
     7     7
     8     8
     9     9
    10    10
    11    11
    12    12

``` r
# Select months and air temperature for the dataset for the year, 2024

air_temp <- canyon |> 
  select(c(2, 14))

# View first few lines of the dataset
head(air_temp)
```

    # A tibble: 6 × 2
         mm  atmp
      <dbl> <dbl>
    1     1  13.5
    2     1  13.5
    3     1  13.7
    4     1  13.8
    5     1  13.8
    6     1  13.7

``` r
# Plot the initial data points on a simple scatterplot
ggplot(air_temp, aes(x = mm, y = atmp)) + 
  geom_point()
```

![](lab-4-student_files/figure-commonmark/unnamed-chunk-5-1.png)

``` r
# Add a smooth line with aesthetic `color` black
ggplot(air_temp, aes(x = mm, y = atmp)) + 
  geom_jitter() + 
  geom_smooth(color = "black")
```

![](lab-4-student_files/figure-commonmark/unnamed-chunk-6-1.png)

``` r
# Add appropriate titles and colors
ggplot(air_temp, aes(x = mm, y = atmp)) +
  geom_jitter(aes(color = factor(mm)), alpha = 0.1, width = .1) + 
  labs(x = "Months in 2024",
       y = "Air Temperature (Celcius)",
       title = "Monterey Bay Canyon Air Temperature in 2024",
       caption = "Source: NOAA National Data Buoy Center") + 
   geom_smooth(color = "black")
```

![](lab-4-student_files/figure-commonmark/unnamed-chunk-7-1.png)

``` r
# Add breaks to the x axis and add additional themes to the plot
ggplot(air_temp, aes(x = mm, y = atmp)) +
  geom_jitter(aes(color = factor(mm)), alpha = 0.1, width = .1) + 
  labs(x = "Months in 2024",
       y = "Air Temperature (Celcius)",
       title = "Monterey Bay Canyon Air Temperature in 2024",
       caption = "Source: NOAA National Data Buoy Center") +
  geom_smooth(color = "black") + 
  scale_x_continuous(breaks = seq(1, 12, 1)) + 
  theme_light() + 
  theme(axis.title = element_text(size = rel(.9))) + 
  theme(title = element_text(size = rel(.9))) + 
  theme(axis.text = element_text(size = rel(.7))) + 
  theme(legend.position = "none") + 
  scale_colour_viridis_d()
```

![](lab-4-student_files/figure-commonmark/unnamed-chunk-8-1.png)

# Part 4: Find a Data Set

For your final portfolio, you will choose a data set to clean and
provide an analysis of through visualization (or more if you have the
experience). For this week, choose a data set

- It is a real-world application of R that has not exactly been worked
  out before (e.g. it isn’t a demo from some package or blog).  
- It is interesting to you.  
- It involves data and analyzing or presenting that data. The data may
  be data you have from a lab, or something you have retrieved from the
  web, some examples of good sources: FBI database on crime statistics,
  National Oceanic and Atmospheric Administration, World Health
  Organization, Twitter, Yahoo finance data, etc. If you are having
  problems finding a dataset, see the resources at the end of the
  project description.  
- The analysis and presentation is useful in the real-world.

One fun source for data is [Tidy
Tuesday](https://github.com/rfordatascience/tidytuesday) which posts a
new data set almost every week. It is a great place to find ideas and
sources.

**For your found data write a brief description of the data and and
provide its source.**

> Insert your description and link to your source here. Identify 2-3
> research questions you could try to answer with this data.

# Lab 4 Submission

For Lab 4 you will submit the link to your GitHub repository. Your
rendered file is required to have the following specifications in the
YAML options (at the top of your document):

- have the plots embedded (`embed-resources: true`)
- include your source code (`code-tools: true`)
- include all your code and output (`echo: true`)

**If any of the options are not included, your Lab 3 or Challenge 3
assignment will receive an “Incomplete” and you will be required to
submit a revision.**

In addition, your document should not have any warnings or messages
output in your HTML document. **If your HTML contains warnings or
messages, you will receive an “Incomplete” for document formatting and
you will be required to submit a revision.**
