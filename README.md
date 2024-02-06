# What's For Dinner?
### A Power BI Dashboard for International Cooking Inspiration and Meal Planning with Ingredients on Hand

As a working parent with a spouse in medical school one of my most consistant challenges is developing a weekly meal plan. Balancing nutrition, ingredient costs, and labor involved is always difficult however, I wanted to see if I could solve another challenge, namely the creative job of generating new meal ideas while taking current pantry and fridge items into account. 

To help accomplish this task I created the report below using a public dataset and API at www.themealdb.com. Click on the picture to go to the live report in Power BI Services (Power BI License Required).

[<img src="https://github.com/MattResner/Whats-For-Dinner/assets/123479836/14755883-3dba-48f7-937d-62b1af19e619">](https://app.powerbi.com/reportEmbed?reportId=707a53e4-cc09-4a2b-9776-f4383cfb8a68&autoAuth=true&ctid=464e15ea-9493-4708-95c2-66f24b51aef9)


## Report Data Connections and Modeling

The data in the report is queried and transformed via three API Calls. The first call accesses the list of meal countries, the second expands that data with each meal in the database for the country, and the third call obtains recipes and ingredients for each of the meals. 

### 

### GetMealInfoByID

## Data Cleaning

## Slicers and Report Design

Text Filter Slicer 
