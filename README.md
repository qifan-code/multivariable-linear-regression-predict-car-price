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
For several features such as "Car_ID", I remove them because its useless for linear regression.

## 









