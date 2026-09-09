##Project Aegis — Advanced Enterprise RAG System

Project Aegis is a context-aware Retrieval-Augmented Generation (RAG) prototype designed to navigate complex corporate policy documents where relevance, document structure, metadata, and versioning matter.

The project focuses on improving retrieval quality rather than relying on a naive fixed-size text split. It combines structure-aware ingestion, metadata-based filtering, query expansion, cross-encoder reranking, basic query guardrails, and a grounded response pipeline.

Problem

Corporate policy documents often contain interconnected sections, tables, numerical rules, and multiple policy versions. A simple vector search can retrieve semantically similar but operationally incorrect content.

Project Aegis was designed to reduce that failure mode by preserving document structure and narrowing retrieval before final answer assembly.

Solution Architecture

Corporate Policy Documents
          |
          v
Markdown Header Parsing
          |
          v
Table Detection + Token Chunking
          |
          v
Metadata Extraction / Tagging
          |
          v
BGE-large Embeddings
          |
          v
Qdrant Vector Store
          |
          v
Query Expansion
          |
          v
Category Pre-filtering
          |
          v
Deduplication + Latest-Version Filtering
          |
          v
Cross-Encoder Reranking
          |
          v
Top Retrieved Context
          |
          v
Grounded Response Assembly

Key Capabilities

1. Structure-aware ingestion

Parses Markdown headers to retain section context.

Detects Markdown-style tables and keeps table blocks intact.

Uses token-based chunking with a 300-token target and 50-token overlap.

Attaches metadata to each chunk, including document ID, policy category, effective date, and header context.

2. Dense retrieval

Embeddings: BAAI/bge-large-en-v1.5

Vector database: Qdrant

Similarity search is combined with metadata filtering when a policy category can be inferred from the query.

3. Retrieval refinement

Multi-query expansion generates several query formulations before retrieval.

Duplicate chunks are removed before reranking.

Retrieved content is post-filtered using document metadata and effective dates.

BAAI/bge-reranker-large is used as a cross-encoder reranker to select the most relevant retrieved chunks.

4. Guardrails and grounding

Basic prompt-injection-style phrases such as ignore previous, bypass, and override are blocked.

The response pipeline is designed to return retrieved policy context rather than invent unsupported policy content.

A simple groundedness score is included in the Streamlit interface.

Demo

A Streamlit interface was created for interactive policy queries. During execution, the application was successfully exposed through an ngrok public tunnel for demonstration.

The ngrok URL is intentionally not included here because tunnel URLs are temporary and tied to a specific execution session.

Technology Stack

Python

Qdrant

Sentence Transformers

Cross-Encoder Reranker

BAAI/bge-large-en-v1.5

BAAI/bge-reranker-large

tiktoken

Streamlit

pyngrok

Google Colab

My Contribution

I designed and implemented the core retrieval approach, including document parsing, chunking, metadata tagging, vector retrieval, query expansion, filtering, deduplication, reranking, guardrails, groundedness checking, and the Streamlit demonstration layer.

The project was developed as an applied AI prototype, with emphasis on solving a retrieval problem through system design and iterative engineering rather than treating the task as a basic chatbot.

Repository Contents

Project_Aegis/
├── Project_Aegis.ipynb
└── README.md

Running the Notebook

The notebook was developed in Google Colab.

Open Project_Aegis(1).ipynb in Google Colab.

Install the dependencies from the first section.

Upload the policy text files when prompted.

Run the notebook from top to bottom.

The final section starts the Streamlit application and can expose it through ngrok.

Important Implementation Notes

This repository is intentionally transparent about the current prototype scope:

The current implementation uses template-based multi-query expansion. It does not implement an LLM-generated HyDE step.

The final answer assembly is based on retrieved policy context rather than a separate generative LLM call.

Qdrant is initialized in memory for the notebook prototype, so the vector store is not persistent across sessions.

The project does not claim production deployment or measured production performance.

Security

Requires an ngrok authentication token/API key. Authentication tokens should be supplied locally through environment variables or secret management.

Project Goal

Project Aegis demonstrates how retrieval quality can be improved by combining document structure, metadata, semantic search, filtering, and reranking into a single RAG workflow for enterprise policy use cases.
