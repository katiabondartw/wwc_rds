# Database Full Text Search with AWS RDS

## Before the workshop
- install [pgAdmin](https://www.pgadmin.org/download/)
- refresh knowledge in basic SQL:

[SQL SELECT statement](http://www.w3schools.com/sql/sql_select.asp)


[Indexes](http://postgresguide.com/performance/indexes.html)

## Set up connection to RDS read replica instance
### Create connection:

Host: wwc-workshop-read.cjfbgrnzzler.ap-southeast-2.rds.amazonaws.com

Port: 5432 (default)

Username: wwc

Password: Generic1

## Verify the database connection is read-only

### See the initial value in the first raw
`SELECT * FROM products WHERE id=1`

### See the updated value in the first raw after mofification done by the presenter
`SELECT * FROM products WHERE id=1`

### Try to update the first raw and see the error message
`UPDATE products SET description='illegal modification' WHERE id=1`


## Follow the demo
The following are the queries which will be demonstrated during the demo. You can execute them from your laptop:
### ILIKE
`SELECT * FROM products WHERE title ILIKE 'fair' OR description ILIKE 'fair'`

`SELECT * FROM products WHERE title ILIKE 'fair%' OR description ILIKE 'fair%'`

`SELECT * FROM products WHERE title ILIKE '%fair%' OR description ILIKE '%fair%'`

### Full text search
`SELECT * FROM products
WHERE to_tsvector('english', title || ' ' || description) @@ to_tsquery('english', 'fair')`

#### Measure execution time / see query plan
`EXPLAIN ANALYZE SELECT * FROM products
WHERE to_tsvector('english', title || ' ' || description) @@ to_tsquery('english', 'fair')`

## Self-paced exercises
### Full text search options
1. Subword: `:*`, e.g. `to_tsquery('english', 'cat:*')`
2. Two and more words to be found: `&`, e.g. `to_tsquery('english', 'cat & dog')`
3. One of the given words to be found: `|`, e.g. `to_tsquery('english', 'cat | dog')`
4. Rows with the given word to be excluded: `!`, e.g. `to_tsquery('english', '!black & cat')`

### Exercise 1 - AND condition
Find records with both words "cat" and "adaptive" in either title or description.

Expected result: 16 rows

### Exercise 2 - Subwords
Find all records which have words starting with "bot" in the title.

Expected result: 8 rows

### Exercise 3 - Exclude
Find records containing the word "machine", but not containing "gift" in either title or description. 

Expected result: 2 rows

### Exercise 4 - Stemming
Execute the query to find out the stem of the word "programming":

`select to_tsvector('english', 'programming')`

Then perform full text search with this word as a search phrase to see all the matching forms of this word.

Expected result: 4 rows, 2 forms of the word "program"

### Exercise 5 - Stop words
Stop words is one of predefined dictionaries(a list of words) for many languages provided by postgreSQL. Stop words are words that are very common, appear in almost every document, and have no discrimination value. Therefore, they can be ignored in the context of full text searching. For example, every English text contains words like a and the, so it is useless to store them in an index.
The words "the" and "will" are included into the stop words list. For both "the" and "will" do the following:

- Find all records starting with "the" and "will" (not using subword!) in description using ILIKE

Expected result: 1 row for both words(therefore we know there is record contain words "the" and "will")

- When searching with full text search SQL, these two words are ignored.

Expected result: 0 rows for both words

### Exercise 6 - Ranking
Read about PostgreSQL full text search ranking at https://www.postgresql.org/docs/9.1/static/textsearch-controls.html#TEXTSEARCH-RANKING.

## Further reading
- [PostgreSQL Full Text Search](https://www.postgresql.org/docs/9.1/static/textsearch-intro.html)
- [Other features and hints on PostgreSQL](http://postgresguide.com/)
- [PostgreSQL AWS RDS](https://aws.amazon.com/rds/postgresql/)
