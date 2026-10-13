# 🗄️ ChromaDB — Vector Stores & RAG with LangChain

A hands-on project exploring **ChromaDB, vector embeddings, similarity search, and Retrieval-Augmented Generation (RAG)** using Python and LangChain.

This notebook demonstrates how to load documents, split them into smaller chunks, generate embeddings with Hugging Face, store vectors in ChromaDB, retrieve relevant information, and build RAG pipelines using LangChain.

It also explores LCEL-based retrieval chains, adding new documents to an existing vector store, and conversational RAG with chat history.

## 🎯 Learning Objectives

- Understand the role of vector stores in RAG.
- Load text documents using LangChain document loaders.
- Split documents into smaller chunks while preserving metadata.
- Generate embeddings using Hugging Face.
- Create and persist a ChromaDB vector store.
- Perform similarity search and inspect distance scores.
- Build retrieval chains using LangChain.
- Explore LangChain Expression Language (LCEL).
- Add new documents to an existing vector store.
- Implement history-aware retrieval for conversational RAG.

## 📂 Project Structure

```text
2-Vector Stores and Vector Databases/
│
├── 1-ChromaDB/
│   ├── 1-chromadb.ipynb
│   ├── README.md
│   │
│   ├── chroma_db/
│   │   ├── [generated collection directory]/
│   │   └── chroma.sqlite3
│   │
│   ├── data/
│   │   ├── doc_0.txt
│   │   ├── doc_1.txt
│   │   └── doc_2.txt
│   │
│   └── faiss_index/
│
└── readme.md
```

**Note:** The notebook creates sample text files and persists the ChromaDB collection under `./chroma_db`. The `faiss_index/` directory appears in the repository structure, but FAISS indexing is not implemented in this notebook.

The exact contents of generated database files and collection directories may vary between runs.

## 📘 Notebook Walkthrough

### 1. RAG Architecture Overview

The notebook introduces the major stages of a RAG system:

1. Document loading
2. Document splitting
3. Embedding generation
4. Vector storage
5. Query embedding
6. Similarity search
7. Context augmentation
8. LLM response generation

This workflow connects document processing with retrieval and language-model-based answering.

### 2. Creating Sample Documents

The notebook defines educational sample documents covering topics such as:

- Machine Learning Fundamentals
- Deep Learning and Neural Networks
- Natural Language Processing and related concepts

These documents provide the knowledge base used for retrieval experiments.

The notebook writes the sample content to text files using Python.

### 3. Loading Documents

The notebook uses LangChain's `DirectoryLoader` and `TextLoader` to load text files.

```python
from langchain_community.document_loaders import DirectoryLoader, TextLoader

loader = DirectoryLoader(
    "data",
    glob="*.txt",
    loader_cls=TextLoader,
    loader_kwargs={"encoding": "utf-8"}
)

documents = loader.load()
```

The loaded documents contain text and associated metadata, including source information.

### 4. Splitting Documents into Chunks

The notebook uses `RecursiveCharacterTextSplitter` to divide documents into smaller chunks.

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    length_function=len,
    separators=[" "]
)

chunks = text_splitter.split_documents(documents)
```

The configuration specifies a target chunk size of 500 characters and an overlap of 50 characters.

Chunking helps create smaller units of text that can be embedded and retrieved independently.

**Important:** The notebook explicitly configures a space as its separator, rather than using the text splitter's usual hierarchy of paragraph, line, and character separators.

### 5. Generating Embeddings with Hugging Face

The notebook initializes the following embedding model:

```python
from langchain_huggingface import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

This pretrained model converts document chunks and queries into numerical vectors.

Using local sentence-transformer embeddings allows the notebook to build a vector store without requiring an OpenAI embedding API key.

The model files may need to be downloaded on the first run.

### 6. Creating a Persistent ChromaDB Vector Store

The notebook creates a ChromaDB vector store from the processed chunks.

```python
from langchain_community.vectorstores import Chroma

persist_directory = "./chroma_db"

vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    persist_directory=persist_directory,
    collection_name="rag_collection"
)
```

This operation:

- Stores the document chunks.
- Generates and stores their embeddings.
- Associates the vectors with document content and metadata.
- Persists the collection to the specified directory.

The notebook prints the collection's vector count and persistence location.

### 7. Performing Similarity Search

The notebook tests retrieval with questions such as:

- What are the types of machine learning?
- What is NLP?
- What is deep learning?

```python
query = "What are the types of machine learning?"

similar_docs = vectorstore.similarity_search(
    query,
    k=3
)
```

The search returns the three most relevant document chunks according to the configured vector-store search behavior.

The notebook also prints retrieved content and source metadata.

### 8. Similarity Search with Scores

The notebook uses:

```python
results_scores = vectorstore.similarity_search_with_score(
    query,
    k=3
)
```

This returns retrieved documents alongside their distance scores.

For the default ChromaDB configuration used here, the score is a distance measure rather than a cosine similarity value.

**Interpretation:** Lower L2 distance generally indicates closer vectors. Do not interpret these raw distances as probabilities or assume they are normalized similarity percentages.

### 9. Initializing the Language Model

The notebook initializes a Groq-hosted chat model through LangChain.

```python
from langchain_groq import ChatGroq

llm = ChatGroq(
    model_name="openai/gpt-oss-120b"
)
```

The LLM is used to generate responses based on questions and retrieved context.

This requires a correctly configured Groq API environment and a model identifier supported by the installed integration and provider account.

### 10. Building a Modern RAG Chain

The notebook uses LangChain's retrieval-chain utilities:

- `create_retrieval_chain`
- `create_stuff_documents_chain`
- `ChatPromptTemplate`

