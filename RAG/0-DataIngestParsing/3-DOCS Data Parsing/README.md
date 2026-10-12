# 📝 DOCX Data Parsing with LangChain

A hands-on Python notebook exploring **Word document ingestion using LangChain**.

This project demonstrates two approaches for loading `.docx` files: `Docx2txtLoader` and `UnstructuredWordDocumentLoader`. It covers document loading, content inspection, metadata exploration, and element-based extraction using LangChain document loaders.

The goal is to understand how Word documents can be ingested and prepared for downstream document-processing and Retrieval-Augmented Generation (RAG) workflows.

---

## 🎯 Learning Objectives

- Understand Word document ingestion in LangChain.
- Load `.docx` files using `Docx2txtLoader`.
- Explore `UnstructuredWordDocumentLoader`.
- Inspect extracted document content and metadata.
- Understand element-based document extraction.
- Compare the two loading approaches.
- Identify the role of document loaders in a RAG ingestion pipeline.

---

## 📂 Project Structure

```text
3-DOCS Data Parsing/
├── 3-dataparsingdoc.ipynb
└── README.md
```

The notebook expects a Word document at `data/word_files/proposal.docx`, relative to the current working directory.

Ensure the input file exists at the expected path before running the notebook.

---

## 📘 Notebook Walkthrough

### 1. Importing Document Loaders

The notebook imports two LangChain document loaders:

```python
from langchain_community.document_loaders import (
    Docx2txtLoader,
    UnstructuredWordDocumentLoader
)
```

These loaders provide different approaches to extracting content from Word documents.

### 2. Method 1 — Docx2txtLoader

`Docx2txtLoader` loads a Word document and returns its extracted content as LangChain `Document` objects.

The notebook demonstrates how to:

- Load `proposal.docx`.
- Display the number of loaded documents.
- Preview the first 200 characters of the extracted content.
- Inspect the associated metadata.
- Handle loading errors using a `try-except` block.

**Key takeaway:** This approach provides a straightforward way to extract text from a DOCX file for further processing.

### 3. Method 2 — UnstructuredWordDocumentLoader

`UnstructuredWordDocumentLoader` provides another approach to Word document ingestion.

The notebook initializes the loader using:

```python
unstructured_loader = UnstructuredWordDocumentLoader(
    "data/word_files/proposal.docx",
    mode="elements"
)
```

The `mode="elements"` setting requests element-level output instead of treating the entire document as a single combined text representation.

The notebook then:

- Loads the Word document.
- Displays the number of extracted elements.
- Inspects the first three elements.
- Prints each element's category from metadata.
- Previews the first 100 characters of each element.

A fallback category of `unknown` is used when the `category` metadata field is unavailable.

**Key takeaway:** Element-based extraction makes it possible to inspect individual document elements and their associated metadata.

### 4. Inspecting Extracted Elements

The notebook evaluates `unstructured_docs` to inspect the extracted LangChain documents directly.

Each element is represented as a document containing:

- `page_content` — the extracted text.
- `metadata` — information associated with the extracted element, potentially including its category and source.

The exact number, categories, and content of extracted elements depend on the input document and the loader's extraction behavior.

### 5. Dependency Installation

The notebook includes commands to install or upgrade:

```bash
uv pip install -U python-docx
uv pip install -U unstructured
```

These commands are demonstrated in the notebook. The primary document-loading examples use the LangChain community loaders.

---

## 🔄 Document Ingestion Workflow

```text
Word Document (.docx)
          |
          v
   Select a Loader
          |
     +----+----------------+
     |                     |
     v                     v
Docx2txtLoader    UnstructuredWordDocumentLoader
     |                     |
     v                     v
Extracted Text       Document Elements
     |                     |
     v                     v
Inspect Content      Inspect Content
and Metadata         and Metadata
     |                     |
     +----------+----------+
                |
                v
     Prepared for Further
      Document Processing
                |
                v
       Potential RAG Input
```

---

## 📊 Loader Comparison

| Feature | Docx2txtLoader | UnstructuredWordDocumentLoader |
|---|---|---|
| Input format | `.docx` | `.docx` |
| Main purpose | Extract Word document text | Extract Word document content |
| Notebook configuration | Default loading mode | `mode="elements"` |
| Content inspection | First 200 characters | First three elements, up to 100 characters each |
| Metadata inspection | Yes | Yes, including element category |
| Error handling | `try-except` | `try-except` |

The appropriate loader depends on the document structure and the level of detail required by the downstream application.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| LangChain Community | Word document loaders |
| `docx2txt` | Text extraction used by `Docx2txtLoader` |
| `unstructured` | Element-based document processing |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible Python version.
- Jupyter Notebook or VS Code.
- A `.docx` file for testing.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/0-DataIngestParsing/3-DOCS Data Parsing"
```

### 3. Install dependencies

```bash
python -m pip install jupyter langchain-community docx2txt python-docx unstructured
```

The notebook also demonstrates installation through `uv pip`. Use a consistent environment and install the packages required by the loaders you intend to run.

### 4. Prepare the input file

Place your Word document at:

```text
data/word_files/proposal.docx
```

Alternatively, update the file path in both loader examples.

### 5. Run the notebook

Open `3-dataparsingdoc.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

Inspect the outputs to compare the document and element extraction approaches.

---

## ⚠️ Current Limitations

- The notebook relies on a predefined input file path.
- The examples do not include a dedicated comparison of extraction accuracy or performance.
- The output depends on the input document and the selected loader.
- Complex formatting, tables, and images are not separately evaluated in this notebook.
- The notebook does not implement OCR or a complete RAG pipeline.
- Dependency installation commands may need adjustment for the operating system and Python environment.

---

## 🔮 Future Improvements

- Test both loaders on the same collection of DOCX files.
- Compare extracted content and metadata systematically.
- Experiment with documents containing tables, headings, and lists.
- Add validation for missing or empty input files.
- Preserve and inspect source metadata during subsequent processing.
- Connect extracted documents to text splitters, embeddings, and a vector store.
- Evaluate how extraction quality affects downstream RAG retrieval.

---

## 📚 Key Takeaway

Word document ingestion is an important step in preparing unstructured content for AI applications.

This notebook demonstrates two LangChain loading approaches and shows how to inspect their extracted text and metadata. These concepts provide a foundation for building document-processing and RAG pipelines.

---

## 👨‍💻 Author

**Aadi**
