# Prompt template: combine sheets from an XLSX file

This prompt is useful when you get a response where data for different years is in different sheets. 

Adapt the details for your own scenario, specifying the spreadsheet columns, tables and fields for your own situation.

```
This XLS file contains a sheet for each financial year. Each sheet has three tables.
Extract the second table, in columns D-E, from each sheet, and create a CSV containing the combined tables with the following columns:
Offence Code | Total | Year from | Year to
Ignore the first two sheets which do not relate to financial years
```
