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
Following our initial API call and transformation our data looks like this.

![image](https://github.com/MattResner/Whats-For-Dinner/assets/123479836/d06f4efa-6821-4130-88fb-749e110f1de7)


### Writing Power Query (M) Functions

Writing functions in M is pretty straight forward and a great way to get familair with using the advanced editor. 

After the obgligitory let keyword each function follows the general pattern below:

FunctionName = (Parameter 1 as DataType, Parameter 2 as DataType...) as DataType => DesiredCalculationOrSubsequentSteps

Additionally, you may specificy an parameter as being optional with the precursor "optional" though you must order your parameters so that any optional parameters follow the required ones. 

The below is a simple example function that multiplys between one and three numbers of your choice. Parameter A is required while B and C are listed as optional. 

```
let
    MathFun =
    ( A as number
    , optional B as number
    , optional C as number
    ) as number => A*B*C
    
    in MathFun
```
You can find out more about Power Query M Functions generally at https://learn.microsoft.com/en-us/powerquery-m/understanding-power-query-m-functions

For the purposes of our project, functions are useful as we can dynamically construct and run API calls for specific records in our data. We will use this to our advantage twice, once for GetCusineByCountry and once for GetMealInfoByID

### Power Query M Function 1 GetCusineByCountry

The below function takes a parameter Cusine Type and returns meal records from the API.

```
let
    Source = (CusineType) => //specifying parameter for our relative path 
let //the start of the calculation for our function. In this case it is a web.contents relative path.
    
    #"BaseURL" = "www.themealdb.com/api/json/v1/", //www.themealdb.com/api/json/v1/1/filter.php?a=Canadian
    Source = Json.Document( 
        
        Web.Contents(#"BaseURL", [
            RelativePath = "1/filter.php?a="&CusineType //using the parameter in relative path
            ]
        )
    ),
    
    meals = Source[meals],
    //Additional transformational steps to convert the csv into our desired formatting and expanding/renaming values
    #"Converted to Table" = Table.FromList(meals, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"strMeal", "strMealThumb", "idMeal"}, {"strMeal", "strMealThumb", "idMeal"}),
    #"Renamed Columns" = Table.RenameColumns(#"Expanded Column1",{{"strMeal", "Meal Name"}, {"strMealThumb", "Meal Image"}, {"idMeal", "Meal ID"}})
in
    #"Renamed Columns"
in
    Source
```
### Power Query M Function 2 GetMealInfoByID

The below function takes a parameter MealID and returns the recipe and ingredients from the API.

```
let
    Source = (MealID) => //specifying parameter for our relative path 
let //the start of the calculation for our function. In this case it is a web.contents relative path.
    
    #"BaseURL" = "www.themealdb.com/api/json/v1/", //www.themealdb.com/api/json/v1/1/lookup.php?i=52772

    Source = Json.Document( 
        
        Web.Contents(#"BaseURL", [
            RelativePath = "1/lookup.php?i="&Number.ToText(MealID)
            ]
        )
    ),
    
    meals = Source[meals],
    //Converting to table and expanding columns
    #"Converted to Table" = Table.FromList(meals, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column2" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"idMeal", "strMeal", "strDrinkAlternate", "strCategory", "strArea", "strInstructions", "strMealThumb", "strTags", "strYoutube", "strIngredient1", "strIngredient2", "strIngredient3", "strIngredient4", "strIngredient5", "strIngredient6", "strIngredient7", "strIngredient8", "strIngredient9", "strIngredient10", "strIngredient11", "strIngredient12", "strIngredient13", "strIngredient14", "strIngredient15", "strIngredient16", "strIngredient17", "strIngredient18", "strIngredient19", "strIngredient20", "strMeasure1", "strMeasure2", "strMeasure3", "strMeasure4", "strMeasure5", "strMeasure6", "strMeasure7", "strMeasure8", "strMeasure9", "strMeasure10", "strMeasure11", "strMeasure12", "strMeasure13", "strMeasure14", "strMeasure15", "strMeasure16", "strMeasure17", "strMeasure18", "strMeasure19", "strMeasure20", "strSource", "strImageSource", "strCreativeCommonsConfirmed", "dateModified"}, {"idMeal", "strMeal", "strDrinkAlternate", "strCategory", "strArea", "strInstructions", "strMealThumb", "strTags", "strYoutube", "strIngredient1", "strIngredient2", "strIngredient3", "strIngredient4", "strIngredient5", "strIngredient6", "strIngredient7", "strIngredient8", "strIngredient9", "strIngredient10", "strIngredient11", "strIngredient12", "strIngredient13", "strIngredient14", "strIngredient15", "strIngredient16", "strIngredient17", "strIngredient18", "strIngredient19", "strIngredient20", "strMeasure1", "strMeasure2", "strMeasure3", "strMeasure4", "strMeasure5", "strMeasure6", "strMeasure7", "strMeasure8", "strMeasure9", "strMeasure10", "strMeasure11", "strMeasure12", "strMeasure13", "strMeasure14", "strMeasure15", "strMeasure16", "strMeasure17", "strMeasure18", "strMeasure19", "strMeasure20", "strSource", "strImageSource", "strCreativeCommonsConfirmed", "dateModified"})
in
    #"Expanded Column2"
in
    Source
```
### Implementing Functions into Our Data

After writing those two functions we will use them sequentially on our data by calling the functions within a custom column. In the first custom column we refer to the Cuisine Type as our parameter and in the second we use the Meal ID.

```
//Calling our first function GetCusineByCountry and expanding associated data
    #"Added Custom" = Table.AddColumn(#"Renamed Columns", "Meals", each GetCusineByCountry([Cuisine Type])),
        #"Expanded Meals" = Table.ExpandTableColumn(#"Added Custom", "Meals", {"Meal Name", "Meal Image", "Meal ID"}, {"Meal Name", "Meal Image", "Meal ID"}),
        #"Changed Type" = Table.TransformColumnTypes(#"Expanded Meals",{{"Meal ID", Int64.Type}}),

//Calling our second function GetMealInfoByID and expanding associated data
    #"Added Custom1" = Table.AddColumn(#"Changed Type", "Meal Information", each GetMealInfoByID([Meal ID])),
        #"Expanded Meal Information" = Table.ExpandTableColumn(#"Added Custom1", "Meal Information", {"idMeal", "strMeal", "strDrinkAlternate", "strCategory", "strArea", "strInstructions", "strMealThumb", "strTags", "strYoutube", "strIngredient1", "strIngredient2", "strIngredient3", "strIngredient4", "strIngredient5", "strIngredient6", "strIngredient7", "strIngredient8", "strIngredient9", "strIngredient10", "strIngredient11", "strIngredient12", "strIngredient13", "strIngredient14", "strIngredient15", "strIngredient16", "strIngredient17", "strIngredient18", "strIngredient19", "strIngredient20", "strMeasure1", "strMeasure2", "strMeasure3", "strMeasure4", "strMeasure5", "strMeasure6", "strMeasure7", "strMeasure8", "strMeasure9", "strMeasure10", "strMeasure11", "strMeasure12", "strMeasure13", "strMeasure14", "strMeasure15", "strMeasure16", "strMeasure17", "strMeasure18", "strMeasure19", "strMeasure20", "strSource", "strImageSource", "strCreativeCommonsConfirmed", "dateModified"}, {"idMeal", "strMeal", "strDrinkAlternate", "strCategory", "strArea", "strInstructions", "strMealThumb", "strTags", "strYoutube", "strIngredient1", "strIngredient2", "strIngredient3", "strIngredient4", "strIngredient5", "strIngredient6", "strIngredient7", "strIngredient8", "strIngredient9", "strIngredient10", "strIngredient11", "strIngredient12", "strIngredient13", "strIngredient14", "strIngredient15", "strIngredient16", "strIngredient17", "strIngredient18", "strIngredient19", "strIngredient20", "strMeasure1", "strMeasure2", "strMeasure3", "strMeasure4", "strMeasure5", "strMeasure6", "strMeasure7", "strMeasure8", "strMeasure9", "strMeasure10", "strMeasure11", "strMeasure12", "strMeasure13", "strMeasure14", "strMeasure15", "strMeasure16", "strMeasure17", "strMeasure18", "strMeasure19", "strMeasure20", "strSource", "strImageSource", "strCreativeCommonsConfirmed", "dateModified"}),

```

After using GetCusineByCountry the data expands with each meal record.

![image](https://github.com/MattResner/Whats-For-Dinner/assets/123479836/6f0b5361-cbc3-47f1-99f1-b3383d72b4b2)

After using GetMealInfoByID the data further expands with information on each meals ingredients, measures and recipe instructions.

![image](https://github.com/MattResner/Whats-For-Dinner/assets/123479836/a2450d17-b134-4c9f-9db8-9eef6c7503f6)

## Data Cleaning

Now that we've got all the data we need it's time to transform it into a user friendly data model. We 

```
// Removing and Renaming columns as part of general cleanup
    #"Removed Columns" = Table.RemoveColumns(#"Expanded Meal Information",{"idMeal", "strDrinkAlternate", "strMeal"}),
    #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns",{{"strCategory", "Meal Category"}}),
        #"Removed Columns1" = Table.RemoveColumns(#"Renamed Columns1",{"strArea", "strMealThumb", "strImageSource", "strCreativeCommonsConfirmed", "dateModified"}),
        #"Changed Type1" = Table.TransformColumnTypes(#"Removed Columns1",{{"strIngredient20", type text}, {"strIngredient19", type text}, {"strIngredient18", type text}, {"strIngredient17", type text}, {"strIngredient16", type text}, {"strIngredient15", type text}, {"strIngredient14", type text}, {"strIngredient13", type text}, {"strIngredient12", type text}, {"strIngredient11", type text}, {"strIngredient10", type text}, {"strIngredient9", type text}, {"strIngredient8", type text}, {"strIngredient7", type text}, {"strIngredient6", type text}, {"strIngredient5", type text}, {"strIngredient4", type text}, {"strIngredient3", type text}, {"strIngredient2", type text}, {"strIngredient1", type text}, {"strYoutube", type text}, {"strSource", type text}, {"strMeasure20", type text}, {"strMeasure19", type text}, {"strMeasure18", type text}, {"strMeasure17", type text}, {"strMeasure16", type text}, {"strMeasure15", type text}, {"strMeasure14", type text}, {"strMeasure13", type text}, {"strMeasure12", type text}, {"strMeasure11", type text}, {"strMeasure10", type text}, {"strMeasure9", type text}, {"strMeasure8", type text}, {"strMeasure7", type text}, {"strMeasure6", type text}, {"strMeasure5", type text}, {"strMeasure4", type text}, {"strMeasure3", type text}, {"strMeasure2", type text}, {"strMeasure1", type text}, {"strTags", type text}, {"strInstructions", type text}, {"Meal Category", type text}, {"Meal ID", type text}, {"Meal Image", type text}, {"Meal Name", type text}, {"Cuisine Type", type text}}),
        #"Renamed Ingredient Columns" = #"Rename Columns"(#"Changed Type1", {"strIngredient1","strIngredient2","strIngredient3","strIngredient4","strIngredient5","strIngredient6","strIngredient7","strIngredient8","strIngredient9","strIngredient10","strIngredient11","strIngredient12","strIngredient13","strIngredient14","strIngredient15","strIngredient16","strIngredient17","strIngredient18","strIngredient19","strIngredient20","strMeasure1","strMeasure2","strMeasure3","strMeasure4","strMeasure5","strMeasure6","strMeasure7","strMeasure8","strMeasure9","strMeasure10","strMeasure11","strMeasure12","strMeasure13","strMeasure14","strMeasure15","strMeasure16","strMeasure17","strMeasure18","strMeasure19","strMeasure20"}, "str", ""),
        #"Renamed Columns2" = Table.RenameColumns(#"Renamed Ingredient Columns",{{"strSource", "Source"}, {"strTags", "Tags"}, {"strInstructions", "Instructions"}, {"strYoutube", "Youtube"}}),

// Creating the custom field shopping list to include all ingredients in one field so that it reads like a recipe
    #"Added Custom2" = Table.AddColumn(#"Renamed Columns2", "Shopping List", each Text.Combine({
      (if [Ingredient1] is null then null else [Ingredient1])   
    , (if [Ingredient2] is null then null else [Ingredient2]) 
    , (if [Ingredient3] is null then null else [Ingredient3])  
    , (if [Ingredient4] is null then null else [Ingredient4])
    , (if [Ingredient5] is null then null else [Ingredient5])
    , (if [Ingredient6] is null then null else [Ingredient6])
    , (if [Ingredient7] is null then null else [Ingredient7])
    , (if [Ingredient8] is null then null else [Ingredient8])
    , (if [Ingredient9] is null then null else [Ingredient9]) 
    , (if [Ingredient10] is null then null else [Ingredient10])
    , (if [Ingredient11] is null then null else [Ingredient11])
    , (if [Ingredient12] is null then null else [Ingredient12])
    , (if [Ingredient13] is null then null else [Ingredient13])
    , (if [Ingredient14] is null then null else [Ingredient14]) 
    , (if [Ingredient15] is null then null else [Ingredient15])
    , (if [Ingredient16] is null then null else [Ingredient16])
    , (if [Ingredient17] is null then null else [Ingredient17])
    , (if [Ingredient18] is null then null else [Ingredient18])
    , (if [Ingredient19] is null then null else [Ingredient19]) 
    , (if [Ingredient20] is null then null else [Ingredient20])   
    
    }, "
")),
    #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom2",{{"Shopping List", type text}}),
    #"Renamed Columns3" = Table.RenameColumns(#"Changed Type2",{{"Meal Name", "Meal"}})
in
    #"Renamed Columns3"
```

## Slicers and Report Design

Text Filter Slicer 
