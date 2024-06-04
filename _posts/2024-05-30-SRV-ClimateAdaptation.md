---
layout: interactive_post
title: Climate Adaptation
date: 2024-05-30
description: Data
categories:
  - data
  - research
image: /images/satellite_view_senegal.png
author_staff_member: dualta
excerpt: Data visualisation of climate adaptation in rice producing Senegal River Valley.
---

## Climate adaptation in the Senegal River Valley
\\
Using deep learning methods, a model can be trained that learns historical patterns of biomass (in this case NDVI). This can be done in a number of different ways, for the results shown here we use a 2D CNN-LSTM architecture. In the newest model, we use a transformer instead, which performs slightly better. 
![Climate adaptation methodology](/images/climate_adaptation_schematic.png)
This interactive map shows the climate adaptation for the different rice growing seasons in the Senegal River Valley for the early irrigated season in 2020 (other values can be seen by hovering over each polygon). In this case red means a region is underperforming relative to the conditions 20 years ago, blue means it is overperforming (adapting). 
\

(Note background tiles are the latest Leaflet satellite imagery)


{% include 2022_climate_adaptation_srv_chunks.html %}

On a pixel level (MODIS: 250m resolution here) this is what the result looks like for feature1 above. This area is a part of a public-private partnership and has expanded rice production rapidly over the last 10 years. 

![Climate adaptation SRV pixels](/images/climate_adaptation_srv.png)