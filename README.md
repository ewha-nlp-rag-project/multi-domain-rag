# Multi-Domain RAG System

This project aims to build a **Retrieval-Augmented Generation (RAG) system** that provides highly accurate answers to questions regarding Ewha Womans University regulations and various academic domains (MMLU-Pro).

<br/>

## 🚀 Key Features

- **Semantic Table Parsing**: Maximized retrieval precision by converting complex table structures in the university regulations—which are difficult to capture using standard text extraction—into structured Markdown using the Upstage Document Parse API.
- **Hybrid Retrieval**: Improved retrieval success rates by combining semantic-based **Dense Vector Retrieval (FAISS)** and keyword-based **Sparse Retrieval (BM25)**.
  - **Weight:** FAISS 0.6 : BM25 0.4 (Experimental optimal value)
  - **Embedding:** `solar-embedding-1-large` (Bilingual support)
- **2-Stage Pipeline**: Designed to first classify the category of the question (Stage 1), and then generate the final answer using a prompt and knowledge base optimized for that specific domain (Stage 2).
- **Domain-Specific Prompting**: Optimized reasoning performance by providing the LLM with personas and One-shot examples tailored to the specific characteristics of each domain (Law, Psychology, Business, etc.).

<br/>

## 🏗 System Architecture

1. **Stage 1 - Category Classification**:
   - **1st Pass (Language-based):** Classifies the question as either Ewha University Regulations (Korean) or MMLU-Pro (English) based on the language of it.
   - **2nd Pass (LLM-based):** For MMLU-Pro questions, the `solar-pro2` model is used to assign two labels (Primary and Secondary) out of 5 academic categories (Business, History, Law, Philosophy, Psychology). The **Primary label** is utilized for domain retrieval in Stage 2.
2. **Stage 2 - Hybrid RAG**:
   - **Retriever Selection:** Selects either the Ewha or Academic hybrid retriever based on the classification results to search for the relevant context.
   - **Answer Generation:** Combines the retrieved context with domain-specific prompts and feeds them into `solar-pro2` to deduce the final answer.

<br/>

## 🚀 Quick Start

**Note**: This project requires an **Upstage API Key** for LLM reasoning and embedding.

### 1. Environment Setup

```bash
git clone https://github.com/jsy200406/NLP-Project.git
cd NLP-Project
pip install -r requirements.txt
```

### 2. API Key Configuration

Create a `.env` file in the root directory and add your Upstage API key:

```python
UPSTAGE_API_KEY=your_api_key_here
```

### 3. Set the Path

Set the `data_path` variable in the source files to point to your working directory:

```python
data_path="YOUR_PATH"
```

### 4. Run the Pipeline

Run `src/embedding-code.ipynb` to build the knowledge base for Retrieval.

(Note: You need to collect raw academic PDF files on your own. We can’t provide the raw files we used due to copyright issues.)

Run `src/run.ipynb` to start the 2-Stage RAG system and generate answers for the test set.

<br/>

## 📂 Project Structure

```
NLP-project
├── data
│   ├── raw
│   │   ├── ewha/                   # Ewha Univ Regulation PDFs
│   │   ├── business/               # Business-related PDFs
│   │   ├── history/                # History-related PDFs
│   │   ├── law/                    # Law-related PDFs
│   │   ├── philosophy/             # Philosophy-related PDFs
│   │   └── psychology/             # Psychology-related PDFs
│   └── testset.csv                 # Dataset for performance evaluation
├── docs
│   └── Presentation.pdf            # Project presentation materials
├── embeddings
│   ├── academic_bm25_retriever.pkl # BM25 index for Academic domain
│   ├── ewha_bm25_retriever.pkl     # BM25 index for Regulation domain
│   ├── academic-vectorstore/       # FAISS vector store for Academic domain
│   └── ewha-vectorstore/           # FAISS vector store for Regulation domain
├── results
│   └── 7_final.csv                 # Final execution results and answers
├── src
│   ├── embedding-code.ipynb        # Data parsing and embedding generation process
│   └── run.ipynb                   # Full RAG pipeline execution
└── requirements.txt                # List of dependency packages
```

<br/>

## 📊 Performance

- **Testset Accuracy: 88.00%**
  - **Korean Regulation (Ewha)**: **100.00%**
  - **English (MMLU-Pro)**: **76.00%**
- **Sampled MMLU-Pro Accuracy**: **63.00%**
- **Category Classification Accuracy**: **91.00%**

<br/>

## 🛠 Tech Stack

- **Language**: Python
- **LLM**: Upstage Solar-Pro2
- **Embedding**: Upstage solar-embedding-1-large
- **Framework**: LangChain, FAISS, BM25
- **Parsing**: Upstage Document Parse API

<br/>

## 👥 Contributors

- **Eunna Lee:** Law/History data embedding, Prompt engineering, Hyperparameter optimization
- **Jungyeon Lee:** Wikipedia/Philosophy data embedding, Regulation data parsing, One-shot example construction
- **Seonyeong Jeong:** Business/Psychology data embedding, Category classification LLM implementation, Full code integration
