# Knowledge Base

This directory contains the source documents used by the **InsureLLM RAG system**.

## Categories

- `company/` — Company-related documents
- `contracts/` — Contract-related documents
- `employees/` — Employee-related documents
- `products/` — Product-related documents

## Structure

knowledge-base/
├── company/
├── contracts/
├── employees/
└── products/

## Purpose

These documents serve as the knowledge source for the Retrieval-Augmented Generation (RAG) pipeline. The system loads the documents, splits them into smaller chunks, generates embeddings, stores them in a vector database, and retrieves relevant information when answering user queries.

## RAG Workflow

Knowledge Base → Document Loading → Text Splitting → Embeddings → Vector Database → Retrieval → LLM → Answer

The original knowledge-base documents are kept separately from the generated vector database so that the vector store can be recreated when required.
