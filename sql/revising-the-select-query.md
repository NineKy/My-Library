---
description: >-
  https://www.hackerrank.com/challenges/revising-the-select-query/problem?isFullScreen=true
---

# Revising the Select Query

![CITY.jpg](https://s3.amazonaws.com/hr-challenge-images/8137/1449729804-f21d187d0f-CITY.jpg)

Query all columns for all American cities in the **CITY** table with populations larger than `100000`. The **CountryCode** for America is `USA`.&#x20;

```sql
select * from CITY where population>100000 AND countrycode='USA';
```



Query the **NAME** field for all American cities in the **CITY** table with populations larger than `120000`. The _CountryCode_ for America is `USA`.

```sql
SELECT name FROM city WHERE population>120000 AND countrycode='USA';
```



Query all columns (attributes) for every row in the **CITY** table.

```sql
SELECT * from CITY;
```



Query all columns for a city in **CITY** with the _ID_ `1661`.

```sql
SELECT * FROM CITY WHERE ID='1661';
```



Query the names of all the Japanese cities in the **CITY** table. The **COUNTRYCODE** for Japan is `JPN`.&#x20;

```sql
SELECT name FROM CITY where countrycode='JPN';
```





Query a list of **CITY** and **STATE** from the **STATION** table. \
The **STATION** table is described as follows: \
![](https://s3.amazonaws.com/hr-challenge-images/9336/1449345840-5f0a551030-Station.jpg)

where **LAT\_N** is the northern latitude and **LONG\_W** is the western longitude.

```sql
SELECT city, state FROM station;
```



Query a list of **CITY** names from **STATION** for cities that have an even **ID** number. Print the results in any order, but exclude duplicates from the answer.&#x20;

```sql
SELECT DISTINCT city FROM station WHERE MOD(ID, 2)=0;
```



Find the difference between the total number of **CITY** entries in the table and the number of distinct **CITY** entries in the table. \
The **STATION** table is described as follows:

![](https://s3.amazonaws.com/hr-challenge-images/9336/1449345840-5f0a551030-Station.jpg)

where **LAT\_N** is the northern latitude and **LONG\_W** is the western longitude.

For example, if there are three records in the table with **CITY** values 'New York', 'New York', 'Bengalaru', there are 2 different city names: 'New York' and 'Bengalaru'. The query returns , because .

```sql
SELECT COUNT(city) - COUNT(DISTINCT city) FROM station;
```



