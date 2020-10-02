![infograph](./summary-infograph.png)

## Introduction
Music drives our lives by setting a narrative tone. From Hollywood productions to a regular Tuesday, music has the power to govern our mindset. Composers and artists have an arsenal of tools to convey their art to their listeners. The most obvious of which is lyrical, however instrumentals are the unspoken heros. Our group will look at instrumental features (such as tempo, rhythm, musical key, etc) to determine relationships between music and human emotions.

## Data
We plan to look at the following datasets (and will consider more if needed).
- [Spotify Tracks API](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-analysis/)
- [Million Song Dataset](http://millionsongdataset.com/)

What we can expect from these datasets are measures of features of a certain track such as valence (the positivity or a song) or arousal(energy of a song). The following is a snippet from Spotify's Track API Audio Analysis tool.

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
## Methods and Algorithms
We plan on performing both a unsupervised and supervised algorithm to create the relationship between songs and emotions.
### Unsupervised - GMM Clustering
GMM Cluster will be used on datasets to cluster closely related emotional activations of songs. We will start out with general properties such as "Happy" or "Sad" but will expand out clusters to encapsulate more emotions.

### Supervised - KNN Classification
The unsupervised portion of our project will play an important role in determing labels for our supervised algorithm. We plan on using KNN Classification to determine what emotions and unknown song conveys.

## Results
The goal of our team is to be able to accurately associated songs to certain emotions that span multiple levels of intensity from calming to exciting, sleepy to excited, and positive to negative. This will help confirm the effect of musical features such as minor key songs being generally more negative and high rhythm songs as more energetic. We also hope to see if performing sentiment analysis on instrumental features are more accurate than performing lyrical analysis.

## Discussion
The best outcome is that we will have a model that is able to measure levels of emotions given a certain song or playlist. We can then use our model to evaluate how effective are current music streaming platform playlists. For example, Spotify and Apple Music both have pregenerated platform playlists for "Workouts" and "Studying." We could evaluate how successful these platforms are at creating their playlists and potentially suggest removal/additions to increase a playlist's performance. Our tool could also be utlizied to analyze personal/community generated playlist.

Our applications could stretch far beyond just songs however. We could apply our model to films where we look at the effectiveness of the background music of a scene to the actual event occuring in the scene. Marketers could use such a model to determine what song/tracks should be used in trailers or product commercials.

Currently our project only looks at the instrumentals but for the next steps in creating a more effective model would include incorporating lyrical analysis. This could help solidfy our results and also account for our "musical sarcasm" problem of positive instrumentals with negative lyrics.

## References
- [Machine Recognition of Music Emotion: A Review](https://www.researchgate.net/publication/254004106_Machine_Recognition_of_Music_Emotion_A_Review)
- [Applying Data Mining for Sentiment Analysis in Music](https://www.researchgate.net/publication/318510880_Applying_Data_Mining_for_Sentiment_Analysis_in_Music)
- [Modeling Music Emotion Judgments Using Machine Learning Methods](https://www.frontiersin.org/articles/10.3389/fpsyg.2017.02239/full)
