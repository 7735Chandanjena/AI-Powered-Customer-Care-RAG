# AI-Powered-Customer-Care-RAG
AI-powered customer care service using RAG, Gemini, LangChain, and vector database retrieval with RAGAS evaluation.
##  Project Overview

This project demonstrates a **Retrieval-Augmented Generation (RAG)** system that combines document retrieval with a Large Language Model (LLM).

The main purpose of this project is to build a system that can:

- Retrieve relevant information from a knowledge base
- Use the retrieved information as context
- Generate an answer using the Gemini API
- Evaluate the quality of retrieval and generated answers
- Perform evaluation using RAGAS
- Run multiple evaluation test cases
- Apply a production-style evaluation gate

---

##  Project Aim

The aim of this project is to understand and implement a practical **RAG pipeline**.

Instead of asking an AI model to answer only from its general knowledge, we first retrieve relevant information from our knowledge base and then give that information to the LLM.

## **Project Architecture**
                    ┌─────────────────┐
                    │   User Query    │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │    Retriever    │
                    └────────┬────────┘
                             ↓
              ┌──────────────┼──────────────┐
              ↓              ↓              ↓
         ChromaDB        Weaviate        SQL/KB
              └──────────────┼──────────────┘
                             ↓
                    ┌─────────────────┐
                    │ Retrieved Docs  │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │   Gemini LLM    │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ Generated Answer│
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │     RAGAS       │
                    │    Evaluation   │
                    └─────────────────┘


The project retrieves relevant information from multiple knowledge sources.

### The project uses retrieval from:

Chroma
Weaviate
SQL / Knowledge Base

The retrieved information is combined and used as context for answer generation.

### Gemini LLM

The project uses Google's Gemini API as the Large Language Model.

Gemini receives:

1.The user's question
2.Relevant retrieved information

It then generates an answer using the available context.

### RAGAS Evaluation

The project uses RAGAS to evaluate the RAG pipeline.

RAGAS helps measure the quality of the generated answers and the retrieved context.

The project prepares an evaluation dataset and runs the RAGAS evaluation on multiple test questions.

## Complete Project Workflow
1. User asks a question
          ↓
2. System searches the knowledge base
          ↓
3. Relevant documents are retrieved
          ↓
4. Retrieved documents are combined
          ↓
5. Context is sent to Gemini
          ↓
6. Gemini generates an answer
          ↓
7. Evaluation dataset is prepared
          ↓
8. RAGAS evaluates the results
          ↓
9. Evaluation results are collected
          ↓
10. Production evaluation gate is checked
          ↓
11. Evaluation run information is stored

## Expected Result

When the project runs successfully, the notebook should show retrieval results for the test questions.

For example:

Question: Can I return headphones after 20 days?

Retrieved Documents:
- kb1
- kb7
- kb3
- t1
- HD100

The exact document IDs can vary depending on the retrieval process.

The system should then generate an answer using the retrieved information.

Example Generated Answer
Yes, headphones can be returned within 30 days,
so a return after 20 days is allowed.

## Technologies Used

| Technology            | Purpose                        |
| --------------------- | ------------------------------ |
| Python                | Main programming language      |
| Gemini API            | Large Language Model           |
| RAG                   | Retrieval-Augmented Generation |
| Chroma                | Vector retrieval               |
| Weaviate              | Vector/database retrieval      |
| SQL                   | Knowledge/data retrieval       |
| RAGAS                 | RAG evaluation                 |
| Pandas                | Data processing                |
| Hugging Face Datasets | Evaluation dataset preparation |
| SQLite                | Evaluation-run storage         |

## How to Run the Project
### Step 1 — Clone the Repository
git clone <YOUR_GITHUB_REPOSITORY_URL>
### Step 2 — Open the Notebook

Open:

RAG_AI_Project_Gemini.ipynb

You can use:

Google Colab
Jupyter Notebook
JupyterLab
### Step 3 — Install Dependencies

If the project contains requirements.txt, run:

pip install -r requirements.txt

You can also run the installation cells provided inside the notebook.

Note: This repository does not contain a personal Gemini API key.

       The person running the project should provide their own Gemini API key.
       RAGAS evaluation can make multiple calls to the Gemini API.

       Therefore, the evaluation may require sufficient Gemini API quota.


##  Conclusion

This project demonstrates how to build a RAG-based AI system that retrieves relevant information and uses an LLM to generate grounded answers.
