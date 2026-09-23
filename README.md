# AI-Powered-Customer-Care-RAG
# Ecommerce Customer Support: RAG Evaluation Harness with CI Quality Gates

A production-oriented **Retrieval-Augmented Generation (RAG) evaluation harness** for an e-commerce customer-support system.

The project evaluates whether an AI support system can:

* Retrieve the correct information from multiple data sources
* Generate answers grounded in the retrieved context
* Maintain factual faithfulness
* Provide correct answers to customer questions
* Detect quality problems before production deployment through evaluation gates

---

## 1. Business Problem

Customer-support agents often need to search across multiple information sources before answering a customer.

For example, a single customer question may require information from:

* Knowledge-base articles
* Previous customer-support tickets
* Product information
* Return and warranty policies
* Shipping and payment information

Manually searching multiple sources can increase **Average Handle Time (AHT)** and can also lead to incomplete or inaccurate responses.

A typical support workflow can therefore look like:

```text
Customer Question
       ↓
Search Knowledge Base
       ↓
Search Previous Tickets
       ↓
Check Product Database
       ↓
Read Multiple Results
       ↓
Prepare Answer
       ↓
Respond to Customer
```

The goal of this project is to evaluate whether a RAG-based system can make this process more reliable.

---

# 2. What Does This Project Solve?

This project builds and evaluates a RAG pipeline that retrieves information from multiple sources and provides the retrieved context to an LLM.

The system combines:

1. **Chroma** — Knowledge-base articles
2. **Weaviate** — Historical support tickets
3. **SQLite** — Structured product information
4. **Gemini Embeddings** — Semantic representation of queries/documents
5. **Groq-hosted LLM** — Answer generation
6. **RAGAS** — LLM-based evaluation
7. **Deterministic retrieval metrics** — Retrieval quality measurement
8. **Quality gates** — Minimum quality requirements

The main objective is not simply to build a chatbot.

The main objective is to answer:

> **Can this RAG system retrieve the right information and generate answers that are sufficiently faithful and correct for production use?**

---

# 3. Actual Aim of the Project

The project focuses on **RAG evaluation and quality control**.

The pipeline measures two major areas:

### Retrieval Quality

Does the system retrieve the relevant information?

### Generation Quality

Does the LLM generate an answer that correctly uses the retrieved information?

This distinction is important because:

```text
Correct Retrieval
       ↓
Does NOT automatically mean
       ↓
Correct Answer
```

A system may retrieve the correct document but still generate an incomplete, misleading, or unsupported answer.

Therefore, this project evaluates both stages.

---

# 4. Project Architecture

```text
                         CUSTOMER QUESTION
                                │
                                ▼
                     ┌─────────────────────┐
                     │  Query Processing   │
                     └──────────┬──────────┘
                                │
                                ▼
                    Gemini Embedding Model
                     gemini-embedding-001
                                │
                ┌───────────────┼───────────────┐
                │               │               │
                ▼               ▼               ▼
          ┌──────────┐   ┌────────────┐   ┌──────────┐
          │  Chroma  │   │ Weaviate   │   │ SQLite   │
          │    KB    │   │  Tickets   │   │ Products │
          └────┬─────┘   └─────┬──────┘   └────┬─────┘
               │               │               │
               └───────────────┼───────────────┘
                               ▼
                    Retrieval + Reranking
                               │
                               ▼
                     Relevant Context
                               │
                               ▼
                  Groq-hosted LLM
                  openai/gpt-oss-20b
                               │
                               ▼
                      Generated Answer
                               │
                ┌──────────────┴──────────────┐
                │                             │
                ▼                             ▼
       Retrieval Metrics                 RAGAS Evaluation
                │                             │
                │                 ┌───────────┼───────────┐
                │                 │           │           │
                │                 ▼           ▼           ▼
                │           Faithfulness  Context     Answer
                │                         Recall    Correctness
                │
                └──────────────┬──────────────┘
                               ▼
                        Quality Gate
                               │
                       ┌───────┴───────┐
                       ▼               ▼
                     PASS             FAIL
```

---

# 5. Data Sources

The evaluation system uses three different types of data sources.

## 5.1 Chroma — Knowledge Base

Chroma stores customer-support knowledge-base articles.

