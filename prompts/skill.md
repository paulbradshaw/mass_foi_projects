---
name: foi-classifier
description: Standardise inconsistent offence and incident categories from Freedom of Information (FOI) responses. Use when users need to classify, map, or standardise crime/incident categories from multiple public bodies with varying levels of granularity. Handles messy data including spelling variations, combined offences, legal references, and maps to Home Office/ONS taxonomy hierarchies. Trigger this skill whenever the user mentions FOI data, crime categories, offence classification, or wants to standardise police/incident data — even if they don't use the word "classify".
---

# FOI Category Classification Skill

## Purpose

Standardise inconsistent offence and incident categories from FOI responses across different public bodies. Different organisations report at different levels of granularity (top-level categories vs sub-categories vs specific offence descriptions), with spelling variations, combined offences, and embedded legal references.

## Taxonomy Reference

The taxonomy is **bundled with this skill** — no user upload required.

**File:** `references/notifiable_offences.csv` (relative to this SKILL.md)

**Columns:**
- `Class` — top-level category (e.g., "Violence Against The Person", "Sexual Offences")
- `Sub class` — sub-category (e.g., "Homicide", "Violence with injury")
- `Offence category` — code-prefixed category (e.g., "1 Murder", "8S Assault with injury on a constable")
- `Offence` — specific offence description
- `Act` — legislation name
- `Section` — section number
- `Home Office code` — offence code (e.g., "001/01", "8F")
- `Current` — all rows are "Y" (current); no filtering needed

**Top-level Classes (12 total):**
- Violence Against The Person
- Sexual Offences
- Robbery
- Burglary
- Theft
- Vehicle offences
- Arson and Criminal Damage
- Drug Offences
- Possession of weapons
- Public Order Offences
- Miscellaneous Crimes Against Society
- NFIB Fraud

Load the taxonomy at the start of every classification task:

```python
import pandas as pd

taxonomy = pd.read_csv('references/notifiable_offences.csv')
```

## When to Use This Skill

Trigger this skill when the user:
- Mentions "FOI", "Freedom of Information", or "FOI responses"
- Asks to classify, categorise, or standardise crime/offence/incident data
- References Home Office or ONS crime categories
- Has messy category data from multiple sources that needs mapping
- Wants to map granular offences to broader categories
- Needs to handle combined offences or spelling variations

## Input Requirements

The user provides **only their FOI data file(s)** — CSV, Excel, or PDF containing offence/incident categories. The taxonomy is already bundled.

## Workflow

### Step 1: Load the Taxonomy

Read `references/notifiable_offences.csv` and build lookup structures:

```python
import pandas as pd

taxonomy = pd.read_csv('references/notifiable_offences.csv')

# Build lookup sets for matching
classes = set(taxonomy['Class'].dropna().str.strip().unique())
sub_classes = set(taxonomy['Sub class'].dropna().str.strip().unique())
offence_categories = taxonomy['Offence category'].dropna().str.strip().tolist()
offences = taxonomy['Offence'].dropna().str.strip().tolist()

# Build code-to-row lookup (Home Office codes like "001/01", "8F")
code_lookup = taxonomy.dropna(subset=['Home Office code']).set_index('Home Office code')
```

### Step 2: Analyse FOI Data

Examine the FOI data files to understand their structure:

1. Identify which column(s) contain category/offence information
2. Assess the level of granularity (top-level, sub-category, or specific offences)
3. Identify patterns:
   - Spelling variations or typos
   - Combined/multiple offences in single entries (e.g., "Theft and Criminal Damage")
   - Embedded legal references (e.g., "S.4 Public Order Act 1986")
   - Inconsistent capitalisation or formatting
   - Use of codes vs descriptive text

### Step 3: Map Categories

For each entry in the FOI data:

**1. Clean and normalise the input:**
- Standardise capitalisation
- Strip extra whitespace
- Extract any embedded legal references
- Extract offence codes if present (e.g., "8F", "001/01")

**2. Identify likely top-level category using keyword clustering:**

Certain keywords are strong indicators:

