# Archie-Hulse-GeoData-Project
Project to show the optimal location for a new business to be located in order to develop.

**GeoSpatial Data:**
- To show the optimal location for a new business to grow based on the set parameters of:
    - Designers like to go to design talks and share knowledge. There must be some nearby companies that also do design.
    - 30% of the company staff have at least 1 child.
    - Developers like to be near successful tech startups that have raised at least 1 Million dollars.
    - Executives like Starbucks A LOT. Ensure there's a starbucks not too far.
    - Account managers need to travel a lot.
    - Everyone in the company is between 25 and 40, give them some place to go party.
    - The CEO is vegan.

- Firstly, data of global comapnies was obtained and inserted into Mongo. This was then broken down in python using a complex code to achieve a pandas dataframe:

- This was then plotted onto a empty map of the globe in heat format in order to determin the distribution of tech startups gloablly from the data, that had alo raised 1 million USD already.

- From the data it could be infered that San Francisco and more specfically San Mateo was the location with the highest frequency of said startups.

- This process was repeated, tapping into the same dataset but this time filtering for Design businesses globally. Since My business has employees that want to be located near to Design and Tech Startup buesinesses. As shown in the two maps below San Mateo has good coverage for both Tech Startups and Design Companies:





- These data set were then saved in .csv files and used for the final map presentation.

- An API from foursquare.com provided the ability to search for nearest schools, taxi ranks, bars, Starbucks, aiport and others in San Mateo.
- These API were imported to python and then converted to pandas dataframes, where they were then plotted onto my global map as markers.

- The .csv files of the positional locations for all of the nearby Tech Startup and Design Companies were coverted back into dataframes and plotted as markers onto the map with corresponding colours and icons.

- finally, the mean lattitude and longitude of all of these parameter locations was calculated from the data in order to pin point the oprimal location of My company in San Mateo:




