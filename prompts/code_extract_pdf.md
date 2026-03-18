# Prompt template for generating code to extract data from a PDF containing the response to an FOI request

Responses to FOI requests often contain data in PDF format that you need to extract. If you cannot extract it using tools like Tabula, then you might try using AI to generate some code that might extract it instead. 

Below is an example of a prompt designed to generate Python code that can be copied and pasted into a Colab notebook. 

Although the prompt relates to an FOI response about hospital crime, it can be adapted for other responses. 

It mainly demonstrates the sort of detail you might include in your prompt, including:

* Specifying your preference if a response contains more than one table
* Breaking down the process into a sequence of distinct tasks
* Anticipating tables that are split across pages
* Providing clues to the location of the target data
* Providing information on the shape of the target data

```
# Objective: 
To import data from an FOI response containing data on crimes in hospitals and clean it so that we have a single dataset in a common structure.
The order of priority is as follows: ideally data which shows every crime, its category, outcome and location.
But if this is not in the response, we want any table showing total crimes by category for each year - or for all years.
If that isn't provided, total crimes by hospital and year - or for all years.
If that isn't provided, total crimes by outcome and year - or for all years.

# Task
Look at the attached PDF and suggest Python code that will work in Colab notebook. It should: 

1. Import the PDF
2. Identify the pages with data to extract. Follow this order of priority: 1) Detailed data on each individual crime, including information on category, outcome and/or location; 2) Aggregate data on crimes by category; 3) crimes by hospital; 4) crimes by outcome.
3. Extract that data into a series of dataframes. Note that tables which continue into subsequent pages will start with a row of data, rather than the headings. 
4. Convert it into a single dataframe with headings from the first table (because where tables continue into subsequent pages they won’t have the headings on them)
5. Clean the dataframe to use the field headings from that table, and remove any rows before that data, and 
6. Export as a CSV
7. Download the CSV

## Table structure [DELETE/ADAPT AS APPLICABLE]

Here is the structure of the tables in the PDF. 
The table appears on page XXX under question XXX and continues onto the first part of page XXX
In the PDF there are grid lines between the rows and columns in the table.
The first column is XXXXX, then there are XX columns for each year, and a grand total column
In the PDF the hospital name sits above the table so there is a gridline below the name of the hospital but not above or beside.
[PASTE EXTRACT]

## Extra details

[INCLUDE ANY EXTRA INFORMATION HERE]
```
