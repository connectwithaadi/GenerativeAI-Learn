# ☁️ Astra DB Vector Store — Cloud-Based Retrieval with LangChain

A hands-on project exploring **Astra DB, cloud-hosted vector storage, semantic similarity search, metadata filtering, and Retrieval-Augmented Generation (RAG)** using Python and LangChain.

This notebook demonstrates how to connect LangChain to Astra DB, generate embeddings with Hugging Face, store documents in a cloud vector collection, retrieve relevant documents, apply metadata filters, experiment with different retrieval strategies, and build RAG chains using LangChain Expression Language (LCEL).

The project extends the understanding of ChromaDB, FAISS, and in-memory vector stores by introducing a managed cloud vector database.

---

## 🎯 Learning Objectives

- Understand Astra DB as a cloud-hosted vector database.
- Connect Astra DB with LangChain using `AstraDBVectorStore`.
- Configure database credentials and collection settings.
- Generate embeddings using Hugging Face.
- Store documents with metadata in Astra DB.
- Perform semantic similarity searches.
- Filter retrieval results using metadata.
- Inspect similarity search scores.
- Experiment with similarity-score-threshold retrieval.
- Explore Maximum Marginal Relevance (MMR) retrieval.
- Build basic RAG chains using LCEL.
- Format retrieved documents for LLM prompts.
- Experiment with conversational prompts and streaming responses.

---

## 📂 Project Structure

```text
RAG/
└── 2-Vector Stores and Vector Databases/
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
    └── 4-AstraDB Vector Store/
        ├── 4-AstraDBVectorStore.ipynb
        └── README.md
```

### Notebook Overview

`4-AstraDBVectorStore.ipynb` covers cloud vector-store initialization, document ingestion, embedding generation, retrieval, metadata filtering, and RAG chain construction.

---

## 📘 Notebook Walkthrough

### 1. Installing Dependencies

The notebook specifies compatible LangChain package versions:

```python
%pip install \
    "langchain>=0.3.23,<0.4" \
    "langchain-core>=0.3.52,<0.4" \
    "langchain-astradb>=0.6,<0.7"
```

These packages provide the LangChain framework and Astra DB vector-store integration used in the notebook.

Additional integrations are needed for the Hugging Face embeddings and Groq chat model.

### 2. Configuring Astra DB Credentials

The notebook defines two configuration values:

```python
ASTRA_DB_API_ENDPOINT = "your_astra_db_api_endpoint"
ASTRA_DB_APPLICATION_TOKEN = "your_astra_db_application_token"
```

These values connect the notebook to an Astra DB database.

- **API endpoint:** Identifies the target Astra DB database.
- **Application token:** Authenticates access to the database.

Keep credentials private. For reusable projects, load them through environment variables rather than hardcoding secrets in a notebook.

### 3. Generating Hugging Face Embeddings

The notebook uses the `sentence-transformers/all-MiniLM-L6-v2` model:

```python
from langchain_huggingface import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The embedding model converts text into numerical vectors that represent semantic information.

These vectors allow Astra DB to index and retrieve documents based on their meaning rather than relying exclusively on exact keyword matches.

### 4. Initializing AstraDBVectorStore

The notebook connects LangChain to Astra DB:

```python
from langchain_astradb import AstraDBVectorStore

vector_store = AstraDBVectorStore(
    embedding=embeddings,
    api_endpoint=ASTRA_DB_API_ENDPOINT,
    collection_name="astra_vector_langchain",
    token=ASTRA_DB_APPLICATION_TOKEN,
    namespace=None
)
```

The configuration specifies the embedding model, database endpoint, collection name, authentication token, and namespace.

The vector store provides a LangChain interface for inserting documents and performing vector searches against Astra DB.

### 5. Creating Documents with Metadata

The notebook creates ten sample `Document` objects covering different topics, including:

- Food and breakfast.
- Weather forecasts.
- LangChain and LangGraph.
- News and crime.
- Movies and product reviews.
- Football.
- Stock-market news.

Each document contains text and metadata such as its source category.

Example:

```python
from langchain_core.documents import Document

