# Future Salary Estimation (Predicting NBA Salaries Using Analytical Models)

## Motivation and Impact
  Over the past few decades, different sports have continuously evolved and began using technology to use different types of statistics, metrics, and data to give their team an edge or ensure progress every year. One league in particular, the National Basketball Association has implemented different technologies to optimize player health, track player movement, and also use software to predict the perfect gameplan. Because it is still a fairly new and evolving part of the game, with new ways to keep track of game and player data coming out every year, there is room to improve on certain aspects like predicting salaries based on performance. For this reason, I thought that it would tv be very interesting to build a model that can predict the salary of a certain player in the NBA. Specifically, I was interested in using the existing statistics of players in the league in order to estimate the salary of a newly recruited player. By identifying the relationship between these various statistics and the salary they get, I will be able to get a general sense of how much the players will earn, which I can base my predictions of their performance on. Thus, I set out to show that using this type of prediction, most players with a similar salary will show similar performance.
A reason this was chosen as one of my main motivations is because over the past few years, some players have gotten away essentially signing record breaking contracts with very mediocre performances and gone off to fail spectacularly right after signing the contract. Meanwhile, there are other players who are vastly underpaid and end up being absolute steals for whatever team decides to give them a chance with a contract. To give a recent example, Ben Simmons from the Philadelphia 76ers signed a 170 million dollar contract extension for 5 years, yet his performance has been terrible which has tanked his value so badly that no team wants to trade for him because taking over paying his remaining contract would be an insane risk for whatever team decides to take him. Meanwhile players like Otto Porter Jr. or Nemanja Bjelica both signed minimum contracts to be on the Golden State Warriors and have shown that they are worth more than they are getting paid. This is a recurring problem in the NBA because teams are starting to extend contracts/sign players millions of dollars based on a few really good games, but their data and statistics show that they are not consistent compared to many other players who deserve a shot at a contract, or a heftier pay day. Set out to find a solution for this problem because with all this new software and technology, there is a lot left to improve on when it comes to using data for different types of predictions in sports.
When I started off figuring out what would be the most important factors that I might want to consider when building the models, I considered wanting to add accolades and other awards players have earned throughout the years and over the season. However, one problem that allowed us to decide to not include these achievements is that some players miss out on opportunities to ‘raise’ their potential salary prediction because they get injured. Another reason this was a problem is that since using data science for basketball is still relatively new, and because every few years new types of statistics start being recorded, as of now there isn’t necessarily a way to quantify awards and accolades as statistics. Without having a viable way to add this value to my dataset I decided to omit Most Valuable Player awards, championship titles, and other awards out of my models for prediction.
With this in mind, if my model was built perfectly, I can check how much a new player from their statistics will be paid when they join the league and expect them to perform at a similar level to those with similar salaries. Consequently, my overall goal is to create a model with my knowledge so that the model can correctly predict the future salary of a player in the NBA.

## Data Cleaning
  First searched for a website where I could fetch NBA data. I was able to obtain the information I needed on two websites, ESPN and NBAstuffer. Obtained data organized by years, players' names, teams, and salaries (ESPN Internet Ventures 2021). Then, I obtained evaluation metrics data of players for each year (NBAstuffer). In order to more accurately predict the salary for a player, Looked at data from multiple seasons in the past, from 2018 to 2022.
Converted these data into CSV files then cleaned it on Jupyter Notebook using python. Tried to combine these two datasets by matching each player's name. However, it was impossible to directly combine them due to the different formats of the name columns. The data obtained from ESPN had additional basketball positions written after the players' names. Thus, defined a function called "data_cleaning," which can handle every year's data cleaning. The "data_cleaning" function receives the salary and statistics dataset as its two parameters and has four major roles.
1. Rearrangement of player name column in Stats data
It deletes the player position information that is located after the player's name in the stats data. The string split function was used to divide before and after the "," and separated the name and position.
2. Rearrangement of salary column in Salary data
Using the Regex, the "$" sign was removed to make it convenient for future operations on the SALARY column. In addition, "," was also removed with the Regex.
3. Combining Stats data and Salary data
Stats data and salary data were merged based on the matching NAME column.
4. Missing value processing
Rows with missing values were dropped from the data.
Since I am considering past season data, I applied the inflation factor (figure 1) as a
way to normalize the salary of a different year, in which the inflation rate for each year was found on the "Official Data" website. Then, I cumulatively multiplied the rate of each year and matched it with the currency rate in 2022. For instance, 2018-2019 salary * 1.0176 * 1.0123 * 1.0739.
Finally, concatenated all data from 2018 to 2022 and created “data_18_22” for the model analysis. By visualizing a heatmap of the “data_18_22”, I am able to assure myself that I had no missing values existing in the final data. As shown in the figure 2 , having no bars and staying all black tells us that there aren't any missing values in the data.
Using Sklearn's train test split, 1359 of the data from “data_18_22” was randomly split into the training set, and 583 of the remaining data were set as the test set.

