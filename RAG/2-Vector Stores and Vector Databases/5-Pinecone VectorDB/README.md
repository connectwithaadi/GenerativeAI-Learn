# 🌲 Pinecone Vector Database — Semantic Search with LangChain

A hands-on project exploring **Pinecone, serverless vector indexing, semantic similarity search, metadata filtering, and retriever configuration** using Python, LangChain, and Hugging Face embeddings.

This notebook demonstrates how to connect to Pinecone, create vector indexes, generate document embeddings, store documents in a cloud-hosted vector database, retrieve relevant results, inspect similarity scores, and configure threshold-based retrieval.

The project extends my understanding of vector stores by exploring a managed vector database designed for scalable similarity search.

---

## 🎯 Learning Objectives

- Understand Pinecone as a managed vector database.
- Connect to Pinecone using the Python SDK.
- Create and inspect serverless vector indexes.
- Configure vector dimensions and similarity metrics.
- Generate embeddings using Hugging Face.
- Store LangChain documents in Pinecone.
- Perform semantic similarity searches.
- Apply metadata filters during retrieval.
- Inspect similarity search scores.
- Configure a retriever with a similarity-score threshold.
- Integrate Pinecone with LangChain's vector-store abstractions.

---

## 📂 Project Structure

```text
5-Pinecone VectorDB/
├── 5-PineconeVectorDB.ipynb
└── README.md
```

### File Overview

- `5-PineconeVectorDB.ipynb` — implementation of Pinecone index creation, embedding integration, document insertion, similarity search, metadata filtering, and retriever configuration.
- `README.md` — project documentation, setup instructions, and learning notes.

---

## 📘 Notebook Walkthrough

### 1. Installing Dependencies

The notebook installs the initial LangChain packages using `uv`:

```python
!uv add langchain langchain-pinecone langchain-openai
```

The notebook also imports the Pinecone SDK and LangChain Hugging Face integration.

For a fresh environment, install the dependencies required by the complete notebook, including `pinecone`, `langchain-pinecone`, `langchain-core`, and `langchain-huggingface`.

### 2. Connecting to Pinecone

The notebook initializes a Pinecone client using an API key.

```python
from pinecone import Pinecone

pc = Pinecone(api_key=api_key)
```

The client provides access to Pinecone operations, including index creation, index inspection, and connecting to an existing index.

**Security best practice:** Store the API key in an environment variable rather than hardcoding it in a notebook.

```python
import os
from pinecone import Pinecone

api_key = os.environ["PINECONE_API_KEY"]
pc = Pinecone(api_key=api_key)
```

### 3. Generating Hugging Face Embeddings

The notebook uses the `sentence-transformers/all-MiniLM-L6-v2` embedding model.

```python
from langchain_huggingface import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The model converts text into numerical vector representations that can be compared for semantic similarity.

The selected model produces 384-dimensional embeddings, which determines the dimension configured for the Pinecone indexes in this notebook.

### 4. Creating a Pinecone Serverless Index

The notebook first creates an index named `rag` if it does not already exist.

```python
from pinecone import ServerlessSpec

index_name = "rag"

if not pc.has_index(index_name):
    pc.create_index(
        name=index_name,
        metric="cosine",
        dimension=384,
        spec=ServerlessSpec(
            cloud="aws",
            region="us-east-1"
        )
    )

index = pc.Index(index_name)
```

The configuration specifies:

- **Index name:** `rag`
- **Dimension:** 384
- **Similarity metric:** Cosine
- **Cloud provider:** AWS
- **Region:** `us-east-1`

The notebook later creates a second index named `rag-384` with the same vector dimension and similarity metric.

### 5. Connecting LangChain to Pinecone

The notebook wraps the Pinecone index in LangChain's `PineconeVectorStore`.

```python
from langchain_pinecone import PineconeVectorStore

index_name = "rag-384"
index = pc.Index(index_name)

vector_store = PineconeVectorStore(
    index=index,
    embedding=embeddings
)
```

This integration allows documents to be added and retrieved through a consistent LangChain interface.

It connects the embedding model to the selected Pinecone index so document vectors can be generated and queried.

### 6. Creating Documents with Metadata

The notebook defines ten sample documents using LangChain's `Document` class.

The examples cover:

- Breakfast and food.
- Weather forecasts.
- LangChain and LangGraph.
- News and crime.
- Movies.
- Product reviews.
- Football.
- Stock-market news.

Each document contains `page_content` and a metadata field called `source`.

Example:

```python
from langchain_core.documents import Document

