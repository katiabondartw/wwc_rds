# PostgreSQL Full Text Search on AWS RDS

## Before the workshop
- install [pgAdmin](https://www.pgadmin.org/download/)
- refresh knowledge in basic SQL:
[SQL SELECT statement](http://www.w3schools.com/sql/sql_select.asp)
[Indexes](http://postgresguide.com/performance/indexes.html)

## Set up connection
### Create connection:

Host: TBC

Port: 5432 (default)

Username: wwc

Password: Generic1


### Configure connection
Run SQL query:
`SET default_text_search_config = "pg_catalog.english";`

If a connection drops, this query needs to be executed again. Otherwise the results of some queries may vary.

## Follow the demo
The following are the queries which will be demonstrated during the demo. You can also execute them from your laptop:
### ILIKE
`SELECT * FROM products WHERE title ILIKE 'fair'`
`SELECT * FROM products WHERE title ILIKE 'fair%'`
`SELECT * FROM products WHERE title ILIKE '%fair%'`
`SELECT * FROM products WHERE title ILIKE '%fair%' OR description ILIKE '%fair%'`

#### Measure execution time / see query plan
`EXPLAIN ANALYZE SELECT * FROM products WHERE title ILIKE '%fair%' OR description ILIKE '%fair%'`

### Use full text search
`SELECT * FROM products
WHERE to_tsvector('english', title || ' ' || description) @@ to_tsquery('english', 'english', 'fair')`

#### Measure execution time / see query plan
`EXPLAIN ANALYZE SELECT * FROM products
WHERE to_tsvector('english', title || ' ' || description) @@ to_tsquery('english', 'fair')`

## Self-paced exercises
### Full text options
1. Subword: `:*`, e.g. `to_tsquery('english', 'cat:*')`
2. Two and more words to be found: `&`, e.g. `to_tsquery('english', 'cat & dog')`
3. One of the given words to be found: `|`, e.g. `to_tsquery('english', 'cat | dog')`
4. Rows with the given word to be excluded: `!`, e.g. `to_tsquery('english', '!black & cat')`

### Exercise 1
Find records with both words "cat" and "adaptive" in either title or description.

### Exercise 2
Find all records which have words starting with "bot" in title only.

### Exercise 3
Find records containing the word "machine", but not containing "gift" in either title or description. 

### Exercise 4
Explore the stem of the word "programming" (`select to_tsvector('english', 'programming')`) and find records with this word's form in the products table.

## Further reading
- [Controlling text search](https://www.postgresql.org/docs/8.3/static/textsearch-controls.html)
- [Other features and hints on PostgreSQL](http://postgresguide.com/)
- [PostgreSQL AWS RDS](https://aws.amazon.com/rds/postgresql/)
