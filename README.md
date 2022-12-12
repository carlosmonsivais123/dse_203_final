# Using Space to Define Opportunity
## By: James Ades, Daniel Hartley, Bhanu Muvva, Carlos Monsivais

### Project Goal
The goal of the project is to find relationships between crime, housing prices and Reddit posts in order to find the safest place to live in San Diego and visualize this in a spatial aspect with edges and nodes connecting the properties in the area.

### Data Sources
1. Census Data 
   * https://data.census.gov/
3. San Diego Police Department
    * This data consists of files that are about stops, race, reason for stop, result, and complaints. We joined these files together in the script below in order to do the analysis.
    *  https://data.sandiego.gov/datasets/police-ripa-stops/
    *  https://data.sandiego.gov/datasets/police-ripa-stop-result/
    *  https://data.sandiego.gov/datasets/police-ripa-stop-reason/
    * https://data.sandiego.gov/datasets/police-ripa-race/
4. Reddit
    * Data was scraped from [] Reddit using the sandiego subreddit for the years January 1, 2020, and January 1, 2021.
    * The following fields were scraped:
        * Score, author, full link, date, number of comments, title, subreddit location, number of subscribers, title, image url, upvote ratio.
    * Ended up scraping 18,709 posts.
    * https://www.reddit.com/r/sandiego/
5. Google Geocode API
    * This is a Google Cloud Service that we were using that is similar to their Google Maps. However we will be inputting partial address to get the exact latitude and longitude we will be using to plot the crimes.
    * https://developers.google.com/maps/documentation/geocoding/start
6. Housing Data
    * Used chrome plugin webscraper.io for scraping [] realtor.com, it works in browser, streamlines web scraping.
    * Set up to select each link on a page
    * Scrapped the first 50 pages of listings for San Diego ~2000 houses
    * We scraped the following fields:
        * Price
        * Address
        * Number of beds
        * Number of baths
        * Area property
    * https://www.realtor.com/realestateandhomes-search/San-Diego_CA/pg-1

7. Open Street Maps
    * These GeoJson Mapping Polygons are from the Open Street Map that we downloaded from Amazon, however we are only using the San Diego polygons since the project is 100GB.
    * The link to these polygons are here, just make sure you use the script below to query for San Diego and download it.
    * https://daylightmap.org/earth/index.html (points of interest) Must be queried with Amazon Athena
    * Create an instance here: https://us-west-2.console.aws.amazon.com/athena/home?region=us-west-2#/landing-page. See (https://github.com/johnymontana/daylight-earth-graph/blob/main/POI_import.ipynb)
    
8. GeoJson Mapping Polygons    
    * Download geojson: https://data.sandiego.gov/datasets/pd-neighborhoods/Only necessary to download 06 (California): https://github.com/arcee123/GIS_GEOJSON_CENSUS_TRACTS/blob/master/06.geojson 


### Project Technologies
* Google Cloud
    * Dataproc Clusters (Run Jupyter Notebooks): This is a centralized area where we ran our notebooks without worrying about the version of libraries.
    * Google Geocode API: This is an API to retrieve longitude and latitude data based on partial addresses.
    * Google Cloud Storage Buckets (Store Data): This is a centralized storage repository where we stored all of our data. This made data source updates easier to do.
* Neo4J Graph Database: We used the Neo4J graph databases to create the visualizations and node and edge creations.
  * NeoMap: Used this as a connector to visualize our graph database objects on a map.
* Python: Used Python to scrape data and for data manipulation.
* PostgreSQL: Used this to merge our datasets and for data manipulation.
* Amazon Athena: Ths is the Open Street Map that we downloaded from Amazon, however we are only using the San Diego polygons since the project is 100GB.

### Project Notebook Descriptions
1. add_coor.ipynb
   * Uses the Google Maps library to match the address column to lat/long coordinates then adds them as columns to the df. Unused columns are also dropped. 
2. clean_col.ipynb
   * Removes substrings from the bath and bed columns. It also removes special characters from the bed, bath and price columns. Handles instances where bed and price had string values outside of what was already dealt with. Finishes by setting the columns to a usable data type. 
3. crime_data_lon_lat.ipynb
   * This is the notebook where we merged our 5 crime data sources including Stops, race, reason for stop, result, complaints. After joining the data soources we cleaned the data by removing low level crimes such as jaywalking and then called the Google Geocode API to get the latitude and longitude for our address data we manipulated as part of the process.
4. Joining_census_data.ipynb
   * Drops unused columns, merges the 3 census track data sets and renames the columns to more manageable things.
   * Takes the three census datasets: Decennial, Race, and Income, and merges them and selects appropriate variables and converts to necessary geo identification to merge with geojson polygon tract info and then outputs to a new csv, which is used in project_203.txt to create relationships those tract info - tract polygon relationships. 
5. project_etl.ipynb
   * Takes the queried Amazon Athena OSM data and converts it into corresponding nodes and edges with identifying spatial coordinates and uploads into neo4j desktop (you must specify ip; user; password).
6. reddit_scrapper_and_features.ipynb
   * This notebook will extract data from Reddit from January 1, 2020 to January 1, 2021. After doing so it will send it to a GCS bucket. We can call in that input file and start to clean the Title column where we will do our sentiment analysis and also our text search matching. The sentiment analysis is done after the data is cleaned and will assign a score between 0 and -1 scoring every Title. Afterwards an NLP pipeline will be created using Spacy to extract any similar neighborhoods that we have as a reference between all the titles to find which posts were talking about specific neighborhoods linking the sentiment score to them.
7. project_203.rtf
   * Contains the code to query Amazon Athena for Open Street Map data and then filter San Diego only SD data, to load all geojson files of neighborhood and tract ids, to load census tract statistical information (previously merged from 3 datasets), unstructured subreddit data and to create necessary entities and relationships for these objects. It also contains queries used to visualize data on NeoMap: https://github.com/stellasia/neomap

### Cypher Queries
* The following files are queries that we used within the Neo4J to query our data as a graph database.
1. Create house nodes
2. Create crime_incident nodes 
3. shortestPath /euclidean distance
4. walking distance (between nodes with route relationship

### Presentation
* The following is the PowerPoint presentation saved as a PDF. The file Presentations_etl.pdf is part one and the DSE 203 Final Presentation.pdf is part two of the presentation.
1. presentation_etl.pdf
2. DSE 203 Final Presentation.pdf


### How to Run
1. In order to run the following project, we need to open up a Google Cloud Account since we will be using their services, however you get a free $300 credit when you open it up which is more than enough to run this project. Afterwards, you will enable the following services:
  * Dataproc, Google Geocode API, Google Cloud Storage Buckets
  * You need to obtain an API key from the Google Geocode API service.

2. Afterwards you will retrieve the data listed above and store them in a GCS Bucket.

3. Run the notebooks in the following order:
  * 1. project_203.rtf (Only Run the first two queries)
  * 2. project_203.rtf (After the data has been imported run the rest of the queries after the first two queries)
  * 3. clean_col.ipynb
  * 4. add_coor.ipynb
  * 5. Joining_census_data.ipynb
  * 6. crime_data_lon_lat.ipynb
  * 7. project_etl.ipynb
  * 8. reddit_scrapper_and_features.ipynb

4. Now we can run the Neo4J Queries in the order they are formatted in the text file. 

5. Examine/visualize relationships among housing, crime, and points of interest.
