# RAG Planning Policy System

A retrieval-augmented generation (RAG) system for querying UK national planning policy documents. Given a planning question, the system retrieves relevant policy chunks and generates a cited answer grounded in the source documents.

---

## Problem

Planning policy in the UK is spread across long, dense documents -- the National Planning Policy Framework (NPPF, ~70 pages) and the London Plan (~500 pages). Local authorities, developers, and citizens struggle to locate relevant policy quickly. This system makes policy accessible through natural-language queries.

---

## Approach

1. **Document ingestion** -- PDF parsing with `pypdf`, chunking with LangChain's `RecursiveCharacterTextSplitter`, also split by paragraph number of the NPPF document
2. **Embeddings** -- `all-MiniLM-L6-v2` via `sentence-transformers` (CPU-fast, no API cost)
3. **Vector store** -- ChromaDB (local, persistent)
4. **Re-ranking** -- cross-encoder re-ranking for improved retrieval precision
5. **Generation** -- Ollama (local LLM) with cited outputs; Claude API for quality comparison
6. **UI** -- Gradio interface, deployed on Hugging Face Spaces

---

## Results

*To be completed as the project progresses.*

- Retrieval recall@5: TBD
- Answer faithfulness (manual eval, 20 test questions): TBD
- CPU inference latency per query: TBD

---

## Documents Covered

- [National Planning Policy Framework (NPPF)](https://www.gov.uk/government/publications/national-planning-policy-framework--2) -- publicly available on GOV.UK
- London Plan 2021 -- publicly available from the GLA (added in Week 3)

---

## Tech Stack

| Component | Tool |
|-----------|-----|
| PDF parsing | `pypdf` |
| Chunking | `langchain-text-splitters` |
| Embeddings | `sentence-transformers` (`all-MiniLM-L6-v2`) |
| Vector store | `ChromaDB` |
| LLM (local) | Ollama (llama3.2) |
| Orchestration | LangChain + LangGraph |
| Frontend | Gradio |
| Deployment | Hugging Face Spaces |

---

## Setup

**Prerequisites:** Python 3.11, conda, [Ollama](https://ollama.com) installed locally

```bash
# 1. Clone the repo
git clone https://github.com/pjay42-stow/rag-planning-policy
cd rag-planning-policy

# 2. Create environment and install dependencies
conda create -n rag-env python=3.11 -y
conda activate rag-env
pip install -r requirements.txt

# 3. Download the NPPF PDF
#    Download from: https://www.gov.uk/government/publications/national-planning-policy-framework--2
#    Save as: data/nppf.pdf

# 4. Pull the LLM via Ollama
ollama pull llama3.2
```

---

## Notebooks

| Notebook | Description |
|---------|-----------|
| `01_chunking.ipynb` | PDF loading, chunking strategy experiments |
| More to be added... | |

---

## Project Status

This project is actively under development as part of a structured ML portfolio (May -- October 2026).

| Phase | Status |
|------|-------|
| Environment setup & PDF chunking | ✓ Complete |
| Embeddings & ChromaDB ingestion | In progress |
| LLM generation step | Pending |
| Re-ranking | Pending |
| Gradio UI + HF Spaces deployment | Pending |
| Evaluation (20 Q/A test set) | Pending |

---

## Author

Peter Jay -- [GitHub](https://github.com/pjay42-stow)

Former UK Civil Servant (DBT, 11 years) transitioning into AI/ML with a focus on public sector applications. MPhys Physics, University of Bath.

