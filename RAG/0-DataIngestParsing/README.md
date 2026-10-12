# 📚 Data Ingestion & Parsing — RAG

A hands-on learning module focused on **data ingestion, document parsing, metadata extraction, and text preparation** using Python and LangChain.

This module explores how different data sources can be loaded, processed, and converted into LangChain `Document` objects to prepare them for downstream Retrieval-Augmented Generation (RAG) pipelines.

## 🎯 Learning Objectives

- Understand the role of data ingestion in RAG.
- Work with LangChain `Document` objects.
- Load text files and parse different document formats.
- Extract and preserve document metadata.
- Process structured and unstructured data.
- Convert relational database information into documents.
- Understand text splitting and chunking fundamentals.
- Prepare documents for embedding generation and vector storage.

## 📂 Repository Structure

```text
0-DataIngestParsing/
│
├── 1-Data Ingestion/
│   ├── 1-dataingestion.ipynb
│   └── README.md
│
├── 2-PDF Data Parsing/
│   ├── 2-dataparsingpdf.ipynb
│   └── README.md
│
├── 3-DOCS Data Parsing/
│   ├── 3-dataparsingdoc.ipynb
│   └── README.md
│
├── 4-CSV Excel Parsing/
│   ├── 4-csvexcelparsing.ipynb
│   └── README.md
│
├── 5-JSON Parsing/
│   ├── 5-jsonparsing.ipynb
│   └── README.md
│
├── 6-Database Parsing/
│   ├── 6-databaseparsing.ipynb
│   └── README.md
│
├── data/
│   ├── databases/
│   │   └── company.db
│   ├── json_files/
│   ├── pdf/
│   ├── structured_files/
│   ├── text_files/
│   └── word_files/
│
└── 1-langchain-document-components.svg
```

**Note:** This is the shared data directory for the module. Individual notebooks may generate files in these folders or expect input files to be present. The structure above reflects the repository layout; generated files may vary as you work through the notebooks.

## 📖 Learning Modules

| No. | Module | Topics Covered | Notebook |
|---|---|---|---|
| 01 | [Data Ingestion](./1-Data%20Ingestion/) | Text loaders, directory loading, document metadata, text splitting | `1-dataingestion.ipynb` |
| 02 | [PDF Data Parsing](./2-PDF%20Data%20Parsing/) | PDF loaders, page extraction, text cleaning, chunking | `2-dataparsingpdf.ipynb` |
| 03 | [DOCX Data Parsing](./3-DOCS%20Data%20Parsing/) | Word document loaders, extracted content, element metadata | `3-dataparsingdoc.ipynb` |
| 04 | [CSV & Excel Parsing](./4-CSV%20Excel%20Parsing/) | CSV records, Excel sheets, structured documents, custom metadata | `4-csvexcelparsing.ipynb` |
| 05 | [JSON Parsing](./5-JSON%20Parsing/) | Nested JSON, JSONL generation, `JSONLoader`, `jq_schema` | `5-jsonparsing.ipynb` |
| 06 | [Database Parsing](./6-Database%20Parsing/) | SQLite, schema inspection, SQL queries, relationship documents | `6-databaseparsing.ipynb` |

## 🧩 Understanding LangChain Documents

LangChain's `Document` class provides a common representation for content loaded from different sources.

A document primarily contains two components:

| Component | Description |
|---|---|
| `page_content` | The textual content of the document |
| `metadata` | Additional information associated with the document |

Example:

```python
from langchain_core.documents import Document

doc = Document(
    page_content="Retrieval-Augmented Generation combines retrieval with language generation.",
    metadata={
        "source": "rag_notes.txt",
        "topic": "RAG"
    }
)
```

### Why metadata matters

- Tracks the original source of information.
- Supports metadata-based filtering.
- Preserves page, section, or record information.
- Helps maintain context throughout document processing.
- Supports debugging and retrieval evaluation.

## 🔄 Overall Data Ingestion Workflow

```text
         Raw Data Sources
                 |
     +-----------+-----------+
     |           |           |
     v           v           v
   Text &      CSV, Excel   Databases
    PDF         & JSON
     |           |           |
     v           v           v
 Document     Structured   SQL-Based
  Loaders     Processing   Processing
     |           |           |
     +-----------+-----------+
                 |
                 v
       LangChain Documents
                 |
                 v
     Cleaning & Text Chunking
                 |
                 v
       Embedding Generation
                 |
                 v
            Vector Store
                 |
                 v
          RAG Retrieval
```

This illustrates the broader RAG preparation workflow. The notebooks in this module focus mainly on ingestion, extraction, document representation, and selected text-splitting techniques.

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experiments |
| LangChain Core | Document representation |
| LangChain Community | Document loaders and database utilities |
| Pandas | CSV and Excel processing |
| SQLite | Relational database experiments |
| PyPDF / PyMuPDF | PDF extraction |
| docx2txt / Unstructured | Document extraction |
| jq | JSON selection support |

Dependencies vary by notebook. Refer to each module's README for installation instructions.

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to this module

```bash
cd "RAG/0-DataIngestParsing"
```

### 3. Install the general dependencies

```bash
python -m pip install jupyter pandas langchain-core langchain-community langchain-text-splitters
```

Install any additional packages required by the specific file loader you intend to use.

### 4. Choose a learning module

Open the corresponding notebook in Jupyter Notebook or VS Code. Execute the cells in order and follow that module's README for input paths and dependencies.

## ⚠️ Scope and Limitations

- Examples use educational datasets and sample files.
- Different loaders may produce different text and metadata.
- Scanned PDFs may require OCR support.
- Complex layouts and tables may require specialized extraction.
- Structured records may need custom formatting for effective semantic retrieval.
- Chunking parameters should be evaluated against the intended retrieval task.
- End-to-end embedding, vector indexing, and RAG question answering are outside the scope of this module's current ingestion exercises.

## 🔮 Next Steps

After completing this module, continue with:

1. Vector embeddings and embedding databases.
2. Vector stores and vector database operations.
3. Advanced chunking strategies.
4. Retrieval pipelines and RAG application development.

## 👨‍💻 Author

**Aadi**
