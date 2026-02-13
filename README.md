# PREDII-Assignment

Vehicle Specification Extraction from Service Manuals
Overview

This project implements a Retrieval-Augmented Generation (RAG) pipeline to extract structured vehicle specifications (such as torque values, fluid capacities, and part numbers) from automotive service manual PDFs.

The system accepts a service manual as input, retrieves the most relevant sections based on a user query, and returns structured output in JSON format.

The focus is strictly on text-based extraction. Images and diagrams are intentionally ignored.

System Design

The solution follows a standard RAG architecture:

1. PDF Parsing

2. Text Chunking

3. Embedding Generation

4. Vector Similarity Search

5. LLM-Based Structured Extraction

This separation keeps the pipeline modular and easy to reason about.

Current Pipeline Logic
1. PDF Text Extraction

The system uses PyMuPDF to extract raw text from each page.

Page-level text is concatenated into a single string.

No OCR is applied.

2. Text Chunking

The extracted text is split using RecursiveCharacterTextSplitter.

Chunk size: 800 characters

Overlap: 100 characters

Each chunk is converted into a LangChain Document object.

3. Embeddings and Vector Store

Embeddings are generated using OpenAI’s text-embedding-3-small model.

Chunks are indexed into a FAISS vector store.

This enables efficient semantic retrieval instead of keyword matching.

The vector store is cached per uploaded document to avoid recomputation.

4. Retrieval

When a user enters a query (e.g., “Torque for brake caliper bolts”):

The system retrieves the top 5 most relevant chunks.

These chunks form the context for the LLM.

5. Structured Extraction with LLM

The model used is GPT-4o-mini.

Temperature is set to 0 to reduce variability.

Output is enforced through a Pydantic schema:

[
  {
    "component": "",
    "spec_type": "",
    "value": "",
    "unit": ""
  }
]

Tools and Libraries Used
Component	Tool
PDF Parsing	PyMuPDF
Chunking	LangChain RecursiveCharacterTextSplitter
Embeddings	OpenAI text-embedding-3-small
Vector Store	FAISS
LLM	GPT-4o-mini
Structured Output	Pydantic
UI	Gradio

Why This Approach

Semantic retrieval performs better than regex-based extraction for technical manuals.

FAISS allows fast similarity search even for large documents.

Structured output enforcement ensures predictable and machine-readable responses.

The pipeline remains modular and extensible.

Assumptions

The input PDF is machine-readable (not scanned).

Specifications appear in text form.

Queries are specific (e.g., torque, capacity, part number).

Limitations

No OCR support for scanned manuals.

No automatic extraction of all specifications (query-based only).

No page number metadata in output.

No evaluation metrics implemented.

Performance depends on document formatting consistency.

Possible Improvements

Add OCR support for scanned PDFs.

Include page numbers in structured output.

Implement hybrid retrieval (BM25 + embeddings).

Add automatic extraction mode (extract all specs without a query).

Persist FAISS index to disk.

Add CSV export functionality.

Add confidence scoring for extracted specifications.

Implement evaluation using a manually labeled test set.

Improve chunking by splitting based on section headers.

How to Run

Install dependencies:

-pip install pymupdf langchain langchain-community langchain-openai faiss-cpu gradio pydantic

-Set your OpenAI API key as an environment variable.

-Run the notebook.

-Upload a service manual PDF.

-Enter a query such as:

Torque for brake caliper bolts

-View structured JSON output.

Example Output
[
  {
    "component": "Brake Caliper Bolt",
    "spec_type": "Torque",
    "value": "35",
    "unit": "Nm"
  }
]

The current implementation is functional, extensible, and aligned with practical RAG system design principles.
