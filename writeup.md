
# Candy Power Ranking

The Lidl purchasing group wants to expand their candy offering. These are store brand candies that they sell along the brand offerings. The idea is to create a brand new product. The team is discussing various options at the moment.
Some prefer cookie-based sweets while others think that it should be gummies. The Divisional Director responsible for purchasing has decided to use a more data-driven approach. He contracted with a market research group to collect data on products in the market and their characteristics and customer sentiment.

In this project, an analytical approach is developed using machine learning models to find out which product characteristics drive customer sentiment and subsequently make a recommendation on a new product.

The data set is located at:
https://github.com/fivethirtyeight/data/tree/master/candy-power-ranking.
The data set is provided by FiveThirtyEight under the Creative Commons Attribution 4.0 International
license (https://creativecommons.org/licenses/by/4.0/).

### Step 0: Load the data

The following script imports required libraries and load the dataset.

Since our file is in CSV format, we will use panda's read_csv method to read our CSV data file.


```python
# Import Libraries
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
%matplotlib inline

# Import the dataset
df = pd.read_csv('candy-data.csv', encoding='utf-8') 
```

### Step 1: Dataset Summary & Exploration


```python
# Display the datset information
df.info() 
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 85 entries, 0 to 84
    Data columns (total 13 columns):
    competitorname      85 non-null object
    chocolate           85 non-null int64
    fruity              85 non-null int64
    caramel             85 non-null int64
    peanutyalmondy      85 non-null int64
    nougat              85 non-null int64
    crispedricewafer    85 non-null int64
    hard                85 non-null int64
    bar                 85 non-null int64
    pluribus            85 non-null int64
    sugarpercent        85 non-null float64
    pricepercent        85 non-null float64
    winpercent          85 non-null float64
    dtypes: float64(3), int64(9), object(1)
    memory usage: 8.8+ KB
    


```python
# Display the top 5 rows in dataset
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>competitorname</th>
      <th>chocolate</th>
      <th>fruity</th>
      <th>caramel</th>
      <th>peanutyalmondy</th>
      <th>nougat</th>
      <th>crispedricewafer</th>
      <th>hard</th>
      <th>bar</th>
      <th>pluribus</th>
      <th>sugarpercent</th>
      <th>pricepercent</th>
      <th>winpercent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100 Grand</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.732</td>
      <td>0.860</td>
      <td>66.971725</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3 Musketeers</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.604</td>
      <td>0.511</td>
      <td>67.602936</td>
    </tr>
    <tr>
      <th>2</th>
      <td>One dime</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.011</td>
      <td>0.116</td>
      <td>32.261086</td>
    </tr>
    <tr>
      <th>3</th>
      <td>One quarter</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.011</td>
      <td>0.511</td>
      <td>46.116505</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Air Heads</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.906</td>
      <td>0.511</td>
      <td>52.341465</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Display the shape of the dataset (number of rows and columns in our dataset)
df.shape 
```




    (85, 13)



The dataset consists of 85 rows and 13 columns.

### 1.1 Pre-process the Dataset

'winpercent' column is not in the same scale as other columns in the dataset. This will cause issues in machine learning models. This is because, lot of machine learning models are based on Euclidean distances. In order to achieve zero mean and equal variance, it is very important to have all the data in the same scale or range.


```python
# Scale the winpercent column ('winpercent' is in percentage)
df['winpercent'] = df['winpercent']/100

# Display the top 5 rows in the dataset
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>competitorname</th>
      <th>chocolate</th>
      <th>fruity</th>
      <th>caramel</th>
      <th>peanutyalmondy</th>
      <th>nougat</th>
      <th>crispedricewafer</th>
      <th>hard</th>
      <th>bar</th>
      <th>pluribus</th>
      <th>sugarpercent</th>
      <th>pricepercent</th>
      <th>winpercent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100 Grand</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.732</td>
      <td>0.860</td>
      <td>0.669717</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3 Musketeers</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.604</td>
      <td>0.511</td>
      <td>0.676029</td>
    </tr>
    <tr>
      <th>2</th>
      <td>One dime</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.011</td>
      <td>0.116</td>
      <td>0.322611</td>
    </tr>
    <tr>
      <th>3</th>
      <td>One quarter</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.011</td>
      <td>0.511</td>
      <td>0.461165</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Air Heads</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.906</td>
      <td>0.511</td>
      <td>0.523415</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Drop the duplicate rows
df.drop_duplicates(inplace=True)

# Shape of the dataframe
df.shape 
```




    (85, 13)



The shape of the initial dataframe is same as dataframe after dropping duplicate rows. This means that, there are no duplicate rows in the dataset.

Divide the dataset into 'y_train' as output dataset and 'X_train' as input dataset.


```python
# Output dataset
y_train = df['winpercent']

# Input dataset which does not consists of 'competitorname' and 'winpercent' columns
X_train = df.drop(['competitorname', 'winpercent'], axis = 1)
```


```python
# Missing Values
df.isnull().sum()/len(df)
```




    competitorname      0.0
    chocolate           0.0
    fruity              0.0
    caramel             0.0
    peanutyalmondy      0.0
    nougat              0.0
    crispedricewafer    0.0
    hard                0.0
    bar                 0.0
    pluribus            0.0
    sugarpercent        0.0
    pricepercent        0.0
    winpercent          0.0
    dtype: float64



This indicates that there are no missing values in the dataset.

### 1.2 Correlation between features

Heatmap is great way to do exploratory data analysis when we have a dataset with multiple variables. Heatmap works by correlation. It can reveal general pattern in the dataset. This can be represented by a scale of range -1 to +1, scale of 1 being the most correlated and -1 is not correlated at all.


```python
# Plot figure
plt.figure(figsize = (20,10))     

# annot=True: Add correlation numbers to each cell in the visuals
sns.heatmap(df.corr(), annot=True, linewidth = 0.5, cmap='YlGnBu')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2085a0997f0>




![png](output_21_1.png)


We can observe that, there is correlation between the features.

## Step 3: Training Models


```python
# Import libraries
# train_test_split: Split arrays or matrices into random train and test subsets
# cross_val_score: Evaluate a score by cross-validation
# cross_validate: Evaluate metric(s) by cross-validation and also record fit/score times.
from sklearn.model_selection import train_test_split, cross_val_score, cross_validate
# Computes the mean squared error between the labels and predictions.
from sklearn.metrics import mean_squared_error

# Divide the dataset into training and test datasets
X_train, X_test, y_train, y_test = train_test_split(df[df.columns[1:-1]],df[df.columns[-1]], test_size=0.33, random_state=42)

# Initialize an empty array to store the MSE values of different ML models
rmse_err = []
```

Several Machine Learning models are chosen to train the data and then the respective Root Mean Square Error is calculated. Top two Machine Learning Models with least Root Mean Sqaure Error are chosen and used for the prediction of product characteristics which drive customer sentiment and subsequently make a recommendation on a new product.

### 3.1 Decision Tree Regression

In this section, I have implemented the decision tree algorithm using Python's Scikit-Learn library. 
The hyper-parameter, maximum depth of the tree is set to 'max_depth=5' so that the decision trees learn too fine details of the training data.

To evaluate performance of the regression algorithm, the commonly used metric such as Root Mean Squared error is used. The Scikit-Learn library contains function such as 'mean_squared_error' that can help calculate this value for us.


```python
# Import a decision tree regressor class
from sklearn.tree import DecisionTreeRegressor
# This library provides access to the mathematical functions
import math

# Instantiate estimator and fit training data to create model
reg = DecisionTreeRegressor(max_depth=5).fit(X_train,y_train)

# Make predictions on testset and Calculate the Root Mean Squared Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,reg.predict(X_test))))

# Print the Root Mean Squared Error value for Decision Tree Regressor
print('Root Mean Squared Error for Decision Tree Regressor = ', rmse_err[-1])
```

    Root Mean Squared Error for Decision Tree Regressor =  0.14178441724560673
    

### 3.2 Ridge Regression

Instead of arbitrarily choosing alpha value, it would be better to use cross-validation to choose the tuning parameter alpha. We can do this using the cross-validated ridge regression function, RidgeCV(). By default, the function performs generalized cross-validation, though this can be changed using the argument cv.


```python
# Import Ridge regressor with built-in cross-validation and Linear least squares with l2 regularization.
from sklearn.linear_model import RidgeCV, Ridge

# The Ridge() function has an alpha argument that is used to tune the model. We'll generate an array of alpha values ranging 
# from very big to very small, essentially covering the full range of scenarios from the null model containing only the intercept, to the least squares fit:
alphas = 10**np.linspace(10,-2,100)*0.5

# Create ridge regression with calculated possible alpha values. 
# Scikit-learn includes a RidgeCV method that allows to select the ideal value for alpha
ridgeRegCV = RidgeCV(alphas = alphas, scoring = 'neg_mean_squared_error', cv = None)

# Fit the ridge regression
ridgeRegCV.fit(X_train, y_train)

# Print Best Model’s Alpha Value
print('Best alpha for Ridge Regression = ', ridgeRegCV.alpha_)

# Refit ridge regression model on the full data set using the value of alpha chosen by cross-validation
ridge_Reg = Ridge(alpha = ridgeRegCV.alpha_)

# Evaluate a score by cross-validation
err = (cross_val_score(ridge_Reg, X_train, y_train, cv = X_train.shape[0], scoring = 'neg_mean_squared_error'))

# Append the value of MSE into an array
rmse_err.append(-np.mean(err[-1]))

print('Mean Squared Error for Ridge Regression = ', rmse_err[-1])
```

    Best alpha for Ridge Regression =  3.0679536367065814
    Mean Squared Error for Ridge Regression =  0.0739015310936934
    

### 3.3 Gradient Boosting Trees

Gradient boosting is a boosting ensemble method. Ensemble machine learning methods are ones in which a number of predictors are aggregated to form a final prediction, which has lower bias and variance than any of the individual predictors.


```python
from sklearn.ensemble import GradientBoostingRegressor
from sklearn.model_selection import RandomizedSearchCV

# Create Gradient Boosting for least squares regression
gb_Reg = GradientBoostingRegressor(loss = 'ls')

# Define hyper parameters. Use a random serach to tune the model parameters: depth, number of estimators and learning rate.
parameter_distribution = { "max_depth": range(2,20), "n_estimators": np.arange(10, 80, 5), "learning_rate":[0.001, 0.01, 0.1, 0.2] }

# Run RandomizedSearch on hyper parameters. The parameters of the estimator used to apply these methods are optimized by cross-validated search over parameter settings. 
randomSearchCV = RandomizedSearchCV(gb_Reg, scoring = 'neg_mean_squared_error', param_distributions = parameter_distribution, n_iter = 200, cv = X_train.shape[0])

# Fit the randomized search
randomSearchCV.fit(X_train, y_train)

# Append MSE value of Gradient Boosting Regressor into an array
rmse_err.append(-randomSearchCV.best_score_)

print('Gradient boosting trees best params:', randomSearchCV.best_params_)
print('Gradient boosting trees MSE score:', -randomSearchCV.best_score_)
```

    Gradient boosting trees best params: {'n_estimators': 10, 'max_depth': 4, 'learning_rate': 0.1}
    Gradient boosting trees MSE score: 0.013712782050027034
    

### 3.4 Random Forest Regression

A random forest is a meta estimator that fits a number of classifying decision trees on various sub-samples of the dataset and use averaging to improve the predictive accuracy and control over-fitting.

The RandomForestRegressor is a class of sklearn.ensemble library which is used to solve regression problems using random forest. The most important hyper parameter of the RandomForestRegressor class is the n_estimators parameter. This parameter defines the number of trees in the random forest. I chose the default value of n_estimators because increase in number of estimators had very negligable influence on MSE.


```python
# Import library
from sklearn.ensemble import RandomForestRegressor

# Instantiate model with 10 decision trees and train the model on training data
randomForest_reg = RandomForestRegressor(n_estimators=10).fit(X_train, y_train)

# Use the random forest's predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,randomForest_reg.predict(X_test))))

print('Mean Squared Error for Random Forest Regression = ', rmse_err[-1])
```

    Mean Squared Error for Random Forest Regression =  0.11304629391092853
    

### 3.5 Linear Regression

The linear regression model basically finds the best value for the intercept and slope, which results in a line that best fits the data.


```python
# Import Linear Regression class from sklearn library
from sklearn.linear_model import LinearRegression

# Instantiate Linear Regression class and train the algorithm with the training dataset
lr_reg = LinearRegression().fit(X_train,y_train)

# Use the linear regression predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,lr_reg.predict(X_test))))

print('Mean Squared Error for Linear Regression = ', rmse_err[-1])
```

    Mean Squared Error for Linear Regression =  0.11969409813723213
    

### 3.6 Stochastic Gradient Descent Regression

Linear model fitted by minimizing a regularized empirical loss with SGD.
In Stochastic Gradient Descent, the gradient of the loss is estimated each sample at a time and the model is updated along the way with a decreasing learning rate.


```python
# Import SGD Regressor class from sklearn library
from sklearn.linear_model import SGDRegressor

# Instantiate model with 500 iterations and train the model on training data
sgd_reg = SGDRegressor(max_iter=500).fit(X_train,y_train)

# Use the SGD regression predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test, sgd_reg.predict(X_test))))

print('Mean Squared Error for Stochastic Gradient Descent Regression = ', rmse_err[-1])
```

    Mean Squared Error for Stochastic Gradient Descent Regression =  0.12370483949079315
    

### 3.7 Ploynomial Regression

Polynomial Regression generates a new feature matrix consisting of all polynomial combinations of the features with degree less than or equal to the specified degree.


```python
# Import PolynomialFeatures and LinearRegression class from sklearn library
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression

# Applying polynomial features with default degree (2) of polynomial features.
# Default value degree=2 is chosen because, increase in degree was resulting in increase in MSE.
poly_reg = PolynomialFeatures(degree=2)

# Transform the array into polynomial form
X_ = poly_reg.fit_transform(X_train)
X_test_ = poly_reg.fit_transform(X_test)

# Instantiate Linear Regression class and train the algorithm with the training dataset
l_reg = LinearRegression().fit(X_, y_train)

# Use the linear regression predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,l_reg.predict(X_test_))))
print('Mean Squared Error for Polynomial Regression = ', rmse_err[-1])
```

    Mean Squared Error for Polynomial Regression =  0.4352861890608805
    

### 3.8 Lasso Regression


```python
# Import Lasso class from sklearn library
from sklearn.linear_model import Lasso

# Instantiate model with alpha=0.01 and train the model on training data.
# alpha value is decreased iteratively starting from default value 1 till 0.01 and chosen based on least value of MSE.
# alpha = 0.1 and 0.001 was resulting in higher MSE
lasso_reg = Lasso(alpha = 0.01).fit(X_train,y_train)

# Use the lasso regression predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,lasso_reg.predict(X_test))))
print('Mean Squared Error for Lasso Regression = ', rmse_err[-1])
```

    Mean Squared Error for Lasso Regression =  0.11782913762827293
    

### 3.9 Lasso Lars Regression


```python
# Import LassoLars class from sklearn library
from sklearn.linear_model import LassoLars

# Instantiate model with alpha=0.001 and train the model on training data.
# alpha value is decreased iteratively starting from default value 1 till 0.001 and chosen based on least value of MSE.
lassolars_reg = LassoLars(alpha = 0.001).fit(X_train,y_train)

# Use the LassoLars regression predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,lassolars_reg.predict(X_test))))
print('Mean Squared Error for Lasso Lars Regression = ', rmse_err[-1])
```

    Mean Squared Error for Lasso Lars Regression =  0.1169753660793363
    

### 3.10 Bayesian Ridge Regression


```python
# Import BayesianRidge class from sklearn library
from sklearn.linear_model import BayesianRidge

# Instantiate model and train the model on training data.
br_reg = BayesianRidge().fit(X_train,y_train)

# Use the BayesianRidge regression predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,br_reg.predict(X_test))))
print('Mean Squared Error for Bayesian Ridge Regression = ', rmse_err[-1])
```

    Mean Squared Error for Bayesian Ridge Regression =  0.11705993007034675
    

### 3.11 Passive Aggressive Regression


```python
# Import PassiveAggressiveRegressor class from sklearn library
from sklearn.linear_model import PassiveAggressiveRegressor

# Instantiate model and train the model on training data.
pa_reg = PassiveAggressiveRegressor(max_iter=1000, random_state=0).fit(X_train,y_train)

# Use the PassiveAggressiveRegressor predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,pa_reg.predict(X_test))))

print('Mean Squared Error for Passive Aggressive Regression = ', rmse_err[-1])
```

    Mean Squared Error for Passive Aggressive Regression =  0.13186318750390263
    

### 3.12 Elastic Net Regression


```python
# Import ElasticNet class from sklearn library
from sklearn.linear_model import ElasticNet

# Instantiate model and train the model on training data.
enet_reg = ElasticNet(max_iter=1000, random_state=0).fit(X_train,y_train)

# Use the ElasticNet predict method on the test data and calculate the Root Mean Square Error
rmse_err.append(math.sqrt(mean_squared_error(y_test,enet_reg.predict(X_test))))

print('Mean Squared Error for Elastic Net Regression = ', rmse_err[-1])
```

    Mean Squared Error for Elastic Net Regression =  0.14645990270419076
    

## Step 4: Visualize the Performance of ML models


```python
# Array of ML models
models = ['Decision Tree','Ridge','GBM', 'RandomForest','Linear','SGD','Polynomial','Lasso','LassoLars','Bayesian Ridge','Passive Aggressive','ElasticNet']

# Performance of all ML models
dataframe = pd.DataFrame.from_dict({'ML_Models':models,'RMSE':rmse_err})
dataframe.sort_values(by='RMSE',ascending=True).plot(x='ML_Models',y='RMSE', kind='bar', sort_columns=True,figsize = (8,5),title='Performance of all ML models')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2085d75c588>




![png](output_58_1.png)


## Step 5: Choose the ML Models for Prediction

We can observe that, GBM and Ridge Regression gives very low value of Mean Squared Error. Hence, I have chosen these two ML models for the prediction. 


```python
# Plot Ridge Regression Results
import plotly
import plotly.graph_objs as go

# Train the model on training data.
ridge_Reg.fit(X_train, y_train)
# Perform an indirect sort using the ridge regression algorithm
index = np.argsort(-ridge_Reg.coef_)
# Select the features except 'competitorname' and 'winpercent'
cols = [f for f in list(df.columns.values) if f not in ['competitorname','winpercent']]
order = [cols[i] for i in index]

fig = go.Figure(data=[go.Pie(labels=order, values=ridge_Reg.coef_[index])])
fig.show()
```


        <script type="text/javascript">
        window.PlotlyConfig = {MathJaxConfig: 'local'};
        if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
        if (typeof require !== 'undefined') {
        require.undef("plotly");
        define('plotly', function(require, exports, module) {
            /**
* plotly.js v1.49.1
* Copyright 2012-2019, Plotly, Inc.
* All rights reserved.
* Licensed under the MIT license
*/
        });
        require(['plotly'], function(Plotly) {
            window._Plotly = Plotly;
        });
        }
        </script>
        



<div>
        
        
            <div id="af890fb8-3e5d-4c34-ab2a-4582aabc2c94" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("af890fb8-3e5d-4c34-ab2a-4582aabc2c94")) {
                    Plotly.newPlot(
                        'af890fb8-3e5d-4c34-ab2a-4582aabc2c94',
                        [{"labels": ["chocolate", "sugarpercent", "peanutyalmondy", "fruity", "bar", "crispedricewafer", "caramel", "pricepercent", "nougat", "pluribus", "hard"], "type": "pie", "values": [0.13159447665327906, 0.06391745785295026, 0.06274524643925276, 0.0424071131710989, 0.037906240354254134, 0.021328860715846814, 0.006782775427198847, 0.002397586403694287, 0.0001937557977458133, -0.033995284293263726, -0.03966007176483874]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}},
                        {"responsive": true}
                    ).then(function(){
                            
var gd = document.getElementById('af890fb8-3e5d-4c34-ab2a-4582aabc2c94');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>



```python
# Plot Gradient Boosting Regressor Results

# Train the model on training data.
gb_Reg = GradientBoostingRegressor(loss = 'ls', max_depth = 6, n_estimators = 20, learning_rate = 0.1)
gb_Reg.fit(X_train, y_train)

# Perform an indirect sort using the gradient boosting regression algorithm
i = np.argsort(-gb_Reg.feature_importances_)

cols = [cols[i] for i in index]

fig = go.Figure(data=[go.Pie(labels=cols, values=gb_Reg.feature_importances_[i])])
fig.show()
```


<div>
        
        
            <div id="2522905e-a61f-47ba-a005-85760268f30c" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("2522905e-a61f-47ba-a005-85760268f30c")) {
                    Plotly.newPlot(
                        '2522905e-a61f-47ba-a005-85760268f30c',
                        [{"labels": ["chocolate", "sugarpercent", "peanutyalmondy", "fruity", "bar", "crispedricewafer", "caramel", "pricepercent", "nougat", "pluribus", "hard"], "type": "pie", "values": [0.44357700349889045, 0.2314461406340732, 0.08334961590231059, 0.06855651487174742, 0.04453259387679476, 0.04117861484285652, 0.03757816225935835, 0.023373395902534816, 0.02288799057759827, 0.0033816827946253655, 0.00013828483921042212]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}},
                        {"responsive": true}
                    ).then(function(){
                            
var gd = document.getElementById('2522905e-a61f-47ba-a005-85760268f30c');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


## Step 5: Conclusion

From the above results we can visualize the feature importance and observe that 'chocolate' and 'sugarpercent' features have resulted in higher win percentage. Hence, I conclude that these two characteristics draw the customer sentiments and I hence I recommend to manufacture a new product with these ingredients in higher percentage for increased sale and win percentage.