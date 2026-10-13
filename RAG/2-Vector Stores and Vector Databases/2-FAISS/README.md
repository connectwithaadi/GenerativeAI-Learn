# ⚡ FAISS — Vector Search & RAG with LangChain

A hands-on project exploring **FAISS vector indexing, semantic search, metadata filtering, and Retrieval-Augmented Generation (RAG)** using Python, Hugging Face embeddings, LangChain, and Groq.

This notebook demonstrates how to convert documents into vector embeddings, build a FAISS index, persist and reload the index, retrieve relevant documents, filter search results using metadata, and integrate retrieved context into LLM-powered RAG pipelines.

The project builds on embedding fundamentals and introduces a practical alternative to ChromaDB for vector similarity search.

---

## 🎯 Learning Objectives

- Understand FAISS and its role in vector search.
- Load and process documents using LangChain.
- Split documents into manageable chunks.
- Generate embeddings using Hugging Face.
- Build a FAISS vector index from document chunks.
- Save and reload a local FAISS index.
- Perform similarity searches.
- Inspect retrieval distance scores.
- Apply metadata-based filtering.
- Build RAG pipelines using LangChain Expression Language (LCEL).
- Stream LLM responses.
- Experiment with conversational RAG and chat history.

---

## 📂 Project Structure

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
├── chroma_db/                  # Generated ChromaDB data
├── data/                       # Sample text files
├── faiss_index/                # Saved FAISS index artifacts
└── readme.md                   # Parent module documentation
```

**Note:** This represents the repository layout shown in the current project screenshot. The `chroma_db/`, `data/`, and `faiss_index/` directories are currently shown at the parent level. The actual location used by the notebook's relative paths depends on the working directory from which it is executed.

---

## 📘 Notebook Walkthrough

### 1. Introduction to FAISS

FAISS (Facebook AI Similarity Search) is a library for efficient similarity search and clustering of dense vectors.

The notebook introduces its main advantages:

- Efficient vector similarity search.
- Memory-conscious vector indexing.
- Support for large vector collections.
- GPU acceleration in supported configurations.

FAISS provides the vector-search infrastructure used in this project, while LangChain provides the integration with documents and embedding models.

### 2. Document Ingestion and Processing

The notebook creates sample `Document` objects containing educational content about:

- Artificial Intelligence
- Machine Learning
- Deep Learning
- Related technical concepts

Each document contains text and metadata, including fields such as `source`, `page`, and `topic`.

This structure allows the notebook to preserve document information throughout chunking and retrieval.

### 3. Text Splitting

The notebook uses `RecursiveCharacterTextSplitter` to divide documents into smaller chunks.

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    length_function=len,
    separators=[" "]
)

chunks = text_splitter.split_documents(sample_documents)
```

The configured chunk size is 500 characters, with an overlap of 50 characters.

Chunking makes long documents easier to embed and retrieve in smaller, relevant pieces.

The notebook also prints the generated chunks and their metadata to inspect the output.

### 4. Generating Embeddings with Hugging Face

The notebook uses the following sentence-transformer model:

```python
from langchain_huggingface import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The model converts text into numerical vector representations.

The notebook demonstrates:

- Embedding a single query.
- Generating embeddings for multiple texts.
- Comparing semantic similarity between text pairs.

It implements a `compare_embeddings()` function using NumPy cosine similarity and tests examples such as:

- AI vs. Artificial Intelligence
- AI vs. Pizza
- Machine Learning vs. ML

These experiments illustrate how vector representations can help compare related and unrelated text.

### 5. Creating a FAISS Vector Store

The notebook builds a FAISS vector store from the processed document chunks.

```python
from langchain_community.vectorstores import FAISS

vectorstore = FAISS.from_documents(
    documents=chunks,
    embedding=embeddings
)

print(vectorstore.index.ntotal)
```

This operation generates embeddings for the documents and adds them to the FAISS index.

The notebook inspects the total number of indexed vectors after creation.

### 6. Saving and Loading the Index

The notebook demonstrates local persistence using `save_local()` and `load_local()`.

```python
vectorstore.save_local("faiss_index")
```

The saved index can later be loaded using the same embedding model:

```python
loaded_vectorstore = FAISS.load_local(
    "faiss_index",
    embeddings,
    allow_dangerous_deserialization=True
)
```

This allows the index to be reused without rebuilding it from scratch every time.

**Security note:** Only load locally saved FAISS artifacts from sources you trust when using dangerous deserialization. Never enable this option for untrusted index files.

### 7. Similarity Search

The notebook retrieves relevant document chunks for a query such as:

```python
query = "What is deep learning"

results = vectorstore.similarity_search(
    query,
    k=3
)
```

The search returns the top three matching documents.

The notebook prints the source metadata and a preview of each retrieved chunk, making it easier to understand what information was selected for a query.

### 8. Similarity Search with Scores

The notebook uses:

```python
result_with_score = vectorstore.similarity_search_with_score(
    query,
    k=3
)
```

This returns retrieved documents alongside their search scores.

For the default FAISS index created by this LangChain integration, the scores are typically **squared L2 distances**.

- Lower distance generally indicates a closer vector match.
- Scores are not probabilities.
- Raw distances should not be interpreted as normalized similarity percentages.

### 9. Metadata Filtering

The notebook demonstrates filtering search results using document metadata.

```python
filter_dict = {"topic": "ML"}

