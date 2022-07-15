# importing libraries
import numpy as np
import pandas as pd
from datetime import datetime
from datetime import date
import calendar
import matplotlib.pyplot as plt
import seaborn as sn
%matplotlib inline
# loadind the data
train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')
# shape of training and testing data
train.shape, test.shape
# printing first five rows
train.head()
test.head()
# columns in the dataset
train.columns
test.columns
# Data type of the columns
train.dtypes
# distribution of count variable
sn.distplot(train["count"])
sn.distplot(np.log(train["count"]))
sn.distplot(train["registered"])
# looking at the correlation between numerical variables
corr = train[["temp","atemp","casual","registered","humidity","windspeed","count"]].corr()
mask = np.array(corr)
mask[np.tril_indices_from(mask)] = False
fig,ax= plt.subplots()
fig.set_size_inches(20,10)
sn.heatmap(corr, mask=mask,vmax=.9, square=True,annot=True, cmap="YlGnBu")
# looking for missing values in the datasaet
train.isnull().sum()
test.isnull().sum()
# extracting date, hour and month from the datetime
train["date"] = train.datetime.apply(lambda x : x.split()[0])
train["hour"] = train.datetime.apply(lambda x : x.split()[1].split(":")[0])
train["month"] = train.date.apply(lambda dateString : datetime.strptime(dateString,"%Y-%m-%d").month)
training = train[train['datetime']<='2012-03-30 0:00:00']
validation = train[train['datetime']>'2012-03-30 0:00:00']
train = train.drop(['datetime','date', 'atemp'],axis=1)
test = test.drop(['datetime','date', 'atemp'], axis=1)
training = training.drop(['datetime','date', 'atemp'],axis=1)
validation = validation.drop(['datetime','date', 'atemp'],axis=1)
from sklearn.linear_model import LinearRegression
# initialize the linear regression model
lModel = LinearRegression()
X_train = training.drop('count', 1)
y_train = np.log(training['count'])
X_val = validation.drop('count', 1)
y_val = np.log(validation['count'])
# checking the shape of X_train, y_train, X_val and y_val
X_train.shape, y_train.shape, X_val.shape, y_val.shape
# fitting the model on X_train and y_train
lModel.fit(X_train,y_train)
# making prediction on validation set
prediction = lModel.predict(X_val)
# defining a function which will return the rmsle score
def rmsle(y, y_):
    y = np.exp(y),   # taking the exponential as we took the log of target variable
    y_ = np.exp(y_)
    log1 = np.nan_to_num(np.array([np.log(v + 1) for v in y]))
    log2 = np.nan_to_num(np.array([np.log(v + 1) for v in y_]))
    calc = (log1 - log2) ** 2
    return np.sqrt(np.mean(calc))
    rmsle(y_val,prediction)
    # uncomment it to save the predictions from linear regression model and submit these predictions to generate score.
# test_prediction = lModel.predict(test)
from sklearn.tree import DecisionTreeRegressor
# defining a decision tree model with a depth of 5. You can further tune the hyperparameters to improve the score
dt_reg = DecisionTreeRegressor(max_depth=5)
dt_reg.fit(X_train, y_train)
predict = dt_reg.predict(X_val)
# calculating rmsle of the predicted values
rmsle(y_val, predict)
test_prediction = dt_reg.predict(test)
final_prediction = np.exp(test_prediction)
submission = pd.DataFrame()
# creating a count column and saving the predictions in it
submission['count'] = final_prediction
submission.to_csv('submission.csv', header=True, index=False)
sub=pd.read_csv("submission.csv")
sub
[Problem Statement.pdf](https://github.com/AnkitSahoo15/Reression_Internshala_Ankit-Sahoo/files/9124070/Problem.Statement.pdf)
[solution_checker.xlsx](https://github.com/AnkitSahoo15/Reression_Internshala_Ankit-Sahoo/files/9124071/solution_checker.xlsx)
[test.csv](https://github.com/AnkitSahoo15/Reression_Internshala_Ankit-Sahoo/files/9124072/test.csv)
[train.csv](https://github.com/AnkitSahoo15/Reression_Internshala_Ankit-Sahoo/files/9124073/train.csv)
