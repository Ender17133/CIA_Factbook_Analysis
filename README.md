# Analyzing CIA Factbook Data
In this project, I will be working with [CIA World Factbook](https://www.cia.gov/the-world-factbook/) data, which includes statistics about all countries on Earth.

Let's use the code to connect Jupyter Notebook to database file.



```python
%%capture 
%load_ext sql 
%sql sqlite:///factbook.db
```




    'Connected: None@factbook.db'



## Overview of the Data
I will begin with observing first 5 rows of the data.


```sql
%%sql
select *
    from facts 
    limit 5
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>1</td>
        <td>af</td>
        <td>Afghanistan</td>
        <td>652230</td>
        <td>652230</td>
        <td>0</td>
        <td>32564342</td>
        <td>2.32</td>
        <td>38.57</td>
        <td>13.89</td>
        <td>1.51</td>
    </tr>
    <tr>
        <td>2</td>
        <td>al</td>
        <td>Albania</td>
        <td>28748</td>
        <td>27398</td>
        <td>1350</td>
        <td>3029278</td>
        <td>0.3</td>
        <td>12.92</td>
        <td>6.58</td>
        <td>3.3</td>
    </tr>
    <tr>
        <td>3</td>
        <td>ag</td>
        <td>Algeria</td>
        <td>2381741</td>
        <td>2381741</td>
        <td>0</td>
        <td>39542166</td>
        <td>1.84</td>
        <td>23.67</td>
        <td>4.31</td>
        <td>0.92</td>
    </tr>
    <tr>
        <td>4</td>
        <td>an</td>
        <td>Andorra</td>
        <td>468</td>
        <td>468</td>
        <td>0</td>
        <td>85580</td>
        <td>0.12</td>
        <td>8.13</td>
        <td>6.96</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>5</td>
        <td>ao</td>
        <td>Angola</td>
        <td>1246700</td>
        <td>1246700</td>
        <td>0</td>
        <td>19625353</td>
        <td>2.78</td>
        <td>38.78</td>
        <td>11.49</td>
        <td>0.46</td>
    </tr>
</table>



The Factbook contains important demographic data such as:
 - **name** - the name of the country. 
 - **area** - the country's total area (both land and water).
 - **area_land** - the country's land area in square kilometers.
 - **area_water** - the country's water area in square kilometers.
 - **population** - the country's population
 - **population_growth** - the annual population growth rate of the country, as a percentage. 
 - **birth_rate** - the country's birth rate, or the number of births per year per 1,000 people.
 - **death_rate** - the country's death rate, or the number of death per year per 1,000 people.
     

## Summary Statistics
Let's continue by calculating some summary staticstics and observe what they tell us.


```sql
%%sql 
select MIN(population) as min_pop, MAX(population) as max_pop, 
MIN(population_growth) as min_pop_growth, MAX(population_growth) as max_pop_growth 
    from facts
```

    Done.
    




<table>
    <tr>
        <th>min_pop</th>
        <th>max_pop</th>
        <th>min_pop_growth</th>
        <th>max_pop_growth</th>
    </tr>
    <tr>
        <td>0</td>
        <td>7256490011</td>
        <td>0.0</td>
        <td>4.02</td>
    </tr>
</table>



Interesting facts about calculated summary statistics:
 - There's a country with a population of 0
 - There's also a country with a population of 7256490011 (7.2+ billion of people)
 
 I will use subqueries to zoom in on just these countries _without_ using the specific values.
 

## Exploring Outliers
First, I will write a query that returns the countries with the minimum population.


```sql
%%sql 
select *
    from facts 
    where population = (select MIN(population) from facts)
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>250</td>
        <td>ay</td>
        <td>Antarctica</td>
        <td>None</td>
        <td>280000</td>
        <td>None</td>
        <td>0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



This table only contains a row for Antarctica, which explains population of 0, as there are no inhabitants in Antarctica. CIA webpage about Antarctica supports my findings. ![](https://camo.githubusercontent.com/6e15f6abc5b22dd52b087ba616738d5f6dd39fc666d445608f0d7c933892e5f9/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f64712d636f6e74656e742f3235372f66625f616e74617263746963612e706e67) 


Now, let's find the countries with the maximum population.


```sql
%%sql
select * 
    from facts 
    where population  = (select MAX(population) from facts)
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>261</td>
        <td>xx</td>
        <td>World</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>7256490011</td>
        <td>1.08</td>
        <td>18.6</td>
        <td>7.8</td>
        <td>None</td>
    </tr>
</table>



It can be seen that there is also a row for the whole World, which could explain 7.2 billion of people that we found earlier.

Knowing this, I should recalculate summary statistics of the data without including a row for the whole World.


```sql
%%sql
select MIN(population) as min_pop, MAX(population) as max_pop, MIN(population_growth) as min_pop_growth, MAX(population_growth) as max_pop_growth
    from facts
    where name <> 'World'
```

    Done.
    




<table>
    <tr>
        <th>min_pop</th>
        <th>max_pop</th>
        <th>min_pop_growth</th>
        <th>max_pop_growth</th>
    </tr>
    <tr>
        <td>0</td>
        <td>1367485388</td>
        <td>0.0</td>
        <td>4.02</td>
    </tr>
</table>



The minimum population is still 0, but the maximum population is around 1.4 billion people (1367485388). Let's find out which country has the the highest number of population.


```sql
%%sql 
select * 
    from facts 
    where name <> 'World'
    ORDER BY population desc
    limit 1
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>37</td>
        <td>ch</td>
        <td>China</td>
        <td>9596960</td>
        <td>9326410</td>
        <td>270550</td>
        <td>1367485388</td>
        <td>0.45</td>
        <td>12.49</td>
        <td>7.53</td>
        <td>0.44</td>
    </tr>
</table>



China is the country with approximately 1.4 billion of people, which is a maximum population according to this data analysis. 

Now I want to find out which countries have the highest and lowest growth rates according to data.


```sql
%%sql 
select * 
    from facts
    where name <> 'World'
    ORDER BY population_growth desc
    limit 1
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>162</td>
        <td>od</td>
        <td>South Sudan</td>
        <td>644329</td>
        <td>None</td>
        <td>None</td>
        <td>12042910</td>
        <td>4.02</td>
        <td>36.91</td>
        <td>8.18</td>
        <td>11.47</td>
    </tr>
</table>




```sql
%%sql 
    select * 
    from facts 
    where population_growth = (select MIN(population_growth) from facts)
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>190</td>
        <td>vt</td>
        <td>Holy See (Vatican City)</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>842</td>
        <td>0.0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>200</td>
        <td>ck</td>
        <td>Cocos (Keeling) Islands</td>
        <td>14</td>
        <td>14</td>
        <td>0</td>
        <td>596</td>
        <td>0.0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>207</td>
        <td>gl</td>
        <td>Greenland</td>
        <td>2166086</td>
        <td>2166086</td>
        <td>None</td>
        <td>57733</td>
        <td>0.0</td>
        <td>14.48</td>
        <td>8.49</td>
        <td>5.98</td>
    </tr>
    <tr>
        <td>238</td>
        <td>pc</td>
        <td>Pitcairn Islands</td>
        <td>47</td>
        <td>47</td>
        <td>0</td>
        <td>48</td>
        <td>0.0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



South Sudan is the country with the highest annual population growth rate. (4%)

Holy See, Cocos, Greenland and Pitcairn Islands are not independent countries, so I will exclude these rows and look on minimum population growth rates again.


```sql
%%sql 
select MIN(population) as 'min_pop', MAX(population) as 'max_pop', MIN(population_growth) as 'min_population_growth', MAX(population_growth) as 'max_population_growth'
    from facts
    where name NOT IN ('Holy See (Vatican City)', 'Holy See (Vatican City)', 'Greenland', 'Pitcairn Islands', 'World', 'Cocos (Keeling) Islands')
```

    Done.
    




<table>
    <tr>
        <th>min_pop</th>
        <th>max_pop</th>
        <th>min_population_growth</th>
        <th>max_population_growth</th>
    </tr>
    <tr>
        <td>0</td>
        <td>1367485388</td>
        <td>0.01</td>
        <td>4.02</td>
    </tr>
</table>




```sql
%%sql 
select * 
    from facts 
    where name NOT IN ('Holy See (Vatican City)', 'Greenland', 'Pitcairn Islands', 'World', 'Cocos (Keeling) Islands')
    AND population_growth = (select MIN(population_growth) from 
    facts where name NOT IN ('Holy See (Vatican City)', 'Greenland', 'Pitcairn Islands', 'World', 'Cocos (Keeling) Islands'));

```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>67</td>
        <td>gr</td>
        <td>Greece</td>
        <td>131957</td>
        <td>130647</td>
        <td>1310</td>
        <td>10775643</td>
        <td>0.01</td>
        <td>8.66</td>
        <td>11.09</td>
        <td>2.32</td>
    </tr>
    <tr>
        <td>203</td>
        <td>nf</td>
        <td>Norfolk Island</td>
        <td>36</td>
        <td>36</td>
        <td>0</td>
        <td>2210</td>
        <td>0.01</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>221</td>
        <td>tl</td>
        <td>Tokelau</td>
        <td>12</td>
        <td>12</td>
        <td>0</td>
        <td>1337</td>
        <td>0.01</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>232</td>
        <td>fk</td>
        <td>Falkland Islands (Islas Malvinas)</td>
        <td>12173</td>
        <td>12173</td>
        <td>0</td>
        <td>3361</td>
        <td>0.01</td>
        <td>10.9</td>
        <td>4.9</td>
        <td>None</td>
    </tr>
</table>



The minimum annual population growth rate is now 0.01 percent. However, we can't consider rows in the table except row with a Greece, as Norfolk Island is Australian territory, Tokelau is New Zealand territory and Falkland Islands is a territory of United Kingdom.

That is why, South Sudan is a country with the highest annual population growth rate and it will more likely to add the most people to population. Oppositely, Greece is a country with the lowest annual population growth rate and it will add the least people to their populations next year.

## Exploring Average Population and Area
Now I will calculate average values for `population` and `area` columns.


```sql
%%sql
select AVG(population) as average_population, AVG(area) as average_area
    from facts 
    where name <> 'World'
```

    Done.
    




<table>
    <tr>
        <th>average_population</th>
        <th>average_area</th>
    </tr>
    <tr>
        <td>32242666.56846473</td>
        <td>555093.546184739</td>
    </tr>
</table>



According to the results, the average population is around 32 million people and average area is 555 thousand square kilometers.

## Finding Densely Populated Countries
I will build the query to find countries which are densely populated. I will identify countries that have the following: 
- **Above-average values for population.**
- **Below-average values for area.**



```sql
%%sql 
select * 
    from facts 
    where population > (select AVG(population) from facts where name <> 'World')
    AND area < (select AVG(area) from facts where name <> 'World')
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>14</td>
        <td>bg</td>
        <td>Bangladesh</td>
        <td>148460</td>
        <td>130170</td>
        <td>18290</td>
        <td>168957745</td>
        <td>1.6</td>
        <td>21.14</td>
        <td>5.61</td>
        <td>0.46</td>
    </tr>
    <tr>
        <td>65</td>
        <td>gm</td>
        <td>Germany</td>
        <td>357022</td>
        <td>348672</td>
        <td>8350</td>
        <td>80854408</td>
        <td>0.17</td>
        <td>8.47</td>
        <td>11.42</td>
        <td>1.24</td>
    </tr>
    <tr>
        <td>80</td>
        <td>iz</td>
        <td>Iraq</td>
        <td>438317</td>
        <td>437367</td>
        <td>950</td>
        <td>37056169</td>
        <td>2.93</td>
        <td>31.45</td>
        <td>3.77</td>
        <td>1.62</td>
    </tr>
    <tr>
        <td>83</td>
        <td>it</td>
        <td>Italy</td>
        <td>301340</td>
        <td>294140</td>
        <td>7200</td>
        <td>61855120</td>
        <td>0.27</td>
        <td>8.74</td>
        <td>10.19</td>
        <td>4.1</td>
    </tr>
    <tr>
        <td>85</td>
        <td>ja</td>
        <td>Japan</td>
        <td>377915</td>
        <td>364485</td>
        <td>13430</td>
        <td>126919659</td>
        <td>0.16</td>
        <td>7.93</td>
        <td>9.51</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>91</td>
        <td>ks</td>
        <td>Korea, South</td>
        <td>99720</td>
        <td>96920</td>
        <td>2800</td>
        <td>49115196</td>
        <td>0.14</td>
        <td>8.19</td>
        <td>6.75</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>120</td>
        <td>mo</td>
        <td>Morocco</td>
        <td>446550</td>
        <td>446300</td>
        <td>250</td>
        <td>33322699</td>
        <td>1.0</td>
        <td>18.2</td>
        <td>4.81</td>
        <td>3.36</td>
    </tr>
    <tr>
        <td>138</td>
        <td>rp</td>
        <td>Philippines</td>
        <td>300000</td>
        <td>298170</td>
        <td>1830</td>
        <td>100998376</td>
        <td>1.61</td>
        <td>24.27</td>
        <td>6.11</td>
        <td>2.09</td>
    </tr>
    <tr>
        <td>139</td>
        <td>pl</td>
        <td>Poland</td>
        <td>312685</td>
        <td>304255</td>
        <td>8430</td>
        <td>38562189</td>
        <td>0.09</td>
        <td>9.74</td>
        <td>10.19</td>
        <td>0.46</td>
    </tr>
    <tr>
        <td>163</td>
        <td>sp</td>
        <td>Spain</td>
        <td>505370</td>
        <td>498980</td>
        <td>6390</td>
        <td>48146134</td>
        <td>0.89</td>
        <td>9.64</td>
        <td>9.04</td>
        <td>8.31</td>
    </tr>
    <tr>
        <td>173</td>
        <td>th</td>
        <td>Thailand</td>
        <td>513120</td>
        <td>510890</td>
        <td>2230</td>
        <td>67976405</td>
        <td>0.34</td>
        <td>11.19</td>
        <td>7.8</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>182</td>
        <td>ug</td>
        <td>Uganda</td>
        <td>241038</td>
        <td>197100</td>
        <td>43938</td>
        <td>37101745</td>
        <td>3.24</td>
        <td>43.79</td>
        <td>10.69</td>
        <td>0.74</td>
    </tr>
    <tr>
        <td>185</td>
        <td>uk</td>
        <td>United Kingdom</td>
        <td>243610</td>
        <td>241930</td>
        <td>1680</td>
        <td>64088222</td>
        <td>0.54</td>
        <td>12.17</td>
        <td>9.35</td>
        <td>2.54</td>
    </tr>
    <tr>
        <td>192</td>
        <td>vm</td>
        <td>Vietnam</td>
        <td>331210</td>
        <td>310070</td>
        <td>21140</td>
        <td>94348835</td>
        <td>0.97</td>
        <td>15.96</td>
        <td>5.93</td>
        <td>0.3</td>
    </tr>
</table>



Some of these countries in the table are known to be densely populated, so I can consider these results accurate.

## Further Population and Area Data Analysis
There are also few interesting questions that should be answered by me in this project. These questions are: 
- **1. Which countries have a higher death rate than birth rate?**
- **2. Which countries have the highest population/area ratio, and how does it compare to list I found in the previous screen?**

Let's answer to the first question. First I need to identify the maximum and minimum death rates in the data.


```sql
%%sql
select MIN(death_rate) as min_death_rate, MAX(death_rate) as max_death_rate
    from facts
```

    Done.
    




<table>
    <tr>
        <th>min_death_rate</th>
        <th>max_death_rate</th>
    </tr>
    <tr>
        <td>1.53</td>
        <td>14.89</td>
    </tr>
</table>




```sql
%%sql 
select * 
    from facts 
    where death_rate = (select MIN(death_rate) from facts)
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>141</td>
        <td>qa</td>
        <td>Qatar</td>
        <td>11586</td>
        <td>11586</td>
        <td>0</td>
        <td>2194817</td>
        <td>3.07</td>
        <td>9.84</td>
        <td>1.53</td>
        <td>22.39</td>
    </tr>
</table>




```sql
%%sql 
select * 
    from facts
    where death_rate > birth_rate 
    ORDER BY death_rate desc
```

    Done.
    




<table>
    <tr>
        <th>id</th>
        <th>code</th>
        <th>name</th>
        <th>area</th>
        <th>area_land</th>
        <th>area_water</th>
        <th>population</th>
        <th>population_growth</th>
        <th>birth_rate</th>
        <th>death_rate</th>
        <th>migration_rate</th>
    </tr>
    <tr>
        <td>183</td>
        <td>up</td>
        <td>Ukraine</td>
        <td>603550</td>
        <td>579330</td>
        <td>24220</td>
        <td>44429471</td>
        <td>0.6</td>
        <td>10.72</td>
        <td>14.46</td>
        <td>2.25</td>
    </tr>
    <tr>
        <td>26</td>
        <td>bu</td>
        <td>Bulgaria</td>
        <td>110879</td>
        <td>108489</td>
        <td>2390</td>
        <td>7186893</td>
        <td>0.58</td>
        <td>8.92</td>
        <td>14.44</td>
        <td>0.29</td>
    </tr>
    <tr>
        <td>96</td>
        <td>lg</td>
        <td>Latvia</td>
        <td>64589</td>
        <td>62249</td>
        <td>2340</td>
        <td>1986705</td>
        <td>1.06</td>
        <td>10.0</td>
        <td>14.31</td>
        <td>6.26</td>
    </tr>
    <tr>
        <td>102</td>
        <td>lh</td>
        <td>Lithuania</td>
        <td>65300</td>
        <td>62680</td>
        <td>2620</td>
        <td>2884433</td>
        <td>1.04</td>
        <td>10.1</td>
        <td>14.27</td>
        <td>6.27</td>
    </tr>
    <tr>
        <td>143</td>
        <td>rs</td>
        <td>Russia</td>
        <td>17098242</td>
        <td>16377742</td>
        <td>720500</td>
        <td>142423773</td>
        <td>0.04</td>
        <td>11.6</td>
        <td>13.69</td>
        <td>1.69</td>
    </tr>
    <tr>
        <td>153</td>
        <td>ri</td>
        <td>Serbia</td>
        <td>77474</td>
        <td>77474</td>
        <td>0</td>
        <td>7176794</td>
        <td>0.46</td>
        <td>9.08</td>
        <td>13.66</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>16</td>
        <td>bo</td>
        <td>Belarus</td>
        <td>207600</td>
        <td>202900</td>
        <td>4700</td>
        <td>9589689</td>
        <td>0.2</td>
        <td>10.7</td>
        <td>13.36</td>
        <td>0.7</td>
    </tr>
    <tr>
        <td>75</td>
        <td>hu</td>
        <td>Hungary</td>
        <td>93028</td>
        <td>89608</td>
        <td>3420</td>
        <td>9897541</td>
        <td>0.22</td>
        <td>9.16</td>
        <td>12.73</td>
        <td>1.33</td>
    </tr>
    <tr>
        <td>116</td>
        <td>md</td>
        <td>Moldova</td>
        <td>33851</td>
        <td>32891</td>
        <td>960</td>
        <td>3546847</td>
        <td>1.03</td>
        <td>12.0</td>
        <td>12.59</td>
        <td>9.67</td>
    </tr>
    <tr>
        <td>57</td>
        <td>en</td>
        <td>Estonia</td>
        <td>45228</td>
        <td>42388</td>
        <td>2840</td>
        <td>1265420</td>
        <td>0.55</td>
        <td>10.51</td>
        <td>12.4</td>
        <td>3.6</td>
    </tr>
    <tr>
        <td>44</td>
        <td>hr</td>
        <td>Croatia</td>
        <td>56594</td>
        <td>55974</td>
        <td>620</td>
        <td>4464844</td>
        <td>0.13</td>
        <td>9.45</td>
        <td>12.18</td>
        <td>1.39</td>
    </tr>
    <tr>
        <td>142</td>
        <td>ro</td>
        <td>Romania</td>
        <td>238391</td>
        <td>229891</td>
        <td>8500</td>
        <td>21666350</td>
        <td>0.3</td>
        <td>9.14</td>
        <td>11.9</td>
        <td>0.24</td>
    </tr>
    <tr>
        <td>65</td>
        <td>gm</td>
        <td>Germany</td>
        <td>357022</td>
        <td>348672</td>
        <td>8350</td>
        <td>80854408</td>
        <td>0.17</td>
        <td>8.47</td>
        <td>11.42</td>
        <td>1.24</td>
    </tr>
    <tr>
        <td>158</td>
        <td>si</td>
        <td>Slovenia</td>
        <td>20273</td>
        <td>20151</td>
        <td>122</td>
        <td>1983412</td>
        <td>0.26</td>
        <td>8.42</td>
        <td>11.37</td>
        <td>0.37</td>
    </tr>
    <tr>
        <td>67</td>
        <td>gr</td>
        <td>Greece</td>
        <td>131957</td>
        <td>130647</td>
        <td>1310</td>
        <td>10775643</td>
        <td>0.01</td>
        <td>8.66</td>
        <td>11.09</td>
        <td>2.32</td>
    </tr>
    <tr>
        <td>140</td>
        <td>po</td>
        <td>Portugal</td>
        <td>92090</td>
        <td>91470</td>
        <td>620</td>
        <td>10825309</td>
        <td>0.09</td>
        <td>9.27</td>
        <td>11.02</td>
        <td>2.67</td>
    </tr>
    <tr>
        <td>47</td>
        <td>ez</td>
        <td>Czech Republic</td>
        <td>78867</td>
        <td>77247</td>
        <td>1620</td>
        <td>10644842</td>
        <td>0.16</td>
        <td>9.63</td>
        <td>10.34</td>
        <td>2.33</td>
    </tr>
    <tr>
        <td>83</td>
        <td>it</td>
        <td>Italy</td>
        <td>301340</td>
        <td>294140</td>
        <td>7200</td>
        <td>61855120</td>
        <td>0.27</td>
        <td>8.74</td>
        <td>10.19</td>
        <td>4.1</td>
    </tr>
    <tr>
        <td>139</td>
        <td>pl</td>
        <td>Poland</td>
        <td>312685</td>
        <td>304255</td>
        <td>8430</td>
        <td>38562189</td>
        <td>0.09</td>
        <td>9.74</td>
        <td>10.19</td>
        <td>0.46</td>
    </tr>
    <tr>
        <td>22</td>
        <td>bk</td>
        <td>Bosnia and Herzegovina</td>
        <td>51197</td>
        <td>51187</td>
        <td>10</td>
        <td>3867055</td>
        <td>0.13</td>
        <td>8.87</td>
        <td>9.75</td>
        <td>0.38</td>
    </tr>
    <tr>
        <td>214</td>
        <td>sb</td>
        <td>Saint Pierre and Miquelon</td>
        <td>242</td>
        <td>242</td>
        <td>0</td>
        <td>5657</td>
        <td>1.08</td>
        <td>7.42</td>
        <td>9.72</td>
        <td>8.49</td>
    </tr>
    <tr>
        <td>85</td>
        <td>ja</td>
        <td>Japan</td>
        <td>377915</td>
        <td>364485</td>
        <td>13430</td>
        <td>126919659</td>
        <td>0.16</td>
        <td>7.93</td>
        <td>9.51</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>10</td>
        <td>au</td>
        <td>Austria</td>
        <td>83871</td>
        <td>82445</td>
        <td>1426</td>
        <td>8665550</td>
        <td>0.55</td>
        <td>9.41</td>
        <td>9.42</td>
        <td>5.56</td>
    </tr>
    <tr>
        <td>117</td>
        <td>mn</td>
        <td>Monaco</td>
        <td>2</td>
        <td>2</td>
        <td>0</td>
        <td>30535</td>
        <td>0.12</td>
        <td>6.65</td>
        <td>9.24</td>
        <td>3.83</td>
    </tr>
</table>



Here are the countries that have higher death rate than birth rate. Ukraine is the country with the highest death rate - around 15 deaths annually per 1000 people, while Quatar is the country with the lowest death rate - around 2 deaths annually per 1000 people.

To finish, let's identify which countries have the highest `population/area` ratio


```sql
%%sql 
select name, AVG(population)/AVG(area) as population_area_ratio
from facts
group by name
order by AVG(population)/AVG(area) DESC
```

    Done.
    




<table>
    <tr>
        <th>name</th>
        <th>population_area_ratio</th>
    </tr>
    <tr>
        <td>Macau</td>
        <td>21168.964285714286</td>
    </tr>
    <tr>
        <td>Monaco</td>
        <td>15267.5</td>
    </tr>
    <tr>
        <td>Singapore</td>
        <td>8141.279770444763</td>
    </tr>
    <tr>
        <td>Hong Kong</td>
        <td>6445.041516245487</td>
    </tr>
    <tr>
        <td>Gaza Strip</td>
        <td>5191.819444444444</td>
    </tr>
    <tr>
        <td>Gibraltar</td>
        <td>4876.333333333333</td>
    </tr>
    <tr>
        <td>Bahrain</td>
        <td>1771.8592105263158</td>
    </tr>
    <tr>
        <td>Maldives</td>
        <td>1319.6409395973155</td>
    </tr>
    <tr>
        <td>Malta</td>
        <td>1310.01582278481</td>
    </tr>
    <tr>
        <td>Bermuda</td>
        <td>1299.9259259259259</td>
    </tr>
    <tr>
        <td>Sint Maarten</td>
        <td>1167.3235294117646</td>
    </tr>
    <tr>
        <td>Bangladesh</td>
        <td>1138.0691432035565</td>
    </tr>
    <tr>
        <td>Guernsey</td>
        <td>847.1794871794872</td>
    </tr>
    <tr>
        <td>Jersey</td>
        <td>838.7413793103449</td>
    </tr>
    <tr>
        <td>Barbados</td>
        <td>675.8232558139534</td>
    </tr>
    <tr>
        <td>Mauritius</td>
        <td>656.7779411764706</td>
    </tr>
    <tr>
        <td>Taiwan</td>
        <td>650.7817120622568</td>
    </tr>
    <tr>
        <td>Aruba</td>
        <td>623.1222222222223</td>
    </tr>
    <tr>
        <td>Lebanon</td>
        <td>594.6827884615385</td>
    </tr>
    <tr>
        <td>Saint Martin</td>
        <td>588.0370370370371</td>
    </tr>
    <tr>
        <td>San Marino</td>
        <td>541.311475409836</td>
    </tr>
    <tr>
        <td>Korea, South</td>
        <td>492.53104693140796</td>
    </tr>
    <tr>
        <td>Rwanda</td>
        <td>480.74010934771053</td>
    </tr>
    <tr>
        <td>West Bank</td>
        <td>475.3184300341297</td>
    </tr>
    <tr>
        <td>Nauru</td>
        <td>454.2857142857143</td>
    </tr>
    <tr>
        <td>Tuvalu</td>
        <td>418.03846153846155</td>
    </tr>
    <tr>
        <td>Netherlands</td>
        <td>407.96052283176465</td>
    </tr>
    <tr>
        <td>Marshall Islands</td>
        <td>398.8453038674033</td>
    </tr>
    <tr>
        <td>Israel</td>
        <td>387.5452094366875</td>
    </tr>
    <tr>
        <td>Burundi</td>
        <td>385.99626302551206</td>
    </tr>
    <tr>
        <td>India</td>
        <td>380.7713541630226</td>
    </tr>
    <tr>
        <td>Belgium</td>
        <td>370.9372707023061</td>
    </tr>
    <tr>
        <td>Haiti</td>
        <td>364.325009009009</td>
    </tr>
    <tr>
        <td>Comoros</td>
        <td>349.42774049217</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>336.6612533333333</td>
    </tr>
    <tr>
        <td>Sri Lanka</td>
        <td>336.12998018594726</td>
    </tr>
    <tr>
        <td>Japan</td>
        <td>335.8418136353413</td>
    </tr>
    <tr>
        <td>Curacao</td>
        <td>334.2477477477477</td>
    </tr>
    <tr>
        <td>Grenada</td>
        <td>321.7848837209302</td>
    </tr>
    <tr>
        <td>Guam</td>
        <td>297.39889705882354</td>
    </tr>
    <tr>
        <td>El Salvador</td>
        <td>291.8753861508483</td>
    </tr>
    <tr>
        <td>Vietnam</td>
        <td>284.8610700160019</td>
    </tr>
    <tr>
        <td>American Samoa</td>
        <td>273.08040201005025</td>
    </tr>
    <tr>
        <td>Jamaica</td>
        <td>268.4205258848148</td>
    </tr>
    <tr>
        <td>Saint Lucia</td>
        <td>266.10714285714283</td>
    </tr>
    <tr>
        <td>Saint Vincent and the Grenadines</td>
        <td>263.82262210796915</td>
    </tr>
    <tr>
        <td>United Kingdom</td>
        <td>263.0771396904889</td>
    </tr>
    <tr>
        <td>Puerto Rico</td>
        <td>260.92067290261764</td>
    </tr>
    <tr>
        <td>Pakistan</td>
        <td>250.07800199724906</td>
    </tr>
    <tr>
        <td>Trinidad and Tobago</td>
        <td>238.3703198127925</td>
    </tr>
    <tr>
        <td>Liechtenstein</td>
        <td>235.15</td>
    </tr>
    <tr>
        <td>Germany</td>
        <td>226.4689795026637</td>
    </tr>
    <tr>
        <td>British Virgin Islands</td>
        <td>221.5496688741722</td>
    </tr>
    <tr>
        <td>Luxembourg</td>
        <td>220.51508120649652</td>
    </tr>
    <tr>
        <td>Dominican Republic</td>
        <td>215.3021573864804</td>
    </tr>
    <tr>
        <td>Nepal</td>
        <td>214.3707747603291</td>
    </tr>
    <tr>
        <td>Cayman Islands</td>
        <td>212.46969696969697</td>
    </tr>
    <tr>
        <td>Antigua and Barbuda</td>
        <td>209.13122171945702</td>
    </tr>
    <tr>
        <td>Korea, North</td>
        <td>207.26414076888616</td>
    </tr>
    <tr>
        <td>Italy</td>
        <td>205.26687462666754</td>
    </tr>
    <tr>
        <td>Seychelles</td>
        <td>203.14285714285714</td>
    </tr>
    <tr>
        <td>Sao Tome and Principe</td>
        <td>201.25103734439833</td>
    </tr>
    <tr>
        <td>Saint Kitts and Nevis</td>
        <td>198.98850574712642</td>
    </tr>
    <tr>
        <td>Switzerland</td>
        <td>196.76405746541658</td>
    </tr>
    <tr>
        <td>Nigeria</td>
        <td>196.5450805830035</td>
    </tr>
    <tr>
        <td>Qatar</td>
        <td>189.43699292249266</td>
    </tr>
    <tr>
        <td>Andorra</td>
        <td>182.86324786324786</td>
    </tr>
    <tr>
        <td>Anguilla</td>
        <td>180.41758241758242</td>
    </tr>
    <tr>
        <td>Gambia, The</td>
        <td>174.13353982300885</td>
    </tr>
    <tr>
        <td>Kosovo</td>
        <td>171.8545972260494</td>
    </tr>
    <tr>
        <td>Kuwait</td>
        <td>156.50095409136827</td>
    </tr>
    <tr>
        <td>Uganda</td>
        <td>153.92487906471177</td>
    </tr>
    <tr>
        <td>Isle of Man</td>
        <td>153.0506993006993</td>
    </tr>
    <tr>
        <td>Malawi</td>
        <td>151.62129063839842</td>
    </tr>
    <tr>
        <td>Micronesia, Federated States of</td>
        <td>149.8803418803419</td>
    </tr>
    <tr>
        <td>Tonga</td>
        <td>142.57161981258366</td>
    </tr>
    <tr>
        <td>China</td>
        <td>142.4915168970174</td>
    </tr>
    <tr>
        <td>Guatemala</td>
        <td>137.01107549890256</td>
    </tr>
    <tr>
        <td>Cabo Verde</td>
        <td>135.3813538308951</td>
    </tr>
    <tr>
        <td>Czech Republic</td>
        <td>134.97206689743493</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>134.41029125224657</td>
    </tr>
    <tr>
        <td>Togo</td>
        <td>132.99846790525666</td>
    </tr>
    <tr>
        <td>Thailand</td>
        <td>132.47662340193327</td>
    </tr>
    <tr>
        <td>Kiribati</td>
        <td>130.34648581997533</td>
    </tr>
    <tr>
        <td>Denmark</td>
        <td>129.51926022184063</td>
    </tr>
    <tr>
        <td>Cyprus</td>
        <td>128.54794076316074</td>
    </tr>
    <tr>
        <td>Akrotiri</td>
        <td>127.64227642276423</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>123.3259958104802</td>
    </tr>
    <tr>
        <td>Dhekelia</td>
        <td>120.76923076923077</td>
    </tr>
    <tr>
        <td>European Union</td>
        <td>118.83823161491145</td>
    </tr>
    <tr>
        <td>Portugal</td>
        <td>117.5514062330329</td>
    </tr>
    <tr>
        <td>Azerbaijan</td>
        <td>112.94203233256351</td>
    </tr>
    <tr>
        <td>Northern Mariana Islands</td>
        <td>112.8103448275862</td>
    </tr>
    <tr>
        <td>Tokelau</td>
        <td>111.41666666666667</td>
    </tr>
    <tr>
        <td>Slovakia</td>
        <td>111.04368308351178</td>
    </tr>
    <tr>
        <td>Ghana</td>
        <td>110.37319364616216</td>
    </tr>
    <tr>
        <td>Wallis and Futuna</td>
        <td>109.95070422535211</td>
    </tr>
    <tr>
        <td>Hungary</td>
        <td>106.3931396998753</td>
    </tr>
    <tr>
        <td>Albania</td>
        <td>105.37352163628775</td>
    </tr>
    <tr>
        <td>Moldova</td>
        <td>104.77820448435793</td>
    </tr>
    <tr>
        <td>France</td>
        <td>103.37630106197412</td>
    </tr>
    <tr>
        <td>Austria</td>
        <td>103.31997949231558</td>
    </tr>
    <tr>
        <td>Armenia</td>
        <td>102.75970816662745</td>
    </tr>
    <tr>
        <td>Turkey</td>
        <td>101.35033220089795</td>
    </tr>
    <tr>
        <td>Cuba</td>
        <td>99.50778459318059</td>
    </tr>
    <tr>
        <td>Dominica</td>
        <td>98.01198402130493</td>
    </tr>
    <tr>
        <td>Slovenia</td>
        <td>97.8351501997731</td>
    </tr>
    <tr>
        <td>Spain</td>
        <td>95.26907810119319</td>
    </tr>
    <tr>
        <td>Costa Rica</td>
        <td>94.21025440313112</td>
    </tr>
    <tr>
        <td>Benin</td>
        <td>92.77625153167232</td>
    </tr>
    <tr>
        <td>Serbia</td>
        <td>92.63487105351473</td>
    </tr>
    <tr>
        <td>Malaysia</td>
        <td>92.50909664177634</td>
    </tr>
    <tr>
        <td>Syria</td>
        <td>92.1527918781726</td>
    </tr>
    <tr>
        <td>Romania</td>
        <td>90.88577169440121</td>
    </tr>
    <tr>
        <td>Jordan</td>
        <td>90.85943900964831</td>
    </tr>
    <tr>
        <td>Ethiopia</td>
        <td>90.0713746264602</td>
    </tr>
    <tr>
        <td>Egypt</td>
        <td>88.3592750511758</td>
    </tr>
    <tr>
        <td>Cambodia</td>
        <td>86.77192808020548</td>
    </tr>
    <tr>
        <td>Iraq</td>
        <td>84.5419388250969</td>
    </tr>
    <tr>
        <td>Burma</td>
        <td>83.24273919636761</td>
    </tr>
    <tr>
        <td>Timor-Leste</td>
        <td>82.76966518757564</td>
    </tr>
    <tr>
        <td>Swaziland</td>
        <td>82.67755125547109</td>
    </tr>
    <tr>
        <td>Sierra Leone</td>
        <td>81.9500696961249</td>
    </tr>
    <tr>
        <td>Greece</td>
        <td>81.66026053941815</td>
    </tr>
    <tr>
        <td>Macedonia</td>
        <td>81.51577023295609</td>
    </tr>
    <tr>
        <td>Kenya</td>
        <td>79.1314823206695</td>
    </tr>
    <tr>
        <td>Croatia</td>
        <td>78.89253277732622</td>
    </tr>
    <tr>
        <td>Honduras</td>
        <td>78.03258988312963</td>
    </tr>
    <tr>
        <td>Bosnia and Herzegovina</td>
        <td>75.53284372131179</td>
    </tr>
    <tr>
        <td>Morocco</td>
        <td>74.6225484268279</td>
    </tr>
    <tr>
        <td>Brunei</td>
        <td>74.5266261925412</td>
    </tr>
    <tr>
        <td>Ukraine</td>
        <td>73.61357136939773</td>
    </tr>
    <tr>
        <td>Cote d&#x27;Ivoire</td>
        <td>72.2417827781792</td>
    </tr>
    <tr>
        <td>Senegal</td>
        <td>71.0435741808237</td>
    </tr>
    <tr>
        <td>Georgia</td>
        <td>70.74929698708752</td>
    </tr>
    <tr>
        <td>Samoa</td>
        <td>69.8597668668315</td>
    </tr>
    <tr>
        <td>Ireland</td>
        <td>69.61855904828312</td>
    </tr>
    <tr>
        <td>United Arab Emirates</td>
        <td>69.13588516746411</td>
    </tr>
    <tr>
        <td>Burkina Faso</td>
        <td>69.04334792122539</td>
    </tr>
    <tr>
        <td>French Polynesia</td>
        <td>67.84329253659708</td>
    </tr>
    <tr>
        <td>Tunisia</td>
        <td>67.46057698184707</td>
    </tr>
    <tr>
        <td>Uzbekistan</td>
        <td>65.26585158694681</td>
    </tr>
    <tr>
        <td>Bulgaria</td>
        <td>64.81744063348334</td>
    </tr>
    <tr>
        <td>Lesotho</td>
        <td>64.16409158293527</td>
    </tr>
    <tr>
        <td>Mexico</td>
        <td>61.972285841552655</td>
    </tr>
    <tr>
        <td>Norfolk Island</td>
        <td>61.388888888888886</td>
    </tr>
    <tr>
        <td>Tajikistan</td>
        <td>56.84911866759195</td>
    </tr>
    <tr>
        <td>Ecuador</td>
        <td>55.96113710982822</td>
    </tr>
    <tr>
        <td>Eritrea</td>
        <td>55.507559523809526</td>
    </tr>
    <tr>
        <td>Virgin Islands</td>
        <td>54.227225130890055</td>
    </tr>
    <tr>
        <td>Tanzania</td>
        <td>53.88565607516098</td>
    </tr>
    <tr>
        <td>Turks and Caicos Islands</td>
        <td>53.037974683544306</td>
    </tr>
    <tr>
        <td>Montserrat</td>
        <td>51.38235294117647</td>
    </tr>
    <tr>
        <td>Yemen</td>
        <td>50.64192716225226</td>
    </tr>
    <tr>
        <td>Cameroon</td>
        <td>49.93104913343429</td>
    </tr>
    <tr>
        <td>Afghanistan</td>
        <td>49.92769728470018</td>
    </tr>
    <tr>
        <td>Fiji</td>
        <td>49.764091058334245</td>
    </tr>
    <tr>
        <td>Iran</td>
        <td>49.64477504178814</td>
    </tr>
    <tr>
        <td>Panama</td>
        <td>48.48878281622912</td>
    </tr>
    <tr>
        <td>Guinea</td>
        <td>47.91469024676946</td>
    </tr>
    <tr>
        <td>Guinea-Bissau</td>
        <td>47.78325259515571</td>
    </tr>
    <tr>
        <td>Montenegro</td>
        <td>46.84860990443093</td>
    </tr>
    <tr>
        <td>Palau</td>
        <td>46.328976034858385</td>
    </tr>
    <tr>
        <td>Belarus</td>
        <td>46.193106936416186</td>
    </tr>
    <tr>
        <td>Nicaragua</td>
        <td>45.31626140983355</td>
    </tr>
    <tr>
        <td>Lithuania</td>
        <td>44.172021439509955</td>
    </tr>
    <tr>
        <td>South Africa</td>
        <td>44.02920457062235</td>
    </tr>
    <tr>
        <td>Cocos (Keeling) Islands</td>
        <td>42.57142857142857</td>
    </tr>
    <tr>
        <td>Cook Islands</td>
        <td>41.686440677966104</td>
    </tr>
    <tr>
        <td>Colombia</td>
        <td>41.036366350282286</td>
    </tr>
    <tr>
        <td>Madagascar</td>
        <td>40.56391461584455</td>
    </tr>
    <tr>
        <td>Liberia</td>
        <td>37.6735536819043</td>
    </tr>
    <tr>
        <td>Zimbabwe</td>
        <td>36.41531949523617</td>
    </tr>
    <tr>
        <td>Faroe Islands</td>
        <td>36.034458004307254</td>
    </tr>
    <tr>
        <td>Djibouti</td>
        <td>35.703620689655175</td>
    </tr>
    <tr>
        <td>Congo, Democratic Republic of the</td>
        <td>33.85072187740153</td>
    </tr>
    <tr>
        <td>United States</td>
        <td>32.70372369087204</td>
    </tr>
    <tr>
        <td>Venezuela</td>
        <td>32.09852530014802</td>
    </tr>
    <tr>
        <td>Mozambique</td>
        <td>31.65342265255573</td>
    </tr>
    <tr>
        <td>Latvia</td>
        <td>30.759185000541887</td>
    </tr>
    <tr>
        <td>Laos</td>
        <td>29.187263513513514</td>
    </tr>
    <tr>
        <td>Kyrgyzstan</td>
        <td>28.331636250881466</td>
    </tr>
    <tr>
        <td>Estonia</td>
        <td>27.978685769877067</td>
    </tr>
    <tr>
        <td>Equatorial Guinea</td>
        <td>26.40700866279277</td>
    </tr>
    <tr>
        <td>Saint Helena, Ascension, and Tristan da Cunha</td>
        <td>25.308441558441558</td>
    </tr>
    <tr>
        <td>Brazil</td>
        <td>23.98606491250938</td>
    </tr>
    <tr>
        <td>Peru</td>
        <td>23.688624324627146</td>
    </tr>
    <tr>
        <td>Bahamas, The</td>
        <td>23.385951008645534</td>
    </tr>
    <tr>
        <td>Saint Pierre and Miquelon</td>
        <td>23.376033057851238</td>
    </tr>
    <tr>
        <td>Chile</td>
        <td>23.15594985861696</td>
    </tr>
    <tr>
        <td>Vanuatu</td>
        <td>22.336861104274345</td>
    </tr>
    <tr>
        <td>Sweden</td>
        <td>21.7670993459843</td>
    </tr>
    <tr>
        <td>Solomon Islands</td>
        <td>21.54170127353267</td>
    </tr>
    <tr>
        <td>Zambia</td>
        <td>20.01847683685482</td>
    </tr>
    <tr>
        <td>Sudan</td>
        <td>19.397885235650698</td>
    </tr>
    <tr>
        <td>Bhutan</td>
        <td>19.3238266395791</td>
    </tr>
    <tr>
        <td>Uruguay</td>
        <td>18.96486110717022</td>
    </tr>
    <tr>
        <td>South Sudan</td>
        <td>18.690622337346294</td>
    </tr>
    <tr>
        <td>Paraguay</td>
        <td>16.67667768074896</td>
    </tr>
    <tr>
        <td>Somalia</td>
        <td>16.649044862676956</td>
    </tr>
    <tr>
        <td>Algeria</td>
        <td>16.602210735760103</td>
    </tr>
    <tr>
        <td>New Zealand</td>
        <td>16.57910798998917</td>
    </tr>
    <tr>
        <td>Finland</td>
        <td>16.196962841384615</td>
    </tr>
    <tr>
        <td>Norway</td>
        <td>16.082942662491277</td>
    </tr>
    <tr>
        <td>Angola</td>
        <td>15.741840859870058</td>
    </tr>
    <tr>
        <td>Argentina</td>
        <td>15.620732988059272</td>
    </tr>
    <tr>
        <td>Belize</td>
        <td>15.125359226682923</td>
    </tr>
    <tr>
        <td>New Caledonia</td>
        <td>14.622611036339165</td>
    </tr>
    <tr>
        <td>Papua New Guinea</td>
        <td>14.416275602800104</td>
    </tr>
    <tr>
        <td>Congo, Republic of the</td>
        <td>13.903792397660819</td>
    </tr>
    <tr>
        <td>Mali</td>
        <td>13.671702446072866</td>
    </tr>
    <tr>
        <td>Saudi Arabia</td>
        <td>12.909915383148268</td>
    </tr>
    <tr>
        <td>Christmas Island</td>
        <td>11.333333333333334</td>
    </tr>
    <tr>
        <td>Turkmenistan</td>
        <td>10.717930751895103</td>
    </tr>
    <tr>
        <td>Oman</td>
        <td>10.620148626817448</td>
    </tr>
    <tr>
        <td>Bolivia</td>
        <td>9.831666486130745</td>
    </tr>
    <tr>
        <td>Central African Republic</td>
        <td>8.654377961552784</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>8.329731968935754</td>
    </tr>
    <tr>
        <td>Kazakhstan</td>
        <td>6.6634085654519435</td>
    </tr>
    <tr>
        <td>Gabon</td>
        <td>6.371110372216224</td>
    </tr>
    <tr>
        <td>Niue</td>
        <td>4.576923076923077</td>
    </tr>
    <tr>
        <td>Botswana</td>
        <td>3.752116961476974</td>
    </tr>
    <tr>
        <td>Libya</td>
        <td>3.644006956363595</td>
    </tr>
    <tr>
        <td>Suriname</td>
        <td>3.5382309852276888</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>3.515372666297434</td>
    </tr>
    <tr>
        <td>Mauritania</td>
        <td>3.4895721354419327</td>
    </tr>
    <tr>
        <td>Guyana</td>
        <td>3.4201303443752353</td>
    </tr>
    <tr>
        <td>Iceland</td>
        <td>3.222504854368932</td>
    </tr>
    <tr>
        <td>Australia</td>
        <td>2.9389442490976876</td>
    </tr>
    <tr>
        <td>Namibia</td>
        <td>2.6838875058838374</td>
    </tr>
    <tr>
        <td>Western Sahara</td>
        <td>2.146112781954887</td>
    </tr>
    <tr>
        <td>Mongolia</td>
        <td>1.9134821202519505</td>
    </tr>
    <tr>
        <td>Pitcairn Islands</td>
        <td>1.0212765957446808</td>
    </tr>
    <tr>
        <td>Falkland Islands (Islas Malvinas)</td>
        <td>0.2761028505709357</td>
    </tr>
    <tr>
        <td>Svalbard</td>
        <td>0.030171649609154645</td>
    </tr>
    <tr>
        <td>Greenland</td>
        <td>0.026653143042335344</td>
    </tr>
    <tr>
        <td>Antarctica</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Arctic Ocean</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Ashmore and Cartier Islands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Atlantic Ocean</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Bouvet Island</td>
        <td>None</td>
    </tr>
    <tr>
        <td>British Indian Ocean Territory</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Chad</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Clipperton Island</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Coral Sea Islands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>French Southern and Antarctic Lands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Heard Island and McDonald Islands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Holy See (Vatican City)</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Indian Ocean</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Jan Mayen</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Navassa Island</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Niger</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Pacific Ocean</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Paracel Islands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Saint Barthelemy</td>
        <td>None</td>
    </tr>
    <tr>
        <td>South Georgia and South Sandwich Islands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Southern Ocean</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Spratly Islands</td>
        <td>None</td>
    </tr>
    <tr>
        <td>United States Pacific Island Wildlife Refuges</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Wake Island</td>
        <td>None</td>
    </tr>
    <tr>
        <td>World</td>
        <td>None</td>
    </tr>
</table>


