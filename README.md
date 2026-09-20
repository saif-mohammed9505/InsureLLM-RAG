# InsureLLM RAG

An advanced **Retrieval-Augmented Generation (RAG)** project built for answering questions about the Insurellm knowledge base.

The project combines **LangChain, ChromaDB, Hugging Face embeddings, Ollama, OpenRouter, Hugging Face LLMs, retrieval, reranking, query rewriting, query expansion, and Gradio** to build an enhanced RAG pipeline.

---

## Project Overview

The system retrieves relevant information from an Insurellm knowledge base and provides that information to an LLM to generate concise answers.

The project goes beyond basic vector retrieval by experimenting with techniques that can improve the quality of retrieved context:

- Semantic retrieval
- Chunk reranking
- Query rewriting
- Query expansion
- Chunk merging and deduplication
- LLM-based reranking
- Context-based answer generation

The main RAG workflow is:

    User Question
          ↓
    Query Processing
          ↓
    Vector Retrieval
          ↓
    Query Expansion / Rewriting
          ↓
    Merge Retrieved Chunks
          ↓
    Reranking
          ↓
    Context Construction
          ↓
    LLM
          ↓
    Final Answer

---

## Knowledge Base

The project uses a structured Insurellm knowledge base containing documents organized into different categories.

    knowledge-base/
    ├── company/
    ├── contracts/
    ├── employees/
    └── products/

The knowledge base contains information about the company, employees, products, and contracts.

The retrieved documents are converted into chunks and stored in a vector database for semantic search.

---

## Notebooks

The project contains two main notebooks.

### 1. `insurellm_advance.ipynb`

This notebook contains the main advanced RAG implementation.

It includes:

- ChromaDB connection
- Hugging Face embeddings
- LangChain retriever
- Ollama LLM integration
- Hugging Face LLM integration
- OpenRouter integration
- Chunk retrieval
- Chunk reranking
- Query rewriting
- Query expansion
- Chunk merging
- Context construction
- Final answer generation
- Gradio integration

### 2. `insurellm_langchain_visualization.ipynb`

This notebook focuses on examining and visualizing the knowledge base and RAG components.

It is useful for understanding the documents, chunks, embeddings, and vector-store data before using them in the advanced RAG pipeline.

---

# RAG Pipeline

## 1. Embeddings

The project uses Hugging Face embeddings:

    all-MiniLM-L6-v2

The embedding model converts text chunks into numerical vectors.

    Text
      ↓
    Embedding Model
      ↓
    Vector Representation

These vectors allow the system to perform semantic similarity searches.

---

## 2. ChromaDB

The generated embeddings are stored in a ChromaDB vector store.

The notebook connects to the persisted vector database using:

    Chroma(
        persist_directory=db_name,
        embedding_function=embeddings
    )

The vector database is used as the retrieval layer for the RAG system.

---

## 3. Semantic Retrieval

A LangChain retriever is created from the ChromaDB vector store.

    retriver = vectordatastore.as_retriever()

When a question is received, relevant document chunks are retrieved from the knowledge base.

    User Question
          ↓
    ChromaDB Retriever
          ↓
    Relevant Chunks

---

# Chunk Reranking

Basic vector retrieval does not always return the chunks in the most useful order.

The project therefore introduces an additional reranking stage.

First, the retrieved chunks are collected:

    Question
       ↓
    Retriever
       ↓
    Retrieved Chunks

The chunks are then passed to an LLM-based reranker.

The reranker is instructed to order all retrieved chunks from the most relevant to the least relevant.

    Retrieved Chunks
          ↓
    LLM Reranker
          ↓
    Ranked Chunks

The notebook uses an Ollama `llama3.2` model for this reranking step.

This allows the system to reorder retrieved information before passing it to the final answer-generation model.

---

# Query Rewriting

The project also implements query rewriting.

A user's original question may not always be the best query for searching the knowledge base.

For example:

    Original:
    "who is the ceo?"

can be rewritten into a more specific knowledge-base query such as:

    "Who is the CEO of Insurellm?"

The rewriting function uses an OpenRouter model to generate a short, refined search query.

The goal is to make the retrieval query more specific and useful.

    User Question
          ↓
    Query Rewriting
          ↓
    Refined Knowledge-Base Query
          ↓
    Retrieval

The rewriting function also accepts conversation history so that conversational questions can be transformed into more complete queries.

