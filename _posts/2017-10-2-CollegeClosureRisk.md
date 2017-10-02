---
layout: post
title: College Closure Risk
---

# Summary

# Overview

College and other forms of post-secondary education (like data science bootcamps!) are increasingly a necessary investment to obtain and maintain the competitive skills in the labor market. Furthermore, an educated populace is important so that civic society can properly evaluate the complex trade offs involved in various economic, social, and geopolitical issues. Given both its public and private value, the performance of universities and other post-secondary institutions should be evaluated to ensure that students are actually learning and not spending too much time playing beer pong and flip cup. (Although some is certainly a key socially formative experience for young adults!)

<br/>

To exacerbate matters, tuition (after adjusting for inflation) has also increased precipitously in the last fifteen years or so see chart), often making the choice of college an agonizing one for all those involved.
<img src="https://aawiegel.github.io/assets/tuition.png" alt="college tuition at 4-year public universities" style="width: 100%;"/>
To help give prospective students (and their parents) evaluate colleges before they apply and enroll, the Department of Education releases a [College Scorecard](https://collegescorecard.ed.gov/) that provides a quick summary of the performance and cost of different colleges. Here is an example of this report for the colleges near my zipcode.
<img src="https://aawiegel.github.io/assets/college_sc.png" alt="College Scorecard Example" style="width: 100%;"/>
The data for average annual cost, graduate rate, and salary after attending is shown, but the Department of Education collects an enormous body of statistics on each college. These [statistics can be accessed](https://collegescorecard.ed.gov/data/) via download or a free API (application protocol interface) over a number of years. The data here is limited to 4-year universities, 2-year community colleges, and technical schools.

<br/>

One particularly interesting statistic that the Department of Education collects is whether a college has closed or lost its accreditation in 2017 (encoded in the same statistic). If a college closes, students lose a lot of time and money since they have to apply to a new school. This new school may not accept transfer credits or be in a different location. A loss of accreditation is less severe, but the reputation loss may harm a student’s prospects afterwards.

<br/>

Given this, I thought it would be interesting to try to predict whether a school would close or lose its accreditation by 2017 from the Department of Education statistics from 2013. During this time, there were a lot of high-profile school closures (particularly for for-profit private schools) because of [their shady practices](https://twocents.lifehacker.com/the-sketchy-world-of-for-profit-colleges-1745584446). Essentially, these schools would heavily market their educational programs as career-changing, take student loan money (subsidized by the government!), and then provide a subpar, nearly worthless education. One of these schools turned out to be particularly important to this problem, but I’ll save that as a surprise for later. 

# Data Collection and Cleaning

<img src="https://aawiegel.github.io/assets/data_collection.png" alt="data collection" style="width: 100%;"/>

To obtain the data, I first downloaded the data definitions table from the Department of Education and stored it in a table in a Postgres SQL database. This table contains the API key for each variable so that it can be queried for information about each school. I then collected data for several different categories for each school. The school table included generic information about the school (e.g., private or public, etc.) along with whether it closed or lost its accreditation by 2017 or not. The other tables (student, aid, and repayment) included data from 2013 on the student demographics, types of federal aid received, and student loan default rates. In particular, I thought the latter two might be indicative of some of the shady practices of the for-profit schools I mentioned earlier.

<br/>

After placing all the data into a Postgres database, I then used several JOIN statements to merge the data from each table into one data set based on school ID. I ended up with 7200 schools described by 43 numerical and categorical features. In the process, there was a missing data (as usual) for several of the features. For numerical data, I imputed the missing data with the mean. I was worried this might cause a leakage problem if the "closed" schools had missing data more often, however. I found that 7.2% and 9.7% of the data was missing for open and closed colleges, respectively. Therefore, there is a slight leakage problem with this data, and the model predictions are probably better than they would be otherwise. 

# Model Building

Once the data was obtained form the SQL database and cleaned, I then applied several different classification algorithms to predict the school's closure by 2017, including [logistic regression](https://en.wikipedia.org/wiki/Logistic_regression) (with l1 and l2 [regularization](https://en.wikipedia.org/wiki/Regularization_(mathematics))), [K-nearest neighbors](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm), a [single decision tree](https://en.wikipedia.org/wiki/Decision_tree), [Random Forest](https://en.wikipedia.org/wiki/Random_forest), and [Gradient Boosted Trees](https://en.wikipedia.org/wiki/Gradient_boosting) classifiers. Of these, Random Forest performed the best of the various classifiers with an [AUC score for the receiver operating characteristic curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic#Area_under_the_curve) of 0.90. In this case, since I was interested in prediction and not explanation (in contrast to the [previous project](https://aawiegel.github.io/2017/07/25/BeerRegressionML.html)), RandomForest's increased performance was worth the increase in model complexity. 

<br/>

Interestingly, however, when I examined the feature importances of the random forest, I noticed that the model really picked up on the number of branches as a very important feature for more than 12% of the splits as shown below:

<img src="https://aawiegel.github.io/assets/featimp_rf_withITT.png" alt="feature importance" style="width: 100%;"/>

Examining this further, I found that the school in the dataset with the most number of branches was ITT Tech, which had 137 branches. [ITT Tech](http://www.latimes.com/business/la-fi-for-profit-schools-20160912-snap-story.html) was one very high profile college closure back in 2016, where all of the campuses closed down at once after state and federal officials filed lawsuits due to their fraudulant and predatory practices. Each of these branches is included as a separate entry in the data, so ITT Tech is somewhat overrepresented in the 900 schools that closed from 2013 to 2017. 

<br/>

To ensure that the model was not just picking up on the characteristics of ITT Tech, I ran the Random Forest model again after removing ITT Tech from the data. Thankfully, the AUC score only declined to 0.89, suggesting that the model was not only picking up on ITT Tech. The feature importance of branches also declined to only 5% of the splits, although it still was the most important feature that the RandomForest made decisions based on.

<img src="https://aawiegel.github.io/assets/featimp_rf.png" alt="feature important without ITT Tech" style="width: 100%;"/>

Here, I plotted the ROC curve and precision recall curve for the Random Forest model without ITT Tech in the data.

<img src="https://aawiegel.github.io/assets/Random%20Forest.png" alt="ROC and Precision-Recall Curves" style="width: 100%;"/>

Here, the receiver operating characteristic plot on the left compares the true positive rate (correctly predicting that a college closed) to the false positive rate (incorrectly predicting that a college closed). Essentially, the more we try to predict college closures, the more we will make false predictions of closure. In addition, if our model is just guessing, we will fall along the diagonal orange line in the center. Similarly, the plot on the right is the the Precision-Recall curve. Here, we compare recall (the true positive rate) to the precision (how many of our predicted positives are actually positive). Again, there is a trade-off between the two where the more positive values you correctly predict, the more times you incorrectly predict negatives as positives.
