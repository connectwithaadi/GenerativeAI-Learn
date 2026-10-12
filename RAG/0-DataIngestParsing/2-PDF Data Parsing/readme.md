# 📄 PDF Data Parsing with LangChain

A hands-on Python project exploring **PDF ingestion, text extraction, text cleaning, and document chunking** using LangChain.

This notebook demonstrates how to load PDF files using different loaders, inspect extracted page content and metadata, clean common PDF extraction artifacts, and build a reusable `SmartPDFProcessor` class for preparing PDF content for downstream RAG pipelines.

## 🎯 Learning Objectives

- Load PDF files using LangChain document loaders.
- Explore `PyPDFLoader` and `PyMuPDFLoader`.
- Inspect page-level text and document metadata.
- Understand common PDF text-extraction challenges.
- Clean excessive whitespace and common ligature characters.
- Split extracted PDF text into smaller chunks.
- Preserve and enhance metadata during chunk creation.
- Build a reusable PDF-processing class with basic error handling.

## 📂 Project Structure

```text
2-PDF Data Parsing/
├── 2-dataparsingpdf.ipynb
└── README.md
```

The notebook expects a PDF file at `data/pdf/attention.pdf`, relative to the working directory from which it is executed.

Make sure the required PDF is available at that path before running the loading and processing cells.

## 📘 Notebook Walkthrough

### 1. Loading PDF Files

The notebook explores two LangChain PDF loaders.

#### PyPDFLoader

`PyPDFLoader` extracts PDF content into LangChain `Document` objects, typically one document per page.

The notebook demonstrates how to:

- Load a PDF using `PyPDFLoader`.
- Inspect the number of extracted pages.
- Preview the first page's content.
- Examine the metadata associated with a page.

#### PyMuPDFLoader

`PyMuPDFLoader` provides an alternative PDF-loading implementation using PyMuPDF.

The notebook demonstrates loading the same PDF and inspecting the resulting documents and metadata.

#### Loader Comparison

The notebook includes a conceptual comparison of the two loaders in terms of text extraction, metadata, and typical use cases.

Actual extraction quality and speed depend on the PDF structure, document contents, and execution environment.

### 2. Handling PDF Text-Extraction Challenges

PDF text extraction can introduce unwanted whitespace, formatting inconsistencies, and ligature characters.

The notebook demonstrates a `clean_text()` function that:

- Collapses repeated whitespace into single spaces.
- Replaces the ligature `ﬁ` with `fi`.
- Replaces the ligature `ﬂ` with `fl`.

A sample financial report illustrates how extracted text can be normalized before further processing.

**Important:** This is a basic cleaning example. It does not implement OCR, comprehensive layout reconstruction, or general-purpose PDF artifact correction.

### 3. Text Splitting

The notebook imports three LangChain text splitters:

- `CharacterTextSplitter`
- `RecursiveCharacterTextSplitter`
- `TokenTextSplitter`

The reusable PDF processor uses `RecursiveCharacterTextSplitter` to divide cleaned page text into smaller chunks.

The default configuration specifies:

| Parameter | Value |
|---|---|
| Chunk size | 1,000 |
| Chunk overlap | 100 |
| Separator configuration | Space character (`" "`) |

The configured splitter uses character-based length measurement by default. Chunk size and overlap represent configuration targets; actual chunk boundaries depend on the splitter and input text.

### 4. SmartPDFProcessor

The notebook defines a `SmartPDFProcessor` class that combines PDF loading, text cleaning, chunk creation, and metadata enhancement.

#### Initialization

The constructor accepts configurable chunk-size and overlap values and initializes a `RecursiveCharacterTextSplitter`.

#### PDF Processing Workflow

The `process_pdf(pdf_path)` method follows these steps:

1. Load the PDF using `PyPDFLoader`.
2. Retrieve page-level documents.
3. Clean the extracted text for each page.
4. Skip pages whose cleaned text contains fewer than 50 characters.
5. Split the remaining page text into smaller chunks.
6. Attach enhanced metadata to the generated chunks.
7. Return the processed chunks as a list of LangChain `Document` objects.

#### Metadata Enhancement

The processor adds the following metadata fields to each generated chunk:

| Field | Description |
|---|---|
| `page` | One-based page number assigned by the processor |
| `total_pages` | Total number of pages loaded from the PDF |
| `chunk_method` | Processing identifier: `smart_pdf_processor` |
| `char_count` | Character count of the cleaned page text before chunking |

The original page metadata is also retained through dictionary unpacking.

This helps preserve document provenance and provides useful context for later retrieval and debugging.

### 5. Running the Processor

The notebook creates a processor using the default configuration:

```python
preprocessor = SmartPDFProcessor()
```

It then processes the input PDF:

```python
smart_chunks = preprocessor.process_pdf(
    "data/pdf/attention.pdf"
)
```

Finally, the notebook prints the number of generated chunks and displays the metadata associated with a sample chunk.

This demonstrates how extracted PDF content can be converted into smaller, metadata-rich documents for subsequent RAG processing.

## 🔄 Processing Workflow

```text
PDF File
   ↓
PyPDFLoader
   ↓
Page-Level Documents
   ↓
Text Cleaning
   ↓
Skip Nearly Empty Pages
   ↓
Recursive Character Splitting
   ↓
Metadata Enhancement
   ↓
Processed Document Chunks
   ↓
Ready for Downstream RAG Processing
```

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| LangChain Community | PDF document loaders |
| LangChain Core | Document representation |
| LangChain Text Splitters | Text chunking |
| PyPDF | PDF text extraction through `PyPDFLoader` |
| PyMuPDF | Alternative PDF loading implementation |

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible Python version.
- Jupyter Notebook or VS Code.
- A readable PDF file for testing.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/0-DataIngestParsing/2-PDF Data Parsing"
```

### 3. Install dependencies

```bash
python -m pip install jupyter langchain-community langchain-core langchain-text-splitters pypdf pymupdf
```

### 4. Prepare the input PDF

Place the PDF at:

```text
data/pdf/attention.pdf
```

The path is relative to the notebook's current working directory. If your file is stored elsewhere, update the path in the relevant cells.

### 5. Run the notebook

Open `2-dataparsingpdf.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

## ⚠️ Current Limitations

- The notebook expects an input PDF at a predefined path.
- Text extraction quality depends on the structure and contents of the PDF.
- The cleaning function handles only a small set of common text artifacts.
- The processor skips pages containing fewer than 50 characters after cleaning; such pages may still contain useful information.
- OCR for scanned PDFs is not implemented.
- The splitter uses a space-only separator configuration, which does not attempt the usual hierarchy of paragraph, newline, and word separators.
- The processor does not implement comprehensive recovery or validation for corrupted PDFs and malformed extracted text.

## 🔮 Future Improvements

- Add OCR support for scanned PDFs.
- Improve text cleaning and preserve meaningful paragraph boundaries.
- Refine the splitter's separator hierarchy.
- Add robust validation for empty PDFs and failed page extraction.
- Test the processor on PDFs with tables, multi-column layouts, and mixed formatting.
- Add source tracking and chunk-level evaluation.
- Integrate the processed chunks with embeddings, a vector database, and a RAG retriever.

## 📚 Key Takeaway

PDF ingestion is an important preprocessing stage in a RAG pipeline. Extracting text is only the first step; cleaning, chunking, and preserving metadata help prepare the content for effective downstream retrieval.

This notebook provides a foundation for experimenting with PDF loaders and building reusable document-processing workflows.

## 👨‍💻 Author

**Aadi**
