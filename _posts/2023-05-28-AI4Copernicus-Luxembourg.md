---
title: AI4Copernicus | EO & AI Solutions for Climate Change Challenges
date: 2023-5-28
description:
categories:
  - conferences
  - workshops
image: /images/srv_rgb.png
author_staff_member: dualta
---


## Identifying Rice Cropping Practices in the Senegal River Valley Post-Intervention using Copernicus

This week the team attended the AI4Copernicus meeting in Mondorf-les-Bains in Luxembourg. Many leading organisations in orbital imagery were present, giving updates on the current impactful use cases for the latest suite of satellites. 

The team presented research using Sentinel-1 and Sentinel-2 to map out cropping regions in the Senegal River Valley. Using time-series to image transformations applied in a novel way to this satellite data, we were not only able to map the extent of rice cropping in this area, but also the cropping intensity for each year. 
This gives decision and policy makers much more nuanced information on the impact that current strategies are having on crop production levels.

We used infrared and optical imagery from Sentinel-2 to create a preliminary crop mask, which is then used to train a random forest model which distinguishes cropped rice from other land use types. This rice mask then provides the basis for data extraction of time-series data from Sentinel-1 (VH) and Sentinel-2 (NDVI, NDYI), which are strong indicators of the phenology of the rice growth cycle. Gramian Angular Fields (GAF) were applied to the time-series to produce 3-channel imagery which can be used to classify the calendar year into single crop, double crop or fallow years of rice growing. 

This type of analysis gives an indepth view of rice production trends in a region which is increasingly reliant on consistent rice yields, particularly given the slow-down in global rice exports. Senegal still aims to be rice self-sufficient in the future, but as yet to achieve that goal. 

The poster presented at the conference can be viewed below.

![](/images/AI4CopernicusPoster.pdf "AI4Copernicus Poster")
*The poster presented at AI4Copernicus*

<!-- ![](/images/srv_rgb.png "AI4Copernicus Poster") -->
