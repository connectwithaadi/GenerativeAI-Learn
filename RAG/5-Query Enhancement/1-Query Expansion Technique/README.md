
# 🔍 Query Expansion Technique — Enhancing RAG Retrieval

A hands-on Generative AI project exploring **Query Expansion**, a query enhancement technique used to improve document retrieval in Retrieval-Augmented Generation (RAG) applications.

The project demonstrates how expanding a user's original query into multiple related search queries can help retrieve relevant information from a knowledge base. It explores query generation using an LLM, retrieval across the expanded queries, and the role of query expansion in improving the quality and coverage of retrieved context.

This project is part of my **Generative AI and RAG learning journey**, focusing on advanced retrieval strategies for building more effective LLM applications.

---

## 🎯 Learning Objectives

- Understand query expansion and its role in RAG.
- Learn why a single user query may not retrieve all relevant documents.
- Explore how LLMs can generate alternative or related search queries.
- Work with document retrieval using expanded queries.
- Understand how query reformulation can improve retrieval coverage.
- Explore the relationship between query expansion and context preparation.
- Study the practical implementation through a Jupyter Notebook.
- Understand the limitations of query expansion, including additional retrieval cost and potentially irrelevant results.

---

## 📂 Project Structure

```text
1-Query Expansion Technique/
│
├── 1-Query_Expansion.ipynb
├── 45-Query+Expansion+Technique.pdf
├── langchain_crewai_dataset.txt
└── README.md
```

### File Description

| File | Description |
|---|---|
| `1-Query_Expansion.ipynb` | Notebook containing the practical implementation and experiments. |
| `45-Query+Expansion+Technique.pdf` | Reference notes explaining query expansion concepts and techniques. |
| `langchain_crewai_dataset.txt` | Text dataset used as the knowledge source for retrieval experiments. |
| `README.md` | Project documentation and learning notes. |

---

## 🧠 Core Concepts

### 1. What Is Query Expansion?

Query expansion is a retrieval enhancement technique in which an original search query is transformed into multiple related queries or enriched with additional terms.

For example, a user might ask:

```text
How does an AI agent use tools?
```

An expansion process could generate related queries such as:

```text
1. How do AI agents interact with external tools?
2. How does tool calling work in LLM applications?
3. How do autonomous agents execute tasks using APIs?
```

These queries explore different aspects of the original question and can help retrieve relevant documents that a single query might miss.

*The queries above are illustrative examples, not claimed outputs from the notebook.*

### 2. Why Query Expansion Matters

A user's question may use different wording from the documents in a knowledge base. Searching only the original query can therefore miss useful information.

Query expansion can help by:

- Increasing the coverage of relevant information.
- Capturing alternative terminology and related concepts.
- Retrieving documents from different perspectives.
- Improving the context available to a downstream LLM.

However, expansion does not guarantee better retrieval. Poorly generated queries can introduce noise, duplicate results, and additional computational cost.

### 3. Query Expansion in RAG

In a RAG system, query expansion can be applied before document retrieval.

The expanded queries are used to search the knowledge base. The retrieved documents are then combined, optionally deduplicated or reranked, and supplied to the LLM as context.

This improves the retrieval stage without requiring the entire knowledge base to be placed in the model's prompt.

---

## ⚙️ Implementation Workflow

The following workflow describes the general query expansion process. Refer to the notebook for the exact implementation details and executed experiments.

### Step 1: Prepare the Knowledge Source

The project includes `langchain_crewai_dataset.txt`, which serves as the text knowledge source for retrieval experiments.

The dataset provides content against which the original and expanded queries can be evaluated.

### Step 2: Define the Original Query

Start with a natural-language question representing the user's information need.

The original query establishes the intent that the expansion process should preserve.

### Step 3: Generate Expanded Queries

An LLM can generate alternative search queries based on the original question.

The goal is to explore related terminology and different ways of expressing the same information need without drifting away from the user's intent.

### Step 4: Retrieve Relevant Documents

Each expanded query can be used to search the knowledge base.

The retrieved results may contain overlapping documents, so combining results and removing duplicates can be useful.

### Step 5: Prepare the Retrieved Context

Relevant documents are collected into a context for the downstream generation stage.

Depending on the implementation, the retrieved results can also be filtered, ranked, or reranked before being passed to the LLM.

### Step 6: Evaluate Retrieval Quality

Compare the retrieved results against those obtained from the original query.

Useful evaluation questions include:

- Did the expanded queries find additional relevant documents?
- Did retrieval coverage improve?
- Were irrelevant or duplicate results introduced?
- Did the additional retrieval work improve the final answer?

---

## 🔄 Architecture

```text
        User Question
              |
              v
       Original Query
              |
              v
      Query Expansion
         (LLM-based)
              |
              v
      Multiple Queries
              |
              v
      Document Retrieval
              |
              v
   Combine Retrieved Results
              |
              v
  Filter / Deduplicate / Rank
              |
              v
       Retrieved Context
              |
              v
          RAG LLM
              |
              v
      Context-Aware Answer
```

This diagram illustrates a typical query expansion workflow. The exact components depend on the implementation in the notebook.

---

## 🛠️ Technology and Concepts

| Technology / Concept | Purpose |
|---|---|
| Python | Core implementation and experimentation |
| Jupyter Notebook | Interactive development and testing |
| Query Expansion | Generates alternative or related search queries |
| Large Language Models | Can be used to generate expanded queries |
| Text Dataset | Provides the knowledge source for retrieval |
| Retrieval-Augmented Generation | Uses retrieved documents to ground LLM responses |

The exact libraries, LLM provider, and retrieval components should be confirmed from the notebook before treating them as implementation dependencies.

---

## 🚀 Getting Started

### Prerequisites

- Python installed.
- Jupyter Notebook or JupyterLab.
- The libraries required by the notebook.
- API credentials if the implementation uses a hosted LLM.

### 1. Clone the Repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the Project

```bash
cd "RAG/5-Query Enhancement/1-Query Expansion Technique"
```

### 3. Install Dependencies

Install the packages required by the notebook in your Python environment. The exact installation commands depend on the imports used in `1-Query_Expansion.ipynb`.

### 4. Run the Notebook

Open `1-Query_Expansion.ipynb` in Jupyter Notebook or JupyterLab and execute the cells in order.

Use the accompanying PDF for conceptual reference and `langchain_crewai_dataset.txt` as the project dataset.

**Note:** If the notebook uses an external LLM API, configure the required credentials through environment variables and never commit API keys to GitHub.

---

## 📈 Potential Improvements

- Compare original-query retrieval against expanded-query retrieval.
- Experiment with different numbers of generated queries.
- Deduplicate overlapping retrieval results.
- Add a reranking stage to improve context relevance.
- Evaluate retrieval using Recall@K, Precision@K, and Mean Reciprocal Rank (MRR).
- Measure latency and token costs introduced by expansion.
- Test on larger and more diverse datasets.
- Integrate query expansion into a complete RAG application.

---

## 📌 Key Takeaways

- Query expansion generates alternative or related queries to improve retrieval coverage.
- Different query formulations can retrieve complementary information.
- Expanded retrieval results may require deduplication and reranking.
- More retrieved documents do not automatically mean better context.
- Retrieval quality should be evaluated rather than assumed.
- Query expansion is a useful technique to explore when developing advanced RAG systems.

---

## 👨‍💻 Author

**Aadi**

Part of my hands-on journey toward Generative AI and AI Engineering.

**Repository:** [GenerativeAI-Learn](https://github.com/connectwithaadi/GenerativeAI-Learn)

