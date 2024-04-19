---
author: Michalis Zouvelos
categories:
  - R
  - Data Visualization
  - Shiny
  - Dashboard
date: "2022-06-10"
slug: "shiny-dashboards"
draft: false
excerpt: "Utilizing Spark cluster to load the biodiversity database of GBIF and using web scrapping to directly import the animal pictures with javascript. Wrapped in a Shiny Dashboard template."
layout: blog-single
subtitle: "Utilizing Spark cluster to load the biodiversity database of GBIF and using web scrapping to directly import the animal pictures with javascript. Wrapped in a Shiny Dashboard template."
title: "Showcasing biodiversity through an interactive Shiny Dashboard app" 
image: featured.png
math: true
---

The Biodiversity Dashboard is a Shiny Application that utilizes data from the **[GBIF organization](https://www.gbif.org/what-is-gbif)** to provide an interactive exploration tool for the biodiversity of any area, showcasing Poland as an example for this dashboard. Hosted at the following **[shinyapps link](https://michalis-zouvelos.shinyapps.io/Poland_Biodiversity/)** which you can visit to explore the dashboard yourself, the application offers users the ability to search for species by their scientific or vernacular names and discover detailed observations on a map, including species information and their photo, a timeline graph of observations and a bar chart detailing yearly observations.

### How It Works

The dashboard allows users to select a species and displays observations on a map, a bar chart with total yearly observations, and a timeline graph for daily observations. Each observation on the map comes with a popup label window, providing detailed information about the observation. This interactive feature is achieved through R cose with the use of R packages like leaflet for mapping layout, rvest for web scraping, sparklyr for a Spark connection and highcharter for the interactive graphs.

Shiny supports both Python and R code to easily create beautiful, informative and functional applications and dashboards. You can check more information about Shiny here on the links for **[Python Shiny](https://shiny.posit.co/py/)** and **[R Shiny](https://www.rstudio.com/products/shiny/)**.

### Data Processing and Visualization

One of the project's workarounds is the integration of a **Spark cluster database connection for data loading**. Due to the high volume nature of the global biodiversity dataset, Spark clusters facilitated efficient data handling, extracting relevant observations for Poland into a manageable csv file. This approach underscores the importance of scalable data processing techniques in handling large datasets in a quick and efficient manner with no high complexity required.

After loading the database with Spark (using the sparklyr library) then you can process the data and write the subset of data you need from the database to use in the app.

```r
Sys.setenv(SPARK_HOME="C:/spark")
# Configure cluster (c3.4xlarge 30G 16core 320disk)

#### Spark Configuration ####
conf <- spark_config()
conf$'sparklyr.shell.executor-memory' <- "7g"
conf$'sparklyr.shell.driver-memory' <- "7g"
conf$spark.executor.cores <- 20
conf$spark.executor.memory <- "7G"
conf$spark.yarn.am.cores  <- 20
conf$spark.yarn.am.memory <- "7G"
conf$spark.executor.instances <- 20
conf$spark.dynamicAllocation.enabled <- "false"
conf$maximizeResourceAllocation <- "true"
conf$spark.default.parallelism <- 32

#  Establishing connection to spark
sc <- spark_connect(master = "local", config = conf)   
occurence_tbl <- spark_read_csv(sc, name="occurence", path="occurence.csv", header = TRUE, 
                                    memory = FALSE, overwrite = TRUE) 
```

Moreover, the project employs the rvest package for **web scraping**, gathering images from observation reports to enrich the dashboard's informational content. This integration of images directly into the popup labels on the map enhances user interaction, providing a more immersive experience.

Just by reading the whole html page and setting the xpath of the html node by using any dev tool addin you are able to scrape the data.
```r
# Check if web scraping is allowed from the target website
paths_allowed("https://observation.org")

# Scrape the images of the species, fill with NA if no image is provided on the observation
poland$img  <- sapply(poland$occurrenceID, function(x) {
  tryCatch({
    x %>%
      read_html() %>% 
      html_node(xpath = '//*[contains(concat( " ", @class, " " ), concat( " ", "app-ratio-box-image", " " ))]') %>%
      html_attr('src')
  }, error = function(e) NA)
})
```

### Interactive Elements and User Experience

A pivotal aspect of the dashboard is its focus on user interaction and engagement. The implementation of **[highchart interactive charts](https://www.highcharts.com/blog/tutorials/highcharts-for-r-users/)** adds a dynamic layer to the data presentation, allowing users to visually explore the data in depth. Furthermore, the app initiates with a welcoming pop-up message, guiding users on navigating the dashboard and making the most out of their exploration.

You can integrate a pop-up message in your Shiny app easily by defining the modal and providing html tags     
```r
# user message - app initial popup
message = function(message) {
  showModal(modalDialog(title = "Welcome to Poland Biodiversity map!",                                      # Opening message
                        tags$p("On this page, you can explore different species that exist in Poland."),
                        HTML('<img src="https://upload.wikimedia.org/wikipedia/commons/1/1e/GBIF-2015-full-stacked.png">'),
                        tags$p(HTML("<b>App info - How to use</b>")),
                        tags$p(HTML("The app allows the user to type or select a species by their name (either scientific or vernacular) and displays: <br>
                               <li> All of the reported observations with their location on the map</li>
                               <li> A bar chart with the total yearly observations of the selected species</li>
                               <li> A timeline graph of the number of recorded observations for each day they occured, with the exact date of the observations</li>
                               <li> Extended information regarding each observation with a popup label window when the user selects one of the pinned observations on the map
                               timeline graph of the number of recorded observations for each day they occurred, displaying the exact date of the observations.</li>")),
                        tags$p(HTML("<em>We have selected a random species for you to get you started.</em>")),
                        tags$p(HTML("<b>Please select the species you want to explore by using the filter on the left side of your screen!</b>")),
                        footer = modalButton("Start Exploring!"))
  )
}
```

Then the popup looks already clean as follows:

<img src="/img/2022-06-10-shiny-biodiversity-poland/popup_window.png" loading="lazy">

The decision to host the app on shinyapps.io is an easy and free solution to showcase an app. shinyapps.io offers a platform where projects, can be shared widely without the need for users to run complex setups locally. Of course the free version comes with limitations but it is still a niche solution to showcase some simple projects like the one displayed on this blogpost.

### Wrapping up

The development of a simple niche application with few lines of code is always an enjoyable and creative way to use some common Data Science practices like data processing with database connectors like Spark, employing web scraping and utilizing different libraries to provide a beautiful and seamless front-end for informative exploration. You can view the whole code used for the project in my GitHub page following this [link](https://github.com/mzouvelos/Biodiversity_Shiny_Application/tree/main) and visit the app by clicking **[here](https://michalis-zouvelos.shinyapps.io/Poland_Biodiversity/)** or directly on the image below.

**Image preview of the app:**

<a href="https://michalis-zouvelos.shinyapps.io/Poland_Biodiversity/" target="_blank" rel="noopener noreferrer">
    <img src="/img/2022-06-10-shiny-biodiversity-poland/preview.png" alt="Shiny App Preview" loading="lazy">
</a>

If you are interested to dive in into more dashboarding with shiny for Python or R there are many more examples, simple and more complex. You can find some in the official galleries [R shiny gallery link](https://shiny.posit.co/r/gallery/) and [Python shiny gallery link](https://shiny.posit.co/py/gallery/).
