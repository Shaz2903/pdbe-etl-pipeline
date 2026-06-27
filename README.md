# PDBe Structural Data ETL Pipeline

A Python-based ETL pipeline that extracts macromolecular structure data from the Protein Data Bank in Europe (PDBe) REST API, transforms and validates it, and loads it into a structured relational database with indexing.

Built as a portfolio project to demonstrate data engineering practices applied to real-world open biological data.

---

## What It Does

- Extracts structural data records from the PDBe Search API using configurable query terms
- Transforms nested JSON responses into a clean tabular format using pandas
- Handles data quality issues including null values, duplicate records, and list-type fields
- Loads cleaned data into a SQLite database (designed for direct portability to PostgreSQL)
- Creates indexes on key fields to support analytical querying

---

## Pipeline Steps

1. Extract: Calls the PDBe REST API and retrieves up to 200 records per query
2. Transform: Parses JSON, flattens nested fields, standardises organism names
3. Validate: Checks for nulls, removes duplicates, enforces data types
4. Load: Writes to a relational table with primary key and index creation
5. Query: Verifies loaded records via SQL read-back

---

## Data Source

PDBe Search API: https://www.ebi.ac.uk/pdbe/search/pdb/select

The Protein Data Bank in Europe (PDBe) is part of the Worldwide Protein Data Bank and is maintained by EMBL-EBI. It provides open access to 3D structural data on macromolecules including proteins and nucleic acids.

---

## Tech Stack

- Python 3.10
- pandas
- requests
- SQLite (local development database, schema portable to PostgreSQL)
- Jupyter Notebook

---

## Database Schema

Table: pdbe_structures

| Column     | Type    | Description                          |
|------------|---------|--------------------------------------|
| pdb_id     | TEXT    | Unique PDB entry identifier          |
| title      | TEXT    | Structure title                      |
| resolution | REAL    | Resolution in Angstroms              |
| organism   | TEXT    | Scientific name of source organism   |

Indexes: pdb_id (primary), resolution

---

## PostgreSQL Portability

The pipeline is built with PostgreSQL migration in mind. The SQLite schema maps directly to PostgreSQL as follows:

- TEXT fields map to VARCHAR or TEXT
- REAL maps to NUMERIC or FLOAT8
- Index creation syntax is identical
- The load step uses pandas to_sql which supports SQLAlchemy PostgreSQL engines

To switch to PostgreSQL, replace the sqlite3 connection with a SQLAlchemy engine:

```python
from sqlalchemy import create_engine
engine = create_engine("postgresql://user:password@host:5432/dbname")
df.to_sql("pdbe_structures", engine, if_exists="replace", index=False)
```

---

## How to Run

1. Clone the repository
2. Install dependencies: pip install requests pandas
3. Open pdbe_etl_pipeline.ipynb in Jupyter
4. Run all cells in order, or call run_etl() with a query term of your choice

```python
run_etl("protein", rows=200)
```

---

## Sample Output

| pdb_id | title                              | resolution | organism      |
|--------|------------------------------------|------------|---------------|
| 2awf   | Structure of human Ubiquitin...    | 2.10       | Homo sapiens  |
| 1gq2   | Malic Enzyme from Pigeon Liver     | 2.50       | Columba livia |

---

## Author

Shaheena Shajira
Data Engineer | MSc Data Science, University of East Anglia
GitHub: https://github.com/Shaz2903
