# PostgreSQL

## BASICS

### Default values

```sql
CREATE TABLE user (
    ...
    age INT DEFAULT 10,
    now_date DATE DEFAULT CURRENT_TIMESTAMP
);
```

### Generated Columns
- Two kinds of generated columns: **`stored`** and **`virtual`**
```sql
CREATE TABLE user (
    ...
    height_cm numeric,
    height_in numeric GENERATED ALWAYS AS (height_cm / 2.54) STORED
);
```

- `STORED`  indicates, value is generated at the time of insertion to the table. 
- Value cannot be specified for the generated columns at the time of insertion or update operation 

## Constraints

### Check Constraints 
- Check for inserted values for a column
```sql
CREATE TABLE products (
    ...
    price INT CHECK (price > 0)
);
```
- Products with positive price is allowed, in case of negative price, it will throw an error

#### Named Constraints
- Give name to the constraint so that error makes sense while reading
```sql
CREATE TABLE products (
    ...
    price INT CONSTRAINT positive_price CHECK (price > 0)
);
```

- Also we can have table level constraints that can check multiple columns. can have multiple constraints.
```sql
CREATE TABLE products (
    ...
    price INT CHECK (price > 0),                            -- Column constraint
    discounted_price INT CHECK (discounted_price > 0),      -- Column constraint
    CHECK (price > discounted_price)                        -- Table level constraint
);
```


### NOT NULL
- Ensures the column is not null
```sql
CREATE TABLE products (
    product_no INT NOT NULL,
    name TEXT NOT NULL
    ...                     
);
```

### UNIQUE
- Ensure a column in each row has unique value
 ```sql
CREATE TABLE products (
    product_no INT UNIQUE,
    ...
);

--OR--

CREATE TABLE products (
    product_no INT,
    ...
    UNIQUE(product_no)
);
 ```

 ### PRIMARY KEY
 - Unique identifier for row. 
 - Value should be unique and not null
```sql
CREATE TABLE products (
    product_no INT PRIMARY KEY,
    ...
);

--OR--

CREATE TABLE products (
    product_no INT,
    ...
    PRIMARY KEY(product_no)
);
```

### FOREIGN KEY
- Value in a column must match values appearing in some other table
```sql
CREATE TABLE products (
    product_no integer PRIMARY KEY,
    ...
);



CREATE TABLE orders (
    ...
    product_no integer REFERENCES products (product_no),
    ...
);

--OR--

CREATE TABLE orders (
    ...
    product_no integer ,
    ...
    FOREIGN KEY(product_no) REFERENCES products (product_no)
);

```

## Modifying Tables

### Add Column
```sql
--Syntax
ALTER TABLE <table_name> 
ADD COLUMN <column_name> <type>;

--Example
ALTER TABLE products 
ADD COLUMN description TEXT;
```
- We can also add constraints, default values etc.

### Remove column
```sql
--Syntax
ALTER TABLE <table_name> 
DROP COLUMN <column_name>;

--Example
ALTER TABLE products 
DROP COLUMN description ;
```

### Remove Constraint

```sql
--Syntax
ALTER TABLE <table_name> 
DROP CONSTRAINT <constraint_name>;  -- At table level

ALTER TABLE <table_name> 
ALTER COLUMN <column_name> 
DROP CONSTRAINT <constraint_name>;  -- At column level

--Example
CREATE TABLE products (
    ...
    price INT CONSTRAINT positive_price CHECK (price > 0)
);

ALTER TABLE products
ALTER COLUMN price
DROP CONSTRAINT positive_price;

```

### Change Default value
```sql
--Syntax
ALTER TABLE <table_name> 
ALTER COLUMN <column_name>
SET DEFAULT <value>;

--Example
ALTER TABLE products
ALTER COLUMN price
SET DEFAULT 100;
```

### Change Columns Data type 
```sql
--Syntax
ALTER TABLE <table_name> 
ALTER COLUMN <column_name>
TYPE <new_type>;

--Example
ALTER TABLE products
ALTER COLUMN price
TYPE NUMERIC(10,2);
```

