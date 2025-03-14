# UK Seafood Report

## Summary 
Use Power BI to explore UK seafood data retrieved from [UK Data Service](https://reshare.ukdataservice.ac.uk/856955/). The data includes information about what's being produced, imported, exported, purchased, and consumed. 

Supplementary data for the EU was retrieved from [EUMOFA](https://eumofa.eu/data) (European Market Observatory for Fisheries and Aquaculture Products) and [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/fish_ld_main/default/table?lang=en&category=fish.fish_ld). EU Population figures were retrieved from the [World Bank.](https://data.worldbank.org/indicator/SP.POP.TOTL) 

## Steps taken
- Import csv files into Power BI.
- Use Power Query Editor to explore and transform data. See [detailed steps](#data-transformation).
- Check the relationships in the Model view.
- Initial exploratory data analysis to understand the data and identify points to investigate.
- Review findings to extract key themes.
- Research sources of EU data for comparison purposes. Import these into Power BI and transform as needed.
- Create calculated columns, measures and visualizations to present the key findings. 

## Key Themes
### [1] Two thirds of our fish consumption is of the "big five" fish
- The big five fish are salmon, tuna, cod, prawn, and haddock. 
- Between 2009 and 2020 we've consumed 4.7 trillion grams of fish, 3.1 trillion grams of the big five fish (~66%) and 1.6 trillion grams of all other fish. The percentage hasn't changed much over the years. 
- Salmon consumption alone is almost 1 trillion grams.
- Production is the other way round. We produce roughly 1/3 of the big five fish and 2/3 of other fish.
- Three of the five most produced fish are underconsumed - mackerel, herring and crab.

  ![image](https://github.com/user-attachments/assets/f69e8317-e553-4a68-817a-c46b349d90a2)

### [2] Production has remained relatively stable with a few notable exceptions
- Aquaculture production has increased from 90bn to 105bn between 2009 and 2020 with some sharp ups and downs. Most of the increase can be explained by an increase in salmon production.
- Aquaculture production of shellfish/molluscs has halved between 2009 and 2020. This is due to a decrease in mussel production. 
- Fisheries production has remained relatively steady over the same period although there was a big spike in 2014. We produce more mackerel and herring than any other fish and this accounts for the spikes observed.

![image](https://github.com/user-attachments/assets/bccefcee-aa60-44ed-b055-efd241b14140)

### [3] The gap between production and consumption is being filled by imports
- We consume roughly double the amount of lean fish that we produce and only consume about 60% of the shellfish we produce.
- 80% of lean fish imports/production consists of imports.
- Between 2009 and 2020 there has been a slow decrease in the consumption of marine fish and a slow increase in the consumption of diadromous fish. This can be explained by an increase in salmon consumption and reductions in cod and haddock consumption. 
- Consumption of fish dish products spiked in 2020.

![image](https://github.com/user-attachments/assets/f41dbe9f-3d01-40e3-bb5c-822d7c905651)

### [4] UK fish consumption is much lower than the NHS recommend
- In 2020 the UK consumed 476bn grams of fish, roughly half what the NHS recommends (977bn). Note: the 977bn is based on population and is slightly inflated because children under 2 have different dietary requirements.
- Consumption remained steady between 2009 and 2019 with a slight improvement in 2020.
- In 2020 the average person consumed 136g of all fish a week and 64g of oily fish. This compares to recommendations of 280g and 140g respectively. The buttons on the chart switch the view between all fish and oily fish. 
- Despite the UK's poor fish consumption it still performs better than most EU countries (except Spain which consumes roughly double the amount of fish per person per week). Note: the EU graph only includes a few countries for easier comparison, countries which overlapped have been removed.

![image](https://github.com/user-attachments/assets/a461859e-cf44-44ca-ad6a-61f8c409d1e5)

### Data Transformation
- ISSCAAP Other, Remove and Product categories had NA as code. I changed this to null.
- Removed Units and TemporalResolution columns from SupplyChain because they always have the same value. Renamed Volume as VolumeGrams.
- For numerical columns changed NA to null and changed the column type to whole number or decimal as appropriate. 
- Checked that all year/population combinations from SupplyComposition were the same. Created a UKPopulation table with year and population and removed population from SupplyComposition. 
- Checked that all species/nutritional information columns were the same. Created a FishNutrition table which lists nutritional information e.g. protein per 100g and removed these columns from SupplyComposition.
- From SeafoodMatchingList I created a FishTypesCFValues table to list fish species, their categories as designated by the SACN and ISSCAAP and the CF value for different data suppliers. Some of the species had trailing spaces so these were removed using the Trim option. A few species had their name changed in a custom column to be more specific e.g. "fish dish" was changed to "fish dish other", "fish dish oily" or "fish dish lean" depending on the SACN type. Supplier was used as a pivot column to get one row per species and the different CF values by supplier (removing any suppliers which weren't in SupplyChain).
- Merged FishNutrition and FishTypesCFValues for a star schema
- The remaining columns in SupplyComposition added up the values in SupplyChain but not for all fish species. To double check that the amounts added up correctly I grouped a duplicate of SupplyComposition by commodity, species and year then used commodity as a pivot column to widen the table. There was only one value which didn't match due to more significant figures in one dataset (the salmon production values for 2019). 
- Deleted SupplyComposition because it no longer contained any different information from SupplyChain.
- A conditional column, DataSupplierDetail, was created to split out DEFRA into DEFRA_eatenOut and DEFRA_household. 
- Created a Conditional Column for Commodity Detail. This was to split Production into ProductionFisheries and ProductionAquaculture and Purchases into PurchasesHome and PurchasesOut. 
- Created a DataSources table which lists the dataset information. Removed these columns from SupplyChain.
- In SupplyChain used CommodityDetail as a pivot column to widen the table i.e. list species and year then the amount in grams of exports, imports, production fisheries, production aquaculture, consumption, purchases out and purchases home. This meant losing the connection to DataSources but because each type of data comes from one data source this isn't too important. As a reminder:
   - HMRC provide imports and exports data
   - MMO provide production data for fisheries
   - CEFAS provide production data for aquaculture
   - NDNS provide consumption data
   - DEFRA provide purchases data (both home and eating out). They only provide data on five fish types: "fish dish", "herring", "other marine fish", "other molluscs" and "salmon"
- Transformation steps for EUMOFA data:
  - Multiplied volume column by 1000 to change from kilograms to grams. 
  - Removed weight column because it always says net weight. Renamed volume column to NetWeightGrams.
  - Removed EU value because we haven't been considering monetary value and MCS column because it categorizes fish in different ways. 
  - Checked that CG values (species names) were unique.
  - Merged CG values with FishTypes table to see which ones matched. Changed the names of non-matching ones when absolutely sure they were referring to the same thing.
  - Grouped the data to retrieve the yearly consumption data instead of monthly. Did some spot checks to ensure the totals matched the original dataset.
  - The next aim was to add the EU data to SupplyChain for a star schema. The following steps were taken:
    - Created new table of species that didn't match FishTypes table and appended to FishTypes
    - Merged on year and fish species to add EU columns to SupplyChain
    - Created a duplicate of EUMOFA data and filtered to keep missing fish species
    - Append missing fish data to SupplyChain
    - Rename columns relating to the UK so it's clear they contain UK data
    - Hide unnecessary tables from report view
- Transformation of World Bank data (due to size, required EU countries were chosen before importing into Power BI):
  - Use first row as headers.
  - Removed country code, indicator name, indicator code and years between 1960 and 2008.
  - Unpivoted by year to get a longer table instead of a wide one.
  - Pivoted by country name to get one row for each year and a column for each country.
  - Merged the EU populations into the existing UKPopulation table and renamed the table Populations. 

### Data Notes
- Fisheries means fishing from the natural environment and aquaculture is harvesting/farming fish.
- Consumer demand is higher for the "big five": cod, haddock, tuna, salmon and prawns.
- SACN stands for Scientific Advisory Committee on Nutrition. "Other" category includes ready meals and fish such as octopus which are molluscs in the ISSCAAP list.
- ISSCAAP stands for International Standard Statistical Classification of Aquatic Animals and Plants. Diadromous fish migrate between salt water and fresh water. Marine fish live in salt water. Products are ready meals containing fish.
- The supply chain dataset includes data on imports, exports, production, consumption and purchases.
- Note the two conversions in the supply chain dataset. V1 says conversion factors were applied to all or part of the import/export data (the edible portion of fish). V2 says grams per capita per week were converted to grams per country per year (purchases/consumption data). The CF value (conversion factor) was found in SeafoodMatchingList.
- The NHS recommend people eat at least 2 portions (280g) of fish per week including one portion of oily fish. This applies to all ages except children under 2.
- Notes about EUMOFA data:
  - Monthly consumption data are provided by national sources or private providers.
  - A lot of countries are covered by Europanel data e.g. France, Germany, Ireland, Netherlands, Denmark etc. In this case an adequate sample size has been chosen to be representative of the population. Only the most common fish species are included and this varies by country. All other consumption is put under the category "other unspecified products". 
