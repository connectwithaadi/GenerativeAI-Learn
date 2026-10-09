# 🧠 Advanced Chunking — Semantic Chunking for RAG

A hands-on learning project exploring **Semantic Chunking**, a document-splitting technique that groups text based on semantic similarity rather than relying only on fixed character or token limits.

This project demonstrates how to implement a custom threshold-based semantic chunker, integrate semantic chunks into a Retrieval-Augmented Generation (RAG) pipeline, and explore LangChain's built-in `SemanticChunker`.

The goal is to understand how document chunking affects retrieval quality and helps build more contextually meaningful RAG applications.

---

## 📂 Project Structure

```text
3-Advance Chunking/
│
├── 1-Semantic_Chunking.ipynb
├── README.md
└── 33-Semantic-Chunking.pdf
```

- **`1-Semantic_Chunking.ipynb`** — Custom semantic chunking, RAG pipeline implementation, and LangChain SemanticChunker.
- **`README.md`** — Project overview, concepts, implementation details, and setup instructions.
- **`33-Semantic-Chunking.pdf`** — Handwritten notes covering semantic chunking and its workflow.

---

## 🎯 Learning Objectives

- Understand semantic chunking and its role in RAG.
- Generate sentence embeddings using Sentence Transformers.
- Measure semantic similarity using cosine similarity.
- Implement a custom threshold-based semantic chunker.
- Convert generated chunks into LangChain documents.
- Store and retrieve chunks using FAISS.
- Connect document retrieval with an LLM to build a RAG pipeline.
- Explore LangChain's built-in `SemanticChunker`.

---

## 🧩 What Is Semantic Chunking?

Semantic chunking divides a document into meaningful text segments based on the semantic similarity between sentences.

Traditional chunking strategies often split documents according to fixed character or token limits. Semantic chunking instead uses embedding representations to identify where the meaning or topic changes.

For example, consider these sentences:

1. LangChain is a framework for building applications with LLMs.
2. LangChain provides abstractions for integrating models with tools.
3. You can create chains, agents, memory, and retrievers.
4. The Eiffel Tower is located in Paris.
5. France is a popular tourist destination.

The first three sentences discuss related concepts and may form one chunk, while the last two may form another.

**Core idea:**

Better semantic grouping can improve retrieval relevance and provide more useful context to the language model. Actual improvements depend on the data, chunking strategy, embedding model, and retrieval configuration.

---

## ⚙️ How Semantic Chunking Works

The custom implementation follows these steps:

### 1. Sentence Segmentation

Split the input document into individual sentences.

### 2. Sentence Embeddings

Convert each sentence into a numerical vector using the `all-MiniLM-L6-v2` Sentence Transformer model.

### 3. Semantic Similarity

Calculate cosine similarity between consecutive sentence embeddings.

### 4. Threshold-Based Grouping

Compare the similarity score with a configurable threshold:

- If the score meets the threshold, append the sentence to the current chunk.
- Otherwise, close the current chunk and begin a new one.

### 5. Chunk Formation

Combine the grouped sentences into text chunks that can be passed to the retrieval pipeline.

**Important:** The custom implementation compares adjacent sentences. It is a simplified threshold-based semantic chunker, not a complete reproduction of every feature of LangChain's built-in `SemanticChunker`.

---

## 📓 Notebook Walkthrough

### 1. Custom Semantic Chunking

**Libraries:** `sentence-transformers`, `scikit-learn`, `numpy`

Implementation highlights:

- Load the `all-MiniLM-L6-v2` embedding model.
- Split sample text into sentences.
- Generate sentence embeddings.
- Calculate cosine similarity between adjacent sentences.
- Group sentences using a configurable similarity threshold.
- Print the resulting semantic chunks.

This section demonstrates the underlying logic behind semantic grouping.

### 2. Custom Chunker Class

A `ThresholdSemanticChunker` class encapsulates the chunking logic.

Its main methods are:

| Method | Purpose |
|---|---|
| `split(text)` | Splits text into semantic chunks. |
| `split_documents(docs)` | Applies chunking to LangChain documents while retaining metadata. |

The class uses a configurable threshold, allowing experimentation with different grouping behavior.

### 3. RAG Pipeline Integration

The notebook connects the custom semantic chunker to a retrieval-based question-answering pipeline.

**Pipeline workflow:**

```text
Input Document
      ↓
Semantic Chunking
      ↓
Hugging Face Embeddings
      ↓
FAISS Vector Store
      ↓
Retriever
      ↓
Retrieved Context + Question
      ↓
Prompt Template
      ↓
Groq-hosted LLM
      ↓
Generated Answer
```

