# Exercise 01: World Database SQL Practice

- Name: Kalei H.
- Course: Database for Analytics
- Module: 1
- Database Used: World Database

---

See:

[MySQL: Setting Up the World Database](https://dev.mysql.com/doc/world-setup/en/)

---

## Instructions

- Answer each question below.
- All SQL commands **must be executed** against the World database.
- For each SQL command:
  - Include the SQL in a fenced code block
  - Include a **screenshot** showing the command and results
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

**Compare and contrast the data types used for:**

- `country.Population`
- `country.LifeExpectancy`

Why were these data types selected?

### Answer

**Country.population** is an example of discrete data, as the values are represented by positive integers, which are "countable". **Country.LifeExpectancy** is an example of continuous data, as the data can range and can be arbitrarily accurate. In MySQL, LifeExpectancy only goes to one decimal place, but it could be more accurate if we took into account minutes, seconds, milliseconds, etc. 

I think these data types were chosen because they most accurately reflect what is wanted from the data points. For Population, you generally count the number of people and cannot have .5 of a person, so there is no need for arbitrary accuracy. Rather LifeExpectancy, people's ages can be arbitrary and can be measured, so using a continuous data point would better reflect the data 


### Screenshot

_Show the table structure or DESCRIBE output._

```sql
DESCRIBE country;
```

![Q1 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/0fcc10d47e95fb7dd164d78294dd408352311f1c/exercises/screenshots/screenshot_1_mod1.png)

---

## Question 2

**What is the data type of `country.IndepYear`?**
Why do you think this data type was selected?

### Answer

**Country.IndepYear** is a small integer. I think this data type was chosen over something like text in the event that you need to add/subtract years 

### Screenshot

```sql
DESCRIBE country;
```

![Q2 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/0fcc10d47e95fb7dd164d78294dd408352311f1c/exercises/screenshots/screenshot_1_mod1.png)

---

## Question 3

**Make a case for a different data type for `country.IndepYear`.**
Explain why your proposed data type might be better in some situations.

### Answer

I think making year a text format may prove beneficial over a numerical value for searches. 

---

## Question 4

Write a SQL command to **list the names of all cities in alphabetical order**.

### SQL

```sql
SELECT Name
FROM world.city
ORDER BY Name asc;
```

### Screenshot

![Q4 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/3657ca7a81a442c49319ce304269d6eeeb81d297/exercises/screenshots/screenshot_3_mod1.png)

---

## Question 5

Write a SQL command to
**list all forms of government from the `country` table**,
showing **each only once**, sorted alphabetically.

### SQL

```sql
SELECT DISTINCT GovernmentForm
FROM world.country
ORDER BY GovernmentForm asc;
```

### Screenshot

![Q5 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/a1f09452d44a67edbd5a5511dc4abb05a584726b/exercises/screenshots/screenshot_5_mod1.png)

---

## Question 6

Write a SQL command to **list all countries in the `Oceania` continent**.

### SQL

```sql
SELECT Name
FROM world.country
WHERE Continent = 'Oceania';
```

### Screenshot

![Q6 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/244adff2c547c15923681d021f68052c3ea29ffd/exercises/screenshots/screenshot_6_mod1.png)

---

## Question 7

Write a SQL command to **list the names and country code of all cities**.

### SQL

```sql
SELECT Name, CountryCode
FROM city;
```

### Screenshot

![Q7 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/c9cb69a3c3c11b46676055dc0720483af165a2c8/exercises/screenshots/screenshot_7_mod1.png)

---

## Question 8

Write a SQL command to **update the city named `"Nashville-Davidson"` to `"Nashville"`**.

### SQL

```sql
UPDATE city
SET Name = 'Nashville'
WHERE Name = 'Nashville-Davidson';
```

### Screenshot

![Q8 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/a5a2fb8c97d613996d803d2589de025600d58794/exercises/screenshots/screenshot8_mod1.png)

---

## Question 9

Write a SQL command to **insert a new country named `"Narnia"`**
with a country code of `"NAR"`.
Use reasonable values for the remaining columns.

### SQL

```sql
INSERT INTO country (Code, Name, Continent, Region, Population)
VALUES ('NAR', 'Narnia', 'Europe', 'Fantasy', 1000000);
```

### Screenshot

![Q9 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/1dd79608cf0df909e8a7141435c5561ce4610938/exercises/screenshots/screenshot9_mod1.png)

---

## Question 10

Write a SQL command to **delete the country with the country code `"NAR"`**.

### SQL

```sql
DELETE FROM country
WHERE Code = 'NAR';
```

### Screenshot

![Q10 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/24d588d68fe623370620eaea3b21336b0bc36604/exercises/screenshots/screenshot10_mod1.png)
