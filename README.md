# A-B-Testing-Course-and-Final-Project
Recap the Udacity a/b testing course and complete final project in Python

# Final Project

## 1. Experiment Overview

Udacity courses currently have two options on the course overview page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the time was over 5 hours a week, they would be taken through the checkout process; if the time was fewer than 5 hours, they were provided with options to either continue enrolling with free trial or access course materials for free.

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time - without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

## 2. Metrics Choice

Udacity provides a list of metrics with a definition and a practical significance boundary, that is, the difference that would have to be observed before that was a meaningful change for the business, is given in parentheses.

Number of cookies: Number of unique cookies to view the course overview page. The metric name is `num_cookie_viewpage`. (dmin=3000)

Number of user-ids: Number of users who enroll in the free trial (complete checkout). The metric name is `num_userid`. (dmin=50)

Number of clicks: Number of unique cookies to click “Start free trial” button (which happens before the free trial screener is trigger). The metric name is `num_cookie_click`. (dmin=240)

Click-through-probability: Number of unique cookies to click the “Start free trial” button divided by number of unique cookies to view the course overview page. The metric name is `ctp` and `ctp = num_cookie_click / num_cookie_viewpage`. (dmin=0.01)

Gross conversion: Number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the “Start free trial” button. The metric name is `gross_conversion` and `gross_conversion = num_userid / num_cookie_click`. (dmin=0.01)

Retention: Number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of user-ids to complete checkout. The metric name is `retention`. The metric name is `retention` and `retention = num_pass14 / num_userid`. (dmin=0.01)

Net conversion: Number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the “Start free trial” button. The metric name is `net_conversion` and `net_conversion = num_pass14 / num_cookie_click`. (dmin=0.0075)

### 2.1 Invariant Metric

There are two types of metrics in the experiment: invariant metric and evaluation metric. 

Invariant metrics are the metrics that shouldn’t be affected by experiments. They are used to check if the control groups and experiment groups are comparable - the samples are randomly assigned to each group. I pick number of cookies, number of clicks and click-through-probability as invariant metrics.

### 2.2 Evaluation Metric

Evaluation metrics include high-level business metrics and detailed ones based on the business objective. We usually expect to see a change in evaluation metrics. As the students would be provided an option of accessing course materials if they could only spend fewer than 5 hours a week, I would expect a decrease in the number of user_ids. On the other hand, if we assume this change will not reduce the number of students to continue past the free trial, I would expect the number of user_ids that past the 14-day boundary will not change, which means the retention will increase and the net conversion will stay unchanged. I pick gross conversion, retention and net conversion as evaluation metrics.

### 2.3 Measuring Variability

We should measure the standard deviation of evaluation metrics based on the baseline values of these metrics.
The unit of analysis for gross conversion is the number of unique cookies to click "Start free trial". Given a sample size of 5000 cookies visiting the course overview page, the standard deviation of gross conversion is 0.0202.

The unit of analysis for retention is the number of user_ids to complete checkout. Given a sample size of 5000 cookies visiting the course overview page, the standard deviation of gross conversion is 0.0549.

The unit of analysis for net conversion is the number of unique cookies to click "Start free trial". Given a sample size of 5000 cookies visiting the course overview page, the standard deviation of gross conversion is 0.0156.
```python
# Gross conversion
gc_sd = round(np.sqrt(0.206250 * (1-0.206250)/ (5000/40000 * 3200)), 4)
print("The standard deviation of gross conversion:", gc_sd)

# Retention
retention_sd = round(np.sqrt((0.530000 * (1-0.530000))/ (5000/40000 * 660)), 4)
print("The standard deviation of retention:", retention_sd)


# Net conversion
net_sd = round(np.sqrt((0.109313 * (1-0.109313))/ (5000/40000 * 3200)) , 4)
print("The standard deviation of net conversion:", net_sd)

```

## 3. Experiment Design

### 3.1 Experiment Subject

The unit of diversion is a cookie, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

### 3.2 Experiment Size

