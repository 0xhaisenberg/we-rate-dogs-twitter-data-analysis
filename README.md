# WeRateDogs

## Wrangle Report
This project makes part of the coursework leading to Udacity's Data Analysis Nanodegree. The objective is to demonstrate skills for the Data Wrangling phase of the Data Analysis Process. Data Wrangling is a key part in analytics and is typically described as the one where analysts and data scientists spend the most part of their time.

The project aims to gather data from Twitter API and Udacity provided tweet data, to create analysis about the tweets and the predicted dog’s breed.

Data Wrangling follows,

## Data Gathering Data Assessing Data Cleaning

### 1. Data Gathering
I have gathered the files twitter_archive_enhanced.csv and image_predictions.tsv, which are provided by Udacity using the requests package,

The twitter_archive_enhanced.csv file contains basic tweet data (tweet ID, timestamp, text, etc.) for 2356 of their tweets as they stood on August 1, 2017. As I need further information from the WeRateDogs user, I have gathered data from Twitter API using the tweepy package and stored it as text_json.txt for the above mentioned period (querying by tweet_id present in twitter_archive_enhanced.csv)

I have extracted some features like retweet_count, favorite_count and hashtag from the json and stored it in separate file named retweet_favorite_count.csv

The gathered data are loaded into three different DataFrame,

archive : Loaded data from twitter_archive_enhanced.csv img_pred : Loaded data from image_predictions.tsv retweet_favorite_count : Loaded data from tweets-json.txt

### Summary of Issues Identified.
**Quality Issues**
[ 1. ] There are 181 retweets (retweeted_status_id, retweeted_status_user_id, retweeted_status_timestamp).

[ 2. ] There are 78 reply tweets (in_reply_to_status_id, in_reply_to_user_id).

[ 3. ] There are 2297 tweets with expanded_urls (links to the tweet), indicating 59 tweets with missing data.

[ 4. ] The timestamp column is in string format.

[ 5. ] There are 109 tweets with regular words in the name column that are NOT a valid name; these words are always the 3rd word in the tweet and are all lowercase; all valid names start with an uppercase letter.

[ 6. ] There are 28 tweets with rating_numerator >= 15. The max value is 1776, which does not make sense. When we only look at tweets with rating_denominator of 10, there are 12 tweets with rating_numerator >= 15. Going further, by ignoring the 7 retweets and replies (these are not “original” tweets as specified in the Key Points) we end up with 5 tweets with a rating_numerator >= 15.

[ 7. ] Other words are also included in the name column of the archive dataset

[ 8. ] Tweet_id fields in the three datasets are stored as numeric values and should be strings.

### Tidiness Issues
'archive' [ 1. ] There are 4 columns for dog stages (doggo, floofer, pupper, puppo). The 4 columns for one variable doesn't conform to the rules of "tidy data".

[ 2. ] The Key Points indicates that we’re only interested in “original tweets”, no “retweets”; this data is stored in the columns retweeted_status_id, retweeted_status_user_id, retweeted_status_timestamp.

[ 3. ] Reply tweets are also not “original tweets” either; this data is stored in the columns in_reply_to_status_id, in_reply_to_user_id.

[ 4. ] When all rating_denominators are the same (10) this column is no longer needed.

'json_data' [ 1. ] The json_data table should be combined with the archive table.

## Data Cleaning
**First Step:** I have copied all the three DataFrames using .copy() method,

archive_copy = archive.copy() predictions_copy = img_prediction.copy() json_data_copy = json_data.copy()

**Further Steps:**

I have dropped the Uninterested observations for Reply(78) and Retweet(181) by their index. And those features were also dropped.

For Erroneous data types, I have converted it using '.astype()' method for 'tweet_id' and 'pd.to_datetime()' method for timestamp

For the surprising rating_numerator and rating_denominator, I have done text scraping for corresponding observation. From the text, it was observed that some observations had inaccurate rating and changed it manually. For tweets without any rating, gave default rating as 10/10. And some other high values is true and are due to tweeter has given ratings to group of dogs present in the post as the sample shown below.

The dog’s names issue was solved by replacing the values starting with lower case with ‘None’.

Source name for source column was found using regex and replaced with the same. The URL in text column was removed using regex. ‘&amp’ and ‘\n’ symbol replced with ‘&’ and ’ ’.

I have removed the additional and duplicated URL from expanded_urls.

The resulting dataset has been stored in 'twitter_archive_master.csv'