| Category | Keywords |
|---|---|
| Sexual Offences | rape, sexual assault, sexual activity, indecent, pornographic, grooming, abuse of position |
| Violence Against The Person | murder, manslaughter, assault, ABH, GBH, wounding, strangulation, threatening, harassment, stalking |
| Theft | burglary, theft, shoplifting, robbery, stolen, taking without consent, TWOC |
| Drug Offences | drug, cannabis, cocaine, heroin, amphetamine, possession, supply, production, trafficking, controlled substance |
| Arson and Criminal Damage | arson, damage, criminal damage, destroy, graffiti |
| Possession of weapons | firearms, weapon, knife, blade, offensive weapon |

Use keyword clustering to:
- Boost confidence when fuzzy match and keywords agree
- Flag for review when they disagree
- Disambiguate when multiple matches are possible
- Confirm category-level inputs (e.g., input is just "DRUG OFFENCES")

**3. Detect combined offences:**
- Look for conjunctions: "and", "&", "+"
- Look for separators: "/", ",", ";"
- Note: Some codes use "/" internally (e.g., "001/01") — don't split these
- If combined, split and map each part separately

**4. Map to taxonomy using this priority order:**

**a) Category-level match** — check if input is a top-level or sub-level term, not a specific offence:
- Compare against `Class` values (similarity > 0.8) → mark `is_category_level_input = TRUE`
- Return: `(matched_class, "CATEGORY-LEVEL", "No specific offence — category classification only", None, 0.90)`
- This is correct behaviour, not an error

**b) Home Office code match** (highest priority for specific offences):
- Extract codes in format: digits, letters, or combinations (e.g., "8F", "001/01")
- Look up in `code_lookup` by `Home Office code` column
- If found, return `Class`, `Sub class`, `Offence category`

**c) Exact match on offence description:**
- Match cleaned input against `Offence` column
- Also try against `Offence category` column

**d) Legislation-based match:**
- If input contains Act name and section (e.g., "Public Order Act 1986 S.4")
- Filter taxonomy by `Act` and `Section` columns
- Return `Class` and `Sub class`

**e) Fuzzy match on descriptions:**
- Fuzzy match against `Offence`, `Offence category` columns
- Use keyword clustering to disambiguate

**f) Semantic match:**
- Understand meaning and map to closest category using context

**5. Assign confidence score:**

| Score | Meaning |
|---|---|
| 0.95–1.0 | Home Office code match or exact description match |
| 0.90–0.94 | Category-level match, legislation-based match (Act + Section) |
| +0.05–0.10 boost | Fuzzy match where keywords confirm category |
| 0.70–0.89 | Good fuzzy match or category-level match |
| 0.60–0.69 | Weaker fuzzy match or semantic match |
| −0.10–0.20 penalty | Match suggests Category X but keywords indicate Category Y |
| 0.0–0.59 | Uncertain — flag for review |

**6. Extract information:**
- Home Office code (if present)
- Legal references: Act names and section numbers
- Input specificity level (see Step 4)

### Step 4: Classify Input Specificity

Determine how specific the input is:

| Level | Description | Example |
|---|---|---|
| `TOP_LEVEL` | Matches a top-level Class name; no specific offence details | "Sexual Offences" |
| `MID_LEVEL` | Matches a sub-category | "Violence with injury", "Domestic burglary" |
| `BOTTOM_LEVEL` | Specific offence description | "Assault occasioning actual bodily harm" |
| `UNKNOWN` | Abbreviated, truncated, or ambiguous | — |

For all levels: `can_allocate_to_top_level = TRUE` unless input is completely unrecognisable.

`allocation_certainty` values: `CERTAIN`, `PROBABLE`, `AMBIGUOUS`

**Combined offences spanning multiple top-level categories:**
- Preferred: create one row per component, flagged as combined
- Alternative: single row with primary category + notes
- Always flag that manual intervention may be needed

### Step 5: Output

Produce an Excel or CSV file preserving **all original columns**, with these added to the right:

