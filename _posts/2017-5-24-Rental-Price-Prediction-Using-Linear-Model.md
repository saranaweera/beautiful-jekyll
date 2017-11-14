---
layout: post
title: Can You Afford to Dwell Well?
subtitle: Analysis and Prediction of Rental Prices in the Big Apple
image: /img/20170411_195356.jpg
bigimg: /img/20170411_195356.jpg
---

New York city is an extremely tough market when it comes to apartment rentals. As all New Yorkers would say, rents go only in one direction, that is 'up'. Increasing trend of urbanization could be the leading cause of this.

As any one who has looked for an apartment in NYC can attest to, apartment hunting could be a grueling stressful process. Like with everything in the big city, the apartment market is very competitive. After weeks of searching, when you find the perfect apartment that fits your budget, with not too long of a commute and the right amount of space in the neighborhood that you always wanted to live, you better be ready to jump on it with two months rent in hand. Even then, by the time inquire about the listing, it might be gone.  

Not to mention, rents are increasing twice as fast for renters in the lower income bracket according to a report from [Street Easy](https://ny.curbed.com/2017/8/16/16154956/nyc-rent-prices-wage-increase-comparison) (I know New York City has rent controlled apartments. I'll reserve exploration of how this fits in to the picture for another day).

As a data scientist, I was curious to find out what drives apartment rents in the Big Apple. Are these factors as intuitive as one would think? Can one build a regression model to predict NYC rental prices?

### Background ###
New York City is one of the largest and most expensive apartment rental markets in the United States and in the world. About 70% of the population of the city lives in rental apartments. Which is very different from the rest of the US, where around 65% of the population are home owners. There are more than 55,000 apartment buildings with 6+ units within the city, which leads to around 2.15 million occupied apartments.

I was surprised to find out that Brooklyn was home to the most number of apartments in the city, closely followed by Manhattan, Queens, Bronx and Staten Island.

#### Data ####

The first step in analysis is to obtain data. It seems data for apartment rentals are an elusive subject. I could not find any public data sources containing recent rental information of apartments. 

Luckily, the internet is full of data. I just needed a way to collect it. Using python libraries: Beautiful Soup and Selenium, I was able to write a pretty robust web scraper to scrape a popular rental listing site for current listings for the 5 boroughs of New York City.

It took me a number of attempts of trial and error to figure out various page layouts of the site and to employ the right amount of delay so that pages are not scraped prior to being fully loaded. Page timeouts were another issue. So, I came up with mechanism to record the scraping status of each listing, so I could rerun the scraper on listings that failed. 

After three days of scraping, I had 30,000 point-in-time rental listings across all 5 boroughs.

In addition to the data I scraped, I incorporated zip code level median age and median household income information from the US Census.

I also employed NY State Health Department's zip code to neighborhood mappings to identify various neighborhoods (Note: realestate agents might use a different definitions)

#### Model Features ####

Here are the features I ended up using in my model. My initial intuition was to use square footage of apartments as a feature, but it turned out square footage for studio and one bedroom apartments were either missing or inaccurate. Realtors in New York City tend to over exaggerate the size of an apartment both in the description and in the numeric field as there is no regulation to prevent this. Many apartmetns listed as spacious studios in NYC tend to be smaller than 400 sq.ft in my experience :)

* Number of bedrooms
* Number of bathrooms
* Commute distance (to a single point in Manhattan)
* Median household income by zip
* Median age by zip
* Property/unit amenities (concierge, dishwasher, elevator, doorman, etc)
  - Only 30 most frequently listed amenities were used in this analysis (I realize this creates a bias when it comes to luxury rental, as they are distinguished by unique features that they offer)
  - One-hot encoding was used to include these categorical variables in the model
* NYC Neighborhoods
  - One-hot encoding was used to include these categorical variables in the model

#### Simple Linear Regression on Simple Features ####
