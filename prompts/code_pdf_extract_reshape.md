# Prompt template: generate code for PDF data extraction and reshaping

```
# Objective: 
To import data from an FOI response containing data on crimes in hospitals and clean it so that we have a single dataset in a common structure.
The order of priority is as follows: ideally data which shows every crime, its category, outcome and location.
But if this is not in the response, we want any table showing total crimes by category for each year - or for all years.
If that isn't provided, total crimes by hospital and year - or for all years.
If that isn't provided, total crimes by outcome and year - or for all years.

We want to structure the data into a table with the following columns:

Filename
Sheet name
Force (extracted from the file name)
Hospital (where specified. If not specified, say ‘NOT SPECIFIED’)
Crime category (where specified. If not specified, say ‘NOT SPECIFIED’). For example ‘Crime Tree LV4 Desc’ is a higher level category but ‘Stats Classification Desc’ is a sub category to that.
Crime code (where specified. If not specified, say ‘NOT SPECIFIED’)
Crime sub-category (where specified. If not specified, say ‘NOT SPECIFIED’). The sub category column can be identified as one where different sub categories appear under the same category. For example ‘Crime Tree LV4 Desc’ is a higher level category but ‘Stats Classification Desc’ is a sub category to that.
Outcome code (where specified. If not specified, say ‘NOT SPECIFIED’)
Outcome description (where specified. If not specified, say ‘NOT SPECIFIED’)
Total 
Year from (where specified. If not specified, say ‘NOT SPECIFIED’). This might be stored as part of a more specific date, e.g. the year 2019 in “2019-05”, or the financial year “2019/2020”
Year to (where specified. If not specified, say ‘NOT SPECIFIED’). This will be the same as Year from, except where the response has used a financial year like ‘2019/2020’ in which case the year to is the second year mentioned
Month (where specified. If not specified, say ‘NOT SPECIFIED’). This might be stored as part of a more specific date, e.g. the month 05 in “2019-05”

# Task
Look at the attached PDF and suggest Python code that will work in Colab notebook. It should: 

Import the PDF
Identify the pages with data to extract. Follow this order of priority: 1) Data on crimes by hospital. Ideally this will also contain the category of crime and/or outcome, but in some situations those will be in separate tables. If that is the case, focus only on the crimes by category table(s). If no breakdown by category is provided, focus only on the totals by hospital. If no breakdown by hospital or category is provided, focus on the totals by outcome.
Extract that data into a series of dataframes. Note that tables which continue into subsequent pages will start with a row of data, rather than the headings. 
convert it into a single dataframe with headings from the first table (because where tables continue into subsequent pages they won’t have the headings on them)
Clean the dataframe to use the field headings from that table, and remove any rows before that data, and 
Clean the data so it has the structure detailed above, separating any text that needs to go into different columns
Export as a CSV
Download the CSV

## Table structure [DELETE/ADAPT AS APPLICABLE]

Here is the structure of the tables in the PDF. 
The table appears on page 2 under question 1b and continues onto the first part of p3
In the PDF there are grid lines between the rows and columns in the table.
The first column is Offence sub group, then there are five columns for each year, and a grand total column
In the PDF the hospital name sits above the table so there is a gridline below the name of the hospital but not above or beside.
[PASTE EXTRACT]

## Extra details

[INCLUDE ANY EXTRA INFORMATION HERE]
```

An example of extra details was this:

`Nottinghamshire: in this response the police force has provided a separate table for each hospital. Each table contains a first column titled ‘year’ with the categories of crime in the rows underneath, then five columns with figures for each of five financial years. The name of the hospital appears just above the table. Grab each table including the name of the hospital above it. Ignore the outcomes tables that come immediately after each of those.`