Components used:

- **Document:** Represents the input text and metadata.
- **Hugging Face Embeddings:** Generates vector representations for document chunks and queries.
- **FAISS:** Stores vectors and supports similarity-based retrieval.
- **Retriever:** Fetches relevant chunks for a question.
- **PromptTemplate:** Structures the retrieved context and user question.
- **ChatGroq:** Connects the pipeline to a hosted language model.
- **StrOutputParser:** Converts the model response into a string.

The notebook uses `openai/gpt-oss-120b` as the model identifier in the `ChatGroq` configuration.

### 4. LangChain SemanticChunker

The final section explores the built-in `SemanticChunker` from `langchain_experimental.text_splitter`.

The workflow includes:

1. Loading a text document with `TextLoader`.
2. Configuring an embedding model.
3. Initializing `SemanticChunker`.
4. Splitting the loaded documents.
5. Inspecting the generated chunks.

This provides an introduction to using a library-provided semantic chunking implementation alongside the custom approach.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Implementation |
| Jupyter Notebook | Interactive experimentation |
| Sentence Transformers | Sentence embeddings |
| scikit-learn | Cosine similarity |
| NumPy | Numerical operations |
| LangChain | Document and RAG pipeline integration |
| Hugging Face Embeddings | Embedding generation |
| FAISS | Vector storage and retrieval |
| Groq | Hosted LLM inference |
| LangChain Experimental | Built-in semantic chunking |

---

## 🚀 Getting Started

### Prerequisites

- Python
- Jupyter Notebook
- A configured Python environment
- A Groq API key for the RAG section

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd RAG/3-Advance\ Chunking
```

Adjust the path if your repository uses a different folder name.

### 3. Install the dependencies

```bash
pip install jupyter sentence-transformers scikit-learn numpy
pip install langchain-classic langchain-core langchain-groq
pip install langchain-huggingface langchain-community
pip install langchain-experimental langchain-openai faiss-cpu
```

### 4. Configure the API key

Set your Groq API key as an environment variable before running the RAG cells.

**Windows PowerShell:**

```powershell
$env:GROQ_API_KEY="your_groq_api_key"
```

**macOS / Linux:**

```bash
export GROQ_API_KEY="your_groq_api_key"
```

Do not commit API keys or other secrets to GitHub.

### 5. Run the notebook

Open `1-Semantic_Chunking.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

The LangChain `SemanticChunker` section also requires a compatible embedding configuration and access to its input text file, `langchain_intro.txt`.

---

## 🔍 Experiments to Try

To understand the effect of semantic chunking, experiment with:

- Different similarity thresholds.
- Different input documents and topics.
- Different embedding models.
- Different semantic chunking strategies.
- Comparing semantic chunks against fixed-size chunks.
- Inspecting which chunks are retrieved for a given query.

For a meaningful comparison, use the same corpus, retrieval configuration, and evaluation questions wherever possible.

---

## ⚠️ Current Limitations

- Sentence segmentation is simplified and depends on punctuation.
- Adjacent-sentence similarity does not capture every possible relationship across a document.
- A fixed similarity threshold may not work equally well for every dataset.
- Chunk size, overlap, retrieval quality, and answer quality are not comprehensively benchmarked in this notebook.
- The RAG pipeline depends on compatible package versions, external API access, and valid credentials.

These limitations provide opportunities for future improvements.

---

## 🔮 Future Improvements

- Add robust sentence segmentation.
- Handle empty inputs and preserve source metadata consistently.
- Compare percentile-based, standard-deviation-based, and threshold-based chunking strategies.
- Add configurable chunk-size limits and overlap.
- Evaluate retrieval using metrics such as Recall@k and MRR.
- Compare answer relevance and groundedness across chunking methods.
- Record execution time and token usage.

---

## 📚 Key Takeaway

Semantic chunking is a useful technique for organizing documents into contextually meaningful units before embedding and retrieval.

This project explores the process from sentence embeddings and similarity-based grouping to vector retrieval and LLM-generated answers.

It serves as a foundation for experimenting with more advanced chunking and retrieval strategies in RAG applications.

---

## 👨‍💻 Author

**Aadi** 

GitHub: [@connectwithaadi](https://github.com/connectwithaadi)

Project Repository: [GenerativeAI-Learn](https://github.com/connectwithaadi/GenerativeAI-Learn)
