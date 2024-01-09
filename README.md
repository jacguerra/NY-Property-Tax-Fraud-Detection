# NY-Property-Tax-Fraud-Detection

**Executive Summary**

In this project, New York City property data was investigated for potential tax fraud. The purpose of the project was to identify potentially misrepresented property characteristics for the city of New York. Given that there were no previously identified fraudulent properties, the methodology involved identifying records with unusual field values that can be investigated further for potential fraud.

Unsupervised machine learning methodologies were used to score properties based on their unusualness, and the final product was a list of ranked properties with calculated outlier scores for each property dimension. Additionally, preliminary investigations have been done on 5 of the outlier properties to evaluate the effectiveness of the algorithm. The scored property list is now ready for domain experts to further investigate, and property identify, fraudulent properties.

**Description of Data**

The data is comprised of property valuations and assessments in New York City. The property valuation data is updated annually by the NYC Department of Finance in order to calculate property tax bills. The dataset has 32 fields and 1,070,994 records. Of the fields, 14 are numerical and 18 are categorical.

**Data Cleaning**

In terms of data cleaning, the OWNER field was updated to remove government, city or state buildings that were not of interest. A list of common words found in government buildings was compiled and any OWNER records containing those words was removed, resulting in the removal of 27,188 records.

The data set also contained substantial missing values that required imputation. For ZIP, records with missing values were first imputed with the previous records zip code if the value of the zip code before and after the record were the same, due to the dataset being sorted by zip code. Any remaining missing zip codes were imputed with the previous records value. For FULLVAL, AVLAND, AVTOT and STORIES, the average value of each field by TAXCLASS was calculated, and any missing values were imputed with the corresponding mean for the TAXCLASS of the record. The same mean by TAXCLASS imputation was also done for LTFRONT, LTDEPTH, BLDFRONT and BLDDEPTH with the additional step of converting unrealistic values of 0 or 1 to N/A before imputation.

**Feature Engineering**

Additional expert variables were created to detect any anomalies in the data that could indicate property tax fraud. Specifically, variables were created to detect unusual property values for that property’s characteristics. These anomalies were valuable because they could indicate that a property’s characteristics were purposefully being misrepresented to commit tax fraud. This included variables that calculated the price per square foot of each property as well as price per square foot by tax class and zip code. In total there were 60 variables at the conclusion of feature engineering.

**Dimensionality Reduction**

Principal Component Analysis (PCA) was used to reduce dimensions and eliminate linear correlations. For this project, before doing the PCA the data was first Z-scaled, which scales and centers the data, so each feature had a mean of 0 and a standard deviation of 1. This was an important step because non-centered data could affect the performance of the PCA. The PCA was then run, and each PC was sorted by its magnitude. Out of 32 PCs, a total of 5 were kept that together accounted for ~75% of the variance, greatly reducing the dimensionality of the data. The final PCs were then Z-scaled once again to give each of the PCs equal importance and convert the data values into zscores that represent the outlierness of each variable.

**Two Score Algorithms**

Two separate anomaly scores were calculated, using both the Minkowski distance and an auto-encoder. After dimensionality, the zscores were centered around the origin with the same scale for every dimension. Because of this preparation, the Minkowski distance could be calculated as the distance from the origin using the formula below:

![image](https://github.com/jacguerra/NY-Property-Tax-Fraud-Detection/assets/113937618/3268ba6e-8592-4a28-8504-a3ec4e3b9d3a)

Where si is the anomaly score for the ith record, which is equal to the distance of the ith point to the origin to the power of 1/p. The p value chosen to calculate the Minkowski distance was p=2, which calculated the Euclidean or straight-line distance to the origin of each point.

The second score was calculated using an auto-encoder. An auto-encoder is a model trained to output the original vector input and the score is calculated as the difference between the original input vector and the model output vector. The difference is calculated using the Minkowski distance between the original value and the output, as seen in the equation below: 

![image](https://github.com/jacguerra/NY-Property-Tax-Fraud-Detection/assets/113937618/8525c16e-f53e-41c8-a93a-9a3968c8b623)

Where si is the anomaly score for the ith record, and the power p is a chosen value. The auto-encoder can generally reproduce normal records well, but has larger error when reproducing unusual values, making it a valuable score for the unusualness of a record. For this project, a neural net with 3 hidden layers was used as the auto-encoder and p=2 for calculating the auto-encoder score.

After the scores were calculated, each was separately ranked from high to low to put them on the same scale. Once the rank-order was performed, the scores were combined by taking the average of the two scores, with each score getting an equal weight of 0.5 in the average. 

**Results**

The final score was then used to sort the data from high to low unusualness for further investigation. The zscores of each variable were also used to determine unusualness as they represented how many standard deviations a value was from the mean. The zscores were also valuable in determining which aspects of the property were unusual, exposing potentially misrepresented property values. 

Utilizing the sorted data, unusual properties were investigated in order to determine the effectiveness of the algorithm. Additionally, investigating the individual aspects of unusual properties helped determine if the algorithm was catching actual fraud or properties that were either genuinely strange or had incorrect values in the dataset. The zscores were a good starting point to identify which property values were potentially inaccurate, and further investigation was done using Google Maps to determine if the values in the data matched with the actual building.  

Based on further investigation of the high scored properties, the algorithm was effective at identifying unusual properties with misrepresented characteristics. 5 interesting cases were found that demonstrated that buildings with unusual property values were being caught by the algorithm, and the created variables were valuable for exposing outlier values. In summation, the scored property list is effective at identifying outlier properties and is ready for further fraud investigation by domain experts.



