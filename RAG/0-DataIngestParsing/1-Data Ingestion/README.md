# 📥 Data Ingestion with LangChain

A hands-on Python notebook exploring **data ingestion for Retrieval-Augmented Generation (RAG)** using LangChain.

This project demonstrates how to represent text as LangChain documents, load individual text files, ingest multiple files from a directory, inspect document metadata, and experiment with different text-splitting strategies.

The goal is to understand how raw text is prepared for downstream processing, embedding generation, and retrieval in RAG applications.

## 🎯 Learning Objectives

- Understand the LangChain `Document` structure.
- Learn the importance of document metadata.
- Load individual text files using `TextLoader`.
- Ingest multiple text files using `DirectoryLoader`.
- Understand character-based, recursive character-based, and token-based splitting.
- Configure chunk sizes and chunk overlap.
- Compare different splitting strategies and their use cases.
- Prepare text for subsequent RAG pipeline stages.

## 📂 Project Structure

```text
1-Data Ingestion/
├── 1-dataingestion.ipynb
├── README.md
└── data/
    └── text_files/
        ├── python_intro.txt
        └── machine_learning.txt
```

**Note:** The notebook creates the sample text files programmatically. The `data/text_files/` directory is generated when the relevant cells are executed.

## 🧩 1. Understanding LangChain Documents

LangChain represents document content using the `Document` class.

A document contains two main components:

- **`page_content`** — the actual text content.
- **`metadata`** — additional information associated with the content.

The notebook creates a sample document with metadata such as source, page, author, creation date, and a custom field.

### Why metadata matters

Metadata helps with:

- Tracking the original document source.
- Filtering retrieved documents.
- Preserving contextual information.
- Debugging and auditing document-processing pipelines.

Keeping metadata associated with documents is important when building RAG applications that need to identify where retrieved information originated.

## 📄 2. Loading Text Files

### TextLoader

`TextLoader` loads an individual text file into a list of LangChain documents.

The notebook loads `python_intro.txt` with UTF-8 encoding and inspects the resulting document, its content, and its metadata.

**Use case:** Loading a single text document before processing it further.

### DirectoryLoader

`DirectoryLoader` loads multiple files from a directory using a file-matching pattern.

The notebook configures it to:

- Read `.txt` files from `data/text_files`.
- Use `TextLoader` for each file.
- Apply UTF-8 encoding.
- Display loading progress.
- Inspect the number of documents, source paths, and content lengths.

**Use case:** Ingesting collections of text files rather than loading each file individually.

## ✂️ 3. Text Splitting Strategies

Before text is embedded and indexed, it is often divided into smaller chunks. Chunking makes it possible to retrieve relevant portions of a larger document rather than passing the entire document into a language model.

This notebook explores three splitting strategies.

### A. CharacterTextSplitter

`CharacterTextSplitter` splits text using a configured separator.

Configuration demonstrated in the notebook:

| Parameter | Value |
|---|---|
| Separator | `"\n"` |
| Chunk size | `200` |
| Chunk overlap | `20` |
| Length function | `len` |

**Advantages**
- Simple to configure.
- Useful when the text contains meaningful delimiters.

**Limitations**
- Does not recursively try alternative separators.
- Depending on the input and configuration, chunks may not align with complete sentences or ideal semantic boundaries.

### B. RecursiveCharacterTextSplitter

`RecursiveCharacterTextSplitter` tries multiple separators in order to preserve text structure as much as possible.

The notebook uses:

```python
separators = ["\n\n", "\n", " ", ""]
chunk_size = 200
chunk_overlap = 20
```

This strategy attempts to split on paragraph breaks first, then newlines, spaces, and finally individual characters when necessary.

**Advantages**
- Better at respecting text structure.
- Provides a useful general-purpose chunking strategy.
- Offers fallback separators when larger text sections exceed the target chunk size.

**Use case:** General-purpose document chunking for RAG pipelines.

### C. TokenTextSplitter

`TokenTextSplitter` splits text according to token counts rather than Python character counts.

Configuration demonstrated in the notebook:

| Parameter | Value |
|---|---|
| Chunk size | `50` tokens |
| Chunk overlap | `10` tokens |

Token-based splitting is useful when chunk limits need to be managed in relation to a model's token budget.

**Important:** Tokens are not the same as characters or words. Tokenization depends on the tokenizer used by the splitter.

## 🔄 4. Understanding Chunk Overlap

Chunk overlap allows consecutive chunks to share a portion of their content.

For example, with a target chunk size of 200 characters and an overlap of 20 characters, the splitter attempts to retain some shared context between neighboring chunks.

Overlap can help preserve context when a relevant passage spans a chunk boundary. However, excessive overlap can increase storage requirements, embedding costs, and duplicated retrieval results.

Actual chunk boundaries depend on the splitter, separator configuration, and input text.

## 📊 5. Comparison of Splitting Strategies

| Feature | CharacterTextSplitter | RecursiveCharacterTextSplitter | TokenTextSplitter |
|---|---|---|---|
| Primary approach | Configured separator | Hierarchy of separators | Token boundaries |
| Main measurement | Configurable length function | Configurable length function | Tokens |
| Structure preservation | Depends on separator | Attempts to preserve text structure | Not primarily structure-aware |
| Typical use | Text with clear delimiters | General-purpose document chunking | Token-budget-sensitive workflows |

No single splitter is best for every dataset. The choice depends on document structure, retrieval requirements, model constraints, and evaluation results.

## 🛠️ Tech Stack

- **Python** — implementation.
- **Jupyter Notebook** — interactive experimentation.
- **Pandas** — imported for data-related operations.
- **LangChain Core** — document representation.
- **LangChain Community** — document loaders.
- **LangChain Text Splitters** — character, recursive, and token-based splitting.

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible Python version.
- Jupyter Notebook or VS Code.
- `pip` or `uv` for package installation.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the notebook directory

```bash
cd "RAG/0-DataIngestParsing/1-Data Ingestion"
```

### 3. Install dependencies

```bash
python -m pip install pandas langchain-core langchain-community langchain-text-splitters
```

### 4. Run the notebook

Open `1-dataingestion.ipynb` in Jupyter Notebook or VS Code.

Execute the cells in order. The notebook creates sample text files, loads them, inspects document metadata, and demonstrates the splitting strategies.

## ⚠️ Notes and Limitations

- The examples use generated sample text files rather than a large external dataset.
- The notebook focuses on text-file ingestion and splitting, not the complete RAG pipeline.
- The token-based splitter uses tokenization behavior that may differ from the tokenizer of a downstream model.
- The configured chunk size is a target; actual chunk boundaries depend on the selected splitter and input structure.
- The notebook's final comparison is conceptual and does not benchmark retrieval quality or performance.

## 🔮 Next Steps

- Explore PDF, CSV, Excel, JSON, and database ingestion in separate notebooks.
- Preserve document metadata consistently across loaders and transformations.
- Compare chunk sizes and overlap values on real documents.
- Connect the resulting chunks to an embedding model and vector database.
- Evaluate retrieval quality using a consistent set of questions.

## 👨‍💻 Author

**Aadi**
