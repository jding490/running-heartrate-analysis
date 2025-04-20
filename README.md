# Train Slow to Race Fast
Jonathan Ding - <a href='mailto:jonathd@umich.edu'>jonathd@umich.edu</a>

---
## Introduction

<p align="center"><em>
Never done, four by forever<br>
Mecca, oasis, no where close<br>
Shameful actions, my eyes avert<br>
Only the two-mile may be worse<br>
Oh wait, it's here, interim bliss<br>
Bile in my throat and on my lips<br>
</em></p>
<p align="center" style="width:25%; margin:auto;">— an anonymous author on the perils of running the 4 x mile relay</p>
<br>
Train slow to race fast. It may seem counterintuitive, but many elite runners perform about 80% of their training in Zone 2 — lower-intensity aerobic exercise done at 60–70% of their maximum heart rate. Zone 2 training is also known to increase cardiovascular health and lower one's resting heart rate.

As a runner, finding out what factors affect heartrate during runs is of much interest to me. Being able to predict what type of runs keep me in Zone 2 can help shape my future training. For me, Zone 2 training would correspond to a heartrate of 120 to 140 bpm. Thus, I analyzed a datset of my own running data containing around 300 running activites recorded between 2022 and 2025 using Strava, a popular app for tracking physical exercise. I obtained the data as a dataframe with all of my uploaded activities, not just the running activities, using the Strava API.

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

To narrow our scope of analysis and to remove irrelevant data, I decided to drop the following rows:

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

- `average_speed` converted to minutes per mile

|          id | Start Time (hour)   |   Distance (miles) |   Heartrate (bpm) |   Elevation Gain (ft) |   Pace (minutes per mile) |
|------------:|--------------------:|-------------------:|------------------:|----------------------:|--------------------------:|
| 13352430485 | 8:00 AM             |           1.51603  |             125.5 |               50.5249 |                   8.63009 |
| 13352429271 | 8:00 AM             |           0.502815 |             103.7 |               22.6378 |                   7.55772 |
| 12387785837 | 6:00 PM             |           2.399    |             136.6 |               58.727  |                  13.1353  |
| 12284049027 | 4:00 PM             |           4.01133  |             179.5 |              240.486  |                   8.79133 |
| 11898056025 | 8:00 PM             |           6.00973  |             182.2 |              127.297  |                   8.3846  |


### Decision to Not Impute

The decision to drop columns with an empty `average_heartrate` value rather than impute values was due to the fact that heartrate was the variable I was trying to predict. Imputing missing values with the mean or a randomly chosen value would introduce bias into the model, especially since these values would not reflect the true physiological effort of the runner and could risk misleading the model with articifially generated information.

### Exploratory Data Analysis

<iframe
src="assets/heartrates.html"
width="800"
height="425"
frameborder="0"
></iframe>

By visualizing the distribution of heartrates, I gained an understanding of how my current training compares to my target training. The graph shows that over 50% of my runs fall within the 150 to 170 bpm range, which is significantly higher than my target Zone 2 of 120 to 140 bpm.

<iframe
src="assets/distance-pace.html"
width="800"
height="425"
frameborder="0"
></iframe>

This graph provides some insight on the distribution of intensity of my runs. Most long runs are done at a less intense pace (higher minutes per mile), while shorter runs have a wider distribution of paces. This is because pre-race easy runs are usually 2-3 miles at a slower pace, while high intensity workouts are also kept on the shorter side.

<iframe
src="assets/distance-heartrate.html"
width="800"
height="425"
frameborder="0"
></iframe>

Now that the relationship between pace and distance has been identified, the following graph shows the distribution of heartrates segmented by distance, with each bin representing a 2-mile interval. These distributions confirm that almost all of my medium to long runs are done above my target heartrate. This suggests that the majority of my runs are more strenuous than intended, potentially leading to overtraining or reduced recovery.

A few reasons for high heartrates regardless of pace could be due to not taking easy days easy enough, which leads to a build up of fatigue, making each consecutive day of running harder than the last. Although maintaining a daily running streak can keep you consistent, if you never get sufficient recovery, the results could be negative. Fatigue can reduce the training benefits of workouts, causing poorer performance in both training and races.

<iframe
src="assets/times.html"
width="800"
height="425"
frameborder="0"
></iframe>

Another interesting factor to consider is the time of day that I run. Nearly 40% of my runs start between 3:00 and 6:00 PM, likely a reflection of running right after school. Performance can vary depending on when you run — your body responds differently at different times of day. For example, when I run first thing in the morning, I often feel groggy, which negatively impacts my performance.

