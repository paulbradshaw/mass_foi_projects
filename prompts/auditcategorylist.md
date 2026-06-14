# Prompt template for auditing a list of categories

When receiving responses to an FOI request from multiple authorities, it's likely that different authorities will have used different language to refer to the same entities or similar categories. 

To make the terminology consistent enough to answer questions accurately, you'll need to clean the data. 

But this won't be a single cleaning task, it will involve various different cleaning methods to tackle different problems with the data.

It can be useful to use AI to 'audit' the data to identify which problems are most widespread, and which terms those problems are most likely to apply to.

Here's a template prompt to do that.

```
# OBJECTIVE:
I am a journalist looking to tell a story about the most common categories in data from multiple FOI responses.
However, the responses are inconsistent and need cleaning so that they can be accurately questioned.
You are an experienced, sceptical data journalist who is helping me manage the project.
We need to audit the category text to identify what cleaning methods will need to be applied to each.

# TASK:
Look at the attached list of unique categories from the data.
Classify each entry against the following six categories of problem:

1. Mixed data (for example, category and code, or crime category and Act/Section)
2. Acronyms (for example some authorities using GBH while others use “Grievous bodily harm”)
3. Upper/lower case inconsistency (for example ARSON AND CRIMINAL DAMAGE vs Arson & Criminal Damage)
4. Extra/analogous characters (for example some responses may use quotation marks, spaces, apostrophes, commas, and currency symbols where others don't.
Or some may be “and” while others use “&”)
5. Slight variations (e.g. one authority uses "Criminal damage and arson" while another users "Criminal damage and arson offences")
6. Different category level (e.g. some use the top level categories, others sub-category, and others lower level categories).

I've attached a lookup file so you can see the different categories that could be used. 

# OUTPUT
Provide the classification as a new CSV with the original list as one column
Assign each of the first 5 problem categories to their own column, classified as either FALSE or TRUE.
Where TRUE, give a description of the problem.
In a further column, attempt to identify the category level based on the lookup file.
Add a column with a description of why that category level appears to apply
Add a column indicating confidence level in that category level (high, medium, low)

# NOTES
Apply a transparent, rule-based detection 
Every flag needs to be reproducible and defensible for a journalist
Alert me to any information or context which is missing, or any potential dangers to consider
Also provide code that can be used in Colab to classify the data along the same lines.
The code should be clearly commented so that a non-coder can understand what it does.
```
