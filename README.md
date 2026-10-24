# IntelliVault — Intelligent PDF Question Answering with RAG

**An AI-powered document question-answering system that retrieves relevant information from uploaded PDFs and generates context-grounded answers.**

[Live Demo](https://intellivault-h6kzpmsmgvaomkqwdxm6o4.streamlit.app/) | [GitHub Repository](https://github.com/anagha-jayarajan/Intellivault)

## Overview

IntelliVault is a Retrieval-Augmented Generation (RAG) application that lets users interact with PDF documents using natural-language questions. The system retrieves relevant document passages and uses them as context for answering questions.

The project includes multiple RAG approaches so their retrieval behaviour and answer generation can be explored and compared.

## Problem Statement

Finding specific information in lengthy PDF documents can be time-consuming. General-purpose language models may also generate answers that are not supported by the document.

IntelliVault addresses this by combining document processing, retrieval, and language-model-based answer generation to make document information easier to access.

## Objectives

- Allow users to upload PDF documents and ask questions in natural language.
- Retrieve relevant passages from the uploaded document.
- Generate answers grounded in retrieved document content.
- Handle questions for which the document does not provide an answer.
- Explore baseline, hybrid, and agentic RAG approaches.
- Provide a simple web interface with user accounts and history.

## Key Features

- **PDF Question Answering:** Ask questions about uploaded PDF content.
- **Retrieval-Augmented Generation:** Use retrieved passages as context for answer generation.
- **Multiple RAG Modes:** Baseline RAG, Hybrid RAG, and Agentic AI.
- **Source Citations:** Answers cite numbered passages with source file and page.
- **Unsupported-Question Handling:** Returns a not-found response when supporting information is unavailable.
- **User Accounts:** Registration and login with Argon2 password hashing, backed by MongoDB Atlas.
- **History:** Per-user document and question-answer history stored in MongoDB.
- **Streamlit Interface:** Use the application through a web browser.
- **Evaluation Tooling:** Scripts for assessing RAG outputs.

## RAG Modes

| Mode | Description |
|---|---|
| Baseline RAG | Dense vector retrieval followed by a single LLM call. |
| Hybrid RAG | Combines dense (embedding) and keyword (BM25) retrieval to find relevant context. |
| Agentic AI | An agent plans the search, retrieves evidence, checks whether it is sufficient, re-queries if needed, and then answers. Ordinary factual questions use a fast path with fewer LLM calls. |

The approaches can be compared to study retrieval behaviour, response quality, and execution time.

## System Architecture

1. **User Login:** The user registers or logs in (credentials stored in MongoDB Atlas).
2. **PDF Upload:** The user uploads a PDF through the Streamlit interface.
3. **Text Extraction:** Text is extracted from the PDF with PyMuPDF.
4. **Document Indexing:** Text is split into chunks, embedded with `BAAI/bge-small-en-v1.5`, and stored in ChromaDB.
5. **Question Input:** The user enters a question and selects a RAG mode.
6. **Context Retrieval:** The selected pipeline retrieves relevant passages.
7. **Answer Generation:** The language model answers using only the retrieved context.
8. **Response Display:** The answer and its sources are shown, and the exchange is saved to the user's history.

## Technology Stack

- **Programming Language:** Python
- **Web Interface:** Streamlit
- **PDF Processing:** PyMuPDF
- **Embeddings:** Sentence-Transformers (`BAAI/bge-small-en-v1.5`)
- **Vector Store:** ChromaDB
- **Language Models:** Google Gemini (default), with Groq, Ollama, and Anthropic supported through configuration
- **RAG Workflows:** Baseline, hybrid, and agentic pipelines
- **Database:** MongoDB Atlas (users, document history, chat history)
- **Authentication:** Argon2 password hashing
- **Deployment:** Streamlit Community Cloud
- **Version Control:** Git and GitHub

## Project Structure

```text
Intellivault/
├── app.py              # Streamlit application and user interface
├── agent.py            # Agentic RAG workflow
├── baseline_rag.py     # Baseline retrieval and LLM calls
├── hybrid.py           # Hybrid retrieval workflow
├── ingest.py           # Document ingestion utilities
├── config.py           # Application configuration
├── database.py         # MongoDB integration (users and history)
├── evaluate.py         # Evaluation and benchmarking utilities
├── questions.json      # Evaluation questions
├── requirements.txt    # Python dependencies
├── packages.txt        # Additional system packages
└── README.md           # Project documentation
```

The `.env` file is for local configuration and must not be committed.

## Getting Started

### Prerequisites

- Python 3.11 or a version compatible with the project's dependencies
- Git
- A Google Gemini API key (or a key for another supported provider)
- A MongoDB Atlas cluster with a database user

### 1. Clone the Repository

```bash
git clone https://github.com/anagha-jayarajan/Intellivault.git
cd Intellivault
```

### 2. Create a Virtual Environment

For Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure MongoDB

In `database.py`, replace the cluster host in the connection string with your own Atlas cluster host (for example `cluster0.xxxxx.mongodb.net`). In Atlas, add your IP (or `0.0.0.0/0` for deployment) under **Network Access**.

### 5. Configure Environment Variables

Create a local `.env` file in the project root:

```dotenv
MONGODB_USERNAME=your_atlas_db_username
MONGODB_PASSWORD=your_atlas_db_password
LLM_PROVIDER=gemini
GEMINI_API_KEY=your_gemini_api_key
GEMINI_MODEL=gemini-3.8-flash
```

To use Groq instead:

```dotenv
LLM_PROVIDER=groq
GROQ_API_KEY=your_groq_api_key
GROQ_MODEL=llama-3.3-70b-versatile
```

**Security:** Never commit `.env`, API keys, database passwords, or other credentials to GitHub. On Streamlit Community Cloud, add them under **Manage app → Settings → Secrets** in TOML format (`NAME = "value"`).

### 6. Run the Application

```bash
streamlit run app.py
```

Open the local URL shown in the terminal.

## How to Use

1. Open IntelliVault and register an account (password of at least 12 characters), then log in.
2. Upload a PDF document.
3. Select an answer mode: Baseline RAG, Hybrid RAG, or Agentic AI.
4. Enter a question about the document.
5. Review the answer and the displayed source information.
6. Ask a question unrelated to the document to test unsupported-question handling.

## Evaluation

Useful evaluation dimensions for a RAG application include:

- **Retrieval Relevance:** Whether retrieved passages contain information relevant to the question.
- **Answer Correctness:** Whether the answer accurately reflects the document.
- **Groundedness:** Whether claims are supported by the retrieved context.
- **Citation Quality:** Whether source references support the answer.
- **Latency:** How long the application takes to return a response.
- **Unsupported-Question Handling:** Whether the system avoids answering when adequate evidence is unavailable.

Use the project's evaluation scripts (`evaluate.py`, `questions.json`) and actual test results to report measured performance. Do not report accuracy percentages or improvements unless they have been measured.

## Deployment

**Live Application:** https://intellivault-h6kzpmsmgvaomkqwdxm6o4.streamlit.app/

**GitHub Repository:** https://github.com/anagha-jayarajan/Intellivault

Deployed on Streamlit Community Cloud, with credentials supplied through Streamlit Secrets.

## Current Limitations

- Retrieval quality depends on PDF text extraction, chunking, embeddings, and retrieval settings.
- A language model may produce incorrect answers if the retrieved context is insufficient or misleading.
- The ChromaDB index is stored on the host's local disk. On Streamlit Community Cloud this storage is temporary, so indexed documents may be lost when the app restarts or goes to sleep and may need to be re-uploaded. User accounts and history are stored in MongoDB and persist.
- Questions about the whole document (such as word counts) cannot be answered reliably, because only a few retrieved passages are sent to the model.
- API availability, rate limits, and model response times may affect performance. The first request after a restart is slower while the embedding model loads.

## Future Enhancements

- Persistent document storage and a hosted vector database.
- Improved retrieval through reranking and retrieval evaluation.
- More comprehensive automated and human-reviewed answer evaluation.
- Better source citations and document-level traceability.
- Performance optimization for large PDFs and repeated questions.
- Expanded testing of privacy, security, and multi-user access.

## Conclusion

IntelliVault demonstrates how Retrieval-Augmented Generation can make PDF documents easier to query using natural language. By exploring baseline, hybrid, and agentic retrieval workflows, the project provides a foundation for studying context-grounded question answering, retrieval quality, and document interaction.

---

**Project:** IntelliVault  
**Category:** Artificial Intelligence · Natural Language Processing · Retrieval-Augmented Generation  
**Live Demo:** [Try IntelliVault](https://intellivault-h6kzpmsmgvaomkqwdxm6o4.streamlit.app/)  
**Source Code:** [GitHub](https://github.com/anagha-jayarajan/Intellivault)
