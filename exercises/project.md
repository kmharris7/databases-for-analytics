# Final Project

- Name: Kalei Harris
- Data used: Yelp Open Dataset
- Date 10/08/26
- Operating System: Windows 11
- SQL Server: Postgres

## **Introduction**

For my final project, I decided to create a database using the Yelp from [Yelp Data Licensing](https://business.yelp.com/data/resources/open-dataset/).


## **Locating the Data**

I found the Yelp Open Dataset through Kaggle, but I downloaded it directly from the business.yelp website.

It was a little diffcult to find data a dataset that wasn't just one table or had related tables that could be joined and aggregated. I did find a couple other datasets that would habve done fine, but due to the the shear size of the Yelp Open Dataset and the initial ease of the data having consistent key-value pairs that would make great CSV file, that's what I chose


## **Installing the Data**

Installing the Data was the hardest part, because each json file required a different cleaning process. Also, the files are massive, roughly 8.65GB for all the files and some of the tables containing over six million rows. I will go through each of the tables, their columns and data types, the cleaning process, and getting them imported into PostgresSQL


### **Business**
The business JSON has roughly 150,000 rows, with each line a different JSON object. All objects had 14 keys including:
- business_id - id of the business
- name  name of the business
- address - address of the business
- city - city the business is in
- state - state the business is in
- postal_code - postal code the business is in
- latitude - latitude point of the business
- longitude - longitude of the business
- stars decimal - Rating of business from 1-5 stars
- review_count - total number of reviews for a business
- is_open binary - binary represents if the business is still active: 0 means No and 1 means Yes
- attributes json - JSON of  some of the attributes of the business. Whether they have free parking, wifi, accept credit cards,  or other amenities
- categories - categories in which the business can be designated. For example, the clothing store H&M has the categories of "Shopping" and "Fashion"
- hours - Json of the day of the week and the hours opened in that day



Not all of the keys had a value for each JSON, but this made it ideal to make it into a CSV value to import into Postgres. From review, what would become the primary key, business_id, was not null so I was not too concerned other nulls. This json file also contained nested JSON, as the hours key and the attribute key both had json values for their pair

Once the json was downloaded, I needed to convert it into a CSV to upload it into Postgres using the COPY command. TO convert, I used Python and the pandas library. I created a file yelp.py. Below was the initial code:

```python
import pandas as pd

df = pd.read_json(
    path_or_buf="C:\Repos\databases-for-analytics\yelp_academic_dataset_business.json",
    lines=True,
)

df.to_csv(path_or_buf="yelp_academic_dataset_business.csv", index=False)
```


The resulting csv file:

![Intial_business_file](insert_picture_here.png)


From there I went to create the corresponding table in Postgres and copy the CSV:

```sql

create table business
business_id varchar(50) primary key,
name text,
address text,
city text,
state text,
postal_code text,
latitude decimal,
longitude decimal,
stars decimal
review_count decimal,
is_open smallint,
attributes json,
categories text,
hours json

```
![Creation of business table](pic2_project.png)



My next step was to finally copy 'yelp_academic_dataset_business.csv'

```sql

copy business from 'C:\Repos\databases-for-analytics\yelp_academic_dataset_business.csv'

with(format csv, header true, delimiter ',')

```

![copying csv into Postges](pic3_project.png)



I received an error that there were more values that columns. Initially, I thought it was because some of the strings contained commas, and since that was my delimiter, it was causing Postgres to think there were more values than there were.  I went through the table creation again and I realized that I forgot the postal_code column. So I dropped the table and re-added the values correctly.

Once corrected Then I ran into a another error which was 'Invalid Token "'" ' starting from row 2. I could not figure this error out for a while, but finally I learned that JSON strictly requires that I have double quotes for all of my key pairs and most of mine had single quotes. The next step was trying to make all of my single quotes into double quotes

I consulted the internet and landed upon a snippet of code which could help me turn all of my single quotes into double quotes

```python
import csv
import json
import ast

input_file = "yelp_academic_dataset_business.csv"
output_file = "yelp_business_final.csv"

with (
    open(input_file, "r", encoding="utf-8") as infile,
    open(output_file, "w", newline="", encoding="utf-8") as outfile,
):
    reader = csv.DictReader(infile)
    writer = csv.DictWriter(outfile, fieldnames=reader.fieldnames)
    writer.writeheader()

    for row in reader:
        if row["attributes"]:
            try:
                parsed_dict = ast.literal_eval(row["attributes"])
                row["attributes"] = json.dumps(parsed_dict)
            except ValueError, SyntaxError:
                pass

        writer.writerow(row)
```


This fixed the double quote issue, and when I went to copy it into Postgres, it ran successfully

![Sucessful run of business table](pic4_project.png)


### **User**


The file 'yelp_academic_dataset_users.json' was a lot easier to navigate in terms of column complexity, but is roughly 2,000,000 rows in the dataset, so took a lot of my memory. The json file had 22 different keys for each object:

- user_id - id of the user
- name - first name of the user
- review_count - number of reviews user has provided
- yelping_since - date which user joined Yelp
- useful, - number of "useful" votes that the user's reviews has received
- cool, -number of "cool" votes that the user's reviews has received
- elite - list of years in which the user was considered elite
- friends- list of the user's friend's id
- fans - number of fans a user has
- average_stars - average rating given by the user
- compliment_hot, - represents the number of compliments of type "hot" that a Yelp user receives from other users.
- compliment_more - represents the number of compliments of type "more" that a Yelp user receives from other users.
- compliment_profile - represents the number of compliments of type "profile" that a Yelp user receives from other users.
- compliment_cute, - represents the number of compliments of type "cure" that a Yelp user receives from other users.
- compliment_list - represents the number of compliments of type "list" that a Yelp user receives from other users.
- compliment_note - represents the number of compliments of type "note" that a Yelp user receives from other users.
- compliment_plain - represents the number of compliments of type "plain" that a Yelp user receives from other users.
- compliment_cool - represents the number of compliments of type "cool" that a Yelp user receives from other users.
- compliment_funny - represents the number of compliments of type "funny" that a Yelp user receives from other users.
- compliment_writer - represents the number of compliments of type "writer" that a Yelp user receives from other users.
- compliment_photos - represents the number of compliments of type "photos" that a Yelp user receives from other users.

This one I was able to use convert JSON to CSV using pandas without much formatting trouble:

```python
import pandas as pd

df = pd.read_json(
    path_or_buf="C:\Repos\databases-for-analytics\yelp_academic_dataset_users.json",
    lines=True,
)

df.to_csv(path_or_buf="yelp_academic_dataset_users.csv", index=False)
```


I created the table similarly to business:

```sql


create table users (
user_id varchar(50) primary key,
name text,
review_count numeric,
yelping_since date,
useful integer,
funny integer,
cool integer,
elite text,
friends, text
fans, integer,
average_stars numeric,
compliment_more integer ,
compliment_profile integer,
compliment_cute integer,
compliment_list integer ,
compliment_note integer,
compliment_plain integer,
compliment_cool integer,
compliment_funny integer,
compliment_writer integer,
compliment_photos integer

)


```

and the copied the csv into Postgres:

```sql

copy users from 'C:\Repos\databases-for-analytics\yelp_academic_dataset_users.csv'

with(format csv, header true, delimiter ',')

```

This was successful!

![Successful user table in Postgres](pic6_project.png)


### **Reviews**

The 'yelp_academic_dataset_reviews.json' gave me a lot of trouble initially, solely on the shear size of the file, as it is roughly 5GB by itself. My computer kept losing memory trying to process it. The file included 9 rows, with no special datatypes such as JSON, etc. The keys are:

- review_id: The id of the review left
- user_id: The id of the user who left the review
- business_id: The id of the business about who the review is
- stars: number of stars a user left a business
- useful: Number of votes from the community members who found the review useful
- funny: Number of votes from community members who found the review funny
- cool: Number of votes from community members who found the review cool
- text: the text content of the review
- date: The date the review was left


Coverting this was very slow, as my computer was pushing to even open the file in VSCODE, and I kept having to restart. I followed the same process I had for the other two in terms of turning the JSON into a CSV, but when I went to copy it into Postgres, the file was way too large. There are roughly 6,000,000 rows in the reviews so I needed a way to make it smaller.

I looked online and found that the read_json method from the pandas library includes an attribute nrows where I can specifiy the number of rows read. I split the data in half and only ran 3,000,000 rows.

I created the CSV and reviewed it and something was instantly awry. I saw that there were lines created that didn't correspond to json object and therfore row, but they were continuations of previous line.

![Line break issue in csv](pic7_project)


I couldn't figure out what went wrong. I eventually came to the conclusion that the answer lied in the json file, which was extremely hard to open given it's 5GB size. Nonetheless, I opened it and realized that there were line breaks "\n" included in the "text" attribute, which was causing the weird alignment in the csv.

After some research online, I realized that I needed to replace all the line breaks "\n" with a space so that the pandas would considered it to be one line. This is the code I used to do that:


```python
import pandas as pd


df = pd.read_json("yelp_academic_dataset_review.json", lines=True, nrows=3000000)

df.replace(to_replace="\n", value=" ", regex=True, inplace=True).to_csv(
    "yelp_reviews.csv", index=False
)
```

I created the table like normal:

```sql

create table reviews(
  review_id varchar(50) primary key,
  user_id varchar(50),
  business_id varchar(50)
  stars numeric,
  useful integer,
  funny integer,
  cool integer,
  text text,
  date, date
)


```

and copied the CSV into the table:

```sql

copy reviews from 'C:\Repos\databases-for-analytics\yelp_reviews.csv'

with(format csv, header true, delimiter ',')

```

And the table was loaded successfully

![Successful add of review table in Postgres](pic8_project)


### **Tip**

Finally was transforming the 'yelp_academic_dataset_tip.json' file. This one had roughlt 900,000 rows, but only 5 keys  so it was easily on of the most managble files. The keys include:
- user_id: Id of th user who left the tip
- business_id: Id of the busines about who the tip was left
- text: The text content of th tip
- date: The date the tip was left
- compliment_count: Number of compliments that were left on the tip

I reviewed this json, and realized that the text attribute also contained line breaks, so I would need to transform the data similarly to the review json:

```python
import pandas as pd


df = pd.read_json("yelp_academic_dataset_tip.json", lines=True, nrows=3000000)

df.replace(to_replace="\n", value=" ", regex=True, inplace=True).to_csv(
    "yelp_tips.csv", index=False
)
```

Created the table in Postgres:

```sql

create table tips(
  user_id varchar(50),
  business_id varchar(50),
  text text,
  date date,
  compliment_count integer
)

```

And copied the CSV into Postrgres

```sql

copy tips from 'C:\Repos\databases-for-analytics\yelp_tips.csv'

with(format csv, header true, delimiter ',')

```

In which the copy was successful

![Successful copy of the tips table](pic9_project.png)



## **Verifying the Data**

Because the tables beautifully have common columns, aggregating and joining the data was pretty simple. Which led to a lot of fun challenges regarding what I could find w/ the data

1. My first query found all the restaurants that a specific user left reviews of and the reviews themselves

```sql

select  us.name, bz.name, rw.text

from business as bz

join reviews as rw on rw.business_id = bz.business_id
join users as us on us.user_id = rw.user_id

where rw.user_id = 'FjMQVZjSqY8syIO-53KFKw'

```
![query1](pic10_project)



2. My second query provided an aggregated count of all the reviews of a business that were in LA. This also could have been acheived using the review_count column of the business table

```sql

select count(rw.user_id),bz.name

from business as bz
join reviews as rw on rw.business_id = bz.business_id

where bz.state ilike '%LA%'

group by bz.name
order by count(rw.user_id) desc

```

![query2](pic11_project.png)


3. For my third query, I wanted to get the practice of pulling JSON from the sql, so I seached for the name of the businesses and if they accept credit cards or not.

```sql

select name, attributes -> 'BusinessAcceptsCreditCards' as Business_Accepts_Credit_Cards
from business


limit 1000

```

![query3](pic12.project.png)


```sql

select
us.user_id,
us.name,
us.yelping_since

from users as us


where friends ilike '%QF1Kuhs8iwLWANNZxebTow%'
```
