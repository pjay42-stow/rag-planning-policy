# RAG Planning Policy System

A retrieval-augmented generation (RAG) system for querying UK national planning policy documents. Given a planning question, the system retrieves relevant policy chunks and generates a cited answer grounded in the source documents.

---

## Problem

Planning policy in the UK is spread across long, dense documents -- the National Planning Policy Framework (NPPF, ~70 pages) and the London Plan (~500 pages). Local authorities, developers, and citizens struggle to locate relevant policy quickly. This system makes policy accessible through natural-language queries.

---

## Approach

1. **Document ingestion** -- PDF parsing with `pypdf`; hybrid chunking strategy splitting on NPPF paragraph boundaries with `RecursiveCharacterTextSplitter` fallback for long paragraphs; footnote stripping to improve chunk quality
2. **Embeddings** -- `all-MiniLM-L6-v2` via `sentence-transformers` (CPU-fast, no API cost); 356 chunks ingested into ChromaDB with paragraph-number metadata
3. **Vector store** -- ChromaDB (local, persistent), cosine similarity
4. **Retrieval** -- top-k semantic search; cross-encoder re-ranking planned for next phase
5. **Generation** -- Ollama (local LLM, `llama3.2:3b`) with cited outputs; Claude API (`claude-sonnet-4-5`) used for quality comparison
6. **UI** -- Gradio interface, deployed on Hugging Face Spaces (planned)

---

## Results

Initial evaluation on 10 test questions across NPPF policy areas (Sessions 1--3):

- **Retrieval grounding**: 10/10 answers grounded in retrieved chunks
- **Citation accuracy**: 10/10 answers cited correct paragraph numbers
- **Answer completeness**: 7/10 fully complete; 2/10 partial (retrieval gaps); 1/10 incomplete
- **Model comparison**: Claude Sonnet 4.5 and llama3.2:3b performed comparably on well-retrieved questions; Claude handled retrieval failures more safely (correctly flagging missing information rather than confabulating)
- **Key finding**: Answer quality is primarily constrained by retrieval quality, not generation quality -- cross-encoder re-ranking is the highest-value next improvement

Full evaluation table and failure analysis: `02_embeddings_clustering.ipynb`

---

## Documents Covered

- [National Planning Policy Framework (NPPF)](https://www.gov.uk/government/publications/national-planning-policy-framework--2) -- publicly available on GOV.UK
- London Plan 2021 -- publicly available from the GLA (planned for Session 4)

---

## Tech Stack

| Component | Tool |
|-----------|------|
| PDF parsing | `pypdf` |
| Chunking | `langchain-text-splitters` (hybrid paragraph + recursive strategy) |
| Embeddings | `sentence-transformers` (`all-MiniLM-L6-v2`) |
| Vector store | `ChromaDB` (persistent, cosine similarity) |
| LLM (local) | Ollama (`llama3.2:3b`) |
| LLM (API) | Anthropic Claude (`claude-sonnet-4-5`) |
| Orchestration | LangChain |
| Frontend | Gradio (planned) |
| Deployment | Hugging Face Spaces (planned) |

---

## Setup

**Prerequisites:** Python 3.11, conda, [Ollama](https://ollama.com) installed locally

**Note for Windows users:** install PyTorch from the CPU wheel index before installing other dependencies, to avoid DLL initialisation failures.

```bash
# 1. Clone the repo
git clone https://github.com/pjay42-stow/rag-planning-policy
cd rag-planning-policy

# 2. Create environment
conda create -n rag-env python=3.11 -y
conda activate rag-env

# 3. Install PyTorch first (CPU build -- Windows requires this order)
pip install torch --index-url https://download.pytorch.org/whl/cpu

# 4. Install remaining dependencies
pip install -r requirements.txt

# 5. Download the NPPF PDF
#    From: https://www.gov.uk/government/publications/national-planning-policy-framework--2
#    Save as: data/nppf.pdf

# 6. Pull the LLM via Ollama
ollama pull llama3.2
```

---

## Notebooks

| Notebook | Description |
|----------|-------------|
| `01_chunking.ipynb` | PDF loading, chunking strategy comparison (fixed-size vs recursive vs sentence vs paragraph vs hybrid) |
| `02_embeddings_clustering.ipynb` | Embedding pipeline, ChromaDB ingestion, K-Means clustering, t-SNE visualisation, evaluation of 10 test questions |
| `03_llm_generation.ipynb` | Retrieval pipeline, Ollama generation, Claude API comparison, model evaluation |

---

## Project Status

| Phase | Status |
|-------|--------|
| Environment setup & PDF chunking | ✓ Complete |
| Embeddings & ChromaDB ingestion | ✓ Complete |
| K-Means clustering & t-SNE visualisation | ✓ Complete |
| LLM generation step (Ollama) | ✓ Complete |
| Model comparison (Claude API vs Ollama) | ✓ Complete |
| Initial evaluation (10 Q/A, manual scoring) | ✓ Complete |
| London Plan ingestion | Pending |
| Cross-encoder re-ranking | Pending |
| Full evaluation (20 Q/A test set) | Pending |
| Gradio UI + HF Spaces deployment | Pending |

---

## Author

Peter Jay -- [GitHub](https://github.com/pjay42-stow)

Former UK Civil Servant (DBT, 11 years) transitioning into AI/ML with a focus on public sector applications. MPhys Physics, University of Bath.