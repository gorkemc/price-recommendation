# Price Recommendation for Online Retail Sellers


This project was prepared as part of Bachelor's Graduation Projct at <a href="http://www.boun.edu.tr/en-US/Index>Bogazici University</a> 
Department of Management Information Systems in 2021. 
The aim of the project is to recommend selling price for products according to their features such as category, brand etc in order to enable online retail sellers to better price tehir products.

## Data

Data of this project is collected from a second-hand e-commerce platform in Turkey with the help of their Data Analytics team. 
The data contains information about sold products between 16th of February and 16th of June of 2021. 
There are a total of 2.556.419 rows meaning unique sold products and 18 columns. 
5 columns are excluded from this project since four of them were ID’s of sellers, products, categories and brands and the other one was product status which is ‘SOLD’ in all the rows in the dataset.

## Exploratory Data Analysis (EDA)
Before applying predictive models, it is significant to perform initial investigations on data. 
In the EDA part, firstly, the distribution of the sold price is analyzed. 
As can be seen below, the histogram of the sold price is right-skewed which indicates that the mean is greater than the median.
Most of the products’ sold prices are in the range of 0-250TL.



To remove the skewness of the data, log transformation is applied to sold_price.
