# SQL Guide

## Contents
[Basic SQL](#basic-sql)
- [Anatomy of a Basic SQL Query](#anatomy-of-a-basic-sql-query)
- [At the Bare Minimum: SELECT and FROM](#at-the-bare-minimum-select-and-from)
- [Column Names](#column-names)
- [An Important Tip: Explore the Dataset](#an-important-tip-explore-the-dataset)
- [Slicing Your Data: WHERE](#slicing-your-data-where)
- [Controlling the Output: LIMIT](#controlling-the-output-limit)

[Intermediate SQL 1](#intermediate-sql-1)
- [Super Powering the WHERE Clause](#super-powering-the-where-clause)
- [Comparison Operations on Numerical Data](#comparison-operations-on-numerical-data)
- [Comparison Operators on Non-Numerical Data](#comparison-operators-on-non-numerical-data)
- [More Operators to Super Power the WHERE Clause](#more-operators-to-super-power-the-where-clause)
- [LIKE](#like)
- [Wildcards and ILIKE](#wildcards-and-ilike)
- [IN](#in)
- [BETWEEN](#between)
- [IS NULL](#is-null)
- [AND](#and)
- [OR](#or)
- [NOT](#not)
- [Sorting Data: ORDER BY](#sorting-data-order-by)

[Intermediate SQL 2](#intermediate-sql-2)
- [Aggregations in the SELECT Clause](#aggregations-in-the-select-clause)
- [Counting all rows](#Counting-all-rows)
- [Counting individual columns](#counting-individual-columns)
- [SUM](#sum)
- [MIN and MAX](#min-and-max)
- [AVG](#avg)
- [Simple Arithmetic in SQL](#simple-arithmetic-in-sql)

[Intermediate SQL 3](#intermediate-sql-3)
- [GROUP BY](#group-by)
- [GROUP BY with ORDER BY](#group-by-with-order-by)
- [HAVING](#having)

[Intermediate SQL 4](#intermediate-sql-4)
- [DISTINCT](#distinct)
- [CASE STATEMENT](#case-statement)

[Advanced SQL](#advanced-sql)
- [JOINS](#joins)
- [JOIN clause](#join-clause)
- [Deconstructing the JOIN clause](#deconstructing-the-join-clause)
- [INNER JOIN](#inner-join)
- [OUTER JOINS](#outer-joins)
- [LEFT JOIN](#left-join)
- [RIGHT JOIN](#right-join)
- [FULL OUTER JOIN](#full-outer-join)
- [UNION](#union)
- [SQL JOIN Comparison Operators](#join-comparison)
- [SQL JOIN on multiple keys](#join-multiple-keys)
- [SQL SELF JOIN](#self-join)



This SQL guide is meant to help you get started with SQL. It's helpful for absolute beginners but better for beginners that need a reference when coding. This guide is adapted from Mode Analytics Intro to SQL which is a great introduction to SQL, however, this guide with the accompanying datasets provide a more hands-on experience that allows you to code live with tools used in industry, All tables found in the Mode Analytics guide are loaded in our databases but we added dozens more to get you better acquainted with SQL and analytics. 

# Basic SQL
### Anatomy of a Basic SQL Query

The anatomy of a SQL query is the same every single time. The clauses (e.g., SELECT, FROM, WHERE) are always in the same order, however, many of the clauses are optional. In this section, I’ll explain the SQL clauses that are always required to pull data as well as a few operators and math operations that help convert raw data into something useful. 

Note: The SQL code examples use the Strata Scratch database and is executable on Strata Scratch through your web browser. I would recommend copying and pasting the code, executing it, and taking a look at the output. 

### At the Bare Minimum: SELECT and FROM

There are two required ingredients in any SQL query: SELECT and FROM — and they have to be in that order.  
Back to SELECT and FROM
SELECT indicates which columns of the table you’d like to view, and FROM identifies the table you want to pull data from.

```sql
SELECT 
    year, 
    month, 
    west
FROM datasets.us_housing_units
```

- In this example, we’re pulling data from a schema called `datasets` and a table called `us_housing_units`. Within the table, we’re interested in the data that are stored in the year, month, and west columns.
- a schema is a logical way to group objects like tables, procedures, views. Think of a schema as a container for your database tables.

Note that the three column names were separated by a comma in the query. Whenever you select multiple columns, they must be separated by commas, but you should not include a comma after the last column name.

If you want to select every column in a table, you can use `*` instead of typing out the column names:

```sql
SELECT *
FROM datasets.us_housing_units
```

### Column Names

If you’d like your results to look a bit more presentable, you can rename columns to include spaces. For example, if you want the west column to appear as West Region in the results, you would have to type:

```sql
SELECT west AS "West Region"
FROM datasets.us_housing_units
```

This gives us the following output:

![strata scratch](assets/3b.png)

- Without the double quotes, that query would read ‘West’ and ‘Region’ as separate objects and would return an error. 

Note that the results will only be case sensitive if you put column names in double quotes. The following query, for example, will return results with lower-case column names.

```sql
SELECT west AS West_Region,
       south AS South_Region
FROM datasets.us_housing_units
```

Output:

![strata scratch](assets/4b.png)

- West_Region would be returned as west_region since double quotes are missing.
- In practice, you’d want to stick to one naming convention, either “West Region” or west_region. Having a consistent naming convention helps standardize coding practices and makes everyone more efficient when pulling data.

### An Important Tip: Explore the Dataset

Now that you understand the basics of query data from a table, the next step is to query, format, and aggregate data so that it’s useful. What’s difficult is that there are often no way to preview the data in the tables. Before diving too deep in any SQL query or analysis, I will always explore tables before starting to write complex queries. All you need to do is perform what I call a SELECT STAR or

```sql
SELECT 
    * 
FROM datasets.us_housing_units
```

This will allow you to see all the columns and some data in the table so that you better understand the data types and column names before writing any complicated query.

![strata scratch](assets/5b.png)

### Slicing Your Data: WHERE 

Now you know how to pull data from tables and even specify what columns you want in your output. But what if you’re interested only in housing units sold in January? The WHERE clause allows you to returns rows of data that meet your criteria. 

The WHERE clause, in this example, will go after the FROM clause. In the WHERE clause you need to write a logical operator. For example, if you’re interested in pulling data from month 1, simply write `month = 1` in the WHERE clause.

```sql
SELECT *
FROM datasets.us_housing_units
WHERE month = 1
```

Output:

![strata scratch](assets/6b.png)

- Note that `month` is a column in the table and the months are represented by numbers. Remember to do a `SELECT * ` to explore the table before writing your queries.

### Controlling the Output: LIMIT

The LIMIT clause is optional and is used to control the number of rows displayed in the output. The LIMIT clause goes at the very end of your SQL query. I find this clause useful when exploring data.  The following syntax limits the number of rows to only 100:

```sql
SELECT *
FROM datasets.us_housing_units
LIMIT 100
```

# Intermediate SQL 1

### Super Powering the WHERE Clause

The WHERE clause is powerful. You can leverage operators and mathematical operations to slice your data into different views. In addition, you can chain together all these operators to efficiently narrow in on the data.

### Comparison Operations on Numerical Data

The most basic way to filter data is to use comparison operators. The easiest way to understand them is to start by looking at a list of them:
- Equal to                        =
- Not equal to                    <>  or  !=
- Greater than                    >
- Less than                       < 
- Greater than or equal to        >=
- Less than or equal to           <=

These comparison operators make the most sense when applied to numerical columns. For example, let’s use > to return only the rows where the West Region produced more than 30 housing units

```sql
SELECT *
FROM datasets.us_housing_units
WHERE west > 30
```

- The SQL query is saying, select all the data located in the schema `datasets` and in the table `us_housing_units` where the `west` column (i.e., the west region) has values greater than 30. 
- The SQL query will then go through the `west` column and look for values greater than 30 then output all the rows in the table where west > 30. 

![strata scratch](assets/49b.png)

### Comparison Operators on Non-Numerical Data

Some of the above operators work on non-numerical data as well. `=` and `!=` make perfect sense — they allow you to select rows that match or don’t match any value, respectively. For example, run the following query and you’ll notice that none of the January rows show up:

```sql
SELECT *
FROM datasets.us_housing_units
WHERE month_name != 'January'
```

Output:

![strata scratch](assets/9b.png)

### More Operators to Super Power the WHERE Clause

Here’s a list of additional logical operators to use in the WHERE clause: 

```sql
LIKE allows you to match similar values, instead of exact values.
IN allows you to specify a list of values you’d like to include.
BETWEEN allows you to select only rows within a certain range.
IS NULL allows you to select rows that contain no data in a given column.
AND allows you to select only rows that satisfy two conditions.
OR allows you to select rows that satisfy either of two conditions.
NOT allows you to select rows that do not match a certain condition.
```

### LIKE

LIKE is a logical operator that allows you to match on similar values rather than exact ones.

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE "group" LIKE 'Snoop%'
```

- The LIKE operator will look for values that start with Snoop. The % symbol is a wildcard (explained below) and ignores any value coming after Snoop. 
- Note that the LIKE operator is case sensitive so `Snoop` and `snoop` are different when using LIKE. 

### Wildcards and ILIKE

The % used above represents any character or set of characters. In this case, % is referred to as a “wildcard.” LIKE is case-sensitive, meaning that the above query will only capture matches that start with a capital “S” and lower-case “noop.” To ignore case when you’re matching values, you can use the ILIKE command:

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE "group" ILIKE 'snoop%'
```

Output:

![strata scratch](assets/11b.png)

- In this case, using ILIKE allows you to be case insensitive. `Snoop` is the same as `snoop` according to ILIKE.

You can also use _ (a single underscore) to substitute for an individual character:

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE artist ILIKE 'dr_ke'
```

Output:

![strata scratch](assets/12b.png)

- In this case any alphanumeric value can take the place of the `_` symbol. We’re obviously looking for Drake but this query will catch any misspellings in the `a` portion of his name (e.g., drbke)

### IN

IN is a logical operator in SQL that allows you to specify a list of values that you’d like to include in the results. 

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE year_rank IN (1, 2, 3)
```

Output:

![strata scratch](assets/13b.png)

- Here I’m only interested in data where year_rank is 1 or 2 or 3. 

As with comparison operators, you can use non-numerical values, but they need to go inside single quotes. Regardless of the data type, the values in the list must be separated by commas. Here’s another example:

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE artist IN ('Taylor Swift', 'Usher', 'Ludacris')
```

The output here should only yield data corresponding to artists named Taylor Swift or Usher or Ludacris.

![strata scratch](assets/14b.png)

### BETWEEN

BETWEEN is a logical operator in SQL that allows you to select only rows that are within a specific range. It has to be paired with the AND operator, which you’ll learn about in a later.

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE year_rank BETWEEN 5 AND 10
```

- Here I’m only interested in data where year_rank is 5 to 10. 
- `Between` is inclusive so the year_rank can include 5 and 10 (i.e., not 6 to 9). 

### IS NULL

IS NULL is a logical operator in SQL that allows you to exclude rows with missing data from your results.

Some tables contain null values—cells with no data in them at all. This can be confusing for heavy Excel users, because the difference between a cell having no data and a cell containing a space isn’t meaningful in Excel. In SQL, the implications can be pretty serious. 

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE artist IS NULL
```

- IS NULL will only catch cells with no data. A space is considered data so IS NULL will not catch any cell with a space. Be mindful of that when exploring your dataset.

### AND

AND is a logical operator in SQL that allows you to select only rows that satisfy two conditions.

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE year = 2012 AND year_rank <= 10
```

You can use AND with any comparison operator, as many times as you want. If you run this query, you’ll notice that all of the requirements are satisfied.

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE year = 2012
   AND year_rank <= 10
   AND "group" ILIKE '%feat%'
```

- This query will return all data in the `billboard_top_100_year_end` table for the year 2012, year_rank is less or equal to 10, and where the group has the word `feat` (i.e., Top 10 song collaborations in 2012).

![strata scratch](assets/18b.png)

### OR

OR is a logical operator in SQL that allows you to select rows that satisfy either of two conditions. It works the same way as AND, which selects the rows that satisfy both of two conditions.

```sql
SELECT *
  FROM datasets.billboard_top_100_year_end
 WHERE year_rank = 5 OR artist = 'Gotye'
 ```

- The query will return all data where the year_rank is 5 or the artist is named Gotye regardless of his year_rank. 

### NOT

NOT is a logical operator in SQL that you can put before any conditional statement to select rows for which that statement is false.

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE year = 2013
   AND year_rank NOT BETWEEN 2 AND 3
```

Output:

![strata scratch](assets/20b.png)

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
WHERE year = 2013
   AND artist IS NOT NULL
```

Output:

![strata scratch](assets/21b.png)

### Sorting Data: ORDER BY

Once you’ve learned how to filter data, it’s time to learn how to sort data. The ORDER BY clause allows you to reorder your results based on the data in one or more columns. First, take a look at how the table is ordered by default:

```sql
SELECT * 
FROM datasets.billboard_top_100_year_end
```

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
ORDER BY artist
```

You’ll need to specify whether you want the data to be displayed in ascending order or descending order. 

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
ORDER BY artist ASC
```

Will output data alphabetically by artist

![strata scratch](assets/50b.png)

```sql
SELECT *
FROM datasets.billboard_top_100_year_end
ORDER BY artist DESC
```

Will output data reverse alphabetically by artist

![strata scratch](assets/51b.png)

# Intermediate SQL 2

Sometimes you’re not necessarily interested in an output of all the data. Your question that you’re trying to answer is simpler like `how many rows are in this table?` or `how many top 10 songs did Usher produce in 2012?`. In these cases, we don’t want a list of values but would rather have one value — the answer.  You can process data in the SELECT clause. 

## AGGREGATIONS IN THE SELECT CLAUSE
### Counting all rows

COUNT is a SQL aggregate function for counting the number of rows in a particular column. COUNT is the easiest aggregate function to begin with because verifying your results is extremely simple. 

```sql
SELECT 
    COUNT(*)
FROM datasets.aapl_historical_stock_price
```

Output:``` 3527 ```

Important note: count(*) also counts the null values. If you want to exclude null values, refer below.

### Counting individual columns

Things start to get a little bit tricky when you want to count individual columns. The following code will provide a count of all of rows in which the high column does not contain a null.

```sql
SELECT 
    COUNT(high)
FROM datasets.aapl_historical_stock_price
```

Output:``` 3527 ```

- Note that by specifying the name of the column `high` in `count()`, the query will ignore any nulls in the `high` column and only count the rows containing values. 

### SUM 

SUM is a SQL aggregate function that totals the values in a given column. Unlike COUNT, you can only use SUM on columns containing numerical values.

```sql
SELECT 
    SUM(volume)
FROM datasets.aapl_historical_stock_price
```

Output:``` 73442072063 ```

### MIN and MAX

MIN and MAX are SQL aggregation functions that return the lowest and highest values in a particular column.

```sql
SELECT MIN(volume) AS min_volume,
       MAX(volume) AS max_volume
FROM datasets.aapl_historical_stock_price
```

Output:

![strata scratch](assets/29b.png)

### AVG

AVG is a SQL aggregate function that calculates the average of a selected group of values. It’s very useful, but has some limitations. First, it can only be used on numerical columns. Second, it ignores nulls completely.

```sql
SELECT 
    AVG(high)
FROM datasets.aapl_historical_stock_price
```

Running the code above will give an output of ```506.5```.
### Simple Arithmetic in SQL

You can perform arithmetic in SQL using the same operators you would in Excel: +, -, *, /. However, in SQL you can only perform arithmetic across columns on values in a given row. To clarify, you can only add values in multiple columns from the same row together using +.

```sql
SELECT year,
       month,
       west,
       south,
       west + south AS south_plus_west
  FROM datasets.us_housing_units
```

The output will contain as many rows as are in the table. Only west and south will be added together on a row level. 

![strata scratch](assets/31b.png)

```sql
SELECT year,
       month,
       west,
       south,
       west + south - 4 * year AS nonsense_column
  FROM datasets.us_housing_units
```

Output:

![strata scratch](assets/32b.png)

# Intermediate SQL 3
### GROUP BY

SQL aggregate functions like COUNT, AVG, and SUM have something in common: they all aggregate across the entire table. But what if you want to aggregate only part of a table? For example, you might want to count the number of entries for each year.

In situations like these, you’d need to use the GROUP BY clause. GROUP BY allows you to separate data into groups, which can be aggregated independently of one another. 

The GROUP BY clause always comes towards the end of the SQL query. It technically goes after the WHERE clause but if the WHERE clause is missing then the GROUP BY will come after the FROM clause (or JOIN clause, but we haven’t learned that yet). 

You’ll know which column name to include in the GROUP BY because it’s listed in the SELECT clause. You only want to include column names that are not being operated on in the GROUP BY clause. In the example below, you do not add COUNT(*) in the GROUP BY because COUNT is an operator. 

```sql
SELECT year,
       COUNT(*) AS count
FROM datasets.aapl_historical_stock_price
GROUP BY year
```

![strata scratch](assets/33b.png)

- The query outputs a count of all the rows by year
- You only add `year` in the GROUP BY because you want to split the COUNT by year.

```sql
SELECT year,
       month,
       COUNT(*) AS count
FROM datasets.aapl_historical_stock_price
GROUP BY year, month
```

Output:

![strata scratch](assets/34b.png)

- You add both year and month in the GROUP BY because you’re interested in the row count by year and month.

### GROUP BY with ORDER BY

The order of column names in your GROUP BY clause doesn’t matter—the results will be the same regardless. If you want to control how the aggregations are grouped together, use ORDER BY. Try running the query below, then reverse the column names in the ORDER BY statement and see how it looks:

```sql
SELECT year,
       month,
       COUNT(*) AS count
FROM datasets.aapl_historical_stock_price
GROUP BY year, month
ORDER BY month, year
```

Output:

![strata scratch](assets/35b.png)

### HAVING

However, you’ll often encounter datasets where GROUP BY isn’t enough to get what you’re looking for. Let’s say that it’s not enough just to know aggregated stats by month. After all, there are a lot of months in this dataset. Instead, you might want to find every month during which AAPL stock worked its way over $400/share. The WHERE clause won’t work for this because it doesn’t allow you to filter on aggregate columns—that’s where the HAVING clause comes in:

```sql
SELECT year,
       month,
       MAX(high) AS month_high
FROM datasets.aapl_historical_stock_price
GROUP BY year, month
HAVING MAX(high) > 400
ORDER BY year, month
```
Output:

![strata scratch](assets/36c.png)

- The HAVING clause always comes after the GROUP BY but before the ORDER BY clauses.
- It might be more intuitive to use a WHERE clause rather than HAVING clause in this query but you’re not allowed to process or aggregate data in the WHERE clause. This is due to the order of operations when the CPU performs the SQL query. The SQL query is processed by first processing the SELECT, FROM, and GROUP BY clauses. From that dataset, the HAVING clause will act on the data and remove any stock prices below 400. Lastly, the SQL query will order the data by year and month as indicated by the ORDER BY clause. 

# Intermediate SQL 4
### DISTINCT

You’ll occasionally want to look at only the unique values in a particular column. You can do this using SELECT DISTINCT syntax.

```sql
SELECT 
    DISTINCT month
FROM datasets.aapl_historical_stock_price
```

- Outputs all the distinct values in the month column of the table.

![strata scratch](assets/37b.png)

DISTINCT is handy when you want to count the number of unique values in a column (e.g., distinct months or distinct users). 

```sql
SELECT 
    COUNT(DISTINCT month) AS unique_months
FROM datasets.aapl_historical_stock_price
```

Output: ``` 12 ```

### CASE STATEMENT

The CASE statement is SQL’s way of handling if/then logic. The CASE statement is followed by at least one pair of WHEN and THEN statements—SQL’s equivalent of IF/THEN in Excel. It must end with the END statement. The ELSE statement is optional, and provides a way to capture values not specified in the WHEN/THEN statements. CASE is easiest to understand in the context of an example:

```sql
SELECT player_name,
       year,
       CASE WHEN year = 'SR' THEN 'yes'
            ELSE 'no' END AS is_a_senior
FROM datasets.college_football_players
```

- The case statement in this example will output a `yes` value for any year with a `SR` value. If the row does not have a `SR` value, the output is `no`. 

![strata scratch](assets/39b.png)

#### Adding multiple conditions to a CASE statement

```sql
SELECT player_name,
       weight,
       CASE WHEN weight > 250 THEN 'over 250'
            WHEN weight > 200 THEN '201-250'
            WHEN weight > 175 THEN '176-200'
            ELSE '175 or under' END AS weight_group
  FROM datasets.college_football_players
```

Output:

![strata scratch](assets/40b.png)

- You can add as many conditions by adding multiple WHENs. 
- There can only be one ELSE statement which is always last in your CASE WHEN. 

# Advanced SQL
### JOINS

Up to this point, we’ve only been working with one table at a time. The real power of SQL, however, comes from working with data from multiple tables at once.

To understand what joins are and why they are helpful, let’s think about Twitter.

Twitter has to store a lot of data. Twitter could (hypothetically, of course) store its data in one big table in which each row represents one tweet. There could be one column for the content of each tweet, one for the time of the tweet, one for the person who tweeted it, and so on. It turns out, though, that identifying the person who tweeted is a little tricky. There’s a lot to a person’s Twitter identity—a username, a bio, followers, followees, and more.

In an organization like this, Twitter will have hundreds of tables, each storing some attribute about the user, tweet, and/or action. If we just have a user table and tweet table, you can store data like this —the first table—the users table—contains profile information, and has one row per user. The second table—the tweets table—contains tweet information, including the username of the person who sent the tweet. By matching—or joining—that username in the tweets table to the username in the users table, Twitter can still connect profile information to every tweet.

Here’s an example using a different dataset:

```sql
SELECT teams.conference AS conference,
       AVG(players.weight) AS average_weight
FROM datasets.college_football_players players
JOIN datasets.college_football_teams teams
    ON teams.school_name = players.school_name
GROUP BY teams.conference
ORDER BY AVG(players.weight) DESC
```

Can you guess what the query is trying to achieve? We’ve covered all aspects of the SQL query except for the JOIN clause.

- In this query, the SELECT clause tells us what information is going to be displayed. We’re interested in the average weight of college football players by conference.

![strata scratch](assets/41b.png)

### JOIN clause

In the example above, the JOIN clause joins the `college_football_players` and `college_football_teams` tables together, presumably so that we can link player attributes with team attributes.

But how do we JOIN two tables together? The key is the `ON` clause. With the `ON` clause, you’re selecting a column in one table and matching it with a column in another table. In this case, we’re taking `school_name` from `datasets.college_football_teams` and matching it with `school_name` from `datasets.college_football_players`.

### Deconstructing the JOIN clause 

Let’s take only the FROM and JOIN clauses from the example above:

```sql
FROM datasets.college_football_players players
JOIN datasets.college_football_teams teams
    ON teams.school_name = players.school_name
```

- `FROM`: pick a table to start
- You can nickname the table for easy reference. In this case, I’ve named the `datasets.college_football_players` table as `players`. 
- I use `players` in the `ON` clause so that I don’t have to type the name of the entire table again (`datasets.college_football_players`). 
- `JOIN`: pick the table you’re interested in joining. Just like with the `FROM` clause, you can nickname the table. In this case, I nicknamed the table  `datasets.college_football_teams` as `teams`. 
- The `ON` clause matches columns from both tables so that the tables can join together. In this case, I’m matching `school_name` from both tables. 

### INNER JOIN

In the football example above, all of the players in the `players` table match to one school in the `teams` table. But what if the data isn’t so clean? What if there are multiple schools in the `teams` table with the same name? Or if a player goes to a school that isn’t in the teams table?

If there are multiple schools in the `teams` table with the same name, each one of those rows will get joined to matching rows in the `players` table. For example, if there was a player named `Michael Campanaro` and if there were three rows in the `teams` table where `school_name = 'Wake Forest'`, an inner join would return three rows with `Michael Campanaro`.

It’s often the case that one or both tables being joined contain rows that don’t have matches in the other table. The way this is handled depends on whether you’re making an inner join or an outer join.

We’ll start with inner joins, which can be written as either `JOIN datasets.college_football_teams teams` or `INNER JOIN datasets.college_football_teams` . Inner joins eliminate rows from both tables that do not satisfy the join condition set forth in the `ON` statement. In mathematical terms, an inner join is the intersection of the two tables.

<p align="center">
  <img width="200" height="150" src="https://github.com/stratascratch/stratascratch.github.io/blob/master/guides/sql-guide/assets/image_43.gif">
</p>

Therefore, if a player goes to a school that isn’t in the `teams` table, that player won’t be included in the result from an inner join. Similarly, if there are schools in the `teams` table that don’t match to any schools in the `players` table, those rows won’t be included in the results either.

### OUTER JOINS

When performing an inner join, rows from either table that are unmatched in the other table are not returned. In an outer join, unmatched rows in one or both tables can be returned. There are a few types of outer joins — LEFT JOIN, RIGHT JOIN, and FULL OUTER JOIN.

![strata scratch](assets/image_44.jpg)

### LEFT JOIN

<p align="center">
  <img width="200" height="150" src="https://github.com/stratascratch/stratascratch.github.io/blob/master/guides/sql-guide/assets/image_45.png">
</p>

Let’s start by running an INNER JOIN on the `Crunchbase` dataset and taking a look at the results. We’ll just look at `company-permalink` in each table, as well as a couple other fields, to get a sense of what’s actually being joined.

```sql
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM datasets.crunchbase_companies companies
JOIN datasets.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
```

You may notice that “280 North” appears twice in this list. That is because it has two entries in the `datasets.crunchbase_acquisitions` table, both of which are being joined onto the `datasets.crunchbase_companies` table.

![strata scratch](assets/43b.png)

Now try running that query as a LEFT JOIN:

```sql
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN datasets.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
```

You can see that the first two companies from the previous result set, `waywire` and `1000memories`, are pushed down the page by a number of results that contain null values in the `acquisitions_permalink` and `acquired_date` fields.

![strata scratch](assets/52b.png)

This is because the LEFT JOIN command tells the database to return all rows in the table in the FROM clause, regardless of whether or not they have matches in the table in the LEFT JOIN clause.

### RIGHT JOIN

Right joins are similar to left joins except they return all rows from the table in the RIGHT JOIN clause and only matching rows from the table in the FROM clause.

<p align="center">
  <img width="200" height="150" src="https://github.com/stratascratch/stratascratch.github.io/blob/master/guides/sql-guide/assets/image_48.gif">
</p>

RIGHT JOIN is rarely used because you can achieve the results of a RIGHT JOIN by simply switching the two joined table names in a LEFT JOIN. For example, in this query of the Crunchbase dataset, the LEFT JOIN section:

```sql
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM datasets.crunchbase_companies companies
LEFT JOIN datasets.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
```

produces the same results as this query:

```sql
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM datasets.crunchbase_acquisitions acquisitions
RIGHT JOIN datasets.crunchbase_companies companies
    ON companies.permalink = acquisitions.company_permalink
```

The convention of always using LEFT JOIN probably exists to make queries easier to read and audit, but beyond that there isn’t necessarily a strong reason to avoid using RIGHT JOIN.

It’s worth noting that LEFT JOIN and RIGHT JOIN can be written as LEFT OUTER JOIN and RIGHT OUTER JOIN, respectively.

### FULL OUTER JOIN

LEFT JOIN and RIGHT JOIN each return unmatched rows from one of the tables—FULL JOIN returns unmatched rows from both tables. It is commonly used in conjunction with aggregations to understand the amount of overlap between two tables.

Here’s an example using the `Crunchbase companies and acquisitions` tables:

```sql
SELECT COUNT(CASE WHEN companies.permalink IS NOT NULL AND acquisitions.company_permalink IS NULL
                  THEN companies.permalink ELSE NULL END) AS companies_only,
       COUNT(CASE WHEN companies.permalink IS NOT NULL AND acquisitions.company_permalink IS NOT NULL
                  THEN companies.permalink ELSE NULL END) AS both_tables,
       COUNT(CASE WHEN companies.permalink IS NULL AND acquisitions.company_permalink IS NOT NULL
                  THEN acquisitions.company_permalink ELSE NULL END) AS acquisitions_only
FROM datasets.crunchbase_companies companies
FULL JOIN datasets.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
```

Output:

![strata scratch](assets/46b.png)

### UNION

SQL joins allow you to combine two datasets side-by-side, but UNION allows you to stack one dataset on top of the other. Put differently, UNION allows you to write two separate SELECT statements, and to have the results of one statement display in the same table as the results from the other statement.

Let’s try it out with the Crunchbase investment data, which has been split into two tables for the purposes of this lesson. The following query will display all results from the first portion of the query, then all results from the second portion in the same table:

```sql
SELECT *
   FROM datasets.crunchbase_investments_part1

 UNION

SELECT *
   FROM datasets.crunchbase_investments_part2
```
Output:

![strata scratch](assets/53.png)

Note that UNION only appends distinct values. More specifically, when you use UNION, the dataset is appended, and any rows in the appended table that are exactly identical to rows in the first table are dropped. If you’d like to append all the values from the second table, use UNION ALL. You’ll likely use UNION ALL far more often than UNION. In this particular case, there are no duplicate rows, so UNION ALL will produce the same results:

```sql
SELECT *
   FROM datasets.crunchbase_investments_part1

 UNION ALL

SELECT *
   FROM datasets.crunchbase_investments_part2
```
Output:

![strata scratch](assets/54.png)

SQL has strict rules for appending data:
- Both tables must have the same number of columns
- The columns must have the same data types in the same order as the first table

While the column names don’t necessarily have to be the same, you will find that they typically are. This is because most of the instances in which you’d want to use UNION involve stitching together different parts of the same dataset (as is the case here).

Since you are writing two separate SELECT statements, you can treat them differently before appending. For example, you can filter them differently using different WHERE clauses.

### SQL JOIN Comparison Operators

We want to find companies which were acquired by acquiriers from same city and within 3 years of their founding.

To satisfy these two conditions we need to know the founding date, acquisition date, company city and acquirier city.

We can find all of these except the founding date in `datasets.crunchbase_acquisitions`. To get the founding date we need `datasets.crunchbase_companies` and its `founded_at` column.

Our ON section consists of 3 conditions:
- 1. join key equality condition on permalink.
- 2. cities must be equal for both company and its acquirier
- 3. check to see if the founding date is at most 3 years apart from the acquisition date

The third condition shows how we can use any form of condition in our joins.
- We check `datasets.crunchbase_companies.founded_at` against `datasets.crunchbase_acquisitions.acquired_at` and neither of them are primary keys.

```sql
SELECT 
    companies.*
FROM
        datasets.crunchbase_acquisitions acquisitions 
    INNER JOIN
        datasets.crunchbase_companies companies
    ON 
        -- Join Key comparison - We want to match companies with their acquiriers
        acquisitions.company_permalink = companies.permalink AND
        -- Arbitrary equal comparison - Company acquirer must be from same city condition
        companies.city = acquisitions.acquirer_city AND
        -- Arbitrary less equal comparison - Company must be acquired at most 3 years from founding
        companies.founded_at <= acquisitions.acquired_at - 3
```

Note that the query we just discussed and the following query are different because the first query filters during join, while the second one filters and joins afterwards.

```sql
SELECT 
    companies.*
FROM
        datasets.crunchbase_acquisitions acquisitions 
    INNER JOIN
        datasets.crunchbase_companies companies
    ON 
        -- Join Key comparison - We want to match companies with their acquiriers
        acquisitions.company_permalink = companies.permalink AND
        -- Arbitrary equal comparison - Company acquirer must be from same city condition
        companies.city = acquisitions.acquirer_city
WHERE
    companies.founded_at <= acquisitions.acquired_at - 3
```

### SQL JOIN on multiple keys

Sometimes your datasets will have multiple columns as their primary key and to perfom an accurate join you must join on equality for all these keys.

Another benefit of joins is that sometimes they can be sped up if you use multiple keys. The reason for this is the indexing done by the database in the background.

```sql
SELECT 
    *
FROM datasets.crunchbase_companies companies 
    INNER JOIN 
        datasets.crunchbase_acquisitions acq
    ON 
    companies.permalink = acq.company_permalink AND
    companies.name      = acq.company_name
```

### SQL SELF JOIN

In addition to joining two different tables you can join a table to itself.

Joining a table to itself follows the same syntax and logic like other joins. The primary difference is that now you have the same table but under two different aliases. 

Notice that we use the same table but under different aliases (`companies1` and `companies2`)

```sql
        datasets.crunchbase_companies companies1 
    FULL JOIN 
        datasets.crunchbase_companies companies2
```

Joining a table to itself allows doing things regular group by can't do like our example here demonstrates. 

We want to find all companies which share a common category_code.
If we stopped here a groupby would do the job perfectly but we also want to only include
companies whose total funding is somewhat similar (that is the difference in total funding is less than 100000$) 

So we join the `datasets.crunchbase_companies` table against itself and make a few tests:
- The first test is to ensure that we do not include the same company in output. This is necessary because when we do a FULL JOIN each company is checked against each other company, including itself from the other table.
- Our second check is to ensure that they have a common category_code
- The third check is the funding difference check. ABS is the absolute function in SQL.

```sql
SELECT 
    companies1.category_code AS category_code,
    companies1.permalink AS company1_permalink,
    companies2.permalink AS company2_permalink,
    companies1.funding_total_usd AS company1_total_funding,
    companies2.funding_total_usd AS company2_total_funding,
    ABS(companies1.funding_total_usd  - companies2.funding_total_usd) AS funding_difference
FROM datasets.crunchbase_companies companies1 
    FULL JOIN 
        datasets.crunchbase_companies companies2
    ON 
        companies1.permalink <> companies2.permalink AND
        companies1.category_code = companies2.category_code AND
        ABS(companies1.funding_total_usd - companies2.funding_total_usd) <= 100000
ORDER BY funding_difference ASC
```