The dataset contains **12 knowledge-base documents** covering topics such as:

* Returns
* Shipping
* Warranty
* Account deletion
* Promotional codes
* Cash on Delivery
* Exchanges
* Payments
* International shipping
* Size guide
* Gift cards
* Subscriptions

Example document IDs:

```text
kb1
kb2
kb3
...
kb12
```

---

## 5.2 Weaviate — Historical Support Tickets

Weaviate stores historical customer-support tickets.

The dataset contains **8 support-ticket records**.

Example IDs:

```text
t1
t2
t3
...
t8
```

These tickets provide additional context from previous customer-support interactions.

---

## 5.3 SQLite — Product Information

SQLite is used for structured product information.

The product table contains fields such as:

```text
SKU
Product Name
Warranty
Returnability
COD Availability
```

Example products include:

```text
HD100
LP200
MS300
PH400
WS500
JK600
BT700
CT800
```

SQLite is useful when the system needs deterministic structured information such as product-level warranty or return details.

---

# 6. Technologies Used

| Technology           | Purpose                                    |
| -------------------- | ------------------------------------------ |
| Python               | Main programming language                  |
| ChromaDB             | Knowledge-base vector retrieval            |
| Weaviate             | Historical support-ticket vector retrieval |
| SQLite               | Structured product information             |
| Gemini               | Text embeddings                            |
| Groq                 | LLM inference                              |
| `openai/gpt-oss-20b` | Answer generation                          |
| LangChain            | RAG pipeline and prompt management         |
| RAGAS                | RAG quality evaluation                     |
| Pandas               | Data processing and analysis               |
| SQLAlchemy           | SQLite database interaction                |
| Scikit-learn         | Similarity/reranking utilities             |
| Tenacity             | Retry handling                             |
| Jupyter Notebook     | Development and execution environment      |

---

# 7. AI Models Used

## Embedding Model

```text
gemini-embedding-001
```

The embedding model converts questions and documents into numerical vector representations.

Example:

```text
Customer Question
       ↓
Gemini Embedding
       ↓
Vector Representation
       ↓
Semantic Search
```

This allows the system to find documents that are semantically related to the customer's question.

---

## Generation Model

```text
openai/gpt-oss-20b
```

The model is accessed through **Groq** and is responsible for generating the final customer-support answer.

The LLM receives the retrieved context and is instructed to:

* Use the supplied context
* Read all relevant information
* Combine information when necessary
* Include important conditions and exclusions
* Avoid unsupported facts
* Stay close to the source meaning
* Provide a concise response
* Escalate when the available context is insufficient

---

# 8. RAG Pipeline

The complete workflow is:

### Step 1 — Customer Question

Example:

```text
Can I return my headphones? I got them 20 days ago.
```

---

### Step 2 — Create Query Embedding

The question is converted into a vector using:

```text
gemini-embedding-001
```

---

### Step 3 — Retrieve From Chroma

The system searches the knowledge-base collection for semantically relevant documents.

Example:

```text
kb1
```

---

### Step 4 — Retrieve From Weaviate

The system searches historical support tickets.

Example:

```text
t1
```

---

### Step 5 — Retrieve Structured Product Information

If the question contains a product/SKU that requires structured information, SQLite is queried.

Example:

```text
HD100
```

---

### Step 6 — Filter and Rerank Context

Retrieved results are evaluated using cosine similarity.

Low-relevance contexts can be removed before generation.

The remaining contexts are passed to the generation stage.

---

### Step 7 — Generate Answer

The relevant context is supplied to the LLM.

```text
Question
   +
Retrieved Context
   ↓
LLM
   ↓
Grounded Answer
```

---

### Step 8 — Evaluate the Answer

The generated answer is evaluated using RAGAS metrics.

---

### Step 9 — Apply Quality Gates

The system checks the mandatory thresholds:

```text
Faithfulness       >= 0.85
Context Recall     >= 0.75
Answer Correctness >= 0.75
```

If a test case does not satisfy all mandatory thresholds, that case fails the quality gate.

---

# 9. Evaluation Methodology

The project uses two types of evaluation.

## A. Deterministic Retrieval Evaluation

The retrieval layer is evaluated using:

### Precision@K

Measures how many of the retrieved documents are relevant.

