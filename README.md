
# Reddit Activity Analysis

This project collects data from a specified subreddit using the Reddit API, processes it to analyze daily and hourly activity patterns, and outputs a table showing the number of posts by day of the week and hour of the day.

## Table of Contents

1. [Installation](#installation)
2. [Setup](#setup)
3. [Data Collection](#data-collection)
4. [Data Processing and Analysis](#data-processing-and-analysis)
5. [Results](#results)

---

## Installation

To run this project, you'll need to install the required packages in your Jupyter Notebook environment. The necessary libraries are listed below and can be installed by running the project code.

## Setup

1. **Create a Reddit API Application**:  
   - Go to [Reddit's app preferences](https://www.reddit.com/prefs/apps) and create a new app to get your `client_id`, `client_secret`, and `user_agent`.
   - Insert these credentials in the `reddit = praw.Reddit(...)` section.

2. **Import Libraries**:
   - This project requires several libraries for data manipulation and visualization:
   ```python
   import math
   import pandas as pd
   import numpy as np
   import nltk
   import matplotlib.pyplot as plt
   import seaborn as sns
   import praw
   ```

   The Seaborn library is used for visualization with a customized style:
   ```python
   sns.set(style='darkgrid', context='talk', palette='Dark2')
   ```

3. **Initialize the Reddit API**:
   - Set up a Reddit API instance and specify the subreddit to analyze (e.g., "learnpython"):
   ```python
   reddit = praw.Reddit(client_id='put ID here',
                        client_secret='put secret here',
                        user_agent='put username here')
   subreddit = reddit.subreddit("learnpython")
   ```

## Data Collection

The script collects recent submissions from the specified subreddit, capturing each post’s title and timestamp. Adjust the `limit` parameter as needed to control the number of posts retrieved.

```python
# List to store data
posts = []

# Collect recent submissions
for submission in subreddit.new(limit=1000):  # Adjust the limit as needed
    posts.append({
        "title": submission.title,
        "created_utc": submission.created_utc
    })

# Convert to DataFrame
df = pd.DataFrame(posts)
```

## Data Processing and Analysis

1. **Convert Timestamps**:
   - Convert the `created_utc` timestamps into human-readable day-of-week and hour-of-day formats.

   ```python
   df['created_datetime'] = pd.to_datetime(df['created_utc'], unit='s')
   df['day_of_week'] = df['created_datetime'].dt.dayofweek  # 0=Monday, 6=Sunday
   df['hour_of_day'] = df['created_datetime'].dt.hour       # 0=12am, 23=11pm

   # Keep only day_of_week and hour_of_day columns
   df = df[['day_of_week', 'hour_of_day']]
   ```

2. **Aggregate Activity Data**:
   - Group the data by `day_of_week` and `hour_of_day` to count the number of posts for each combination.

   ```python
   activity_counts = df.groupby(['day_of_week', 'hour_of_day']).size().unstack(fill_value=0)
   print(activity_counts)
   ```

## Results

The result of the analysis is a table (`activity_counts`) that displays Reddit post activity by the day of the week and hour of the day. This table can be used to identify peak activity times, helping you understand when the community is most active. The image file added to the repository shows an example of what the heatmap can look like.

