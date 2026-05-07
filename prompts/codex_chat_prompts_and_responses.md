# ChatGPT Codex transcript

Codex is an AI 'agent' that runs on ChatGPT, available on the Pro plan and downloaded as a separate app. 

This transcript records prompts and responses used to see how it performed on a pure vibe coding approach to combining the FOI hospital crimes data. Tool output and raw terminal logs are omitted.

When compared to the actual responses, the vibe coding result was:

* Missing 100,000 incidents, roughly half of the total
* Missing one third of police forces (10)
* Missing incidents for over half of the remaining police forces (13 of 20)

As a result this approach is not recommended.

---

## Turn 1

### User

I have a folder of FOI responses with data about crimes in hospitals. Help me get them into a consistent shape

### Assistant

I can help, but the folder I can currently access is empty:

`/Users/paul/Documents/Codex/2026-05-07/i-have-a-folder-of-foi`

Please add or upload the FOI response files into that folder, then tell me when they're there. I'll inspect the formats, extract the columns, and produce a cleaned consistent dataset plus a repeatable script/workflow so you can rerun it as more responses arrive.

---

## Turn 2

### User

I've created a new folder called FOI responses

### Assistant

Done. I created a `normalised` folder here:

[FOI responses/normalised](/Users/paul/Documents/Codex/FOI%20responses/normalised)

Main outputs:

[normalised_hospital_crimes.xlsx](/Users/paul/Documents/Codex/FOI%20responses/normalised/normalised_hospital_crimes.xlsx)  
[normalised_hospital_crimes.csv](/Users/paul/Documents/Codex/FOI%20responses/normalised/normalised_hospital_crimes.csv)  
[source_summary.csv](/Users/paul/Documents/Codex/FOI%20responses/normalised/source_summary.csv)  
[extraction_notes.csv](/Users/paul/Documents/Codex/FOI%20responses/normalised/extraction_notes.csv)  
[normalise_foi.py](/Users/paul/Documents/Codex/FOI%20responses/normalised/normalise_foi.py)

The normalised table has `24,467` rows: `20,409` incident-level rows and `4,058` aggregate rows. I kept `record_level`, `measure_type`, `period`, and `period_type` explicit so incident disclosures, offence totals, outcome totals, hospital totals, financial years, calendar years, and monthly rows do not get misleadingly blended.

A few PDFs are marked `text_only` in the notes because the text extracted but the table layout was too ambiguous for a safe automatic parse: North Wales PDF, Nottinghamshire, Surrey, Sussex, and West Mercia. North Wales is already covered by the XLSX, so that one is not a real gap.

---

## Turn 3

### User

Create a separate version of hospital crimes which just focuses on incidents by category. To avoid double counting it should not include separate counts of incidents by hospital or outcome.
However, if there are figures showing both category and another measure (e.g. outcome and/or hospital) that information should also be included.

### Assistant

Done. I created the category-focused version here:

[hospital_crimes_by_category.xlsx](/Users/paul/Documents/Codex/FOI%20responses/normalised/hospital_crimes_by_category.xlsx)  
[hospital_crimes_by_category.csv](/Users/paul/Documents/Codex/FOI%20responses/normalised/hospital_crimes_by_category.csv)

It has `22,813` rows and includes only:

`incident` rows, where the incident has a category and any attached hospital/outcome is retained  
`offence_count` rows, category totals only  
`offence_outcome_count` rows, where category and outcome are shown together

I excluded separate `hospital_count`, `outcome_count`, and `total_count` rows to avoid double counting. The Excel file includes a `rules` tab plus summaries of what was included/excluded.

---

## Turn 4

### User

Compare with combined_csv_files.csv and identify any inconsistencies

### Assistant

Compared them and wrote the audit outputs here:

[comparison_with_combined_csv_files.xlsx](/Users/paul/Documents/Codex/FOI%20responses/normalised/comparison_with_combined_csv_files.xlsx)  
[comparison_with_combined_issues.csv](/Users/paul/Documents/Codex/FOI%20responses/normalised/comparison_with_combined_issues.csv)  
[comparison_with_combined_summary.md](/Users/paul/Documents/Codex/FOI%20responses/normalised/comparison_with_combined_summary.md)

Headline inconsistencies:

- **Met / Metropolitan Police is missing** from `hospital_crimes_by_category.csv`. `combined_csv_files.csv` has `1,375` Met rows, total count `87,890`.
- **Merseyside differs materially**. Combined has `648` rows / total `5,792`; category file has `803` rows / total `17,775` because it includes both category totals and category+outcome rows. Even comparing only category+outcome rows, category file is `47` rows and `132` counts higher.
- **City of London matches** on rows and total count: `195` in both. Differences are naming-level.
- **Kent matches** on rows and total count: `8,156` in both. Differences are category naming: combined splits offence titles into broader `Crime category`, while the category file keeps fuller offence titles.
- Most other forces are not in `combined_csv_files.csv`, so those are coverage differences, not count inconsistencies.

