# 🧠 Other Vector Stores — InMemoryVectorStore with LangChain

A hands-on project exploring **in-memory vector storage, embedding-based similarity search, and retriever integration** using Python, LangChain, and Hugging Face.

This notebook demonstrates how to initialize LangChain's `InMemoryVectorStore`, generate embeddings with a pretrained sentence-transformer model, store documents with metadata, perform similarity searches, and convert a vector store into a retriever.

The goal is to understand an alternative to persistent vector databases and learn how vector-store abstractions simplify semantic retrieval in AI applications.

---

## 🎯 Learning Objectives

- Understand the concept of an in-memory vector store.
- Initialize `InMemoryVectorStore` using LangChain.
- Generate text embeddings using Hugging Face.
- Represent documents using LangChain's `Document` class.
- Store multiple documents with associated metadata.
- Perform semantic similarity searches.
- Control the number of retrieved results using `k`.
- Convert a vector store into a retriever.
- Retrieve relevant documents using `retriever.invoke()`.
- Understand the difference between in-memory and persistent vector storage.

---

## 📂 Project Structure

```text
3-Other Vector Stores/
├── 3-OtherVectorstores.ipynb
└── README.md
```

### File Overview

- `3-OtherVectorstores.ipynb` — practical implementation of in-memory vector storage, document insertion, similarity search, and retriever operations.
- `README.md` — project documentation and setup instructions.

---

## 📘 Notebook Walkthrough

### 1. Understanding InMemoryVectorStore

The notebook introduces LangChain's `InMemoryVectorStore`.

It stores document embeddings in memory and uses NumPy-based cosine similarity for searching.

Unlike persistent vector databases, an in-memory store is designed for lightweight workflows, experiments, and applications where persistence is not required.

### 2. Configuring the Language Model

The notebook loads environment variables using `python-dotenv` and initializes a Groq-hosted chat model.

```python
from dotenv import load_dotenv
from langchain_groq import ChatGroq

load_dotenv()

llm = ChatGroq(
    model_name="openai/gpt-oss-120b"
)
```

The notebook configures the LLM, although the demonstrated vector-search workflow does not invoke it to generate answers.

A valid Groq API key is required to initialize and use the model.

### 3. Initializing Hugging Face Embeddings

The notebook uses the following pretrained embedding model:

```python
from langchain_huggingface import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The model converts text into numerical vectors that can be compared mathematically.

This embedding integration allows the notebook to perform local embedding generation without calling an OpenAI embedding API.

### 4. Creating the In-Memory Vector Store

The notebook initializes the vector store using the embedding model:

```python
from langchain_core.vectorstores import InMemoryVectorStore

vector_store = InMemoryVectorStore(
    embedding=embeddings
)
```

The vector store is now ready to accept documents and perform similarity searches.

### 5. Creating Documents with Metadata

The notebook creates ten sample documents using LangChain's `Document` class.

The examples cover different subjects, including:

- Food and breakfast.
- Weather forecasts.
- LangChain and LangGraph.
- News and crime.
- Movies.
- Product reviews.
- Football.
- Stock-market news.

Each document contains `page_content` and metadata, such as its source category.

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

Metadata provides additional information associated with a document and can support filtering and downstream processing.

### 6. Adding Documents to the Vector Store

The notebook inserts the document collection into the in-memory store:

```python
vector_store.add_documents(
    documents=documents
)
```

The vector store generates embeddings for the documents and keeps their content and metadata available for retrieval.

Because the store is in memory, its contents are not automatically persisted to disk.

### 7. Performing Similarity Search

The notebook searches for information related to a weather forecast:

```python
results = vector_store.similarity_search(
    "hows the weather forecast"
)
```

The search compares the query embedding with the stored document embeddings and returns relevant documents.

The notebook also changes the number of retrieved results:

```python
results = vector_store.similarity_search(
    "hows the weather forecast",
    k=2
)
```

Here, `k=2` requests up to two results.

This demonstrates how retrieval parameters control the number of documents returned for a query.

### 8. Converting the Vector Store into a Retriever

The notebook converts the vector store into a retriever:

```python
retriever = vector_store.as_retriever(
    search_kwargs={"k": 2}
)
```

A retriever provides a standardized interface for fetching relevant documents.

This abstraction allows the retrieval component to be integrated into larger LangChain pipelines without directly calling the vector store for every operation.

### 9. Invoking the Retriever

The notebook executes a query through the retriever:

```python
results = retriever.invoke(
    "hows the weather forecast"
)
```

The retriever returns relevant document objects based on the configured search behavior.

This is a foundational step toward building retrieval pipelines for question answering and RAG applications.

---

## 🔄 Workflow

```text
Sample Documents
       |
       v
Hugging Face Embeddings
       |
       v
InMemoryVectorStore
       |
       v
Document Embeddings
       |
       v
Similarity Search
       |
       v
Top-K Results
       |
       v
Retriever Interface
       |
       v
retriever.invoke(query)
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
| LangChain Core | Documents and vector-store abstractions |
| LangChain Hugging Face | Embedding integration |
| Sentence Transformers | Pretrained embedding model |
| NumPy | Vector similarity calculations |
| LangChain Groq | Chat model integration |
| python-dotenv | Environment variable management |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- Internet access for downloading the embedding model.
- A Groq API key if executing the LLM initialization cell.

### 1. Clone the Repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the Project Folder

```bash
cd "RAG/2-Vector Stores and Vector Databases/3-Other Vector Stores"
```

### 3. Install Dependencies

```bash
python -m pip install jupyter python-dotenv langchain-core langchain-huggingface langchain-groq
```

### 4. Configure Environment Variables

Create a local `.env` file containing your Groq API key:

```text
GROQ_API_KEY=your_groq_api_key
```

Never upload API keys or secret environment files to GitHub.

### 5. Run the Notebook

Open `3-OtherVectorstores.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

---

## ⚠️ Current Limitations

- The vector store keeps its data in memory rather than providing persistent storage.
- Stored documents are not automatically saved for reuse across independent sessions.
- The notebook uses a small, manually defined collection of ten documents.
- The notebook demonstrates retrieval but does not implement a complete RAG question-answering chain.
- The Groq LLM is initialized but is not used in the demonstrated retrieval operations.
- Retrieval quality is not evaluated against a labeled benchmark.
- The notebook does not demonstrate metadata filtering or document deletion.

---

## 🔮 Future Improvements

- Compare `InMemoryVectorStore` with ChromaDB and FAISS.
- Add metadata-based filtering examples.
- Demonstrate adding and deleting documents.
- Build a complete RAG chain using the retriever.
- Connect retrieved documents with an LLM for contextual question answering.
- Add tests for retrieval behavior.
- Evaluate retrieval quality using precision and recall.
- Experiment with larger document collections.

---

## 📚 Key Takeaway

This notebook demonstrates the fundamentals of in-memory vector storage and semantic retrieval using LangChain.

It progresses from embedding generation and document insertion to similarity search and retriever invocation, providing a lightweight foundation for understanding how vector stores integrate into larger retrieval and RAG systems.

---

## 👨‍💻 Author

**Aadi**
