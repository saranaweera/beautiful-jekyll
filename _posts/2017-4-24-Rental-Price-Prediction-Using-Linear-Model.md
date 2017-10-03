---
layout: post
title: Can You Afford to Dwell Well?
subtitle: Analysis and Prediction Rental Prices in the Big Apple
image: /img/20170411_195356.jpg
bigimg: /img/20170411_195356.jpg
---

New York city is an extremely tough market when it comes to apartment rentals. As all New Yorkers would say, rents go only in one direction, that is 'up'. Increasing trend of urbanization could be the leading cause of this.


As all New Yorkers would attest to apartment hunting could be a grueling stressful process.


Can one use linear regression to predict NYC rental prices? I was quite surprised how effective a simple linear regression is at predicting rental prices.

#### Data ####

I used around 30,000 point-in-time rental listings as observations for this analysis.

In addition, I incorporated zip code level median age and median household income information from the US Census.

I also employed NY State Health Department's zip code to neighborhood mappings to identify various neighborhoods (Note: realestate agents might use a different definitions)

#### Model Features ####

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