---

## Turn 5

### User

Sorry that was the wrong file. Compare with this CSV instead: 30forcesFeb18version.csv

### Assistant

Done. I created the corrected comparison here:

[comparison_with_30forcesFeb18version.xlsx](/Users/paul/Documents/Codex/FOI%20responses/normalised/comparison_with_30forcesFeb18version.xlsx)  
[comparison_with_30forcesFeb18version_issues.csv](/Users/paul/Documents/Codex/FOI%20responses/normalised/comparison_with_30forcesFeb18version_issues.csv)  
[comparison_with_30forcesFeb18version_summary.md](/Users/paul/Documents/Codex/FOI%20responses/normalised/comparison_with_30forcesFeb18version_summary.md)

Headline inconsistencies:

- `30forcesFeb18version.csv`: `30,823` rows, total numeric count `211,673`.
- `hospital_crimes_by_category.csv`: `22,813` rows, total numeric count `111,269`.
- The Feb file has `29` normalised forces; the category file has `20`; all 20 category-file forces appear in the Feb file.
- Missing from `hospital_crimes_by_category`: **Cleveland, Durham, Essex, Greater Manchester Police, Metropolitan Police, Nottinghamshire, Surrey, Sussex, West Mercia**.
- Exact matches at force total level: **City of London** and **Kent**.
- Major overlapping-force count differences include **Merseyside**, **Norfolk and Suffolk**, **Leicestershire**, **Cambridgeshire**, **Cumbria**, and **PSNI**.

The workbook has the useful detail tabs: `force_overview`, `category_total_diffs`, and a `detail_diffs_sample`.

---

## Turn 6

### User

Repeat the process with this list of categories from police forces:

Category in 29forces data
"Public fear, alarm or distress"
"Racially or religiously aggravated public fear, alarm or distress"
Absconding from lawful custody
Action Fraud
Administering poison with intent to injure or annoy
Affray
Affray - Public Order Act 1986. Sec 3
Aggravated burglary business & community
Aggravated vehicle taking
Aggravated vehicle taking (driving / being carried) offences causing damage to vehicle and / or property under £5000
All other theft offences
Arrange or facilitate travel of another person with a view to exploitation
Arrange or facilitate travel of another person with a view to exploitation - Modern Slavery Act 2015 Sec 2 (1) & 5 (1)
Arson
Arson & Criminal Damage
ARSON AND CRIMINAL DAMAGE
Arson endangering life
Arson endangering life - Criminal Damage Act 1971 Sec 1
Arson not endangering life
Assault (without injury) on a constable (Police Act 1996)
Assault constable - ABH
Assault Constable - assault occasioning actual bodily harm
Assault emergency worker - ABH
Assault Emergency Worker - actual bodily harm (Sec 47)
Assault emergency worker - wounding with intent to do GBH (Sec 18)
Assault Occasioning ABH on an Emergency Worker - Offences against the Person Act 1861 Sec 47
Assault occasioning actual bodily harm - Offences against the Person Act 1861 Sec 47
Assault occasioning actual bodily harm (Sec 47)
Assault occasioning actual bodily harm (Section 47)
Assault on a constable - Police Act 1996 Sec 89(1)
Assault on a constable occasioning actual bodily harm - Offences against the Person Act 1861 Sec 47
Assault on a female by penetration
Assault on a male by penetration
Assault or assault by beating of a constable
Assault or assault by beating of a constable (Assaults on Emergency Workers (Offences) Act 2018 Sec 1)
Assault or assault by beating of an emergency worker (except a constable)
Assault or assault by beating on a constable - Criminal Justice Act 1988 Sec 39 as added by Assaults on Emergency Workers (Offences) Act 2018 Sec 1
Assault or assault by beating on a emergency worker (except a constable) - Criminal Justice Act 1988 Sec 39 as added by Assaults on Emergency Workers (Offences) Act 2018 Sec 1
Assault or Assault by beating on an emergency worker (except a constable)
Assault Other Emergency Worker
Assault Police
Assault Police Officer - Sec 47. Assault occasioning actual bodily harm
Assault with Injury
Assault with injury EW
Assault with injury on a constable
Assault with injury PC
Assault with Intent to cause Serious Harm
Assault without Injury
Assault Without Injury on a Constable
Assault without injury on a constable (Police Act offence)
Assault without injury on a constable, Assault or Assault by beating on a constable
Assault without injury PC
Assaults on an emergency worker (other than constable)
Assaults on emergency workers (other than constables)
Assisting a detained person to escape
Attempted
Attempted - Absconding from lawful custody
Attempted - Arson not endangering life
Attempted - Assault or assault by beating of a constable
Attempted - Assault or assault by beating of an emergency worker (except a constable)
Attempted - Assault Other Emergency Worker -Wounding with intent to do grievous bodily harm (Indictable) (S.18)
Attempted - Assault with Injury - Assault occasioning actual bodily harm
Attempted - Assault with Intent to cause Serious Harm - Wounding with intent to do grievous bodily harm
Attempted - Assault without Injury - Common assault and battery
Attempted - Assisting a prisoner to escape
Attempted - Causing intentional harassment, alarm or distress
Attempted - Causing or inciting a male child under 16 to engage in sexual activity No Penetration - Offender 18 or over
Attempted - Conveyance etc of List A articles into or out of prison
Attempted - Kidnapping - Kidnapping
Attempted - Other criminal damage to a building - business and community (£5,000 and over)
Attempted - Other criminal damage to a building - business and community (Under £5,000)
Attempted - Other criminal damage to a building other than a dwelling (Under £5,000)
Attempted - Other criminal damage to a residence (Under £5,000)
Attempted - Other criminal damage to a vehicle (Under £5,000)
Attempted - Other criminal damage, other (Under £5,000)
Attempted - Racially or religiously aggravated intentional harassment, alarm or distress
Attempted - Robbery (of Personal Property)
Attempted - Sexual assault on a female
Attempted - Sexual assault on a male
Attempted - Theft from the person of another
Attempted - Theft if not classified elsewhere
Attempted Burglary
Attempted Burglary - Business & Community - Theft Act 1968 Sec 9
Attempted burglary business & community
Attempted burglary business and community
Attempted murder - Common Law. Criminal Attempts Act 1981
Attempted rape of a female aged 16 or over
Attempted rape of a male aged 16 or over
Attempted rape of a male child under 13 by a male
Bicycle theft
Blackmail
Breach a sexual risk order / risk of sexual harm order etc. or fail to comply with Sec 122 C (4) in relation to a sexual risk order
Breach of a criminal behaviour order
Breach of a Criminal Behaviour Order Sentencing Act
Breach of a restraining order
Breach of a restraining order - Sentencing Act 2020 Sec 363 (1) (2).
Breach of a stalking order or interim stalking order
Breach of conditions of injunction against harassment
Breach of conditions of injunction against harassment - Protection from Harassment Act 1997 Sec 3
Breach of Criminal behaviour order
Breach of non
Breach of non-molestation order
Breach Sexual Harm Prevention Order (SHPO) Sentencing Act 2020 Sec 354 (1) (4)
Burglary
Burglary - Business & Community - Theft Act 1968 Sec 9
Burglary - Business and Community
BURGLARY - BUSINESS/COMMUNITY
Burglary - Residential
Burglary business & community
Burglary business and community
Burglary residential
Burglary Residential (Dwelling)
BURGLARY RESIDENTIAL (UNCONNECTED BLDG)
Business and community burglary
Care provider breach duty of care resulting in ill
Care worker ill
Care worker ill-treat / wilfully neglect an individual
Care worker ill-treat /wilfully neglect an individual - Criminal Justice and Courts Act 2015 Sec 20 (1) and (2)
Care workers : Sexual activity with a person with a mental disorder - Male person - Sexual Offences Act 2003 Sec 38
Care workers: Causing or inciting sexual activity with a person with a mental disorder: penetration
Care workers: Sexual activity with a person with a mental disorder: Female person
Care workers: Sexual activity with a person with a mental disorder: Male person
Causing a child under 13 to watch a sexual act - Offender aged 18 or over - Sexual Offences Act 2003 Sec 12
Causing a person to engage in sexual activity without consent: Male person
Causing a person with a mental disorder impeding choice to watch a sexual act
Causing intentional harassment, alarm or distress
Causing intentional harassment, alarm or distress - Public Order Act 1986 Sec 4A
Causing intentional harassment, alarm or distress (S4A POA)
Causing intentional harassment, alarm or distress (Sec 4a POA)
Causing intentional harassment, alarm or distress (Sec 4A)
Causing or inciting a child under 13 to engage in sexual activity by an offender under 18 years of age: Female child
Causing or inciting a child under 13 to engage in sexual activity: Female child
Causing or inciting a child under 16 to engage in sexual activity - Female child - no penetration - Sexual Offences Act 2003 Sec 10
Causing or inciting a child under 16 to engage in sexual activity - Female child penetration - Sexual Offences Act 2003 Sec 10
Causing or inciting a female child under 16 to engage in sexual activity No Penetration
Causing or inciting a female child under 16 to engage in sexual activity, no penetration, offender aged under 18
Causing or inciting a female child under 16 to engage in sexual activity, penetration, offender aged under 18
Causing or inciting a person with a mental disorder impeding choice to engage in sexual activity: Female person
Causing sexual activity without consent
Cheque, Plastic Card and Online Bank Accounts (not PSP)
Child Abduction
Commit offence of kidnapping or false imprisonment with intention of arranging travel with view to exploitation
Committing or conspiring to commit an act outraging public decency
Committing or conspiring to commit, an act outraging public decency
Committing or conspiring to commit, an act outraging public decency - Common Law
Common assault (excluding on PC)
Common assault (S39) and assault without injury (Class 105A)
Common Assault and Battery
Common assault and battery - Criminal Justice Act 1988 Sec 39
Common assault and battery (Violence without injury)
Communicating false information alleging presence of bomb
Concealing etc criminal property
Controlling and coercive behavious
Conveyance etc. of List A articles into or out of prison - Prison Act 1952 as inserted by Offender Management Act 2007
CRIMINAL DAMAGE
Criminal Damage - Dwelling
Criminal Damage - Other Than Dwelling
Criminal Damage - Vehicle
CRIMINAL DAMAGE & ARSON
Criminal Damage and Arson
Criminal damage and arson offences
Criminal Damage excluding Arson
Criminal damage to a building - business and community (under £5000)
Criminal Damage to a Building other than a Dwelling
Criminal Damage to a Dwelling
Criminal damage to a residence (under £5000)
Criminal Damage to a Vehicle
Criminal damage to other property (under £5000)
Criminal damage to vehicle (under £5000)
Cruelty to and neglect of children
Cruelty to and neglect of children - Children and Young Persons Act 1933 Sec 1
Cruelty to Children/Young Persons
Damage to dwellings
Damage to other buildings
Damage to vehicles
Dangerous Driving
Dating Scam
Death or serious injury caused by unlawful driving
Disclose or threats to disclose private sexual photographs/film with intent to cause distress
Disclose private sexual photographs and films with intent to cause distress Criminal Justice and Courts Act Sec 33(1) (9)
"Disclosure, Obstruction, False Or Misleading
Statements Etc"
Drug - Possession
Drug - Production
Drug - Supply
Drug Offence
DRUG OFFENCES
DRUG POSSESSION
Drug Possession and Use
Drug Trafficking
Emergency Worker - Sec 20. Malicious wounding or inflicting grievous bodily harm (without intent)
Emergency Worker - Sec 47. Assault occasioning actual bodily harm
Endangering Life
Engage in controlling/coercive behaviour in an intimate / family relationship.
Engage in controlling/coercive behaviour in an intimate family relationship
Engage in controlling/coercive behaviour in an intimate/family relationship - Serious Crime Act 2015 Sec 76
Engage in sexual communication with a child
EXPIRED
EXPIRED – NOT FOR USE AFTER 30/01/24
Exposure
Exposure  (S66, SOA 2003)
Exposure - Sexual Offences Act 2003 Sec 66
Exposure and Voyeurism
False imprisonment
Fear or provocation of violence
Fear or provocation of violence - Public Order Act 1986 Sec 4
Fear or provocation of violence (S4 POA)
Fear or provocation of violence (Sec 4)
Forgery or Use of Drug Prescription
Fraud
Fraud offences to 2012/13
Going Equiped for Stealing etc.
Going equipped for stealing etc
Harassment
Harassment - Protection from Harassment Act 1997 Sec 2
Harassment - Putting people in fear of violence - Protection from Harassment Act 1997 Sec 4
Harassment (S2 Harassment Act 1997)
Harassment, alarm or distress - Public Order Act 1986 Sec 5
Harassment, alarm or distress (S5 POA)
Harassment, alarm or distress (Sec 5 POA)
Harassment, alarm or distress (Sec 5)
Having an article with a blade or point in a public place
Having an article with a blade or point in a public place - Criminal Justice Act 1988 Sec 139 as amended by Offensive Weapons Act 1996
Having possession of a controlled drug
Having possession of a controlled drug - Amphetamine
Having possession of a controlled drug - Cannabis
Having possession of a controlled drug - cannabis - Misuse of Drugs Act 1971 Sec 5(2)
Having possession of a controlled drug - Class A - Other - Misuse of Drugs Act 1971 Sec 5(2)
Having possession of a controlled drug - Class C drug
Having possession of a controlled drug - Cocaine
Having possession of a controlled drug - Heroin - Misuse of Drugs Act 1971 Sec 5(2)
Homicide
In charge of dog out of control in ANY place injuring any person or assistance dog
Incest or familial sexual offences
Intentionally / recklessly cause a public nuisance - Police, Crime, Sentencing and Courts Act 2022
Intentionally doing an act capable of encouraging or assisting the suicide or attempted suicide of another
Interference with a motor vehicle
Interference with a motor vehicle - Criminal Attempts Act 1981 Sec 9
Interfering With A Motor Vehicle
Intimidating a juror or witness or person assisting in investigation of offence
Intimidation or intending to intimidate a witness - Criminal Justice & Police Act 2001 Sec 39
Kidnapping
Less Serious Violence Against The Person
Making off without payment
Making off without payment - Theft Act 1978 Sec 3
Making, supplying or obtaining articles for use in offence under sections 1 or 3 - Computer Misuse Act 1990 as added by Police and Justice Act 2006
Malicious Comms - Sending letters etc with intent to cause distress or anxiety
Malicious communications
Malicious Communucations
Malicious wounding: wounding or inflicting grievous bodily harm (Sec 20)
Malicious Wounding:- wounding or inflicting grievous bodily harm - Offences against the Person Act 1861 Sec 20
Malicious Wounding:- Wounding or Inflicting grievous bodily harm (without intent Section 20)
MISC CRIMES AGAINST SOCIETY
Miscellaneous Crime
Miscellaneous Crime Against Society
Miscellaneous Crimes Against Society
Most Serious Violence Against The Person
Multiple Undefined Offenders Rape of a female aged 16 or over.
Multiple Undefined Offenders Rape of a male aged 16 or over
Murder
Murder & Attempted Murder
Murder of a person under 1 year of age
Murder of persons aged 1 yr or over - Common Law
NFIB52B Hacking-Personal
"NFIB5A Cheque, Plastic card and Online Bank
Accounts (not PSP)"
NFIB90 Other Fraud Not Covered Elsewhere
Non
Non Crime
Non-fatal strangulation and suffocation
Non-fatal strangulation and suffocation - on an Emergency Worker - Serious Crime Act Sec 75 (1) (a) (5)
NON-NOTIFIABLE
NOT SPECIFIED
Obscene Publications, etc. and Protected Sexual Material
Other Criminal Damage
Other criminal damage to a building
Other criminal damage to a building - business and community ( Under £5000.00 damage )
Other criminal damage to a building - business and community (over £5000)
Other criminal damage to a building - business and community (under £5000)
Other criminal damage to a building other than a dwelling - Explosive Substance Act 1883 Sec 2,3. Criminal Damage Act 1971 Sec 1(1)
Other criminal damage to a building other than a dwelling (Under £5,000)
Other criminal damage to a business or community building £500 -£5000 - Explosive Substance Act 1883 Sec 2,3 Criminal Damage Act 1971 Sec 1(1)
Other criminal damage to a business or community building up to £500 - Explosive Substance Act 1883 Sec 2,3 Criminal Damage Act 1971 Sec 1(1)
Other criminal damage to a dwelling - Explosive Substance Act 1883 Sec 2,3. Criminal Damage Act 1971 Sec 1(1)
Other criminal damage to a dwelling (£5,000 and over)
Other criminal damage to a dwelling (Under £5,000)
Other criminal damage to a residence ( Under £5000.00 damage )
Other criminal damage to a residence (Under £5,000)
Other criminal damage to a residence (under £5000)
Other criminal damage to a vehicle - Explosive Substance Act 1883 Sec 2,3. Criminal Damage Act 1971 Sec 1(1)
Other criminal damage to a vehicle ( Under £5000.00 damage )
Other criminal damage to a vehicle (£5,000 and over)
Other criminal damage to a vehicle (over £5000)
Other criminal damage to a vehicle (Under £5,000)
Other criminal damage to a vehicle (under £5000)
Other criminal damage to a vehicle up to £5000 - Explosive Substance Act 1883 Sec 2,3 Criminal Damage Act 1971 Sec 1(1)
Other criminal damage to other residential building £500 - £5000 - Explosive Substance Act 1883 Sec 2,3 Criminal Damage Act 1971 Sec 1(1)
Other criminal damage, other - Explosive Substance Act 1883 Sec 2,3. Criminal Damage Act 1971 Sec 1(1) Malicious Damage Act 1861 Secs 35, 36, 47,48. Ancient Monuments & Archaeological Areas Act 1979 Sec 28(1). Post Office Act 1953 Sec 60 Salmon and Freshwater Fisheries Act 1975 Sec 5.
Other criminal damage, other - Explosive Substance Act 1883 Sec 2,3. Criminal Damage Act 1971 Sec 1(1). Malicious Damage Act 1861 Secs 35, 36, 47,48. Ancient Monuments & Archaeological Areas Act 1979 Sec 28(1)
Other criminal damage, other ( Over £5000.00 damage )
Other criminal damage, other ( Under £5000.00 damage )
Other criminal damage, other (£5,000 and over)
Other criminal damage, other (over £5000)
Other criminal damage, other (Under £5,000)
Other criminal damage, other (under £5000)
Other criminal offences
Other damage
Other Forgery
Other Fraud (Not covered elsewhere)
Other fraud and forgery
Other Notifiable Offences
Other Offences against the State and Public Order
Other Offences Against The State Or Public Order
OTHER OFFENCES PUBLIC ORDER
Other serious sexual
Other Serious Sexual Offences
Other Sexual Offences
Other Theft
Other Theft And Handling
Other Violence
Other violence with injury
Other wounding
Owner or person in charge allowing dog to be dangerously out of control in any place in E&W (whether or not in public place) injuring any person or assistance dog
Owner or person in charge allowing dog to be dangerously out of control in any place in England or Wales (whether or not a public place) injuring any person or assistance dog
Pass etc counterfeit coin or note as genuine
Perverting The Course Of Justice
Possess / control a false / improperly obtained / another persons identity document
Possess an offensive weapon
Possess class B controlled drug - cannabis
Possessing anything with intent to destroy or damage property
Possession Class A drug
Possession Class C drug (NOT Cannabis)
Possession Firearms Offences
Possession of a controled drug (Cannabis)
Possession of a controled drug (Excluding Cannabis)
Possession of a controlled drug with intent to supply
Possession of a controlled drug with intent to supply - Cannabis
Possession of Amphetamine (class B drug)
Possession of an indecent or pseudo indecent photo of a child
Possession of an indecent or pseudo-photograph of a child
Possession of an offensive weapon in a private place - Criminal Justice Act 1988 Sec 141 (1A)
Possession Of Article With Blade Or Point
Possession of Cannabis (class B drug)
Possession of Cocaine (class A drug)
Possession of Controlled Drugs (Cannabis)
Possession of Controlled Drugs (excl. Cannabis)
Possession of drugs
Possession of extreme pornographic image
Possession of firearm offences (Class 10B)
Possession of firearms offences (VAP BUT COUNTED UNDER OTHER OFFENCES)
Possession of Ketamine (class B drug)
Possession of offensive weapon without lawful authority or reasonable excuse
Possession of offensive weapon without lawful authority or reasonable excuse - Offensive Weapons Act 1996 Sec 2(1)
Possession of other class A drug
Possession of other class B drug
Possession of other class C drug
Possession Of Other Weapons
Possession of synthetic cannabinoid receptor agonists (class B drug)
Possession of unspecified controlled drug
Possession of weapon offences
Possession of Weapons
POSSESSION OF WEAPONS OFFENCES
Possession with intent to supply Cannabis (class B drug)
Possession with intent to supply unspecified controlled drug
Production or being concerned in production of a controlled drug - Cannabis
Profiting from or concealing knowledge of the proceeds of crime
PUBLIC DISORDER
Public Fear Alarm or Distress
Public Fear, Alarm Or Distress
Public nuisance
Public Nuisance - Common Law
Public Order
Public Order Offence
Public order offences
Pursue a course of conduct in breach of S1 (1) which amounts to stalking - Protection from Harassment Act 1997 Sec 2A (1) as inserted by Protection of Freedoms Act 2012
Pursue course of conduct in breach of Sec 1 (1) which amounts to stalking
RACE OR RELIGIOUS AGG PUBLIC FEAR
Racially or religiously aggravated assault occasioning actual bodily harm
Racially or Religiously Aggravated assault occasioning actual bodily harm - Crime & Disorder Act 1998 Sec 29(1)(b) & (2)
Racially or religiously aggravated assault or assault occasioning actual bodily harm
Racially or religiously aggravated assault or assault occasioning actual bodily harm (Sec 47)
Racially or religiously aggravated Assault with Injury
Racially or Religiously Aggravated Assault Without Injury
Racially or religiously aggravated common assault
Racially or religiously aggravated common assault or beating
Racially or Religiously aggravated common assault or beating - Crime & Disorder Act 1998 Sec 29(1)(c) & (3)
Racially or religiously aggravated criminal damage
Racially or religiously aggravated fear of violence - words or writing (Sec 4 POA )
Racially or religiously aggravated fear or provocation of violence
Racially or religiously aggravated fear or provocation of violence - Crime & Disorder Act 1998 Sec 31(1)(a) And (4) (as added to by Anti-terrorism, Crime and Disorder Act 2001 Sec 39)
Racially or Religiously Aggravated Harassment
Racially or religiously aggravated harassment or stalking without violence
Racially or religiously aggravated harassment, alarm or distress
Racially or religiously aggravated harassment, alarm or distress (Sec 5 POA)
Racially or religiously aggravated harassment, alarm or distress.
Racially or religiously aggravated intentional harassment, alarm or distress
Racially or religiously aggravated intentional harassment, alarm or distress (Sec 4a POA)
Racially or Religiously Aggravated Public Fear Alarm or Distress
"Racially Or Religiously Aggravated Public Fear, Alarm
Or Distress"
Racially or religiously aggravated public fear, alarm or distress
Racially or Religiously Aggravated Stalking or Harassment with fear of violence or serious alarm/distress- Crime & Disorder Act 1998 Sec 32(1)(b) & 4
Racially or religiously aggravated wounding or grievous bodily harm
Rape
Rape of a Female - Multiple Undefined Offenders
Rape of a Female aged 16 and over
Rape of a female aged 16 or over
Rape of a female aged 16 or over - Sexual Offences Act 2003 Sec 1
Rape of a Female aged 16 or over (Sec 1 SOA 2003)
Rape of a female aged under 16
Rape of a Female aged under 16 (Sec 1 SOA 2003)
Rape of a female child under 13 by a male
Rape of a female child under 13 by a male (S5 SOA 2003)
Rape of a Female Child under 16
Rape of a Male aged 16 and over
Rape of a male aged 16 or over
Rape of a male aged 16 or over - multiple undefined offenders - Sexual Offences Act 2003 Sec 1
Rape of a male aged 16 or over - Sexual Offences Act 2003 Sec 1
Rape of a Male aged 16 or over (Sec 1 - SOA 2003)
Rape of a male aged under 16 - Sexual Offences Act 2003 Sec 1
Rape of a male child under 13 by a male
Rape of a male child under 13 by a male (S5 SOA 2003)
Residential burglary
Residential Burglary of a Home
Residential Burglary of a Home - Theft Act 1968 Sec 9
Residential Burglary of a Home (Dwelling)
Residential Burglary-Unconnected Build
Robbery
Robbery - personal
Robbery - personal - Theft Act 1968 Sec 8
Robbery (of Personal Property)
Robbery business
Robbery of business property
Robbery of personal property
Robbery Personal
Sec 18. Wounding with intent to do grievous bodily harm
Sec 20. Malicious wounding or inflicting grievous bodily harm (without intent)
Sec 47. Assault occasioning actual bodily harm
Send false communication with intent to cause harm
Send indecent or grossly offensive communication to cause distress or anxiety Sec 1A Malicious Communications Act 1988
Sending letters etc with intent to cause distress or anxiety
Sending letters etc with intent to cause distress or anxiety - Malicious Communications Act 1988 Sec 1
Sending letters etc. with intent to cause distress or anxiety
Serious wounding
Sexual activity  etc with a person with a mental disorder (Class 70)
Sexual activity etc with a person with a mental disorder
Sexual activity with a person with a mental disorder impeding choice: Female person
Sexual activity with a person with a mental disorder impeding choice: Male person
Sexual assault
Sexual assault of a female 13 or over by penetration (S2, SOA 2003)
Sexual assault of a female child under 13
Sexual assault on a female
Sexual assault on a female 13 and over - Sexual Offences Act 2003 Sec 3
Sexual Assault on a female aged 13 and over
Sexual assault on a female aged 13 or over
Sexual assault on a female aged 13 or over (S3, SOA 2003)
Sexual assault on a male
Sexual assault on a male 13 and over - Sexual Offences Act 2003 Sec 3
Sexual Assault on a male aged 13 and over
Sexual assault on a male aged 13 or over
Sexual assault on a male aged 13 or over (Sec 3, SOA 2003)
Sexual assault on a male child under 13
Sexual assault on a male child under 13 - Sexual Offences Act 2003 Sec 7
Sexual Assault on Female aged 13 and over
Sexual Assualt on a Male aged 13 and over
Sexual Grooming
Sexual Offence
SEXUAL OFFENCES
Sexual penetration of a corpse
Shoplifting
Single Offender: Attempted rape of a female aged 16 or over
Single Offender: Rape of a female aged 16 or over
Single Offender: Rape of a female aged under 16
Single Offender: Rape of a male aged 16 or over
Stalking
Stalking and Harassment
Stalking involving fear of violence
Stalking involving fear of violence - Protection from Harassment Act 1997 Sec 4A (1) (a) (b) (i) as inserted by Protection of Freedom Act 2012 Sec 111
Stalking involving serious alarm/distress - Protection from Harassment Act 1997 Sec 4 (1) (a) (b) (ii) as inserted by Protection of Freedoms Act 2012
Stalking without fear/alarm/distress
Supply etc of articles for administering or preparing controlled drugs
Supplying or offering to supply a controlled drug
Supplying or offering to supply or being concerned in the supply of a controlled drug - Cannabis
Take / make / distribute indecent photographs or pseudo- photographs, of children Obscene Publications
Take or to make or to distribute indecent photographs or pseudo
Take/ make / distribute indecent photographs or pseudo- photographs, of children - Protection of Children Act 1978 Secs 1 & 6 as amended by the Criminal Justice & Public Order Act 1994 Sec 84
Taken Without Owners Consent
Tampering with motor vehicles
THEFT
Theft Bike
Theft by an employee
Theft by an employee.
Theft by employee
Theft from a motor vehicle
Theft from a motor vehicle - Theft Act 1968 Sec 1
Theft from a motor vehicle.
Theft from a Vehicle
Theft from a vehicle - other than a motor vehicle
Theft from automatic machine or meter
Theft from automatic machine or meter - Theft Act 1968 Sec 1
Theft from automatic machine or meter.
THEFT FROM MOTOR VEHICLE
Theft From MV
Theft from shops and stalls
Theft from shops and stalls (Shoplifting)
Theft from shops and stalls.
Theft from the person
Theft from the person of another
Theft from the person of another - Theft Act 1968 Sec 1
Theft from the person of another.
Theft From Vehicle
Theft if not classified elsewhere
Theft if not classified elsewhere - Theft Act 1968 Sec 1
Theft in a dwelling
Theft in a Dwelling other than from an Automatic Machine or Meter
Theft in a dwelling other than from automatic machine or meter
Theft in dwelling other than auto machine or meter
Theft Making Off Without Payment
Theft of a motor vehicle
Theft of a motor vehicle - Theft Act 1968 Sec 1
Theft of a pedal cycle
Theft of a pedal cycle - Theft Act 1968 Sec 1
Theft of a pedal cycle.
Theft of conveyance other than a motor or pedal cycle
Theft of conveyance other than motor vehicle or pedal cycle - Theft Act 1968 Sec 1
THEFT OF MOTOR VEHICLE
Theft Offence
THEFT OFFENCES
THEFT OR UNAUTH TAKING OF A MOTOR VEH
Theft or unauthorised taking of a cycle
Theft or unauthorised taking of a motor vehicle
Theft or Unauthorised Taking of a Pedal Cycle
Theft or unauthorised taking of a vehicle
Theft or unauthorised taking of motor vehicle
Theft or unlawful taking of pedal cycle
Theft other
Theft Person
Theft Shop
Theft: All Other Theft
Theft: Bicycle Theft
Theft: Shoplifting
Theft: Theft From Person
Theft/UTMV
"Threat Or Possession With Intent To Commit
Criminal Damage"
Threat or Possession with Intent to Commit Criminal Damage
Threat or possession with intent to commit criminal damage/to destroy own property
Threaten a person with a bladed/pointed article in a private place - Offensive Weapons Act 2019 Sec 52 (1) & (6)
Threaten a person with an article in a private place
Threaten a person with an offensive weapon / substance in a private place; Offensive Weapons Act 2019 Sec 52 (1) & (6)
Threaten with a blade or sharply pointed article in a public place
Threaten with a blade or sharply pointed article in a public place - Criminal Justice Act 1988 Sec 139 AA as inserted by Legal Aid, Sentencing and Punishment of Offenders Act 2012
Threaten with an offensive weapon in a public place
Threaten with an offensive weapon in a public place - Prevention of Crime Act 1953 Sec 1 (A) (1) as inserted by Legal Aid, Sentencing and Punishment of Offenders Act 2012
Threats to destroy or damage property
Threats to destroy or damage property - Criminal Damage Act 1971 Sec 2 (note MOPI 2 if endanger life)
Threats to kill
Threats to Kill - Offences against the Person Act 1861 Sec 16
Trafficking In Controlled Drugs
Trafficking of drugs
Unauthorised taking of a motor vehicle (does not include 'driving or being carried knowing motor vehicle has been taken ')
Unauthorised taking of conveyance other than a motor vehicle or pedal cycle (does not include being found with a conveyance that has already been stolen)
Unlawful importation of a drug controlled under the Misuse of Drugs Act 1971
Vehicle Interference
Vehicle Interference and Tampering
Vehicle interference/tampering - motor vehicle
Vehicle offences
Violence Against the Person
Violence With Injury
Violence Without Injury
Voyeurism
Wounding with intent or causing grievous bodily harm with intent (Sec 18)
Wounding with intent to do GBH Section 18
Wounding with intent to do grievous bodily harm - Offences against the Person Act 1861 Sec 18

### Assistant

Done. I created the new lookup here:

[29forces_category_lookup.csv](/Users/paul/Documents/Codex/classifying%20crimes%20in%20FOIs/29forces_category_lookup.csv)

It has the same structure as before, with `Category in 29forces data` as the source category column. Final count: 700 rows, with 372 high-confidence matches, 233 medium, and 95 low.

I also saved the reproducible script here:

[build_29forces_category_lookup.py](/Users/paul/Documents/Codex/classifying%20crimes%20in%20FOIs/build_29forces_category_lookup.py)