| Start Time (hour)   |   Pace (minutes per mile) |   Heartrate (bpm) |
|:--------------------|--------------------------:|------------------:|
| 6:00 AM             |                   7.33051 |           151.3   |
| 7:00 AM             |                   8.09671 |           160.5   |
| 8:00 AM             |                   8.21487 |           151.724 |
| 9:00 AM             |                   7.7467  |           161.867 |
| 10:00 AM            |                   8.09924 |           156.807 |
| 11:00 AM            |                   7.75507 |           157.995 |
| 12:00 PM            |                   7.75416 |           150.4   |
| 1:00 PM             |                   7.67579 |           155.117 |
| 2:00 PM             |                   8.02449 |           165.467 |
| 3:00 PM             |                   7.1929  |           149     |
| 4:00 PM             |                   7.24439 |           145.664 |
| 5:00 PM             |                   7.52844 |           159.911 |
| 6:00 PM             |                   8.9565  |           152.94  |
| 7:00 PM             |                   7.1963  |           158.2   |
| 8:00 PM             |                   7.83226 |           166     |
| 11:00 PM            |                   8.90811 |           148.5   |

Surprisingly, runs started between 3:00 and 5:00 PM have the lowest average heartrates, despite being not only my most frequent start time but also when I run the fastest. This suggests that my body performs best during this time window. As I consistently run in the late afternoon, my body has likely adapted to expect and optimize for physical activity at that time.

Another possibility is that I was in my best running shape during high school, and many of my afternoon runs come from that time period. If that’s the case, the lower heartrates and faster paces might reflect my peak fitness level rather than the time of day itself.

---
## Framing a Prediction Problem

With the goal of keeping my future runs in Zone 2, I will predict my average heartrate during a run using start time, distance, elevation gain, and pace. This regression problem uses heartrate as the target variable because it is the most closely related metric to cardiovascular capability and aerobic fitness. At the time of prediction, I will have already decided what distance and pace to run at. I also will have a route and start time, so all four of my input variables will be known.

### Loss Function
The data collected will be split into training and test sets with and 80:20 split. I will use the  Mean Squared Error (MSE) of the test set to evaluate model performance, which is the squared difference between actual and predicted values. MSE has the following characteristics:

- High penalties for large errors, making it robust to outliers

- Efficient model training due to differentiability

- Standard for linear regression problems and commonly used


---
## Baseline Model
### Model Selection
To predict `Heartrate`, I will use a linear regression baseline model with only `Distance` and `Elevation Gain` as input features in `sklearn`. Higher distance and elevation gain should both be correlated with higher heartrate, as they both represent a large change in overall position. Heartrate, distance, and elevation gain are all numerical features, and they will make a good starting point for improving my final model. The model will have no hyperparameter tuning or feature engineering.

### Results and Analysis
The resulting MSE of the test set was 260.467. For a 5 mile run with 200 ft of elevation gain, the predicted heartrate comes out to be 159 bpm, which seems a bit low, as the median heartrate of my past runs between 4 and 6 miles is 164 bpm. This performance is not too good, but it should improve as more features are added and the hyperparameters are tuned.

<iframe
src="assets/baseline.html"
width="800"
height="425"
frameborder="0"
></iframe>

---
## Final Model

### Regularization
The final model was a ridge regression model, which is a linear regression model with L2 regularization. L2 regularization acts as a penalty to reduce the influence of large coefficients and is appended to the end of MSE calculations. The larger the penalty, the more the coefficients shrink towards zero and vice versa. The value for the penalty was tuned as a hyperparamter.

### Feature Engineering
Alongside the existing `Distance` and `Elevation Gain` input features, `Pace` and `Start Time` were added, which are numerical and categorical, respectively. Pace allows the model to account for speed/intensity of the activity, while start time also affects performance as shown in previous EDA steps. The following features were transformed/encoded:

`Distance` - raised to an exponent to capture a possible non-linear relationship; the degree of the polynomial was treated as a hyperparameter, 

`Pace` - raised to an exponent to capture a possible non-linear relationship; the degree of the polynomial was treated as a hyperparameter

`Start Time` - required one-hot encoding, converting different time windows to distinct categories with binary values

### Hyperparamter Tuning
Hyperparameters are values that are set before a model is fit to the data. The model's hyperparameters were found using sklearn's `GridSearchCV` with 5-fold cross-validation:

Distance Polynomial Degree - 1

Pace Polynomial Degree - 2

Ridge Regularization Penalty - 10

### Results and Analysis
The final model achieved a significantly improved MSE of 167.468. For example, it predicted a heart rate of 166 bpm for a 5 mile run at a 7:30 pace, with 200 ft of elevation gain, started at 3:00 PM — a result that aligns more closely with expectations. This improvement can be attributed to several factors. Most notably, the inclusion of additional features like pace and start time helped the model distinguish between runs that may have similar distances and elevation gains but differ in intensity and context. Furthermore, the use of ridge regularization helped prevent any single feature from dominating the predictions by shrinking excessively large coefficients. Together, these enhancements contributed to a more robust and accurate model.

<iframe
src="assets/final.html"
width="800"
height="425"
frameborder="0"
></iframe>
