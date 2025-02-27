# UK Seafood Report

## Summary 
Use Power BI to explore UK seafood data retrieved from [UK Data Service](https://reshare.ukdataservice.ac.uk/856955/). The data includes information about what's being produced, imported, exported and consumed. 

### Data Notes
- Fisheries means fishing from the natural environment and aquaculture is harvesting/farming fish.
- Consumer demand is higher for the "big five": cod, haddock, tuna, salmon and prawns.
- SACN stands for Scientific Advisory Committee on Nutrition. Other includes ready meals and fish such as octopus which are molluscs in the other species list.
- ISSCAAP stands for International Standard Statistical Classification of Aquatic Animals and Plants. They have a "Remove" category which might need to be removed depending on how we use SeafoodMatchingList.csv. Diadromous fish migrate between salt water and fresh water. Marine fish live in salt water. Products are ready meals containing fish.
- ISSCAAP Other, Remove and Product categories had NA as code. I changed this to null. 

### Things to Investigate
- What is the CF value in SeafoodMatchingList.csv?
- Does the data mention where products are being imported from?
- Is there any data on fish stocks / sustainable fishing?
- Is there any data on carbon emissions of producing and trading fish?
- How much of our consumption is of the big five fish? Has this changed over time?
- How much of our consumption is of fish products (i.e. ready meals) rather than whole fish? Double check Product in ISSCAAP does mean this...
- How much of the big five are we importing? What other fish are we exporting and in what quantities?
- Would it be possible to compare this data with another country?
