# Auditing a mass FOI project

Before starting to extract any data from multiple FOI responses, you need to know *what* data the responses contain. In other words, you need to **audit** them.

Here's how to do that:

1. Browse the FOIs, or a representative sample
2. Identify the range of information covered: 
  * Are there any questions which some bodies refused to answer?
  * Or levels of detail they couldn’t provide?
3. Identify any differences (e.g. financial year vs calendar year)
4. Decide what takes priority where multiple tables are given (e.g. offence category, not hospital or outcome)

Create a table to store the information from your FOI:
* Have a row for each response/organisation
* Have columns for format (e.g. PDF, CSV, MSG etc), timescale (e.g. annual, monthly, etc.), year type (calendar, financial, academic etc), and each piece of data asked for in the request (e.g. if you asked for crimes by category and location, and outcomes, then that's three pieces of data. Also include a column for any notes or caveats. 

You can do this manually but you can also use AI to help...

## A prompt for creating an audit in an AI tool

AI is particularly well suited for getting a quick overview on a large collection of documents or data. You don't need it to be 100% accurate - you just need to get a decent feel for what's covered by the information.

[NotebookLM](https://notebooklm.google.com/) is especially well suited for working with documents. It uses Google Gemini but you upload your documents to a 'notebook' and it will ground its responses in those (complete with linked page numbers so you can go straight to the source).

Create a new notebook in NotebookLM and upload the FOI files (you can [use the files here](https://github.com/paulbradshaw/mass_foi_projects/tree/main/foi-requests)). Then type this prompt in the chat box in the middle:

```
OBJECTIVE: You are a journalist looking to combine data from multiple FOI responses into a single table that can be analysed to identify trends over time and compare categories or bodies. 
TASK: Audit these responses and produce a table identifying the range of information covered for each force. Each column should identify Y/N if they provided that information.
Include a column indicating what type of year was used (e.g. financial vs calendar) and a column indicating whether data is provided for each incident, or per year, month, quarter, another period.
If the response includes any warnings or caveats, quote these and the page number in a caveats column.
```
