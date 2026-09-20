# Exercise 04: Advanced SQL, Jupyter, and Visualization

- Name: Kalei H
- Course: Database for Analytics
- Module: 4
- Database Used: World Database
- Tools Used: PostgreSQL, SQLAlchemy, Pandas, Jupyter Notebooks

---

## Instructions

- Complete each task using the **World database** installed earlier.
- For SQL questions:
  - Write the SQL command in a fenced code block
  - Execute the command and include a **screenshot of the results**
- For Jupyter Notebook questions:
  - Include the required Python statements
  - Include **screenshots of the notebook output**
- Store all screenshots in the `screenshots/` folder and embed them below each question.

---

## Question 1

Considering the World database, write a SQL statement that will
**display the names of countries**
that speak **more than two official languages**,
along with the **number of official languages spoken**.

- Sort the results by **number of languages**, from **most to least**.
- _Hint: There are fewer than 10 countries in the results._

### SQL

```sql
with num_of_languages as (
  select c.name as name, count(l.language) as official_languages
  from country as c
  join countrylanguage as l on l.countrycode = c.code
  where isOfficial = 'T'

  group by c.name

)

select name, official_languages
from num_of_languages
where official_languages > 2 

order by official_languages desc
```

### Screenshot

![Q1 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/a08ca0a25b1f03e328e9ab960c18e2d6facfd767/exercises/screenshots/mod4/q1_mod4.png)

---

## Question 2

Using **Jupyter Notebooks**, you must use the
`create_engine` command to connect to your database.

After the `create_engine` command is executed,
**what are the three statements** required to
execute the query from Question 1 and
**display the results in the notebook**?

### Python Code

```python
#statement 1
query = """ with num_of_languages as (
  select c.name as name, count(l.language) as official_languages
  from country as c
  join countrylanguage as l on l.countrycode = c.code
  where isOfficial = 'T'

  group by c.name

)

select name, official_languages
from num_of_languages
where official_languages > 2 

order by official_languages desc
"""
#statement 2
languages = pd.read_sql_query(query,engine)

#statement 3
languages
```

### Screenshot

![Q2 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/a08ca0a25b1f03e328e9ab960c18e2d6facfd767/exercises/screenshots/mod4/q2_mod4.png)

---

## Question 3

Using **Jupyter Notebooks**, write the Python code needed
to produce the following graph:

![countries.jpg](./instructions/04-countries.jpg)

(The graph shows country-level results derived from the World database.)

### Python Code

```python
graph_query = """
    with num_of_languages as (
  select c.name as name, count(l.language) as num_languages
  from country as c
  join countrylanguage as l on l.countrycode = c.code
  where isOfficial = 'T'

  group by c.name

)

select name, num_languages
from num_of_languages
where num_languages > 2 

order by num_languages desc
"""
graph_data = pd.read_sql_query(graph_query,engine)
graph = graph_data.plot(x='name',y='num_languages',kind='bar',figsize=(5,4))
plt.legend(loc='upper right')
```

### Screenshot

![Q3 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/a08ca0a25b1f03e328e9ab960c18e2d6facfd767/exercises/screenshots/mod4/q3_mod4_1.png)