### Rename Column
```sql
--Syntax
ALTER TABLE <table_name>
RENAME COLUMN <column_name> TO <new_column_name>

--Example
ALTER TABLE products 
RENAME COLUMN product_no TO product_number;
```

### Rename Table
```sql
--Syntax
ALTER TABLE <table_name> 
RENAME TO <new_table_name>

--Example
ALTER TABLE products
RENAME TO items
```


## Data Manipulation

### Inserting Data

```sql
--Table Creation
CREATE TABLE products (
    product_no INT,
    name TEXt,
    price NUMERIC
);
```
```sql
INSERT INTO products 
VALUES (1, 'Cheese', 9.99);     --Values listed as per which column appear in which order  

--OR--

INSERT INTO products 
    (product_no, price )        --Specify the cloumns and order 
VALUES (1, 9.99);

--OR--

INSERT INTO products 
    (product_no, name, price) 
VALUES                          --Insert multiple columns
    (1, 'Cheese', 9.99),
    (2, 'Bread', 1.99),
    (3, 'Milk', 2.99);
```

### Update Data
```sql
--Syntax
UPDATE <table_name>
SET <column_name> = <value>
WHERE <condition>;              --Optional

--Example 
UPDATE products
SET price=100
WHERE product_no=1;

--OR--
UPDATE products
SET price=100, product_name='apple'  --Update multiple columns together
WHERE product_no=1;
```

### Deleting Data
```sql
--Syntax
DELETE FROM <table_name>
WHERE <condition>;

--Example 
DELETE FROM products
WHERE product_no=1;
```

### Returning Data from modified rows
- Obtain Data from modified rows while they are being manipulated
```sql
INSERT INTO users 
    (firstname, lastname) 
VALUES ('Joe', 'Cool') RETURNING id;
```
- `RETURNING` keyword is used to specify which columns should be returned.

## Queries
```sql
--Syntax
SELECT <column_names>
FROM <table_name>
WHERE <condition>
GROUP BY <column_name> -- Group rows based on the column value
HAVING <condition> 
ORDER BY <column_name> <order>  -- Used for Sorting, order can be `DESC` or `ASC`(default)
```

### Distinct
- Get Only the distict values from a cplumn that may contain duplicate values
```sql
SELECT DISTINCT age 
FROM users;
```


## Combining Queries
```sql
<query1> UNION [ALL] <query2>
<query1> INTERSECT [ALL] <query2>
<query1> EXCEPT [ALL] <query2>
```
3 Ways

### `UNION` :   
   - Appends the result of query2 to the result of query1
   - Duplicates are eliminated unless `UNION ALL` is used.
### `INTERSECT` :
   - Returns All rows that are both in the result of query1 and in the result of query2.
   -  Duplicate rows are eliminated unless `INTERSECT ALL` is used.
### `EXCEPT`
- Returns all rows that are in the result of query1 but not in the result of query2. 
- Duplicates are eliminated unless `EXCEPT ALL` is used.


### PAGINATION with `LIMIT` and `OFFSET`
- retrieve portion of rows.
- `LIMIT` sets the number of records to be retrieved
- `OFFSET` sets the number of records to be skipped before it starts retrieving
```sql
--Syntax
SELECT <column_names>
FROM <table_name>
...
LIMIT <number> OFFSET <number>

--Example
```

### `With` queries
- Used to define temporary tables that exist just for one query.
```sql
WITH regional_sales AS (
    SELECT region, SUM(amount) AS total_sales
    FROM orders
    GROUP BY region
),

SELECT * FROM regional_sales;
```


## Data Types

