# Extracting data from FOI responses

AI can be especially helpful in extracting data from FOI responses - but don't reach for it immediately. Here are some techniques and tools that are quicker and less energy intensive than AI, and reduce deskilling:

* Try copy and paste with DOC and emails (and XLSX)
* Try Open Refine to combine sheets from XLSX — or search DuckDuckGo* to find other solutions like this
* Try Tabula first for PDF extraction: tabula.technology
* Splitting/combining PDFs if too large/many: Adobe Acrobat

Only turn to AI once those approaches don't work. 

Before using AI, make a security assessment of the information you are about to put into the AI tool. If the documents contain sensitive information that you wouldn't make public, then do not use a public AI tool (you might use a private LLM instead).

With FOI responses, this is unlikely, as the authority wouldn't release information if it was not prepared to make it public (and the authority may publish their response on a public disclosure log). 

## Using AI to combine sheets from an XLSX file

If you could not extract data from a multi-sheet XLSX file using Open Refine, the prompt template below should allow you to do that with an AI tool.

First upload your file, and make sure you know which sheets contain the data you need, and which columns, then adapt this template so that it describes *your* file:

```
This XLSX file contains a sheet for each financial year.
Each sheet has three tables.
Extract the second table, in columns D-E, from each sheet
Create a CSV containing the combined tables with the following columns:
Offence Code | Total | Year from | Year to
Ignore the first two sheets which do not relate to financial years
```

## Using AI to extract a table from a PDF file

If you could not extract data from a PDF file using Tabula, the prompt template below should allow you to do that with an AI tool.

First upload your file, and make sure you know which pages and tables contain the data you need, and what they look like, then adapt this template so that it describes *your* file:

```
Attached is a PDF. Extract ONLY the table on pages 2 and 3 which shows crime totals by category and year.
If any line breaks or hyphenations cause split labels, reconstruct using nearest-neighbor/line-merge logic and re-validate.
Export as a CSV.
Check you have grabbed rows at the ends and beginnings of pages. 
Check that figures in the extracted CSV, when totalled match totals in the PDF
If any uncertainty remains (e.g., an OCR-confused label), include a “flags” list with the exact text span and your best-guess normalization.
```

## Using AI to generate *code* to extract tables from a PDF

Rather than extract the table in the AI tool, which raises verification challenges and issues around explainability, it may be preferable to extract it using code. Code allows you to see exactly *how* the table is extracted, to explain that to others, and to identify any potential problems.

Below is an example of a prompt template designed to generate Python code that can be copied and pasted into a Colab notebook in Google Drive. 

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
3. Extract that data into a series of dataframes. Store the force, filename and page number(s) in extra columns. Note that tables which continue into subsequent pages will start with a row of data, rather than the headings. 
4. Convert it into a single dataframe with headings from the first table (because where tables continue into subsequent pages they won’t have the headings on them)
5. Clean the dataframe to use the field headings from that table, and remove any rows before that data, and 
6. Export as a CSV
7. Download the CSV

## Table structure [DELETE/ADAPT AS APPLICABLE]

Here is the structure of the tables in the PDF. 
The table appears on page XXX under question XXX and continues onto the first part of page XXX
In the PDF there are grid lines between the rows and columns in the table.
The first column is XXXXX, then there are XX columns for each year, and a grand total column
[PASTE EXTRACT]

## Extra details

[INCLUDE ANY EXTRA INFORMATION HERE]
```

## Examples of descriptions and extra details

`In the PDF the hospital name sits above the table so there is a gridline below the name of the hospital but not above or beside.`

`Nottinghamshire: in this response the police force has provided a separate table for each hospital. Each table contains a first column titled ‘year’ with the categories of crime in the rows underneath, then five columns with figures for each of five financial years. The name of the hospital appears just above the table.`

`Ignore the outcomes tables that come immediately after each of those. `

