# AI Medical Assistant: A RAG-Powered Q&A System for Healthcare Professionals

## Project Overview & Business Problem

In the high-stakes healthcare industry, professionals are often faced with an overwhelming volume of medical data. The need for quick, accurate, and reliable information for diagnoses and treatment planning is critical, especially in time-sensitive emergencies. Relying on generic search engines provides no guarantee of accuracy and can lead to information overload.

This project tackles this challenge by building a functional prototype of an AI Medical Assistant. The solution uses a **Retrieval-Augmented Generation (RAG)** pipeline to create a specialized Q&A system. This system grounds a powerful Large Language Model (LLM) in a trusted, specific knowledge base: **The Merck Manual**, a 4,000+ page comprehensive medical reference.

The objective is to provide healthcare professionals with a tool that can answer complex medical queries (e.g., "What is the protocol for managing sepsis?") with answers that are not just *plausible* but are *verifiably sourced* from this "gold standard" medical text.


## Key Skills & Technologies

This project demonstrates an end-to-end workflow in applied NLP and LLM implementation, highlighting the following skills:

* **Core Stack:** Python, LangChain
* **LLM:** Mistral-7B-Instruct-v0.2 (run locally via `llama-cpp-python`)
* **Embedding Model:** `thenlper/gte-large` (via Hugging Face)
* **Vector Database:** `ChromaDB`
* **Data Ingestion:** `PyMuPDF` for PDF extraction
* **Text Processing:** `RecursiveCharacterTextSplitter` (token-aware)
* **Prompt Engineering:** Iterative development of complex system prompts to guide model behavior, formatting, and role-playing.
* **Evaluation:** Advanced **"LLM-as-a-Judge"** methodology to score RAG outputs for "Groundedness" and "Relevance."

---

## The RAG Pipeline

This system was built from scratch, comparing a baseline LLM's performance to the final, augmented RAG pipeline.

### 1. Baseline: The Problem with Generic LLMs

The base Mistral-7B model was first tested on its own. When asked medical questions, its answers were generic, similar to a standard web search, and provided no verifiable source. It could not be trusted for clinical use.

### 2. Step 1: Data Ingestion and Chunking
The 4,114-page Merck Manual PDF was loaded and processed. It was split into **~9,000 text chunks** using a token-aware `RecursiveCharacterTextSplitter`, with a chunk overlap to ensure semantic context was not lost between chunks.

### 3. Step 2: Embedding and Vector Storage
Each text chunk was converted into a high-dimensional vector (embedding) using the `gte-large` model. These vectors were then loaded into a `ChromaDB` vector store. This database allows for rapid, semantic-based searching of the entire 4,000-page manual.

### 4. Step 3: Retrieval-Augmented Generation
When a user asks a question, the RAG pipeline executes the following:
1.  **Retrieve:** The user's query is embedded, and the `ChromaDB` retriever finds the top-k most semantically relevant text chunks from the Merck Manual.
2.  **Augment:** These raw text chunks are dynamically injected as "Context" into a sophisticated system prompt.
3.  **Generate:** The prompt (containing the context and the original question) is sent to the Mistral-7B LLM. The model is explicitly instructed to answer the user's question *using only the provided context*.

This process forces the LLM to act as a "reasoning engine" on top of a trusted data source, dramatically improving accuracy and eliminating "hallucinations."

---

## Evaluation: "LLM-as-a-Judge"

A key component of this project was not just building the pipeline but proving its effectiveness. An "LLM-as-a-Judge" pipeline was created to automatically evaluate the RAG system's outputs.

The same LLM was given a new role: an evaluator. It was asked to score the final answers based on two critical metrics:

1.  **Groundedness:** Is the answer *only* derived from the provided context (the Merck Manual)? This checks for hallucinations.
    * **Result: 5/5**
2.  **Relevance:** Does the answer directly and comprehensively address the user's question?
    * **Result: 4.5/5**

These high scores validate that the RAG pipeline is working as intended, providing reliable, sourced, and relevant answers to complex medical queries.

## Example Output (RAG-Powered)

**Query:** "What is the protocol for managing sepsis in a critical care unit?"

**Generated Response:**

**Case Title: Sepsis Management in Critical Care Unit**

**Treatment Plan:**
1.  Administer oxygen therapy as needed.
2.  Provide replacement-dose corticosteroids.
3.  Monitor systemic pressure, CVP or PAOP, pulse oximetry, ABGs, blood glucose, lactate, and electrolyte levels, renal function, and possibly sublingual PCO2 frequently.
4.  Measure urine output using an indwelling catheter.
5.  Initiate fluid resuscitation with 0.9% saline until CVP reaches 8 mm Hg (10 cm H2O) or PAOP reaches 12 to 15 mm Hg.
6.  If hypotension persists after target levels are reached, administer dopamine to increase mean BP to at least 60 mm Hg. If dopamine dose exceeds 20 μg/kg/min, add another vasopressor such as norepinephrine.
7.  Provide parenteral antibiotics after specimens have been taken for Gram stain and culture.
8.  Initiate empiric therapy immediately upon suspecting sepsis.
9.  Administer normalization of blood glucose levels using a continuous IV insulin infusion to maintain glucose between 80 to 110 mg/dL (4.4 to 6.1 mmol/L).
10. Drain abscesses and excise necrotic tissues as necessary.

**Follow-up Care:**
1.  Schedule frequent follow-ups.
2.  Monitor blood tests, including electrolytes, CBC, Mg, phosphate, Ca levels (for patients with arrhythmias), liver enzymes, and coagulation profiles (for patients receiving TPN).
3.  Adjust antibiotic regimen based on culture and sensitivity results.
4.  Continue antibiotics for at least 5 days after shock resolves and evidence of infection subsides.

## Conclusion

This project successfully demonstrates the creation of a RAG-based AI solution that solves a critical business problem in healthcare. It moves beyond generic AI to create a specialized, reliable tool that can parse thousands of pages of domain-specific knowledge to provide quick, actionable, and trustworthy answers for professionals in an emergency setting.
