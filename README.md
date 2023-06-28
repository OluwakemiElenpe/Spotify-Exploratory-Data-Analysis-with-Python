# Exploratory Data Analysis with Python

## Introduction
This is an exploratory data analysis of Spotify dataset. The dataset comprises of songs published on the platform from 1922 to 2021. The two datasets used for the demo are gotten from kaggle.com and are classified as [Spotify tracks](https://www.kaggle.com/datasets/zaheenhamidani/ultimate-spotify-tracks-db) (features of tracks) and [Soptify datasets](https://www.kaggle.com/datasets/lehaknarnauli/spotify-datasets) (classification by Genre).

Spotify is an audio streaming and media service provider founded in 2006 by Daniel Ek and Martin Lorentzon. It has over 433 million monthly active users, including 188 million subscribers as of June 2022 with total revenue of 9.668 billion euros and total asset of 7.170 billion euros. As of June 2022, Spotify has 9,058 total employees with the total number of downloads exceeding 1 billion in May, 2021 and currently the official sponsors of FC Barcelona of Spain (both male and female football team). Millions of people listen to music worldwide, it’s valuable to explore and quantify data about music and drawing important insight.

## Scope and Limitation

Only the exploratory data analysis was done. It is assume that reader have basic python programming knowledge with include: the basic Python operations, NumPy, Pandas, Matplotlib, and Seaborn. Jupyter notebook was used for this project.

### Exploring the data

![](Spotify_dataset.JPG)

Numpy, Pandas, matplotlib.pyplot and seaborn are the necessary libraries for this demo. I import and load the dataset as shown below
spotify_tracks = pd.read_csv('C:\\Users\\Admi\\Documents\\My Documents\\tracks.csv')
spotify_tracks.head()

The data set was saved with variable spotify_tracks and .head command displayed the first five rows of the dataset, if an integer is passed in the function, say 10, the first ten rows will be printed.

![](Head.JPG)

Since the dataset was downloaded from an open repository, there are chances the dataset contain null values it is a good practice to always check null values in dataset. The pandas library made work easy for us by using the .isnull() function on the dataset. The .sum() function calculate the total sum of null values in each colunms.

![](isnull.JPG)

The .info() methods gives us the total number of rows and columns in the dataset and the memory ussage.

![](info.JPG)

Here comes the first analysis, finding the top most popular songs in spotify from 1922 to 2021. This can be achieved using the .sort_values() methods, passing passing popularity and setting ascending to be false will define the sorting. Top least popular songs can be achieved using same code by changing ascending=True
sorted_df = df_tracks.sort_values("popularity", ascending = False).head(3)
sorted_df
the .head methods narrows the sorting top 3

![](popularity.JPG)

The .describe() methods gives the descriptive statistics of the data which include the count, mean, standard deciation, 50th percetile (median) etc

![](describe.JPG)

To check the songs with more than 90 popularity we use the .query function from pandas and pass ‘popularity >90’, inplace=False (so the the original data frame will be intact) and again using the .sort_values we used earlier. We used the square bracket to slice the first 3 songs with popularity  >90
most_popular = df_tracks.query('popularity>95', inplace=False).sort_values('popularity', ascending = False) 
most_popular[:5]

![](most_popular.JPG)

We wish to change the index of the dataset, instead of numberig we want the release date to be the index. This will require us to change the frame of our dataset as such we set inplace=True and we go ahead to print the head (top 5) of the data
spotify_tracks.index=pd.to_datetime(df_tracks.index)
spotify_tracks.head()


To locate the 18th artist and the popularity in the data we use the .iloc function, this will display the name and the popularity of the artist in the 18th position (NB: Numbering starts with 0 in programming)

![](ms_to_s.JPG)

The duration of the tracks are all in milliseconds, we now convert the unit to seconds for readability and easy understanding.
df_tracks['duration'] = df_tracks['duration_ms'].apply(lambda x: round(x/1000))
df_tracks.drop('duration_ms', inplace= True, axis = 1)
df_tracks.duration.head()
We used the lambda function to convert the time in milliseconds to seconds by dividing by 1000 and replace the “duration_ms” column to “duration” then we print the top 5 entries in the “duration” column.


Our first visualization is the correlation map heat map, we use the pearson corelation to draw three unmounted columns between key mode and explicit setting the figure size at (14,6) we set the title to Spotify Correlation HeatMap and the roration to 90 degrees
corr_df = df_tracks.drop(['key', 'mode', 'explicit'], axis = 1).corr(method="pearson")
plt.figure(figsize=(14,6))
heatmap = sns.heatmap(corr_df, annot=True, fmt=".1g", vmin=-1, vmax=1, center=0, cmap='inferno', linewidths = 1, linecolor = "Black")
heatmap.set_title("Spotify Correlation HeatMap ")
heatmap.set_xticklabels(heatmap.get_xticklabels(), rotation = 90)


The scale was set from -1 to +1 (from a negative or least correlation to positive or high correlation at +1) there is a positive correlation between energy and loundness of songs with the value of 0.8, this means if the song is loud the energy will increase. There is negative correlation between acousticness and danceability, a positive correlation between between energy and valence and so on.
We are now going to take a sample of the dataset, we will do this by taking 0.4% of the original dataset.
sample_track= spotify_tracks.sample(int(0.004*len(spotify_tracks)))
print(sample_track)
The sample dataset will contain 2346 rows
We check the relationship between loudness and energy using regression line
plt.figure(figsize=(10,6))
sns.regplot(data = sample_df, y = "loudness", x = "energy", color = "c").set(title = "Loudness Vs Energy reg correlation")

![](correlation.JPG)

To know the total number of songs realeased per year from 1922 to 2021 that are available in spotify we use the histogram plot. But we need to create a new column that will contain only the year, the code below will achieve that
spotify_tracks['release_date'] = pd.to_datetime(spotify_tracks['release_date'])
year = spotify_tracks['release_date'].dt.year
we now plot the histogram 
sns.displot(years, discrete=True, aspect=2,height=5, kind="hist").set(title="Number of songs per year")


In the 1920s there are limited number of songs, the number increased exponencially towards the beginning of 2000
We will now see the duration of songs over years usuing bar plot
total_duration = df_tracks.duration
fig_dims = (18,7)
fig, ax = plt.subplots(figsize=fig_dims)
fig = sns.barplot(x=years, y=total_duration , ax = ax, errwidth = False).set(title = 'Year Vs duration')
plt.xticks(rotation = 90)


A line plot can also be used to see the relationship between the duration of songs and year released using the code
total_duration = df_tracks.iduration
sns.set_style(style="whitegrid")
fig_dims = (10,5)
fig, ax = plt.subplots(figsize=fig_dims)
fig = sns.lineplot(x=years, y=total_duration , ax = ax).set(title = 'Year Vs duration')
plt.xticks(rotation = 60)

