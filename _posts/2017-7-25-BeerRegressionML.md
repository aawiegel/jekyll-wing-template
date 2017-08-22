---
layout: post
title: Beer Regression Machine Learning
---

# Introduction

Given my interest in home brewing and craft beer, I wanted to analyze the preferences of craft beer geeks, with the hope of using this information to help advise new breweries on which beers to include in their initial tap list. These consumers are novelty-seeking and will spread word of a brewery they like to friends or online, so winning over these consumers early is the key to success.

To help obtain this information, I web crawled [BeerAdvocate](https://www.beeradvocate.com/), a webisite where craft beer geeks can rate various commercial craft beer. Using these ratings, I wanted to understand which characteristics of the beer were particularly important in determining beer geeks' preferences for craft beer. After a particular beer receives 10 ratings, the website aggregates the users' ratings of the appearance, mouthfeel, smell, and taste to produce a BeerAdvocate score from 0 to 100. In addition, the website also contains information about the number of ratings, spread between each of the ratings (in other words, how polarizing the beer is), alcohol by volume (ABV), and style of the beer.

I obtained approximately 35,000 beers with valid BeerAdvocate scores. The distribution of scores is shown below: 

<img src="https://aawiegel.github.io/assets/ba_hist.png" alt="BeerAdvocate score histogram" style="width: 800px;height: 600px;"/>

Most of the beers receive scores of about 85 with an approximately symmetric distribution. Few beers in this subset received ratings lower than 70, although this particular subset includes only ales, which tend to be more highly rated. Lagers (the kind of beer most non-beer geeks are familiar with) such as [Budweiser](https://www.beeradvocate.com/beer/profile/29/65/) are generally very poorly rated except for, "inexplicably", [Pabst Blue Ribbon](https://www.beeradvocate.com/beer/profile/447/1331/) and [Schlitz](https://www.beeradvocate.com/beer/profile/106/44315/) (Hipsters!!!! *shakes fist*). I did not include lagers for this project, although I did include them for a beer recommendation system I developed later.

# Feature Engineering

Once I had collected the data, I began building machine learning regression models to explain beer preferences. The initial models using the original features (variables) were not all that interesting because it came up with the brilliant insight that people like beers that taste and smell good (PBR and Schlitz not withstanding) with a high <i>R</i><sup>2</sup> score (>0.9). Given that taste and smell are also used to calculate the BeerAdvocate score, this was also circular reasoning, so I removed sensory data (taste, smell, mouthfeel, appearance) from the data. 

In addition, much of the information about the beer that was embedded in the style variable was not used in the regression. As such, I created several categorical dummy variables based on typical characteristics for each style. First, I separated beers into country of origin variables for American, German, Belgian, and British beers. Then, I created an ordinal variable that represnted the approximate hue (yellow -> orange/amber -> dark) of the beer. I also created several dummy variables that represented whether the beer style was typically hoppy, made with a particular grain (wheat or rye), or sour. 

Although this provided more information about the beer to the regression algorithm, this was only a crude approximation of the actual characteristics of the beer. In reality, the style of a beer is a very loose representation of the beer as brewers tend to call beers whatever they feel like. Furthermore, some styles of beer such as American IPA (India Pale Ale) are incredibly broad since the hops used could provide a citrusy, herbal, floral, or other aroma. (Lately, hop farmers in the Northwestern US have been experimenting a lot with breeding many types of [new hops](https://learn.kegerator.com/mosaic-hops/) with different aromas or flavors from the typical noble German hops.)

# Model building

Once I produced additional features for each beer, I ran several different kinds of regression models.
