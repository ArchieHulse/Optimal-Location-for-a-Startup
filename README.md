**Archie Hulse GeoData Project:**
*Project to show the optimal location for my new business to be located in order to grow.*

![image info](./Project_3/san-francisco.jpeg)

At **SurfTech**, the HR team has asked everyone in the company to submit their requests for what they think is most important to have near to the office:
    - Designers like to go to design talks and share knowledge. There must be some nearby companies that also do design.
    - 30% of the company staff have at least 1 child.
    - Developers like to be near successful tech startups that have raised at least 1 Million dollars.
    - Executives like Starbucks A LOT. Ensure there's a starbucks not too far.
    - Account managers need to travel a lot.
    - Everyone in the company is between 25 and 40, give them some place to go party.
    - The CEO is vegan and a Surfer.

Based on these criteria I divided them between distance and density.

**Distance**
 - Airport
 - Tech Startups (1 million raised)
 - Design Companies
 - Vegan restaurants and Surf shop locations
 - Nearest Bar
 - Taxi rank
 
**Density**
 - Schools
 - Starbucks


Firstly, data of global comapnies was obtained and inserted into Mongo. This was then broken down in python using a complex code to achieve a pandas dataframe that depicts the distribution of Tech Startups and Design Comapnies:

**TECH & DESIGN DATA FRAMES:**




The Tech Startups and Design Companies were then plotted onto separate empty maps of the globe in heat density format, in order to determin the distribution from the data:

**HEAT MAPS:**




From the data it can be infered that San Francisco and more specfically San Mateo was the location with the highest frequency of Tech Startups and that also has many Design Companies located near by.

**API DISTANCE & DENSITY:**
An API from foursquare.com provided the ability to search for nearest schools, taxi ranks, bars, Starbucks, aiport and others in San Mateo.
These API requests were imported to python and then converted to pandas dataframes, where they were then plotted onto my global map as markers.

The .csv files of the positional locations for all of the nearby Tech Startup and Design Companies were coverted back into dataframes and plotted as markers onto the map with corresponding colours and icons.

finally, the mean lattitude and longitude of locations that were cosidered in Distance (Airport, Tech Startups, Design Companies etc.) were calculated and triangulated from the data in order to pin point the optimal and middle point location of My company in San Mateo: 

** Basic Key:**
  - Tech Startups: purple
  - Design Companies: cadet blue with paint brush
  - My new business location (SurfTech): red with building icon.

**MAP:**







