# 🗄️ Database Parsing with LangChain

A hands-on Python project exploring **SQLite database ingestion and SQL-to-Document conversion** for Retrieval-Augmented Generation (RAG).

This notebook demonstrates how to create a relational SQLite database, define tables, insert sample records, inspect database schemas using LangChain's `SQLDatabase` utility, and transform relational data into context-rich LangChain `Document` objects.

It also explores how SQL joins can represent relationships between employees and projects in a document format suitable for downstream retrieval.

## 🎯 Learning Objectives

- Understand relational database ingestion for RAG.
- Create a SQLite database programmatically.
- Define tables and insert sample records using Python's `sqlite3` module.
- Inspect database schemas and DDL using LangChain.
- Convert database table information into LangChain documents.
- Preserve table-level metadata.
- Represent relationships between tables using SQL JOIN operations.
- Prepare relational data for downstream retrieval workflows.

## 📂 Project Structure

```text
RAG/
├── 0-DataIngestParsing/
│   ├── 6-Database Parsing/
│   │   ├── 6-databaseparsing.ipynb
│   │   └── readme.md
│   └── data/
│       └── databases/
│           └── company.db
```

**Note:** The `data/` directory is shared across the data-ingestion notebooks. This notebook creates and uses `data/databases/company.db` relative to its current working directory.

## 📘 Notebook Walkthrough

### 1. Creating the SQLite Database

The notebook uses Python's built-in `sqlite3` and `os` modules to create a database directory and connect to `company.db`.

```python
import sqlite3
import os

os.makedirs("data/databases", exist_ok=True)

conn = sqlite3.connect("data/databases/company.db")
cursor = conn.cursor()
```

This establishes the database connection and provides a cursor for executing SQL statements.

### 2. Creating Database Tables

The notebook creates two relational tables.

**Employees table**

| Column | Description |
|---|---|
| `id` | Employee identifier and primary key |
| `name` | Employee name |
| `role` | Professional role |
| `department` | Department name |
| `salary` | Employee salary |

**Projects table**

| Column | Description |
|---|---|
| `id` | Project identifier and primary key |
| `name` | Project name |
| `status` | Project status |
| `budget` | Project budget |
| `lead_id` | Employee identifier used to associate a project with its lead |

The notebook uses `lead_id` in a SQL JOIN but does not explicitly declare a foreign-key constraint.

### 3. Inserting Sample Data

The notebook inserts four employee records and four project records.

Example projects include:

- RAG Implementation
- Data Pipeline
- Customer Portal
- ML Platform

The records are inserted using `executemany()` with parameterized SQL statements. The transaction is committed before the connection is closed.

### 4. Inspecting the Database with SQLDatabase

The notebook uses LangChain's `SQLDatabase` utility to inspect the SQLite database.

```python
from langchain_community.utilities import SQLDatabase

db = SQLDatabase.from_uri(
    "sqlite:///data/databases/company.db"
)

print(db.get_usable_table_names())
print(db.get_table_info())
```

This demonstrates how to:

- Retrieve usable table names.
- Inspect table schemas and DDL.
- Understand the structure of a relational database before processing its contents.

### 5. Custom SQL-to-Document Conversion

The notebook defines a reusable function:

```python
sql_to_documents(db_path: str) -> List[Document]
```

The function uses `sqlite3` to discover database tables and transform table information into LangChain documents.

#### Strategy A — Table Overview Documents

For each discovered table, the function:

1. Retrieves the column information using `PRAGMA table_info`.
2. Queries the table's records.
3. Generates an overview containing the table name, column names, and record count.
4. Includes up to five sample records.
5. Creates a LangChain `Document` with the generated text and metadata.

The metadata includes:

| Field | Description |
|---|---|
| `source` | Database file path |
| `table_name` | Name of the database table |
| `num_records` | Number of records retrieved |
| `data_type` | Identifies the document as `sql_table` |

This creates compact, metadata-rich summaries of the database tables.

#### Strategy B — Employee-Project Relationship Document

The function executes a SQL JOIN to combine employee information with project information.

```sql
SELECT
    e.name,
    e.role,
    p.name AS project_name,
    p.status
FROM employees e
JOIN projects p
    ON e.id = p.lead_id;
```

The results are converted into readable statements describing which employees lead which projects and their current statuses.

The relationship document includes:

- `source` — database file path
- `data_type` — `sql_relationships`
- `query` — `employee_project_join`

**Key takeaway:** SQL JOIN operations can combine information from related tables into a single document, preserving relationships that would be difficult to understand from isolated table summaries.

### 6. Generating the Final Documents

The notebook invokes the custom processor:

```python
documents = sql_to_documents(
    "data/databases/company.db"
)
```

The function returns a list of LangChain `Document` objects containing table overviews and an employee-project relationship document.

These documents can be inspected or passed to subsequent processing stages, such as embedding generation and vector-store indexing.

The notebook prepares documents for a potential RAG pipeline; it does not implement end-to-end retrieval or question answering.

## 🔄 Database Ingestion Workflow

```text
SQLite Database
      |
      +---------------------------+
      |                           |
      v                           v
SQLDatabase Utility        Custom SQL Processor
      |                           |
      v                           v
Inspect Schema             Discover Tables
and DDL                    and Retrieve Records
                                  |
                       +----------+----------+
                       |                     |
                       v                     v
                 Table Overview       SQL JOIN Query
                       |                     |
                       v                     v
                Table Documents      Relationship Document
                       |                     |
                       +----------+----------+
                                  |
                                  v
                       LangChain Documents
                                  |
                                  v
                       Downstream RAG Pipeline
```

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| SQLite | Relational database |
| `sqlite3` | Database connections and SQL execution |
| LangChain Community | `SQLDatabase` utility |
| LangChain Core | `Document` representation |

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- Required Python dependencies.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/0-DataIngestParsing/6-Database Parsing"
```

### 3. Install dependencies

```bash
python -m pip install jupyter langchain-community langchain-core sqlalchemy
```

SQLite support is generally available through Python's standard-library `sqlite3` module.

### 4. Run the notebook

Open `6-databaseparsing.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

The notebook creates the database, inserts sample records, inspects the schema, and converts database information into LangChain documents.

## ⚠️ Current Limitations

- The notebook uses a small, synthetic SQLite database.
- Table overview documents contain up to five sample records per table.
- The relationship query is specific to the employees and projects tables.
- The SQL JOIN returns only matching employee-project relationships.
- Table summaries do not make every individual database record independently searchable.
- The notebook does not implement natural-language-to-SQL generation.
- Documents are not embedded or indexed in a vector database.
- Retrieval quality and answer accuracy are not benchmarked.

## 🔮 Future Improvements

- Add row-level document generation.
- Generalize SQL-to-Document conversion for different database schemas.
- Preserve additional relationship metadata.
- Explore `SQLDatabaseLoader` as an alternative ingestion approach.
- Add validation for empty tables and missing database files.
- Connect generated documents to embeddings and a vector store.
- Evaluate retrieval quality using employee and project questions.

## 📚 Key Takeaway

Database ingestion for RAG requires useful representations of both the database schema and its underlying information.

This notebook demonstrates SQLite database creation, schema inspection, table-level document generation, and relationship extraction using SQL JOIN operations.

These techniques provide a foundation for preparing relational data for retrieval-based AI applications.

## 👨‍💻 Author

**Aadi**