---

# Query Expansion

The project further explores query expansion.

Instead of depending on only one search query, multiple related queries can be used to retrieve additional relevant chunks.

The notebook combines retrieved information from the original and rewritten queries.

    Original Query
          ↓
      Retrieval
          ↓
      Chunks
          \
           \
            → Merge → Deduplicate → Rerank
           /
          /
    Rewritten Query
          ↓
      Retrieval
          ↓
      Chunks

This approach can increase the amount of relevant information available to the final answer-generation stage.

---

# Chunk Merging

When multiple retrieval queries are used, some chunks may appear more than once.

The project therefore includes a chunk-merging step that checks whether a chunk is already present before adding it to the merged collection.

This helps avoid unnecessary duplicate context.

    Retrieved Set 1
          +
    Retrieved Set 2
          ↓
    Merge
          ↓
    Remove Duplicates
          ↓
    Combined Chunks

---

# Final Answer Generation

After retrieval and reranking, the selected chunks are provided to the LLM as context.

The system prompt instructs the model to:

- Answer using the provided context
- Give a direct and concise answer
- Avoid showing reasoning
- Avoid mentioning the retrieval process
- Say that it does not know when the answer is not present in the context
- Avoid inventing information

The final process is:

    User Question
          ↓
    Retrieve Chunks
          ↓
    Rewrite / Expand Query
          ↓
    Merge Chunks
          ↓
    Rerank Chunks
          ↓
    Build Context
          ↓
    LLM
          ↓
    Final Answer

---

# LLM Integrations

The project experiments with multiple LLM providers and interfaces.

## Ollama

The notebook uses Ollama with:

    llama3.2

Ollama is used for local LLM inference and for the chunk-reranking process.

## Hugging Face

The project uses Hugging Face components including:

- `HuggingFaceEmbeddings`
- `HuggingFaceEndpoint`
- `ChatHuggingFace`
- `InferenceClient`

The notebook also uses:

    swiss-ai/Apertus-8B-Instruct-2509

as a Hugging Face text-generation model.

## OpenRouter

OpenRouter is used through the OpenAI-compatible client.

It is used in the project for tasks such as query rewriting.

---

# Gradio

The project imports Gradio and uses it as the interface layer for interacting with the RAG system.

This allows the RAG pipeline to be exposed through a simple user interface rather than requiring every question to be entered directly into notebook cells.

---

# Technologies Used

- Python
- Jupyter Notebook
- LangChain
- LangChain Chroma
- LangChain Hugging Face
- LangChain Ollama
- ChromaDB
- Hugging Face
- Sentence Transformers
- Ollama
- OpenRouter
- OpenAI-compatible API
- Gradio
- python-dotenv
- JSON

---

# Project Structure

    InsureLLM-RAG/
    │
    ├── knowledge-base/
    │   ├── company/
    │   ├── contracts/
    │   ├── employees/
    │   └── products/
    │
    ├── notebooks/
    │   ├── insurellm_advance.ipynb
    │   └── insurellm_langchain_visualization.ipynb
    │
    ├── README.md
    ├── requirements.txt
    └── .gitignore

The exact project structure can be adjusted depending on where the notebooks and knowledge-base directory are placed in the repository.

---

# Environment Variables

The project uses environment variables for API credentials.

Create a `.env` file in the project directory.

    OPENROUTER_API_KEY=your_openrouter_api_key
    HUGGING_FACE_API=your_huggingface_api_key

Do not commit API keys or other secrets to GitHub.

The `.env` file should be included in `.gitignore`.

---

# Installation

## 1. Clone the Repository

    git clone <your-repository-url>

    cd InsureLLM-RAG

## 2. Create a Virtual Environment

Using Python:

    python -m venv .venv

Activate it on Windows:

    .venv\Scripts\activate

## 3. Install Dependencies

    pip install -r requirements.txt

## 4. Configure Environment Variables

Create a `.env` file and add the required API credentials.

## 5. Start Ollama

If using the local Ollama components, make sure Ollama is installed and the required model is available.

For the notebook's local model:

    llama3.2

## 6. Run the Notebooks

Open the project in Jupyter Notebook or VS Code and run:

    notebooks/insurellm_langchain_visualization.ipynb

and:

    notebooks/insurellm_advance.ipynb

---

# RAG Architecture

