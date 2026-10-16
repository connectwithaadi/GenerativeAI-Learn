# 🔎 Hybrid Retrieval — Combining Dense and Sparse Retrievers

A hands-on Generative AI project demonstrating **hybrid retrieval by combining dense semantic search and sparse keyword-based search** to improve document retrieval in Retrieval-Augmented Generation (RAG) applications.

This project uses **FAISS, Hugging Face embeddings, BM25, LangChain's `EnsembleRetriever`, and a Groq-hosted LLM** to retrieve relevant documents and generate context-aware answers.

The implementation explores how semantic similarity and keyword matching complement each other, followed by their integration into a complete RAG pipeline.

---

## 🎯 Learning Objectives

- Understand dense and sparse retrieval.
- Explore semantic search using vector embeddings.
- Implement keyword-based retrieval using BM25.
- Combine multiple retrievers using LangChain's `EnsembleRetriever`.
- Configure retriever weights to balance retrieval strategies.
- Retrieve relevant documents for natural-language queries.
- Build a RAG pipeline using retrieved context.
- Generate answers using a large language model (LLM).
- Inspect source documents used to generate answers.

---

## 📂 Project Structure

```text
1-Combining Dense and Sparse Retriever/
│
├── 1-Combining_Dense_and_Sparse_Retriever.ipynb
└── README.md
```

---

## 🧠 Core Concepts

### 1. Dense Retrieval

Dense retrieval represents the semantic meaning of text using vector embeddings.

In this project, Hugging Face's `all-MiniLM-L6-v2` embedding model converts documents into vectors, while FAISS provides the vector store for similarity-based retrieval.

**Technologies:**

- Hugging Face Embeddings
- `all-MiniLM-L6-v2`
- FAISS
- LangChain

**Key advantage:** Dense retrieval can identify semantically related content even when queries and documents use different wording.

### 2. Sparse Retrieval

Sparse retrieval focuses on lexical matching between queries and documents.

This project uses **BM25**, a ranking algorithm that scores documents according to the relevance of their terms to a query.

**Key advantage:** Sparse retrieval can identify documents containing important keywords, technical terms, and exact phrases that may not rank as highly through semantic retrieval alone.

### 3. Hybrid Retrieval

Hybrid retrieval combines dense and sparse retrieval to benefit from both semantic understanding and keyword matching.

The notebook implements this approach using LangChain's `EnsembleRetriever`.

```python
hybrid_retriever = EnsembleRetriever(
    retrievers=[dense_retriever, sparse_retriever],
    weights=[0.7, 0.3]
)
```

The configured weights are:

- **70% weight:** Dense retriever using FAISS and Hugging Face embeddings.
- **30% weight:** Sparse retriever using BM25.

These values configure the relative contribution of the retrievers during ensemble result fusion. They do not guarantee that 70% of the retrieved relevance comes from semantic search and 30% from keyword matching.

---

## ⚙️ Implementation Workflow

### Step 1: Prepare Sample Documents

The notebook creates five sample documents covering LangChain, Pinecone, semantic search, agentic AI applications, and retrievers.

These documents form the knowledge base used in the retrieval experiments.

### Step 2: Build the Dense Retriever

The notebook loads the `all-MiniLM-L6-v2` embedding model and creates a FAISS vector store from the sample documents.

The vector store is then exposed through a LangChain retriever for similarity-based document retrieval.

### Step 3: Build the Sparse Retriever

A BM25 retriever is initialized using the same documents.

The notebook configures the sparse retriever to return up to three documents.

### Step 4: Combine Both Retrievers

The dense and sparse retrievers are combined using `EnsembleRetriever` with weights of `0.7` and `0.3`.

The resulting hybrid retriever combines the retrieval results from both strategies.

### Step 5: Test Hybrid Retrieval

The notebook tests retrieval using the following query:

```text
How can I build an application using LLM?
```

The retrieved documents are printed so their relevance to the query can be inspected.

### Step 6: Configure the LLM and Prompt

The project uses LangChain's `ChatGroq` integration with the configured model identifier `openai/gpt-oss-120b`.

A prompt template instructs the model to answer questions using the supplied context.

The notebook configures the model with a temperature of `0.2`.

### Step 7: Build the RAG Pipeline

The notebook creates a document-combination chain using `create_stuff_documents_chain` and combines it with the hybrid retriever through `create_retrieval_chain`.

This connects document retrieval with LLM-based answer generation.

### Step 8: Generate an Answer and Inspect Sources

The final query is passed to the RAG chain:

```python
response = rag_chain.invoke({
    "input": "How can I build an app using LLMs?"
})
```

