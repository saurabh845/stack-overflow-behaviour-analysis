# Stack Overflow Behaviour Analysis

This project was used to analyse the behaviour of contributors on [Stack Overflow](https://stackoverflow.com). For this, five metrics were chosen. Their motivation and results are discussed below. These metrics were applied to a set of Posts (Questions and Answers) containing two different tags, _python_ and _c#_. The time frame for this comparison was a 6-month period from _01-01-2021_ to _06-30-2021_.

## Data

The data for this project was obtained from [https://archive.org/details/stackexchange](https://archive.org/details/stackexchange), specifically _stackoverflow.com-Posts.7z_ and _stackoverflow.com-Users.7z_ as on _03-06-2022_.

## Project Organisation

The project was made using Python Jupyter Notebook, and all code is in the "stack-overflow-behaviour-analysis.ipynb" file. 2 filtered CSV files with tags used to generate below graphs are in the project root folder.

## Metrics

### RQ1: How much time does it take for contributors on the platform to post an answer which will be Accepted?

**Motivation:** Often times, users would post questions as they are stuck while programming. With this analysis, the idea was to calculate a _turnaround time_ for the different tags i.e. the time difference between posting a question and getting a helpful answer from the community.

**Method:** Only the questions with an accepted answer were taken into consideration. The dataset contains an accepted answer ID for each such post. To calculate _turnaround time_, the question posted date is subtracted from accepted answer posted date, and converted into hours.

``` python
def time_diff(row):
    ques_date = row["CreationDate_x"]
    ans_date = row["CreationDate_y"]
    ques_date_obj = datetime.datetime.fromisoformat(ques_date)
    ans_date_obj = datetime.datetime.fromisoformat(ans_date)
    return (ans_date_obj - ques_date_obj).seconds

QuesAns_df["TurnaroundTime"] = QuesAns_df.apply(time_diff, axis=1)
```

**Result:** It was found that Python posts get answered quicker, with a mean time of 3 hours. In comparison, C# posts consistently took longer, with a mean time of 4 hours 33 minutes.

<img src="https://github.com/saurabh845/stack-overflow-behaviour-analysis/blob/main/plots/turnaround%20time.jpg" alt="Turnaround Time" style="width:400px;">

### RQ2: When are the most number of questions asked containing a particular tag?

**Motivation:** After the previous result, this metric was taken to investigate when is a particular community most active on the platform.

**Method:** For this analysis, the daily number of questions asked with a particular tag were was computed and plotted over time.

**Result:** It was observed that more Python questions are asked in comparison to C#, by a factor of 4. Further, it was found that the communities for both the categories are more active during weekdays than weekends.

<img src="https://github.com/saurabh845/stack-overflow-behaviour-analysis/blob/main/plots/question%20count.jpg" alt="Questions per day" style="width:400px;">

### RQ3: What is the distribution of question posters' reputation score between different tag categories?

**Motivation:** This metric was chosen to analyse community maturity. The [reputation score](https://stackoverflow.com/help/whats-reputation) of a user is earned by contributing to the platform in various ways. It can also be lost by downvotes and placing bounties on questions.

**Method:** Every post in the dataset is linked to a user ID. To get the reputation score for the question poster, the table containing posts was merged with users' information table based on ID. As there might be some users with exceptionally high reputation scores, these were considered as outliers and removed according to the 1.5 * IQR rule.

``` python
def outlier_treatment(datacolumn):
    sorted(datacolumn)
    Q1,Q3 = np.percentile(datacolumn, [25,75])
    IQR = Q3 - Q1
    lower_range = Q1 - (1.5 * IQR)
    upper_range = Q3 + (1.5 * IQR)
    return lower_range,upper_range
```

**Result:** The people asking C# related questions had a higher reputation score mean as 126, compared to Python which was 63.

<img src="https://github.com/saurabh845/stack-overflow-behaviour-analysis/blob/main/plots/reputation%20of%20question%20posters.jpg" alt="Reputation score" style="width:400px;">

### RQ4: What is the ratio of accepted answers to total answers on a question?

**Motivation:** As it was found that one tag category users had higher mean reputation score, this metric was chosen to investigate whether there is a difference in the quality of answers being posted.

**Method:** The dataset contains the answer count for a question post. For this metric, only the questions which have an accepted answer were chosen and the weekly mean answer count was plotted over time for the two tags.

**Result:** It was found that Python questions had 32% more **_additional_** answers, other than the accepted answer. It can be hypothesised that less answers were needed to get an accepted answer for the C# questions.

<img src="https://github.com/saurabh845/stack-overflow-behaviour-analysis/blob/main/plots/answers%20per%20accepted%20answer.jpg" alt="Accepted answer ratio" style="width:400px;">

**Threats to validity:** The additional answers may be providing an alternate solution. Further, more answers may be added after one has been marked as accepted.

### RQ5: What is the ratio of answers posted on a question to number of views?

**Motivation:** Through this metric, the idea was to investigate how likely are users to post an answer once they view a question.

**Method:** The answer count for the questions was divided by the views count, and plotted over time.

**Result:** Observed was that the Python community was more active in posting an answer, which ties to RQ4 results as well, as it was found that Python questions have more answers in comparison to C#. It is to be noted that the upward trend is due dividing by a lesser number of views for recent posts.

<img src="https://github.com/saurabh845/stack-overflow-behaviour-analysis/blob/main/plots/answers%20per%20question%20view.jpg" alt="Answer to views ratio" style="width:400px;">

### Additional Metric - Why do Python questions have more answers?

**Motivation:** It is observed in RQ4 that Python questions have more additional answers other than the accepted one, and in RQ5 that Python questions have more answers per view.

**Method:** To investigate this, the mean Score (Upvotes - Downvotes) of the questions in the two tag categories is calculated and plotted over time.

**Result:** It is found that the mean Score of Python questions is significantly higher than that of C# questions as 0.21 and 0.08, respectively. This could be a reason why contributors are more likely to post answers in the Python tag category.

<img src="https://github.com/saurabh845/stack-overflow-behaviour-analysis/blob/main/plots/question%20score.jpg" alt="Question score" style="width:400px;">

## License
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
