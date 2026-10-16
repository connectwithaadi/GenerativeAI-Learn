# 🔎 Hybrid Search Strategies for RAG

A hands-on Generative AI learning module focused on **hybrid search, reranking, and diversity-aware document retrieval** for Retrieval-Augmented Generation (RAG) applications.

This module explores three retrieval strategies that help improve how relevant documents are selected from a knowledge base. It covers combining dense and sparse retrievers, reranking retrieved results, and using Maximal Marginal Relevance (MMR) to balance relevance with diversity.

The repository includes Jupyter notebooks, supporting text datasets, and individual README files for each topic.

---

## 🎯 Learning Objectives

- Understand dense and sparse retrieval techniques.
- Combine semantic search and keyword-based retrieval.
- Explore hybrid retrieval using LangChain's `EnsembleRetriever`.
- Understand reranking and its role in improving retrieval quality.
- Learn how Maximal Marginal Relevance (MMR) balances relevance and diversity.
- Experiment with different retrieval strategies for RAG.
- Inspect retrieved documents and compare their relevance.
- Understand how retrieval strategies fit into an end-to-end RAG pipeline.

---

## 📂 Repository Structure

```text
4-Hybrid Search Strategies/
│
├── 1-Combining Dense and Sparse Retriever/
│   ├── 1-Combining_Dense_and_Sparse_Retriever.ipynb
│   └── README.md
│
├── 2-Reranking Hybrid Search Strategy/
│   ├── 2-reranking.ipynb
│   ├── langchain_sample.txt
│   └── README.md
│
├── 3-MMR Retriever/
│   ├── 3-MMR_Retriever.ipynb
│   ├── langchain_rag_dataset.txt
│   └── README.md
│
└── README.md
```

---

## 🧠 Core Concepts

### 1. Combining Dense and Sparse Retrievers

Hybrid retrieval combines two complementary approaches:

- **Dense retrieval:** Uses embeddings to retrieve semantically similar documents.
- **Sparse retrieval:** Uses keyword-based matching, such as BM25, to identify documents containing relevant terms.

Combining these approaches can help retrieve documents that match either the meaning or the specific terminology of a query.

**Study folder:** `1-Combining Dense and Sparse Retriever/`

### 2. Reranking Hybrid Search Results

Reranking is a retrieval refinement technique in which an initial set of candidate documents is scored again to improve their ordering according to their relevance to the query.

A typical workflow retrieves a broader set of candidate documents first, then uses a reranking model to prioritize the most relevant results.

**Study folder:** `2-Reranking Hybrid Search Strategy/`

### 3. Maximal Marginal Relevance (MMR)

Maximal Marginal Relevance (MMR) selects documents by balancing their relevance to the query against their similarity to documents already selected.

Its purpose is to return useful results while reducing unnecessary redundancy.

This can be especially valuable when multiple retrieved documents contain similar information.

**Study folder:** `3-MMR Retriever/`

---

## ⚙️ Retrieval Workflow

```text
              User Query
                   |
                   v
          Initial Retrieval
                   |
                   v
       Dense + Sparse Retrieval
                   |
                   v
          Candidate Documents
                   |
                   v
              Reranking
                   |
                   v
         Relevance and Diversity
                   |
                   v
          MMR-Based Selection
                   |
                   v
          Retrieved Context
                   |
                   v
          LLM Answer Generation
```

*This diagram illustrates how the techniques can fit into a RAG workflow. It is a conceptual overview, not a claim that every notebook implements this exact combined pipeline.*

---

## 📚 Learning Path

| Order | Topic | Main Focus |
|---|---|---|
| 1 | Combining Dense and Sparse Retriever | Semantic and keyword-based retrieval |
| 2 | Reranking Hybrid Search Strategy | Improving the ordering of retrieved candidates |
| 3 | MMR Retriever | Balancing query relevance and document diversity |

Following this order builds an understanding of retrieval from combining complementary search methods to refining the quality and variety of retrieved results.

---

## 🛠️ Technologies and Tools

- **Python** — core implementation
- **Jupyter Notebook** — interactive experimentation
- **LangChain** — retrieval and RAG orchestration
- **Hugging Face embeddings** — semantic representation of text
- **FAISS** — vector similarity search
- **BM25** — sparse keyword retrieval
- **Reranking models** — candidate relevance refinement
- **MMR** — relevance and diversity-aware retrieval

The exact libraries and models used may vary across individual notebooks.

---

## 🚀 How to Explore

1. Start with `1-Combining Dense and Sparse Retriever` to understand hybrid retrieval.
2. Continue with `2-Reranking Hybrid Search Strategy` to explore retrieval result refinement.
3. Move to `3-MMR Retriever` to study relevance and diversity-aware selection.
4. Review the supporting text datasets where available.
5. Run each notebook and inspect the retrieved documents and outputs.

---

## 📌 Key Takeaways

- Dense and sparse retrieval provide complementary ways to search a knowledge base.
- Hybrid retrieval combines semantic similarity and keyword matching.
- Reranking can improve the ordering of initially retrieved candidates.
- MMR helps reduce redundancy while maintaining relevance.
- Retrieval quality directly affects the context available to a RAG system.
- Different retrieval strategies should be evaluated against the requirements of the application.

---

## 🔮 Future Improvements

- Compare dense, sparse, and hybrid retrieval on a common evaluation dataset.
- Measure Precision@K, Recall@K, and Mean Reciprocal Rank (MRR).
- Evaluate reranking models using retrieval benchmarks.
- Experiment with MMR parameters and different diversity settings.
- Compare retrieval latency, relevance, and redundancy.
- Integrate hybrid retrieval, reranking, and MMR into a configurable RAG pipeline.
- Build an evaluation framework to compare retrieval strategies systematically.

---

## 👨‍💻 Author

**Aadi**

Part of my hands-on journey toward Generative AI and AI Engineering.
