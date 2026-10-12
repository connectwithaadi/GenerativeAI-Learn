# 🤖 OpenAI Embeddings with LangChain

A hands-on project exploring **OpenAI text embeddings, cosine similarity, and semantic search** using Python and LangChain.

This notebook demonstrates how to convert text into numerical vector representations using OpenAI's `text-embedding-3-small` model, compare sentence embeddings using cosine similarity, and build a simple semantic search function that retrieves the most relevant documents for a given query.

The goal is to understand how real-world embedding models support semantic retrieval and how these concepts form a foundation for Retrieval-Augmented Generation (RAG).

---

## 🎯 Learning Objectives

- Understand OpenAI text embeddings.
- Integrate OpenAI embedding models with LangChain.
- Generate embeddings for individual text inputs.
- Generate embeddings for multiple documents.
- Understand embedding dimensions.
- Explore different OpenAI embedding models.
- Implement cosine similarity using NumPy.
- Compare semantic similarity between sentences.
- Build a basic semantic search function.
- Rank documents according to query similarity scores.

---

## 📂 Project Structure

```text
3-OpenAI Embeddings/
├── 3-OpenaiEmbeddings.ipynb
└── README.md
```

- `3-OpenaiEmbeddings.ipynb` — practical implementation of embeddings, similarity calculations, and semantic search.
- `Embeddings.pdf` — accompanying learning material.
- `README.md` — project documentation.

---

## 📘 Notebook Walkthrough

### 1. Environment Configuration

The notebook uses `python-dotenv` to load environment variables and configure the OpenAI API key.

```python
import os
from dotenv import load_dotenv

load_dotenv()
```

The OpenAI API key is required to make embedding requests.

**Security best practice:** Store the key in a local `.env` file and never upload the key to GitHub.

Example `.env` file:

```text
OPENAI_API_KEY=your_openai_api_key
```

The notebook also contains a Windows-specific `.env` path for checking configuration. For portability, use a project-relative configuration or load the environment file from a known repository location.

### 2. Initializing OpenAI Embeddings

The notebook initializes LangChain's `OpenAIEmbeddings` integration.

```python
from langchain_openai import OpenAIEmbeddings

embeddings = OpenAIEmbeddings(
    model="text-embedding-3-small"
)
```

The selected model converts text into numerical vectors that can be compared mathematically.

Key concepts:

- `OpenAIEmbeddings` provides LangChain integration for OpenAI's embedding API.
- `model` specifies the embedding model.
- Embedding requests require valid API credentials and available API access.

### 3. Generating an Embedding for a Single Text

The notebook creates an embedding for a sample sentence:

```python
single_text = (
    "Langchain and Rag are amazing frameworks "
    "and projects to work on"
)

single_embeddings = embeddings.embed_query(single_text)
```

The notebook prints the resulting vector, its length, and a sample of its values.

For `text-embedding-3-small`, the default embedding dimension is **1,536**.

The vector represents the input text numerically and can be used in downstream similarity calculations.

### 4. Generating Embeddings for Multiple Documents

The notebook creates embeddings for four sample texts:

```python
multiple_texts = [
    "Python is a programming language",
    "LangChain is a framework for LLM applications",
    "Embeddings convert text to numbers",
    "Vectors can be compared for similarity"
]

multiple_embeddings = embeddings.embed_documents(
    multiple_texts
)
```

The notebook inspects the generated embeddings and prints:

- Number of input texts.
- Number of generated embeddings.
- Dimension of each embedding vector.

Each input text receives its own vector representation.

This is an important step when preparing collections of documents for semantic search or vector database indexing.

### 5. Comparing OpenAI Embedding Models

The notebook introduces three OpenAI embedding models through a Python dictionary.

| Model | Dimensions in Notebook | General Use Case |
|---|---:|---|
| `text-embedding-3-small` | 1,536 | Cost-effective, general-purpose embeddings |
| `text-embedding-3-large` | 3,072 | Applications prioritizing embedding quality |
| `text-embedding-ada-002` | 1,536 | Legacy applications |

The notebook also includes descriptive information and reference pricing values for these models.

**Note:** This is a static comparison defined in the notebook, not a live pricing lookup or an empirical benchmark. Check current OpenAI documentation before making model or cost decisions.

### 6. Implementing Cosine Similarity

The notebook implements cosine similarity using NumPy.

```python
import numpy as np

def cosine_similarity(vec1, vec2):
    dot_product = np.dot(vec1, vec2)
    norm_a = np.linalg.norm(vec1)
    norm_b = np.linalg.norm(vec2)

    return dot_product / (norm_a * norm_b)
```

Cosine similarity measures the angle between two vectors.

\[
\operatorname{cosine}(A,B)
=
\frac{A \cdot B}{\|A\|\|B\|}
\]

For nonzero vectors, the score ranges from -1 to 1.

