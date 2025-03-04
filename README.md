# UK Seafood Report

## Summary 
Use Power BI to explore UK seafood data retrieved from [UK Data Service](https://reshare.ukdataservice.ac.uk/856955/). The data includes information about what's being produced, imported, exported, purchased, and consumed. 

## Steps taken
- Import csv files into Power BI.
- Use Power Query Editor to explore and transform data. See [detailed steps](#data-transformation).
- Check the relationships in the Model view.
- Initial exploratory data analysis to understand the data and identify points to investigate. 

### Things to Investigate
- Look at totals of all fields
  - Over all years: imports 8tn, production fisheries 3.5tn, production aquaculture 1tn, home purchases 6tn, exports 5.5tn, consumption 5tn,  purchases out 0.5tn. Producing 4.5tn vs exporting 5.5tn, suggests we're exporting most of what we're producing. Could also be value add on imports. 
- Look at how each field has changed over time
  - Imports from 765bn to 715bn (sharp decrease between 2013-15 and then again 2016-18)
  - Aquaculture production from 90bn to 105bn (slow increase with ups and downs)
  - Fisheries production from 270bn to 265bn but big spike 335bn in 2014, upside down curve
  - Home purchases no clear trend 510bn to 490bn
  - Out purchases slow but steady increase from 44bn to 57bn
  - Exports from 470bn to 420bn (ups and downs, could be related to production/weather)
  - Consumption from 380bn to 480bn (sharp increase between 2019 and 2020, slow increase rest of time)
  - Look at percentages for this but initially looks like imports are decreasing as consumption is increasing
- How many species do we have data for? How many species do we have all fields for?
  - 73 unique species. Only "Other marine fish" has data for all fields
  - If we look for one non-null production value and one non-null consumption value we only get 8 species in all years that don't have this data: brill, char, gurnard, lemon sole, pilchard, sand eel, whelk and witch. So 65 species have production/consumption data for at least one year. 
- Has species data changed over time? Could the Brexit deal be affecting some of this?
  - Aquaculture of shellfish/molluscs has halved between 2009 and 2020. A lot of this is due to mussel production. 
  - Consumption of fish products more than doubled between 2019 and 2020 (home/out purchases didn't show the same trend)
- For consumption / purchases how many grams per person? Does the NHS make recommendations about quantities of fish?
- How much of our consumption is of the big five fish? Has this changed over time?
- How much of our consumption is of fish products rather than whole fish? Note: ISSCAAP Product includes ready meals, caviar, dips, pastes, fishcakes and fish in sauce. 
- How much of the big five are we importing? What other fish are we exporting and in what quantities?
- Where are the biggest differences between what we produce and consume?
  - Overall, we're purchasing more than we produce (the same doesn't apply for consumption)
  - We're purchasing/consuming more lean fish than we're producing
  - Big discrepancies between consumption/production values for different fish species. Need to investigate this and why it might be happening. One notable is lack of purchase date for shellfish/crustaceans. 
- Is there a difference between the fish being consumed within / outside the home?
- Does the data mention where products are being imported from?
- Is there any data on fish stocks / sustainable fishing?
- Is there any data on carbon emissions of producing and trading fish?
- Would it be possible to compare this data with another country?
 
### Data Transformation
- ISSCAAP Other, Remove and Product categories had NA as code. I changed this to null.
- Removed Units and TemporalResolution columns from SupplyChain because they always have the same value. Renamed Volume as VolumeGrams.
- For numerical columns changed NA to null and changed the column type to whole number or decimal as appropriate. 
- Checked that all year/population combinations from SupplyComposition were the same. Created a UKPopulation table with year and population and removed population from SupplyComposition. 
- Checked that all species/nutritional information columns were the same. Created a FishNutrition table which lists nutritional information e.g. protein per 100g and removed these columns from SupplyComposition. 
- Created a FishTypes table which lists the fish categories as designated by the SACN and ISSCAAP. Removed these columns from SupplyComposition.
- The remaining columns in SupplyComposition added up the values in SupplyChain but not for all fish species. To double check that the amounts added up correctly I grouped a duplicate of SupplyComposition by commodity, species and year then used commodity as a pivot column to widen the table. There was only one value which didn't match due to more significant figures in one dataset (the salmon production values for 2019). 
- Deleted SupplyComposition because it no longer contained any different information from SupplyChain.
- In SeafoodMatchingList the only remaining useful information was the CF value. Distinct data supplier, species and CF values were kept. Some of the species had trailing spaces so these were removed using the Trim option. There was one supplier/species combination (HMRC/Other marine fish) which had 4 CF values so the supplier/species were grouped and the average CF value calculated. Supplier was used as a pivot column to get one row per species and the different CF values by supplier (removing any suppliers which weren't in SupplyChain). 
- A conditional column, DataSupplierDetail, was created to split out DEFRA into DEFRA_eatenOut and DEFRA_household. 
- Created a Conditional Column for Commodity Detail. This was to split Production into ProductionFisheries and ProductionAquaculture and Purchases into PurchasesHome and PurchasesOut. 
- Created a DataSources table which lists the dataset information. Removed these columns from SupplyChain.
- In SupplyChain used CommodityDetail as a pivot column to widen the table i.e. list species and year then the amount in grams of exports, imports, production fisheries, production aquaculture, consumption, purchases out and purchases home. This meant losing the connection to DataSources but because each type of data comes from one data source this isn't too important. As a reminder:
   - HMRC provide imports and exports data
   - MMO provide production data for fisheries
   - CEFAS provide production data for aquaculture
   - NDNS provide consumption data
   - DEFRA provide purchases data (both home and eating out). They only provide data on:
     - Fish dish (product / lean)
     - Herring (marine / oily)
     - Other marine fish (marine / lean and oily)
     - Other molluscs (molluscs / shellfish)
     - Salmon (diadromous / oily)

### Data Notes
- Fisheries means fishing from the natural environment and aquaculture is harvesting/farming fish.
- Consumer demand is higher for the "big five": cod, haddock, tuna, salmon and prawns.
- SACN stands for Scientific Advisory Committee on Nutrition. "Other" category includes ready meals and fish such as octopus which are molluscs in the ISSCAAP list.
- ISSCAAP stands for International Standard Statistical Classification of Aquatic Animals and Plants. Diadromous fish migrate between salt water and fresh water. Marine fish live in salt water. Products are ready meals containing fish.
- The supply chain dataset includes data on imports, exports, production, consumption and purchases.
- Note the two conversions in the supply chain dataset. V1 says conversion factors were applied to all or part of the import/export data (the edible portion of fish). V2 says grams per capita per week were converted to grams per country per year (purchases/consumption data). The CF value (conversion factor) was found in SeafoodMatchingList. 
