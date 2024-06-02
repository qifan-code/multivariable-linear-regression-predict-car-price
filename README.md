# multivariable-linear-regression-predict-car-price
This project is aiming for predicting car price based on given dataset by linear regression. After processing, my MSE is 0.07 which means 93% accuracy. 
## Feature introduction
Car_ID			Unique id of each observation (Interger)		
Symboling 			Its assigned insurance risk rating, A value of +3 indicates that the auto is risky, -3 that it is probably pretty safe.(Categorical) 		
carCompany			Name of car company (Categorical)		
fueltype			Car fuel type i.e gas or diesel (Categorical)		
aspiration			Aspiration used in a car (Categorical)		
doornumber			Number of doors in a car (Categorical)		
carbody			body of car (Categorical)		
drivewheel			type of drive wheel (Categorical)		
enginelocation			Location of car engine (Categorical)		
wheelbase			Weelbase of car (Numeric)		
carlength			Length of car (Numeric)		
carwidth			Width of car (Numeric)		
carheight			height of car (Numeric)		
curbweight			The weight of a car without occupants or baggage. (Numeric)		
enginetype			Type of engine. (Categorical)		
cylindernumber			cylinder placed in the car (Categorical)		
enginesize			Size of car (Numeric)		
fuelsystem			Fuel system of car (Categorical)		
boreratio			Boreratio of car (Numeric)		
stroke			Stroke or volume inside the engine (Numeric)		
compressionratio			compression ratio of car (Numeric)		
horsepower			Horsepower (Numeric)		
peakrpm			car peak rpm (Numeric)		
citympg			Mileage in city (Numeric)		
highwaympg			Mileage on highway (Numeric)		
price(Dependent variable)			Price of car (Numeric)		

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

## Using Lasso Regression























