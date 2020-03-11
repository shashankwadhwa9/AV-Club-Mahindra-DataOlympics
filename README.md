# AV Club Mahindra DataOlympics

This repository contains the 19th place solution for the [Club Mahindra DataOlympics](https://datahack.analyticsvidhya.com/contest/club-mahindra-dataolympics/) hackathon arranged by AnalyticsVidhya between 3rd May and 5th May, 2019.

Public Leaderboard | Private Leaderboard
------------------ | -------------------
95.6008236950 | 96.4881092559

## Problem Statement
Given around ~300,000 reservations across 32 different hotels/holiday homes of Club Mahindra the objective is to predict the average spend of customers per room per night stay at the hotel against the booking. A wide variety of attributes on the reservation were provided which includes
`booking_date`, `checkin_date`, `checkout_date`, `resort_type_code`, `total_pax` etc.

## Approach

#### Feature Engineering
My approach is pretty straightforward which mainly revolves around feature engineering and feature selection. I tried many different combination of features and found the below three feature sets to be most useful for this contest.

1. **Features on `memberid`**
   - there were more than 100,000 unique member ids i.e. unique customers present in the whole dataset. But the train and test dataset didn't share any reservation for the same memberid. To put it in another way - the train and test dataset were split on `memberid`. Hence, it didn't make sense for me to use `memberid` itself as a variable in the model but a variety of different aggregated features on `memberid` proved to be the most important one later on. This also makes sense intuitively, as with customer level features the model could get additional information about the customers past and present behavior and more importantly relate similar customers in one way or the other.

   - I created a pool of such features which include:
     - total number of reservations by a member
     - total number of reservation by a member on a particular resort
     - visit number of a member
     - visit number of a member on a particular resort
     - visit number of a member in that year (similarly with month)
     - visit number of a member in that year on a particular resort (similarly with month)


2. **Temporal Features**
   - These are the second most imporant feature in my pool of features. Temporal features almost always helps boosted trees as most of the time these models can leverage the cross-sectional correlation of the data (e.g. interaction between different features at a given observation level) but there is no way for the model to tackle the time dimension (e.g. latent interaction between two or more observations recorded at different time points). by infusing these featues explicitly - the model can also learn the cross-time correlation e.g. how booking of member in the past affects the nature of booking of a member at present. This is very important.

   - The temporal features that I considered are:
     - The sequential booking number of a member
     - Days since last checkin
     - The sequential booking number of a member in a particular resort
     - Days since last checkin in a particular resort

3. **`resort_id` features**
   - The last main feature set was about aggregating different attributes on `resort` level e.g. total number of reservations in a resort, sequential booking number of a resort, etc.

4. **Ratio/Multiplication features**
  - ratio of sequential booking number of a member to the total reservations made
  - multiplication of total_people and duration_of_stay


5. **Mean encoding features**
  - Mean likelihhod of various columns with the target column
  - Inspired from [here](https://maxhalford.github.io/blog/target-encoding-done-the-right-way/)

#### Modeling
I had 2 models in total:
1. CatBoost
2. LightGBM

For the final submission, took the mean of these 2 models.
