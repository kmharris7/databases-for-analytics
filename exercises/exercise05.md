# Exercise 05: SQLDA Database - Dates, Data Quality, Arrays, and JSON

- Name: Kalei H
- Course: Database for Analytics
- Module: 5
- Database Used: `sqlda` (Sample Datasets)
- Tools Used: PostgreSQL (pgAdmin or psql)

---

## Instructions

- Use the **sqlda** database from the "Loading the Sample Datasets" instructions.
- For each SQL task:
  - Include your SQL in a fenced code block
  - Execute it and include a **screenshot** showing the query and results
- Store screenshots in the `screenshots/` folder and embed them below each answer.
- For explanation questions:
  - Write your answer in complete sentences
  - Include a screenshot if requested

---

## Question 1

Using the `sqlda` database, write the SQL needed
to show a **list of years** that emails were sent.

Your results should list years like this (order matters):

```text
year
2011
2013
2014
2015
2016
2017
2018
2019
```

### SQL

```sql
select distinct extract(year from sent_date) as year

from emails

order by year asc
```

### Screenshot

![Q1 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/17ff4e1c3e2611ca39ad377aaa1c13e88bc0b0ce/exercises/screenshots/mod5/q1_mod5.png)

---

## Question 2

Using the `sqlda` database, write the SQL needed to
show the **number of messages sent by year**,
ordered by year (as shown in the prompt).

Output should resemble:

```text
count   year
...
```

### SQL

```sql
select count(email_id) as count,extract(year from sent_date) as year

from emails

group by year 
order by year asc
```

### Screenshot

![Q2 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/17ff4e1c3e2611ca39ad377aaa1c13e88bc0b0ce/exercises/screenshots/mod5/q2_mod5.png)

---

## Question 3

Using the `sqlda` database, write the SQL needed to show:

- the **sent date**
- the **opened date**
- the **interval** between the two

Only include emails that contain **both** a sent date and an opened date.

### SQL

```sql
select 
sent_date,
opened_date,
opened_date - sent_date as interval_between

from emails

where sent_date is not null 
and opened_date is not null
```

### Screenshot

![Q3 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/17ff4e1c3e2611ca39ad377aaa1c13e88bc0b0ce/exercises/screenshots/mod5/q3_mod5.png)

---

## Question 4

Using the `sqlda` database,
write the SQL needed to
show emails that contain an **opened date BEFORE the sent date**.

### SQL

```sql
select *

from emails 

where sent_date > opened_date
```

### Screenshot

![Q4 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/17ff4e1c3e2611ca39ad377aaa1c13e88bc0b0ce/exercises/screenshots/mod5/q4_mod5.png)

---

## Question 5

Using the `sqlda` database:
there are **over 100 emails**
that contain an opened date **BEFORE** the sent date.

After looking at the data, **why is this the case?**

### Answer

All of the emails that have a opened date before the sent date are at the same time, showing that those dates where rounded up to the same time (15:00) for their specific date. 

### Screenshot (if requested by instructor)

![Q5 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/dc50ec04ae0c38964fc59f21fff59ae143bc8cde/exercises/screenshots/mod5/q5_mod5.png)

---

## Question 6

Using the `sqlda` database, explain in your own words what the following code does:

```sql
CREATE TEMP TABLE customer_points AS (
    SELECT
        customer_id,
        point(longitude, latitude) AS lng_lat_point
    FROM customers
    WHERE longitude IS NOT NULL
    AND latitude IS NOT NULL
);

CREATE TEMP TABLE dealership_points AS (
    SELECT
        dealership_id,
        point(longitude, latitude) AS lng_lat_point
    FROM dealerships
);

CREATE TEMP TABLE customer_dealership_distance AS (
    SELECT
       customer_id,
       dealership_id,
       c.lng_lat_point <@> d.lng_lat_point AS distance
    FROM customer_points c
    CROSS JOIN dealership_points d
);
```

### Answer
The code is using the earthdistance module to find the distance between the a patient's location and a dealership's location. The first part create a table and pulls the longitude and latitude points of each customer from the customer table. Then the another temp table is created pulling the the latitude and longitude of the dealerships from the dealership table. Finally The last table is calculating the distance between a customer's location and every possible dealership location using a cross join and the the <@> function

---

## Question 7

Using the `sqlda` database,
write SQL to display an
**array of salespeople for each dealership**,
sorted by dealership.

For example - dealership 1 is below:

```text
"{""Fidell,Granville"",""Onele,Jereme"",""Sheriff,Lelia"",""McSpirron,Massimiliano"",""Rennick,Nadia"",""Mace,Eveleen"",""Oxteby,Dukie"",""Spong,Marcos"",""Wogden,Quent"",""Duny,Sandye"",""Loraine,Englebert"",""Meere,Ira"",""Gibbens,Cristine"",""Prine,Lyda"",""McCoughan,Sheff"",""Schule,Giselbert"",""McAndie,Eleen"",""Dosedale,Dorie"",""Nafziger,Shay""}"
```

### SQL

```sql
select d.dealership_id,
array_agg(concat(s.last_name, ',', s.first_name))

from dealerships as d
join salespeople as s on s.dealership_id = d.dealership_id

group by d.dealership_id
order by d.dealership_id asc

```

### Screenshot

![Q7 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/dc50ec04ae0c38964fc59f21fff59ae143bc8cde/exercises/screenshots/mod5/q7_mod5.png)

---

## Question 8

Using the `sqlda` database, write SQL to display:

- an **array of salespeople for each dealership**
- the **state** of the dealership
- the **number of salespeople** for the dealership

Sort by **state**.

Reference image:

![05-ExerciseArray](./instructions/05-ExerciseArray.jpg)

### SQL

```sql
select 
d.dealership_id,
d.state,
array_agg(concat(s.last_name, ',' , s.first_name)) as dealer_salespeople,
count(salesperson_id)

from dealerships as d
join salespeople as s on s.dealership_id = d.dealership_id

group by d.dealership_id,d.state
order by d.state asc
```

### Screenshot

![Q8 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/dc50ec04ae0c38964fc59f21fff59ae143bc8cde/exercises/screenshots/mod5/q8_mod5.png)

---

## Question 9

Using the `sqlda` database, write the SQL needed to convert
the **customers** table to **JSON**.

### SQL

```sql
select row_to_json(c)

from customers as c 
```

### Screenshot

![Q9 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/dc50ec04ae0c38964fc59f21fff59ae143bc8cde/exercises/screenshots/mod5/q9_mod5.png)

---

## Question 10

Using the `sqlda` database, write SQL to display:

- an **array of salespeople for each dealership**
- the **state**
- the **number of salespeople**
- sorted by **state**

Then **convert this result to JSON**.

Reference image:

![05-ExerciseArray-1](./instructions/05-ExerciseArray-1.jpg)

### SQL

```sql
with dealership_salespeople as (
	select 
d.dealership_id,
d.state,
array_agg(concat(s.last_name, ',' , s.first_name)) as dealer_salespeople,
count(salesperson_id)

from dealerships as d
join salespeople as s on s.dealership_id = d.dealership_id

group by d.dealership_id,d.state
order by d.state asc
)

select row_to_json(ds) from dealership_salespeople as ds
```

### Screenshot

![Q10 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/dc50ec04ae0c38964fc59f21fff59ae143bc8cde/exercises/screenshots/mod5/q10_mod5.png)
