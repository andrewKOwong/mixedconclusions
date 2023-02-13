---
title: "It’s mostly about bedrooms — a brief saunter into a month of Airbnb listings in Vancouver"
date: 2021-07-05T11:24:04-08:00
publishdate: 2021-07-05T11:24:04-08:00
author: Andrew Wong
tags: [airbnb]
comments: false 
draft: true
ShowToc: true
TocOpen: true
cover:
    image: "images/cover-roberto-nickson-unsplash.jpg"
    caption: "Photo by [Roberto Nickson](https://unsplash.com/@rpnickson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/fAa25CyYtrg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)"
---
## Introduction

Airbnb is a platform where home owners can list all or parts of their property as a place for guests to stay. These listings are priced on a nightly basis.

How would a potential Airbnb host price their listing? They might want to find out what everyone else is pricing their listings, so that they can adjust their price accordingly.

Thus, I was curious about the following questions find out about Airbnbs in my city (Vancouver, Canada):

Where are the Airbnb listings in Vancouver, and how are they priced?
What features affect the price of an Airbnb listing?
Can we fit a model to predict a listing’s price based on its features?

To get data on Airbnb listings, I accessed webscraped data from [Inside Airbnb](http://insideairbnb.com/), a website that documents the impact of Airbnb on housing availability.
As a free service, it provides [twelve months of data](http://insideairbnb.com/get-the-data.html) on listings in many cities. For simplicity, I decided to limit my analysis to a single month (April 2021). These data consists of 4299 listings that were scraped over 2 days in mid-April.

## Where are the Airbnb listings and how are they priced?

Here’s a map of the listings, with yellow dots indicating listing, and white outlines for the neighbourhoods of Vancouver:

![Map of the neighbourhoods of Vancouver, with dots indicating Airbnb listings](images/map.png "Map of the neighbourhoods of Vancouver, with dots indicating Airbnb listings")

Downtown is the busiest neighbourhood of listings. I then checked to see what the prices of listings were in the different neighbourhoods. There were large spreads of prices in a lot of neighbourhoods, with Kitsilano having the highest median price:

![Boxplots of nightly prices of listings by neighbourhood (in CAD). Note the log scale for price.](images/price_v_neighbourhood.png "Boxplots of nightly prices of listings by neighbourhood (in CAD). Note the log scale for price.")

Overall, prices follow a right-skewed distribution:

![Price Distribution Histogram](images/price_hist.png)