# H2O AutoML Classification in R #

H2O AutoML interface is designed to have as few parameters as possible so that all the user needs to do is point to their dataset, identify the response column and optionally specify a time constraint, a maximum number of models constraint, and early stopping parameters.

## Dataset ##
The dataset used in this example can be obtained from here:
 - [titanic_list.csv](https://raw.githubusercontent.com/Avkash/mldl/master/data/titanic_list.csv)

Note: Use "wget" and above links to pull the the data locally or use the URL above directly to load data into H2O.
  
## Get the Sample R Code ##
  - [H2O AutoML Classification with Titanic Dataset R Code](https://github.com/Avkash/mldl/blob/master/code/R/H2O_AutoML_Classification_titanic.R)
  
## H2O AutoML Binomial Classification Sample Code in R ##


###: Importing H2O Library
```
library(h2o)
```


###: Initializing h2O
```
h2o.init()
```


###: Getting H2O Cluster status
```
h2o.clusterStatus()
```


###: Importing Dataset
```
df = h2o.importFile("https://raw.githubusercontent.com/Avkash/mldl/master/data/titanic_list.csv")
```


###: Understanding Dataset
```
h2o.describe(df)
```


###: Getting a list of columns in our dataset
```
h2o.colnames(df)
```


###: Setting Response column
```
response = "survived"
```


###: Setting Response column to factor so we can get unique values in it
```
h2o.levels(h2o.asfactor(df$survived))
```


###: Setting Response column to factor so we can build classification model
```
df[response] = h2o.asfactor(df[response])
```


###: Verifying Response column as factor 
```
h2o.describe(df[response])
```

###: spliting the dataset into train, test and validation  
```
df_splits = h2o.splitFrame(df, ratios = c(0.8,0.1))
train = df_splits[[1]]
valid = df_splits[[2]]
test = df_splits[[3]]
print(nrow(train))
print(nrow(valid))
print(nrow(test))
```


###: Setting all columns/features into a local variable
```
features = h2o.colnames(df)
```


###: Settings all features for model building
```
features = setdiff(features, response)
print(features)
```


###: Ignoring list of features which are not required for model building
```
features = setdiff(features, c('name', 'ticket', 'home.dest'))
print(features)
```


###: Using H2O AutoML Library to train a collection of models
```
titanic_automl = h2o.automl(max_models= 100,
                           seed=1234,
                           max_runtime_secs=60, 
                           project_name= "Titanic AutoML",
                           x = features, y = response,training_frame=train,validation_frame=valid)
```


###: Getting the list of models created by AutoML in given time
```
titanic_automl@leaderboard
```


###: Getting the best model or Leader model from the AutoML leader board
```
titanic_leader = titanic_automl@leader
```


###: Understanding best Model (Leader Model) overall performance based on training data
```
h2o.performance(titanic_leader, valid = TRUE)
h2o.auc(titanic_leader, valid = TRUE)
```

###: Generating Variable Importance Plot for the Leader Model
```
h2o.varimp_plot(titanic_leader)
```


###:  H2O AutoMLmodel varialbe Scoring History
```
h2o.scoreHistory(titanic_leader)
```

### PERFORMING PREDICTIONS USING a TEST DATA SET with Auto ML Leader model
```
h2o.predict(titanic_leader, newdata = test)
```


###: Getting AUC metrics from the test data using AutoML Leader Model
```
h2o.performance(titanic_leader, newdata = test)
h2o.auc(titanic_leader)
```
