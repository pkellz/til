# Covering Indexes in Postgres

PostgreSQL supports the concept of covering indexes - *indexes that eliminate the need to do a lookup from the actual table by including all necessary fields/columns in the index itself*. If I had a commonly-occurring query such as:

```SQL
SELECT vin, make, model, FROM vehicles WHERE vin = '123xyz'
```

I could create an index based on the VIN:

```SQL
CREATE INDEX idx_vehicles_vin ON vehicles(vin)
```

This would create a single column index where the query would 1) find the row in the index that has a vin of "123xyz", then 2) look up the rest of the information (make and model) from the actual table now that we know where to find it.

But we can cut out step 2 completely if we just included vin, make, and model in the index from the jump. We can create a "covering index" that still only indexes based off the VIN, but will also include the make and model columns in the index.

```SQL
-- Store the 'make' and 'model' in the index alongside the 'vin' 
CREATE INDEX idx_faculty_users ON users(email) INCLUDE (make, model)
```
