# Clean the combined data

Once you've combined data from multiple responses into a single CSV, you may need to tackle inconsistencies between the language or categories used by different authorities.

First, break down each problem that you face, and the different strategies for tackling each. Those might include:

* Mixed data (code and category; category and Act/Section): use *Text to columns* or `XLOOKUP` functions
* Acronyms (e.g. GBH = “Grievous bodily harm”)
* Upper/lower case: use the `LOWER` or `PROPER` function (use `IF` with `UPPER` or `REGEXMATCH` to identify all-caps entries)
* Extra/analogous characters: quotation marks, spaces, apostrophes, commas, “and”/“&”, currency symbols: use *Find and replace* or `SUBSTITUTE` function; `TRIM`; `REGEXREPLACE`
* Different category level (top level, sub-category, lower level): use the `XLOOKUP` function; `COUNTIF` to identify keywords then manual assignment to category
* Slight variations: sort and edit manually; use Open Refine's cluster and edit tool

With some of these problems you will need a 'master' list of terms (e.g. official categories) that you want to map the responses to using `XLOOKUP` and/or AI prompts like [this one](https://github.com/paulbradshaw/mass_foi_projects/blob/main/prompts/classifycategorylevel.md).

Look for spreadsheets that use official categories - in particular those which map sub-categories to their parent categories, and include any codes used as well. There's a good chance that some responses will use both top-level categories, others sub-categories and others codes. 

An example is in the [files folder](https://github.com/paulbradshaw/mass_foi_projects/tree/main/files)
