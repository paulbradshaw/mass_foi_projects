# Prompt template for reshaping data into a common structure

This prompt template was used to reshape data extracted from an FOI response so that it fit into a consistent structure for comparison and combination with other reponses.

```
# Objective: 
To reshape a CSV with data from an FOI response to a question on crimes in hospitals so that we have a single dataset in a common structure.

That structure has the following columns:

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

Look at the attached CSV and suggest Python code that will work in Colab notebook. It should: 

import the CSV
Reshape the data so it has the structure detailed above, separating any text that needs to go into different columns, such as category/outcome text and code, and year from and year to
Export as a CSV
Download the CSV

Provide the code in two blocks: the first block to upload the file, and the second block for everything after that point.

Comment the code thoroughly so that it is easy for a non-coder to understand. Avoid jargon.
Simple but longer code is preferable to shorter more complex code, if simple code is easier to understand.
```

## Optional: include location and/or structure information

You might include extra information about the structure, like so:

```
## LOCATION/STRUCTURE OF DATA

The table appears on page 2 under question 1b and continues onto the first part of p3
In the PDF there are grid lines between the rows and columns in the table.
The first column is Offence sub group, then there are five columns for each year, and a grand total column
Here is the structure of the tables in the PDF:
[PASTE EXTRACT IF POSSIBLE]
```

## Optional: include extra information

Sometimes a PDF might be include data around the table rather than inside it, or you need to provide other extra information about elements to grab or ignore. 

Add this information to the end of your prompt like so:

```
## EXTRA DETAILS

Nottinghamshire: in this response the police force has provided a separate table for each hospital. Each table contains a first column titled ‘year’ with the categories of crime in the rows underneath, then five columns with figures for each of five financial years. The name of the hospital appears just above the table.
Grab each table including the name of the hospital above it. Put this in a column called ‘hospital’
Ignore the outcomes tables that come immediately after each of those. 
```
