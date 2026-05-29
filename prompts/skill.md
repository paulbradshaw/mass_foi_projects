# FOI Category Classification Skill

## Purpose

Standardise inconsistent offence and incident categories from FOI responses across different public bodies. Different organisations report at different levels of granularity (top-level categories vs sub-categories vs specific offence descriptions), with spelling variations, combined offences, and embedded legal references.

## When to Use This Skill

Trigger this skill when the user:
- Mentions "FOI", "Freedom of Information", or "FOI responses"
- Asks to classify, categorise, or standardise crime/offence/incident data
- References Home Office or ONS crime categories
- Has messy category data from multiple sources that needs mapping
- Wants to map granular offences to broader categories
- Needs to handle combined offences or spelling variations

## Input Requirements

The user should provide:
1. **FOI data file(s)**: CSV, Excel, or PDF containing offence/incident categories
2. **Taxonomy reference documents**: Home Office and/or ONS spreadsheets showing category hierarchies, sub-categories, and offence descriptions

**Common taxonomy files:**
- `categoryLOOKUPTOMATCH.csv`: Maps offence codes to ONS categories with old/new category structures
- `notifiableoffencesLOOKUP.csv`: Detailed notifiable offences with class, sub-class, category, specific offence descriptions, Acts and sections

## Workflow

### Step 1: Parse Taxonomy Reference Documents

First, extract and understand the classification structure from the reference documents:

1. Read the taxonomy files (Home Office/ONS spreadsheets)
2. Identify the hierarchical structure based on the file type:

**For categoryLOOKUPTOMATCH.csv:**
   - Offence Code (e.g., "1", "8F", "19C")
   - Offence description (human-readable name)
   - Old PRC offence group / Old offence sub-group (legacy categories)
   - **New ONS offence group / New ONS sub-offence group** (current taxonomy - use these for standardisation)
   
**For notifiableoffencesLOOKUP.csv:**
   - Class (top level, e.g., "Violence Against The Person")
   - Sub class (e.g., "Homicide", "Violence with injury")
   - Offence category (code-based category, e.g., "1 Murder", "8S Assault with injury on a constable")
   - Offence (specific offence description)
   - Act (legislation name)
   - Section (section number)

3. Build an internal mapping structure that includes:
   - Hierarchical relationships (Class → Sub-class → Category → Offence)
   - Offence codes and their descriptions
   - Alternative names and variations
   - Legislation references (Acts and sections)
   - Both old and new category systems for backward compatibility

**Key considerations:**
- Taxonomy files may have multiple sheets - check all sheets
- notifiableoffencesLOOKUP has very detailed offence descriptions - use for exact matching
- categoryLOOKUPTOMATCH has simpler descriptions and codes - good for fuzzy matching
- Some offence codes appear multiple times with slight variations (e.g., 8H, 8J, 8K)
- Look for patterns: codes like "19C", "19D", "19E" are variations of the same offence type
- Prioritise "New ONS" categories over "Old PRC" categories for modern standardisation

### Step 2: Analyse FOI Data

Examine the FOI data files to understand their structure:

1. Identify which column(s) contain category/offence information
2. Assess the level of granularity used (top-level, sub-category, or specific offences)
3. Identify patterns in the data:
   - Spelling variations or typos
   - Combined/multiple offences in single entries (e.g., "Theft and Criminal Damage")
   - Embedded legal references (e.g., "S.4 Public Order Act 1986")
   - Inconsistent capitalisation or formatting
   - Use of codes vs descriptive text

### Step 3: Map Categories

For each entry in the FOI data:

1. **Clean and normalise** the input:
   - Standardise capitalisation
   - Remove extra whitespace
   - Extract any embedded legal references
   - Extract offence codes if present (e.g., "8F", "19C", "105A")