## Feature selection
  All features were used except player name, team name, position, and year. The features that were initially used in the model analysis are below.
['AGE', 'GP', 'MPG', 'MIN%', 'USG%', 'TO%', 'FTA', 'FT%', '2PA', '2P%', '3PA', '3P%', 'eFG%', 'TS%', 'PPG', 'RPG', 'TRB%', 'APG', 'AST%', 'SPG', 'BPG', 'TOPG', ' VIVersatility ', 'ORTG', 'DRTG', 'RK']
Feature selection will be updated with additional findings in model analysis, such as examining the correlation of each feature, automated cross-validation, VIF value, P-value, etc.

## Analytic Models

### Linear Regression
  When I first ran the linear regression on all of the features, an error happened because there was ‘%’ in some of the column names, which did not fit the way the formula parser patsy required, so I used a builtin transformation Q(“”) for each of those features. I began by running the linear regression on my full training dataset, in which the model had a pretty high R-squared of 0.789 and an adjusted R-squared of 0.785. However, when I looked at the variance inflation factor (VIF) of each feature, many of them had high VIFs, so I first got rid of the feature with the greatest VIF. Then, I ran the linear regression model again without that removed feature and continued to check the VIFs for the remaining ones. I repeated this process until all the VIFs of the features left were less than 5. Specifically, I then took out 8 features in the order of MIN%, PPG, APG, RPG, TOPG, 2PA, VIVersatility, and TS%. Since all the features left had VIFs less than 5, looked at the p-value of each feature and removed the highest one. The process repeatedly ran the linear regression on the remaining features whenever one got removed until all of the p-values were less than or equal to significance level of 0.05. In the end, the decision was to remove 9 features in the order of 2P%, MPG, FT%, TO%, ORTG, eFG%, BPG, 3P%, and 3PA. The remaining features were AGE, GP, USG%, FTA, TRB%, AST%, SPG, DRTG, and RK. In addition, the model that I ended up with had a relatively high R-squared of 0.753, adjusted R-squared of 0.751, and an OSR-squared of 0.7649 (figure 3). This result strongly implied that the dataset had a linear relationship. But, I realized that it was because the ranking feature actually represented the salary ranking, which definitely had a direct correlation with the salaries, causing the dataset to resemble a linear behavior. Therefore, decided to redo the feature selection all over again, but removing the ranking feature from the start. Immediately, the R-squared value drops to 0.568 and the adjusted R-squared value decreases to 0.560 as well. For this case, the 8 features that were removed based on the VIFs were in the order of MPG, PPG, APG, RPG, TOPG, 2PA, VIVersatility, and TS%. Moreover, the 9 features that were removed based on the p-values were in the order of eFG%, TO%, FT%, 2P%, 3P%, 3PA, BPG, DRTG, and ORTG. As a result, the remaining features were AGE, GP, MIN%, USG%, FTA, TRB%, AST%, and SPG. The final model had a much lower R-squared of 0.507, adjusted R-squared of 0.504, and an OSR-squared of 0.5096 (figure 4). Consequently, I was able to find out that the data set does not resemble a linear relationship without the ranking feature, so decided to apply the rest of the models to my dataset while excluding the ranking feature.

### CART
  Since my model has a continuous outcome, I decided to use the CART model for one of my analytics models in order to predict the outcome of a single observation. After processed the data cleaning, I used the X_train and y_train data to classify the CART model. The first thing I did was I split the data using GridSearchCV and I used DecisionTreeRegressor since the impurity is usually high when a bucket contains a wide range of values. Second, I used the best ccp_alpha value for the model to calculate the value of R-squared and OSR-squared, in which different values of ccp_alpha ironically gave similar or same values of CV R-squared result. Moreover, I got the positive OSR-squared of 0.538, which meant that the CART model can be usable. However, the R-squared I got was 0.436, which was much smaller than 1, so it was not the best result. For this regression tree, my goal
