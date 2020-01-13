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

The original database presents the data at the national level by country, category of products, and consumption segment.
```
df = pd.read_csv("https://raw.githubusercontent.com/doinalangille/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/master/consumption_per_capita.txt")
df.head()
```
![Pce_head](/img/head_pce.PNG)

The column names have different formats, so it's always a good idea to rename them, to work more efficiently.
```
df.rename(columns={'Country_Hide': 'country_hide', 
                   'Number of Records': 'no_of_records',   
                   'Area':'area', 'Country':'country',  
                   'countrycode':'country_code', 
                   'Consumption Segment':'consumption_segment', 
                   'Level':'level', 
                   'Sector Category or Product':'sector_cat_prod', 
                   'Region':'region'}, 
           inplace=True)
```
Observations on the data set:
- The database contains 11,830 rows and 182 missing values. We can drop the missing values using dropna() function.
- The columns `no_of_records`, `area`, and `level` have only one value
- The indicator per capita expenditure is presented in three different unit measures: local currency (pce_lcu), purchasing power parity (pce_ppp), and USD (pce_usd)
- The columns `country_hide` and `country` have the same values
Only the variable `pce_usd` is analyzed for the purpose of this article, in order to have comparability over the countries.

Another important step in pre-processing data is to check the types of the variables.
```
# Check the types of the variables
df.dtypes
```
![Pce_dtypes](/img/pce_dtypes.PNG)
The variable `pce_usd` should be float, but it is a string in our database. I also noticed that the values of this variable have a comma as thousand separator, so we have to delete comma before converting the string to float.
```
# Take a look at the longest string of this variable
max(df['pce_usd'], key=len)
```
![separator](/img/pce_longest.PNG)
```
# Delete comma before converting to numeric
df['pce_usd'].replace(regex=True,inplace=True,to_replace=r',',
                      value=r'')
# Convert the variable to float
df['pce_usd'] = df['pce_usd'].astype(float)
```
World Bank categorized the households in four consumption segments based on global income distribution data, but this analysis is focused on the overall consumption, ignoring the defined segments. 
```
# Filter the dataframe for "All" consumption segment
df = df[df['consumption_segment']=='All']
```
Another issue of the data set is an aggregate value for all the categories of products. The database contains data for 23 different categories and also an aggregate value called "All categories". I split the dataframe into two different dataframes in order to simplify the work while analyzing the data.
```
# Dataframe with the aggregated values
df_AggCat = df[df['sector_cat_prod']=='All categories']
# Dataframe for detailed categories
df_ByCat = df[df['sector_cat_prod']!='All categories']
```
You can see more details about cleaning and pre-processing the database, including code and comments of the analysis, in my [Colab Notebook](https://github.com/doinalangille/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/Consumption_in_developing_countries.ipynb).

Let's take a look at the overall expenditures for all the products, using a choropleth map.
```
fig = px.choropleth(df_AggCat, locations="country_code",
                 color="pce_usd",
                 hover_name="country",
                 color_continuous_scale=px.colors.sequential.Plasma,
                 width=750, height=500, 
                 title='Global overview of the per capita
                        expenditures') 
fig.update_layout(coloraxis_colorbar=dict(title='USD', 
                  tickprefix="$")) 
fig.show()
```
<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~doinalangille/7.embed" height="525" width="100%"></iframe>
People from more than half of the developing countries spend less than $1,000 a year on goods and services, especially in Sub-Saharan Africa and South Asia. People from Bosnia and Herzegovina spend the most on goods and services ($4,841 per person), followed by those from Brazil ($4,279), Latvia ($4,133), Russia ($3,916), and Montenegro ($3,610). People from Madagascar spend the less ($149 per person) on goods and services, followed by those from Burundi ($174), Democratic Republic of Congo ($217), Tanzania ($238), and Nicaragua ($248).

Each country has a certain per capita expenditure for 23 categories of products or services.
![Categories](/img/img_categories.PNG){: .center-block :}
To see the top 5 categories of products on which people from developing countries spend the most, I created subplots for every region. Skip the code snippet if you are not interested in following the code behind the visualization.
```
# Get the data for each region
graph_data1 = df_ByCat[df_ByCat['region']=='Sub-Saharan Africa']
myresults1 = graph_data1.groupby('sector_cat_prod')
             ['pce_usd'].mean().sort_values(ascending=False).head(5)
graph_data2 = df_ByCat[df_ByCat['region']=='South Asia']
myresults2 = graph_data2.groupby('sector_cat_prod')
             ['pce_usd'].mean().sort_values(ascending=False).head(5)
graph_data3 = df_ByCat[df_ByCat['region']=='East Asia and Pacific']
myresults3 = graph_data3.groupby('sector_cat_prod')
             ['pce_usd'].mean().sort_values(ascending=False).head(5)
graph_data4 = df_ByCat[df_ByCat['region']=='Latin America and 
              Caribbean']
myresults4 = graph_data4.groupby('sector_cat_prod')
             ['pce_usd'].mean().sort_values(ascending=False).head(5)
graph_data5 = df_ByCat[df_ByCat['region']=='Middle East and North 
              Africa']
myresults5 = graph_data5.groupby('sector_cat_prod')
             ['pce_usd'].mean().sort_values(ascending=False).head(5)
graph_data6 = df_ByCat[df_ByCat['region']=='Eastern Europe and 
              Central Asia']
myresults6 = graph_data6.groupby('sector_cat_prod')
             ['pce_usd'].mean().sort_values(ascending=False).head(5)

# Plot the data
fig = make_subplots(rows=6, cols=1, shared_xaxes=True,
subplot_titles=("Sub-Saharan Africa", "South Asia", 
                "East Asia and Pacific", 
                "Latin America and Caribbean", 
                "Middle East and North Africa", 
                "Eastern Europe and Central Asia"))
fig.append_trace(go.Bar(x=myresults1.values, y=myresults1.index,  
                        name='Sub-Saharan Africa', orientation='h',
                        marker=dict(color='rgb(22,7,138)')), 
                 row=1, col=1)
fig.append_trace(go.Bar(x=myresults2.values, y=myresults2.index, 
                        name='South Asia', orientation='h', 
                        marker=dict(color='rgb(22,7,138)')), 
                 row=2, col=1)
fig.append_trace(go.Bar(x=myresults3.values, y=myresults3.index, 
                        name='East Asia and Pacific',
                        orientation='h',                
                        marker=dict(color='rgb(22,7,138)')), 
                 row=3, col=1)
fig.append_trace(go.Bar(x=myresults4.values, y=myresults4.index, 
                        name='Latin America and Caribbean',  
                        orientation='h', 
                        marker=dict(color='rgb(22,7,138)')), 
                 row=4, col=1)
fig.append_trace(go.Bar(x=myresults5.values, y=myresults5.index, 
                        name='Middle East and North Africa',
                        orientation='h',
                        marker=dict(color='rgb(22,7,138)')),
                 row=5, col=1)
fig.append_trace(go.Bar(x=myresults6.values, y=myresults6.index, 
                        name='Eastern Europe and Central Asia',
                        orientation='h',
                        marker=dict(color='rgb(22,7,138)')),
                 row=6, col=1)
fig.update_layout(height=950, width=1200, showlegend=False,  
                  title_text="Top 5 categories of products by per 
                           capita expenditure in different regions")
fig.update_xaxes(title_text="Per capita expenditures (USD)", 
                 row=6, col=1)
fig.show()
```
![Top](/img/top_prod.PNG){: .center-block :}
People from all the six regions spend most of their income on almost the same categories of products and services. For example, the housing and the other products and services are on the top five categories on which people spend the most. The category "housing" covers products and services such as actual and imputed rentals for housing, maintenance and repair of the dwelling, major household appliances, small tools, and miscellaneous accessories. In the "other" category are included products and services like furniture, carpets, household textiles, major tools and equipment, postal services, major durables for outdoor and indoor recreation, garden and pets, veterinary, cultural services, hairdressing salons and personal grooming establishments, jewelry, clocks and watches, social protection or tobacco. 

However, the expenses for vehicles and other transport services are on top only in Latin America and the Caribbean comparing to the other regions. In this category are included motor cars, motorcycles, bicycles, maintenance and repair of personal transport equipment, as well as passenger transport in different ways (railway, road, air, sea and inland waterway). South Asia is the only region that has on top the expenses for other food (oil, fats, sugar, jams, marmalades, honey, confectionery, chocolate, ice cream, spices, and seasonings). Eastern Europa and Central Asia differ from the other regions by having the dairy on top five products on which people spend. Other top categories of products for the analyzed regions are grains, fruits and vegetables, meat and fish.

In conclusion, people from developing countries spend most of their income on the indispensable products to survive, such as food or housing, as well as on products aimed to make their life easier, such as household goods. As a future project, it would be interesting to analyze the expenditures in the highly developed countries and to compare the level of happiness between these "different worlds" based on their spendings.

You can find this article on [Medium](https://medium.com/@doina.jitoreanu/consumption-in-developing-countries-ea98dbb5795d) too.
