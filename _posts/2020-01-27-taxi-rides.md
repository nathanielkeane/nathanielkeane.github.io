---
layout: post
title: Predicting NYC Yellow Cabs with Linear Regression
---


<div class="message">
  Metis, three weeks in
</div>

I thought things might calm down a bit after the first week, but we've continued learning and coding at a breakneck pace, perhaps even accelerating. Our second projects were both more complex and more self directed, and after some brainstorming and prototyping I settled on trying to predict NYC cab rides with weather and sports data.

The broad question is whether some features that are relatively intuitive could accurately daily rides.

## Data
I grabbed data from 3 different sources for this project, using the years 2016-2018:

* The NYC TLC trip [record data](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
* Central Park [climate data](https://www.ncdc.noaa.gov/cdo-web/datasets/LCD/stations/WBAN:94728/detail) from NOAA Local Climatological Survey
*  Scheduled games data for the
[NYY](https://www.baseball-reference.com/teams/NYY/2016-schedule-scores.shtml),
[NYM](https://www.baseball-reference.com/teams/NYM/2016-schedule-scores.shtml),
[NYK](https://www.basketball-reference.com/teams/NYK/2016_games.html), and 
[NYR](https://www.hockey-reference.com/teams/NYR/2016_games.html) from their respective reference sites

In order to make sure I had adequately populated features for my weather data, I decided to use daily summaries and averages, which meant that I would be looking at daily rides and the daily number of games from the selected teams.

## Cleaning

### Taxis

I quickly discovered that the taxi dataset was *enormous*, with most months pushing 10 million rows and a full gigabyte. It simply wasn't feasible to try processing it all at once, so I did exploratory analysis and cleaning on a few individual months and hoped the logic and trends would map to all of them.

Most of the cleaning was tossing out the extreme values, (from 2 second rides to one particularly memorable record which implied a taxi traveling at approximately 0.015*c*). After the table was cleaned appropriately, I aggregated on dates, counted the records and tossed everything else. I packaged all the cleaning and aggregate logic into a script and then set it loose on all 36 months - I think it took almost 3 hours to finish.

### Weather

Luckily the weather data was easier to work with - it already included summary rows, which helped avoid the messy process of calculating averages from what ended up being lots of missing data. Out of 124 columns, I ulimately ended up using 7, due to a combination of missing data, huge categorical spaces, and strong multicollinearity in many of the features.

Very few of the values fell out of expected ranges, so the only real tweak was setting a low numerical value for precipitaiton entries that simply said "trace". Merging into the cleaned taxi table, I retained about 900 observations to work with.

### Sports

The sports data was the easiest to work with by far - even the scraping was more staightforward than expected. All the team schedules had the same format, and aside from a little bit of inspection and URL differences, figuring out how many teams were playing on any given date was pretty much painless.

## Analysis

Once the data was cleaned and put together, I fit a linear regression model to the entire dataset to get a baseline. Initial results weren't inspiring, but were workable, with r2 around 0.18 and mean absolute error at ~40k, or about 12% of the mean.

I decided to try out Ridge and Lasso, varients of a linear regression that attempt to minimize and zero out coefficients, respectively. Initial trials put them only slightly improving on the naive approach, so I ran many trials weighting their added factors higher.

Eventually, I reached significantly lower values for my root mean squared error and mean absolute error.

<table>
  <thead>
    <tr>
      <th>Model</th>
      <th>RMSE</th>
      <th>MAE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Linear</td>
      <td>40262</td>
      <td>32051</td>
    </tr>
    <tr>
      <td>Ridge</td>
      <td>40737</td>
      <td>32634</td>
    </tr>
    <tr>
      <td>Lasso</td>
      <td>40252</td>
      <td>32017</td>
    </tr>
  </tbody>
</table>

Lasso performed the best, now sitting at just under 10% the mean value of total daily rides (about 310k). These mean we expect our predictions to mostly fall within 10% of the actual value, which seemed accurate enough for my purposes.

### Features

The most interpretable features ended up being the intuitive ones - precipitation caused a negative effect on rides (especially snow), and the number of games played boosted them. Temperature and humidity had negative influences, which I surmise might be a combination of walking becoming preferable as temperatures rise and a general coincidence of precipitation and humidity.

### Looking Back
Thinking about my dataset after analysis, it occurred to me that most of my features could be considered broadly seasonal - that is, their trends (especially team schedules) were fairly predictable given a time of year. As such, it's possible that a purely date-based analysis might yielf similar results to this feature set.

Another consideration is the sheer size of the input space - NYC is a city of **8 million people**, and the number of factors affecting taxi rides is definitely larger than my feature set.

On the other hand, given that my question wasn't one of pinpoint accuracy, this model seems to do at least adequately as an answer.

## Conclusions
The final model can reasonably predict the daily rides given only data about the weather and which teams are playing that day - the kind of data you might acquire from just asking someone in the street. All things considered, I think it satisfies my original question - that yes, the intuitive understanding of how weather and events might affect taxi traffic is backed up by the data.