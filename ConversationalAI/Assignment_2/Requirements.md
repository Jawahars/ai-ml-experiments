# Assignment 2 – Comparative Financial QA System: RAG vs Fine-Tuning

**Status:** Completed  
This repository contains the full solution for **Assignment 2** from the Distributed Machine Learning course.

---

## Objective
Develop and compare two systems for answering questions based on company financial statements (last two years):

- **Retrieval-Augmented Generation (RAG) Chatbot**: Combines document retrieval and generative response.  
- **Fine-Tuned Language Model (FT) Chatbot**: Directly fine-tunes a small open-source language model on financial Q&A.

Both systems use the **same financial data** for comparison on accuracy, speed, and robustness.

---

## Step-by-Step Tasks

### 1. Data Collection & Preprocessing
- Obtain financial statements for the last two years.
- Convert documents (PDF, Excel, HTML) to plain text using OCR or parsers.
- Clean text by removing noise such as headers, footers, and page numbers.
- Segment reports into logical sections (e.g., income statement, balance sheet).
- Construct at least **50 Q/A pairs** reflecting the financial data.

**Example:**

| Question | Answer |
|----------|--------|
| What was the company’s revenue in 2023? | The company’s revenue in 2023 was $4.13 billion. |

---

### 2. Retrieval-Augmented Generation (RAG) System Implementation

#### 2.1 Data Processing
- Split the cleaned text into chunks (e.g., 100 and 400 tokens).
- Assign unique IDs and metadata to chunks.

#### 2.2 Embedding & Indexing
- Embed chunks using small open-source models like `all-MiniLM-L6-v2` or `E5-small-v2`.
- Build:
  - **Dense vector store** (e.g., FAISS, ChromaDB)
  - **Sparse index** (BM25, TF-IDF)

#### 2.3 Hybrid Retrieval Pipeline
- Preprocess queries (clean, lowercase, stopword removal).
- Generate query embeddings.
- Retrieve top-N chunks:
  - Dense retrieval (vector similarity)
  - Sparse retrieval (BM25)
- Combine results via union or weighted score fusion.

#### 2.4 Advanced RAG Technique
Choose one based on **group number mod 5**:

| Group Remainder | Technique | Description |
|-----------------|-----------|-------------|
| **1** | Multi-Stage Retrieval | Stage 1: Broad retrieval; Stage 2: Re-rank with a cross-encoder. |
| **2** | Chunk Merging & Adaptive Retrieval | Dynamically merge adjacent chunks or adapt chunk size based on query. |
| **3** | Re-Ranking with Cross-Encoders | Use a cross-encoder to re-rank retrieved chunks by relevance. |
| **4** | Hybrid Search (Sparse + Dense) | Combine BM25 with dense retrieval for balanced precision and recall. |
| **0** | Memory-Augmented Retrieval | Maintain a persistent memory bank of common or critical Q&A pairs. |

#### 2.5 Response Generation
- Use a generative model like DistilGPT-2, GPT-2 Small, or Llama-2 7B.
- Concatenate query and retrieved passages as input.
- Respect token limits of the chosen model.

#### 2.6 Guardrail Implementation
Implement at least one:
- **Input-side**: Filter irrelevant or harmful queries.
- **Output-side**: Flag or filter hallucinated or non-factual answers.

#### 2.7 Interface Development
- Build a UI using Streamlit, Gradio, CLI, or GUI.
- Features:
  - Accept user query
  - Display answer, retrieval confidence score, method used, and response time
  - Allow switching between **RAG** and **Fine-Tuned** modes

---

### 3. Fine-Tuned Model (FT) System Implementation

#### 3.1 Q/A Dataset Preparation
- Use the same ~50 Q/A pairs from the RAG system.
- Convert to fine-tuning compatible format.

#### 3.2 Model Selection
- Choose a small open-source language model:
  - DistilBERT
  - MiniLM
  - GPT-2 Small/Medium
  - Llama-2 7B
  - Falcon 7B
  - Mistral 7B
- Avoid proprietary APIs.

#### 3.3 Baseline Benchmarking
- Evaluate the base model on at least 10 test questions.
- Record:
  - Accuracy
  - Confidence
  - Inference speed

#### 3.4 Fine-Tuning
- Fine-tune on the Q/A dataset.
- Log:
  - Learning rate
  - Batch size
  - Number of epochs
  - Compute setup (CPU/GPU)

#### 3.5 Advanced Fine-Tuning Technique
Choose one based on **group number mod 5**:

| Group Remainder | Technique | Description |
|-----------------|-----------|-------------|
| **1** | Supervised Instruction Fine-Tuning | Train on instruction-style Q/A pairs. |
| **2** | Adapter-Based Parameter-Efficient Tuning | Train adapter modules to reduce cost. |
| **3** | Mixture-of-Experts Fine-Tuning | Use multi-expert architecture for efficient tuning and inference. |
| **4** | Retrieval-Augmented Fine-Tuning | Combine retrieval with fine-tuning for better knowledge grounding. |
| **0** | Continual Learning / Domain Adaptation | Incrementally fine-tune while preserving previous knowledge. |

#### 3.6 Guardrail Implementation
- Apply input-side or output-side guardrails similar to RAG.

#### 3.7 Interface Development
- Integrate FT model into the same UI.
- Show:
  - Answer
  - Confidence score
  - Method name
  - Inference time
- Toggle between RAG and FT modes.

---

### 4. Testing, Evaluation & Comparison

#### 4.1 Test Questions (Mandatory)
Include three official queries:
- Relevant, high-confidence
- Relevant, low-confidence
- Irrelevant (e.g., "What is the capital of France?")

#### 4.2 Extended Evaluation
- Test with at least **10 financial questions**.
- For each:
  - Ground-truth answer
  - Model-generated answer
  - Confidence score
  - Response time
  - Correctness (Y/N)

#### 4.3 Sample Results Table

| Question | Method | Answer | Confidence | Time (s) | Correct (Y/N) |
|-----------|--------|--------|------------|----------|---------------|
| Revenue in 2023? | RAG | $4.02B | 0.92 | 0.50 | Y |
| Revenue in 2023? | Fine-Tune | $4.13B | 0.93 | 0.41 | Y |
| Unique products? | RAG | 13,000 units | 0.81 | 0.79 | Y |
| Unique products? | Fine-Tune | 13,240 units | 0.89 | 0.65 | Y |
| Capital of France? | RAG | Data not in scope | 0.35 | 0.46 | Y |
| Capital of France? | Fine-Tune | Not applicable | 0.85 | 0.38 | Y |

#### 4.4 Analysis
- Compare:
  - Average inference speed
  - Accuracy
  - Robustness to irrelevant queries
- Discuss:
  - Strengths of RAG (adaptability, factual grounding)
  - Strengths of FT (fluency, efficiency)
  - Practical trade-offs

---

## Repository Structure
