---
layout: post
title: MTA
---


<div class="message">
  Looking at the 1st week of Metis
</div>

During our whirlwhind first week at Metis my team was tasked with putting together a recommendation for an outreach group focusing on women in STEM. Specifically, we were called on to use the publicly available MTA turnstile <a href="http://web.mta.info/developers/turnstile.html">data</a> to come up with a strategy to maximize email signups for their canvassing team, with the ultimate goal of pushing attendance to their summer gala.

## Data

Given the timing of our target, we opted to examine data from the beginning of Summer 2019, from April->June. Each entry represents a report on the state of a specific turnstile at the end of a 4 hour period.

## Cleaning

Our team started by examining the data for inconsistencies. First we isolated the combination of columns that identified a unique turnstile. Once we had that identifier, we checked to see if any turnstiles had multiple entries for the same period. We simply picked the first entry for the small number of duplicates encountered.

As we started putting together traffic analysis, we also corrected volume counts that decreased over time, and outliers like turnstiles with enough traffic for an entire station.

## Analysis

After putting together daily reports for each turnstile, we then grouped them by station and found total traffic over our target period. The following plot shows the top 15 stations by volume:

![STATIONS](https://nathanielkeane.github.io/images/stations.svg "Stations by Total Traffic")

In addition, we used our daily data to look at the weekly trends for Penn Station, and found that weekday traffic was consistently higher.

![PENN](https://nathanielkeane.github.io/images/pennstation.svg "Penn Station 3 months")


## Conclusions

Working from our analyses, we recommended focusing on the top 5 stations with the highest traffic volume for their canvassing effort, during weekdays.

## Lessons

One of the most important things I learned over the course of this project was the importance of sane data saving and management. We gradually came around to pickling/sharing a single set of data and core functions, but consolidating (and delegating where appropriate) earlier would have improved our efficiency dramatically.
