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
