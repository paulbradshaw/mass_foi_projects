# Reshaping data extracted from multiple FOI responses

Once you've extracted the data from multiple FOI responses into a series of CSV files, you face the next challenge: getting them all into the same shape so that you can combine them.

You need a structure that can accommodate the variety of responses, and that allows you to ask different questions (e.g. pivot tables). It should have the following:

* Have ‘year from’/‘year to’ (DON’T try to have five columns for five years)
* Have a column that measures the number of events (e.g. crimes)
* Have a category column that classifies the type of event (e.g. ‘theft’)
* You might have sub-category column as well
* Allow columns to have ‘NOT SPECIFIED’ where bodies didn’t provide that level of detail (or combined detail)
* Have columns for filename and authority for traceability/combination

Once you've decided on that structure, adapt the prompt template below.

## Prompt template for reshaping data into a common structure

This prompt template was used to reshape data extracted from an FOI response so that it fit into a consistent structure for comparison and combination with other reponses.

Upload your CSVs and adapt this so that it describes the structure you want to reshape them to:

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
