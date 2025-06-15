# Marketing Campaign Response Analysis

## Project Overview
This project analyzes marketing campaign data from a retail company to understand customer behavior in response to various direct marketing efforts. The dataset contains customer demographics, spending behavior across multiple product categories, and response outcomes to five different campaigns.

## Dataset
The dataset was obtained from Kaggle and imported from the marketing_campaign.csv file. [It](https://github.com/deaanisaaulia/campaign-response-analysis/blob/main/marketing_campaign.csv) covers customer data collected between 2012 and 2014, and the analysis assumes 2015 as the reference year based on the Year_Birth variable.

## Objective
To explore strategic business questions regarding campaign performance, customer segmentation, and purchase behavior, with the goal of improving the effectiveness of future marketing initiatives.

## Business Questions
1.	Which marketing campaign had the highest customer acceptance rate?
2.	What are the demographic differences between customers who responded and those who did not?
3.	Does marital status influence customer response behavior?
4.	Do responders have different spending patterns compared to non-responders?

## Tools & Technologies
- Language: R
- Libraries: tidyverse, dplyr, lubridate, ggplot2
- Techniques: Data wrangling, segmentation, descriptive statistics
- Visualizations: Bar chart, stacked bar chart, grouped column chart
  
All analysis was conducted using reproducible R scripts, covering steps from data cleaning, feature engineering (e.g., TotalAccepted, Age), to visualization and insight generation.

## Key Code Snippets
### Data Preparation

```r
df$Dt_Customer <- dmy(df$Dt_Customer)
df$Year_Birth <- as.integer(df$Year_Birth)
df$Age <- 2015 - df$Year_Birth
```

### Acceptance Rate per Campaign

```r
campaign_cols <- c("AcceptedCmp1", "AcceptedCmp2", "AcceptedCmp3", "AcceptedCmp4", "AcceptedCmp5")
df$TotalAccepted <- rowSums(df[, campaign_cols])
acceptance_rate <- colMeans(df[, campaign_cols])
acceptance_df <- data.frame(Campaign = campaign_cols, Rate = acceptance_rate)
```


### Summary: Responders vs Non-Responders

```r
responders <- df[df$Response == 1, ]
non_responders <- df[df$Response == 0, ]

responders_summary <- responders %>%
  summarise(Average_Income = mean(Income, na.rm = TRUE),
            Average_Age = mean(Age),
            Married_Ratio = mean(Marital_Status == "Married"))

non_responders_summary <- non_responders %>%
  summarise(Average_Income = mean(Income, na.rm = TRUE),
            Average_Age = mean(Age),
            Married_Ratio = mean(Marital_Status == "Married"))
```
            
### Spending by Product

```r
product_cols <- c("MntWines", "MntFruits", "MntMeatProducts",
                  "MntFishProducts", "MntSweetProducts", "MntGoldProds")

df %>%
  mutate(Group = ifelse(Response == 1, "Responders", "Non-Responders")) %>%
  group_by(Group) %>%
  summarise(across(all_of(product_cols), mean, na.rm = TRUE)) %>%
  pivot_longer(-Group, names_to = "Product", values_to = "AvgSpend") %>%
  ggplot(aes(x = Product, y = AvgSpend, fill = Group)) +
  geom_col(position = "dodge") +
  labs(title = "Average Spending per Product by Response Group") +
  theme_minimal()
```

## Key Insights

### 1. Campaign Performance Insight
![alt text](https://github.com/deaanisaaulia/campaign-response-analysis/blob/main/pic1.png)

Campaigns 3, 4, and 5 recorded the highest acceptance rates, each around 7.3%. In contrast, Campaign 2 showed the lowest performance with only 1.3% acceptance. Newer or more personalized campaigns seem to resonate better with customers. Campaign 2 might have suffered from poor timing or irrelevant messaging.

### 2. Customer Profile: Responders vs Non-Responders

| Group           | Average Income | Average Age | Married Ratio |
|-----------------|----------------|-------------|----------------|
| Responders      | 60,209.68      | 45.58       | 29.3%          |
| Non-Responders  | 50,839.13      | 46.30       | 40.2%          |

Responders tend to be younger, have higher income, and are less likely to be married. These traits suggest a profile of customers more open to promotional offers and brand engagement.

### 3. Campaign Response by Marital Status
![alt text](https://github.com/deaanisaaulia/campaign-response-analysis/blob/main/pic2.png)

Customers categorized under YOLO and Absurd marital statuses had the highest response rates (up to 50%). On the other hand, Married and Together customers responded the least.
Customers with non-traditional relationship statuses may be more open to novel experiences or impulsive buying, making them more receptive to creative campaign messaging.

### 4. Spending Behavior per Product Category
![alt text](https://github.com/deaanisaaulia/campaign-response-analysis/blob/main/pic3.png)

Responders spent significantly more in every product category, particularly:
- Wine: Over 500 units
- Meat Products: Over 300 units

Responders are high-value customers who not only engage with campaigns but also actively purchase premium products.

## Strategic Recommendations
### 1. Focus on High-Performing Campaigns
Continue and replicate the strategies used in Campaigns 3, 4, and 5. Re-evaluate or discontinue Campaign 2. Perform A/B testing with different target audiences or messaging.

### 2. Target High-Value Customer Segments
Focus on customers: Aged 40–50, With above-average income, Who are single, YOLO, or have unique social statuses, Use highly personalized offers, modern visuals, and exclusive deals to attract this segment.

### 3. Design Campaigns Based on Marital Status
For Married customers: highlight family-oriented or stable lifestyle themes. For YOLO/Alone customers: emphasize freedom, fun, and limited-time offers with vibrant, bold designs.

### 4. Introduce Loyalty Programs for Responders
Reward responders with loyalty points, product bundles, or exclusive premium promotions. Encourage repeat engagement and increase lifetime customer value.




