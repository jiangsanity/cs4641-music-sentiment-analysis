![infograph](./summary-infograph.png)

## Introduction
Music drives our lives by setting a narrative tone. From Hollywood productions to a regular Tuesday, music has the power to govern our mindset. Composers and artists have an arsenal of tools to convey their art to their listeners, the most obvious of which are lyrics; however, instrumentals are the unspoken heroes. Our group will look at instrumental features (such as tempo, rhythm, musical key, etc.) to determine relationships between music and human emotions.

## Objective
The project objective is to relate instrumental elements of music with the emotions they convey, providing a breakdown of the emotional composition of a set of music samples. In the past, this is accomplished using lyrics. Instead, we emphasize the importance of instrumental elements in the emotion a musical piece conveys. These elements include timbre, rhythm, melody, valence, arousal, and musical key.

# Touchpoint 2

## Data
We plan to look at the following datasets (and will consider more if needed).
- [Spotify Tracks API](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-analysis/)
- [Kaggle 160k Song Dataset](https://www.kaggle.com/yamaerenay/spotify-dataset-19212020-160k-tracks)

What we can expect from these datasets are measures of features of a certain track such as valence (positivity or a song) or arousal (energy of a song). The following is a snippet from Spotify's Track API Audio Analysis tool:

```
{
  "duration_ms" : 255349,
  "key" : 5,
  "mode" : 0,
  "time_signature" : 4,
  "acousticness" : 0.514,
  "danceability" : 0.735,
  "energy" : 0.578,
  "instrumentalness" : 0.0902,
  "liveness" : 0.159,
  "loudness" : -11.840,
  "speechiness" : 0.0461,
  "valence" : 0.624,
  "tempo" : 98.002,
  "id" : "06AKEBrKUckW0KREUWRnvT",
  "uri" : "spotify:track:06AKEBrKUckW0KREUWRnvT",
  "track_href" : "https://api.spotify.com/v1/tracks/06AKEBrKUckW0KREUWRnvT",
  "analysis_url" : "https://api.spotify.com/v1/audio-analysis/06AKEBrKUckW0KREUWRnvT",
  "type" : "audio_features"
}
```
The dataset is a cleaned pull of songs from the Spotify Audio Feature API. Spotify is the second most popular music streaming service in the United States. This dataset is composed of over 160,000 songs and details 14 instrumental features for each record such as key, danceability, energy, and more. Due to musical styles changing with time, records with a creation date from 2000 onwards were included in the final dataset, resulting in 40,000 records.

### Selected Features
We looked through the features and picked out the ones we wanted to use for our exploration. Features like name or year of the song were not chosen for obvious reasons. For other features, such as instrumentalness or speechiness, we read the Spotify API descriptions for and felt wouldn't really contribute much in terms of sentiment, so we decided to leave those out as well. We ended up with the following 8 features:

```
selected_features = [
  "acousticness",
  "danceability",
  "energy",
  "valence",
  "tempo",
  "loudness",
  "key",
  "mode",
]
```

For a full description of what each feature represents, visit the Spotify documentation [here](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/).

We believe that *key* and *mode* will be crucial as the major/minor key heavily impacts the mood of a song. 

A few concerns arose when selecting our features.

Some features had different ranges. For example, features like energy and valence ranged from 0-1, while tempo and loudness ranged from 0 to 240 and -60 to 0, respectively. We made sure to scale the features to a common range to account for this.

Some features were discrete. For example, mode is a binary value (0 or 1 for minor or major), key is a value from 0-11 (representing each pitch class), and items such as valence are continuous values from 0-1. We decided to try our clustering with these mixed types anyway to see if they would help produce clean clusters.

We also worried that some of the features, such as energy, somewhat overlapped with other features such as loudness and tempo. We decided to include them anyway, as we figured it may help add additional information when transforming the feature space with PCA.

### Plotting Valence vs Energy
The first attempt in finding a relationship between a record's musical elements and its emotional properties was
experimentation with the valence and energy features. Valence describes the musical positivity conveyed by a track.
Tracks with high valence sound more positive, while tracks with low valence sound more negative. Energy represents a
perceptual measure of intensity and activity. Energetic tracks feel fast, loud, and noisy. We sought a relationship between
these features to find a "definition" of what positive and negative songs are. We expected for energy and valence to be
positively correlated, and when plotted against each other, we did see what seemed to be a positive correlation (songs with high valence also had high energy). We returned to this later to do clustering on.

### Performing PCA
With 8 features, our data would have been hard to visualize and analyze, so we decided to try using PCA on our dataset to reduce the number of dimensions while hopefully capturing the important variation within our data. We used 70k datapoints for this. This is a bar graph of the % of variance explained by each principal component, *without scaling the data first*:

![pre-pca-variance](./figures/pre_pca_all_variances.png)

We can see an imbalance in variance explained. This is probably due to the fact that tempo, one of the 8 features, ranged from 0-240, while most of the other features ranged from 0-1. The tempo feature's large variance contributes a lot to that first principal component and skews the graph quite a bit.

After we scaled the data, here is what the % of explained variances looked like:

![post-pca-variance](./figures/post_pca_all_variances.png)

With all of the features on a common scale, the graph looks a bit more balanced. We took the top two principal components when graphing most of the graphs below.

## Unsupervised Algorithm Results
We approached our data from multiple clustering techniques such as K-means, DBScan, and GMM.

### K-Means
We initially attempted to apply K-Means to our data. The results of the elbow method to find the optimal number of clusters is shown below.

![elbow_method](./figures/kmeans_elbow.png)

We see that the optimal number is between 6-8. From here, we plot the results of k-means clustering and see the following results.

![kmeans](./figures/kmeans_8.png)

The results are somewhat promising but K-Means is not ideal as there are not any visible circular clusters, thus we decided to attempt DBScan and GMM on the data.

### DBScan
DBScan was also performed to see if it could give us more information compared to previous approaches. The most challenging part of DBScan we encountered was optimizing the eps value and min_components. The results from the clustering are shown below.

Results for all 8 features through PCA, top 2 principal components:<br>
![dbscan_all](./figures/dbscan_all.png)
We see two strip-like clusters, probably due to the binary mode feature included. This seemed a little awkward, so we decided to try this with mode (as well as key, another discrete-valued feature) removed.

Results without key and mode, leaving 6 features put through PCA, top 2 principal components:<br>
![dbscan_no_key_mode](./figures/dbscan_no_key_mode.png)

We also decided to use only valence and energy:<br>
![dbscan_energy_valence](./figures/dbscan_energy_valence.png)
(seems to be tilted since we forgot to remove PCA from this one)

We didn't get any satisfactory results, so we decided to try GMM.

### GMM
Songs can convey a mix of emotions, so we decided to try a soft-clustering approach to our problem with GMM. Here, we arbitrarily decided to use 12 cluster centers, each one corresponding to one emotion on the emotion circle shown in the results and conclusion section.

Results for all 8 features through PCA, top 2 principal components:<br>
![gmm_all](./figures/gmm_all.png)

We see the same two-strip separation. The 12 separate clusters are likely separated by key (0-11). These clusters are believable but we felt that those features would make our classification too reliant on mode and key. We wanted to see what our clusters would look like without key and mode.

![gmm_all](./figures/gmm_all_no_key_mode.png)

This looks a lot better but we approach GMM again but only looking at **energy vs valence**. The results are very promising because they coincide with expected results and allow us to label our clusters. The results are shown below. 

![gmm_all](./figures/gmm_energy_valence.png)


## Unsupervised Learning Results and Conclusion
GMM seems to provide the most promising clustering (especially energy vs valence) and would allow us to assign clusters to the following emotion circle.

![emotion_circle](./figures/emotion_circle.png)

We generated csv files corresponding to each cluster to verify any differences and assist in labeling. You can listen to examples of such below to see differences in musical emotion.


### Music Cluster Snippets
The following clips are used for educational purposes. The following links will correspond to snippets of music from songs found in respective clusters on Google drive.

[Cluster 0 Song Clip](https://drive.google.com/file/d/17mtqN4uOsIIy5JJNEc4943LtqLBx-DgY/view?usp=sharing)

[Cluster 2 Song Clip](https://drive.google.com/file/d/1mLX2YiOUhb-GoJeijT-tsb-nq0JlQP6e/view?usp=sharing)

[Cluster 4 Song Clip](https://drive.google.com/file/d/13Tnl24dhBc-84y9RafF0DeNfhRGfBF6m/view?usp=sharing)

[Cluster 6 Song Clip](https://drive.google.com/file/d/1Rye5-_d4DlLFIFBBp43qqlGRy2FqwJIC/view?usp=sharing)

[Cluster 8 Song Clip](https://drive.google.com/file/d/1vnPJs-GrFQh28fpdZ5_xV0NWWRE0xBr9/view?usp=sharing)

[Cluster 10 Song Clip0](https://drive.google.com/file/d/1yODw4yaGGe29XYh9CVfDcEE6zGCkMcit/view?usp=sharing)


### Discussion
The results are promising as when listening to the music clips, we observe distinct differences in moods (subjectively) as a team. It is interesting to note that when looking at simply energy and valence plotted without clustering...

![energy_vs_valence](figures/e_vs_v.png)

We see density along the positive diagonal noting that songs tend to match their levels and not too many songs are created with unique profiles as outliers. From here, we will label our clusters with a general emotion which will assist in our supervised learning portion.

### Supervised Learning Plans KNN Classification
The unsupervised portion of our project will play an important role in determining labels for our supervised algorithm. We plan on using KNN Classification to determine what emotions an unknown song conveys.

With our unsupervised results, we can set a ground truth for our dataset. Our plan will be to run our GMM model on the whole dataset and train our Supervised model on half of this dataset. We will use the other half as testing data and since we have labels for the entire dataset, we can easily confirm our results.

# Touchpoint 1 Items
The items below are for touchpoint 1, some may still be applicable but the content above reports on the midterm progress

## Planned Methods and Algorithms
We plan on using both unsupervised and supervised algorithms to reveal the relationship between songs and emotions.
### Unsupervised - GMM Clustering
GMM Clustering will be used on datasets to cluster closely related emotional activations of songs. We will start out with general properties such as "happy" or "sad" but will expand out clusters to encapsulate more emotions.

### Supervised - KNN Classification
The unsupervised portion of our project will play an important role in determining labels for our supervised algorithm. We plan on using KNN Classification to determine what emotions an unknown song conveys.

## Expected Results
The goal of our project is to be able to accurately associate songs to certain emotions that span multiple levels of intensity from calming to exciting, sleepy to energetic, and positive to negative. This will help confirm correlations we see between musical features and emotions, such as minor key songs being generally more negative and high rhythm songs as more energetic. We also hope to see if performing sentiment analysis on instrumental features are more accurate than performing lyrical analysis.

## Discussion
The best outcome is having a model that is able to measure levels of emotions given a certain song or playlist. We can then use our model to evaluate how accurate current music streaming platform playlists are. For example, Spotify and Apple Music both have pregenerated platform playlists for workouts and studying. We could evaluate how successful these platforms are at creating their playlists and potentially suggest removal/additions to increase a playlist's performance. Our tool could also be utilized to analyze personal/community generated playlist.

Our applications could stretch far beyond just songs. We could apply our model to films by looking at the effectiveness of the background music of a scene in invoking a certain emotion. Marketers could use such a model to determine what song/tracks should be used in trailers or product commercials.

Currently our project only looks at the instrumentals but for the next steps in creating a more effective model would include incorporating lyrical analysis. This could help enhance our results and also account for our "musical sarcasm" problem of positive instrumentals with negative lyrics.

## References
- [Machine Recognition of Music Emotion: A Review](https://www.researchgate.net/publication/254004106_Machine_Recognition_of_Music_Emotion_A_Review)
- [Applying Data Mining for Sentiment Analysis in Music](https://www.researchgate.net/publication/318510880_Applying_Data_Mining_for_Sentiment_Analysis_in_Music)
- [Modeling Music Emotion Judgments Using Machine Learning Methods](https://www.frontiersin.org/articles/10.3389/fpsyg.2017.02239/full)
