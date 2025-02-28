# UK Seafood Report

## Summary 
Use Power BI to explore UK seafood data retrieved from [UK Data Service](https://reshare.ukdataservice.ac.uk/856955/). The data includes information about what's being produced, imported, exported and consumed. 

### Data Notes
- Fisheries means fishing from the natural environment and aquaculture is harvesting/farming fish.
- Consumer demand is higher for the "big five": cod, haddock, tuna, salmon and prawns.
- SACN stands for Scientific Advisory Committee on Nutrition. "Other" category includes ready meals and fish such as octopus which are molluscs in the ISSCAAP list.
- ISSCAAP stands for International Standard Statistical Classification of Aquatic Animals and Plants. Diadromous fish migrate between salt water and fresh water. Marine fish live in salt water. Products are ready meals containing fish.
- The supply chain dataset includes data on imports, exports, production, consumption and purchases.
- Note the two conversions in the supply chain dataset. V1 says conversion factors were applied to all or part of the import/export data (the edible portion of fish). V2 says grams per capita per week were converted to grams per country per year (purchases/consumption data). The CF value (conversion factor) was found in SeafoodMatchingList. 
- Supply chain notes:
  - For imports/exports/consumption/purchases, per species per year there's only one value (except for "Fish dish" and "Other marine fish" + "Other molluscs" in purchases)
  - For production, per species per year there can be more than one value. Often there are two values, one for fisheries and one for aquaculture. However, there can be more e.g. scallops in 2017 have two values for aquaculture and one for fisheries.
 
### Data Transformation
- ISSCAAP Other, Remove and Product categories had NA as code. I changed this to null.
- Removed Units and TemporalResolution columns from SupplyChain because they always have the same value. Renamed Volume as VolumeGrams.
- For numerical columns changed NA to null and changed the column type to whole number or decimal as appropriate. 
- Checked that all year/population combinations from SupplyComposition were the same. Created a UKPopulation table with year and population and removed population from SupplyComposition. 
- Checked that all species/nutritional information columns were the same. Created a FishNutrition table which lists nutritional information e.g. protein per 100g and removed these columns from SupplyComposition. 
- Created a FishTypes table which lists the fish categories as designated by the SACN and ISSCAAP. Removed these columns from SupplyComposition.
- The remaining columns in SupplyComposition added up the values in SupplyChain but not for all fish species. To double check that the amounts added up correctly I grouped a duplicate of SupplyComposition by commodity, species and year then used commodity as a pivot column to widen the table. There was only one value which didn't match due to more significant figures in one dataset (the salmon production values for 2019). 
- Deleted SupplyComposition because it no longer contained any different information SupplyChain.
- In the SeafoodMatchingList the only remaining useful information was the CF value. Distinct data supplier, species and CF values were kept. Some of the species had trailing spaces so these were removed using the Trim option. There was one supplier/species combination (HMRC/Other marine fish) which had 4 CF values so the supplier/species were grouped and the average CF value calculated.
- Before merging the SeafoodMatchingList and SupplyChain a conditional column, DataSupplierDetail, was created to split out DEFRA into DEFRA_eatenOut and DEFRA_household. 
- The SupplyChain dataset was merged with the Seafood Matching List to retrieve the CF value. There were some nulls for MMO so I doubled checked these supplier/species combinations didn't exist in the Seafood Matching List. 
- Created a Conditional Column for Commodity Detail. This was to split Production into ProductionFisheries and ProductionAquaculture and Purchases into PurchasesHome and PurchasesOut. 
- Created a DataSources table which lists the dataset information. Removed these columns from SupplyChain. 

### Things to Investigate
- Does the data mention where products are being imported from?
- Is there any data on fish stocks / sustainable fishing?
- Is there any data on carbon emissions of producing and trading fish?
- How much of our consumption is of the big five fish? Has this changed over time?
- How much of our consumption is of fish products (i.e. ready meals) rather than whole fish? Double check Product in ISSCAAP does mean this...
- How much of the big five are we importing? What other fish are we exporting and in what quantities?
- Where are the biggest differences between what we produce and consume?
- Is there a difference between the fish being consumed within / outside the home?
- Would it be possible to compare this data with another country?
