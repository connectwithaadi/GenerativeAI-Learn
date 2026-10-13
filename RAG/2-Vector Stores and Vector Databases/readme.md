# 🗄️ Vector Stores & Vector Databases — Generative AI Learning

A hands-on learning module focused on **vector embeddings, vector stores, semantic search, document retrieval, and the foundations of Retrieval-Augmented Generation (RAG)**.

This module explores different vector storage technologies and their integration with LangChain. Through practical Jupyter notebooks, I experiment with local and cloud-based vector stores, document indexing, metadata filtering, similarity search, and retriever configuration.

The goal is to understand how text embeddings are stored and retrieved efficiently to support context-aware AI applications.

---

## 🎯 Learning Objectives

- Understand the role of vector stores in RAG systems.
- Learn how embeddings enable semantic similarity search.
- Work with LangChain's vector-store abstractions.
- Store and retrieve documents with metadata.
- Compare local, in-memory, and cloud-based vector stores.
- Perform similarity searches and inspect retrieval scores.
- Apply metadata filters to narrow search results.
- Explore retrievers and different retrieval strategies.
- Understand vector-index creation and persistence.
- Build a foundation for advanced RAG pipelines.

---

## 📂 Repository Structure

```text
2-Vector Stores and Vector Databases/
│
├── 1-ChromaDB/
│   ├── 1-chromadb.ipynb
│   └── README.md
│
├── 2-FAISS/
│   ├── 2-FAISS.ipynb
│   └── README.md
│
├── 3-Other Vector Stores/
│   ├── 3-OtherVectorstores.ipynb
│   └── README.md
│
├── 4-AstraDB Vector Store/
│   ├── 4-AstraDBVectorStore.ipynb
│   └── README.md
│
├── 5-Pinecone VectorDB/
│   ├── 5-PineconeVectorDB.ipynb
│   └── README.md
│
├── chroma_db/                  # Local ChromaDB artifacts
├── data/                       # Sample document data
├── faiss_index/                # Local FAISS index artifacts
│
└── README.md
```

*Note: The structure above reflects the repository layout documented during this learning stage. The generated database files and index artifacts depend on the notebook execution paths and configurations.*

---

## 📚 Projects Overview

### 1. [ChromaDB](./1-ChromaDB/)

Explore ChromaDB as a vector store integrated with LangChain.

**Topics covered:**
- Initializing a ChromaDB vector store.
- Working with documents and embeddings.
- Inserting and retrieving documents.
- Performing semantic similarity searches.
- Understanding local vector-store persistence.

**Primary file:** `1-chromadb.ipynb`

### 2. [FAISS](./2-FAISS/)

Explore FAISS for efficient vector indexing and similarity search.

**Topics covered:**
- Creating a FAISS index.
- Generating Hugging Face embeddings.
- Saving and loading local indexes.
- Performing similarity searches with scores.
- Applying metadata filtering.
- Building basic RAG chains with LCEL.
- Exploring streaming and conversational prompts.

**Primary file:** `2-FAISS.ipynb`

### 3. [Other Vector Stores](./3-Other%20Vector%20Stores/)

Experiment with LangChain's `InMemoryVectorStore` to understand lightweight vector storage and retrieval.

**Topics covered:**
- Initializing an in-memory vector store.
- Adding documents with metadata.
- Performing semantic similarity searches.
- Controlling the number of retrieved results.
- Converting vector stores into retrievers.
- Invoking retrievers with natural-language queries.

**Primary file:** `3-OtherVectorstores.ipynb`

### 4. [Astra DB Vector Store](./4-AstraDB%20Vector%20Store/)

Explore Astra DB as a managed cloud vector database.

**Topics covered:**
- Connecting LangChain to Astra DB.
- Configuring database credentials and collections.
- Inserting documents with embeddings and metadata.
- Performing similarity searches.
- Applying metadata filters.
- Exploring threshold-based and MMR retrieval.
- Constructing LCEL-based RAG pipelines.
- Experimenting with streamed LLM responses.

**Primary file:** `4-AstraDBVectorStore.ipynb`

### 5. [Pinecone VectorDB](./5-Pinecone%20VectorDB/)

Explore Pinecone's managed vector database and serverless indexing capabilities.

**Topics covered:**
- Connecting to Pinecone using its Python SDK.
- Creating serverless vector indexes.
- Configuring vector dimensions and similarity metrics.
- Integrating Hugging Face embeddings.
- Inserting documents with metadata.
- Performing semantic similarity searches.
- Applying metadata filtering.
- Inspecting retrieval scores.
- Configuring threshold-based retrievers.

