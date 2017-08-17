---
layout: post
title: Beer Regression Machine Learning
---

# Introduction

Given my interest in home brewing and craft beer, I wanted to analyze the preferences of craft beer geeks, with the hope of using this information to help advise new breweries on which beers to include in their initial tap list. These consumers are novelty-seeking and will spread word of a brewery they like to friends or online, so winning over these consumers early is the key to success.

To help obtain this information, I web crawled [BeerAdvocate](https://www.beeradvocate.com/), a webisite where craft beer geeks can rate various commercial craft beer. Using these ratings, I wanted to understand which characteristics of the beer were particularly important in determining beer geeks' preferences for craft beer. After a particular beer receives 10 ratings, the website aggregates the users' ratings of the appearance, mouthfeel, smell, and taste to produce a BeerAdvocate score from 0 to 100. In addition, the website also contains information about the number of ratings, spread between each of the ratings (in other words, how polarizing the beer is), alcohol by volume (ABV), and style of the beer.

I obtained approximately 35,000 beers with valid BeerAdvocate scores. The distribution of scores is shown below: 

![BeerAdvocate score histogram](https://aawiegel.github.io/assets/ba_hist.png)

Most of the beers receive scores of about 85 with an approximately symmetric distribution. Few beers in this subset received ratings lower than 70, although this particular subset includes only ales, which tend to be more highly rated. Lagers (the kind of beer most non-beer geeks are familiar with) such as [Budweiser](https://www.beeradvocate.com/beer/profile/29/65/) are generally very poorly rated except for, "inexplicably", [Pabst Blue Ribbon](https://www.beeradvocate.com/beer/profile/447/1331/) and [Schlitz](https://www.beeradvocate.com/beer/profile/106/44315/) (Hipsters!!!! *shakes fist*). 


