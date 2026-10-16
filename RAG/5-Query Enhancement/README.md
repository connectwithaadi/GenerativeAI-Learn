# 🔍 Query Enhancement Techniques for RAG

A hands-on Generative AI learning module focused on **query enhancement techniques for Retrieval-Augmented Generation (RAG)**.

This module explores how user queries can be transformed or expanded before retrieval to improve the chances of finding relevant information from a knowledge base. It covers two important techniques: **Query Expansion** and **Query Decomposition**.

The repository includes Jupyter notebooks, supporting datasets, and PDF study notes for understanding and experimenting with these techniques.

---

## 🎯 Learning Objectives

- Understand the role of query enhancement in RAG pipelines.
- Learn how query expansion generates alternative search queries.
- Understand query decomposition for complex, multi-part questions.
- Explore how enhanced queries can improve document retrieval.
- Work with LangChain and LLM-based query transformation.
- Experiment with practical query enhancement implementations.
- Understand how query preprocessing fits into a RAG workflow.

---

## 📂 Repository Structure

```text
5-Query Enhancement/
│
├── 1-Query Expansion Technique/
│   ├── 1-Query_Expansion.ipynb
│   ├── 45-Query+Expansion+Technique.pdf
│   ├── langchain_crewai_dataset.txt
│   └── README.md
│
├── 2-Query Decomposition/
│   ├── 2-Query_Decomposition.ipynb
│   ├── langchain_crewai_dataset.txt
│   └── README.md
│
└── README.md
```

---

## 🧠 Core Concepts

### 1. Query Expansion

Query expansion improves a search query by generating alternative phrasings, related terms, or additional search queries that express the user's original intent.

**Example:**

Original query:
> How can I build an application using an LLM?

Possible expanded queries:
- How do I develop an application powered by a large language model?
- What frameworks can be used to build LLM applications?
- How can LLMs be integrated into software applications?

These variations can help retrieval systems find relevant documents that may use different terminology from the original query.

**Study materials:** `1-Query Expansion Technique/`

### 2. Query Decomposition

Query decomposition breaks a complex question into smaller, more focused sub-questions that can be handled individually.

**Example:**

Original query:
> How does RAG work, which vector databases can it use, and how does retrieval improve the final answer?

Possible sub-questions:
1. What is Retrieval-Augmented Generation, and how does it work?
2. Which vector databases can be used in a RAG pipeline?
3. How does retrieving relevant context improve LLM-generated answers?

The results from the individual sub-questions can be combined to help answer the original question.

**Study materials:** `2-Query Decomposition/`

---

## ⚙️ RAG Workflow

```text
          User Query
               |
               v
       Query Enhancement
               |
        +------+------+
        |             |
        v             v
 Query Expansion  Query Decomposition
        |             |
        +------+------+
               |
               v
         Document Retrieval
               |
               v
       Retrieved Documents
               |
               v
        LLM Answer Generation
               |
               v
          Final Answer
```

*This diagram illustrates the conceptual role of query enhancement. The exact workflow depends on the implementation.*

---

## 📚 Study Materials

| Folder | Contents |
|---|---|
| `1-Query Expansion Technique` | Query expansion notebook, supporting dataset, and PDF study notes |
| `2-Query Decomposition` | Query decomposition notebook and supporting dataset |

The notebooks provide hands-on practice, while the accompanying materials support conceptual understanding.

---

## 🛠️ Technologies and Tools

- **Python** — implementation and experimentation
- **Jupyter Notebook** — interactive development
- **LangChain** — LLM application and retrieval workflows
- **Large Language Models (LLMs)** — query transformation and decomposition
- **Text datasets** — supporting content for experiments

The exact dependencies depend on the code used in each notebook.

---

## 🚀 How to Explore

1. Start with `1-Query Expansion Technique` to understand how search queries can be expanded.
2. Review its notebook and PDF notes.
3. Move to `2-Query Decomposition` to explore breaking complex questions into smaller sub-questions.
4. Run the notebooks in order and inspect the generated queries and outputs.
5. Compare how each technique can support retrieval in a RAG application.

---

## 📌 Key Takeaways

- Query enhancement modifies or restructures queries before retrieval.
- Query expansion explores alternative expressions of the original question.
- Query decomposition separates complex questions into smaller sub-questions.
- Both techniques can help retrieval systems address different types of queries.
- Their effectiveness depends on query quality, the knowledge base, retrieval configuration, and evaluation.

---

## 🔮 Future Improvements

- Compare retrieval results before and after query enhancement.
- Evaluate Precision@K, Recall@K, and Mean Reciprocal Rank (MRR).
- Compare query expansion and decomposition on a common evaluation dataset.
- Measure retrieval latency and token usage.
- Integrate query enhancement with hybrid retrieval and reranking.
- Build an end-to-end RAG application with evaluation and monitoring.

---

## 👨‍💻 Author

**Aadi**

Part of my hands-on journey toward Generative AI and AI Engineering.
