# Introduction
A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analytics team is particularly interested in understanding what songs users are listening to. Currently, they don't have an easy way to query their data, which resides in S3, in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

My aim in this project is to extract the json logs and have this data stored as .parquet files means they can be easily loaded into Hadoop for analysis whenever required.
This means that the .json files will not need to be reprocessed in order to make use of this data.

## Files
>- README.md -- is this file
>- etl.py -- the main ETL script that interacts with Spark to create the paraquet files
>- dl.cfg -- configuration file where AWS connection details need to be entered

## Setup
Set up server

*remember to create a python virtual environment to install python packages*

In order to run these Python scripts, you will first need to install Python 3 and Apache Spark on your server, and 
then install the following Python modules via pip: 

>pyspark - a Pyspark adapter for Python

To install these via pip you can run:

>pip install pyspark

### Testing

To test the etl.py script you can replace below variables :

> song_path = input_data + 'song_data/A/A/A/*.json'
> log_path = input_data +  'log_data/2018/11/*.json'

This reduces the number of files that need to be processed and will help with debugging as it takes long to process the 
whole S3 bucket.

#Database Purpose and Schema

##Purpose

1. Enable further ad-hoc analysis by using SQL language thought AWS Athena or AWS Redshift Spectrum
2. Structure the JSON data in parquet files making consumption easier and faster
3. Very good query and ETL performance because the data is not big enough
4. Build a powerful, flexible, and cost effective analytics platform by using Python, SparkSQL and AWS S3.

### Fact Table

>songplays: records in event data associated with song plays. This is extracted from both the song_data and log_data 
> JSON files: songplay_id, start_time, month, year, user_id, level, song_id, artist_id, session_id, location, user_agent

### Dimension Tables

>- users: users in the app, extracted from the log_data JSON files: user_id, first_name, last_name, gender, level
>- songs: songs in music database, extracted from the song_data JSON files: song_id, title, artist_id, year, duration
>- artists: artists in music database, extracted from the song_data JSON files: artist_id, name, location, latitude, longitude
>- time table: timestamps of records in songplays broken down into specific units,  extracted from the log_data 
   > JSON files: start_time, hour, day, week, month, year, weekday

### SparkSQL Temp Tables

I want to store the spark Dataframe as the table and query it. For this purpose, I used createOrReplaceTempView on spark 
Dataframe.

createOrReplaceTempView creates (or replaces if that view name already exists) an evaluated "view" that you can 
then use like a hive table in Spark SQL. It does not persist to memory unless you cache the dataset that underpins the 
view [1].

>- vw_songs_data: subset of real data from the Million Song Dataset.
>- vw_log_data: log files in JSON format generated that contains informations about actions done by users.