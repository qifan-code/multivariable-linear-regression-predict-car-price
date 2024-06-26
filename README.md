# multivariable-linear-regression-predict-car-price
This project is aiming for predicting car price based on given dataset by linear regression. After processing, my MSE is 0.07 which means 93% accuracy. 
## Feature introduction
|feature|introduction|
|-|-|
|Car_ID|Unique id of each observation (Interger)|
|Symboling|Its assigned insurance risk rating, A value of +3 indicates that the auto is risky, -3 that it is probably pretty safe.(Categorical)|	
|carCompany|Name of car company (Categorical)|
|fueltype|Car fuel type i.e gas or diesel (Categorical)|
|aspiration|Aspiration used in a car (Categorical)|
|doornumber|Number of doors in a car (Categorical)|
|carbody|body of car (Categorical)|
|drivewheel|type of drive wheel (Categorical)|
|enginelocation|Location of car engine (Categorical)|
|wheelbase|Weelbase of car (Numeric)|
|carlength|Length of car (Numeric)|
|carwidth|Width of car (Numeric)|
|carheight|height of car (Numeric)|
|curbweight|The weight of a car without occupants or baggage. (Numeric)|
|enginetype|Type of engine. (Categorical)|
|cylindernumber|cylinder placed in the car (Categorical)|
|enginesize|Size of car (Numeric)|
|fuelsystem|Fuel system of car (Categorical)|
|boreratio|Boreratio of car (Numeric)|
|stroke|Stroke or volume inside the engine (Numeric)|
|compressionratio|compression ratio of car (Numeric)|
|horsepower|Horsepower (Numeric)|
|peakrpm|car peak rpm (Numeric)|
|citympg|Mileage in city (Numeric)|
|highwaympg|Mileage on highway (Numeric)|
|price(Dependent variable)|Price of car (Numeric)|

## Data Cleaning Process
For dataset contains both category and numeric data, we need to first separate them then do following process:
1. Category data: convert them into numeric data. (in R, we use as.factor() function) For this dataset, we have an exception on "door number" column which directly uses "two" and "four". Here, I treat them as category and convert them into corresponding numbers further. 
2. Numeric data: create a subset of numeric data then check for outliers and normalize them.

For "CarName" feature, it has a combination of car company and car model. So I use str.split() function to separate it into 2 features: "Company" and "Model". 

Correcting typos:
1. After splitting, I checked "Company" feature. It uses both "maxda" and "mazda" for Mazda. So I use str.replace() function to replace all of them. Same process for "nissan" vs "Nissan", "porcshce" vs "porshe" and "toyouta" vs "toyoya".
2. For "Model" feature, it contains so many separate models for Civic such as 'civic 1300', 'civic cvcc, 'civic (auto)', 'civic 1500 gl' as well as 'civic' itself. So I replace all values that has a substring of "civic" to "civic".
3. I then check all other category features, for "enginetype", it shows me:('dohc', 'ohcv', 'ohc', 'l', 'rotor', 'ohcf', 'dohcv'). However, after searching online, there is no introduction of "ohcv". Before I started merging them to 'ohc', I checked the number of rows contains 'ohcv' which shows me 14 rows in total. Therefore, I just leave them unchanged but it still confuses me till now.

For several features such as "Car_ID", I remove them because its useless for linear regression.

## check Linear Regression Assumption
For Linear Regression model, we must made the following assumptions: Normality, Independence, Linearity, Homoscedasticity. If one of them is violated, we must do transformation to dataset such as powerTransformation, Lasso/Ridge Regression etc. 

### Normality
I use Q-Q plot here to check for Normality. From Q-Q plot, all the points are following diagnose line in the graph. Therefore, our target value(car price feature) is normally distributed. 
There are other methods we can use: density plot, residual plot, or Shapiro-Wilk Test for smaller dataset(n < 50). 

### Independence
I use Durbin-Watson Test here. My p-value is very large. So my features are all independent to dependent value. Also, based on my p-value = 0.92, it shows a potential multicollinearity issue that I will demonstrate it further. 

### Linearity
For checking Linearity, I use Harvey-Collier Test and residual fitted plot. But, I failed to implement on Harvey-Collier Test and my residual fitted plot is kind of separated by smooth line. So I dont think this dataset violate Linearity. Further, I will try to fix the bugs and try to use powerTransformation test to check if power transformation is necessary needed.