```text
Precision@K =
Relevant Retrieved Documents
----------------------------
Total Retrieved Documents
```

---

### Recall@K

Measures how many of the expected relevant documents were retrieved.

```text
Recall@K =
Relevant Retrieved Documents
----------------------------
Total Relevant Documents
```

---

### MRR — Mean Reciprocal Rank

Measures how highly the first relevant result appears.

A relevant document appearing earlier gives a higher score.

---

### NDCG@K

Measures the ranking quality of retrieved results while considering the position of relevant documents.

---

# 10. RAGAS Evaluation

The project also uses RAGAS for generation and context evaluation.

The evaluated metrics are:

### Faithfulness

Checks whether the generated answer is supported by the retrieved context.

### Answer Relevancy

Checks whether the answer directly addresses the user's question.

### Context Precision

Measures how much of the retrieved context is relevant to answering the question.

### Context Recall

Measures whether the necessary information was retrieved.

### Answer Correctness

Measures how closely the generated answer matches the expected correct answer.

---

# 11. Quality Gates

The project defines the following mandatory thresholds:

| Metric             | Required Score |
| ------------------ | -------------: |
| Faithfulness       |     **≥ 0.85** |
| Context Recall     |     **≥ 0.75** |
| Answer Correctness |     **≥ 0.75** |

A test case passes only when all mandatory metrics satisfy their thresholds.

```text
Faithfulness       >= 0.85
        AND
Context Recall     >= 0.75
        AND
Answer Correctness >= 0.75
        ↓
      PASS
```

Otherwise:

```text
      FAIL
```

---

# 12. Production Evaluation Results


```text
Test Cases       : 4
RAGAS Workers    : 1
RAGAS Timeout    : 600 seconds
Retrieval K      : 3
Chroma K         : 1
Weaviate K       : 1

LLM              : openai/gpt-oss-20b
Embeddings       : gemini-embedding-001

Faithfulness     : >= 0.85
Context Recall   : >= 0.75
Answer Correctness: >= 0.75
```

---

| Metric      |      Score |
| ----------- | ---------: |
| Precision@K | **1.0000** |
| Recall@K    | **1.0000** |
| MRR         | **1.0000** |
| NDCG@K      | **0.5000** |

The retrieval evaluation successfully identified the expected relevant information for the tested cases.

### Retrieved Sources

| Test Case             | Chroma | Weaviate | SQLite  |
| --------------------- | ------ | -------- | ------- |
| Headphone return      | `kb1`  | `t1`     | `HD100` |
| Cracked laptop screen | `kb3`  | `t3`     | `LP200` |
| Account deletion      | `kb4`  | —        | —       |
| Promo codes           | `kb5`  | —        | —       |

---

# 14. RAGAS Results

| RAGAS Metric       |      Score | 
| ------------------ | ---------: | 
| Faithfulness       | **0.8250** | 
| Answer Relevancy   | **0.9468** | 
| Context Precision  | **1.0000** | 
| Context Recall     | **1.0000** |   
| Answer Correctness | **0.8423** |  

---

# 15. CI Quality Gate Result

```text
CI Gate: FAIL
```

The evaluation produced:

```text
pass_gate = 0.25
```

This means:

```text
1 / 4 test cases passed all mandatory quality gates
```

Therefore:

```text
Test-case pass rate = 25%
```

`pass_gate` should not be interpreted as an overall model-quality score.

---

# 16. Failed Evaluation Cases

### Case 1 — Headphone Return

```text
Faithfulness       = 0.5000
Context Recall     = 1.0000
Answer Correctness = 0.8071
```

Result:

```text
FAIL
---

Then run the notebook cells sequentially.

The notebook performs:

# 25. Evaluation Output

The system produces a production evaluation summary similar to:

```text
Production Evaluation Summary

precision_at_k       1.000000
recall_at_k          1.000000
mrr                  1.000000
ndcg_at_k            0.500000
faithfulness         0.825000
answer_relevancy     0.946826
context_precision    1.000000
context_recall       1.000000
answer_correctness  0.842347
pass_gate            0.250000

CI Gate: FAIL
```

---
##  Conclusion

This project demonstrates how to build a RAG-based AI system that retrieves relevant information and uses an LLM to generate grounded answers.