4
was to visually see the prediction of average salary by the players rating in the NBA so that I can get a sense of how much newly recruited players will earn (figure 5). For instance, the regression tree split equally for the player who has a PPG value less than or equal to 13.85.

### Bagging
  I used Bagging since it helped reduce the high variance and avoid overfitting. I used Random Forest Regressor with GridSearchCV on all of the features to create Bagging model. The result I obtained for the OSR-squared value was 0.669, which was the highest value compared to other models. Also, the R-squared value was 0.639, which was relatively high (figure 7). The importance of the features used in the bagging model were in the order of PPG, AGE, MIN%, MPG, TOPG, VIVersatility, and so on (figure 6). Using this bagging method, I got a better result than any of the other models, so it meant that the bagging method was far from overfitting and in fact it can achieve a more precise prediction.

### Random Forest
  For the Random Forest model, I set grid values as a linspace of max features instead of a specific value to find optimal max features. Moreover, 5-fold cross-validation was used to improve the model. I predicted that random forest would bring the best outcome to predict salary since the dataset had many features, and I expected Random Forest to handle overfitting well. In addition, considering the advantages of Random Forest, which is easy to generalize and adjust parameters, it is expected to perform well enough in predicting players’ salaries. After training the model, I found that checking cross-validation plateaued at around 64%, the OSR-squared came out to around 67%(figure 9), whilst the max features reached around 20 (figure 10). After looking at the features with the most impact, I realized that the age, the points per game, and the min% with possession were the most critical factors when predicting a salary with this model (figure 8).

### Gradient Boosting
  I tried the gradient boosting model with hyperparameter tuning, and it runs through all the different parameters fed into the parameter grid and produces the best combination of parameters, based on a scoring metric ‘R-squared’, and 5-fold cross-validation used in building this model. Started training with the original data that only filtered categorical variables in my dataset. To estimate the performance of the model, I used R-Squared and OSR-squared. Results were 63.6% in R-squared and 65.6% in OSR-squared. Decided to filter out more features to produce better results. For this, we tried to filter out high correlated features by the correlation plot (figure 11). This plot showed the degree of relationship between each data in numbers and graphs and helped identify the correlation between each data. For example, the plot showed us that TRB% was highly related to DRTG. After removing this feature, both R-squared and OSR-squared values were improved to around 64% and 65.7% (figure 12). Boosting, which gives weights in each trial, is bound to be vulnerable to overfitting, and in that regard, boosting model did not produce better results than the other ensemble model, bagging.

