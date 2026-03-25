# Prompts for checking data

The following are examples of prompts that might be used to check extracted data against the original PDF.

Make sure that you start a new chat to run any checking prompts, so they are not influenced by the chats where data was first extracted. Also check that [memory isn't retained between chats](https://www.howtogeek.com/how-to-disable-chatgpt-memory-and-delete-saved-memory/). 


## All in one

```
Attached is a CSV and the source PDF.
Focus only on the table(s) specified below and perform all of the following checks.
Report findings by section.
Target table: [DESCRIBE LOCATION/LABELS/STRUCTURE]
Ignore any tables that [DESCRIBE OTHERS]
If you cannot read all pages of the PDF (e.g., due to encryption or rendering limits), explicitly state which pages were validated and confine all findings to those pages. Recommend next steps for validating the remaining pages (e.g., obtaining a non-encrypted copy or exporting page by page).
Conclude with a one-line verdict: Accurate — no discrepancies found across all checks, or Discrepancies found — followed by a brief summary of the most significant issues.

## CHECKS
1. Value matching
Compare every individual cell value between the PDF table and the CSV.
Report any numerical discrepancies as: *PDF value → CSV value* with row label and column/year.
Identify every instance where the PDF shows non-numerical values such as `–` or `<5` alongside numerical values. For each, state how it appears in the CSV, and assess whether the representation affects totals (e.g., `<5` stored as text vs a numeric value).
Note cosmetic differences separately (header typos, spacing, em-dash vs zero, etc)

2. Row matching
Flag any rows or categories present in the PDF but missing from the CSV, or extra rows in the CSV not in the PDF.
Check for systematic gaps in the CSV relative to the PDF, such as one missing row per page or missing first/last rows at page breaks.
If the PDF contains sequential IDs or numbering, list any missing IDs, infer any pattern (e.g., every 59th row absent), and provide examples.

3. Totals 
Compute the totals for the CSV and check those exactly match totals in the PDF (calculate those if not printed)
Do this for both row totals, column totals, and any other totals
Report any mismatches with the relevant row label and year column(s).

4. Label checks
Compare all category, group, or label strings between the PDF and CSV.
Identify mismatches caused by punctuation, whitespace, or character encoding differences (e.g., curly apostrophes vs straight, en-dash vs hyphen).
For each discrepancy, map the PDF label to its CSV variant, estimate how many rows are affected, and confirm whether the underlying numeric values are otherwise aligned.

5. Count checks
Check that the CSV has the expected number of values based on the number of rows and columns in the PDF, excluding grand totals (e.g. 5 columns × 8 rows = 40 values)
Count how many values are in each row in the CSV, and how many are in each row in the PDF. Identify any mismatches.
```