document = Document(
    page_content=(
        "The weather forecast for tomorrow is "
        "cloudy and overcast, with a high of 62 degrees."
    ),
    metadata={"source": "news"}
)
```

The `source` metadata identifies the document category and enables filtered searches.

### 7. Inspecting Index Statistics

The notebook inspects the Pinecone index using:

```python
index.describe_index_stats()
```

It also inspects index configuration through:

```python
pc.describe_index("rag")
```

These operations help verify index configuration and examine information about stored vectors.

The notebook subsequently checks statistics through the LangChain vector-store integration as well.

### 8. Inserting Documents into Pinecone

The notebook inserts the sample documents into the configured vector store:

```python
vector_store.add_documents(
    documents=documents
)
```

The integration generates embeddings and writes document vectors and their associated metadata to Pinecone.

Because Pinecone is a managed cloud service, the indexed data is not limited to the lifetime of the local Python session.

### 9. Metadata-Filtered Similarity Search

The notebook searches for information related to LangChain:

```python
results = vector_store.similarity_search(
    "LangChain provides abstractions to make working with LLMs easy",
    k=2,
    filter={"source": "tweet"}
)
```

This query requests up to two relevant documents and restricts the search to documents whose `source` metadata matches `tweet`.

The example demonstrates how semantic similarity and metadata filtering can be combined to narrow retrieval results.

### 10. Similarity Search with Scores

The notebook retrieves a result and its associated score:

```python
results = vector_store.similarity_search_with_score(
    "Will it be hot tomorrow?",
    k=1,
    filter={"source": "news"}
)

for res, score in results:
    print(
        f"* [SIM={score:3f}] "
        f"{res.page_content} [{res.metadata}]"
    )
```

This makes it possible to inspect the retrieved document alongside its score.

The score's interpretation depends on the vector-store integration and metric. It should not automatically be interpreted as a probability or normalized relevance percentage.

### 11. Configuring a Threshold-Based Retriever

The notebook creates a retriever using the `similarity_score_threshold` search strategy.

```python
retriever = vector_store.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={
        "k": 1,
        "score_threshold": 0.4
    }
)
```

It then invokes the retriever with a query about bank robbery:

```python
results = retriever.invoke(
    "Stealing from the bank is a crime",
    filter={"source": "news"}
)
```

The retriever is configured to request up to one result and apply a threshold of `0.4`.

Threshold-based retrieval can help control which results are considered sufficiently relevant. However, the threshold must be validated against the score semantics of the installed Pinecone integration and its configured metric.

---

## 🔄 End-to-End Workflow

```text
Pinecone API Credentials
          |
          v
Pinecone Client
          |
          v
Create / Inspect Index
          |
          v
Hugging Face Embeddings
          |
          v
PineconeVectorStore
          |
          v
Insert Documents + Metadata
          |
          v
Semantic Similarity Search
          |
          v
Metadata Filtering
          |
          v
Similarity Scores
          |
          v
Threshold-Based Retriever
          |
          v
Relevant Documents
```

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| uv | Python dependency management |
| Pinecone Python SDK | Index management and database access |
| Pinecone | Managed cloud vector database |
| LangChain Core | Document and retrieval abstractions |
| LangChain Pinecone | Pinecone vector-store integration |
| LangChain Hugging Face | Embedding integration |
| Sentence Transformers | Pretrained embedding model |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- A Pinecone account and API key.
- Internet access to download the embedding model and access Pinecone.

### 1. Clone the Repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the Project Folder

```bash
cd "RAG/2-Vector Stores and Vector Databases/5-Pinecone VectorDB"
```

### 3. Install Dependencies

```bash
python -m pip install jupyter uv pinecone langchain langchain-core langchain-pinecone langchain-huggingface sentence-transformers
```

### 4. Configure the API Key

Set the Pinecone API key as an environment variable.

**Windows PowerShell:**

```powershell
$env:PINECONE_API_KEY="your_pinecone_api_key"
```

**macOS / Linux:**

```bash
export PINECONE_API_KEY="your_pinecone_api_key"
```

Update the notebook to retrieve the key using `os.environ["PINECONE_API_KEY"]`.

Never commit API keys, tokens, or other credentials to GitHub.

### 5. Run the Notebook

Open `5-PineconeVectorDB.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

Ensure the Pinecone API key is configured, the index is available, and the embedding dimension matches the index configuration.

---

## ⚠️ Current Limitations

- The notebook uses a small, manually defined dataset of ten documents.
- Two indexes, `rag` and `rag-384`, are created or accessed during experimentation.
- Index configuration and retrieval behavior depend on the Pinecone service and installed package versions.
- The threshold-based retriever requires validation against the actual score convention.
- Retrieval quality is not evaluated against a labeled benchmark.
- The notebook does not implement a complete LLM-powered RAG answer-generation chain.
- Production deployment, monitoring, automated tests, and access controls are outside the current implementation.

---

## 🔮 Future Improvements

- Consolidate the examples around a single index.
- Move all credentials into environment variables.
- Add PDF and other document-ingestion workflows.
- Compare similarity search with threshold-based retrieval.
- Experiment with different embedding models.
- Evaluate retrieval quality using precision, recall, and ranking metrics.
- Integrate the retriever into an LCEL-based RAG chain.
- Add source citations to generated answers.
- Build a FastAPI endpoint for semantic retrieval.
- Add error handling and automated tests.

---

## 📚 Key Takeaway

This project demonstrates how Pinecone can be integrated with LangChain to build a cloud-hosted semantic retrieval system.

It covers index creation, 384-dimensional Hugging Face embeddings, document ingestion, metadata filtering, similarity-score inspection, and threshold-based retrieval.

Together with the ChromaDB, FAISS, in-memory vector-store, and Astra DB projects, it builds a practical understanding of vector database choices and retrieval techniques for modern AI applications.

---

## 👨‍💻 Author

**Aadi**
