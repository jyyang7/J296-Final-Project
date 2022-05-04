# Data Analysis
## Dataset:
### Domestic violence related homicides
1. Download homicide-actuals data from [https://openjustice.doj.ca.gov/data](https://openjustice.doj.ca.gov/data) (84365 rows) 
2. Import to google sheets [https://docs.google.com/spreadsheets/d/16IzkOG\_wi1r0BWCIKgYMjOMdH9QEJOcNTKF-Bct93So/edit?usp=sharing](https://docs.google.com/spreadsheets/d/16IzkOG_wi1r0BWCIKgYMjOMdH9QEJOcNTKF-Bct93So/edit?usp=sharing) (Name: Homicide _Actuals_1987-2020)
3. Make a copy
### Domestic violence related calls for service
1. Download Domestic violence related calls for service data from [https://openjustice.doj.ca.gov/data](https://openjustice.doj.ca.gov/data) (165878 rows)
2. Import to google sheets [https://docs.google.com/spreadsheets/d/1OxZJbG1EetmLxCoSk3Ap5JHXOpQNrvL7ImAmLvNS8UE/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1OxZJbG1EetmLxCoSk3Ap5JHXOpQNrvL7ImAmLvNS8UE/edit?usp=sharing)
3. Make a copy

## Data Cleaning and Filtering
### homicide data set
1. Import “Homicide _Actuals_1987-2020” to OpenRefine (84365 rows)
2. Trim all whitespace
3. Transform “Total Victim” and “Total Suspect” to numbers
4. Create text facet on the “county” column
	1. Change the county code to 2-digit (eg. 1 to 01)
	2. Eventually we come down to 58 counties
5. Create text facet on the “Jurisdiction (NCIC)” column
	1. Change the NCIC code to 4-digit (eg. 900-0900) by using Edit cells - Transform - Expression: `"0000"[0,4-length(value)] + value`
6. Create text facet on the columns, according to the [data dictionary](https://data-openjustice.doj.ca.gov/sites/default/files/dataset/2021-06/Homicide%20Context_062921.pdf):
	1. Change “Age” to 2-digit 
	2. Change “Victim/Offender Relationship #1” to 2-digit
	3. Change “Victim/Offender Relationship #2” to 2-digit
	4. Change “Victim/Offender Relationship #3” to 2-digit
	5. Change “Victim/Offender Relationship #4” to 2-digit
	6. Change “Means of death” to 2-digit
	7. Change “Location” to 2-digit
	8. Change “Precipitating Event” to 2-digit
	9. Change “Special Circumstances” to 3-digit
7. Create a column named “ID” to combine NCIC code and BCS number to create a unique case number (because some cases in different counties can have the same BCS number, but the NCIC code would be different.) 
	1. Use formula: =CONCAT(C2,D2) 
	2. Cope the column and paste values only
8. Create filter to narrow the data set down to homicides related to intimate partner violence between 2001-2020. I include cases where: 
	1. Precipitating Event = 62, AND:
	2. Year of Report = 2001-2020
9. Export the csv. file and Import to google sheets. Name it [CLEAN_Homicide-Actuals-2001-2020-csv ](https://docs.google.com/spreadsheets/d/1HQQCQ9QQ6IHLuy5SfWrFYghetdZuaP7LoQbt41HJpIE/edit?usp=sharing)(2583 rows)
### DV calls data set
1. In the “COUNTY” column, everything is written as XX County, but I just want the name of the county. So I split the column with the separator “County”. Now I have only the county names. 
2. Import the data set into OpenRefine, it looks clear. 
3. Export the csv. file and import to google sheets. Name it [CLEAN_DVcalls2001-2020 ](https://docs.google.com/spreadsheets/d/1OxZJbG1EetmLxCoSk3Ap5JHXOpQNrvL7ImAmLvNS8UE/edit?usp=sharing)

## Change code to words
Since the Homicide data set used a lot of codes to represent information, I want to use VLOOKUP and the [data dictionary](https://data-openjustice.doj.ca.gov/sites/default/files/dataset/2021-06/Homicide%20Context_062921.pdf) provided to transfer the code to its meaning. 
For example, in the “County” column, we can see county code instead of the name of the counties. 
### Steps: 
1. Create a new column named “County”, name the old column “Countycode”
2. Find the county code dictionary and save it as a sheet in the data set. 
3. Use VLOOKUP formula: =VLOOKUP(A2,'County Code'!A:B,2,false)
4. Copy the new column and paste values only.
### Do the same to these columns: 
- Gender
- Race/Ethnicity
	- I created another column called “W_Race/Ethnicity”_ to document the wider categories of race & ethnicity (eg. Combine Chinese, Vietnamese, Japanese into Asian)
- Victim/Offender Relationship #1
- Victim/Offender Relationship #2
- Victim/Offender Relationship #3
- Victim/Offender Relationship #4
- Means of death
- Location
- Precipitating Event
- Special Circumstance

We can see the information (instead of the codes) now. 
![](screenshot_decode.png)

## Data questions
**Question 1: How many domestic violence related homicide cases happen in California every year from 2001 to 2020? What’s the gender distribution of victims?**
1. On the [CLEAN_Homicide-Actuals-2001-2020-csv ](https://docs.google.com/spreadsheets/d/1HQQCQ9QQ6IHLuy5SfWrFYghetdZuaP7LoQbt41HJpIE/edit?usp=sharing) data set, create a pivot table.
2. Set the rows to “Year of Report” and values “ID”. The values should be summarized by COUNTUNIQUE. It looks like this: 
![](screenshot_Q1_years.png)
3. Set the rows to “Year of Report”, set the columns to “Gender” and set values to “ID”. The values should be summarized by COUNTA. Show values as % of row. It looks like this:
![](screenshot_Q1_victimgender.png)
![](screenshot_Q1_victimgender_pct.png)
4. Finding: There are 2527 homicide cases related to domestic violence. 2583 victims are involved. Women are disproportionately the majority of victims. 

**Question 2: What’s the racial distribution of the victims?**
1. On the [CLEAN_Homicide-Actuals-2001-2020-csv ](https://docs.google.com/spreadsheets/d/1HQQCQ9QQ6IHLuy5SfWrFYghetdZuaP7LoQbt41HJpIE/edit?usp=sharing) data set, create a pivot table.
2. Set the rows to “W_Race/Ethnicity”_ and values “ID”. The values should be summarized by COUNTA. Show as % of column. Sort rows by COUNTA of ID in descending order. It looks like this: 
![](screenshot_race.png)
3. Finding: More than 33% of domestic violence related homicide victims are Hispanics/Latinos. 17% are black. Nearly 9% are Asian. 

**Question 3: What are the locations where victims are mostly likely to be assaulted?**
1. On the [CLEAN_Homicide-Actuals-2001-2020-csv ](https://docs.google.com/spreadsheets/d/1HQQCQ9QQ6IHLuy5SfWrFYghetdZuaP7LoQbt41HJpIE/edit?usp=sharing) data set, create a pivot table.
2. Set the rows to “Location” and values “ID”. The values should be summarized by COUNTA. Show as % of column. Sort rows by COUNTA of ID in descending order. It looks like this: 
![](screenshot_Q3.png)
3. Finding: For a woman, the most dangerous place is not out in the wild, but her own home, where she is supposed to feel the safest. More than 70% domestic violence related homicide happen in the victim’s own residence or her shared residence with someone else, likely the offender. 

**Question 4: How many domestic violence calls for service are received by each county in first decade and the second one between 2001-2020? Combine census population data, count the number of calls per 100,000 people.**
1. On the [CLEAN_DVcalls2001-2020 ](https://docs.google.com/spreadsheets/d/1OxZJbG1EetmLxCoSk3Ap5JHXOpQNrvL7ImAmLvNS8UE/edit?usp=sharing)data set, create a pivot table.
2. Set the rows to “COUNTY” and values “TOTAL_CALLS”. The values should be summarized by SUM. Sort rows by SUM of TOTAL_CALLS in descending order. Filter YEAR to 2001-2010, 2011-2020 respectively. It looks like this: 
![](screenshot_Q4_sum.png)
3. Copy and paste the pivot table into another sheet, name it “decade change.” It looks like this: 
![](screenshot_Q4_sum_2.png)
4. Use VLOOKUP to combine the two tables into one. `=VLOOKUP(A2,F:G,2,false)` Cope and paste values only. 
![](screenshot_Q4_total.png)
5. Download [2010](http://census.ire.org/data/bulkdata.html?state=06&sumlev=050) and [2020](https://censusreporter.org/data/table/?table=B01001&geo_ids=04000US06,050%7C04000US06&primary_geo_id=04000US06#valueType%7Cestimate) census data on population in California counties. Paste them in a new sheet named “population.”
![](screenshot_Q4_population.png)
6. Use VLOOKUP to combine the population sheet together with the decade change sheet. Copy and paste values only.`=VLOOKUP(A2,population!A:B,2,false)`
![](screenshot_Q4_popmerged.png)
7. Calculate the number of calls per 100,000 people. `=B2/C2*100000` Copy and paste values only. Now we have a number we can compare. 
![](screenshot_Q4_per.png)
8. Finding: There’s a huge spike of calls per 100,000 people in Del Norte county. While most counties are receiving fewer domestic violence calls, Kern, Lake, Siskiyou, Lassen, Inyo, Mariposa and Modoc county are seeing increase in domestic violence related calls. 
![This is a draft visualization. See the final version below in the data visualization section. ](Number%20of%20domestic%20violence%20related%20calls%20for%20service%20per%20100,000%20people%20in%20California%20counties%20between%202001-2010%20and%202011-2020.png)
[![](Map_ruralcounties.png)](https://www.datawrapper.de/_/NbYDL/)

**Question 5: What is the average age of male and female victims?**
1. On the [CLEAN_Homicide-Actuals-2001-2020-csv ](https://docs.google.com/spreadsheets/d/1HQQCQ9QQ6IHLuy5SfWrFYghetdZuaP7LoQbt41HJpIE/edit?usp=sharing)data set, create a pivot table.
2. Set the rows to “Age” and values “ID”. The values should be summarized by COUNTA. Filter Gender to male and female respectively. Create filter to exclude Age = 00 and Age = BB. It looks like this: 
![](Q5gender-age.png)
3. Copy and paste values only to another sheet named “age.”
4. Calculate the weighted average of both female and male age. `=SUMPRODUCT(B2:B82,C2:C82)/sum(C2:C82)`
![](Q5%20average%20age.png)
5. Finding: the average age of female victims is slightly younger than male victims, with 40 years old for female, 42 for male.  

## Story Idea and Sourcing
### Human Sources: 
1. Alexis Bagon, staff attorney at Asian and Pacific Islander Legal Outreach
	- Email: [abagon@apilegaloutreach.org](abagon@apilegaloutreach.org)
	- She works with domestic violence survivors to apply for their green card through the Violence Against Women Act (VAWA) or helps them get a U Visa, which is a visa based on being the victim of a crime. Talking to her has provided me with a lot of context regarding domestic violence. 
2. Jasmeen Kairam, Capacity Building Coordinator at California Partnership to End Domestic Violence
	- Email: [jasmeen@cpedv.org](jasmeen@cpedv.org)
	- Kairam works at California Partnership to End Domestic Violence, which represents over 1,000 survivors, advocates, organizations and individuals across the state. Kairam has experience working with many other domestic violence organizations in the state, which could also help me gain a better sense of the scope of the problem. 
3. Michelle, domestic violence survivor
	- Phone number: +1 (626) 557-7765
	- Michelle lives in Los Angeles with her kid. She escaped her home after having the fight with her ex-husband. Her ex-husband didn’t beat her, but there was a lot of emotional abuse. She lives in a shelter now and is about to move out. I’m really grateful that she’s willing to talk to me about such a private experience.  
### Additional Sources
1. [15th Annual Domestic Violence Counts Report](https://nnedv.org/wp-content/uploads/2021/05/15th-Annual-DV-Counts-Report-California-Summary.pdf) from National Network to End Domestic Violence (2021)
2. Law Enforcement Response to Domestic Violence Calls for Service [report](https://www.ojp.gov/pdffiles1/nij/grants/215915.pdf) funded by the U.S. Department of Justice

## Data Visualizations
1. This is an area chart showing the average number of domestic violence related calls per 100,000 people in California counties during the first and second decade of the 21st century. 
[![](Q4counties-calls.png)](https://www.datawrapper.de/_/83EsV/)
2. This a stacked area chart showing the gender distribution of domestic violence related homicide victims. 
[![](Areachart_gender.png)](https://www.datawrapper.de/_/ayrvU/)

