# **Candy Power Ranking** 

---
Overview
---
A renowned SuperMarket chain wants to expand their candy offering. These are store brand candies that they sell along the brand offerings. The idea is to create a brand new product. The team is discussing various options at the moment.

Some prefer cookie-based sweets while others think that it should be gummies. The Divisional Director responsible for purchasing has decided to use a more data-driven approach. He contracted with a market research group to collect data on products in the market and their characteristics and customer sentiment.

In this project, an analytical approach is developed using machine learning models to find out which product characteristics drive customer sentiment and subsequently make a recommendation on a new product.


---

**Build a Halloween Candy Power Ranking Project**

The goals / steps of this project are the following:
* Load the data set
* Explore, summarize and visualize the data set
* Train the Machine Learning Models
* Visualize the performance of Machine Learning Models
* Choose the best Machine Learning Models for prediction
* Conclude/summarize the results

[//]: # (Image References)
[image1]: ./images/data_table.PNG "Dataset"
[image2]: ./images/heatmap.png "Correlation between features"
[image3]: ./images/dataset.PNG "Feature Description"
[image4]: ./images/visualize.PNG "Visualize the Performance of ML models"
[image5]: ./images/Ridge_reg_result.PNG "Prediction Results by Ridge Regression Model"
[image6]: ./images/GBM.PNG "Prediction Results by Gradient Boosting Method"

### Data Set Summary & Exploration
candy-data.csv includes attributes for each candy along with its ranking. For binary variables, 1 means yes, 0 means no.
The data contains the following fields:
![alt text][image3]

The dataset consists of 85 rows and 13 features.
The top 5 rows of the dataset is as shown below.
![alt text][image1]

* 'winpercent' column is not in the same scale as other columns in the dataset. This will cause issues in machine learning models. This is because, lot of machine learning models are based on Euclidean distances. In order to achieve zero mean and equal variance, it is very important to have all the data in the same scale or range.
* There were no duplicate rows and no missing values in the dataset.
* Divide the dataset into 'y_train' as output dataset and 'X_train' as input dataset.

#### Exploratory visualization of the dataset.
###### Correlation between variables
Heatmap is great way to do exploratory data analysis when we have a dataset with multiple variables. Heatmap works by correlation. It can reveal general pattern in the dataset. This can be represented by a scale of range -1 to +1, scale of 1 being the most correlated and -1 is not correlated at all.
![alt text][image2]

We can observe that, there is correlation between the features.

### Train the Machine Learning Models
Several Machine Learning models are chosen to train the data and then the respective Root Mean Square Error is calculated. Top two Machine Learning Models with least Root Mean Sqaure Error are chosen and used for the prediction of product characteristics which drive customer sentiment and subsequently make a recommendation on a new product.

The Machine Learning Models chosen for training dataset are as follows:
* Decision Tree Regression
* Ridge Regression
* Gradient Boosting Method (GBM)
* RandomForest Regression
* Linear Regression
* Stochastic Gradient Boosting
* Polynomial Regression
* Lasso Regression
* LassoLars Regression
* Bayesian Ridge Regression
* Passive Aggressive Regression
* ElasticNet Regression

### Visualize the Performance of ML models
![alt text][image4]

### Choose the best ML Models for Prediction
We can observe that, GBM and Ridge Regression gives very low value of Mean Squared Error. Hence, I have chosen these two ML models for the prediction.
#### Visualize the results for Ridge Regression
![alt text][image5]

#### Visualize the results for GBM
![alt text][image6]

### Conclusion
From the above results we can visualize the feature importance and observe that 'chocolate' and 'sugarpercent' features have resulted in higher win percentage. Hence, I conclude that these two characteristics draw the customer sentiments and I hence I recommend to manufacture a new product with these ingredients in higher percentage for increased sale and win percentage.
