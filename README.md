# Hypothesis-Testing-with-Chi-Square
In this case study I perform a chi-square test with the data from the Eniac, applying a post-hoc correction to perform pairwise tests and find the true winner.
This is what the homepage currently looks like:

## Business Challenge
![image](https://github.com/user-attachments/assets/477e91b6-3eca-4681-8a72-cafe2a4fc677)
The white “SHOP NOW” button is prominently featured in the primary banner, yet it only attracts about 2% of users. This click-through rate (CTR) is a common metric in online marketing, representing the number of clicks an ad receives divided by the number of times the ad is shown. In this context, CTR is measured as the number of clicks a website element receives divided by the total number of website visits. The team analyzing the website provided specific numbers and their reactions:

During the sample period from October 13, 2021, to October 20, 2021, which included 50,061 homepage visits, just under 2% of users clicked “SHOP NOW.” This button attracted about twice as many users as the “iPhone” link in the left sidebar but had a lower CTR compared to the surrounding banner, which achieved roughly 3.5%. The team questioned whether the visual features of the “SHOP NOW” button or the text itself might be too immediate a commitment for users.

![image](https://github.com/user-attachments/assets/470dfd2f-3f34-4cc8-892f-288487f14324)

In an A/B test, the UX team typically performs user research and develops a new version of the website element to be tested. They conducted an anonymous user survey, showing a de-branded mock-up of the homepage and asking, “What do you think and feel when you see this ‘SHOP NOW’ button on an online retailer’s website?” 

Here are a few recorded responses:

Holly, 32:
“I’m on a shopping site, so it seems pretty natural to me. I would probably click it if I knew which items it would take me to.”

Ed, 57:
“I like to do my research before I put anything in a basket. I’d like to see some product specs before I’m ready to ‘shop’.”

V., 16
“Seems a little unnecessary. I came here to shop, so why is the website telling me to shop?”

To boost the click-through rate (CTR) of the ‘SHOP NOW’ button, Marketing has requested the Design team to create a redesign. The result is the original button along with three new variations.

![image](https://github.com/user-attachments/assets/4aa42928-4fd1-439d-8d1a-5c50b2e20423)

Changes in each version:

Version A: the original site.
Version B: the SHOP NOW button is now red.
Version C: the SHOP NOW button has a new text, “SEE DEALS”
Version D: both the B and the C changes to the button have been applied.

The goal is to determine if there is a statistically significant difference in the click-through rate.

The hypotheses to be tested in the experiment are as follows:

Null Hypothesis: All versions have the same click-through rate (CTR).
Alternative Hypothesis: There is a difference in the CTR among the different versions.
A typical statistical significance level of 95% was chosen. The minimum detectable effect was set at 20%, as it was determined that even a small increase in the conversion pipeline would justify the costs of a minor website change.

Currently, the CTR for the “SHOP NOW” button is approximately 2%, and the page receives an average of 7,142 visits per day. With these numbers, a power calculator like this one can be used to determine the test duration. The length of the test was extended to 14 days to cover a full two business cycles.

While exploring the data from the different versions of the website, we discovered significant differences in click-through rates (CTR). These rates can be calculated by dividing the number of clicks each element of interest (such as “SHOP NOW” and “SEE DEALS”) received by the total number of visits to each page.


![image](https://github.com/user-attachments/assets/fcd1a448-bdae-4316-9454-56dcfb385ee4)


It appears that the red variations are the worst performers, while the white buttons perform much better. But are these differences due to chance? This is what we aim to test:

Null Hypothesis: The four versions of the button are equally likely to receive clicks, and the observed differences are due to chance.
Alternative Hypothesis: The observed differences are not due to chance; at least one version received significantly more or fewer clicks than the others, indicating a better or worse CTR (i.e., better or worse performance).
By referring back to the original counts that produced these rates, we can create a contingency table like the one below. Since we are only interested in people clicking on that single element, the counts for “Click” represent the clicks on that element, and “No-click” is calculated as visits minus clicks.

![image](https://github.com/user-attachments/assets/2387e55d-e108-4648-9305-5e74cb313fb1)

This is how the data should be structured to perform a chi-square test using the chi2_contingency function from scipy. This test will help us determine whether the values in the rows (“Click” or “No-click”) depend on the values in the columns (“Version_A”, “Version_B”, “Version_C”, “Version_D”).

Python

from scipy import stats
chisq, pvalue, df, expected = stats.chi2_contingency(your_contingency_table)

What p-value did you get? For a confidence level of 95%, the significance level is 1 – 0.95 = 0.05. We need to check whether the p-value is smaller than 0.05. If the p-value is smaller than our significance level, we can reject the Null Hypothesis, indicating that it is very likely the clicks depend on the version of the website.

But does this tell us the real winner? Not yet! This only indicates that some version(s) performed better (or worse) than others. While we might be confident that the best version (Version C) performed better than the worst one (Version D), we cannot be certain that the differences between the two versions with white buttons or the two versions with red buttons are significant.

When we get significant results while comparing more than two variants, we perform a post-hoc test. This involves running a new chi-square test for each pair of variants.

## Key Learnings

Prior to taking the chi-squared test, we covered the following topics to prepare for it:

1. The Central Limit Theorem. As we draw samples from a population and we look at their sample means, we obtain a sampling distribution. The Central Limit Theorem tells us that, no matter the original distribution of the population, as we take more and more samples, the sampling distribution of sample means will approach a normal distribution.
  
2. Confidence Level. The confidence level of a confidence interval represents the percentage of times the interval would contain the population parameter if the sampling process were repeated many times. For instance, a 95% confidence interval indicates that we are confident the interval would include the population parameter 95 out of 100 times.

3. Hypothesis Testing: 1-sample T-test and Hypothesis Testing: 2-sample T-test

![image](https://github.com/user-attachments/assets/161e56ae-4e79-42fb-974f-65ac363435b4)

## Additional Reflections

Since it is not clear which version (A or C) performs better, the following options are available: Repeating a test multiple times increases the likelihood of incorrectly rejecting the null hypothesis (known as a “Type 1 error”). Consider it this way: if you are 95% confident you won’t make a mistake in scenario 1, and 95% confident you won’t make a mistake in scenario 2, your overall confidence of not making a mistake in either scenario is 0.95 * 0.95 = 90.25%. The same principle applies when conducting multiple tests: each time we run a single test, there is a chance we are mistakenly rejecting the null hypothesis (indicated by the p-value), and the probability of making any mistake increases as we perform more tests. In practice, this means we need to adjust our alpha (the p-value threshold for rejecting the null hypothesis).

There are several methods to do this. We will use the Bonferroni Adjustment, as recommended in the book Passion Driven Statistics by Alan T. Arnholt:

For post hoc tests following a Chi-Square, we use the Bonferroni Adjustment. This adjustment is used to counteract the problem of Type I Error that occurs when multiple comparisons are made. Following a Chi-Square test that includes an explanatory variable with 3 or more groups, we need to subset to each possible paired comparison. When interpreting these paired comparisons, instead of setting the α-level (p-value) at 0.1, we divide 0.1 by the number of paired comparisons we will be making. The result is our new α-level (p-value).

After performing post-hoc tests, do you have a single variant winner?

If the answer is still no, your options are:

Performing another experiment with the winning variants.
Analyzing other metrics like the drop-off-rate and the homepage-return rate to better understand user behavior with each variant and making the final decision based on that.

![image](https://github.com/user-attachments/assets/df10fad4-6b99-422f-a46f-e0ba9aacd928)

![image](https://github.com/user-attachments/assets/fdf00c5b-59e7-49d4-ac90-d1addd34d27e)

Therefore the winner is version A.

