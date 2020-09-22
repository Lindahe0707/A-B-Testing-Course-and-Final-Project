
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
