# Udacity_AB_Test_Project

## Experiment Overview
### Experiment Name: Free Trial Screener
Udacity is a website providing online education. To improve its services and maximize course completion by students, Udacity set up an A/B test titled "Free Trial Screener".

### Current Condition before change
At the time of this experiment, Udacity courses currently have two options on the course overview page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

### Experiment Description
In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead.The screenshot below shows what the experiment looks like.
![screener](https://github.com/XHuang2046/Udacity_AB_Test_Project/blob/main/Final%20Project_%20Experiment%20Screenshot.png)

### Experiment Hypothesis:
The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time???without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.
The unit of diversion is a cookie, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

## Experiment Design
The user enrolling flow in A/B version and the seven metrics (provided by Udacity) are shown in figure below:
![A/B test flow](https://github.com/XHuang2046/Udacity_AB_Test_Project/blob/main/flow.JPG)

### Unit of Diversion
The unit of diversion is a cookie, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

### Metric Choice
We need two types of metrics for an experiment, invariant metrics and evaluation metrics. Invariant metrics are used for "sanity checks", which is to check if our experiment is set up correctly. Invariant metrics are the metrics we expect not to change, or in other words, we expect those metrics not to be affected by the new feature.
Evaluation metrics on the other hand, are the metrics which we expect to see a change, and are related to the business goals we aim to achieve. An ideal evaluation metric should be sensitive to the changes we care about, meanwhile robust enough against the changes we don???t care about.
For each metric, Udacity provides a minimum change (Dmin) which is practically significant to the business, it is also called minimum detectable effect (MDE).

**Invariant Metrics**
| Metric | Formula |Notation|dmin|
| --- | ----------- |----------- |----------- |
| Number of Cookies | # Unique cookies to view the course overview page per day|C| 3000|
| Number of Clicks | # Unique cookies to click "Start free trial" per day | CL | 240|
| Click-through-probability | CL/C |CTP |0.01|

These three metrics related to data collected before the screener showing up, so we expect these metrics not to vary between experiment group and control group.

**Evaluate Metrics**
| Metric | Formula |Notation|dmin|
| --- | ----------- |----------- |----------- |
Gross conversion|# user-ids enroll / CL|GCV|0.01|
Retention|# user-ids paid after free trial / # user-ids enroll|R|0.01|
Net Conversion|# use-ids paid after free trial / CL| NCV|0.0075|

Note: Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) 

**Reason to choose/ not choose the metrics:**
- Gross Conversion: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. Since the hypothesis is that the screener reduces the number of frustrated students who left the free trial because they don???t have enough time, which is equivalent to the assumption of that the student without enough time will not enroll free trial, so we would expect the GCVExperiment  is lower than GCVControl.

- Retention: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. Again, our hypothesis is that the number of frustrated students who left the free trial will decrease because of the screener, so we would expect that retention in the experiment group is higher than in the control group.

- Net Conversion: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. The hypothesis states that the screener will not significantly reduce the number of students to continue past the free trial and eventually complete the course, so we expect to see this metric not to vary between two groups.
- Number of user-ids/ Number of enrollments: That is, number of users who enroll in the free trial. We expect to see a decrease in the number of enrollments,  however, this metric is not normalized and it provides equivalent information to the Gross Conversion, so we will not choose this metric to evaluate.

### Metric Variability 
Now, we need to check the variability of a metric to later determine the experiment sample size and to analyze confidence intervals and draw conclusions. The more variant a metric is, the larger the experiment size required and the harder to reach a significant result, so a good evaluation metric should have a small variance. We usually use standard deviation to describe the variance, since all the evaluation metrics are probability, we assume they are all binomial distributed so the standard deviation could be calculated by the formula:

SD = sqrt(p*(1-p)/N)  
p - baseline probability of the event to occur  
N - sample size


Assuming a sample size of 5,000 cookies visiting the course overview page per day (given by Udacity) - we want to estimate a standard deviation for the evaluation metrics. 
The standard error of each evaluation metric is shown in table below:

| Metric | Description |Baseline Value|Sampled Value|SD|
| --- | ----------- |----------- |----------- |----------- |
| Number of Cookies | Unique cookies to view course overview page per day | 40000| 5000|
| Number of Clicks | Unique cookies to click "Start free trial" per day | 3200 | 400|
| Number of Enrollments | Enrollments per day | 660|82.5|
| CTP | Click-through-probability on "Start free trial"|0.08| NA|
| Gross Conversion | Probability of enrolling, given click|0.20625| NA |0.0202
| Retention | Probability of payment, given enroll |0.53| NA |0.0549
| Net Conversion | Probability of payment, given click |0.1093125| NA |0.0156  

The analytical estimation of variance is good enough only when the **unit of diversion** of the experiment is equal to the **unit of analysis** (the denominator of the metric formula). In the case of Gross Conversion and Net Conversion, both units of diversion and analysis are cookies, so the analytical variances we calculated are reliable. However, the unit of analysis of Retention is user-id meanwhile the unit of diversion is cookie. In this case, the actual variance might be different from the analytical estimation and we need to estimate it empirically.

### Experiment Size 
Now we need to determine the experiment sample size. It could be calculated by either python or online calculator [Evan Miller](https://www.evanmiller.org/ab-testing/sample-size.html) 

The standard deviation is shown below:

| Metric|Baseline Value|Sampled Value|SD|dmin|Experiment Size ???Cal. by python???|Experiment Size ???Cal. by Evan's calculator???
| --- | ----------- |----------- |----------- |----- |----- |----- |
| Number of Cookies | 40000| 5000|  |3000
| Number of Clicks | 3200 | 400| |50
| CTP | 0.08| NA| |0.01
| Gross Conversion | 0.20625| NA |0.0202|0.01|645868|645875
| Retention|0.53| NA |0.0549|0.01|4737771|4741213
| Net Conversion |0.1093125| NA |0.0156 |0.0075|685334|685325


### Experiment Duration
The experiment size for Retention is more than 4 million, which means that the experiment will last 119 days even If we divert 100% of traffic to the experiment. This 4-month duration appears to be too risky for Udacity. First, we cannot run any other experiment simultaneously. Secondly, if the treatment jeopardizes the user experience and decreases conversion rates, Udacity has to afford the economic cost, which is a business risk. Consequently, it???s reasonable to abandon Retention and keep working with Gross Conversion and Net Conversion. This reduces the number of required pageviews to 685,334.
Given 40,000 pageviews per day as baseline, the experiment would take:

| % of Traffic|Experiment Duration (days)
| --- | ----------- |
| 100 | 18|
|75|23
|50|35

If we explore the syllabus for Udacity???s courses, many courses are scheduled in 4-5 weeks to be completed, so a 35-day experiment might be a good choice, and meanwhile it???s relatively safer to divert only half of traffic to experiment.

## Experiment Analysis
### Sanity Checks

Before we really perform complicated analysis on results, we need to do the sanity checks to verify the experiment conducted as expected and the data collected correctly.

We have 3 Invariant Metrics:

1. Number of Cookies in Course Overview Page
2. Number of Clicks on Free Trial Button
3. Free Trial button Click-Through-Probability  
 
And we expect to see that these metrics not to vary between control group and experiment group, to be more precise, we need to verify there's no significant difference between metrics in two groups.


| |Expected Value|Observed Value|CI_left|CI_right| Pass
| ---- |--- | ---|----|---- |---- |
Cookies| 	0.5| 0.4994| 0.4988| 0.5012	| Yes
Clicks	| 0.5| 0.4995| 0.4959| 0.5041| Yes
CTP	| 0| 	0.0001| -0.0013| 0.0013| Yes

### Effect Size Test
Similar to the click-through probability, we can test our evaluation metric hypotheses using two proportion z-test (thereby, the same assumptions as outlined above apply). Further, we will check if the changes are also practically significant (if larger than dmin)

| |difference|CI_left|CI_right|dmin|Statistically significant|Practically significant|
| ---- |--- | ---|----|---- |---- |--- |
Gross Conversion| -0.0206|-0.0291|-0.012|0.0100|Yes|Yes
Net Conversion	| -0.0049|-0.0116|0.0019|0.0075|No|	No

### Double check with Sign Tests
In a sign test we get another perspective at analyzing the results we got - we check if the trend of change we observed (increase or decrease) was evident in the daily data. We are going to compute the metric's value per day, then count on how many days the metric was lower in the experiment group and this will be the number of success for our binomial variable. Once this is defined we can look at the proportion of days of success out of all the available days.


| |P Value for sign test|Statistically Significant with alpha = 0.05|
| ---- |--- | ---|
Gross Conversion| 0.0026|Yes
Net Conversion	| 0.6776|No

Sign test results match the effect size calculation.

### Summary

Since the two evaluation metrics are not independent from each other so Bonferroni correction is not adopted here, otherwise the results too conservative.

The observed gross conversion in the experiment group is around 2.06% smaller than which observed in the control group. As number of clicks remaining unchanged, the number of students enrolling in the free trial decreased as we expected. Meanwhile, the net conversion didn't vary significantly between two groups which corresponds to our hypothesis - the screener doesn't significantly reduce the number of students to continue past the free trial. However, the results are less compatible with the assumption that the decrease in gross conversion is entirely absorbed by an improvement in the overall student experience. And from a profit perspective, this treatment doesn't increase the revenue, consequently, I recommend not to launch the screener at this time. 

### Suggestions to the Follow-up Experiment.

The previous hypothesis mentioned that new feature could probably help improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course, however, we don't have any metrics to meaure them. So in the follow-up experiment, we could characterize some metrics to this objective, for example, the optional survey for students when they complete each lesson and the mandaroty survey for coaches.

The experiment size required for tracking retention is too large, so we could charaterize other metrics replacing it. Such as 7-day student churn, 28-days student churn and 45-days student churn.

Re-run the experiment with tracking extra metrics, weekly revenue, the results of survey and student churn (7, 28, 45 days respectively), but decrease the percentage of traffic diverted to the experiment (eg. 25%), then extend experiment duration to 2 months with constantly tracking the weekly revenue for avoiding business risk. If the decline in revenue turns to be out of the certain threshold value, then stop the experiment immediatly for stop-loss. 

Hypothesis: 
1. the gross conversion rate will decrease (the screener will reduce the number of students who don't have enough time)
2. the 7-days student churn will decrease.
3. the 28-days and 45-days student churn will not increase.
4. net conversion will not decrease
5. Survey results will give a positive feedback (students feel better, coaches are able to provide more and useful guidence). 