filtered_results = vectorstore.similarity_search(
    query,
    k=3,
    filter=filter_dict
)
```

This illustrates how retrieval can be constrained to documents matching a particular metadata field.

Metadata filtering helps organize retrieval by topic, source, document type, or other attributes.

### 10. Building a Basic RAG Chain with LCEL

The notebook integrates FAISS retrieval with a Groq-hosted language model.

It uses:

- `ChatPromptTemplate`
- `RunnablePassthrough`
- `StrOutputParser`
- A document-formatting function
- A FAISS retriever

The retriever is configured to return the top three documents:

```python
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)
```

The notebook formats retrieved documents and passes them into a prompt that instructs the LLM to answer using the supplied context.

The resulting pipeline combines retrieval, prompt construction, generation, and output parsing.

### 11. Conversational RAG

The notebook defines a conversational prompt containing:

- System instructions
- A `chat_history` placeholder
- Retrieved context
- The current user question

It also maintains conversation history using `HumanMessage` and `AIMessage`.

The notebook demonstrates a first question about machine learning and a follow-up question about how it differs from traditional programming.

**Implementation detail:** The current conversational function passes chat history into the prompt, but it does not independently rewrite the follow-up question using a history-aware retriever. That would be a useful future improvement.

### 12. Streaming RAG

The notebook defines a streaming chain that connects the retriever, document formatting, prompt, and chat model.

It uses the model's streaming interface to print response chunks as they arrive.

The notebook tests the RAG pipeline with questions about AI, machine learning, deep learning, and NLP.

Streaming can make an interactive application feel more responsive because users can see partial output before the full answer is generated.

---

## 🔄 Overall Workflow

```text
Sample Documents
       |
       v
 Document Chunking
       |
       v
Hugging Face Embeddings
       |
       v
    FAISS Index
       |
       v
Save / Load Index
       |
       v
Similarity Search
       |
       v
Metadata Filtering
       |
       v
Retrieved Context
       |
       v
Groq Chat Model
       |
       v
Generated Answer
       |
       v
Streaming / Chat History
```

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| NumPy | Vector operations and cosine similarity |
| LangChain Core | Documents, prompts, runnables, and messages |
| LangChain Community | FAISS vector-store integration |
| LangChain Text Splitters | Document chunking |
| Hugging Face | Embedding generation |
| Sentence Transformers | Pretrained embedding model |
| FAISS | Vector indexing and similarity search |
| LangChain Groq | LLM integration |
| python-dotenv | Environment variable management |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- Internet access to download the embedding model and access the LLM API.
- A valid Groq API key for the RAG examples.

### 1. Clone the Repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the FAISS Project

```bash
cd "RAG/2-Vector Stores and Vector Databases/2-FAISS"
```

### 3. Install Dependencies

```bash
python -m pip install jupyter python-dotenv numpy faiss-cpu langchain-core langchain-community langchain-text-splitters langchain-huggingface langchain-groq langchain-openai
```

The notebook imports `langchain-openai` components, although the demonstrated embedding workflow uses Hugging Face and the RAG examples use Groq.

### 4. Configure Environment Variables

Create a local `.env` file containing your Groq API key:

```text
GROQ_API_KEY=your_groq_api_key
```

Never upload API keys or secret environment files to GitHub.

### 5. Run the Notebook

Open `2-FAISS.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

Check the working directory before saving or loading the index so that the `faiss_index` path resolves consistently.

---

## ⚠️ Current Limitations

- The notebook uses a small, manually defined educational dataset.
- The text splitter uses a space-only separator.
- FAISS index persistence uses a local directory.
- Similarity scores are raw distances rather than normalized relevance probabilities.
- Metadata filtering is demonstrated with a simple topic filter.
- The conversational chain passes history to the prompt but does not perform history-based query rewriting.
- The notebook suppresses warnings, which may hide useful diagnostic information.
- API access is required for Groq-based response generation.
- The project does not implement production deployment, automated evaluation, or access controls.

---

## 🔮 Future Improvements

- Add a dedicated comparison between ChromaDB and FAISS.
- Experiment with different FAISS index types.
- Improve chunking strategies and separators.
- Add metadata filtering examples with additional fields.
- Implement history-aware query rewriting.
- Add retrieval evaluation using precision, recall, and ranking metrics.
- Add source references to generated answers.
- Improve error handling and environment configuration.
- Build a FastAPI endpoint for the RAG pipeline.
- Add tests for ingestion, indexing, persistence, and retrieval.

---

## 📚 Key Takeaway

This project demonstrates how FAISS can be used to build a reusable vector-search system and connect it to a retrieval-augmented generation pipeline.

It progresses from document chunking and Hugging Face embeddings to vector indexing, persistence, metadata filtering, and LCEL-based RAG.

Together with the ChromaDB module, it provides a practical foundation for understanding different approaches to vector storage and retrieval in AI applications.

---

## 👨‍💻 Author

**Aadi**
