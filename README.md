# Price Recommendation for Online Retail Sellers


This project was prepared as part of Bachelor's Graduation Projcet at <a href="http://www.boun.edu.tr/en-US/Index">Bogazici University</a> 
Department of Management Information Systems in 2021. 
The aim of the project is to recommend selling price for products according to their features such as category, brand etc. in order to enable online retail sellers to better price their products.

## Data

Data of this project is collected from a second-hand e-commerce platform in Turkey with the help of their Data Analytics team. 
The data contains information about sold products between 16th of February and 16th of June of 2021. 
There are a total of 2.556.419 rows meaning unique sold products and 18 columns. 
5 columns are excluded from this project since four of them were ID’s of sellers, products, categories and brands and the other one was product status which is ‘SOLD’ in all the rows in the dataset.
The data was extracted from the database of the collaborated company, and while extracting, most of the missing values were already eliminated by building right queries in the Google Cloud Platform, Big Query. Later, in Python, 83.092 duplicate rows are deleted in product_id.

## Exploratory Data Analysis (EDA)
Before applying predictive models, it is significant to perform initial investigations on data. 
In the EDA part, firstly, the distribution of the sold price is analyzed. 
As can be seen below, the histogram of the sold price is right-skewed which indicates that the mean is greater than the median.
Most of the products’ sold prices are in the range of 0-250TL.

<img width="525" alt="SoldPriceDist-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140924196-2fcb9023-ff7d-42c2-bfb2-23e1e4fc2232.png">

To remove the skewness of the data, log transformation is applied to sold_price.

<img width="525" alt="SoldPriceDistLog-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140924217-a29c35d5-238f-4626-b8d9-551d2c753d2c.png">

How the sold price changed when the seller or the buyer paid for the shipment is examined. As expected, product prices were higher when the seller paid for the shipment.

<img width="511" alt="PriceDistShipment-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140924455-3943ec03-aec7-44bb-861d-854cf7723ade.png">

Stop words in the data are not desired since only meaningful data should be included in the model. Stopwords in Turkish have been eliminated by dividing each text in the item description section into words and then removing the word if it exists in the list of stop words. In addition, it is important to delete stopwords that are not useful for the regression models to improve process time. The following codes display the logic behind how the stopwords, punctuations and spaces are deleted:

```Python
def text_preprocess(value, stopwords):
value = value.replace('\\r', ' ')
value = value.replace('\\"', ' ')
value = value.replace('\\n', ' ')
value = ' '.join(e for e in value.split() if e not in stopwords)
return value.lower().strip()
```
## Outliers
IQR scores is one of the ways of using mathematical functions to eliminate outliers. The IQR approach is used by Box Plot to display data and outliers (data shape), but in order to receive a list of detected outliers, we must utilize a mathematical calculation and extract the outlier data. The IQR is the first quartile minus the third quartile.

<img width="513" alt="BoxPlotSoldPrice-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140925553-f36db13f-ce0d-4f55-902f-024d92c20fd1.png">

As can be understood, the range of the sold price in our data is very wide (10TL to 25.000TL with the mean of 82TL) and the frequency in the high prices is low. Only the data that lies within lower and upper limits are statistically considered normal and to find the exact upper and lower limits, an IQR approach is used. 

## Correlation

<img width="865" alt="CorrMatrix-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140926291-8d55d1d6-6149-44b6-bcdf-ebe5f6cfbe5f.png">

As it can be seen in the correlation matrix, price and original_price have quite a stronger correlation with sold price. Since the price variable corresponds to the listing price of the product, it is not included in the model. Accordingly, sold and original price means by brand and third level titles has strong positive correlation between the sold price variable, so they are included in the model. Also, discount_rate_by_brand_title has a positive correlation between the sold price. The other variables are dummy variables, so they are not considered.

## Normalization

The data has three continuous variables (original price, sold price and item description word count) exist. Other continuous variables are also derived using these variables. The discount rate is calculated using the formula (Original Price - Sold Price) / Original Price. Sold price and original price averages were calculated separately in the brand title and third-level title groups. As a result Log and Box Cox transformations are applied to “sold_price_mean_by_thirdlevel_title_”, “original_price”, “original_price_mean_by_thirdlevel_title_”, “original_price_mean_by_brand_title”, “sold_price_mean_by_brand_title” columns.

## Categorical Variable Encoding

One of the important parts in feature engineering is turning categorical variables into numerical variables. Because many machine learning algorithms cannot operate directly on categorical data, they require all input variables and output variables to be numeric. This project is applied two methods for treating categorical variables: one hot encoding and dummy encoding. Even though dummy variable coding comes from the statistics field and one-hot encoding comes from the computer science field, in the end they are doing exactly the same thing. So, in this project, dummy variables are created from the 3 categorical variables: brand type, condition and shipment term. Moreover, dummy variables created from third level category titles. However, there were over 500 dummy variables, so it was eventually abandoned for the model performance and interpretability.

## Models

In this project, different regression models, normalization techniques, outlier deletion applications and feature engineering techniques are applied to create a model to accurately suggest a product’s selling price. The data is divided into two; 20% for test data and 80% for train data, and is used for all the regression models developed. In this way, the performance of the model can be evaluated.

Models are created based on 3 regression models: Linear (OLS), Ridge and Lasso. Ridge and Lasso models performed regularization and an alternative to subset selection. From the theoretical knowledge, it was expected Ridge regressions to have higher prediction accuracy then the Lasso regressions.

The best performing model was a Ridge regression and it performed the best R² and lowest Roor Mean Squared Error value which are R² of 66% and RMSE score of 25. This means that the model is predicting the sold price with +/- 25TL error margin and the model explains 66% of the data. In this model, original and sold price outliers in both third level and brand title groups were determined and eliminated according to the IQR approach. Moreover, 8 continuous variables and 9 categorical variables were inserted into the model. While using the mean of the original price and sold price in different groups, discount rate, etc. as continuous variables; the product's status, shipment term, brand type in dummy format are used as categorical variables. Normalization (log and box cox transformations) was not performed in this model. 

Other model results and details of these model can be seen in the below tables:

<img width="572" alt="modelScores-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140928997-a8870dc6-b102-47bd-80e7-e65dad7927b7.png">

<img width="886" alt="modelDetails1-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140929017-e7519923-7552-49eb-b270-4d488d26aeaa.png">

<img width="886" alt="modelDetails2-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140929020-62d8f5cb-a541-42d6-9f7e-d80ed61a9961.png">

<img width="886" alt="modelDetails3-PriceRecom" src="https://user-images.githubusercontent.com/45122094/140929033-c3b7ce6b-5fbe-4cb2-9bfe-6801ea7456fe.png">

 








