# 🤗 Hugging Face Embeddings with LangChain

A hands-on Python project exploring **text embeddings using Hugging Face models and LangChain**.

This notebook demonstrates how to initialize a pretrained sentence-transformer model, convert text into numerical vectors, generate embeddings for multiple sentences, and explore popular open-source embedding models and their use cases.

The goal is to understand how real embedding models transform text into vector representations for semantic search and Retrieval-Augmented Generation (RAG).

## 🎯 Learning Objectives

- Understand text embeddings using pretrained models.
- Integrate Hugging Face embedding models with LangChain.
- Initialize an embedding model without requiring an API key.
- Generate embeddings for individual text inputs.
- Generate embeddings for multiple documents.
- Understand embedding dimensions.
- Explore popular open-source embedding models.
- Compare embedding model characteristics and use cases.
- Prepare for semantic search and vector database integration.

## 📂 Project Structure

```text
2-HuggingFace Embeddings/
├── 2-HuggingfaceEmbeddings.ipynb
└── README.md
```

The notebook contains the implementation and experiments for this module.

## 📘 Notebook Walkthrough

### 1. Installing the Dependency

The notebook begins with:

```bash
uv add langchain-huggingface
```

This installs the LangChain Hugging Face integration using `uv`.

### 2. Importing Hugging Face Embeddings

The notebook imports the embedding integration:

```python
from langchain_huggingface import HuggingFaceEmbeddings
```

This class provides an interface for using supported Hugging Face sentence-transformer models with LangChain.

### 3. Initializing a Pretrained Embedding Model

The notebook initializes the following model:

```python
embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The model is loaded locally through the Hugging Face and sentence-transformers ecosystem. On the first run, the required model files may need to be downloaded.

**Key concepts:**

- `HuggingFaceEmbeddings` connects a pretrained embedding model to LangChain.
- `model_name` specifies which pretrained model to use.
- The notebook does not require a hosted embedding API key for this model.

### 4. Generating an Embedding for a Single Text

The notebook creates an embedding for a sample sentence:

```python
text = "Hello, I am learning about embeddings!"

embedding = embeddings.embed_query(text)

print(f"Text: {text}")
print(f"Embedding Length: {len(embedding)}")
print(embedding)
```

The `embed_query()` method converts the input text into a numerical vector.

For `sentence-transformers/all-MiniLM-L6-v2`, the output vector has **384 dimensions**.

The notebook prints the original text, the embedding length, and the generated vector.

### 5. Generating Embeddings for Multiple Sentences

The notebook defines a list of example sentences:

```python
sentences = [
    "The cat sat on the mat",
    "The cat sat on the mat",
    "The dog played in the yard",
    "I love programming in Python",
    "Python is my favorite programming language"
]

embedding_sentence = embeddings.embed_documents(sentences)

print(embedding_sentence[0])
print(embedding_sentence[1])
```

The `embed_documents()` method generates an embedding for each sentence in the input list.

The first two sentences are identical, making them useful for observing how the same input is represented consistently by the same model under the same configuration.

The remaining sentences provide examples involving different topics and related programming concepts.

**Important:** The notebook prints the first two embeddings. It does not calculate cosine similarity or rank the sentences by semantic similarity in this section.

### 6. Exploring Popular Embedding Models

The notebook presents a comparison of five embedding models using their dimensions, general characteristics, and intended use cases.

| Model | Dimensions | Example Use Case |
|---|---:|---|
| `all-MiniLM-L6-v2` | 384 | General-purpose embeddings and real-time applications |
| `all-mpnet-base-v2` | 768 | Applications prioritizing embedding quality |
| `all-MiniLM-L12-v2` | 384 | A balance of speed and quality |
| `multi-qa-MiniLM-L6-cos-v1` | 384 | Question answering and semantic search |
| `paraphrase-multilingual-MiniLM-L12-v2` | 384 | Multilingual applications |

The notebook stores this information in a Python dictionary and prints each model's name, embedding size, description, and suggested use case.

These descriptions are introductory guidance rather than benchmark results produced by the notebook.

**Note:** Although the notebook imports `time`, it does not implement a timing benchmark or load all five models for an empirical performance comparison.

## 🔄 Embedding Workflow

```text
Input Text
    |
    v
HuggingFaceEmbeddings
    |
    v
Pretrained Sentence Transformer
    |
    v
Numerical Vector
    |
    v
384-Dimensional Embedding
    |
    v
Downstream Semantic Search / RAG
```

The workflow illustrates how the selected model converts text into a vector representation. Semantic search and RAG are downstream applications, not implemented in this notebook.

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| Jupyter Notebook | Interactive experiments |
| Hugging Face | Pretrained embedding models |
| Sentence Transformers | Text-to-vector encoding |
| LangChain Hugging Face | Embedding integration |
| `uv` | Python dependency management |

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or a compatible version.
- Jupyter Notebook or VS Code.
- `uv` package manager, or another compatible Python package installation workflow.
- Internet access for downloading the model files on the first run, unless they are already cached.

### 1. Clone the repository

```bash
git clone https://github.com/connectwithaadi/GenerativeAI-Learn.git
cd GenerativeAI-Learn
```

### 2. Navigate to the project folder

```bash
cd "RAG/1-VectorEmbeddingsAndDatabases/2-HuggingFace Embeddings"
```

### 3. Install the dependency

Using `uv`:

```bash
uv add langchain-huggingface
```

Or using `pip`:

```bash
python -m pip install langchain-huggingface
```

If Jupyter is not already installed in your environment:

```bash
python -m pip install jupyter
```

### 4. Run the notebook

Open `2-HuggingfaceEmbeddings.ipynb` in Jupyter Notebook or VS Code and execute the cells in order.

The notebook initializes the model, generates embeddings, prints vector dimensions, and displays the model comparison.

## ⚠️ Current Limitations

- The notebook initializes `sentence-transformers/all-MiniLM-L6-v2` as its working model.
- The other listed models are discussed through descriptive metadata, not empirically evaluated.
- No cosine similarity calculations are performed in this notebook.
- No vector database is connected.
- No document indexing or semantic retrieval pipeline is implemented.
- No retrieval-quality or latency benchmark is performed.
- Model downloads and local inference require suitable environment configuration.

## 🔮 Future Improvements

- Calculate cosine similarity between generated sentence embeddings.
- Compare semantically similar and dissimilar sentences.
- Benchmark model latency and memory requirements.
- Load and compare multiple embedding models experimentally.
- Explore batch processing for larger document collections.
- Integrate embeddings with FAISS or another vector store.
- Build a semantic search pipeline over real documents.
- Use the embeddings in a complete RAG application.

## 📚 Key Takeaway

Hugging Face and LangChain provide a practical way to generate text embeddings using pretrained sentence-transformer models.

This notebook moves beyond manually defined vectors and introduces actual model-generated embeddings, providing a foundation for vector stores, semantic search, and RAG retrieval.

## 👨‍💻 Author

**Aadi**