2. **Identify likely top-level category using keyword clustering**:
   
   Certain keywords are strong indicators of the top-level category:
   
   **Sexual Offences indicators:**
   - rape, sexual assault, sexual activity, indecent, pornographic, grooming, abuse of position
   - These words ONLY appear in sexual offences category
   
   **Violence Against The Person indicators:**
   - murder, manslaughter, assault, ABH, GBH, wounding, strangulation, threatening, harassment, stalking
   - High correlation with violence category
   
   **Theft Offences indicators:**
   - burglary, theft, shoplifting, robbery, stolen, taking without consent, TWOC
   - Exclusively or primarily in theft category
   
   **Drug Offences indicators:**
   - drug, cannabis, cocaine, heroin, amphetamine, possession, supply, production, trafficking, controlled substance
   - Only in drug offences category
   
   **Criminal Damage indicators:**
   - arson, damage, criminal damage, destroy, graffiti
   - Only in criminal damage category
   
   **Weapons indicators:**
   - firearms, weapon, knife, blade, offensive weapon, possession of
   - Only in weapons category
   
   Use this for:
   - **Confidence boosting**: If fuzzy match suggests Category X and keywords confirm Category X, increase confidence
   - **Validation**: If match suggests Category X but keywords strongly indicate Category Y, flag for review
   - **Generic term handling**: If input is just "DRUG OFFENCES" and contains drug keywords, confirm it's a category-level input
   - **Disambiguation**: When multiple matches possible, prefer the one matching keyword cluster

3. **Identify if combined offences** exist:
   - Look for conjunctions: "and", "&", "+"
   - Look for separators: "/", ",", ";"
   - Note: Some codes use "/" internally (e.g., "1/4.1/4.2") - don't split these
   - If combined, split and map each separately

3. **Map to taxonomy** using this priority order:
   
   **a) Check if input is a category-level term** (not a specific offence):
      - Compare cleaned input against top-level category names
      - Categories: "Sexual Offences", "Violence Against The Person", "Theft Offences", "Drug Offences", "Criminal Damage", "Robbery", "Public Order Offences", etc.
      - If high similarity (>0.8) to a category name, mark as "CATEGORY-LEVEL INPUT"
      - Return: (matched_category, "CATEGORY-LEVEL", "No specific offence - category classification only", None, 0.90, "Input is a category header, not a specific offence")
      - Add flag: `is_category_level_input = TRUE`
      - This is NOT an error - it's correct identification that more specificity is needed
      - **Also check keyword clustering**: If input is generic like "THEFT" but contains theft keywords, treat as category-level
   
   **b) Offence code match** (highest priority for specific offences):
      - Extract any codes in the format: numbers, letters, or combinations (e.g., "8F", "19C", "105A")
      - Look up in categoryLOOKUPTOMATCH by "Offence Code" column
      - If found, use the "New ONS offence group" and "New ONS sub-offence group"
   
   **b) Exact match on offence description**:
      - Match against "Offence" column in notifiableoffencesLOOKUP
      - Match against "Offence description" in categoryLOOKUPTOMATCH
   
   **c) Legislation-based match**:
      - If input contains Act name and section (e.g., "Public Order Act 1986 S.4")
      - Look up in notifiableoffencesLOOKUP by Act and Section columns
      - Return the Class and Sub-class
   
   **d) Fuzzy match on descriptions**:
      - Try fuzzy matching against offence descriptions
      - Match against "Offence category" in notifiableoffencesLOOKUP (e.g., "8S Assault with injury on a constable")
   
   **e) Category-level match**:
      - Match against Class/Sub-class in notifiableoffencesLOOKUP
      - Match against New ONS offence group/sub-offence group in categoryLOOKUPTOMATCH
   
   **f) Semantic match**:
      - Understand meaning and match to closest category using context

4. **Assign confidence score**:
   - Very High (0.95-1.0): Offence code match or exact description match
   - High (0.90-0.94): Category-level match, legislation-based match (Act + Section)
   - High with keyword boost (add 0.05-0.10): Fuzzy match where keywords confirm the category
   - Medium (0.7-0.89): Good fuzzy match or category-level match
   - Medium (0.6-0.69): Weaker fuzzy match or semantic match
   - Low with keyword conflict (reduce 0.10-0.20): Match suggests Category X but keywords indicate Category Y
   - Low (0.0-0.59): Uncertain mapping requiring review