| SR. | Name                                    | Aliases            | Description                                                      |
| --- | --------------------------------------- | ------------------ | ---------------------------------------------------------------- |
| 1   | bigint                                  | int8               | signed eight-byte integer                                        |
| 2   | bigserial                               | serial8            | autoincrementing eight-byte integer                              |
| 3   | bit [ (n) ]                             |                    | fixed-length bit string                                          |
| 4   | bit varying [ (n) ]                     | varbit [ (n) ]     | variable-length bit string                                       |
| 5   | boolean                                 | bool               | logical Boolean (true/false)                                     |
| 6   | box                                     |                    | rectangular box on a plane                                       |
| 7   | bytea                                   |                    | binary data (“byte array”)                                       |
| 8   | character [ (n) ]                       | char [ (n) ]       | fixed-length character string                                    |
| 9   | character varying [ (n) ]               | varchar [ (n) ]    | variable-length character string                                 |
| 10  | cidr                                    |                    | IPv4 or IPv6 network address                                     |
| 11  | circle                                  |                    | circle on a plane                                                |
| 12  | date                                    |                    | calendar date (year, month, day)                                 |
| 13  | double precision                        | float8             | double precision floating-point number (8 bytes)                 |
| 14  | inet                                    |                    | IPv4 or IPv6 host address                                        |
| 15  | integer                                 | int, int4          | signed four-byte integer                                         |
| 16  | interval [ fields ] [ (p) ]             |                    | time span                                                        |
| 17  | json                                    |                    | textual JSON data                                                |
| 18  | jsonb                                   |                    | binary JSON data, decomposed                                     |
| 19  | line                                    |                    | infinite line on a plane                                         |
| 20  | lseg                                    |                    | line segment on a plane                                          |
| 21  | macaddr                                 |                    | MAC (Media Access Control) address                               |
| 22  | macaddr8                                |                    | MAC (Media Access Control) address (EUI-64 format)               |
| 23  | money                                   |                    | currency amount                                                  |
| 24  | numeric [ (p, s) ]                      | decimal [ (p, s) ] | exact numeric of selectable precision                            |
| 25  | path                                    |                    | geometric path on a plane                                        |
| 26  | pg_lsn                                  |                    | PostgreSQL Log Sequence Number                                   |
| 27  | pg_snapshot                             |                    | user-level transaction ID snapshot                               |
| 28  | point                                   |                    | geometric point on a plane                                       |
| 29  | polygon                                 |                    | closed geometric path on a plane                                 |
| 30  | real                                    | float4             | single precision floating-point number (4 bytes)                 |
| 31  | smallint                                | int2               | signed two-byte integer                                          |
| 32  | smallserial                             | serial2            | autoincrementing two-byte integer                                |
| 33  | serial                                  | serial4            | autoincrementing four-byte integer                               |
| 34  | text                                    |                    | variable-length character string                                 |
| 35  | time [ (p) ] [ without time zone ]      |                    | time of day (no time zone)                                       |
| 36  | time [ (p) ] with time zone             | timetz             | time of day, including time zone                                 |
| 37  | timestamp [ (p) ] [ without time zone ] |                    | date and time (no time zone)                                     |
| 38  | timestamp [ (p) ] with time zone        | timestamptz        | date and time, including time zone                               |
| 39  | tsquery                                 |                    | text search query                                                |
| 40  | tsvector                                |                    | text search document                                             |
| 41  | txid_snapshot                           |                    | user-level transaction ID snapshot (deprecated; see pg_snapshot) |
| 42  | uuid                                    |                    | universally unique identifier                                    |
| 43  | xml                                     |                    | XML data                                                         |




### Array

#### Create array
```sql
CREATE TABLE road_map (
    locations string[],
    pathMatrix INTEGER[2][3]  --Specify Exact size also
)
```
#### Insert INTO array
- While inserting, enclose the values within flower brackets and separate them with delimeter/ coma
```sql
INSERT INTO road_map 
VALUES ( 
    {'karkala','maglaore','udupi'},   
    {{1,2,3},{3,2,2},{1,1,2}}
)

```

- Or Use `ARRAY` constructor, separate elemets using a coma and wrap everything inside the square bracket. 
```sql
VALUES ( 
    ARRAY['karkala','maglaore','udupi'],   
    ARRAY[[1,2,3],[3,2,2],[1,1,2]]
)
```

#### Concat 2 array
```sql
SELECT ARRAY[1,2] || ARRAY[3,4];
```
