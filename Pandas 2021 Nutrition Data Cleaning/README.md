Pandas 2021 Nutrition Data Cleaning
===================================

**Description:** This project prepares sample 2021 Nutrition data for future visualization through extensive data cleaning and feature engineering. Missing and duplicate data are handled, feature data types are recasted, and 11 new features are created using existing data (through advanced grouping and window functions).

**Language(s):** Python  
**Package(s):** Pandas, Seaborn  
**Software:** Jupyter Notebooks  

**Motivation:** This project was created to demonstrate skills in Python data cleaning and feature engineering with the attempt to simulate a more sophisticated workplace scenario. Project goals were outlined beforehand so some tasks required further research into package documentation and creative problem solving than was known initially. Thus, I was able to become more comfortable with Pandas and the entire of process of data cleaning and transformation.


Viewing the Project
--------------------
The main file for this project is located in the root of this repository named _[Python Nutrition Data Cleaning.ipynb](Python%20Nutrition%20Data%20Cleaning.ipynb)_. If you have **Jupyter Notebooks** installed, you can download the .ipynb file and view it there. If not, you can view the project using Google Colaboratory [here](https://colab.research.google.com/github/AvinashBisram/Data-Cleaning/blob/master/Pandas%202021%20Nutrition%20Data%20Cleaning/Python%20Nutrition%20Data%20Cleaning.ipynb).  
**Note:** Executing all cells in the main project file will create 2 CSV files on your computer.

About the Data
---------------
The dataset used in this project was personal 2021 Nutrition Data tracked over the course of an entire year.  
The data was recorded in Google Docs and downloaded as a [Microsoft Excel file](./2021%20Nutrition%20Data%20(RAW).xlsx). It was then imported into the project file as a **Pandas** Data Frame.
<br>
A snippet of the data can be seen below:  

![Head of Raw Dataset](./readMe%20images/2021_Nutrition_Raw_Data_Snapshot.PNG)
Features were tracked daily such as daily bodyweight (lbs), calorie and macronutrient goals as well as actual amounts consumed for each, and additional cardio performed with one column specifying the activity and another specifying the duration.


Exploratory Data Analysis
--------------------------
A simple EDA process was performed to gain a better understanding of the format and structure of the dataset and identify areas that would need to be cleaned later on. Steps taken include:
* Inspecting the head of the Data Frame
* Using .info() to observe non-null count and data types per column  

At this stage, no additional statistical information could be gleaned as almost every column had inaccurate data types (numeric values being represented as strings).


Data Frame Cleaning
--------------------
During the EDA process, several unwanted qualities of the overall Data Frame were observed and needed to be resolved.
* Columns were renamed to hold the value of their first row (originally every column was named 'Unnamed").
* An unwanted column of entirely missing data was dropped from the dataset.


Data Cleaning
--------------
Now that the general format of the Data Frame was improved, we moved on to cleaning the actual data.
* Duplicate data was identified using ```.duplicated()```.
    * No duplicate records were found but the appropriate method for handling duplicates was discussed anyways.
* Missing Data was identified.
    * 8 columns contained missing values at this stage.
    * Barplots were created to visualize the missing values per feature as total counts and percentages of the dataset length. The visualization expressing the values as percentages can be seen below:  ![Percentage of Missing Data per Feature](./readMe%20images/missing_data_percent_viz.PNG)  
    * Intuition behind the decision to drop or keep columns with missing data was discussed. No columns were dropped but it was decided to recast them to their appropriate data types so methods for filling missing data could be used.
* Data types for 11 columns were recasted (only the Activity column remained as 'object').
* Missing data was successfully handled for 2 columns.
    * A method was created to visualize the remaining percentages of missing values per column.
    * The 'Activity' and 'Duration' columns were filled using ```.fillna()```.
    * The various methods for filling 'Daily Weight' were discussed (propagation, interpolation, etc.). It was decided to calculate average weekly bodyweight through feature engineering first and then fill the missing values in this column.
    * The remaining columns ('Actual' columns for macronutrient information) contained missing values in the same records. Filling these values were also postponed until after feature engineering.
    * Intuition was given for every choice made in this process such as juxtaposing the various methods and their effects on the data integrity and final visualization, and the best way to approximate values while preserving long-term trends.
    * The percentages of missing values remaining after this process was visualized as below:<br>![Perentage of Missing Data per Column after Cleaning](./readMe%20images/missing_data_percent_viz_2.PNG)


Feature Engineering
--------------------
This section was the most challenging as specific goals for each column was outlined in the beginning of the project. For some columns, a much deeper understanding of Pandas documentation was required as well as theory for filling missing values in datasets.  
The names and general descriptions of those columns can be seen below:  
![Feature Engineer Column Descriptions](./readMe%20images/FE_column_descriptions.PNG)  
* "Weekly Average Weight" was created by recording the values of 'Date' as their respective weekdays (with ```dt.day_name()```). Then ```pd.Grouper()``` was used with a Weekly frequency to partition records starting on each Monday. Lastly, an aggregate ```.mean()``` method was applied to each group of weights. The final aggregate Data Frame was merged to the main dataset (left merge) where records shared the same 'Date' value (weekly averages were displayed on the Sunday of each week with all other values being NaN).
* "Net Weight Change" was created in a similar manner with the addition of ```.rolling()``` and a lambda defined aggregate function calculating the difference between weeks. The Data Frame constructed here was also merged with the main dataset. 
* The remaining Adherence and Difference columns were created by applying simple arithmetic to the appropriate features and assigning those values to a new feature for each.

During this process, a total of 10 new features were created from the original dataset.

Final Cleaning
---------------
With the feature engineering process finished, it was now time to go back and attempt to fill the remaining missing values that were postponed in the original data cleaning process.  
The missing value percentage per feature at this stage can be seen below:  
![Missing Data Percent after Feature Engineering](./readMe%20images/missing_data_percent_viz_3.PNG)  
<br>
* Due to the large amount of missing data in the two weekly aggregate columns (Average Weekly Weight and Net Weight Change), it was decided to store these values as a separate Data Frame that could always be merged back if necessary (or simply visualized as is). Due to that decision, we were again unable to fill the missing values in Daily Weight. However, since the Average Weekly Weights were already calculated and stored as a deep copy of the main dataset (changing values in one would not carry over to the other) we now had the freedom to use a different method to fill the values. 
* A new column for Trend Weight was created which calculated the rolling average of bodyweights over 7 days (fortunately no cluster of missing records were larger than 6 so this method ensures every value is filled in some manner). This column was then used to populate missing values in the Daily Bodyweight column as there was no risk of altering the previously calculated Average Weekly Weights and the long-term weight trend was still preserved.
* The possibilities for filling the remaining macronutrient columns were discussed but no method was found that preserved the integrity and trend of the data. 
* Lastly, several columns had their data types recasted again to integers (this was not possible before feature engineering as Pandas int columns cannot contain np.NaN values). A total of 5 columns were recasted as this stage.


Saving the Data
----------------
Finally, both the main data set and weekly aggregate data frame were saved as CSV files. The main dataset can be seen [here](./2021_Nutrition_Data_CLEAN_Pandas.csv) and the weekly aggregate data can be seen [here](./2021_Nutrition_Data_WeeklyAgg.csv).


Next Steps
-----------
If I wanted to expand on this project in the future, I would want to visualize the final cleaned datasets using an interactive software such as Tableau or Power BI.

