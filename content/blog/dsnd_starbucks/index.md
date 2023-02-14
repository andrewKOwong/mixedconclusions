---
title: "Will It Complete? Predicting Starbucks Offer Completion Rates"
date: 2021-11-05T16:38:45-08:00
publishdate: 2021-11-05T16:38:45-08:00
author: Andrew Wong
tags: [starbucks]
comments: true
draft: true
ShowToc: true
TocOpen: true
EnableMathJax: false
cover:
    image: "images/cover.jpg" 
    caption: "Photo by [Asael Peña](https://unsplash.com/@asaelamaury?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)"
---
## Project Definition
### Project Overview
(Note: The code accompanying this post is available in [this GitHub repository](https://github.com/andrewKOwong/dsnd-starbucks)).

Starbucks is a well-known coffee company [operating ~32,000 stores](https://s22.q4cdn.com/869488222/files/doc_financials/2020/ar/2020-Starbucks-Annual-Report.pdf) with net revenues of 23B USD as of 2020. Starbucks customers can choose to participate in the Starbucks Rewards programs, allowing them to earn discounts on purchases as well as receive special offers from time to time. Knowing how customers react to specific offers could be valuable information, as sending the right offer to the right customer at the right time could favorably influence how much customers spend.

As part of the Data Scientist Nanodegree, Udacity provided a simulated dataset of 17000 rewards program customers. This dataset contains customer transactions over a period of around one month, including when these customers received, viewed, and completed reward offers.

### Problem Statement
The customer data provided could potentially be used to understand and predict how customers respond to different rewards offers. After exploring the data, I elected to build statistical learning models (specifically, K nearest neighbours and random forest models) to predict whether a customer would respond to an offer based on four pieces of information — the customer’s age, gender, income, and length of how long the customer had been a rewards member. These models achieved an F1 score of at least ~0.70, although I found some scores may be inflated by class imbalances.

### Metrics 
Depending on the business scenario, one may choose to minimize false positives or false negatives. Since digital offers have near zero marginal cost, at times one may want to err on the side of sending more offers if they didn’t want to miss a chance for a customer to use an offer (i.e. higher false positives, lower false negatives). Conversely, it may not be important to miss a customer on an offer round, if one could simply send another offer later (higher false negatives). Alternatively, too many unattractive offers might irritate customers (lower false positives).

Thus, it did not appear to me that there are strong reasons *a priori* to minimize either the false positive or false negative rates. Hence, for model development, I decided to use F1 scoring as a generic first attempt to balance precision and recall.