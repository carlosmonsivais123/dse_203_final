# Using Space to Define Opportunity
## By: James Ades, Daniel Hartley, Bhanu Muvva, Carlos Monsivais

### Data Sources
1. Census Data 
2. San Diego Police Department
    * This data consists of files that are about stops, race, reason for stop, result, and complaints. We joined these files together in the script below in order to do the analysis.
3. Reddit
    * Data was scraped from [] Reddit using the sandiego subreddit for the years January 1, 2020, and January 1, 2021.
    * The following fields were scraped:
        * Score, author, full link, date, number of comments, title, subreddit location, number of subscribers, title, image url, upvote ratio.
    * Ended up scraping 18,709 posts.
4. Google Geocode API
    * This is a Google Cloud Service that we were using that is similar to their Google Maps. However we will be inputting partial addresess to get the exact latitude and longitude we will be using to plot the crimes.
5. Housing Data
    * Used chrome plugin webscraper.io for scraping [] realtor.com, it works in browser, streamlines web scraping.
    * Set up to select each link on a page
    * Scrapped the first 50 pages of listings for San Diego ~2000 houses
    * We scraped the following fields:
        * Price
        * Address
        * Number of beds
        * Number of baths
        * Area property
6. GeoJson Mapping Polygons
    * These GeoJson Mapping Polygons are from the Open Street Map that we downloaded from Amazon, however we are only using the San Diego polygons since the project is 100GB.
    * The link to these polygons are here, just make sure you use the script below to query for San Diego and download it.

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
3. clean_col.ipynb
   * Removes substrings from the bath and bed columns. It also removes special characters from the bed, bath and price columns. Handles instances where bed and price had string values outside of what was already dealt with. Finishes by setting the columns to a usable data type. 
5. crime_data_lon_lat.ipynb
6. Joining_census_data.ipynb
   * Drops unused columns, merges the 3 census trac datasets and renames the columns to more manageable things.
   * Takes the three census datasets: Decennial, Race, and Income, and merges them and selects appropriate variables and converts to necessary geo identification to merge with geojson polygon tract info and then outputs to a new csv, which is used in project_203.txt to create relationships those tract info - tract polygon relationships. 
8. neo4j_notebook.ipynb
9. project_etl.ipynb
   * Takes the queried Amazon Athena OSM data and converts it into corresponding nodes and edges with identifying spatial coordinates and uploads into neo4j desktop (you must specify ip; user; password).
11. reddit_scrapper_and_features.ipynb

### Cypher Queries


### Neo4J Queries
* The following files are queries that we used within the Neo4J to query our data as a graph database.