**Primary file:** `5-PineconeVectorDB.ipynb`

---

## 🧠 Concepts Practiced

| Concept | Description |
|---|---|
| Vector Embeddings | Representing text as numerical vectors |
| Vector Stores | Storing embeddings alongside document content and metadata |
| Semantic Search | Retrieving content based on vector similarity |
| Vector Indexing | Organizing vectors for efficient retrieval |
| Metadata Filtering | Restricting retrieval using document attributes |
| Similarity Scores | Inspecting the relationship between queries and retrieved vectors |
| Retrievers | Providing a consistent interface for document retrieval |
| MMR Retrieval | Balancing relevance with diversity in retrieved results |
| Index Persistence | Saving or retaining vector data for reuse |
| LCEL | Composing retrieval and LLM operations into pipelines |
| Streaming | Producing LLM output incrementally |

---

## 🔄 General Vector Retrieval Workflow

```text
Documents
    |
    v
Document Processing
    |
    v
Text Chunking
    |
    v
Embedding Model
    |
    v
Vector Store / Database
    |
    v
Similarity Search
    |
    v
Metadata Filtering
    |
    v
Retriever
    |
    v
Relevant Context
    |
    v
RAG Pipeline
```

The individual notebooks explore different parts of this workflow, depending on the vector-store technology and retrieval techniques being studied.

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experiments |
| LangChain | Document, embedding, and retrieval abstractions |
| Hugging Face | Embedding model integration |
| Sentence Transformers | Pretrained text embedding models |
| ChromaDB | Vector storage and similarity search |
| FAISS | Vector indexing and similarity search |
| InMemoryVectorStore | Lightweight in-memory retrieval |
| Astra DB | Cloud-hosted vector storage |
| Pinecone | Managed vector database |
| Groq | LLM integration in selected notebooks |
| NumPy | Vector operations and similarity calculations |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- Internet access for downloading embedding models.
- Appropriate API credentials for cloud-based examples.

### 1. Clone the Repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to This Module

```bash
cd "RAG/2-Vector Stores and Vector Databases"
```

### 3. Install Dependencies

Install the packages required for the specific notebook you want to run. Different vector stores use different integrations.

For example:

```bash
python -m pip install jupyter langchain-core langchain-community langchain-huggingface sentence-transformers
```

Additional dependencies are required for ChromaDB, FAISS, Astra DB, Pinecone, and the LLM integrations used by individual notebooks.

### 4. Configure API Credentials

Cloud-based notebooks may require credentials for Astra DB, Pinecone, or Groq.

Use environment variables or a local `.env` file where appropriate. Never commit API keys or secrets to GitHub.

### 5. Run a Notebook

Open the notebook associated with the technology you want to explore and execute the cells in order.

For cloud vector stores, verify your credentials, database configuration, index settings, and network access before running the notebook.

---

## ⚖️ Understanding the Different Vector Stores

| Vector Store | Storage Approach | Best Learning Focus |
|---|---|---|
| ChromaDB | Local or configured persistent storage | Document storage and semantic retrieval |
| FAISS | Vector index managed by the application | Efficient vector search and index persistence |
| InMemoryVectorStore | Application memory | Lightweight retrieval experiments |
| Astra DB | Managed cloud database | Cloud-hosted vector storage |
| Pinecone | Managed cloud vector database | Serverless indexes and scalable retrieval |

These technologies have different capabilities, persistence models, operational requirements, and deployment considerations. Their suitability depends on the application, data size, infrastructure, and retrieval requirements.

---

## 🔮 Future Improvements

- Add a consistent dataset for comparing all five vector stores.
- Benchmark retrieval latency and relevance.
- Compare different embedding models.
- Evaluate retrieval using precision, recall, and ranking metrics.
- Explore advanced metadata filtering.
- Compare similarity search, threshold-based retrieval, and MMR.
- Integrate the strongest retrieval approach into a complete RAG application.
- Add automated tests and reusable ingestion pipelines.
- Document configuration and troubleshooting for each integration.
- Connect the retrieval pipeline to a FastAPI application.

---

## 📚 Key Takeaway

This module builds practical knowledge of vector storage and retrieval technologies used in modern AI applications.

By experimenting with ChromaDB, FAISS, in-memory storage, Astra DB, and Pinecone, I am developing a clearer understanding of how embeddings are indexed, stored, searched, and integrated into retrieval pipelines.

This foundation prepares me for the next stage of my RAG journey: **advanced chunking strategies, retrieval optimization, and more capable end-to-end RAG systems.**

---

## 👨‍💻 Author

**Aadi**
