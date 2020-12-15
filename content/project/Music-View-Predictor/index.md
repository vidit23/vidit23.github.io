---
title: Music View Predictor
summary: Gathers data from multiple streaming and social platforms to predict a future views on YouTube
tags:
- Data Analytics
- AWS Lambda
date: "2020-04-27T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption: 
  focal_point: Smart

links:
- icon: github
  icon_pack: fab
  name: Code Repository
  url: https://github.com/vidit23/MusicViewPredictor
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---

The goal of this project is to help streaming services better their content delivery speeds and thus increase user retention. We gather two-day historical data from YouTube and aggregate it with data from different content providers (such as Spotify) and social media platforms to predict the ​change in the Youtube View Count of each individual video the next day​. ​Each instance is a song at a particular point in time. ​This allows the Content Delivery Network (CDN) to focus on accurately distributing the videos to local servers which are closer to the users, thus decreasing buffering lags.

To collect data, we used:
* [Spotify Track DB](https://www.kaggle.com/zaheenhamidani/ultimate-spotify-tracks-db) - A Kaggle Dataset consisting of information about songs divided based on Genre.
* [Spotify API](https://developer.spotify.com/documentation/web-api/reference/tracks/get-several-audio-features/) - To fetch the updated audio analysis, song metadata as well as indexed artist information for each song.
* [YouTube Search API](https://developers.google.com/youtube/v3/docs/search/list) - Takes a single search string returns a list of songs similar to how the search functionality works on the Youtube. However, we only need the official Youtube Video associated with the song because the other videos are usually bootlegged. In order to ensure that we picked the right song, we wrote a script to find the song with the most relevance to our query using cosine distance. The main roadblock is that each Youtube API key has a limit on the number of queries it can perform in 24 hours. To bypass this, we generated around 30 keys and implemented an automated script to cycle through these iteratively once each one reaches its limit.
* [Youtube Data API](https://developers.google.com/youtube/v3/docs/videos/list) - Used to fetch YouTube statistics for each song. Using the Youtube Ids we collected using the Search API, we batch queried the Data API to get the count of corresponding views, likes, dislikes, and comments. However, the API does not return the statistics in the same order as the query, it can also return NULL. This API was run at the same time every 24 hours to get the daily increase in statistics and allow the API limit to reset.