| Score | General Interpretation |
|---|---|
| Close to `1` | Vectors point in similar directions |
| Close to `0` | Vectors are approximately perpendicular |
| Close to `-1` | Vectors point in opposite directions |

The interpretation of embedding similarity depends on the model and the task. A score alone does not guarantee that two sentences are semantically equivalent.

### 7. Comparing Sentence Embeddings

The notebook creates embeddings for five example sentences:

- The cat sat on the mat.
- A feline rested on the rug.
- The dog played in the yard.
- I love programming in Python.
- Python is my favorite programming language.

It calculates cosine similarity for every unique pair of sentence embeddings.

This produces a comparison of all sentence pairs, illustrating how an embedding model can represent relationships between different expressions.

The notebook prints the sentences alongside their calculated similarity scores.

### 8. Building a Semantic Search Function

The notebook implements a custom function:

```python
def semantic_search(
    query,
    documents,
    embeddings_models,
    top_k=3
):
    query_embedding = embeddings_models.embed_query(query)
    doc_embeddings = embeddings_models.embed_documents(
        documents
    )

    similarities = []

    for i, doc_emb in enumerate(doc_embeddings):
        similarity = cosine_similarity(
            query_embedding,
            doc_emb
        )
        similarities.append((similarity, documents[i]))

    similarities.sort(reverse=True)

    return similarities[:top_k]
```

The function performs the following operations:

1. Generates an embedding for the user's query.
2. Generates embeddings for the supplied documents.
3. Calculates cosine similarity between the query and each document.
4. Associates each score with its corresponding document.
5. Sorts the results in descending score order.
6. Returns the top `k` results.

The notebook tests semantic search with queries such as:

```python
query = "What is Langchain?"
results = semantic_search(query, documents, embeddings)
```

It also tests:

```python
query = "What is Embeddings?"
results = semantic_search(query, documents, embeddings)
```

The results are printed with similarity scores, demonstrating the basic principle behind embedding-based retrieval.

**Important:** This is an in-memory semantic search implementation. It generates document embeddings again when the function runs and does not use a vector database.

---

## 🔄 Semantic Search Workflow

```text
              User Query
                  |
                  v
           Query Embedding
                  |
                  v
          Document Collection
                  |
                  v
         Document Embeddings
                  |
                  v
         Cosine Similarity
                  |
                  v
       Rank by Similarity Score
                  |
                  v
           Select Top-K
                  |
                  v
         Relevant Documents
```

This workflow demonstrates the retrieval component that can later be integrated into a larger RAG application.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| OpenAI Embeddings API | Generate text embeddings |
| LangChain OpenAI | OpenAI embedding integration |
| NumPy | Vector operations and cosine similarity |
| python-dotenv | Environment variable management |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- An OpenAI API key with access to the embedding model.
- API billing or available quota sufficient for the requests.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/1-VectorEmbeddingsAndDatabases/3-OpenAI Embeddings"
```

### 3. Install dependencies

```bash
python -m pip install jupyter langchain-openai python-dotenv numpy
```

### 4. Configure the API key

Create a local `.env` file in the repository root or the directory from which the notebook loads environment variables.

```text
OPENAI_API_KEY=your_openai_api_key
```

Ensure `.env` is included in `.gitignore` and is not committed to the repository.

### 5. Run the notebook

Open `3-OpenaiEmbeddings.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

The notebook initializes the embedding model, generates vectors, calculates sentence similarity, and runs semantic search examples.

---

## ⚠️ Current Limitations

- OpenAI API credentials and available quota are required.
- The notebook depends on an external embedding API.
- Model comparison details and reference prices are hardcoded.
- Cosine similarity does not explicitly handle zero vectors.
- Semantic search generates document embeddings again on each call.
- Documents and vectors are held in memory.
- No persistent vector database is used.
- No metadata filtering or hybrid search is implemented.
- No end-to-end RAG question-answering pipeline is implemented.
- Retrieval relevance is not evaluated against a labeled benchmark.

---

## 🔮 Future Improvements

- Add zero-vector validation to cosine similarity.
- Cache or reuse document embeddings across searches.
- Store document embeddings in FAISS, Chroma, or another vector store.
- Add metadata-based filtering.
- Compare OpenAI embeddings with Hugging Face embedding models.
- Evaluate retrieval quality using precision, recall, or ranking metrics.
- Experiment with different `top_k` values.
- Integrate retrieved documents with an LLM to build a complete RAG pipeline.

---

## 📚 Key Takeaway

OpenAI embeddings transform text into numerical vectors that enable semantic comparison and similarity-based retrieval.

This notebook progresses from generating individual and batch embeddings to implementing cosine similarity and a working top-k semantic search function.

It establishes the foundation for the next stage: **persistent vector storage, efficient retrieval, and end-to-end RAG applications.**

## 👨‍💻 Author

**Aadi**
