# Prompt used in Codex to classify crime categories

This was the prompt used in Codex to try to classify 570 different offence descriptions used in police force responses. 

The resulting file matched my own classifications in 199 descriptions, but did not for 139. For the remaining 232 the descriptions did not match the list in the cleaned files.

```
The `classifying crimes in FOIs` folder has two CSV files in it containing crime classification categories.

Within that folder is a folder called `FOI responses` containing CSV files with data from 30 police forces.

There is a lot of inconsistency between the forces in the categories that they use: some use categories from different levels, some enter the category inconsistently (e.g. missing words, abbreviations) and some include extra information (e.g. Act/section and/or crime code)



Create a lookup CSV which contains the following:

A column with all the unique categories used in the police force responses

A column with the 'clean' version of that category, based on the lookup files (no code or act/law etc.)

A column indicating which category level that clean version was taken from (e.g. class, sub class, new ONS offence group etc)

A column indicating level of confidence in the match (high, medium, low)

A column with the 'Class' for the offence (top level of category)

A column indicating level of confidence in the Class match (high, medium, low)
```
