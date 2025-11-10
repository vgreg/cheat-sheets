# DuckDB CLI — Quick Cheat Sheet

*Practical reference for data exploration (Parquet, CSV, SQL summaries).*

## Launch & Basics 🦆

* Start CLI:

  ```bash
  duckdb
  ```

* Start with a file-backed DB:

  ```bash
  duckdb mydb.duckdb
  ```

* Run a query directly:

  ```bash
  duckdb -c "SELECT * FROM 'data.parquet' LIMIT 5;"
  ```

* Quit: `Ctrl-d` or `\q`
* Show help: `\?`
* List tables: `SHOW TABLES;`

---

## Working with Files 📂

### Parquet

* Query directly (no import):

  ```sql
  SELECT * FROM 'data.parquet' LIMIT 10;
  ```

* Multiple files:

  ```sql
  SELECT COUNT(*) FROM 'data/*.parquet';
  ```

* Inspect schema:

  ```sql
  DESCRIBE SELECT * FROM 'data.parquet';
  ```

### CSV

* Query directly:

  ```sql
  SELECT * FROM 'data.csv' LIMIT 5;
  ```

* With options:

  ```sql
  SELECT * FROM read_csv_auto('data.csv', header=True, sample_size=-1);
  ```

* Create a view for convenience:

  ```sql
  CREATE VIEW data AS SELECT * FROM read_csv_auto('data.csv');
  ```

---

## Exploring Data 🔍

* **Schema / Structure**

  ```sql
  DESCRIBE data;
  SHOW TABLES;
  ```

* **Preview**

  ```sql
  SELECT * FROM data LIMIT 10;
  ```

* **Count rows**

  ```sql
  SELECT COUNT(*) FROM data;
  ```

* **Unique values**

  ```sql
  SELECT DISTINCT column FROM data LIMIT 10;
  ```

* **Null counts**

  ```sql
  SELECT
    SUM(column IS NULL) AS nulls,
    COUNT(*) AS total
  FROM data;
  ```

* **Summary stats**

  ```sql
  SELECT
    COUNT(*) AS n,
    COUNT(DISTINCT col) AS unique_vals,
    MIN(col) AS min,
    MAX(col) AS max,
    AVG(col) AS mean,
    STDDEV(col) AS sd
  FROM data;
  ```

* **By group**

  ```sql
  SELECT category, COUNT(*) AS n, AVG(value) AS avg_val
  FROM data
  GROUP BY category
  ORDER BY n DESC;
  ```

---

## Sampling & Filtering 🎯

* Random sample:

  ```sql
  SELECT * FROM data USING SAMPLE 100 ROWS;
  SELECT * FROM data USING SAMPLE 1%;  -- percent
  ```

* Conditional subset:

  ```sql
  SELECT * FROM data WHERE value > 100;
  ```

* Limit / offset:

  ```sql
  SELECT * FROM data LIMIT 10 OFFSET 20;
  ```

---

## Table Management 🧱

* Create persistent table:

  ```sql
  CREATE TABLE t AS SELECT * FROM 'data.parquet';
  ```

* Drop table:

  ```sql
  DROP TABLE t;
  ```

* Save as Parquet:

  ```sql
  COPY (SELECT * FROM data) TO 'filtered.parquet' (FORMAT PARQUET);
  ```

* Save as CSV:

  ```sql
  COPY (SELECT * FROM data) TO 'summary.csv' (HEADER, DELIMITER ',');
  ```

---

## Introspection & Meta 🧭

* List loaded extensions:

  ```sql
  PRAGMA show_extensions;
  ```

* Show config:

  ```sql
  PRAGMA show;
  ```

* Query history: `↑` / `↓`
* Execution time: prefix with `\timing on`

---

## Common Extensions ⚙️

* Enable extensions (some auto-load on demand):

  ```sql
  INSTALL httpfs;  LOAD httpfs;   -- for S3/HTTPS parquet
  INSTALL json;    LOAD json;     -- for JSON files
  INSTALL spatial; LOAD spatial;  -- geo data
  ```

---

## Handy Shell Commands 🧰

* Run SQL file:

  ```bash
  duckdb < query.sql
  ```

* Export result to CSV:

  ```bash
  duckdb -c "COPY (SELECT * FROM 'data.parquet' LIMIT 10) TO 'out.csv' (HEADER, DELIMITER ',');"
  ```

* Pretty-print mode:

  ```sql
  .mode column
  .headers on
  ```

---

## Quick Patterns 💡

* Top-N values:

  ```sql
  SELECT col, COUNT(*) AS n FROM data GROUP BY col ORDER BY n DESC LIMIT 10;
  ```

* Correlation:

  ```sql
  SELECT corr(x, y) FROM data;
  ```

* Approx quantiles:

  ```sql
  SELECT quantile(value, 0.25), quantile(value, 0.5), quantile(value, 0.75) FROM data;
  ```

* Explore schema across files:

  ```sql
  SELECT * FROM parquet_metadata('data/*.parquet');
  ```

---

## Minimal Survival Kit

* Open file: `duckdb -c "SELECT * FROM 'data.parquet' LIMIT 10;"`
* Explore: `DESCRIBE`, `SELECT COUNT(*)`, `SELECT DISTINCT col`
* Summarize: `AVG()`, `STDDEV()`, `GROUP BY`
* Export: `COPY (...) TO 'file.parquet' (FORMAT PARQUET)`
* Extensions: `INSTALL httpfs; LOAD httpfs;`

*Think SQLite meets Pandas, but faster — all queries are just SQL on files.*