| Column | Description |
|---|---|
| `matched_class` | Top-level ONS/Home Office category |
| `matched_sub_class` | Sub-category |
| `matched_offence_category` | Code-prefixed offence category |
| `matched_offence` | Specific offence description |
| `home_office_code` | Home Office code if matched |
| `matched_act` | Legislation Act |
| `matched_section` | Section number |
| `confidence_score` | 0.0–1.0 |
| `match_method` | e.g., "code_match", "exact", "fuzzy", "legislation", "semantic" |
| `input_specificity` | TOP_LEVEL / MID_LEVEL / BOTTOM_LEVEL / UNKNOWN |
| `can_allocate_to_top_level` | TRUE / FALSE |
| `allocation_certainty` | CERTAIN / PROBABLE / AMBIGUOUS |
| `is_combined_offence` | TRUE / FALSE |
| `contains_multiple_top_categories` | TRUE / FALSE |
| `needs_manual_untangling` | TRUE / FALSE |
| `flag_for_review` | TRUE / FALSE |
| `review_reason` | Why flagged (if applicable) |
| `keyword_cluster_detected` | Which keyword cluster was identified |
| `original_category_cleaned` | Normalised version of original input |

For Excel output, include a "README" sheet explaining columns and confidence scoring.

### Step 6: Summary Report

Provide a brief summary including:
- Total entries processed
- Distribution across top-level categories
- Number of uncertain mappings flagged for review
- Common spelling variations detected
- Number of combined offences split
- Any categories from FOI data not found in taxonomy

## Technical Implementation

### Reading Files

```python
import pandas as pd

# Load taxonomy (bundled)
taxonomy = pd.read_csv('references/notifiable_offences.csv')

# Load FOI data (user-provided)
foi_data = pd.read_csv('foi_data.csv')        # or
foi_data = pd.read_excel('foi_data.xlsx')
```

### Fuzzy Matching

```python
from rapidfuzz import fuzz  # prefer rapidfuzz over fuzzywuzzy

def fuzzy_match(input_str, candidates, threshold=80):
    best_match, best_score = None, 0
    for candidate in candidates:
        score = fuzz.ratio(input_str.lower(), candidate.lower())
        if score > best_score and score >= threshold:
            best_score = score
            best_match = candidate
    return best_match, best_score / 100.0
```

### Handling Combined Offences

```python
import re

def split_combined_offences(text):
    # Avoid splitting Home Office codes like "001/01"
    if re.match(r'^\d{3}/\d{2}$', text.strip()):
        return [text], False
    separators = [' and ', ' & ', ' + ', ', ', '; ']
    for sep in separators:
        if sep.lower() in text.lower():
            parts = re.split(re.escape(sep), text, flags=re.IGNORECASE)
            return [p.strip() for p in parts], True
    return [text], False
```

### Extracting Legal References

```python
def extract_legislation(text):
    act_pattern = r'([A-Z][A-Za-z\s]+Act\s+\d{4})'
    section_pattern = r'(?:S\.?|Section)\s*(\d+[A-Za-z]?)'
    acts = re.findall(act_pattern, text)
    sections = re.findall(section_pattern, text, re.IGNORECASE)
    return {'acts': acts, 'sections': sections}
```

## Quality Checks

Before presenting results:
1. Verify all rows from input appear in output
2. Check high-confidence mappings look reasonable
3. Ensure flagged items are genuinely uncertain
4. Validate combined offences are properly split
5. Confirm legal references are extracted correctly

## Common Pitfalls

- Don't assume consistent column names across FOI files
- Don't ignore context from surrounding columns (incident counts, dates)
- Don't map aggressively — flag uncertain cases rather than force a poor match
- Don't lose original data — always preserve all input columns
- Don't forget edge cases: blank entries, "Unknown", "Other", "N/A"

## Edge Cases

- **Blank or missing categories**: Flag for review, don't map
- **"Other" or "Unknown"**: Keep as-is, note taxonomy may be incomplete
- **Historical offence names**: May not appear in modern taxonomy — flag for review
- **Regional variations**: Scottish law differs from English/Welsh — flag if suspected
- **Free text descriptions**: May need more semantic understanding than exact matching

## Iterative Refinement

After showing initial results:
1. Ask the user to review flagged uncertain mappings
2. Learn from user corrections
3. Ask if there are systematic patterns to adjust
4. Re-run with improved mappings if needed