document = Document(
    page_content=(
        "Robbers broke into the city bank "
        "and stole $1 million in cash."
    ),
    metadata={"source": "news"}
)
```

Metadata makes it possible to categorize documents and constrain retrieval to relevant subsets.

### 6. Inserting Documents into Astra DB

The notebook inserts the prepared documents into the cloud vector store:

```python
vector_store.add_documents(
    documents=documents
)
```

The integration generates embeddings for the documents and stores the associated content and metadata in the configured Astra DB collection.

Unlike a purely in-memory store, the cloud database is designed to retain indexed data beyond the lifetime of the Python session.

### 7. Semantic Similarity Search

The notebook searches for documents related to weather:

```python
results = vector_store.similarity_search(
    "what is the weather"
)
```

It also searches for content related to LangChain while limiting the results to documents whose metadata identifies their source as a tweet:

```python
results = vector_store.similarity_search(
    "LangChain provides abstractions to make working with LLMs easy",
    k=3,
    filter={"source": "tweet"}
)
```

This demonstrates how semantic similarity and metadata constraints can work together during retrieval.

### 8. Similarity Search with Scores

The notebook uses:

```python
results = vector_store.similarity_search_with_score(
    "LangChain provides abstractions to make working with LLMs easy",
    k=3,
    filter={"source": "tweet"}
)
```

Each result includes a document and its associated retrieval score.

These scores help inspect retrieval behavior, but their interpretation depends on the vector-store implementation and configured similarity metric. They should not automatically be treated as probabilities.

### 9. Similarity-Score-Threshold Retrieval

The notebook configures a retriever using a score threshold:

```python
retriever = vector_store.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={
        "k": 1,
        "score_threshold": 0.5
    }
)
```

This approach is intended to return documents that satisfy a relevance threshold rather than relying exclusively on a fixed number of nearest neighbors.

The threshold must be interpreted according to the score convention used by the underlying vector-store integration.

### 10. Maximum Marginal Relevance (MMR)

The notebook also experiments with MMR retrieval:

```python
retriever = vector_store.as_retriever(
    search_type="mmr",
    search_kwargs={"k": 1}
)
```

Maximum Marginal Relevance balances query relevance with diversity among retrieved documents.

This can help reduce redundant results when several documents contain similar information.

The notebook invokes the retriever with a crime-related query and a news-source metadata filter.

### 11. Initializing the Groq Chat Model

For answer generation, the notebook initializes a Groq-hosted chat model:

```python
from langchain_groq import ChatGroq

llm = ChatGroq(
    model_name="openai/gpt-oss-120b"
)
```

The model is used to generate responses based on the retrieved document context.

A valid Groq API key is required for the LLM operations.

### 12. Building a Basic RAG Chain with LCEL

The notebook creates a prompt that instructs the LLM to answer using the supplied context:

```python
simple_prompt = ChatPromptTemplate.from_template("""
Answer the question based only on the following context:
Context: {context}

Question: {question}

Answer:
""")
```

It then defines a retriever that returns up to three documents:

```python
retriever = vector_store.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)
```

A document-formatting function combines the retrieved text and source metadata into a single context string.

The LCEL chain connects retrieval, formatting, prompt construction, LLM generation, and string output parsing:

```python
simple_rag_chain = (
    {
        "context": retriever | format_docs,
        "question": RunnablePassthrough()
    }
    | simple_prompt
    | llm
    | StrOutputParser()
)
```

This is the main RAG pipeline demonstrated in the notebook.

### 13. Conversational RAG Prompt

The notebook defines a conversational prompt with a system instruction, a chat-history placeholder, and the current question:

```python
conversational_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        "You are a helpful AI assistant. "
        "Use the provided context to answer questions."
    ),
    ("placeholder", "{chat_history}"),
    ("human", "Context: {context}\n\nQuestion: {input}")
])
```

It also creates a function that retrieves context and connects it to the conversational prompt and LLM.

**Implementation note:** Although the prompt includes `chat_history`, the current `create_conversational_rag()` function does not itself implement persistent conversation memory or pass history through its retrieval function. Those capabilities would need to be added for a fully functional conversational RAG system.

### 14. Streaming RAG Chain

The notebook defines a streaming chain:

```python
streaming_rag_chain = (
    {
        "context": retriever | format_docs,
        "question": RunnablePassthrough()
    }
    | simple_prompt
    | llm
)
```

It then uses the chain's `.stream()` method to print response chunks as they arrive.

Streaming provides incremental output instead of waiting for the entire answer to be generated.

### 15. Testing the RAG Pipeline

The notebook defines a test function that runs both the basic RAG chain and streaming chain.

Example questions include:

- How much cash did the robbers steal?
- Tell me about the incident.
- Calculate the loss after the robbery.
- Who won?

These questions test how the retriever and LLM handle different queries against the sample document collection.

The quality of the answers depends on whether the relevant information exists in the indexed documents and whether the retrieval step returns it.

---

## 🔄 End-to-End Workflow

```text
Sample Documents
       |
       v
