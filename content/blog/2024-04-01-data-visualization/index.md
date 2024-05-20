---
author: Michalis Zouvelos
categories:
  - Python
  - R
  - Data Visualization
date: "2024-04-02"
slug: "effective-data-visualization"
draft: false
excerpt: "A glimpse over effective data visualization conventions and a simple approach with examples over this ever ending topic"
layout: blog-single
subtitle: "A glimpse over effective data visualization conventions and a simple approach with examples over this ever ending topic."
title: "Yet another data visualization best practices post" 
image: featured.png
math: true
---


Effective data visualization design and application is an ever-ending topic with numerous posts and resources written over the subject all the time.

Hence, I want to try and keep this post brief, since for any professional working in almost every possible Data role, they have most likely already encountered and read about these data visualization issues and aspects hunderds of times.

### Common Issues in Data Visualization

<div style="float: right; margin-left: 20px; width: 300px; max-width: 100%;">
    <img src="/img/2024-04-01-data-visualization/data_viz_meme.png" style="width: 100%; height: auto;" frameborder="0" allowfullscreen loading="lazy">
</div>

However, considering the above, ***it is impressive how common it still is, to observe constantly in various dashboards and analyses pitfalls like, cluttered and overcomplicated visuals, weird and unintuitive graphs, and the list goes on.***


This is a familiar problem, and the usual approach to tackle this in many organizations is, that they will reference you to either 10 different guides of "efficient data visualization" or recommend you to read books like the very classic "Storytelling with Data".. ye not surprising I can imagine. 

