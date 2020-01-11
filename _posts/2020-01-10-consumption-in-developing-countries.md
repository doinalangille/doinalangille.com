---
layout: post
title: Consumption in Developing Countries
subtitle: Top five categories of products on which people spend the most
image: /img/food.jpg
bigimg: /img/food.jpg
tags: [consumption, per capita expenditure, developing countries, expenditure, top products]
comments: true
---

Everybody is different, and we all are similar at the same time. At least when we talk about our regular life. We all have the same fundamental needs, and we all try to have the best life based on our beliefs and values. The rules of this game called "Life" are pretty simple: we are born, we get a certain level of education and then receive an income which we use to survive or to be happier. According to [an article from April 2019](https://journals.sagepub.com/doi/abs/10.1177/1948550619835215?journalCode=sppa#articleCitationDownloadContainer) people are happier when they spend. [The world's happiest countries](https://www.cnn.com/travel/article/worlds-happiest-countries-united-nations-2019/index.html) are all developed economies. How about the countries with economies in transition and developing economies? Why none of these countries is on the top?

If consumption is a factor of happiness, this article aims to show what people from economies in transition and developing economies are buying. So what are the products and services on which people from these countries spend most of their income? The World Bank's [global consumption database](https://datatopics.worldbank.org/consumption/detail) is the best source to analyze the spending on goods and services. The data are World Bank estimates based on national household consumption or expenditure survey datasets, surveys that have nationwide coverage. More information about the database is presented on the [World Bank website](https://datatopics.worldbank.org/consumption/detail).

The indicator analyzed in this article is Per capita consumption 2010 for countries from six regions: 
- Latin America and the Caribbean, 
- The Middle East and North Africa,
- Sub-Saharan Africa,
- East Asia and the Pacific,
- Eastern Europe and Central Asia,
- South Asia.

The original database presents the data at the national level by country, category of products, and consumption segment. World Bank categorized the households in four consumption segments based on global income distribution data, but this analysis is focused on the overall consumption, ignoring the defined segments. You can see details about cleaning and pre-processing the database, including code and comments of the analysis, in my [Colab Notebook](https://github.com/doinalangille/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/Consumption_in_developing_countries.ipynb).
Each country has a certain per capita expenditure for 23 categories of products or services.

![Categories](/img/img_categories.PNG){: .center-block :}

First, let's take a look at the overall expenditures for all the products. People from more than half of the developing countries spend less than $1,000 a year on goods and services, especially in Sub-Saharan Africa and South Asia. People from Bosnia and Herzegovina spend the most on goods and services ($4,841 per person), followed by those from Brazil ($4,279), Latvia ($4,133), Russia ($3,916), and Montenegro ($3,610). People from Madagascar spend the less ($149 per person) on goods and services, followed by those from Burundi ($174), Democratic Republic of Congo ($217), Tanzania ($238), and Nicaragua ($248).

<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~doinalangille/7.embed" height="525" width="100%"></iframe>

People from all the six regions spend most of their income on almost the same categories of products and services. For example, the housing and the other products and services are on the top five categories on which people spend the most. The category "housing" covers products and services such as actual and imputed rentals for housing, maintenance and repair of the dwelling, major household appliances, small tools, and miscellaneous accessories. In the "other" category are included products and services like furniture, carpets, household textiles, major tools and equipment, postal services, major durables for outdoor and indoor recreation, garden and pets, veterinary, cultural services, hairdressing salons and personal grooming establishments, jewelry, clocks and watches, social protection or tobacco. 

![Top](/img/top_prod.PNG){: .center-block :}

However, the expenses for vehicles and other transport services are on top only in Latin America and the Caribbean comparing to the other regions. In this category are included motor cars, motorcycles, bicycles, maintenance and repair of personal transport equipment, as well as passenger transport in different ways (railway, road, air, sea and inland waterway). South Asia is the only region that has on top the expenses for other food (oil, fats, sugar, jams, marmalades, honey, confectionery, chocolate, ice cream, spices, and seasonings). Eastern Europa and Central Asia differ from the other regions by having the dairy on top five products on which people spend. Other top categories of products for the analyzed regions are grains, fruits and vegetables, meat and fish.

In conclusion, people from developing countries spend most of their income on the indispensable products to survive, such as food or housing, as well as on products aimed to make their life easier, such as household goods. As a future project, it would be interesting to analyze the expenditures in the highly developed countries and to compare the level of happiness between these "different worlds" based on their spendings.

You can find this article on [Medium](https://medium.com/@doina.jitoreanu/consumption-in-developing-countries-ea98dbb5795d) too.
