# UK Seafood Report

## Summary 
Use Power BI to explore UK seafood data retrieved from [UK Data Service](https://reshare.ukdataservice.ac.uk/856955/). The data includes information about what's being produced, imported, exported and consumed. 

### Data Notes
- Fisheries means fishing from the natural environment and aquaculture is harvesting/farming fish.
- Consumer demand is higher for the "big five": cod, haddock, tuna, salmon and prawns.
- SACN stands for Scientific Advisory Committee on Nutrition. "Other" category includes ready meals and fish such as octopus which are molluscs in the ISSCAAP list.
- ISSCAAP stands for International Standard Statistical Classification of Aquatic Animals and Plants. They have a "Remove" category which might need to be removed depending on how we use SeafoodMatchingList.csv. Diadromous fish migrate between salt water and fresh water. Marine fish live in salt water. Products are ready meals containing fish.
- ISSCAAP Other, Remove and Product categories had NA as code. I changed this to null.
- The supply chain dataset includes data on imports, exports, production, consumption and purchases.
- I hid the Units and TemporalResolution columns because they always have the same value and won't be useful for visuals.
- Note the two conversions in the supply chain dataset. V1 says conversion factors were applied to all or part of the import/export data (the edible portion of fish). V2 says grams per capita per week was converted to grams per country per year (purchases/consumption data).
- The supply composition dataset adds up the values in the supply chain dataset but not for all fish species.
  - To double check that the amounts added up correctly I grouped the composition dataset by commodity, species and year then used commodity as a pivot column to widen the table. There was one value which didn't match due to more significant figures in one dataset (the salmon production values for 2019). 
- Supply chain notes:
  - For imports/exports/consumption/purchases, per species per year there's only one value (except for "Fish dish" and "Other marine fish" + "Other molluscs" in purchases)
  - For production, per species per year there can be more than one value. Often there are two values, one for fisheries and one for aquaculture. However, there can be more e.g. scallops in 2017 have two values for aquaculture and one for fisheries. 

### Things to Investigate
- Is the consumption csv the total of the individual lines in the supply chain csv?
- What is the CF value in SeafoodMatchingList.csv?
- Does the data mention where products are being imported from?
- Is there any data on fish stocks / sustainable fishing?
- Is there any data on carbon emissions of producing and trading fish?
- How much of our consumption is of the big five fish? Has this changed over time?
- How much of our consumption is of fish products (i.e. ready meals) rather than whole fish? Double check Product in ISSCAAP does mean this...
- How much of the big five are we importing? What other fish are we exporting and in what quantities?
- Where are the biggest differences between what we produce and consume?
- Is there a difference between the fish being consumed within / outside the home?
- Would it be possible to compare this data with another country?