While I am not saying or even implying that all of these sources are bad, or that the aforementioned popular book is not worth it (I admit that I haven't read it), all of them while being good, they are likely to overcomplicate the subject, therefore, suddenly **you are faced with an abundance of information for a topic that should and is fairly straightforward.**

### Simplicity: Focus on Basic Graphs and Clarity

Let's face it, the average Data professional that has to analyze some data and create a handful of reports and dashboards, does not need to create a funky shankey diagram or one of the many food named charts like waffle, lollipop, doughnuts charts etc. Those are usually more fit to help with your appetite than with your data viz goals. ***All somebody really needs for 90% + of their daily use cases is, a linegraph, a barchart and potentially a groupped or stacked barchart to accomodate for groups.*** That's it, that should cover the visual aspect you use for almost anything, without having to think what would be the most groundbreaking way to present your data. While keeping always in mind, that these are also the graphs, that the receivers of your reports are most likely to be able to understand, without nodding their heads during a presentation while in reality they have no idea where to look to find the one piece of information or trend they are looking for.

Now, ***while the utilization of only these ~3 graphs for almost all cases is enough, they still need to be kept tidy with a focus of maintaining a clean and insightful style.*** This is where the "visualization conventions" come in to play. My recommendation would be, that instead of circling over a numerous of different sources and books, to simply visit a website like the Financial Times (FT), which I will use as an example for this post, and subsequently follow them on LinkedIn or whatever medium you use (*since also their website is paywalled*), to drive inspiration, use as a reference point and ultimately understand how your visuals should always look like. The truth is that the teams responsible for data visualization in organizations like the Financial Times really understand and know how to effectively communicate data, since their audience is not just a couple of business stakeholders, but millions of readers with different backgrounds, and all of them should be able to retrieve the message from each chart. Thus, with such an approach, in reality you do not even need to have "experience" in visualizing data, simply visit a recent FT article and replicate their visual format and style to your own linegraph or barchart.

<figure style="display: flex; flex-direction: column; align-items: center; margin: 10px;">
  <div style="display: flex; justify-content: center; align-items: center;">
    <img src="/img/2024-04-01-data-visualization/ft_linegraph_example.png" style="max-width: 50%; height: auto; margin: 10px;" loading="lazy">
    <img src="/img/2024-04-01-data-visualization/ft_barchart_example.png" style="max-width: 50%; height: auto; margin: 10px;" loading="lazy">
  </div>
  <figcaption style="text-align: center; width: 100%;">Example graphs - Images courtesy of the Financial Times</figcaption>
</figure>



### Creating the plots with Python and R 

Hence, this what I will try also in this article. ***Creating a FT styled graph with Python using [matplotlib](https://matplotlib.org/) and R using [ggplot2]()***, and show how easy it actually is to create insightful graphs. *Same approach would apply in any dashboarding tool, Tableau, Power BI, Looker etc.  We will use self-defined dummy dataframe(s) just to facilitate our examples.

Let's begin by first creating a linegraph using Python and the [matplotlib](https://matplotlib.org/) package!

``` python
# Load libraries
import pandas as pd
import matplotlib.pyplot as plt

# Create data frame
example_data = pd.DataFrame({
    'year': [2000, 2001, 2002, 2003, 2004, 2005, 2006, 2007, 2008, 2009, 
             2010, 2011, 2012, 2013, 2014, 2015],
    'employment_rate': [82, 80, 79, 77, 76, 76, 77, 77, 76, 73, 
                        74, 75, 75, 76, 77, 77]
})

# Custom labels for the x-axis
custom_labels = ["2000", "01", "02", "03", "04", "05", "06", "07", "08", "09", 
                 "10", "11", "12", "13", "14", "15"]

# Create the plot
fig, ax = plt.subplots(figsize=(10, 6))

# Plot the line chart
ax.plot(example_data['year'], example_data['employment_rate'], color="#2E5E88", linewidth=2.6)

# Add title and subtitle
fig.suptitle("Example of FT style Linechart", 
             fontsize=16, fontweight='bold', family='Times New Roman', color="#000000", x=0.1, ha='left')
fig.text(0.1, 0.92, "Share of XYZ (%),  2000-2015", 
         fontsize=12, family='Times New Roman', color="#333333")

# Add caption
fig.text(0.1, 0.02, "Source: Dummy Data \n© Your Logo", 
         fontsize=10, family='Times New Roman', color="#616161")

# Custom x-axis breaks and labels
ax.set_xticks(example_data['year'])
ax.set_xticklabels(custom_labels, color="#616161", fontsize=10)

# Define y-axis breaks and limits
ax.set_yticks(range(73, 84, 1))
ax.set_ylim(72, 83)
ax.tick_params(axis='y', right=True, left=False, labelleft=False, labelright=True, colors="#616161", length=0)  

# Add dashed grid lines
ax.grid(which='major', axis='y', color="#c4c4c4", linestyle='--', linewidth=0.7)  
ax.grid(which='major', axis='x', color='none')

# Set background color
fig.patch.set_facecolor('#FDF1E6')
ax.set_facecolor('#FDF1E6')

# Remove borders
for spine in ax.spines.values():
    spine.set_visible(False)

# Adjust plot margins
plt.subplots_adjust(left=0.1, right=0.9, top=0.85, bottom=0.15)

plt.show()
```

The above code returns the following output for the linechart (line is rough estimation of example defined with the dummy data as we do not have the monthly values):

<img src="/img/2024-04-01-data-visualization/python_line_chart_example.png" height = "500px" width = "100%" frameborder="0" allowfullscreen loading="lazy">


Now creating a barchart using R and [ggplot2](https://ggplot2.tidyverse.org/) as the library for data visualization!

``` r
# Load necessary library
library(ggplot2)
library(data.table)

# Create a dummy data.table 
example_data <- data.table(name = c("A bar", "B bar", "C bar", "D bar"), value = c(35, 28, 33, 23))

# Create the plot
ggplot(example_data, aes(x = name, y = value)) +
  # Create bar chart with no legend to remove unnecessary clutter and set color for bars
  geom_col(fill = "#2E5E88", show.legend = FALSE) + 
  # Add titles and captions
  labs( 
    title = "Example of FT style Barchart",
    subtitle = "Share of XYZ (%)",
    caption = "Source: Dummy Data \n© Your Logo"
  ) +
  # Define y-axis breaks and limits
  scale_y_continuous( 
    breaks = seq(0, 40, by = 10),  
    limits = c(0, 40) 
  ) +
  # Use a minimal theme for clean outlook and define the style settings
  theme_minimal(base_size = 14) +
  theme(
    # Title - Subtitle and Axis titles
    plot.title = element_text(family = "Times New Roman", hjust = -0.065, face = "bold", size = 16, color = "#000000"),
    plot.subtitle = element_text(family = "Times New Roman", color = "#333333", hjust = -0.05, size = 12, margin = margin(b = 20, unit = "pt")),
    axis.text.x = element_text(color = "#616161", angle = 0, vjust = 5, hjust = 0.5, size = 10),
    axis.text.y = element_text(color = "#616161"),
    axis.title.y = element_blank(),
    axis.title.x = element_blank(),
    # Set grid lines
    panel.grid.major.x = element_blank(),
    panel.grid.major.y = element_line(color = "#c4c4c4", linetype = "dashed", size = 0.4), 
    panel.grid.minor.x = element_blank(),
    panel.grid.minor.y = element_blank(),
    # Set background color
    panel.background = element_rect(fill = "#FDF1E6", color = NA), 
    plot.background = element_rect(fill = "#FDF1E6", color = NA),
    panel.border = element_blank(),
    # Set a margin for the whole graph
    plot.margin = unit(c(1, 1, 1, 1), "lines"),
    # Set the caption
    plot.caption = element_text(family = "Times New Roman", hjust = -0.05, vjust = 1, size = 10, color = "#616161", margin = margin(t = 10, unit = "pt"))
  ) 

```

The above code returns the following result for the barchart:

<img src="/img/2024-04-01-data-visualization/r_barchart_example.png" height = "500px" width = "100%" frameborder="0" allowfullscreen loading="lazy">

Having these code chunks as baselines and saving the themes in some variables etc. you can always quickly recreate the graphs.

You can add additional elements like we saw in the example images like shades or annotations to highlight different parts of a graph to communicate extra information with the reader but always make sure not to overdo it!

In this post I showcased, the creation of FT based graphs that anyone can adapt to their own organisation font, palette and style to effectively communicate data through the foundational charts like the linechart and the barchart. 

In essence what is highlighted is the importance of ***keeping visualizations simple, uncluttered and easy to understand. The main components of achieving this result is, using clear labels and scales, avoiding excessive colors, gridlines or other distracting elements and providing only the necessary context like time periods and units of measurement.***

-------------------------------------------------------------------------------------------------------------------

If you want to get an idea of how you can communicate your data using also other components like **tables**, then you can visit an older post of mine on this website, focusing on how to upgrade table data reports in an efficient and beautiful way, which you can find by clicking [here!](https://mzouvelos.github.io/blog/insightful-table-data-reports/)
