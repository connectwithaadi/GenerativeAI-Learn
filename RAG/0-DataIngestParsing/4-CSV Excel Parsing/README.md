
# 📊 CSV & Excel Data Parsing with LangChain

A hands-on Python project exploring **structured data ingestion for Retrieval-Augmented Generation (RAG)** using Pandas and LangChain.

This notebook demonstrates how to generate sample CSV and Excel files, load CSV records as LangChain documents, transform structured rows into metadata-rich text, and process Excel workbooks containing multiple sheets.

The goal is to understand how structured data can be represented as documents for downstream retrieval and question-answering applications.

---

## 🎯 Learning Objectives

- Understand structured data ingestion for RAG.
- Generate CSV and Excel files using Pandas.
- Load CSV records using LangChain's `CSVLoader`.
- Convert CSV rows into custom LangChain documents.
- Preserve row-level metadata for filtering and traceability.
- Read Excel workbooks containing multiple sheets.
- Convert each Excel sheet into a separate document.
- Explore `UnstructuredExcelLoader` in element mode.
- Compare row-based, sheet-based, and element-based document representations.

---

## 📂 Project Structure

```text
4-CSV Excel Parsing/
├── 4-csvexcelparsing.ipynb
└── README.md
```

The notebook generates its sample datasets under:

```text
data/
└── structured_files/
    ├── products.csv
    └── inventory.xlsx
```

These files are created when the corresponding notebook cells execute successfully.

---

## 📘 Notebook Walkthrough

### 1. Generating Sample Structured Data

The notebook uses Pandas to create a sample product inventory containing:

| Column | Description |
|---|---|
| `Product` | Product name |
| `Category` | Product category |
| `Price` | Product price |
| `Stock` | Available stock |
| `Description` | Product description |

The dataset contains five example products, including laptops, mice, keyboards, monitors, and webcams.

Two files are generated:

- `products.csv` — the product dataset in CSV format.
- `inventory.xlsx` — an Excel workbook containing multiple sheets.

The Excel workbook includes:

- **Products:** The complete product dataset.
- **Summary:** Aggregated category information with item counts and total values.

This generated data provides a consistent input for experimenting with different ingestion strategies.

### 2. CSV Processing with CSVLoader

The notebook uses LangChain's `CSVLoader` to load the product CSV file.

Configuration includes:

- File path: `data/structured_files/products.csv`
- Encoding: UTF-8
- Delimiter: Comma
- Quote character: Double quotation mark

The loader creates one LangChain `Document` per CSV row.

Each document contains:

- `page_content` — the row represented as text.
- `metadata` — information associated with the source file and row.

The notebook inspects the loaded documents, displays their content and metadata, and prints the number of records loaded.

**Key takeaway:** Row-based ingestion is useful when individual records need to be retrieved independently.

### 3. Custom CSV Processing

The notebook defines a reusable function:

```python
process_csv_intelligently(filepath: str) -> List[Document]
```

This function uses Pandas to read the CSV file and converts each row into a structured text representation.

Example document content:

```text
Product Information:
Name: Laptop
Category: Electronics
Price: $999.99
Stock: 50 units
Description: High-performance laptop with 16GB RAM and 512GB SSD
```

Each generated document contains custom metadata:

| Field | Purpose |
|---|---|
| `source` | Source CSV file path |
| `row_index` | Zero-based row index |
| `product_name` | Product name |
| `category` | Product category |
| `price` | Product price |
| `data_type` | Identifies the document as `product_info` |

The function returns a list of LangChain `Document` objects.

**Why this approach matters:** Custom document creation provides control over how structured records are represented as natural-language text and which fields are preserved as metadata.

### 4. Excel Processing with Pandas

The notebook defines another reusable function:

```python
process_excel_with_pandas(filepath: str) -> List[Document]
```

This function reads all sheets from an Excel workbook and converts each sheet into a separate LangChain document.

For every sheet, it:

1. Identifies the sheet name.
2. Reads the sheet into a Pandas DataFrame.
3. Records the column names and row count.
4. Converts the tabular data into a text representation using `DataFrame.to_string()`.
5. Creates a LangChain `Document` containing the resulting text and metadata.

The metadata includes:

- `source`
- `sheet_name`
- `num_rows`
- `num_columns`
- `data_type`

For the generated workbook, the function processes the `Products` and `Summary` sheets separately.

**Key takeaway:** Sheet-aware ingestion retains the distinction between different logical sections of an Excel workbook.

