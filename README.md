Author: Elaine Gu

## Introduction
The dataset contains Google Maps information for various locations in Hawaii. This project uses the Hawaii business metadata, which is part of a much larger dataset.

**Research Question:** Do certain naming features of Hawaii restaurants correlate with higher price levels and average ratings?

This question is relevant because restaurant names are intentional and important to how businesses reflect their style and identity. Understanding whether these naming choices relate to customer ratings or price can reveal patterns in how restaurants are presented and perceived. 

The following dataset was originally scraped and used in the research papers [UCTopic: Unsupervised Contrastive Learning for Phrase Representations and Topic Mining](https://aclanthology.org/2022.acl-long.426.pdf) and [Personalized Showcases: Generating Multi-Modal Explanations for Recommendations](https://arxiv.org/pdf/2207.00422).

`meta-Hawaii.json` contains 21,507 rows and 15 columns recording the following information: 

| Column | Description |
|---|---|
| `name` | Name of the business |
| `address` | Address of the business |
| `gmap_id` | ID of the business |
| `description` | Description of the business |
| `latitude` | Latitude of the business |
| `longitude` | Longitude of the business |
| `category` | Category of the business |
| `avg_rating` | Average rating of the business |
| `num_of_reviews` | Number of reviews |
| `price` | Price of the business |
| `hours` | Open hours |
| `MISC` | Miscellaneous information |
| `state` | Current status of the business |
| `relative_results` | Related businesses recommended by Google |
| `url` | URL of the business |

## Data Cleaning and Exploratory Data Analysis
1. Keep only relevant columns from the metadata
    - We only keep the columns relevant to the research question, which include `name`, `category`, `longitude`, `price`, `avg_rating`, and `num_of_reviews`.
2. Remove rows missing necessary information
    - Rows missing `name`, `category`, or `avg_rating` were removed because these columns are necessary for identifying restaurants and analyzing their naming features and ratings.
3. Convert `price` into numeric price levels
    - The original `price` column uses dollar signs such as `$`, `$$`, `$$$`, and `$$$$`. These were converted to numeric levels 1 through 4 so price can be more easily compared and analyzed. Missing prices were kept as `NaN`. 
4. Filter the dataset to restaurants only
    - Since we are only analyzing the restaurant business, we only keep rows where at least one category contains the word `restaurant`. 
5. Clean and split restaurant names into individual words
    - Restaurant names were converted to lowercase and cleaned by removing punctuation and special characters. A new column, `name_words`, was then created to store the individual words in each restaurant name.
6. Create groups of naming features
    - Words were grouped into categories including food, cuisine, dining type, Hawaiian words, descriptive words, and person names. These groups make it easier to compare variables across broader naming patterns rather than individual words.
7. Add boolean columns for each naming feature
    - The columns `has_food_word`, `has_cuisine_word`. `has_dining_type_word`, `has_hawaiian_word`, `has_descriptive_word`, and `has_person_name` were added to indicate whether a restaurant name contains a word from each category. 

The cleaned dataset contains 4,301 rows and 14 columns. The following is the output of the first 5 rows:

| name | category | longitude | price | avg_rating | num_of_reviews | name_words | has_food_word | has_cuisine_word | has_dining_type_word | has_hawaiian_word | has_descriptive_word | has_person_name |
|---|---|---:|---:|---:|---:|---|---|---|---|---|---|---|
| Hale Pops | [Restaurant] | -157.920714 | NaN | 4.4 | 18 | [hale, pops] | False | False | False | True | False | False |
| Akasatana Ramen Kyoto | [Ramen restaurant] | -157.843730 | NaN | 5.0 | 1 | [akasatana, ramen, kyoto] | True | False | False | False | False | False |
| Grill City | [Restaurant] | -158.026218 | NaN | 3.5 | 8 | [grill, city] | False | False | True | False | False | False |
| Buona Sera | [Italian restaurant, Restaurant] | -157.742702 | NaN | 3.7 | 24 | [buona, sera] | False | False | False | False | False | False |
| Tucker & Bevvy Breakfast | [Fast food restaurant, Restaurant] | -157.822399 | NaN | 4.2 | 57 | [tucker, bevvy, breakfast] | False | False | False | False | False | False |

### Univariate Analysis
The distribution of the most common words in restaurant names shows that terms such as "restaurant", "grill", and "cafe" appear most frequently and can all be categorized as dining types. Many other common words describe foods or cuisines, giving insight into a few of the naming features that restaurants most commonly use.

<iframe
  src="assets/univariate_most_common_words.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

The distribution of word types in restaurant names shows `has_food_word` and `has_dining_type_word` are the  most common naming features. However, it is important to note that because these categories are manually defined, this plot should be interpreted broadly and only as a general classification of naming patterns rather than a precise or exhaustive measure.

<iframe
  src="assets/univariate_distribution_of_word_types.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

### Bivariate Analysis
From this scatter plot, it can be interpreted that most restaurants have average ratings between approximately 3.5 and 5. Restaurants with fewer reviews show a wider range of ratings, while restaurants with more reviews tend to have ratings concentrated toward the higher end.

<iframe
  src="assets/bivariate_num_reviews_avg_rating.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

The box plot shows that higher price levels are generally associated with higher average ratings. Higher-priced restaurants appear to have more concentrated ratings than lower-priced restaurants, although it should be considered that there are fewer restaurants at the higher price levels.

<iframe
  src="assets/bivariate_price_level_avg_rating.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

### Aggregates
This pivot table aggregates the data by naming feature and displays the mean `avg_rating` and `price`. While the "Hawaiian" word type corresponds with the highest average rating, "Dining Type" and "Hawaiian" names have the highest average prices. However, the difference between the naming features are relatively small.

| word_type | avg_rating | price |
|---|---:|---:|
| Cuisine | 4.188274 | 1.599190 |
| Descriptive | 4.290206 | 1.627660 |
| Dining Type | 4.286122 | 1.853107 |
| Food | 4.222242 | 1.509774 |
| Hawaiian | 4.374708 | 1.781513 |
| Person Name | 4.118033 | 1.406250 |

## Assessment of Missingness

### MNAR Analysis
I believe the `description` column could be argued as **MNAR** because whether a business has a description may depend on factors that are not observed in the dataset, such as how actively the owner manages the Google Maps listing. Additional data on business owner activity or the completeness of other fields in the listing, for example, could potentially make the missingness **MAR** since it could then be explained by another observed variable.

### Missing Dependency
> Missing Price Dependent on Number of Reviews

The missingness of `price` was tested to determine whether it depends on `num_of_reviews`.

**Null Hypothesis:** The missingness of `price` does not depend on `num_of_reviews`

**Alternative Hypothesis:** The missingness of `price` does depend on `num_of_reviews`

**Test Statistic:** The absolute difference in mean number of reviews between restaurants with and without missing prices.

**Significance Level:**: 0.05

<iframe
  src="assets/missingness_dependent_price.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

*\*The number of reviews was log-transformed to reduce right skew and make the missing and non-missing price distributions easier to visually compare*

A permutation test was conducted by shuffling the `price_missing` labels 1000 times to generate a distribution of simulated test statistics as defined above. The observed test statistic was then compared to this distribution, resulting in a **p-value** of less than **0.001**.

Since the p-value is less than the significance level of 0.05, we **reject the null hypothesis**. This suggests that the missingness of `price` is not **MCAR** and may be **MAR** with respect to observed variables such as `num_of_reviews`. This makes sense intuitively because restaurants with more reviews may have more established and complete Google Maps listings, making price information more likely to be recorded. Conversely, restaurants with fewer reviews may have less complete listings, increasing the likelihood that `price` is missing.

> Missing Price Not Dependent on Longitude

The missingness of `price` was tested to determine whether it depends on `longitude`.

**Null Hypothesis:** The missingness of `price` does not depend on `longitude`.

**Alternative Hypothesis:** The missingness of `price` does depend on `longitude`.

**Test Statistic:** The absolute difference in mean longitude between restaurants with and without missing prices.

**Significance Level:**: 0.05

A second permutation test was conducted using the same method described above, but with respect to `longitude`. The resulting **p-value** was **0.462**.

Since the p-value is greater than the significance level of 0.05, we **fail to reject the null hypothesis**. This suggests that the missingness of `price` does not depend on `longitude`. In other words, a restaurant's east-west geographic position does not appear to explain why `price` is missing. 

## Hypothesis Testing
We are interested in whether the presence of Hawaiian words in restaurant names is associated with higher average price levels. This directly addresses the main research question by examining one distinct naming feature. A permutation test is conducted to evaluate this relationship.

**Null Hypothesis:** Restaurants with Hawaiian words in their names have the same average price level as restaurants without Hawaiian words in their names.

**Alternative Hypothesis:** Restaurants with Hawaiian words in their names have a higher average price level than restaurants without Hawaiian words in their names.

**Test Statistic:** Difference in mean price level between restaurants with and without Hawaiian words in their names.

**Significance Level:** 0.05

The null hypothesis provides a baseline of no difference, while the alternative hypothesis tests the directional relationship of interest. The difference in mean price is an appropriate test statistic because it captures both the size and direction of the difference between the two groups.

The `has_hawaiian_word` labels were shuffled 1000 times to generate simulated differences in mean price under the null hypothesis. The **observed statistic** was approximately **0.132**, and the resulting **p-value** was **0.012**.

<iframe
  src="assets/empirical_distribution_price.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

Since the p-value is less than the significance level of 0.05, we **reject the null hypothesis**. This provides evidence that restaurants with Hawaiian words in their names tend to have higher average price levels than restaurants without Hawaiian words in their names. 

## Framing a Prediction Problem
We want to **predict a restaurant's average rating using its price, number of reviews, and naming features**. This is a regression problem because the response variable, average rating, is numerical and continuous. Average rating was chosen because it directly reflects customer evaluation and is one of the main outcomes considered in the research question.

The model is evaluated using **RMSE** (Root Mean Squared Error) because it measures how far the predicted ratings are from the actual ratings while giving more weight to larger prediction errors. This is more appropriate than classification metrics such as accuracy or F1-score because the response variable is continuous rather than categorical.

At the time of prediction, the restaurant's name, price level, number of reviews, and category information would already be available from its Google Maps listing. The naming features are derived only from the restaurant name, so they would also be known before prediction, making all selected features appropriate for training the model.

## Baseline Model
The baseline model uses a linear regression model that predicts a restaurant's average rating using `price`, `num_of_reviews`, and `has_hawaiian_word`.

The model achieved an **RMSE** of approximately **0.456**, meaning that its predicted ratings are typically about 0.456 points away from the actual ratings. Since the performance was measured based on a test set, it reflects how well the model generalizes to unseen data. This model is reasonably effective but still limited because it uses only three relatively simple features and assumes a linear relationship with average rating.

## Final Model
The final model builds on the baseline with the following features:

`price`
This feature represents a restaurant's price level, **ordinally encoded** from `$`-`$$$$` to 1-4. As shown in the bivariate box plot, higher price levels generally correspond with higher average ratings. Intuitively, price can reflect differences in restaurant positioning, service quality, and customer expectations, all of which may relate to how customers evaluate the restaurant.

`num_of_reviews`
This feature represents how many reviews a restaurant has received. A larger review count can indicate greater popularity or visibility and may produce a more stable and reliable average rating because it is based on more customer experiences. It can also make a restaurant appear more established or trustworthy to potential customers, influencing their expectations and perceptions.

`has_hawaiian_word`
This feature indicates whether the restaurant name contains a Hawaiian word. Since the analysis focuses on naming patterns in Hawaii, this variable captures a locally relevant aspect of restaurant identity that may be associated with how the restaurant is positioned and perceived by customers.

`has_cuisine_word`
This feature indicates whether a restaurant name contains a cuisine-related word, such as "Thai" or "Hawaiian", both of which appear among the top 20 most common words in the univariate analysis. Cuisine can shape expectations around food, pricing, and a more stylized dining environment, which may influence how customers evaluate and rate a restaurant.

`is_fast_food` and `is_fine_dining`
These two features were derived from the `category` column in the original dataset as **binary indicators** of whether a business is classified as a "Fast Food Restaurant" or "Fine Dining Restaurant". This helps distinguish restaurants with different dining styles, service expectations, and price ranges, which may shape customer experiences and therefore ratings. 

`price_fast_food` and `price_fine_dining`
These two engineered **interaction terms** combine price level with restaurant type. This is useful because fine dining and fast food operate within very different price ranges, so the same price level may carry a different meaning depending on the type of restaurant. Although Random Forest can learn interactions on its own, explicitly including these terms gives the model direct information about price within each dining context.

`category_count`
This feature represents the number of categories assigned to a restaurant on Google Maps. Restaurants with more categories may offer a broader range of services or fall into multiple dining classifications. For instance, a less specialized restaurant may be represented by several categories rather than one specific type, giving the model additional context. 

### Model and Hyperparameter Selection
The final model uses a **Random Forest Regressor** instead of linear regression. Linear regression assumes that the relationship between each feature and average rating is approximately linear, which may be too restrictive for this data. Random Forest was chosen because it can capture nonlinear relationships and interactions between features, allowing it to learn more complex patterns in the data.

`GridSearchCV` was used to select the hyperparameters `max_depth` and `min_samples_leaf`. `max_depth` controls how complex each decision tree can be, while `min_samples_leaf` controls the minimum number of observations required in each leaf, helping prevent the model from fitting overly specific patterns.

> Final Search Ranges

`model__max_depth`: [4, 5, 6]  
`model__min_samples_leaf`: [4, 6, 8, 10]

These ranges were refined through several trials. Broader ranges were initially tested, then narrowed around the values that performed best to compare nearby values more closely. `GridSearchCV` then evaluated each combination using cross-validation and selected the parameters with the lowest RMSE.

> Best Performing Parameters

`max_depth` = 6  
`min_samples_leaf` = 8

### Performance Results
On the same test data as the baseline model, the Random Forest model achieved an **RMSE** of approximately **0.425**, which, compared to the baseline model, **improved** by **0.031**. Since lower RMSE indicates predictions closer to the actual average ratings, this decrease shows that the final model performs better on unseen restaurants. The improvement suggests that the additional features, along with the Random Forest model's ability to account for nonlinear patterns and interactions, provide more useful predictive information. However, the RMSE remains relatively high, meaning that the model still has substantial room for improvement and does not predict individual ratings precisely.

## Fairness Analysis
**Group X:** Restaurants with fewer than the median of 106 reviews.  
**Group Y:** Restaurants with at least the median of 106 reviews.

**Null Hypothesis:** The model is fair. Its RMSE for restaurants with fewer reviews and restaurants with more reviews is roughly the same, and any observed differences is due to random chance.

**Alternative Hypothesis:** The model is unfair. Its RMSE for restaurants with fewer reviews is higher than its RMSE for restaurants with more reviews.

**Test Statistic:** Difference in RMSE (low reviews - high reviews)

**Significance Level:** 0.05

Using the **RMSE** as the **evaluation metric**, which measures the size of prediction errors for a continuous response variable, the two groups were compared to determine whether the model predicts ratings less accurately for restaurants with fewer reviews. 

A permutation test was conducted by shuffling the group labels 1000 times and comparing the simulated differenes in RMSE to the observed difference. The resulting **p-value** was less than **0.001**. 

<iframe
  src="assets/empirical_distribution_rmse.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

Since the p-value is less than the significance level of 0.05, we **reject the null hypothesis**. This provides evidence that the model has a higher RMSE for restaurants with fewer reviews and therefore does not have equal performance across the two groups. A possible explanation is that restaurants with fewer reviews have less stable average ratings and greater variability, making their ratings more difficult for the model to predict accurately.