
# AI-Drug-Approval-Engine

**An AI-powered agentic copilot designed to automate the pre-screening and compliance auditing of clinical trial applications and Serious Adverse Event (SAE) reports for the Central Drugs Standard Control Organisation (CDSCO).**

This system turns days of manual regulatory review into a **30-second automated scan**—flagging missing compliance columns, redacting sensitive patient data, checking report similarity, and providing a local Hierarchical RAG chatbot to query clinical findings privately.

---

## Core Capabilities

- **Hybrid Text Extraction (OCR):** Extracts digital text from native PDFs via PyMuPDF and automatically falls back to a local Tesseract-OCR engine for high-resolution text extraction on scanned or image-only pages.
- **PII Anonymization (Privacy Compliance):** Automatically redacts patient names, phone numbers, email addresses, and medical centers using title-case sequence heuristics and regular expressions before downstream processing.
- **Hierarchical Abstractive Summarization:** Employs a recursive chunk-and-summarize pipeline powered by `facebook/bart-large-cnn` to condense lengthy clinical dossiers (200+ pages) into structured compliance summaries.
- **Zero-Shot Event Classification:** Categorizes regulatory document types and predicts SAE severity levels (`death`, `hospitalization`, `life-threatening`, etc.) using a zero-shot `facebook/bart-large-mnli` model.
- **Regulatory Field Checklists:** Audits extracted text to verify the presence of mandatory CDSCO regulatory fields (Patient Details, Suspect Drug Name, Adverse Event Description, and Investigator/Doctor Signatures).
- **Document Lineage & Revisions:** Compares updated submissions against prior versions using cosine similarity metrics and generates sentence-by-sentence addition, deletion, and modification diffs.
- **Local Hierarchical RAG Chatbot:** A private, air-gapped Q&A assistant powered by `Llama-3-8B-Instruct` (via Ollama) mapped onto a hierarchical semantic search index built with `all-MiniLM-L6-v2`.

---

## Tech Stack & AI Models

| Layer | Technologies & Models |
| :--- | :--- |
| **Frontend** | Vanilla HTML5, Glassmorphic CSS3 (Obsidian Dark Theme), JavaScript (ES6) |
| **Backend** | FastAPI, Uvicorn, Python 3.9+ |
| **PDF & OCR** | PyMuPDF (`fitz`), ReportLab, `pytesseract` (with local Tesseract binaries) |
| **NLP & ML Models** | PyTorch, Hugging Face Transformers (`bart-large-cnn`, `bart-large-mnli`), SentenceTransformers (`all-MiniLM-L6-v2`) |
| **Local LLM** | Ollama (`llama3`) |

---

## Repository Structure

```text
AI-Drug-Approval-Engine/
├── api/
│   └── main.py              # FastAPI server, audit endpoints, and Hierarchical RAG router
├── preprocessing/
│   ├── loader.py            # Hybrid PDF text loader & Tesseract-OCR wrapper
│   ├── cleaner.py           # Text normalization, error-correction & sanitization
│   └── pipeline.py          # End-to-end document preprocessing orchestrator
├── modules/
│   ├── anonymization/       # PII redaction engine (Regex + Title-case heuristics)
│   ├── classification/      # Zero-shot document & SAE severity classification
│   ├── completeness_check/  # Mandatory CDSCO field auditor & severity scoring
│   ├── duplicate_detection/ # Cosine similarity matching for duplicate submissions
│   ├── comparison/          # Sentence-level revision & lineage diffing
│   └── reporting/           # Automated ReportLab compliance audit PDF generator
├── Tesseract-OCR/           # Local OCR engine binaries (Windows)
├── samples/                 # Sample clinical trial dossiers & SAE reports for testing
├── Dockerfile               # Container configuration for Hugging Face Spaces deployment
├── requirements.txt         # Python package dependencies
└── PROJECT_GUIDE.md         # Detailed developer & system architecture guide