Hugging Face Embeddings
       |
       v
Astra DB Vector Store
       |
       v
Cloud Vector Collection
       |
       v
Similarity Search
       |
       v
Metadata Filtering / MMR
       |
       v
Retrieved Documents
       |
       v
Context Formatting
       |
       v
LCEL Prompt
       |
       v
Groq Chat Model
       |
       v
Generated / Streamed Answer
```

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| LangChain Core | Documents, prompts, runnables, and output parsing |
| LangChain Astra DB | Cloud vector-store integration |
| Astra DB | Cloud-hosted vector storage and retrieval |
| Hugging Face | Embedding integration |
| Sentence Transformers | Pretrained embedding model |
| LangChain Groq | LLM integration |
| python-dotenv | Environment configuration |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- An Astra DB database with a valid API endpoint and application token.
- A Groq API key.
- Internet access to download the embedding model and access cloud services.

### 1. Clone the Repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the Project Folder

```bash
cd "RAG/2-Vector Stores and Vector Databases/4-AstraDB Vector Store"
```

### 3. Install Dependencies

```bash
python -m pip install \
    "langchain>=0.3.23,<0.4" \
    "langchain-core>=0.3.52,<0.4" \
    "langchain-astradb>=0.6,<0.7" \
    langchain-huggingface \
    langchain-groq \
    sentence-transformers \
    python-dotenv \
    jupyter
```

### 4. Configure Environment Variables

Create a local `.env` file:

```text
ASTRA_DB_API_ENDPOINT=your_astra_db_api_endpoint
ASTRA_DB_APPLICATION_TOKEN=your_astra_db_application_token
GROQ_API_KEY=your_groq_api_key
```

Update the notebook to load these values with `os.getenv()` and `load_dotenv()`.

Never commit API tokens, `.env` files, or other credentials to GitHub.

### 5. Run the Notebook

Open `4-AstraDBVectorStore.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

Ensure that the Astra DB endpoint, credentials, collection configuration, and required API keys are correct before running database and LLM operations.

---

## ⚠️ Current Limitations

- The notebook uses a small, manually created document collection.
- Database access requires valid Astra DB credentials and network connectivity.
- The Groq model requires a valid API key.
- The conversational prompt includes a chat-history placeholder, but the current function does not implement complete conversation-history management.
- The notebook does not include a formal retrieval evaluation benchmark.
- The threshold-based retrieval configuration requires validation against the score semantics of the installed integration.
- Production features such as authentication controls for end users, monitoring, and automated tests are outside the current implementation.

---

## 🔮 Future Improvements

- Load documents from PDFs, DOCX files, and other external sources.
- Add a reusable ingestion pipeline.
- Implement proper conversational memory.
- Explore history-aware query rewriting.
- Compare similarity search, threshold retrieval, and MMR on the same queries.
- Evaluate retrieval quality using precision, recall, and ranking metrics.
- Add source citations to generated answers.
- Add robust error handling and configuration validation.
- Build a FastAPI service around the retrieval and generation pipeline.
- Add automated tests and deployment documentation.

---

## 📚 Key Takeaway

This project demonstrates how Astra DB can serve as a cloud-based vector store in a LangChain RAG workflow.

It connects Hugging Face embeddings, cloud document storage, semantic retrieval, metadata filtering, alternative retrieval strategies, and Groq-powered answer generation.

Together with the ChromaDB, FAISS, and in-memory vector-store projects, it strengthens the practical understanding of vector storage choices for modern AI applications.

---

## 👨‍💻 Author

**Aadi**
