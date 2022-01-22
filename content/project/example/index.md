---
title: VASA (Visual Analytics for Spatial Associateion)
summary: A python tool to visualize spatio-temporal structure of the data
tags:
- Visual Analytics
date: "2021-01-15T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: "https://github.com/move-ucsb/VASA"

image:
  caption: Figure from the International Journal of GIS 
  focal_point: Smart

links:
- icon: twitter
  icon_pack: fab
  name: Follow
  url: https://twitter.com/EvgenyNoi
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---

The developed Python package, named VASA, will be accessible at https://github.com/move-ucsb/VASA. To demonstrate the applicability of the designed visualizations, the variability in spatiotemporal structure of human mobility patterns during the COVID-19 pandemic in the United States is assessed. VASA offers three novel multivariate visualizations: A stacked recency and consistency map, a line-path scatter plot, and a categorical strip (dot) plot. All three techniques use LISA as the base and utilize local Moran’s I and permuted p-values. The techniques are best suited for analysis of areal data at two levels of analysis: the object-level and the summary-level. The object-level of analysis receives the data at the finest available scale (e.g. county, census blocks, etc.), whereas the summary-level (e.g. state) refers to the less granular spatial units that contain object-level units. The stacked recency and consistency map allows to ascertain the spatiotemporal structure of data at both object- and summary-level. The categorical strip plot allows for comparison of trends at the summary-level. The line-path visualization is better suited for a fine-detail analysis of individual object-level trajectories within a specified summary-level.