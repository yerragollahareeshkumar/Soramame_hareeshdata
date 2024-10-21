# Installation Manual for AEROS Data

## 1. Prerequisites
- **Linux Operating System** (e.g., Ubuntu)
- **PostgreSQL** (Postgres Database)
- **PostGIS** (Extension for geographic objects)
- **Python 3.10 or above**
- **Google Maps API key** (for spatial location information)
- **Air Pollution Data** (CSV files containing sensor data)
- **Access to Soramame website to download air pollution data**

## 2.Setting up the database
1. Create a database in postgres to store the air pollution data
   ```sql
        Create database soramame
2. Connect to the database
   ```sql
        \c soramame
3. Enable PostGIS extension to the database (admin privileges are needed)
    ```sql
         Create extension postgis
4. Create a table to store the location information of the sensors.
    ```sql
          CREATE TABLE station_info(stationid varchar not null primary key, name varchar, address varchar, location geography(POINT,4326));
5.  Create a table to store the hourly observations of the sensors on a daily basis.
     ```sql
           create table hourly_observations(stationid varchar not null, obsDate timestamp, SO2 double precision, no double precision, no2 double precision, nox double precision, co double precision, ox double precision, nmhc double precision, ch4 double precision, thc double precision, spm double precision, pm25 double precision, sp double precision, wd varchar, ws double precision, temp double precision, hum double precision, constraint SOH unique (stationID,obsDate));
> **Note:** EDo not establish primary key and foreign key relation for stationid attribute in station_info and hourly_observations tables
> Constraint SOH unique (stationID,obsDate) is used to prevent data repetition. A sensor cannot have multiple transactions with the same timestamp.
## 3. Collect the data from AEROS website 
1. Visit the [AEROS Website](https://soramame.env.go.jp/station):
2. Collect the data of this columns (測定局 コード,
測定局 名称,住所....etc) Store a csv file.
## 4. Access the Server 
1. Open [Skytree server](http://163.143.165.141:8000/hub/login?next=%2Fhub%2F) Login with your credentials:
2. Create a Folder (e.g., Air Pollution Analytics)
3. Upload your CSV (eros data) file into Floder
4. Upload address2LatLong.py file into Floder
5. Open Terminal and execute the following commands
   ```sql
   python3 address2LatLong.py <input_csv_file> <output_csv_file> <google_maps_api_key>
6. Check the contents of the file using the `head()` and `tail` commands:
   ```sql
       head filename.txt
       tail filename.txt
## 5. Storing Station Info into the Database
1. Once finished, store the values into the database 
2. Open the Python program
3. Specify the database name, username, password, hostname, and check the port number (Go to the following line)
    ```sql
        conn = psycopg2.connect(database="soramame", user="temp", password="BunnyBittu@143", host="163.143.165.136", port=5432)
4. Execute the following command to store the station info CSV file into the 
5. database: 
   ```sql
        python3 insertStationDataIntoDatabase.py stationInfo.csv
## 6 Connect to Database
1. Read the contents of the station_info table using
    ```sql
   SELECT * FROM station_info;
## 7 Download the Air polluction data from Soramame Website 
1.Vist the Soramame Website (https://soramame.env.go.jp/download)
2. Select the period you want to download.
3. Select the measurement station for which you want to download (e.g., Nationwide)
4. After downloading the data, rename the zip file with the format YYYY-MM
5. Create a separate folder on the server named, for example, data
    ```sql
   mkdir data
6. Upload the zip files into the data folder
7. Unzip the files using the following command
    ```sql
   unzip filename.zip
8. Delete the zip files by executing the following command 
    ```sql
   rm -rf *.zip
## 8.Insert New Hourly Data into the Database
1. Open the insertNewHourlyDataIntoDatabase.py file
2. Check the database connection details: database name, username, password, hostname, and port number (Go to the following line)
    ```sql
   conn = psycopg2.connect(database="soramame", user="aeros", password="aeros123", host="163.143.165.136", port=5432)
3. Execute the following Python command
    ```sql
   python3 insertNewHourlyDataIntoDatabase.py ./data
4. Save the file and exit.



 
  



 



   
