---
layout: post
title: College Closure Risk
---

# Summary

# Overview

College and other forms of post-secondary education (like data science bootcamps!) are increasingly a necessary investment to obtain and maintain the competitive skills in the labor market. Furthermore, an educated populace is important so that civic society can properly evaluate the complex trade offs involved in various economic, social, and geopolitical issues. Given both its public and private value, the performance of universities and other post-secondary institutions should be evaluated to ensure that students are actually learning and not spending too much time playing beer pong and flip cup. (Although some is certainly a key socially formative experience for young adults!)



To exacerbate matters, tuition (after adjusting for inflation) has also increased precipitously in the last fifteen years or so see chart), often making the choice of college an agonizing one for all those involved.
<img src="https://aawiegel.github.io/assets/tuition.png" alt="college tuition at 4-year public universities" style="width: 90%;"/>
To help give prospective students (and their parents) evaluate colleges before they apply and enroll, the Department of Education releases a [College Scorecard](https://collegescorecard.ed.gov/) that provides a quick summary of the performance and cost of different colleges. Here is an example of this report for the colleges near my zipcode.
<img src="https://aawiegel.github.io/assets/college_sc.png" alt="College Scorecard Example" style="width: 90%;"/>
The data for average annual cost, graduate rate, and salary after attending is shown, but the Department of Education collects an enormous body of statistics on each college. These [statistics can be accessed](https://collegescorecard.ed.gov/data/) via download or a free API (application protocol interface) over a number of years. The data here is limited to 4-year universities, 2-year community colleges, and technical schools.



One particularly interesting statistic that the Department of Education collects is whether a college has closed or lost its accreditation in 2017 (encoded in the same statistic). If a college closes, students lose a lot of time and money since they have to apply to a new school. This new school may not accept transfer credits or be in a different location. A loss of accreditation is less severe, but the reputation loss may harm a student’s prospects afterwards.



Given this, I thought it would be interesting to try to predict whether a school would close or lose its accreditation by 2017 from the Department of Education statistics from 2013. During this time, there were a lot of high-profile school closures (particularly for for-profit private schools) because of [their shady practices](https://twocents.lifehacker.com/the-sketchy-world-of-for-profit-colleges-1745584446). Essentially, these schools would heavily market their educational programs as career-changing, take student loan money (subsidized by the government!), and then provide a subpar, nearly worthless education. One of these schools turned out to be particularly important to this problem, but I’ll save that as a surprise for later. 

# Data Collection and Cleaning

<img src="https://aawiegel.github.io/assets/data_collection.png" alt="data collection" style="width: 90%;"/>

To obtain the data, I first downloaded the data definitions table from the Department of Education and stored it in a table in a Postgres SQL database. This table contains the API key for each variable so that it can be queried for information about each school. I then collected data for several different categories for each school. The school table included generic information about the school (e.g., private or public, etc.) along with whether it closed or lost its accreditation by 2017 or not. The other tables (student, aid, and repayment) included data from 2013 on the student demographics, types of federal aid received, and student loan default rates. In particular, I thought the latter two might be indicative of some of the shady practices of the for-profit schools I mentioned earlier.



After placing all the data into a Postgres database, I then used several JOIN statements to merge the data from each table into one data set based on school ID. In the process, there was a missing data (as usual) for several of the features. For numerical data, I imputed the missing data with the mean. I was worried this might cause a leakage problem if the "closed" schools had missing data more often, however. I found that 7.2% and 9.7% of the data was missing for open and closed colleges, respectively. Therefore, there is a slight leakage problem with this data, and the model predictions are probably better than they would be otherwise. 