### 5. Excel Processing with UnstructuredExcelLoader

The notebook also explores LangChain's `UnstructuredExcelLoader` using:

```python
UnstructuredExcelLoader(
    "data/structured_files/inventory.xlsx",
    mode="elements"
)
```

The loading operation is wrapped in a `try-except` block so that exceptions can be displayed for debugging.

The notebook then inspects the resulting documents.

This section provides an alternative approach to Excel ingestion. Actual extraction behavior depends on the loader, its dependencies, the workbook structure, and the installed package versions.

**Note:** The notebook does not include a systematic comparison of extraction accuracy between Pandas and `UnstructuredExcelLoader`.

---

## 🔄 Data Ingestion Workflow

```text
Generated Product Data
          |
          +-----------------------+
          |                       |
          v                       v
     products.csv             inventory.xlsx
          |                       |
     +----+-----+            +----+------+
     |          |            |           |
     v          v            v           v
 CSVLoader   Custom CSV    Pandas     Unstructured
             Processing    Processing    Loader
     |          |            |           |
     v          v            v           v
 Row-Based   Rich Metadata  Sheet-Based  Element-Based
 Documents   Documents      Documents    Documents
     |          |            |           |
     +----------+------------+-----------+
                        |
                        v
             Downstream RAG Pipeline
```

The notebook prepares documents for potential downstream retrieval; it does not implement the complete embedding, vector-store, or RAG question-answering pipeline.

---

## 📊 Processing Strategy Comparison

| Strategy | Document granularity | Main advantage |
|---|---|---|
| `CSVLoader` | One document per row | Straightforward record-level ingestion |
| Custom CSV processing | One document per row | Control over text formatting and metadata |
| Pandas Excel processing | One document per sheet | Preserves sheet-level organization |
| `UnstructuredExcelLoader` | Depends on loader output and mode | Alternative element-based extraction |

There is no universally best representation. The right choice depends on the questions users will ask, the size of the data, and the retrieval strategy.

For example, product lookup may benefit from row-based documents, while questions involving relationships across many rows may require additional aggregation or a more structured querying approach.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| Pandas | CSV and Excel data manipulation |
| LangChain Community | CSV and Excel document loaders |
| LangChain Core | `Document` representation |
| OpenPyXL | Excel workbook reading and writing |

`UnstructuredExcelLoader` may require additional dependencies depending on the workbook and environment.

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible Python version.
- Jupyter Notebook or VS Code.
- `pip` or another Python package manager.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project directory

```bash
cd "RAG/0-DataIngestParsing/4-CSV Excel Parsing"
```

### 3. Install dependencies

```bash
python -m pip install jupyter pandas openpyxl langchain-core langchain-community
```

For the `UnstructuredExcelLoader` section, install the additional dependency:

```bash
python -m pip install unstructured
```

If the loader reports a missing optional dependency, follow the error message and the relevant package documentation.

### 4. Run the notebook

Open `4-csvexcelparsing.ipynb` in Jupyter Notebook or VS Code.

Execute the cells in order. The notebook creates the sample files, loads CSV records, processes Excel sheets, and displays the generated documents.

---

## ⚠️ Current Limitations

- The notebook uses a small, synthetic product dataset.
- Custom CSV processing assumes the expected column names are present.
- The custom CSV processor converts prices into metadata values without explicit schema validation.
- Pandas Excel processing serializes entire sheets into text, which may be inefficient for large workbooks.
- The notebook does not implement advanced table summarization or cross-row reasoning.
- `UnstructuredCSVLoader` is imported but not used in the demonstrated processing workflow.
- No retrieval-quality benchmark is included.

---

## 🔮 Future Improvements

- Add input validation for missing columns and malformed records.
- Handle empty files and missing values consistently.
- Explore CSV processing with `UnstructuredCSVLoader`.
- Add configurable document granularity for Excel sheets.
- Implement filtering and retrieval using metadata.
- Compare document representations using a consistent evaluation dataset.
- Connect generated documents to embeddings and a vector database.
- Evaluate retrieval quality for product lookup and inventory questions.

---

## 📚 Key Takeaway

Structured data ingestion requires more than simply reading CSV and Excel files. The data must be represented in a form that preserves useful information and supports the intended retrieval task.

This notebook demonstrates row-based CSV ingestion, custom metadata-rich document creation, sheet-aware Excel processing, and an alternative Excel loading approach.

These techniques provide a foundation for preparing structured data for RAG applications.

---

## 👨‍💻 Author

**Aadi**
