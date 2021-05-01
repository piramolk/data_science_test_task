# Data Science: test task

This repository gives background info and contains test data for a common geospatial task in machine learning feature extraction.

The task is to combine features of different geospatial layers, and then later use the features to train machine learning models.

## Background

Consider the following use case with two datasets:

 1. A building dataset from open-street-map; and
 2. A property/parcel dataset from the UK government.

The building dateset contains the foot-print of buildings, as a geometry, and certain attributes like the name of the building and what type of building it is (residential, commercial, etc.). The property/parcel dataset contains the foot-print of the property, address, rates and taxes and the dedicated uses of the property (residential, commercial, light-industrial, heavy industrial, etc.)

In waste management, we would use these features to predict how much waste a property or building is producing. 

### Example data

Below is a map displaying the dataset, with the building dataset having red outlines, and the property/lot dataset filled-grey.

![Screenshot 2021-05-01 at 1 19 48 PM](https://user-images.githubusercontent.com/19407764/116771894-f3a24a80-aa7f-11eb-9924-fd8190e03ea6.png)

An interactive version of the map is here: https://kepler.gl/demo/map?mapUrl=https://dl.dropboxusercontent.com/s/taw4k9jh0azc4uj/keplergl_0b8x9pm.json (give it a few seconds to load). You can hover-over the different properties and buildings to see some of their attributes.

### Use-case

Each dataset contains potentially useful features to predict waste generation rates. For example, the following can be calculated from the buildings dataset:

 * building footprint (m$2$); 
 * number of floors;
 * total floor space (combination of the above two); and
 * building type.

The following can be calculated from the property/parcel dataset:

 * property footprint (m$2$);
 * rates and taxes ($); and
 * usage type.

Through the address of the property, we can link even more features from external datasets, such as how many companies are registered to the address, and types of companies, etc.

Further, our target/prediction variable, waste generation rate, is only available for one of the datasets: property/parcel.

If we only use the property/parcel dateset, our prediction model will not be very good. We can improve it by combining features from the building dataset.

## The problem 

The challenge, and task for this assignment, is to combine the features of the building dataset with the property/parcel dataset. For that purpose, we need to assign buildings to properties/parcels based on their geospatial relationship. There are quite a few corner cases which complicates the task.

### Corner cases

In the case below (bottom of the figure) we can see that the property/parcel contains three buildings. All three have to be assigned to the property/parcel.

![Screenshot 2021-05-01 at 1 14 31 PM](https://user-images.githubusercontent.com/19407764/116772010-fc931c00-aa7f-11eb-8e21-fae2b4298519.png)

In the case below, on the left, we can see that a single building runs across multiple parcels, so the building has to be assigned to all the parcels. Note that the building is not fully contained in the parcels.

![Screenshot 2021-05-01 at 1 14 37 PM](https://user-images.githubusercontent.com/19407764/116773442-fe60dd80-aa87-11eb-80ab-9c894d0834b9.png)

In the case below, we see that there is a one-to-one match between buildings and property/parcels, but that the geometries do not line-up perfectly. In this case, each building should only be assigned to the property/parcel with which it overlaps the most.

![Screenshot 2021-05-01 at 1 15 56 PM](https://user-images.githubusercontent.com/19407764/116772245-1fbdcb80-aa80-11eb-9a53-827bcbcfabf6.png)

## Assignment

There are many ways to geospatially assign buildings to properties/parcels. Each will have its advantages and disadvantages. Some will deal better with corner cases than others. The challenge is then to come up with a codable process of matching the buildings to the properties/parcels.

The task can be split in three:

 1. Conceptually describe and document how you would solve it, laying out the steps and calculations. You can chose one or two ways to do it, or combine different methods. Just logically evaluate and describe the benefits of the approach and where you think it will struggle with the corner cases.
 2. Implement the approach in python using geopandas (<https://geopandas.org/index.html>) as a function for reuse, and test it on the data in the `data/` folder in this repository. It contains the same data as used in the example. The most important column is the `geometry` column we will use to spatially merge/join the datasets:
   * `data/uk_land_registry_parcel_extract.geojson`: contains the property/parcel data
   * `data/uk_osm_building_extract.geojson`: contains the building data
 3. Share the results of the data as a python notebook

Feel free to share conceptual steps first for discussion. We can then pick which part is the best to implement. 

Further, in practice we will solve this problem iteratively by quickly trying an approach, even if we know it will have weaknesses, and then evaluating the results. We will then improve the approach, or come up with a new one for the instances in which the first approach failed.

In spirit of the above, don't try and develop a perfect approach. Rather try and quickly implement and test the approach for discussion, even if it will have weaknesses.

## Background info on why we do the above

Different datasets have different attributes. We have no control over how they got generated, and since there is so little data to work with in waste, we have to use what we have.  Also, the datasets are generated by different entities and according to different standards. Hence their discrepency.

If we use only one dataset, we will have very little data to work with and our analysis will be poorer. When we combine the datasets, we develop more and more accurate data on which we can develop our models.

For the task, we only have two datasets. In practice we combine many more. And we don't know in advance which ones are the best to combine.

Part of Waste Labs' success this far is our ability to combine these datasets to form our digital twins, which is an accurate representation of where waste is being generated and by who.
