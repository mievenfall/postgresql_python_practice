# PostgreSQL Python Practice

Practice using Python to interact with a PostgreSQL database using `psycopg2`.

This repository follows the [PostgreSQL Python](https://neon.com/postgresql/python).

## Topics

The exercises cover:

- Connecting to a PostgreSQL database
- Creating tables
- Inserting data
- Updating data
- Querying data using:
  - `fetchone()`
  - `fetchall()`
  - `fetchmany()`
- Working with transactions
- Calling PostgreSQL functions
- Calling stored procedures
- Writing and reading BLOB data
- Deleting data

## Project Structure

```text
postgresql_python_practice/
├── call_function.py
├── call_stored_procedure.py
├── config.py
├── connect.py
├── create_tables.py
├── database.ini.example
├── delete.py
├── get_part_vendors.py
├── get_vendors_fetchall.py
├── get_vendors_fetchone.py
├── insert.py
├── read_blob.py
├── requirements.txt
├── transaction.py
├── update.py
└── write_blob.py
```

## Setup

### 1. Create a virtual environment

```bash
python3 -m venv venv
source venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure the PostgreSQL connection

Copy the example configuration file:

```bash
cp database.ini.example database.ini
```

Update `database.ini` with your PostgreSQL connection information:

```ini
[postgresql]
host=localhost
database=suppliers
user=your_username
password=your_password
port=5432
```

`database.ini` is ignored by Git to prevent database credentials from being committed.

## Exercises

### Connect to PostgreSQL

```bash
python3 connect.py
```

Tests the connection to the PostgreSQL database.

### Create Tables

```bash
python3 create_tables.py
```

Creates the tables used by the exercises.

### Insert Data

```bash
python3 insert.py
```

Practices inserting single and multiple records into PostgreSQL.

### Update Data

```bash
python3 update.py
```

Updates existing records in the database.

### Query Data

Using `fetchall()`:

```bash
python3 get_vendors_fetchall.py
```

Using `fetchone()`:

```bash
python3 get_vendors_fetchone.py
```

Using `fetchmany()`:

```bash
python3 get_part_vendors.py
```

### Transaction

```bash
python3 transaction.py
```

Practices executing multiple database operations within a transaction.

### PostgreSQL Function

Before running the Python script, create the `get_parts_by_vendor` function in the PostgreSQL `suppliers` database:

```sql
CREATE OR REPLACE FUNCTION get_parts_by_vendor(id INTEGER)
  RETURNS TABLE(part_id INTEGER, part_name VARCHAR) AS
$$
BEGIN
 RETURN QUERY

 SELECT parts.part_id, parts.part_name
 FROM parts
 INNER JOIN vendor_parts on vendor_parts.part_id = parts.part_id
 WHERE vendor_id = id;

END; $$

LANGUAGE plpgsql;
```

Then call the PostgreSQL function from Python:

```bash
python3 call_function.py
```

### Stored Procedure

Before running the Python script, create the `add_new_part` stored procedure in the PostgreSQL `suppliers` database:

```sql
CREATE OR REPLACE PROCEDURE add_new_part(
	new_part_name varchar,
	new_vendor_name varchar
)
AS $$
DECLARE
	v_part_id INT;
	v_vendor_id INT;
BEGIN
	-- insert into the parts table
	INSERT INTO parts(part_name)
	VALUES(new_part_name)
	RETURNING part_id INTO v_part_id;

	-- insert a new vendor
	INSERT INTO vendors(vendor_name)
	VALUES(new_vendor_name)
	RETURNING vendor_id INTO v_vendor_id;

	-- insert into vendor_parts
	INSERT INTO vendor_parts(part_id, vendor_id)
	VALUEs(v_part_id,v_vendor_id);

END;
$$
LANGUAGE PLPGSQL;
```

Then call the stored procedure from Python:

```bash
python3 call_stored_procedure.py
```

### BLOB Data

Write image data to PostgreSQL:

```bash
python3 write_blob.py
```

Read image data from PostgreSQL:

```bash
python3 read_blob.py
```

The BLOB exercise stores binary image data in the `part_drawings` table using PostgreSQL `BYTEA`.

### Delete Data

```bash
python3 delete.py
```

Deletes records from PostgreSQL using Python.

## Reference

- [PostgreSQL Python](https://neon.com/postgresql/python)
