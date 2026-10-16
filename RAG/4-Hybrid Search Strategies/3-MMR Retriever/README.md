# 🔎 Maximal Marginal Relevance (MMR) Retriever

A hands-on Generative AI project exploring **Maximal Marginal Relevance (MMR)** for improving document retrieval in Retrieval-Augmented Generation (RAG) applications.

This project focuses on retrieving relevant documents while maintaining diversity among the selected results. Instead of returning documents that are highly similar to one another, MMR balances query relevance with the reduction of redundant information.

The implementation uses LangChain's retriever functionality and a sample RAG dataset to explore how MMR can improve the quality and diversity of retrieved context.

---

## 🎯 Learning Objectives

- Understand Maximal Marginal Relevance (MMR).
- Learn the difference between similarity search and MMR retrieval.
- Understand relevance and redundancy in document retrieval.
- Explore MMR-based retrieval using LangChain.
- Configure retrieval parameters such as `k` and `fetch_k`.
- Inspect and compare retrieved documents.
- Understand how diverse context can benefit RAG pipelines.

---

## 📂 Project Structure

```text
3-MMR Retriever/
│
├── 3-MMR_Retriever.ipynb
├── langchain_rag_dataset.txt
└── README.md
```

### Files

| File | Description |
|---|---|
| `3-MMR_Retriever.ipynb` | Notebook for exploring MMR-based document retrieval. |
| `langchain_rag_dataset.txt` | Text dataset used for retrieval experiments. |
| `README.md` | Project documentation. |

---

## 🧠 Core Concepts

### 1. Similarity Search

Similarity search retrieves documents based primarily on their similarity to the user's query.

For example, if several documents discuss the same LangChain concept, similarity search may return multiple documents containing overlapping information.

**Limitation:** The retrieved results can be relevant but repetitive.

### 2. Maximal Marginal Relevance (MMR)

Maximal Marginal Relevance is a retrieval strategy that balances two objectives:

- **Relevance:** Select documents that are relevant to the query.
- **Diversity:** Avoid selecting documents that are too similar to documents already selected.

MMR is useful when a RAG application needs a diverse set of relevant documents rather than several near-duplicates.

### 3. How MMR Works

The MMR selection process can be described conceptually as follows:

1. Retrieve an initial set of candidate documents.
2. Evaluate how relevant each candidate is to the query.
3. Compare each candidate with the documents already selected.
4. Prefer candidates that provide relevant but less redundant information.
5. Continue until the required number of documents has been selected.

---

## 🧮 MMR Scoring Formula

The MMR objective is commonly expressed as:

```text
MMR = argmax [
    lambda * Sim(document, query)
    - (1 - lambda) * max Sim(document, selected_documents)
]
```

Where:

- `document` is a candidate document being considered.
- `query` is the user's search query.
- `selected_documents` are documents already chosen.
- `Sim(document, query)` measures the candidate's relevance to the query.
- `Sim(document, selected_documents)` measures redundancy with the selected results.
- `lambda` controls the balance between relevance and diversity.

The value of `lambda` is typically between `0` and `1`.

| Lambda value | Behavior |
|---|---|
| Closer to `1` | Gives more importance to query relevance. |
| Closer to `0` | Gives more importance to diversity. |
| Between `0` and `1` | Balances relevance and diversity. |

The formula describes the general MMR objective; the exact similarity calculation depends on the implementation.

---

## ⚙️ Implementation Workflow

### Step 1: Load the Dataset

The project includes `langchain_rag_dataset.txt`, which supplies text for the retrieval experiments.

The dataset provides content that can be processed into documents for similarity-based retrieval.

### Step 2: Prepare Documents

The text is prepared for retrieval using the document-processing components configured in the notebook.

Document preparation is an important step because the retrieval strategy operates on the representations of these documents.

### Step 3: Configure the Retriever

The retriever is configured to use MMR rather than relying exclusively on ordinary similarity search.

LangChain vector-store retrievers commonly expose MMR through the `search_type` parameter:

```python
retriever = vectorstore.as_retriever(
    search_type="mmr",
    search_kwargs={
        "k": 3,
        "fetch_k": 10
    }
)
```

This is an illustrative configuration; use the actual parameter values and vector store from the notebook.

- `k`: Number of documents returned to the caller.
- `fetch_k`: Number of candidate documents considered before MMR selects the final results.

### Step 4: Retrieve Relevant and Diverse Documents

A query is passed to the retriever, which selects documents by balancing query relevance against redundancy.

The returned results can then be inspected to understand how MMR changes the selection of documents.

### Step 5: Inspect the Results

Review the retrieved documents and compare their content.

The objective is to understand whether the results cover different relevant aspects of the query instead of repeatedly presenting similar information.

---

## 🔄 Conceptual Architecture

```text
             Text Dataset
                   |
                   v
          Document Preparation
                   |
                   v
          Vector Representations
                   |
                   v
           Candidate Retrieval
                fetch_k
                   |
                   v
          MMR Selection Strategy
       Relevance + Diversity Balance
                   |
                   v
          Final Retrieved Documents
                   |
                   v
           Context for RAG
```

This diagram illustrates the general MMR retrieval workflow. The exact components depend on the implementation in the notebook.

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| LangChain | Retriever configuration and orchestration |
| Vector embeddings | Representing document meaning |
| Vector store | Candidate document retrieval |
| MMR | Balancing relevance and diversity |
| Text dataset | Source content for retrieval experiments |

---

## 🚀 Getting Started

### Prerequisites

- Python installed.
- Jupyter Notebook or JupyterLab.
- The dependencies required by the notebook.
- Any embedding model credentials or API keys required by the selected implementation.

### 1. Install Dependencies

Install the packages required by your notebook. For example, if it uses LangChain's community vector-store integrations:

```bash
pip install langchain-community langchain-core langchain-huggingface sentence-transformers faiss-cpu
```

The exact dependencies depend on the embedding model and vector store used in the notebook.

### 2. Open the Notebook

Open `3-MMR_Retriever.ipynb` in Jupyter Notebook or JupyterLab.

### 3. Run the Cells

Execute the cells in order, ensuring the dataset path and any required model configuration are correct.

Inspect the retrieval results to understand the relationship between relevance and diversity.

---

## 📌 Key Takeaways

- Similarity search prioritizes matching the query.
- MMR balances relevance with diversity.
- Diverse retrieval can reduce redundant context in RAG applications.
- `k` controls the number of final results.
- `fetch_k` controls the candidate pool considered by MMR.
- The best relevance-diversity balance depends on the dataset and retrieval task.

---

## 🔮 Future Improvements

- Compare similarity search and MMR using identical queries.
- Experiment with different `lambda_mult`, `k`, and `fetch_k` values.
- Evaluate retrieval quality using Precision@K, Recall@K, and MRR.
- Test MMR on larger document collections.
- Integrate MMR retrieval into a complete RAG pipeline.
- Measure whether diverse retrieval improves answer quality and reduces redundant context.

---

## 👨‍💻 Author

**Aadi**

Part of my hands-on journey toward Generative AI and AI Engineering.
