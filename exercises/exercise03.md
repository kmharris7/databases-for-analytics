# Exercise 03: MongoDB – Document Queries and Analysis

- Name: Kalei Harris
- Course: Database for Analytics
- Module: 3
- Database Used: MongoDB
- Dataset: `restaurants-json.json`

---

## Instructions

- Import the provided `restaurants-json.json` file into MongoDB.
- All commands must be **executed by you** in the MongoDB shell or MongoDB Compass.
- For each query:
  - Include the MongoDB command in a fenced code block
  - Include a **screenshot** showing the command and its result
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

When importing the documents from `restaurants-json.json`,
**how many documents were imported into your collection**?

### Answer

25358

### Screenshot

_Show evidence of how you determined this (for example, a count query)._

```javascript
db["restaurants"].countDocuments()
```

![Q1 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/9bf5751a63ee81227fd3d8f45476952fb3f47c10/exercises/screenshots/mod3/q1_mod3.png)

---

## Question 2

Before writing queries on the data,
**what command** do you use to set the
**MongoDB shell to operate on the `44661` database**?

### MongoDB Command

```javascript
use("44661")
```

### Screenshot

![Q2 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/9bf5751a63ee81227fd3d8f45476952fb3f47c10/exercises/screenshots/mod3/q2_mod3.png)

---

## Question 3

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**locate all documents in the `"Queens"` borough**.

### MongoDB Query

```javascript
db.restaurants.find({borough:'Queens'})
```

### Screenshot

![Q3 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/9bf5751a63ee81227fd3d8f45476952fb3f47c10/exercises/screenshots/mod3/q3_mod3.png)

---

## Question 4

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**find the number of restaurants in the `"Queens"` borough**.

### MongoDB Query

```javascript
db.restaurants.find({borough:'Queens'}).count()
```

### Screenshot

![Q4 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/9bf5751a63ee81227fd3d8f45476952fb3f47c10/exercises/screenshots/mod3/q4_mod3.png)

---

## Question 5

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**find the number of restaurants** in the `"Queens"` borough
**whose cuisine is `"Hamburgers"`**.

### MongoDB Query

```javascript
db.restaurants.find({borough:'Queens',cuisine:'Hamburgers'})
```

### Screenshot

![Q5 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/9bf5751a63ee81227fd3d8f45476952fb3f47c10/exercises/screenshots/mod3/q5_mod3.png)

---

## Question 6

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**find the number of restaurants in Zipcode `10460`**.

_Hint: Look up how to query **embedded documents**._

### MongoDB Query

```javascript
db.restaurants.find({'address.zipcode':'10460'})
```

### Screenshot

![Q6 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/9bf5751a63ee81227fd3d8f45476952fb3f47c10/exercises/screenshots/mod3/q6_mod3.png)

---

## Question 7

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**display only the names of restaurants in Zipcode `10460`**.

_Hint: Look up how to **project fields** in MongoDB._

Your output should resemble:

```json
{ name: "Wild Asia" }
{ name: "Terrace Cafe" }
{ name: "African Terrace" }
{ name: "Cool Zone" }
{ name: "Beaver Pond" }
...
```

### MongoDB Query

```javascript
db.restaurants.find({'address.zipcode':'10460'},{_id:0,name:1})
```

### Screenshot

![Q7 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e67536860906fbee00ec81ea9cd0f2e982066404/exercises/screenshots/mod3/q7_mod3_v2.png)

---

## Question 8

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**display only the names of restaurants whose name contains `"IHOP"`**,
ignoring case.

Your results should include:

- `"Ihop"`
- `"Ihop Restaurant"`

### MongoDB Query

```javascript
db.restaurants.find({name:/.*ihop.*/i},{_id:0,name:1})
```

### Screenshot

![Q8 Screenshot](https://github.com/kmharris7/databases-for-analytics/blob/e67536860906fbee00ec81ea9cd0f2e982066404/exercises/screenshots/mod3/q8_mod3_v2.png)
