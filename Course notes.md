
## Chapter 1

**What is A/B testing:**

A/B testing is a general methodology used online when you want to test out a new product or new feature. You show the control set with old version and experiment set with the new version. You can test a wide of things, like new features, different look, new UI, recommendations, ranking changes, and page load time latency.

**What can’t be tested by A/B testing:**

* New experiences like changing the brand logo (change aversion or novelty effect)
* Experiment takes too long to change.
* AB test cannot help you find anything missing

**How to do an A/B testing?**

Scenario: Suppose we have an initial hypothesis about changing the color of the button on the homepage will increase numbers of students who explore the course page. The experiment goes with several steps:

Step 1: choosing an appropriate metric to measure the change. (Chapter 3)

click-through-probability (Now the hypothesis is updated to ‘increase the click-through-probability of the button’)
CTR: measure the usability of the site
CTP: measure the total impact

Step 2: statistics

Review binomial distribution, confidence interval, hypothesis test
Key: the quantitative results tell us whether the difference we observed, could have occurred by chance assuming the null hypothesis is true. So we use the hypothesis testing to see if a difference we observe is significant.
Statistical significance: repeatability
Practically significant (substantive): business standpoint

Step 3: design experiment

Statistical power trade-off: smaller the change to detect or increase confidence requires larger experiment size.
Small size: low alpha, high beta
Large size: same alpha, lower beta
We consider beta at practical significant boundary.
Key: higher CTP (still less than 0.5), decreased practical significance level, increased confidence level (1-alpha), high sensitivity (1-beta), all those requires larger size.

Step 4: analyze the result
Three situations: launch the experiment, won’t launch or need additional data.

## Chapter 2 Policy and ethics for experiments (tbc)

## Chapter 3 Metrics for Experiments

Two use cases: The sanity checking metrics and evaluation metrics

Single overall metric, or if you have multiple metrics, you can create a composite metric (OEC overall evaluation criterion). A general applicable metric you can use across the suite is better than a perfect metric for one test.

#### (1)  high level concept of a business metric.

#### (2)  Translating the high-level metric into specific measurement

**How to choose a metric?**
Examples: 
Update a description on the course list page: probability of progressing from course list to course page;
Increase size of “Start Now” button: CTR on “start now” button;
Explain benefits of paid service: probability that enrolled students pay for coaching, user retention;

**Difficult metrics: don’t have access to data, takes too long to collect**
* New techniques to gather additional data: surveys, retrospective analysis, focus groups and user experience research (UER). They are great for brainstorming new metrics and validating metrics.
* Note that using retrospective analysis is best for establishing a correlation rather than a causation. 
* We want the metric to move as we make changes, so “we run an experiment to validate whether or not we can use a metric for evaluating experiments”.
* Take your company’s culture into consideration when you define a metric.

**Other knowledge:**
Def #1 (Cookie probability): For each <time interval>, number of cookies that click divided by number of cookies
Def #2 (Pageview probability): Number of pageviews with a click within <time interval> divided by number of pageviews
Def #3 (Rate): Number of clicks divided by number of pageviews

**Evaluate possible filters**
Segmenting and filtering data is good for evaluating definitions and building intuition:
* external reasons like spam and internal reasons like selecting a specific language channel for experiment.
(Slicing your data by country, language or platform.)

#### (3) Characterize metrics

We Characterize metrics by sensitivity and robustness or distribution (histogram).
We can run experiments or use retrospective analysis to check the metric.

**Sensitivity and robustness**
For instance, mean is sensitive to outliers, median is robustness. 
You want to the metric to pick up the change you care about (sensitivity) and to be robust against changes that you don’t care about.