5. **Apply keyword validation**:
   - If matched category contradicts keyword clustering, flag for review
   - If matched category confirms keyword clustering, boost confidence slightly
   - If input contains exclusive keywords (e.g., "rape" only in sexual offences), use for disambiguation

6. **Extract information**:
   - Offence code (if present)
   - Legal references: Act names and section numbers
   - Both old and new category systems (for reference)

### Step 4: Create Output

Generate an Excel/CSV file with the original data PLUS new columns:

**Core Classification Columns:**
1. `offence_code` - Extracted offence code (e.g., "8F", "19C") if present
2. `standardised_category_level1` - Top-level category from taxonomy (always populated)
3. `standardised_category_level2` - Sub-category from taxonomy (if identified)
4. `standardised_offence` - Specific offence description from taxonomy (if identified)

**Confidence and Quality Columns:**
5. `confidence_score` - Mapping confidence (0.0 to 1.0)
6. `mapping_notes` - Explanation of mapping decisions, flags for review
7. `extracted_legislation` - Any legal references found (Act and Section)

**Category Level Analysis Columns:**
8. `input_category_level` - Detected level: "TOP_LEVEL", "MID_LEVEL", "BOTTOM_LEVEL", or "UNKNOWN"
9. `category_level_confidence` - Confidence in level detection (0.0 to 1.0)
10. `can_allocate_to_top_level` - Boolean: Can this be reliably assigned to a top-level category? (TRUE/FALSE)
11. `allocation_certainty` - How certain is the top-level allocation: "CERTAIN", "PROBABLE", "AMBIGUOUS"

**Multi-Category Detection Columns:**
12. `is_combined_offence` - Boolean flag if multiple offences detected
13. `contains_multiple_top_categories` - Boolean: Do the combined offences span different top-level categories?
14. `needs_manual_untangling` - Boolean: Requires human review to separate properly

**Additional Context Columns:**
15. `keyword_cluster_detected` - Which keyword cluster was identified
16. `original_category_cleaned` - Normalised version of original input
17. `old_category_system` - Old PRC categories (for reference/backward compatibility)

**Category Level Detection Logic:**

**TOP_LEVEL identification:**
- Input matches or closely resembles a top-level category name (e.g., "Sexual Offences", "Violence Against The Person")
- No specific offence details present
- Contains only generic category keywords
- `can_allocate_to_top_level = TRUE`, `allocation_certainty = CERTAIN`

**MID_LEVEL identification:**
- Input matches a sub-category (e.g., "Violence with injury", "Domestic burglary", "Rape")
- More specific than top-level but not a detailed offence
- Can allocate to top-level with high confidence
- `can_allocate_to_top_level = TRUE`, `allocation_certainty = CERTAIN` or "PROBABLE"

**BOTTOM_LEVEL identification:**
- Input is a specific offence description (e.g., "Assault occasioning actual bodily harm", "Burglary in a dwelling")
- Contains detailed offence characteristics
- Maps through hierarchy to both sub-category and top-level
- `can_allocate_to_top_level = TRUE`, `allocation_certainty = CERTAIN`

**UNKNOWN identification:**
- Cannot determine specificity level
- May be abbreviated, truncated, or ambiguous
- `can_allocate_to_top_level` depends on whether any category can be inferred
- `allocation_certainty = AMBIGUOUS`

**Combined Category Detection:**

For entries like "Theft and Criminal Damage":
1. Detect that multiple offences present (`is_combined_offence = TRUE`)
2. Map each component to top-level category
3. If components map to SAME top-level (e.g., both are theft offences):
   - `contains_multiple_top_categories = FALSE`
   - `needs_manual_untangling = FALSE`
   - Can be allocated to single top-level category
4. If components map to DIFFERENT top-levels (e.g., "Theft" and "Criminal Damage"):
   - `contains_multiple_top_categories = TRUE`
   - `needs_manual_untangling = TRUE` 
   - Cannot allocate to single top-level category - requires separation