The prompt instructs the model to answer using retrieved context and to acknowledge when the answer is unknown.

The document chain combines retrieved documents into a context prompt before sending it to the LLM.

The retrieval chain connects the retriever with the document-processing chain.

Conceptually:

```text
User Question
      |
      v
Retriever
      |
      v
Relevant Documents
      |
      v
Context Prompt
      |
      v
Chat Model
      |
      v
Generated Answer
```

The notebook tests the chain with questions about deep learning, machine learning, and related topics.

### 11. Building a RAG Chain with LCEL

The notebook also constructs an alternative pipeline using LangChain Expression Language (LCEL).

It uses:

- `RunnablePassthrough`
- `RunnableParallel`
- `StrOutputParser`
- A custom prompt template

A `format_docs()` function combines retrieved document text into a context string.

The LCEL pipeline then passes the formatted context and question through the prompt and language model.

This demonstrates a more composable way to build retrieval and generation workflows.

### 12. Adding New Documents to an Existing Vector Store

The notebook creates a new document about reinforcement learning and assigns metadata:

```python
from langchain_core.documents import Document

new_doc = Document(
    page_content=new_document,
    metadata={
        "source": "manual_addition",
        "topic": "reinforcement_learning"
    }
)
```

It splits the new document into chunks and inserts them into the existing ChromaDB collection.

```python
new_chunks = text_splitter.split_documents([new_doc])

vectorstore.add_documents(new_chunks)
```

The notebook prints the number of added chunks and the updated vector count.

A follow-up query then tests retrieval of the newly added reinforcement-learning information.

### 13. Conversational RAG with Chat History

The notebook introduces history-aware retrieval to handle follow-up questions that depend on earlier conversation context.

For example:

```text
User: What is machine learning?
Assistant: Explains machine learning.

User: What are its main types?
```

The second question depends on the earlier mention of machine learning.

The notebook uses:

- `create_history_aware_retriever`
- `MessagesPlaceholder`
- `HumanMessage` and `AIMessage`
- `create_retrieval_chain`

The history-aware retriever uses the conversation history to reformulate context-dependent questions before retrieving relevant documents.

The resulting conversational RAG chain combines retrieval with a question-answering document chain.

The notebook demonstrates a follow-up question using an explicitly maintained `chat_history` list.

## 🔄 Overall Workflow

```text
Sample Text Files
        |
        v
   Document Loading
        |
        v
   Text Chunking
        |
        v
Hugging Face Embeddings
        |
        v
   ChromaDB Store
        |
        v
   Similarity Search
        |
        v
 Retrieved Context
        |
        v
   Groq Chat Model
        |
        v
    RAG Answer
        |
        v
History-Aware Retrieval
```

The notebook also explores LCEL as an alternative way to compose the retrieval and generation stages.

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experimentation |
| LangChain Community | Document loaders and Chroma integration |
| LangChain Core | Documents, prompts, messages, and runnables |
| LangChain Classic | Retrieval-chain utilities |
| LangChain Text Splitters | Document chunking |
| LangChain Hugging Face | Local embedding generation |
| ChromaDB | Persistent vector storage and retrieval |
| LangChain Groq | Chat model integration |
| NumPy | Numerical operations |
| python-dotenv | Environment variable management |

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- Internet access for downloading the embedding model.
- A Groq API key for LLM-based RAG examples.
- A compatible installed version of the LangChain integrations.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/2-Vector Stores and Vector Databases/1-ChromaDB"
```

### 3. Install dependencies

```bash
python -m pip install jupyter python-dotenv numpy chromadb langchain-community langchain-core langchain-classic langchain-text-splitters langchain-huggingface langchain-groq langchain-openai
```

Some imports in the notebook are not required by every execution path, but the command installs the main integrations used across the notebook.

### 4. Configure environment variables

Create a local `.env` file containing your Groq API key:

```text
GROQ_API_KEY=your_groq_api_key
```

Never commit API keys or secret environment files to GitHub.

### 5. Run the notebook

Open `1-chromadb.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

The notebook creates sample documents, builds the vector store, performs retrieval experiments, and constructs RAG chains.

## ⚠️ Current Limitations and Notes

- The notebook uses a small educational document collection.
- The text loader uses the `data/` directory and expects matching `.txt` files.
- The sample-file creation code writes files to the current working directory, while the loader reads from `data/`. Ensure the files are placed in the expected directory before running the loader.
- The notebook's text splitter uses a space-only separator.
- ChromaDB's returned distance scores should not be confused with cosine similarity scores.
- The retriever configuration uses `search_kwarg`; the supported parameter is typically `search_kwargs`.
- The notebook uses a provider-specific Groq model identifier that may require updating if unavailable.
- Persistence and collection counts can change when the notebook is executed repeatedly.
- The `faiss_index/` directory is not used by this notebook.
- The notebook does not implement production-grade access controls, evaluation, or deployment.

## 🔮 Future Improvements

- Standardize sample-file creation and loading paths.
- Correct the retriever configuration to use `search_kwargs`.
- Add source citations and richer metadata to generated answers.
- Compare ChromaDB retrieval with FAISS.
- Experiment with similarity thresholds and metadata filtering.
- Add persistent chat-history management.
- Evaluate retrieval relevance and answer quality.
- Add automated tests for document ingestion and retrieval.
- Build an API around the RAG pipeline using FastAPI.
- Add monitoring, error handling, and deployment configuration.

## 📚 Key Takeaway

This project connects the major building blocks of a practical RAG system: document ingestion, chunking, embeddings, persistent vector storage, similarity search, and LLM-based answer generation.

It also introduces LCEL, document updates, and history-aware retrieval, providing a foundation for more advanced RAG applications.

## 👨‍💻 Author

**Aadi**
