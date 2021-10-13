![cover_photo](./READ_ME_img/cover.png)
# Building a model for identifying credit card fraud

*As technology has evolved and more people use the internet for transactions, credit card fraud has continually increased. In 2018, worldwide credit card fraud loss was [estimated to be at $24.26 billion](https://shiftprocessing.com/credit-card-fraud-statistics/). Even worse, since the COVID-19 pandemic, credit card fraud has increased even more, [up to 44.7%](https://www.fool.com/the-ascent/research/identity-theft-credit-card-fraud-statistics/). The United States has ranked among the top countries where this occurs.
Companies are continuously searching and improving fraud detection systems. The main crux of the issue is that transactional data tends to be private to a company so improving upon real data must come from within a compnay. Other areas where data is publicly available have more robust solutions to their relative problems. In the case of credit card fraud, synthetic data is often used to help develop models that can work well on real world data. In this project, I create a fraud detection system that helps accurately classify a transaction as fraud/non-fraud and reduce the fraud loss rate.*

## 1. Data

PaySim utilizes aggregated real world data taken from a private dataset to generate synthetic transactional data resembling normal transactions. This real world data is a sample of real transactions extracted from financial logs from a mobile money service implemented in an African country. PaySim was created by Edgar Alonso Lopez-Rojas, Stefan Axelsson, and Ahmad Elmir as part of their Phd theses. Their project goes into the background of creating PaySim and how they approached that. The links to their paper and the data can be found below :

> * [PaySim research paper](https://www.researchgate.net/publication/313138956_PAYSIM_A_FINANCIAL_MOBILE_MONEY_SIMULATOR_FOR_FRAUD_DETECTION)

> * [Kaggle Dataset](https://www.kaggle.com/ealaxi/paysim1)


## 2. Issues with the dataset

The dataset has some challenges on its own. This is in part due to the fact that its synthetic data, and is a challenge that the research paper by Rojas et al. attempts to address. Some of these challenges include:

1. **Imbalanced data:** This is the nature of transactional data, especially when creating a fraud detection system. There will always be data where the amount of non-fraud transactions is much greater than the amount of fraud transactions. It would be worrisome if this wasn't the case! This is addressed with a technique such as undersampling/oversampling. 

2. **Data irregularities:** Looking into the data I saw that there were issues with transaction balances. The difference between the origin amount and destination amount did not match the amount transacted. Additionally, the PaySim system has a column that already flags transactions as fraud but only flags 16 of over 8000 fraud transactions correctly. This may be a product of the fact that this is synthetically created data. I use the balance difference for each transaction as a feature and drop the isFlaggedFraud column.



**The goal** 

The most common strategy in place for identifying potentially fraudulent transactions is to place a limit on the transaction amount; this is what the Paysim system attempts to do. This approach can work but it does have downsides and can be exploited and negatively impact the customer experience. 

In the current dataset, system in place has a fraud loss of just over $12 billion.
In our business scenario, we work for a payment processing company which has identified a major flaw in its technology. Too many of their customers are losing money to fraudulent transactions. Specifically, the system is not catching fraud transactions and thinks a lot of them are legitimate.

The goal is to create a more robust and intelligent classifier to be able to catch these transactions and reduce the fraud loss by $5 billion.
This is approached through identifying what features correlate to existing fraud transactions and creating new features from those to help identify new ones.

## 3. Data Cleaning 

![](./READ_ME_img/cleaning.png)

In a classification system we pick out a target/feature column in mind, in this case the isFraud column. Then we develop features that would help a model correctly classify the data relative to this target/feature. 

This data didn't require much cleaning. A lot of the issues from above were addressed in the EDA and feature engineering sections. 

## 4. EDA

* We see the frauds only occur from two types of transactions. Reducing our data to these two reduces our heavy imbalance by lowering data size from 6.3 million to 2.7 million transactions. 
![](././READ_ME_img/EDA.png)

* We can also take a look at how fraud occurs depending on the time of day. We see a normal distribution of fraud occuring in the early morning hours. This is used to generate one of the features, which is based on when the transaction occurs. 
![](././READ_ME_img/EDA2.png)

## 5. Undersampling and normalization 
Generally with skewed datasets such as the fraud dataset we are working with we run into the issue of imbalanced data. This imbalanced data influences the performance of a ML model. We need to use methods such as undersampling or oversampling so that our model isn't influenced by the highly skewed data.

Undersampling deletes samples from the majority class to try and balance the data. Oversampling duplicates samples from the minority class.

Here we use random undersampling since we have a large amount of the majority data. Random sampling is a simple technique that assumes nothing of the data. There are other methods for undersampling and oversampling that may be used if random sampling is deemed too simple.

Performing feature scaling is really dependent on which ML model you use. In some cases scaling the features can help model performance, in other cases it does not.
Gradient descent based algorithms require features to be scaled, and this is why it is used here with logistic regression and with the gradient boosting model. On the other hand, tree based algorithms are not affected by scaling, this is why it is not used in the random forest model.

## 6. Algorithms & Machine Learning

My approach was to try several different models. A simple dummy classifier, linear regression, decision tree, random forest, and gradient boosting. In my feature engineering section I found that in 99% of fraud transactions, there was a transfer of a certain amount followed by a cash out right after. Normally, when a feature applies to nearly all of the target data uniquely then a model may not be necessary. However, I test the winning model both with and without this feature to see how it affects performance. I find that the model works best with all feaures and removing all features except the 2 important ones and vice versa decreased model performance. 

![](./READ_ME_img/model.png)

>***NOTE:** I choose recall as my evaluation metric because the focus of the model is to be able to accurately classify fraud transactions. It would be more detrimental from a business standpoint to classify a fraud transaction as nonfraud than the other way around.*

**WINNER: Random Forest Algorithm**

While I wasn't surprised that the random forest algorithm won. I was surprised that the random forest without undersampling had a better recall than the one with undersampling. This went against my hypothesis and might be due to the synthetic nature of the data or human error of model preparation. This is something I'll continue to look into as I iterate on the project. 


## Implications
Models can always be optimized. The tradeoff is that tuning parameters using CV algorithms such as GridSearchCV comes with the heavy cost of computation time. There may be parameters that produce much better results than our winning model, however, the decision we make for the business is that those steps need not be taken. The winning model is accurate enough and satisfies our business goals.

Our winning model classifies 2455 out of 2457 fraud correctly from our test set and 828657 out of 828666 nonFrauds correctly. Implementing this model into our system would drastically improve fraud detection rate and help retain existing customers and incentivize new customers to join. Had our winning model been in place, it would have detected 8206 of the 8213 frauds and prevented the loss of over $3.6 billion.

## 10. Future Improvements

* In the future, I would love to spend more time addressing the undersampling issue as to why removing undersampling improves the random forest model. 

* This fraud detection system could also be improved by introducing more data to it. I would like to find a way to generate the synthetic data myself to feed into the model and test it. 

## 11. Credits

Thanks to Edgar Alonso Lopez-Rojas for posting his Phd thesis and the dataset onto Kaggle, and Mukesh Mithrakumar for being an amazing Springboard mentor.




