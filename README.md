# Using Space to Define Opportunity
## By: James Ades, Daniel Hartley, Bhanu Muvva, Carlos Monsivais

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
    * This is a Google Cloud Service that we were using that is similar to their Google Maps. However we will be inputting partial addresess to get the exact latitude and longitude we will be using to plot the crimes.
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
7. GeoJson Mapping Polygons
    * These GeoJson Mapping Polygons are from the Open Street Map that we downloaded from Amazon, however we are only using the San Diego polygons since the project is 100GB.
    * The link to these polygons are here, just make sure you use the script below to query for San Diego and download it.
    * https://daylightmap.org/earth/index.html (points of interest) Must be queried with Amazon Athen
    * Create an instance here: https://us-west-2.console.aws.amazon.com/athena/home?region=us-west-2#/landing-page. See (https://github.com/johnymontana/daylight-earth-graph/blob/main/POI_import.ipynb)
    * Download geojson: https://data.sandiego.gov/datasets/pd-neighborhoods/Only necessary to download 06 (California): https://github.com/arcee123/GIS_GEOJSON_CENSUS_TRACTS/blob/master/06.geojson 


### Project Technologies
* Google Cloud
    * Dataproc Clusters (Run Jupyter Notebooks): This is a centralized area where we ran our notebooks without worring about the version of libraries.
    * Google Geocode API: This is an API to retrieve longitude and latitude data baed on partial addresses.
    * Google Cloud Storage Buckets (Store Data): This is a centralized storage repository where we stored all of our data. This made data source updates easier to do.
* Neo4J Graph Database: We used the Neo4J graph databases to create the visualizations and node and edge creations.
* Python: Used Python to scrape data and for data manipulation.
* PostgreSQL: Used this to merge our datasets and for data manipulation.

### Project Notebook Descriptions
1. add_coor.ipynb
   * Uses the Googlemaps library to match the address column to lat/long coordinates then adds them as columns to the df. Unused columns are also dropped. 
2. clean_col.ipynb
   * Removes substrings from the bath and bed columns. It also removes special characters from the bed, bath and price columns. Handles instances where bed and price had string values outside of what was already dealt with. Finishes by setting the columns to a usable data type. 
3. crime_data_lon_lat.ipynb
   * This is the notebook where we merged our 5 crime data sources including Stops, race, reason for stop, result, complaints. After joining the data soources we cleaned the data by removing low level crimes such as jaywalking and then called the Google Geocode API to get the latitude and longitude for our address data we manipulated as part of the process.
4. Joining_census_data.ipynb
   * Drops unused columns, merges the 3 census trac datasets and renames the columns to more manageable things.
   * Takes the three census datasets: Decennial, Race, and Income, and merges them and selects appropriate variables and converts to necessary geo identification to merge with geojson polygon tract info and then outputs to a new csv, which is used in project_203.txt to create relationships those tract info - tract polygon relationships. 
5. project_etl.ipynb
   * Takes the queried Amazon Athena OSM data and converts it into corresponding nodes and edges with identifying spatial coordinates and uploads into neo4j desktop (you must specify ip; user; password).
6. reddit_scrapper_and_features.ipynb
   * This notebook will extract data from Reddit from January 1, 2020 to January 1, 2021. After doing so it will send it to a GCS bucket. We can call in that input file and start to clean the Title column where we will do our sentiment analysis and also our text search matching. The sentiment analysis is done after the data is cleaned and will assign a score betweeen 0 and -1 scoring every Title. Afterwards an NLP pipeline will be created using Spacy to extract any similar neighborhoods that we have as a reference between all the titles to find which posts were talking about specific neighborhoods linking the setniment score to them.
7. project_203.rtf
   * Contains the code to query Amazon Athena for Open Street Map data and then filter San Diego only SD data, to load all geojson files of neighborhood and tract ids, to load census tract statistical information (previously merged from 3 datasets), unstructured subreddit data and to create necessary entities and relationships for these objects. It also contains queries used to visualize data on NeoMap: https://github.com/stellasia/neomap

### Cypher Queries
* The following files are queries that we used within the Neo4J to query our data as a graph database.
1. Create house nodes
2. Create crime_incident nodes 
3. shortestPath /euclidean distance
4. walking distance (between nodes with route relationship

### Presentation
* The folloeing is the PowerPoint presentation saved as a PDF.
1. DSE 203 Final Presentation.pdf

### How to Run
* In order to run the following project, we need to open up a Google Coud Account since we will be using their services, however you get a free $300 credit when you open it up which is more than enough to run this project. Affterwards you will enable the following services:
  * Dataproc, Google Geocode API, Google Cloud Storage Buckets
  * You need to obtain an API key from the Google Geocode API service.

* Afterwards you will retrieve the data listed above and 