The architecture can be summarized as:

    ┌──────────────────────┐
    │    Knowledge Base    │
    │ company / contracts  │
    │ employees / products │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │  Document Processing  │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │      Embeddings       │
    │  all-MiniLM-L6-v2     │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │       ChromaDB        │
    │    Vector Storage     │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │       Retriever       │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │ Query Rewriting /     │
    │ Query Expansion       │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │  Merge & Deduplicate  │
    │       Chunks          │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │    LLM Reranking      │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │    Context + Query    │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │         LLM          │
    └──────────┬───────────┘
               │
               ↓
    ┌──────────────────────┐
    │    Final Response     │
    └──────────────────────┘

---

# Key Concepts Demonstrated

This project demonstrates several important concepts used in modern RAG systems:

### Retrieval

Finding semantically relevant information from a vector database.

### Reranking

Reordering retrieved chunks according to their relevance to the question.

### Query Rewriting

Transforming a user's question into a more retrieval-friendly query.

### Query Expansion

Using additional query variations to retrieve more potentially relevant information.

### Deduplication

Removing duplicate chunks obtained from multiple retrieval operations.

### Context Grounding

Providing retrieved knowledge to the LLM so that the answer is based on the available knowledge base.

### Multi-Model Integration

Using different LLM services for different parts of the pipeline, including Ollama, Hugging Face, and OpenRouter.

---

# Example RAG Flow

A question such as:

    Who is the CEO?

can be processed as:

    User Question
          ↓
    Query Rewriting
          ↓
    "Who is the CEO of Insurellm?"
          ↓
    ChromaDB Retrieval
          ↓
    Relevant Document Chunks
          ↓
    Reranking
          ↓
    Context
          ↓
    LLM
          ↓
    Final Answer

The same pipeline can be used for questions concerning employees, products, company information, and contracts contained in the knowledge base.

---

# Why Reranking?

Vector similarity provides an initial ranking of retrieved chunks, but semantic similarity does not always guarantee that the first retrieved chunk is the most useful one for answering a particular question.

The project therefore uses an additional LLM-based reranking stage.

This provides:

    Initial Retrieval
          ↓
    Candidate Chunks
          ↓
    LLM Relevance Ranking
          ↓
    Better Ordered Context
          ↓
    Answer Generation

---

# Why Query Rewriting?

Users often ask short or conversational questions.

For example:

    "who is the ceo?"

The knowledge base may contain more specific terminology.

Query rewriting transforms the original question into a more explicit search query before retrieval.

This is especially useful when the question depends on conversational context or lacks important identifying information.

---

# Why Query Expansion?

A single query may not retrieve every relevant piece of information.

Query expansion allows the system to retrieve information through additional query formulations and then combine the results.

This can provide a larger candidate set before reranking.

---

# Security

Do not commit sensitive information to the repository.

The following should generally remain outside GitHub:

- API keys
- `.env` files
- Virtual environments
- Local credentials
- Generated vector databases
- Temporary files
- Local model files
- Cache files

Use `.gitignore` to prevent accidental commits.

---

# Learning Outcomes

This project demonstrates practical experience with:

- Retrieval-Augmented Generation
- Vector databases
- Semantic search
- Embeddings
- LangChain
- ChromaDB
- Hugging Face models
- Ollama
- OpenRouter
- Query rewriting
- Query expansion
- LLM-based reranking
- Context construction
- LLM answer generation
- Gradio-based interfaces
- Notebook-based experimentation

---

# Future Improvements

Possible improvements to the project include:

- Hybrid keyword + semantic retrieval
- Cross-encoder reranking
- Retrieval evaluation
- RAG evaluation metrics
- Better chunking strategies
- Embedding model comparison
- Retrieval parameter tuning
- Source-document citations in answers
- Streaming responses
- Conversational memory
- FastAPI deployment
- Docker deployment
- Production vector-database configuration
- Automated evaluation of retrieval quality
- Web-based frontend

---

# Disclaimer

The knowledge base contains synthetic Insurellm-related information used for experimentation and development of the RAG pipeline.

This project is intended as a technical demonstration of Retrieval-Augmented Generation and related LLM techniques.

---

# Author

**Saif Mohammed**

GitHub:

https://github.com/saif-mohammed9505


Built as a practical project for exploring advanced RAG, LangChain, vector databases, embeddings, LLMs, and retrieval optimization.

## License

This project is intended for educational and experimental purposes.
