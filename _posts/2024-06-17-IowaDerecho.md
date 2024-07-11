---
layout: interactive_post
title: "Assessing Disaster Impact Rapidly Using SAR"
date: 2024-06-10
description: Crop failure from wind storm (derecho) in Iowa
categories:
  - analysis
  - research
image: /images/sar_images/iowa_sar_vv_image.png
author_staff_member: dualta
excerpt: Methods to assess the impact of wind storms on lodging in maize in Iowa (2020)
---

## Derecho wind storm 
In August 2020, central Iowa experienced a [Derecho](https://en.wikipedia.org/wiki/August_2020_Midwest_derecho), a straight-line wind storm with hurricane speed winds. This particular storm reached wind speeds of 203 km/h. As a result there was widespread destruction of property and NOAA estimated the total damage to be in the range of US$11 billion. For agriculture, there was particular damage done to corn and soybean crops. Corn is particularly susceptible due to its sie and shape, and can be [lodged](https://en.wikipedia.org/wiki/Lodging_(agriculture)), where the crop is permanently bent over sideways and the crop is usually lost.

Remotely sensed radar data is particularly useful in detecting this lodging. The vertical crop canopy will produce a much different backscatter signal compared to a flattened crop. 

This event has been investigated a number of times, more in depth than I present here, with published results [1,2]. In this blog post I want to highlight the ease and speed SAR data can be used to assess damage extent in this scenario. 

### Code
To accomplish this we will use geemap and Google Earth Engine in python.

```python
    import ee
    import geemap
```
<br>

Define out time periods of interest:
```python
    time_periods = {
      'July': ('2020-07-01', '2020-07-31', '2019-07-01', '2019-07-31'),
      'August': ('2020-08-01', '2020-08-31', '2019-08-01', '2019-08-31')
    }
```
<br>
Get the iowa state boundaries:
```python
    states = ee.FeatureCollection('TIGER/2018/States')
    iowa = states.filter(ee.Filter.eq('NAME', 'Iowa')).geometry()
```
<br>
Using Sentinel 1 and the ESA World Cover mask we can get the SAR backscatter for regions which are agricultural. Getting the median per month (July and August) per year (2020 and 2019) we can then find the differences in backscatter between the years before and after the storm occured.

```python
    monthly_median_images_2024 = {}
    monthly_median_images_2023 = {}

    worldcover = ee.Image('ESA/WorldCover/v200/2021')
    agriculture_mask = worldcover.eq(40).clip(iowa)  # 40 is the class for cropland

    def apply_speckle_filter(image):
        return image.focal_median(kernelType='circle', radius=500, units='meters')

    for month, (start_date_2024, end_date_2024, start_date_2023, end_date_2023) in time_periods.items():
        # Load the Sentinel-1 ImageCollection for 2024
        sentinel1_2024 = ee.ImageCollection('COPERNICUS/S1_GRD') \
            .filterBounds(iowa) \
            .filterDate(start_date_2024, end_date_2024) \
            .filter(ee.Filter.eq('instrumentMode', 'IW')) \
            .filter(ee.Filter.eq('orbitProperties_pass', 'ASCENDING')) \
            .select('VH') \
            .map(lambda image: apply_speckle_filter(image).updateMask(agriculture_mask).clip(iowa))

        # Calculate the median image for 2024
        median_s1_2024 = sentinel1_2024.median()
        monthly_median_images_2024[month] = median_s1_2024

        # Load the Sentinel-1 ImageCollection for 2023
        sentinel1_2023 = ee.ImageCollection('COPERNICUS/S1_GRD') \
            .filterBounds(iowa) \
            .filterDate(start_date_2023, end_date_2023) \
            .filter(ee.Filter.eq('instrumentMode', 'IW')) \
            .filter(ee.Filter.eq('orbitProperties_pass', 'ASCENDING')) \
            .select('VH') \
            .map(lambda image: apply_speckle_filter(image).updateMask(agriculture_mask).clip(iowa))

        # Calculate the median image for 2023
        median_s1_2023 = sentinel1_2023.median()
        monthly_median_images_2023[month] = median_s1_2023

    difference_maps = {}

    for month in time_periods.keys():
        difference = monthly_median_images_2024[month].subtract(monthly_median_images_2023[month])
        difference_maps[month] = difference
```
<br>

Then using `geemap` we can plot the results interactively:
```python
    m = geemap.Map(height='1000px', add_google_map=False)
    m.setCenter(lon=-93.3015, lat=41, zoom=8)

    # Define visualization parameters
    vis_params_diff = {
        'min': -5,
        'max': 5,
        'palette': ['red', 'white', 'blue']
    }

    vis_params_agriculture = {
        'min': 0,
        'max': 1,
        'palette': ['FFFFFF', '00FF00'],  # Black for non-agriculture, green for agriculture
        'opacity': 0.4
    }

    m.addLayer(agriculture_mask, vis_params_agriculture, 'Agriculture Mask')

    # Add each difference map to the map
    for month, difference_image in difference_maps.items():
        m.addLayer(difference_image, vis_params_diff, f'Difference {month}')

    # Add a layer control panel to the map
    m.addLayerControl()

    # m.add_colorbar(vis_params_diff, discrete=False, label='SAR Differences VH')
    m.add_colorbar_branca(colors=['red', 'white', 'blue'], vmin=-5, vmax=5, caption='SAR Differences VH', orientation='horizontal')

    m
```

### Output
It is clear from our map the regions that have been badly damaged from the derecho. Flipping between the July differences and August differences shows a stark contrast between the before and after effects.

{% include iowa_derecho.html %}

<br>

###### 1 - Hosseini, M et al. 2020. "Evaluating the Impact of the 2020 Iowa Derecho on Corn and Soybean Fields Using Synthetic Aperture Radar" Remote Sensing 12, no. 23: 3878. https://doi.org/10.3390/rs12233878

###### 2 - Ajadi, O. A. et al. 2020. "Landscape-Scale Crop Lodging Assessment across Iowa and Illinois Using Synthetic Aperture Radar (SAR) Images" Remote Sensing 12, no. 23: 3885. https://doi.org/10.3390/rs12233885


