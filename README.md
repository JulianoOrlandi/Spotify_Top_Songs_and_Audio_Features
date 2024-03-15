# Building a dataset with Spotify's Top Songs and their Audio Features

## **Description:**

This is the second section of a three-part project whose main objective is to practice data science skills and technologies.

The **first part** was an application that retrieves the [Weekly Top Songs Charts from Spotify Charts](https://charts.spotify.com/home) webpage. It was called **Spotify_Charts_Downloader_Bot** and it is available [here](https://github.com/JulianoOrlandi/Spotify_Charts_Downloader_Bot). After running that script, all the available Weekly Top Songs Charts from Spotify will be in a folder called `\charts`.

The **second part** is a script to create a dataset with information about the top songs from Spotify Charts. It combines the charts downloaded in the previous part with more data obtained through the [Spotify's API](https://developer.spotify.com/). At the end of this part, a **.csv** file with all the information combined is created.

The **third part** of the project will be an exploratory analysis of the data gathered in the **.csv** file. I have not yet decided what kind of analysis that will be. But probably it will explore the relations between the audio features of the songs and their popularity. The goal - speaking vaguely - will be to discover if features like key, mode or tempo have any influence on a song success.

The code is in a Jupyter Notebook file called [spotify_dataset_builder.ipynb](spotify_dataset_builder.ipynb). It is divided into 3 main parts and each one of them has also three divisions. Below there is an explanation about each segment goals and characteristics.

Besides the Jupyter Notebook, there is a python file called [creds.py](creds.py). It is used to store the credentials needed to access Spotify's API. These are personal informations and for this reason they are not available on the file. When the moment to discuss the API comes, there will be a more detailed explanation of how to obtain these credentials.

## **Part 1 - Analyzing and preparing the charts:**

### **1.1 - Importing packages and modules:**

*Libraries:*
- numpy               1.26.4
- pandas              2.2.0
- prettytable         3.10.0
- requests            2.31.0

*Modules:*
- os
- math
- creds


### **1.2 - Analyzing and cleaning the charts:**

The aim of this section is to verify the integrity of the charts stored in `/charts` folder.

**Firstly**, the code looks for generic problems in regards to each one of the files. It checks if the chart has 200 entries as it is expected and if there are no duplicated entries.

**Secondly**, the code looks for specific problems in regards to the columns such as missing, duplicated or inconsistent values.

**At the end**, if any problem was discovered, two tables are printed for further analysis.

The only problem founded is 15 charts that don't have the expected 200 rows. It was impossible to discover why they are missing. It may be an issue with Spotify's database or they may have been removed for any other reason. In any case, the number of missing rows is negligible and the rest of the information remains consistent. Threfore, the problematic charts rows are kept and used.

### **1.3 - Removing unwanted columns, joining the charts and organizing the dataframe:**

The goal in this chunk of code is to join all the charts into a single dataframe. It starts by removing unwanted information('rank', 'peak_rank' and 'previous_rank' columns). And then the charts are concatenated.

As the charts are published weekly, some tracks will have multiple entries on the dataframe. Because some songs remain for several weeks in the top charts. To deal with this problem, the code group all the entries by 'uri' column, leavinf just one for each track.

In regards to the rest of the columns, the code groups them in three different ways. For the three nominal features ('artist_names', 'track_name' and 'source'), it chooses the first value. For 'weeks_on_chart', it chooses the maximum value and, for the 'stream', it sums all the values. These otpions seemed more apropriated to differenciate songs that remains many weeks at the top charts from songs that make a huge success for a brief period of time.

## **Part 2 - Using Spotify's API to get the audio features of the tracks:**

### **2.1 - Getting access to Spotify's API service:**

To download information from Spotify's Web API, an application must have an access token. To get this token, it needs two information: 'client_id' and 'client_secret'. To get these informations, the application must be registerd in [Spotify For Developers](https://developer.spotify.com/) webpage. To register the application and to obtain the credentials, follow the 8 steps below:

1. Create an account on [https://developer.spotify.com/](https://developer.spotify.com/);
2. At the right top corner, open the menu and click on `Dashboard`;
3. Click on `Create app`;
4. Fill the `App name`, `App description` and `Redirect URI` fields (anything will work);
5. Check the `"I understand and agree with [...]"` box and click on `Save`;
6. Click on `Settings`;
7. Copy the `Client ID` code to its respective field in `creds.py`;
8. Click on `View client secret` and copy the code to its respective field in `creds.py`.

### **2.2 - Using the tracks ids to request information about the audio features of the tracks:**

The first task of this code is to retrieve the tracks' 'ids' from the merged_charts_df. The second task is to loop through the 'ids' and to request the data from Spotify's API. The information is stored in a list called 'responses'.

### **2.3 - Creating a dataframe to store the information from Spotify's API:**

The goal in this part is to create a new dataframe with the information about the audio features stored in the list 'responses'. This process is not performed inside the loop of the previous part because it is possible to have problems with the internet connection and/or the expiration of Spotify's API access token.

## **Part 3 - Joining the two dataframes (charts and audio features), organizing and creating the .csv file:**

### **3.1 - Joining the two dataframes (charts and audio features)**

Based on the 'id' column, the code here merges the dataframe with the charts' and the dataframe with the audio features.

### **3.2 - Organizing the dataframe: converting values, removing and rearranging columns:**

The **first** task here is to drop unwanted information ('type', 'uri', 'track_href' and 'analysis_url' columns). The **second** is to rearrange the columns observing the distinct nature of the data. The first four columns (id', 'artist_names', 'track_name' and 'source') contains nominal data. The following three ('key', 'mode' and 'time_signature') contains categorical data. And all the rest contains numerical data. The **third** part of the code converts the three categorical values ('key', 'mode' and 'time_signature') from numbers to strings. The goal is to underline that these features are categorical and there are no order between their values.

### **3.3 - Creating the .csv file with all the information:**

The last chunk of code simply creates a .csv file from the merged dataframe.