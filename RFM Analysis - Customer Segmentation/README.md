# Customer Segmentation Using RFM Analyis and Kmeans


![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/rfm%20segments.png?raw=True)

## 4 Segments 

### RFM Segment 1: Best Customer

This segment consists of frequent customers who engage in substantial spending and have made recent purchases.

In terms of gender, age, and income distribution, this segment mirrors the overall sample.
While their average income is slightly lower than other segments, it remains considerable.
A significant number of customers in this segment joined the app during 2016 and 2017, with the highest concentration joining between 2013 and 2015.
Notably, this segment boasts the highest proportion of individuals belonging to Generation Z and Millennials.

Offers yield remarkable results with this group, exhibiting high view and completion rates across various offer characteristics such as categories, difficulty levels, durations, and rewards.

### RFM Segment 2: High Value

In this segment, you'll find customers who are frequent spenders, although it has been a significant duration since their last transaction.


The age and income distribution of this segment aligns with the overall sample.
An almost equal number of male and female customers are present. Additionally, this segment showcases the highest proportion of females and those identifying as 'Others' compared to other segments.
While the average age is comparable to other segments, it's noteworthy that this group has the highest proportion of individuals falling into the GenX category and the least proportion of GenZ customers.
This segment boasts the highest average income among all segments. Furthermore, it has the largest representation of individuals earning above 75k and the least representation of those earning under $50k.
Most of the customers in this segment joined the app during 2016 and 2017, with the majority joining in 2016.

Overall, offers show solid performance within this segment, closely rivaling the performance in the 'Best Customers' segment.
Particularly notable is their slightly superior performance in areas such as BOGO offers, offers with a difficulty level of 10, a duration of 5, and a reward of 10.

### RFM Segment 3: Lost Customer

This segment comprises customers who infrequently make purchases, spend minimally, and haven't engaged in recent transactions.

Gender, age, and income distribution aligns with the overall sample.
Notably, there is a significantly higher representation of males and a lower representation of females within this segment compared to the overall sample.
This segment encompasses a relatively high proportion of individuals categorized as Boomers and Matures.
Interestingly, a substantial proportion of high-income customers are part of this segment.
The majority of customers in this segment joined the app between 2017 and 2018, with the smallest portion having joined between 2013 and 2016.

As anticipated, this segment demonstrates inferior performance across all offer categories.
While customers in this segment tend to view a substantial number of the offers sent to them, the conversion rates for these offers remain notably lower.

### RFM Segment 4: New Customers

Within this segment, you'll find customers who are recent shoppers, displaying a low frequency of purchases and smaller transaction amounts.

Gender, age, and income distribution align with the overall sample.
This segment includes the highest proportion of male customers and the lowest proportion of females within the entire sample.
A notable characteristic of this segment is the highest representation of individuals earning less than 50k.
The majority of customers in this segment joined the app during 2017 and 2018.

Due to their recent arrival, new customers exhibit relatively modest offer performance.
Nevertheless, their performance consistently surpasses that of the Low Value segment.

# Project Flow 

## Data Loading

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/load.png?raw=True)

## Data Cleaning 

### Drop NULL vvalues
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/null%20val.png?raw=True)

### Parse Date

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/parse%20date.png?raw=True)

### Extract Values from value column

![](?raw=True)
## EDA

### GENDER 
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/age.png?raw=True)
### AGE

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/age%20dist.png?raw=True)

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/age%20grp.png?raw=True)

### INCOME

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/income%20dist.png?raw=True)

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/income%20range.png?raw=True)

### YEAR
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/year.png?raw=True)

### MONTH 

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/month.png?raw=True)

### WEEKDAY
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/weekday.png?raw=True)

### AGE VS GENDER
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/age%20vs%20gender.png?raw=True)

### INCOME VS GENDER
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/income%20vs%20gender.png?raw=True)

### INCOME VS AGE
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/income%20vs%20age.png?raw=True)

### OFFER TYPE
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/offer%20type.png?raw=True)

### OFFER NAME
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/offer%20name.png?raw=True)

### CHANNELS
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/channel%20.png?raw=True)

### REWARD
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/reward.png?raw=True)

### DIFFICULTY
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/difficulty.png?raw=True)

### DURATION
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/duration.png?raw=True)

### EVENT 
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/events.png?raw=True)


## Data Manipulation

## Encoding Portfolio using MultiLabelBinarizer() 
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/multilabel.png?raw=True)

## Encoding Profile using get_dummies()
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/profile%20encode.png?raw=True)

## Creating Seperate dataframes for offers and transactions
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/transcript%20mani.png?raw=True)

## Calculating View Rate and Completion Rate
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/vr%20.png?raw=True)

## RFM Analysis 
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/rfm%20code.png?raw=True)

## Scale Data
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/scale.png?raw=True)

## Kmeans

## Elbow Method
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/elbow%20method.png?raw=True)

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/kmeans.png?raw=True)

## Visualize the segments 
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/rfm%20segments.png?raw=True)

![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/rfm%20viz.png?raw=True)

### GENDER
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/gender%20viz.png?raw=True)

### AGE
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/age%20viz.png?raw=True)

### INCOME
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/income%20viz.png?raw=True)

### YEAR
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/year%20viz.png?raw=True)

### BOGO
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/bogo%20viz.png?raw=True)

### DISCOUNT
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/discount%20viz.png?raw=True)

### INFORMATIONAL
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/info%20viz.png?raw=True)

### CHANNEL
![](https://github.com/Tushara08/Portfolio-Projects/blob/main/RFM%20Analysis%20-%20Customer%20Segmentation/images/channel%20viz.png?raw=True)


