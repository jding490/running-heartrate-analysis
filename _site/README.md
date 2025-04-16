# How hard do I run?
---
## Introduction

Jonathan Ding - <a href='mailto:jonathd@umich.edu'>jonathd@umich.edu</a>

I analyzed a datset of my own running data of around 300 running activites recorded between 2022 and 2025 using Strava, a popular app used for tracking physical exercise. I obtained the data as a dataframe with all of my uploaded activities, not just the running activities, using the Strava API.

The main question that interested me is how certain factors affect my heartrate during an activity. The factors that I decided to look at where time of activity, distance, pace, and elevation gain. It is generally well-accepted that the higher one's heart rate is during an activity, the more strenuous it is. Certain types of training such as lower intensity, "zone 2" aerobic runs where your heart rate is at 60-70% of its max are known to increase cardiovascular health and lower your overall heart rate. Thus, being able to predict what type of runs place my heartrate at zone 2 can help shape my future training.

Each activity had the following columns:

`id` - a unique identifier for each activity

`name` - the name of the activity

`start_date_local` - the local time at which I began recording the activity

`type` - sport type (run, bike, ski, etc.)

`distance` - the distance travelled

`average_heartrate` - the average heartrate in beats per minute for the activity

`moving_time` - the time spent moving during the activity (not including time paused)

`elapsed_time` - the total time from start to end (including time paused)

`total_elevation_gain` - the total elevation gain in meters

`average_speed` - the average speed in meters per second

`max_speed` - the max speed in meters per second

`external_id` - an external identifier for the activty

---
## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

To narrow our scope of analysis and remove the irrelevant data, I decided to drop the following rows:

- Non-running activities

- Activities shorter than 100 seconds

- Activities with an empty `average_heartrate` value

In addition, the following columns were dropped:

- `name` for irrelevance

- `type` as only running activities were kept

- `moving_time` introduces multicollinearity

- `elapsed_time` introduces multicollinearity

- `max_speed` to narrow the scope

- `external_id` not needed as `id` provides an unique identifier

The columns `moving_time` and `elapsed_time` were dropped because they introduce multicollinearity into the dataset. Both are directly derived from `average_speed` and `distance` — since time can be calculated as distance multiplied by speed, including them would add redundant information which can negatively impact regression models.

Last, the columns that were kept were converted to more useful formats/units:

- `start_date_local` changed to the start hour

- `distance` converted to miles

- `average_heartrate` left unchanged

- `total_elevation_gain` converted to feet

- `average_speed` converted to miles per hour

|          id | Start Time (hour)   |   Distance (miles) |   Heartrate (bpm) |   Elevation Gain (ft) |   Pace (minutes per mile) |
|------------:|--------------------:|-------------------:|------------------:|----------------------:|--------------------------:|
| 13352430485 | 8:00 AM             |           1.51603  |             125.5 |               50.5249 |                   8.63009 |
| 13352429271 | 8:00 AM             |           0.502815 |             103.7 |               22.6378 |                   7.55772 |
| 12387785837 | 6:00 PM             |           2.399    |             136.6 |               58.727  |                  13.1353  |
| 12284049027 | 4:00 PM             |           4.01133  |             179.5 |              240.486  |                   8.79133 |
| 11898056025 | 8:00 PM             |           6.00973  |             182.2 |              127.297  |                   8.3846  |


### Decision to not impute

The decision to drop columns with an empty `average_heartrate` value rather than impute values was due to the fact that heartrate was the variable I was trying to predict. Imputing missing values with the mean or a randomly chosen value would introduce bias into the model, especially since these values would not reflect the true physiological effort of the runner and could risk misleading the model with articifially generated information.


---
## Framing a Prediction Problem
---
## Baseline Model
---
## Final Model