Use an alpha of 0.05 (significance level) and a beta of 0.2 (power), we want to estimate how many pageviews are needed in the experiment. The course provides an [online calculator](https://www.evanmiller.org/ab-testing/sample-size.html) to determine the **sample size** we'll need to collect in our experiment. The Minimum Detectable Effect is the practical significance boundary.

[online_cal](screenshots/online_cal.png)

[size_result](screenshots/size_result.png)

As there are two groups for each metric, the actual sample size across both groups should be twice the sample size we get above. As this sample size represents the unique cookies to click "Start free trial", the total cookies to view course overview page is 40000/3200 = 12.5 times the sample size according to the baseline value. The total pageviews needed for gross conversation is 645,875. 

By using the same method, we can get the total pageviews for retention is 4,746,061 and the total pageviews for net conversation is 685,325.

### 3.3 Experiment Duration and Exposure

If we consider all the evaluation metrics, the experiment will take 119 days with a 100% traffic diversion, given 40,000 unique cookies to view course overview page per day. It will turn out to be a very long-running experiment. By stepping back and looking into the definition of these metrics, we can find the retention equals to ratio of net conversion and gross conversion, so I drop the retention metric considering the duration and cost of the experiment. The experiment duration decreases to 18 days given 685,325 pageviews needed and 100% traffic diversion.

## 4. Result Analysis

### 4.1 Sanity Check

The first step in analyzing the experiment result is checking whether the invariant metrics are equivalent between the control and experiment group. 

Given the cookies to pageviews are randomly assigned to each group with probability 0.5, I first calculated the standard deviation of binomial distribution with probability of 0.5 success (assigning to the control group is defined as a success in this problem).  Then I got the margin of error by multiplying standard deviation by z-score. If the experiment is set up properly, it’s very likely that the observed fraction of success falls within the 95% confidence interval around 0.5. I used the same way to check the metric of the number of cookies to click “Start free trial” button.

```python
check = {"Control":pd.Series([ctl_df.Pageviews.sum(),ctl_df.Clicks.sum()],
                                  index = ["num_cookie_viewpage","num_cookie_click"]),
           "Experiment":pd.Series([exp_df.Pageviews.sum(),exp_df.Clicks.sum()],
                               index = ["num_cookie_viewpage","num_cookie_click"])}
check_df = pd.DataFrame(check)

check_df['frac_of_control'] = check_df.Control / (check_df.Control+check_df.Experiment)
check_df['SD'] = np.sqrt(0.5*(1-0.5)/(check_df.Control+check_df.Experiment))
check_df['ME_with_95_CI'] = check_df.SD * 1.96
check_df['lower_bound'] = 0.5 - check_df.ME_with_95_CI
check_df['upper_bound'] = 0.5 + check_df.ME_with_95_CI
check_df['Falls_in_CI'] = (check_df.frac_of_control >= check_df.lower_bound)&(check_df.frac_of_control <= check_df.upper_bound)
check_df

```

When it comes to click-through-probability, I calculated the pooled probability and pooled standard error in the first step. Then I got the margin of error by multiplying standard error by z-score. A 95% confidence interval was constructed around 0 for checking whether the difference between group values falls within confidence level.

```python

control_ctp = check_df.Control.num_cookie_click/check_df.Control.num_cookie_viewpage
experiment_ctp = check_df.Experiment.num_cookie_click/check_df.Experiment.num_cookie_viewpage
pooled_prob = (check_df.Control.num_cookie_click+check_df.Experiment.num_cookie_click) / (check_df.Control.num_cookie_viewpage + check_df.Experiment.num_cookie_viewpage)
pooled_SE = np.sqrt(pooled_prob * (1 - pooled_prob) * (1/check_df.Control.num_cookie_viewpage + 1/check_df.Experiment.num_cookie_viewpage))
diff = control_ctp - experiment_ctp
ME_95_CI = 1.96* pooled_SE
lower_bound = -ME_95_CI
upper_bound = ME_95_CI
falls_in_CI = (diff>=lower_bound)&(diff<=upper_bound)
print("Control ctp: {:.4f}, experiment ctp: {:.4f}, pooled probability is {:.4f}, pooled standard error: {:.4f}, \ndifference between groups: {:.4f}, margin error: {:.4f}, lower bound: {:.4f}, upper bound: {:.4f}."
          .format(control_ctp, experiment_ctp,pooled_prob, pooled_SE, diff, ME_95_CI, lower_bound, upper_bound))

print ("Wether falls in the CI:", falls_in_CI)

```

According to the results, all invariant metrics pass the sanity check.

### 4.2 Result Analysis

As all sanity check were passed, we can go forward to analyze the evaluation metrics. The hypothesis is reducing the number of frustrated students who give up with 14 days boundary after enrollment while keeping the number of students who continue to pay and complete the course. I expect the gross conversion in experiment group to be lower than that in control group, and expect the net conversion to keep unchanged in the experiment group. 

#### (1) Effect Size Tests 

We have two evaluation metrics, but I decided not to use Bonferroni correction to adjust the significant level. Because the evaluation metrics are not independent based on their definition, the false positive result may be overestimated, which makes the Bonferroni method too conservative.

For gross conversion, the practical significance is 0.01 and the confidence interval is 95%. The analysis process is similar to the click-through-probability one in the sanity check, and I got the difference between two groups, the standard error and the margin of error in this way. Then the confidence interval was constructed around the difference between two groups for checking the statistically significance and practically significance.
Net conversion metric was checked in the same way. 
```python
res = {"Control":pd.Series([ctl_temp_df.Enrollments.sum()/ctl_temp_df.Clicks.sum(), ctl_temp_df.Payments.sum()/ctl_temp_df.Clicks.sum()],
                                  index = ["gross_conversion","net_conversion"]),
       "Experiment":pd.Series([exp_temp_df.Enrollments.sum()/exp_temp_df.Clicks.sum(), exp_temp_df.Payments.sum()/exp_temp_df.Clicks.sum()],
                                  index = ["gross_conversion","net_conversion"]),
      "enroll_pay": pd.Series([ctl_temp_df.Enrollments.sum() + exp_temp_df.Enrollments.sum(), ctl_temp_df.Payments.sum() + exp_temp_df.Payments.sum()], 
                              index = ["gross_conversion","net_conversion"])}
                                 
                                 
res_df = pd.DataFrame(res)

res_df['pooled_prob'] = res_df.enroll_pay/(ctl_temp_df.Clicks.sum() + exp_temp_df.Clicks.sum())
res_df['pooled_SE'] = np.sqrt(res_df.pooled_prob * (1- res_df.pooled_prob) * (1/ctl_temp_df.Clicks.sum()+1/exp_temp_df.Clicks.sum()))
res_df['difference'] = res_df.Experiment - res_df.Control
res_df['ME_95_CI'] = 1.96 * res_df.pooled_SE

res_df['lower_bound'] = res_df.difference - res_df.ME_95_CI
res_df['upper_bound'] = res_df.difference + res_df.ME_95_CI
res_df['prac_diff'] = [-0.01, -0.0075]
res_df['Stat_sig'] = (0 <= res_df.lower_bound)|(0 >= res_df.upper_bound)
res_df['Prac_sig'] = (res_df.prac_diff <= res_df.lower_bound)|(res_df.prac_diff >= res_df.upper_bound)
res_df
```

The result shows the gross conversion is both statistically significance and practically significance, while the net conversion is neither statistically significance nor practically significance. According to the result, we reject the null hypothesis of gross conversion and see a difference between two groups. In contrast, we fail to reject the null hypothesis of net conversion and can conclude that we see no significant difference between the two group.

#### (2) Sign Tests

The sign test checks consistent sign (positive or negative) between pair value of experiment and control group. For each evaluation metric, I did a sign test using the day-by-day breakdown. 
```python
dtd_gc = np.sum(ctl_temp_df.Enrollments < exp_temp_df.Enrollments)
pval_gc = stats.binom_test(dtd_gc, n = ctl_temp_df.shape[0], p=0.5, alternative='two-sided')

print("The p-value of gross conversion by day-by-day breakdown is: {:.4f}".format(pval_gc))

dtd_nc = np.sum(ctl_temp_df.Payments < exp_temp_df.Payments)
pval_nc = stats.binom_test(dtd_nc, n = ctl_temp_df.shape[0], p=0.5, alternative='two-sided')

print("The p-value of net conversion by day-by-day breakdown is: {:.4f}".format(pval_nc))
```

The gross conversion result has a p-value of 0.0026 which is much smaller than 0.05, meaning it’s unlikely that the difference occurs by chance. The net conversion has a value of 0.6776 which means a high probability that the difference occurs by chance. Both the gross conversion and net conversion sign tests are in alignment with the effect size tests. 

## 5. Conclusion

According to the analysis result, I would recommend launching the experiment. After running the experiment, we can monitor the results to see any changes caused by seasonal or holiday reasons. Especially for online courses, user behavior can be affected by school holidays. However, since the change is not a risky one, there is no big harm even if the effect of the experiment is not as significant as we expect. 

A follow-up experiment can be designed about reducing the number of frustrated students who cancel early in the course. We define the frustrated students as those who enroll the free trial but fail to pass the 14-day boundary to pay, so the metric could be the number of enrolled user_ids that drop within 14 days, or could be the retention derived from this experiment which is closely related to cancellation rate.

***Calculation process and results can be found in Python code and Excel file.***