### Homoscedasticity
To check homoscedasticity, we mainly use ncv() Test in R. But in python, we use the Breusch-Pagan test. After testing, my p-value is less than 0.05 so homoscedasticity passed. 

## Remove outlier
For outliers, we cant simply use 1.5-IQR Rule because for me, it is an entry level of static. There are so many outliers such as studentized residuals, high leverage points and high cook's distances points. Here are outlier selection rules based on these three kinds of points: 
1. Studentized residuals greater than 2 or less than -2
2. High leverage points (greater than 2*(k+1)/n where k is the number of predictors and n is the number of observations)
3. High Cook's distance (commonly used threshold is 4/(n-k-1))

After this process, I found about 20 outliers. Then I remove all of them from dataset. 

## Multicollearity
From previous chapter, in Durbin-Watson Test, it indicates my dataset may have multicollearity issue. Here, I use vif to check my dataset. After running program, there indeed some features has vif score greater than 5 such as "curbweight", "horsepower" etc. 
To avoid this issue, I will perform PCA, Lasso Regulation and feature selection to get more accurate model. 

Note: 
usually vif is to check numeric features. If some category features has large vif, just ignore them. 

## PowerTransformation
As previous talked, I didnt find any issue of Linearity. So PowerTransformation is no needed. But, for further analysis, I will improve my code of running transformation test to check if it is needed. So I just set it as "TODO" here. 

## Basic implementation of using all features
After checking those points, I tried to make a naive implement of multivariable linear regression. Based on summary report, it shows: 
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
[2] The smallest eigenvalue is 7.19e-27. This might indicate that there are strong multicollinearity problems or that the design matrix is singular.

So I will use several strageties to solve multicollinearity issue. 

## Method1: Using Lasso Regression
### Introduction of Lasso and Ridge
Lasso Regression(L1) and Ridge Regression(L2) are useful tools to prevent multicollinearity and overfitting. 
Lasso is usually used if there are so many features and we just want a litte subset of those features. 
Ridge is usually used when #rows < #features and if noise data happens in training data. 

### Lasso Regression parameter adjustment --- using loop
For python Lasso method, I run parameter adjustment by nested loop to find the best combination of parameters. The results are: alpha = 0.1, max_iter = 100, tol = 0.01 to generate with mse = 0.099421, r2 = 0.800997. 

Note: 
I use mse and r2 to determine the best parameter. For same lowest mse value, I pick the highest r2 value. 

### get features and coefficient
After running Lasso, I get a list of features with their coefficients in linear regresssion model. But most of them have coefficient equals or less than 0. So I reject them all and pick the positive ones to run linear regression model. At last my model has mse = 0.1127 with the following features and their coefficients: 
wheelbase     0.007559
carwidth      0.163034
curbweight    0.304283
enginesize    0.381289
fuelsystem    0.005185
intercept: 
-0.06580978126963455

### Rescale coefficient
Probabily you have noticed that my coefficient here is pretty small. Thats because I normalized them in Data Clean process. Here are equations to roll back: 
normalization euqation:

$$X_{\text{normalized}} = \frac{X - \mu}{\sigma}$$
rescale coefficient equation:
$$\beta_{\text{original}} = \frac{\beta_{\text{normalized}}}{\sigma}$$
rescale intercept equation:
$$\text{intercept}_{\text{original}} = \text{intercept}_{\text{normalized}} - \sum \left( \frac{\beta_{\text{normalized}} \times \mu}{\sigma} \right)$$

## Method2: Using PCA
### Introduction to PCA
PCA stands for Principal Component Analysis. It's a statistical procedure used primarily to reduce the dimensionality of a dataset while retaining as much variability (information) as possible. 
If a dataset has multicollinearity issue, we can run PCA on and ONLY on numeric features to avoid this issue. 

