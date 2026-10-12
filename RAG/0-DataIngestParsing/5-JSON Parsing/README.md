
# 🧩 JSON Data Parsing with LangChain

A hands-on Python project exploring **JSON ingestion, nested data processing, JSONL file generation, and LangChain document creation**.

This notebook demonstrates how to create structured JSON data, save JSON and JSONL files, extract employee records using LangChain's `JSONLoader`, and build a custom processor that converts nested employee information into context-rich `Document` objects.

The goal is to understand how structured and nested JSON data can be transformed into document representations for downstream Retrieval-Augmented Generation (RAG) applications.

---

## 🎯 Learning Objectives

- Understand JSON and JSONL data formats.
- Generate and save nested JSON data using Python.
- Create JSONL files containing individual event records.
- Extract selected records using LangChain's `JSONLoader`.
- Understand `jq_schema` for selecting JSON elements.
- Convert nested employee data into readable text.
- Create LangChain `Document` objects with custom metadata.
- Explore the relationship between structured data ingestion and RAG.

---

## 📂 Project Structure

```text
5-JSON Parsing/
├── 5-jsonparsing.ipynb
├── README.md
└── data/
    └── json_files/
        ├── company_data.json
        └── events.jsonl
```

The notebook creates the `data/json_files/` directory and generates both sample data files when the corresponding cells execute successfully.

---

## 📘 Notebook Walkthrough

### 1. Creating Nested JSON Data

The notebook creates a sample company dataset representing an organization named `AK Unity`.

The JSON structure contains three main sections:

- **Company:** The company name.
- **Employees:** A list of employee records.
- **Department:** Nested departmental information.

Each employee record includes:

| Field | Description |
|---|---|
| `id` | Employee identifier |
| `name` | Employee name |
| `role` | Professional role |
| `skills` | List of technical skills |
| `projects` | List of projects and their statuses |

The department section contains nested information such as department heads, budgets, and team sizes.

The notebook saves this structure to `company_data.json` using Python's `json` module and formatted JSON output.

**Key takeaway:** JSON supports nested objects, arrays, and structured relationships that need to be preserved during ingestion.

### 2. Generating JSONL Data

The notebook also creates `events.jsonl`, containing sample event records such as:

- User login
- Page view
- Purchase

Each record contains a timestamp, event type, and user identifier. The purchase event also contains an amount, while the page-view event includes a page path.

The notebook writes each record as an individual JSON object followed by a newline.

**JSON vs. JSONL**

| Feature | JSON | JSONL |
|---|---|---|
| Typical structure | One JSON value, often an object or array | One JSON value per line |
| Multiple records | Commonly stored in an array | Stored as separate lines |
| Common use cases | Nested configuration and structured datasets | Event streams, logs, and record-oriented data |

**Important:** The notebook generates the JSONL file but does not implement a JSONL loading or processing pipeline.

### 3. JSONLoader with `jq_schema`

The notebook uses LangChain's `JSONLoader` to extract individual employee records from `company_data.json`.

```python
employee_loader = JSONLoader(
    file_path="data/json_files/company_data.json",
    jq_schema=".employees[]",
    text_content=False
)
```

The configuration demonstrates three important concepts:

- `file_path` specifies the JSON input file.
- `jq_schema=".employees[]"` selects each employee object from the employees array.
- `text_content=False` allows the selected JSON objects to be represented as JSON content rather than requiring a text field.

The notebook then loads the selected records, prints the number of employee documents, previews the first document, and inspects the resulting `Document` objects.

**Key takeaway:** `jq_schema` makes it possible to select specific elements from a nested JSON structure instead of treating the entire file as one document.

### 4. Custom JSON Processing

The notebook defines a custom function:

```python
process_json_intelligently(filepath: str) -> List[Document]
```

The function reads the JSON file and iterates over the employee records.

For each employee, it constructs a readable employee profile containing:

- Name
- Role
- Skills
- Projects
- Project statuses

The function then creates a LangChain `Document` with the generated text and associated metadata.

#### Metadata fields

| Field | Purpose |
|---|---|
| `source` | Source JSON file path |
| `data_type` | Identifies the record as an `employee_profile` |
| `employee_id` | Employee identifier |
| `employee_name` | Employee name |
| `role` | Employee's professional role |

This approach converts nested JSON records into human-readable text while retaining useful metadata for filtering and source tracking.

**Important:** The current custom function processes employee profiles only. Although departmental information exists in the JSON dataset, the function does not yet create separate department documents.

### 5. Custom Processing and Return Value

The notebook calls:

```python
process_json_intelligently(
    "data/json_files/company_data.json"
)
```

Before using the returned documents in another pipeline, the function needs to return its accumulated list.

Add the following line at the end of the function, outside the employee loop:

```python
return documents
```

Without this return statement, Python returns `None` by default, even if documents have been appended internally.

After this correction, the function can provide a list of employee documents for further inspection, embedding generation, or retrieval.

---

## 🔄 JSON Ingestion Workflow

```text
Nested JSON Data
       |
       v
company_data.json
       |
       +--------------------------+
       |                          |
       v                          v
   JSONLoader              Custom Processor
       |                          |
       v                          v
Select Employee Objects    Format Employee Profiles
       |                          |
       v                          v
LangChain Documents        Documents + Custom Metadata
       |                          |
       +-------------+------------+
                     |
                     v
           Downstream RAG Pipeline
```

The notebook also generates `events.jsonl` as a separate data-format example.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| `json` | Reading, writing, and serializing JSON |
| `os` | Creating directories |
| LangChain Community | `JSONLoader` |
| LangChain Core | `Document` representation |
| `jq` | JSON query support used by the loader configuration |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible Python version.
- Jupyter Notebook or VS Code.
- A Python environment with the required dependencies.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/0-DataIngestParsing/5-JSON Parsing"
```

### 3. Install dependencies

```bash
python -m pip install jupyter langchain-community langchain-core jq
```

The notebook also contains a `uv pip install jq` command. Ensure the required `jq` functionality is available in your environment if the JSON loader reports a dependency error.

### 4. Run the notebook

Open `5-jsonparsing.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

The notebook generates the sample JSON and JSONL files, loads employee records using `JSONLoader`, and demonstrates custom document processing.

---

## ⚠️ Current Limitations

- The notebook uses a small, synthetic company dataset.
- The custom processor assumes employee records contain the expected fields.
- Department records are not converted into separate documents.
- JSONL file generation is implemented, but JSONL ingestion is not.
- The custom processor needs a `return documents` statement to return its results.
- The notebook does not implement embedding generation, vector storage, or end-to-end RAG retrieval.
- No automated validation or benchmark is included.

---

## 🔮 Future Improvements

- Add JSONL ingestion and event-record processing.
- Generate separate documents for departmental information.
- Add validation for missing fields and malformed JSON.
- Handle optional employee skills and project lists safely.
- Preserve additional metadata for source tracking.
- Explore configurable `jq_schema` expressions.
- Add filtering and retrieval over employee and department documents.
- Connect processed documents to embeddings and a vector database.
- Evaluate retrieval quality using structured-data questions.

---

## 📚 Key Takeaway

JSON ingestion involves more than reading a file. Nested objects and arrays often need to be transformed into meaningful document representations while preserving useful context and metadata.

This notebook explores JSON file generation, JSONL generation, selective extraction using `JSONLoader`, and custom employee-profile processing.

These techniques provide a foundation for preparing structured JSON data for RAG applications.

---

## 👨‍💻 Author

**Aadi**