**Output approach for combined offences spanning multiple top categories:**
- **Option A (Recommended):** Create separate rows for each component with flag indicating they're from the same source entry
- **Option B:** Single row with primary category + notes listing all categories involved
- Flag clearly that manual intervention needed to separate the data properly

**For combined offences:**
- Create one row per offence component
- Mark with `is_combined_offence = TRUE`
- Include reference to original combined entry in notes

**Flag for review when:**
- Confidence score < 0.6
- No reasonable match found in taxonomy
- Ambiguous mapping (multiple possible matches)
- Unusual formatting or unexpected content

### Step 5: Summary Report

Provide a brief summary including:
- Total entries processed
- Distribution across top-level categories
- Number of uncertain mappings flagged for review
- Common spelling variations detected
- Number of combined offences split
- Any categories from FOI data not found in taxonomy (potential new categories)

## Technical Implementation

### Reading Files

**For Excel/CSV:**
```python
import pandas as pd
import openpyxl

# Read taxonomy
taxonomy_df = pd.read_excel('taxonomy.xlsx', sheet_name=None)  # Read all sheets

# Read FOI data
foi_data = pd.read_csv('foi_data.csv')  # or pd.read_excel()
```

**For PDF:**
Use PDF extraction tools to convert to structured data first, then process as CSV.

### Fuzzy Matching

Use string similarity for handling variations:
```python
from difflib import SequenceMatcher
from fuzzywuzzy import fuzz  # or rapidfuzz

def fuzzy_match(input_str, taxonomy_entries, threshold=80):
    best_match = None
    best_score = 0
    
    for entry in taxonomy_entries:
        score = fuzz.ratio(input_str.lower(), entry.lower())
        if score > best_score and score >= threshold:
            best_score = score
            best_match = entry
    
    return best_match, best_score / 100.0
```

### Handling Combined Offences

```python
import re

def split_combined_offences(category_text):
    # Common separators for combined offences
    separators = [' and ', ' & ', ' + ', ' / ', ', ']
    
    # Check for separators
    for sep in separators:
        if sep in category_text.lower():
            parts = re.split(re.escape(sep), category_text, flags=re.IGNORECASE)
            return [part.strip() for part in parts], True
    
    return [category_text], False
```

### Extracting Legal References

```python
def extract_legislation(text):
    # Pattern for Acts: "Name Act YEAR"
    act_pattern = r'([A-Z][A-Za-z\s]+Act\s+\d{4})'
    
    # Pattern for sections: "S.4", "Section 4", "s4"
    section_pattern = r'(?:S\.?|Section)\s*(\d+[A-Za-z]?)'
    
    acts = re.findall(act_pattern, text)
    sections = re.findall(section_pattern, text, re.IGNORECASE)
    
    return {
        'acts': acts,
        'sections': sections
    }
```

## Output Format

The output Excel/CSV should preserve ALL original columns and add the new standardised columns on the right. Use clear column headers and include a "README" sheet (for Excel) explaining the new columns and confidence scoring.

## Quality Checks

Before presenting results:
1. Verify all rows from input are in output
2. Check that high-confidence mappings look reasonable
3. Ensure flagged items are genuinely uncertain
4. Validate that combined offences are properly split
5. Confirm legal references are extracted correctly

## Common Pitfalls to Avoid

- Don't assume consistent column names across FOI files
- Don't ignore context from surrounding columns (e.g., incident counts, dates)
- Don't map aggressively - it's better to flag for review than force a poor match
- Don't lose the original data - always preserve it
- Don't forget about edge cases: blank entries, "Unknown", "Other", "N/A"

## Edge Cases

- **Blank or missing categories**: Flag as requiring review, don't map
- **"Other" or "Unknown"**: Keep as-is, flag that taxonomy may be incomplete
- **Historical offence names**: May not appear in modern taxonomy - flag for review
- **Regional variations**: Scottish law differs from English/Welsh - be aware
- **Free text descriptions**: May need more semantic understanding than exact matching

## Iterative Refinement

After showing initial results:
1. Ask user to review flagged uncertain mappings
2. Learn from user corrections
3. Ask if there are systematic patterns to adjust
4. Re-run with improved mappings if needed