The notebook prints:

- The generated answer.
- The source documents returned in the RAG context.

This makes it possible to inspect the context supplied to the model alongside its generated answer.

---

## 🔄 Architecture

```text
                    Sample Documents
                           |
                 +---------+---------+
                 |                   |
                 v                   v
          Dense Retrieval      Sparse Retrieval
                 |                   |
         Hugging Face                BM25
          Embeddings                 |
                 |                   |
               FAISS                 |
                 |                   |
                 +---------+---------+
                           |
                           v
                   EnsembleRetriever
                     Weights
                      0.7 / 0.3
                           |
                           v
                   Retrieved Context
                           |
                           v
                    Prompt Template
                           |
                           v
                        Groq LLM
                           |
                           v
                    Generated Answer
                           |
                           v
                    Source Documents
```

---

## 🧮 Hybrid Scoring — Conceptual Foundation

Hybrid retrieval can be described conceptually as a weighted combination of dense and sparse retrieval scores:

**Hybrid scoring formula:**

`S_hybrid = α × S_dense + (1 − α) × S_sparse`

Where:

- `S_hybrid` represents the combined retrieval score.
- `S_dense` represents the dense retrieval score, illustrated using cosine similarity.
- `S_sparse` represents the sparse retrieval score, illustrated using TF-IDF/BM25-style keyword relevance.
- `α` is a weighting parameter between `0` and `1` that controls the relative contribution of each score.

The accompanying PDF uses an illustrative weight of `0.5` in its worked example, whereas the notebook configures `EnsembleRetriever` with weights `[0.7, 0.3]`.

**Important distinction:** The equation illustrates weighted score fusion conceptually. The notebook uses LangChain's `EnsembleRetriever` to combine retrieval results. Its weights should not be interpreted as a direct arithmetic combination of raw cosine-similarity and BM25 scores.

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| LangChain | Retriever and RAG orchestration |
| Hugging Face | Text embedding generation |
| `all-MiniLM-L6-v2` | Embedding model |
| FAISS | Dense vector retrieval |
| BM25 | Sparse keyword-based retrieval |
| `EnsembleRetriever` | Combines dense and sparse retrieval results |
| Groq | LLM inference integration |
| `openai/gpt-oss-120b` | Configured LLM model identifier |

---

## 🚀 Getting Started

### Prerequisites

- Python and Jupyter Notebook.
- A compatible environment for Hugging Face embeddings.
- A Groq API key.
- The Python packages required by the notebook.

### 1. Install Dependencies

The notebook uses `uv` for dependency management. The following commands illustrate the packages used by the project:

```bash
uv add langchain-community langchain-huggingface
uv add langchain-classic langchain-groq
uv add faiss-cpu sentence-transformers rank_bm25
```

**Note:** Package requirements may vary depending on the installed LangChain versions and the imports used in the notebook.

### 2. Configure the Groq API Key

Set your API key as an environment variable before running the notebook.

**PowerShell:**

```powershell
$env:GROQ_API_KEY="your_groq_api_key"
```

Keep your API key private and never commit it to GitHub.

### 3. Run the Notebook

Open `1-Combining_Dense_and_Sparse_Retriever.ipynb` and execute the cells in order.

The notebook loads the embedding model, builds the retrievers, tests hybrid retrieval, and constructs the RAG pipeline.

---

## 📌 Key Takeaways

- Dense retrieval captures semantic relationships between queries and documents.
- Sparse retrieval supports keyword-based matching through BM25.
- Hybrid retrieval combines complementary retrieval strategies.
- Ensemble weights influence how retrieval results are combined.
- RAG uses retrieved documents as context for LLM-generated answers.
- Inspecting source documents helps identify the context supplied to the model.

---

## 🔮 Future Improvements

- Compare dense, sparse, and hybrid retrieval on the same evaluation dataset.
- Experiment with different ensemble weights.
- Evaluate retrieval using Precision@K, Recall@K, and Mean Reciprocal Rank (MRR).
- Explore reciprocal rank fusion and other result-fusion methods.
- Add metadata filtering and document identifiers.
- Test larger document collections.
- Implement automated evaluation and retrieval benchmarks.
- Integrate the pipeline into a FastAPI application.

---

## 👨‍💻 Author

**Aadi**

Part of my hands-on learning journey toward Generative AI and AI Engineering.

---

## 📚 Repository

This project is part of the [GenerativeAI-Learn](https://github.com/connectwithaadi/GenerativeAI-Learn) repository, which documents hands-on learning across RAG, embeddings, vector databases, and other Generative AI concepts.
