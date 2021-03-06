# Example Dynamic Communication of Biomedical Data

An example approach to dynamically communicating biomedical data.

## Objective

Create a web page that both summarizes the composition of and highlights the outliers (interesting cases) of a cohort.

## Desired Elements

The checklist of desired elements includes annotations below:

- [X] Dynamic web page that lays out the various components in a clean and easy to follow manner  
  While I hope that both the "Explore" and "Cohort Comparison" tabs are easy to follow, I have included a "Guidance" tab with an overview of the interface.
- [X] Have a header that includes the name of the cohort and placeholder for other descriptors or metadata  
  The header is visible in the "Explore" tab.
- [X] Include at least 2 meaningful visuals using Vega (https://vega.github.io/vega/)  
  The code in this repository includes examples of using an R API to vegalite to render visualizations using Vega. However, as I was developing this codebase, testing revealed that Vega-Lite takes substantially longer to render than `ggplot2` (5+ seconds). Given that speed was a desired element of this challenge, and that I have implemented visualizations in Vega-Lite here (but have left the code commented out), I think it reasonable to have decided to use `ggplot2` to render visualizations, while still checking this box.
- [X] Summary tables and other sections can be used to highlight certain points  
  The "Explore" tab includes a table of selected cases, with several summary visualizations.
- [X] Have a section or callout to outliers  
    (The t-SNE output is intended to call out outliers in an interactive way. Age-SD-based-outlier display (mean of selected cases, +/- 3*SD) is also featured in the "Explore" tab). 
	- [X] Include the reason why it is an outlier based on the method used (For example, a value is 3 times the stdev of the other points)  
	  Although t-SNE is somewhat of a black box in clarifying *what* about a set of points is different from another set of points, the "Guidance" tab provides an overview of this visualization technique.  
	  Further, age-based outlier detection within a sampled set of points is also explained in the "Guidance" tab, and is clearly indicated above the age histogram.
- [X] Web server component (any language) that serves up the data dynamically to power the page  
  The page is implemented in R with [Shiny](https://shiny.rstudio.com/).
	- [X] Some elements may be computed on the server, some may be done in JavaScript
	- [ ] Async requests from the browser to the server are preferred  
	  Async requests have **not yet** been implemented. However, this feature was recently introduced in Shiny, through the use of the `promises` and `future` packages, and is [newly documented](https://rstudio.github.io/promises/articles/shiny.html) (See also the RStudio webinar by Joe Cheng, "https://www.rstudio.com/resources/videos/scaling-shiny-apps-with-async-programming/").
	- [X] The web page should load in under a second  
	  The page itself loads in under 1 second, especially rendering the "Guidance" tab first; however, Shiny takes approximately 1 second to load pre-cached cleaned data, and GGplot2 takes approximately 4-5 seconds to render the main t-SNE plot. Precomputing this did not help, unfortunately. In a real-world scenario, I would focus additional future attention on optimizing load and computation time.
- [X] *Bonus:* a separate page that compares the two cohorts  
  The "Cohort Comparison" tab displays a visualization comparing the two cohorts for each categorical variable present in the cleaned dataset.

## Notes

- Data cleaning is not included in the main dashboard code. Instead, it is implemented through the scripts in the `0b_data_exploration_and_cleaning` directory, which are meant to be run in order on a pre-caching computer.
- No values (aside from column names) are hard-coded in the codebase.

## Use

The instructions below assume that [R](https://cran.cnr.berkeley.edu/) and [RStudio](https://www.rstudio.com/products/rstudio/download/#download) are installed.

The instructions below have been tested using RStudio for Linux version 1.1.442 and RStudio for Mac OSX version 1.0.153.

1. Open RStudio.
1. Within RStudio, click File --> "Open Project..."
1. Select `2018-06_shiny_example_biomedical_data.Rproj`.
1. **RStudio will now pause as it installs packages.** This installation process only needs to happen once.
  1. RStudio will present output as it completes these tasks (for example, `Installing rstudioapi...`).
  1. Some of the installation steps (for example, for the `BH`, `stringi`, and `dplyr` packages) may take a minute or more.
  1. If RStudio offers a `y/n` prompt, type "y" and press Enter.
  1. **When RStudio is finished, it will print the message, "Packrat bootstrap successfully completed."** Once this happens, R will restart, and you will be able to run any command in the console (for example, at this point, if you type `2+2` next to the `>` prompt in the "Console" pane and press Enter, R should return `4`).
1. Once RStudio is finished installing packages above:
    1. **Close RStudio (no need to save `.Rdata` when prompted), then reopen it and open the project again** (following the initial steps above).  
    Closing and re-opening RStudio will cause RStudio to recognize the packages it installed above to the project directory.
    1. Within RStudio, open `1_application/ui.R`.
    1. **Within RStudio, click "Session" --> "Set Working Directory" --> "To Source File Location"**
    1. Click "Run App" in the top right corner of the RStudio editor pane.
    1. RStudio may ask to install an updated version of the `shiny` package. Click "Yes".
    1. The app should launch in a web browser. If it launches in an internal browser, you can click "Open in Browser" in the top left corner of the internal viewer window.

## Additional Resources

### t-SNE

- A [Google Talks overview](https://medium.com/@Zelros/anomaly-detection-with-t-sne-211857b1cd00)
- A [conceptual text overview, with implementation notes for R and Python](https://www.analyticsvidhya.com/blog/2017/01/t-sne-implementation-r-python/)

### Vega

I initially put quite a bit of work into implementing the main t-SNE scatterplot in Vega or Vega-Lite.

After substantial research, however, I concluded that Vega does not yet have a robust (or any) API for selections. Selections (e.g., brushing / click-and-drag selection of scatterplot points) is possible in Vega, but publishing that back to R, e.g., does not seem feasible yet. See:

- The GitHub Issue ["APIs to interact with Selection's Data and Signals"](https://github.com/vega/vega-lite/issues/1830), which is open.
- The Vega [view API](https://vega.github.io/vega/docs/api/view/), which seems promising for this purpose, but is not yet well-documented enough to use, and almost wholly undocumented in the secondary landscape of StackExchange, and blogs.
  - I do have an [open StackOverflow Question](https://stackoverflow.com/questions/50902820/are-selections-in-vega-visualizations-accessible-from-outside-vega) about this topic.

### ICD-9 Codes

- [ICD 10 codes structure](https://www.webpt.com/blog/post/understanding-icd-10-code-structure)

## Issues / Places for Further Development

- data_subset() is an event that launches both on load and then one second after load, causing load time to be longer. This could be optimized.
- As mentioned above, currently, `ggplot2` is used in place of `vega`, as the latter took substantially longer to render on my development laptop.
- The emphasis on visual inspection of the t-SNE scatterplot means that this interface is not particularly (or perhaps at all) accessible to clinicians and researchers who have limited vision. Researching and developing approaches to convey the t-SNE output (and scatterplot output more generally) to users through a screen reader could be a fruitful future step. This could also be accomplished, e.g., by running the t-SNE output through a clustering algorithm such as k-means, and then summarizing the output of that. However, [t-SNE output cannot always be cluster-analyzed](https://stats.stackexchange.com/questions/263539/clustering-on-the-output-of-t-sne/264647#264647), because the t-SNE algorithm does not preserve distance.
- This development involved a lot of research into interface possibilities with Shiny. Now that the interface design has settled, in a real-world scenario, I would focus on adding both unit tests and functional tests (the latter, for example, through the new [Shinytest](https://www.rstudio.com/resources/webinars/testing-shiny-applications-with-shinytest/) package).
    - Further, there are several internal functions that, ideally, should receive full ROxygen-based documentation.
- While the app is running, I have allowed `ggplot2` to produce a warning that notes when there are data rows that are left out of visualizations because of incomplete data. I decided to leave this in (rather than adding an `na.omit()` line in the code) to improve initial diagnostics as the app runs, pointing out the number of rows that remain with missing data even after data cleaning. This warning could be turned off / the missing data removed more gracefully in the future.
- To facilitate development, this app uses the `tidyverse` package, which is a wrapper for loading several related packages. In the future, this app could be made more lean by replacing the call to the `tidyverse` package with a call to specific packages within that wrapper package.

## Style Guide

The code in this repository follows the [Tidyverse Style Guide](http://style.tidyverse.org), with occasional additional guidelines (specifically, the use of `##` rather than `#` to delimit text comments) taken from the [Google R Style Guide](https://google.github.io/styleguide/Rguide.xml).