### Bootstrap and Conclusion
![Screen Shot 2022-09-07 at 1 03 12 PM](https://user-images.githubusercontent.com/25239743/188967243-de461a8d-7afb-49c1-b59e-5ba0acc23b98.png)

(figure 13)

  Before choosing which model to base bootstrap on, I compared the OSR-squared of each of the models in order to choose the one with the highest value. To be specific, the OSR-squared for each of the models were 0.6654 (Random Forest), 0.5383 (CART), 0.6696 (Bagging), 0.6558 (Boosting), and 0.5096 (Linear Regression). As it can be seen, the bagging model had the highest OSR-squared in comparison between models. Now to compare the performance between models, I decided to apply the bootstrapping method around 5000 times, in which I bootstrapped not only the difference of OSR-squared between one of the models and the bagging model, but also the metric OSR-squared of the bagging model. To begin with, the difference between the (Random Forest OSR-squared or Gradient Boosting OSR-squared) and the (Bagging OSR-squared)  had no statistically significant difference because they both contained 0 in their 95% confidence intervals (figure 14 & figure 15). On the other hand, the difference between the (CART OSR-squared  or Linear Regressions OSR-squared) and the (Bagging OSR-squared) seemed to have a statistically significant difference since they did not contain 0 in their confidence intervals (figure 16 & figure 17) . In addition, the difference in the OSR-squared of the two models were all in a negative value, so the Bagging OSR-squared was always higher than that of other models, so I chose the Bagging as the best model. Last but not least, I observed the variability of the metric OSR-squared for the Bagging model, and found that it’s 95-percent confidence interval is between 0.6 and 0.73(figure 18). Therefore, I can confidently recommend using Bagging model to predict the future salaries of newly recruited players.

## Work Cited
- ESPN Internet Ventures. (n.d.). NBA Player Salaries - 2021-2022. ESPN. Retrieved December
18, 2021, from http://www.espn.com/nba/salaries
- “NBA Player Stats Index.” NBAstuffer, https://www.nbastuffer.com/player-stats/
- “$100 in 2018 → 2021 | Inflation Calculator.” Official Inflation Data, Alioth Finance, 18 
Dec. 2021, https://www.officialdata.org/us/inflation/2018?amount=100)

## Appendix
### Figure 1

![Screen Shot 2022-09-07 at 1 08 50 PM](https://user-images.githubusercontent.com/25239743/188967866-72ade90a-54ad-43fb-8ef2-a8a5200b22ed.png)

Figure 2

![Screen Shot 2022-09-07 at 1 10 39 PM](https://user-images.githubusercontent.com/25239743/188967993-8321ed7e-6a99-4e67-a6b5-b4badcde35cb.png)

Figure 3

![Screen Shot 2022-09-07 at 1 11 01 PM](https://user-images.githubusercontent.com/25239743/188968067-e81d1512-71ff-4e13-9ca5-3172e23b7678.png)


Figure 4

![Screen Shot 2022-09-07 at 1 11 20 PM](https://user-images.githubusercontent.com/25239743/188968118-6e3fbf42-cdaf-4975-a458-e2933ff71ac1.png)

Figure 5

![Screen Shot 2022-09-07 at 1 11 48 PM](https://user-images.githubusercontent.com/25239743/188968190-b8382f20-5408-49f0-8dfa-5785cc5cf06f.png)

Figure 6 (Bar graph of important features in Bagging)

![Screen Shot 2022-09-07 at 1 12 35 PM](https://user-images.githubusercontent.com/25239743/188968316-84620f4c-fa2f-4b4e-a2b8-42ce701898ab.png)

Figure 7

![Screen Shot 2022-09-07 at 1 12 55 PM](https://user-images.githubusercontent.com/25239743/188968418-d4926c1b-9582-4b46-8f01-71818316fe3a.png)

Figure 8

![Screen Shot 2022-09-07 at 1 13 30 PM](https://user-images.githubusercontent.com/25239743/188968495-0e01132d-6e18-4b40-a848-138ad0504d6d.png)

Figure 9

![Screen Shot 2022-09-07 at 1 13 48 PM](https://user-images.githubusercontent.com/25239743/188968568-6efbc084-3d71-4e1b-8d80-867c272f100d.png)

Figure 10

![Screen Shot 2022-09-07 at 1 14 06 PM](https://user-images.githubusercontent.com/25239743/188968623-5fc3d2d1-c906-4408-9c61-3bc3cefd4b6a.png)

Figure 11

![Screen Shot 2022-09-07 at 1 14 32 PM](https://user-images.githubusercontent.com/25239743/188968705-4e33f838-0faf-446b-ab82-2573f99db89e.png)

Figure 12

![Screen Shot 2022-09-07 at 1 14 51 PM](https://user-images.githubusercontent.com/25239743/188968754-49d1bb3b-a5b4-460b-8bc9-cc2e6b21bb05.png)

Figure 13

![Screen Shot 2022-09-07 at 1 15 06 PM](https://user-images.githubusercontent.com/25239743/188968794-7d9be46a-de09-42d6-9f42-df126ec0439c.png)

Figure 14

![Screen Shot 2022-09-07 at 1 15 24 PM](https://user-images.githubusercontent.com/25239743/188968841-d35bf613-19ab-4e05-9c21-b9337b7d2e00.png)

Figure 15

![Screen Shot 2022-09-07 at 1 15 44 PM](https://user-images.githubusercontent.com/25239743/188968907-79334c92-535a-4fbd-83ed-cd657a356627.png)

Figure 16

![Screen Shot 2022-09-07 at 1 16 03 PM](https://user-images.githubusercontent.com/25239743/188968952-386e6df9-acf3-45f0-b97e-3c55fb8aa83c.png)

Figure 17

![Screen Shot 2022-09-07 at 1 16 17 PM](https://user-images.githubusercontent.com/25239743/188969006-e13b0c4f-ec4c-4062-be3d-6087413207cf.png)

Figure 18

![Screen Shot 2022-09-07 at 1 16 35 PM](https://user-images.githubusercontent.com/25239743/188969064-68b46a30-ebaa-4023-b2ca-b1cb04909403.png)
