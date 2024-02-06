# What's For Dinner?
### A Power BI Dashboard for International Cooking Inspiration and Meal Planning with Ingredients on Hand

As a working parent with a spouse in medical school one of my most consistant challenges is developing a weekly meal plan. Balancing nutrition, ingredient costs, and labor involved is always difficult however, I wanted to see if I could solve another challenge, namely the creative job of generating new meal ideas while taking current pantry and fridge items into account. 

To help accomplish this task I created the report below using a public dataset and API at www.themealdb.com. Click on the picture to go to the live report in Power BI Services (Power BI License Required).

[<img src="https://github.com/MattResner/Whats-For-Dinner/assets/123479836/14755883-3dba-48f7-937d-62b1af19e619">](https://app.powerbi.com/reportEmbed?reportId=707a53e4-cc09-4a2b-9776-f4383cfb8a68&autoAuth=true&ctid=464e15ea-9493-4708-95c2-66f24b51aef9)


## Report Data Connections and Modeling

The data in the report is queried and transformed via three API Calls. The first call accesses the list of meal countries, the second expands the country data with each meal in the database for the country, and the third call obtains recipes and ingredients for each of the meals. 

### Initial Connection

For more information on using relative path and query options in Web.Contents checkout my previous project on using relative path for Consumer Price Index Data [Link Here](https://github.com/MattResner/Power-BI-Relative-Path/tree/main).

```'*.PowerQuery'
let
    //using the relative path method for source to enable automated refresh of Public API data.
    BaseURL="www.themealdb.com/api/json/v1",
    APISource="1/list.php?a=list",
    Source = Json.Document(
        Web.Contents(
            BaseURL,
            [RelativePath = APISource] 
            
            )),
    meals = Source[meals],
    //converting the CSV records to a table, expanding the table and renaming values before the first function
    #"Converted to Table" = Table.FromList(meals, Splitter.SplitByNothing(), null, null, ExtraValues.Error), 
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"strArea"}, {"Column1.strArea"}),
    #"Renamed Columns" = Table.RenameColumns(#"Expanded Column1",{{"Column1.strArea", "Cuisine Type"}}),
```
### Writing Power Query (M) Functions


### Power Query M Function 1 GetCusineByCountry
```

```
### Power Query M Function 2 GetMealInfoByID
```

```
### Implementing Function 1 & 2
```

```
## Data Cleaning

## Slicers and Report Design

Text Filter Slicer 