### Perform PCA
Before running PCA model, I use Scree Plot to check my available n_factors value. 
![1717303122864](https://github.com/qifan-code/multivariable-linear-regression-predict-car-price/assets/64823500/a8a634b6-b4f7-4480-a2e3-1f48788202e7)
From this figure, I need to find "knee" point which means before this points, plot is decreasing so fast and after this point, plot becomes flat. 
My optional n_factors values are: 2, 3, 4, 5, 6

Then I run PCA for each of them to see how much variance they captured:
|n_factors|variance captured|
|---------|-----------------|
|2|0.685205|
|3|0.788831|
|4|0.858844|
|5|0.903513|
|6|0.931659|

my choice here is to pick n_factors = 4 because: 
1. usually variance captures between 85% to 90% is the optimal solution.
2. although n_factors = 3 is a clear knee point, it only capture 78.8% variance.
3. n_factors = 4 reaches the bottom level of requirements.
4. although other values can capture more variance, it has a trade-off between time consuming and variance. So for n_factors = 5 or 6, I will ignore them.

### Combine PCA with category dataset to form a new dataset
Before I run PCA, I separate dataset into numeric and category part. So after that, I combine them to form a new dataset. 

### Run Lasso and feature selection
Although I reduced dimension of numeric features, there are lots of category features. I need Lasso or stepwise to reduce number of features. 
#### Lasso regualarlization result
For Lasso regualarlization, I run parameter adjustment to get the best combination of parameters: 
alpha = 0.1, max_iter = 100, tol = 0.1 generate a result of mse = 0.12, r2 = 0.742516

Then I use this model to select my features: 
PCA1          0.274638
fuelsystem    0.006599
intercept: 
-0.16537733126361487

This might be confused because this model only contains 2 features. But for each PCA column we generated, it is a combination of each numeric feature. So Lasso indeed reduce several category features and give us a model with mse = 0.12, accuracy = 0.88. 

### Run feature selection
General ways of feature selection contains 3 methods: forward selction, backwards selection and stepwise selection. 
#### Introduction to these methods
1. forward selection: start with empty sets. Every time, sdds the variable that provides the most significant improvement to the model. When features left has no contribution to model performance, it stops.
2. backward selection: start with all features set. Evert time, removes the least significant variable (the one with the highest p-value above a certain threshold) at each step. When removing more variables does not improve the model performance or all remaining variables are significant, it stops.
3. stepwise selection: a combination of forward and backward selection. Begins like forward selection by adding variables, but after adding each new variable, it checks if any variables previously added have become statistically insignificant and should be removed. Stops when no further variables can be added or removed to improve the model.

#### pick optimal methods
My function basically calculate AIC, BIC and R2 for each selection method. For AIC/BIC, the lower the better. For R2, the higher the better. 
Based rules above, I run linear regression model for selected features:
|method|mse|features|
|------|-|-|
|forward|0.1138|'PCA1', 'Company', 'drivewheel', 'carbody'|
|backward|0.1176|'PCA1', 'PCA2', 'PCA4', 'fueltype', 'doornumber', 'carbody', 'drivewheel', 'enginetype', 'cylindernumber', 'Company', 'Model|
|stepwise|0.1138|'PCA1', 'Company', 'drivewheel', 'carbody'|

## Method3: Using feature selection
As I introduced before, we can use forward, backward and stepwise selction. 
Here is my result: 
|method|mse|features|
|------|-|-|
|forward|0.081|'curbweight', 'enginesize', 'peakrpm', 'stroke', 'compressionratio', 'Company', 'citympg', 'wheelbase', 'horsepower', 'enginetype', 'boreratio'|
|backward|0.079|'fueltype', 'aspiration', 'doornumber', 'carbody', 'enginelocation', 'wheelbase', 'curbweight', 'enginetype', 'enginesize', 'boreratio', 'stroke', 'compressionratio', 'peakrpm', 'citympg', 'highwaympg', 'Company'|
|stepwise|0.0815|'enginesize', 'peakrpm', 'stroke', 'compressionratio', 'Company', 'citympg', 'wheelbase', 'horsepower'|

## cross validation
To get the most accurate score of my model, I need run cross validation of dataset. Here I use 10-fold cross validation to generate my mse = 0.07580652185151038 which means accuracy = 92.4%

## check overfitting or underfitting
To check overfitting or underfitting issue, I run my model on training dataset and testing dataset. 
|dataset|mse|
|-------|---|
|training|0.0531|
|testing|0.0788|

There are not too much difference. So my model has neither overfitting nor underfitting issue. 

## TODO list
1. try to implement better method for testing linearity.
2. run powerTransformation to get suggested feature transformation.
3. although my model performs well, it still has multicolliearity issue for a little bit. I need find some ways improving it.
4. run non-linear regression model to see if there is any improvement for accuracy. 










