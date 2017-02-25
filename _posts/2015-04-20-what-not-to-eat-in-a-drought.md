---
layout: post
title: "What (not) to eat in a drought: water usage scaled by nutritional value of food products"
date: 2015-04-20
tags: [ggplot2, r, dataviz]
---

The graphics below are an attempt to improve upon some of the recently published drought-related (info)graphics regarding water use for food production.  (Relevant examples include this [LA Times graphic](http://graphics.latimes.com/food-water-footprint/) and [supplementary graphics on nuts](http://www.latimes.com/local/lanow/la-me-g-how-water-hungry-are-californias-nuts-20150408-htmlstory.html), published on 7 and 9 April 2015, respectively, and "[Wired's guide to produce that won't make the drought worse](http://www.wired.com/2015/04/wireds-guide-produce-wont-make-drought-worse/)," published on 15 April 2015.) The major issue with these approaches is the metric they employ: the number of gallons of water required to produce some weight (ounces, grams, etc.) of a given food.  The approach used below instead assesses the number of gallons of water required to produce a unit of nutritional value for each food (Calories or grams of protein, for example).

To accomplish this, two major data sources were used.  The first is extensive ["water footprint" data by Mekonnen and Hoekstra](http://waterfootprint.org) on both crops and farm animal products. This is the same data source used by the LA Times and Wired (above). The second data source was the [USDA National Nutrient Database](http://ndb.nal.usda.gov/).  More details, along with a full list of references, are provided at the end of this document.

Notably, a "water footprint per kcal" approach, very similar to the one employed below, has been suggested and utilized in published works by Mekonnen and Hoekstra. Joanna Pearlstein, the author of the Wired article (referenced above), concedes that their infographic "doesn't consider foods' nutritional properties.... a water-intensive food that was also high-calorie and highly nutritious might be worth it."



***

The graphic below presents the average gallons of water required to produce one nutritional calorie of 78 food products of the United States. All food products in the LA Times graphic (referenced above) are included below except sparkling wine, for which nutritional information was not available in the USDA's NDB, and goat meat, for which U.S. water footprint information was not available in Mekonnen and Hoekstra's dataset.

Calorie for Calorie, asparagus requires the most water to produce, followed by mangoes, beef, lamb, and pork. Dates require the least water per kcal, with garlic, carrots, pineapples, and onions not far behind.

<img src="http://files.saraemoore.com/figures/2015-04-20-what-not-to-eat-in-a-drought-galperkcal-1.png" title="Average gallons of water required in the U.S. to produce one Calorie, listed by food product and category" alt="Average gallons of water required in the U.S. to produce one Calorie, listed by food product and category" width="600px" height="850px" style="display: block; margin: auto;" />

The second graphic, below, is more directly related to the California drought. It instead displays the average gallons of water used in California to produce one nutritional calorie of 64 food products. The food products included are a subset of those in the analogous U.S. figure, above, but are limited to those for which a California-specific average was included in Mekonnen and Hoekstra's dataset.

<img src="http://files.saraemoore.com/figures/2015-04-20-what-not-to-eat-in-a-drought-galperkcalcali-1.png" title="Average gallons of water required in California to produce one Calorie, listed by food product and category" alt="Average gallons of water required in California to produce one Calorie, listed by food product and category" width="600px" height="850px" style="display: block; margin: auto;" />

The third graphic is similar to the two above, but includes the global average gallons of water needed to produce one nutritional calorie of 80 food products.

<img src="http://files.saraemoore.com/figures/2015-04-20-what-not-to-eat-in-a-drought-galperkcalglobal-1.png" title="Average gallons of water required globally to produce one Calorie, listed by food product and category" alt="Average gallons of water required globally to produce one Calorie, listed by food product and category" width="600px" height="850px" style="display: block; margin: auto;" />

The graphic below displays a different take on nutritional content: the average gallons of water required, in the U.S., to produce one gram of protein in 18 foods in "protein-rich" food product categories. Hazelnuts, walnuts, and beef perform worst, while soy milk, chicken, and eggs are the most water-efficient "proteins."

<img src="http://files.saraemoore.com/figures/2015-04-20-what-not-to-eat-in-a-drought-galpergprotein-1.png" title="Average gallons of water required in the U.S. to produce one gram of protein, listed by food product and category" alt="Average gallons of water required in the U.S. to produce one gram of protein, listed by food product and category" width="600px" height="850px" style="display: block; margin: auto;" />

***

## Additional details

Water footprint data encompasses so-called "blue" (fresh surface and groundwater), "green" (rainwater), and "gray" (freshwater polluted by a product's production) water use, the sum of which is used here for simplicity. A more detailed analysis could investigate these types of water use separately. Further, farm animal product water use was available for three production systems (grazing, industrial, and mixed), the weighted average of which was used in this analysis.

[Google's unit converter](https://www.google.com/search?q=unit+converter#q=cubic+meters+per+ton+to+gallons+per+100+grams) was used to calculate a "scaling factor" to convert the water footprint data from cubic meters of water per metric ton of food product to gallons of water per 100 grams of food product. The units used in the USDA's database were nutritional value (Calories, grams of protein, etc.) per 100 grams of food (edible portion). Food products were manually matched between the two databases. Subsequently, the quotient of these two metrics was calculated for each food product, yielding gallons of water needed to produce a given nutritional value unit.

Note that via Ercin, Aldaya, & Hoekstra (2012), feedback received on twitter, and the [LA Times' correction on 12 April 2015](http://www.latimes.com/local/corrections/la-a4-correx-20150412-story.html), the figure for U.S. and global average gallons of water required to produce 100 grams of soy milk has been manually corrected from 84.92338 gal/100g to 7.580173 gal/100g.  This figure is for global, not U.S., production (China, France, and Canada).

Code (as an RMarkdown file) and data files required to reproduce this analysis are available [on github](http://github.com/saraemoore/h2otofood).

(**Update** 22 Aug 2016: this report has been regenerated as a blog post using ggplot2 v2.1.0 for all plots and readxl v0.1.1 for one problematic spreadsheet. The updated code to reproduce this analysis is available in [this website's github repository](https://github.com/saraemoore/saraemoore.github.io/tree/master/_source).)

***

## References

### Data

[Mekonnen, M.M. and Hoekstra, A.Y. (2011) The green, blue and grey water footprint of crops and derived crop products, Hydrology and Earth System Sciences, 15(5): 1577-1600.](http://waterfootprint.org/media/downloads/Mekonnen-Hoekstra-2011-WaterFootprintCrops_1.pdf)

[Mekonnen, M.M. and Hoekstra, A.Y. (2010) The green, blue and grey water footprint of crops and derived crop products, Value of Water Research Report Series No. 47, UNESCO-IHE, Delft, the Netherlands.](http://www.waterfootprint.org/Reports/Report47-WaterFootprintCrops-Vol1.pdf)

Data file used in this analysis: [Water footprints of crops and derived crop products (1996-2005)](http://waterfootprint.org/media/downloads/Report47-Appendix-II.zip)

[Ercin, A.E., Aldaya, M.M. and Hoekstra, A.Y. (2012) The water footprint of soy milk and soy burger and equivalent animal products, Ecological Indicators, 18: 392−402.](http://waterfootprint.org/media/downloads/Ercin-et-al-2012-WaterFootprintSoy_1.pdf)

[Mekonnen, M.M. and Hoekstra, A.Y. (2012) A global assessment of the water footprint of farm animal products, Ecosystems, 15(3): 401–415.](http://waterfootprint.org/media/downloads/Mekonnen-Hoekstra-2012-WaterFootprintFarmAnimalProducts_1.pdf)

[Mekonnen, M.M. and Hoekstra, A.Y. (2010) The green, blue and grey water footprint of farm animals and animal products, Value of Water Research Report Series No. 48, UNESCO-IHE, Delft, the Netherlands.](http://waterfootprint.org/media/downloads/Report-48-WaterFootprint-AnimalProducts-Vol1.pdf)

Data file used in this analysis: [Water footprints of farm animals and animal products (1996-2005)](http://waterfootprint.org/media/downloads/Report48-Appendix-V.zip)


U.S. Department of Agriculture, Agricultural Research Service. 2014. USDA National Nutrient Database for Standard Reference, Release 27. [Nutrient Data Laboratory Home Page](http://www.ars.usda.gov/ba/bhnrc/ndl)

Data file used in this analysis: [Abbreviated SR27, Excel version](https://www.ars.usda.gov/SP2UserFiles/Place/12354500/Data/SR27/dnload/sr27abxl.zip)

### Software

[R Core Team (2016). R: A language and environment for statistical computing. R Foundation for Statistical Computing, Vienna, Austria.](http://www.R-project.org/)

[Hadley Wickham (2009). ggplot2: elegant graphics for data analysis. Springer New York.](http://had.co.nz/ggplot2/book)

[Yihui Xie (2016). knitr: A General-Purpose Package for Dynamic Report Generation in R. R package version 1.13.](http://CRAN.R-project.org/package=knitr)

[Alexander Walker (2015). openxlsx: Read, Write and Edit XLSX Files. R package version 3.0.0.](http://CRAN.R-project.org/package=openxlsx)

[Hadley Wickham (2016). readxl: Read Excel Files. R package version 0.1.1.](https://CRAN.R-project.org/package=readxl)
