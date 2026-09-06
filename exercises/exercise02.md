# Exercise 02: World Database – Joins, Grouping, and Data Quality

- Name:
- Course: Database for Analytics
- Module: 2
- Database Used: World Database (PostgreSQL)

---

## Instructions

- Answer each question below using SQL executed against the **World database**.
- All SQL commands **must be run by you**.
- For each SQL-based question:
  - Include the SQL command in a fenced code block
  - Include a **screenshot** showing the command and its results
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

When importing records from `worldPGSQL.sql`, **how many cities were imported**?

### Answer

_Write the number of cities imported._

### Screenshot

_Show evidence of how you determined this (for example, a COUNT query)._

```sql
select count(name)
from city
```

![Q1 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/4e98df64d9c3aa59d908bd9f668c96c0c1dbd9f4/exercises/screenshots/mod2/q1_mod2.png)

---

## Question 2

Using the World database, write the SQL command to
**display each country name**
along with the **name of each language spoken in that country**.

### SQL

```sql
select c.name, l.language
from country as c
left join countrylanguage as l on l.countrycode = c.code
```

### Screenshot

![Q2 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/4e98df64d9c3aa59d908bd9f668c96c0c1dbd9f4/exercises/screenshots/mod2/q2_mod2.png)

---

## Question 3

Using the World database, write the SQL command
to **display each country name** along with the name
of each **official language spoken in that country**.

### SQL

```sql
select c.name, l.language
from country as c
left join countrylanguage as l on l.countrycode = c.code
where isOfficial = 'T'
```

### Screenshot

![Q3 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e9d47e85ec87de20a8406c08694643e6540b8b7b/exercises/screenshots/mod2/q3_mod2.png)

---

## Question 4

Consider the following two SQL statements:

```sql
SELECT *
FROM country, countrylanguage
WHERE country.code = countrylanguage.countrycode;
```

```sql
SELECT *
FROM country
LEFT OUTER JOIN countrylanguage
ON country.code = countrylanguage.countrycode;
```

**In your own words**, describe what data the
**second query returns that the first query does not**.

### Answer

 The second query will show you all the countries from the country table, even if they do not have a corresponding country code in the countryLangauge table, whereas query 1 will only show you countries that have corresponding country codes in both tables. Query 2 will show countries such as Antarctica and Bouvet island though they do not have country codes in the countryLanguages table. Query 1 omits those countries. 
---

## Question 5

Using the World database, write the SQL command
to **list all different forms of government** found in the data.
Do **not** repeat any form of government more than once.

### SQL

```sql
select distinct governmentform
from country
```

### Screenshot

![Q5 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e9d47e85ec87de20a8406c08694643e6540b8b7b/exercises/screenshots/mod2/q5_mod2.png)

---

## Question 6

Using the World database, write the SQL command
to **list all names of cities and countries in one column**.
Label the column **"City or Country Name"**.

### SQL

```sql
(
  select name as "City or Country"
  from city
) union
(
  select name
  from country
)
```

### Screenshot

![Q6 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e9d47e85ec87de20a8406c08694643e6540b8b7b/exercises/screenshots/mod2/q6_mod2.png)

---

## Question 7

Using the World database, write the SQL command
to **list all countries by name**,
along with the **number of languages spoken in each country**.
Be sure to **sort by country name**.

### SQL

```sql
select c.name,
count(l.language) as numOfLanguages
from country as c
left join countrylanguage as l on l.countrycode = c.code

group by c.name
order by c.name asc
```

### Screenshot

![Q7 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e9d47e85ec87de20a8406c08694643e6540b8b7b/exercises/screenshots/mod2/q7_mod2.png)

---

## Question 8

Using the World database, write the SQL command
to **list all languages**, along with the
**number of countries where each language is spoken**.
Be sure to **sort by language name**.

### SQL

```sql
select
l.language,
count(c.name) as "Number of Countries"

from country as c
left join countrylanguage as l on l.countrycode = c.code

group by l.language
order by l.language
```

### Screenshot

![Q8 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e9d47e85ec87de20a8406c08694643e6540b8b7b/exercises/screenshots/mod2/q8_mod2.png)

---

## Question 9

Using the World database, write the SQL command
to **list countries that have more than two official languages**,
along with the **number of official languages spoken**.

_Hint: There are 8 such countries in this dataset._

### SQL

```sql
with numberOfLanguages as (
  select c.name as name, count(l.language) as official_languages
  from country as c
  left join countrylanguage as l on l.countrycode = c.code
  where isOfficial = 'T'

)

select name, official_languages
from numberOfLanguages
where official_languages > 2 
```

### Screenshot

![Q9 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e9d47e85ec87de20a8406c08694643e6540b8b7b/exercises/screenshots/mod2/q9_mod2.png)

---

## Question 10

Using the World database, write the SQL command to
**find cities where the district value is missing**.

Hint: Use `LIKE` and the dash (`-`)
since some rows use that instead of actual data.

### SQL

```sql
select name
from city

where district like '%–%'
```

### Screenshot

![Q10 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/82beda76388930d7ca5c2a39e4f085ff12591ad9/exercises/screenshots/mod2/q10_2_mod2.png)

---

## Question 11

Using the World database, write the SQL command to
**calculate the percentage of cities with missing district values**.

_Hint: The result should be approximately 0.4%._

### SQL

```sql
with numOfMissingDistricts as (
  select count(name) as districtCount
  from city
  where district like '%–%'
),
numOfCities as (
  select count(name) as cityCount
  from city 
)

select cast(districtCount as decimal(9,2)) / cast(cityCount as decimal(9,2)) * 100 as percent_of_missing_districts
from numOfMissingDistricts, numOfCities
```

### Screenshot

![Q11 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/82beda76388930d7ca5c2a39e4f085ff12591ad9/exercises/screenshots/mod2/q11_mod2.png)
