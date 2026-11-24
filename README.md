# NovaTech Agentic RAG Assistant

An Agentic RAG system that answers complex, multi-step questions about company documents and data tables. The agent plans before answering, chooses its own tools, refines its searches, and returns answers with sources.

**Author:** Shrouq Abdelaziz Ali Ali

## What is Agentic RAG?

Standard RAG retrieves once and answers. Agentic RAG lets a language model plan the steps, decide when and how to retrieve, call tools (search, data analysis, calculation, date math), and combine the results before writing the final answer.

## Features

- Planning step before answering
- Hybrid retrieval: dense embeddings (Chroma) + BM25, merged with Reciprocal Rank Fusion
- Cross-encoder reranker that also rejects weak results
- Four tools plus date math: document search, summarization, pandas data analysis, calculator, date calculation
- Query rewriting when a search finds nothing
- Answers written only from collected evidence, with numbered source citations
- Clear "I could not find this information" reply for questions outside the data
- Gradio web app showing the answer, sources, and the agent process

## How it works

1. **Ingestion:** read PDF, DOCX, TXT and CSV files, clean the text, split by section, and add the document name and section title to every chunk.
2. **Storage:** embed chunks with `BAAI/bge-large-en-v1.5` and store them in ChromaDB. A BM25 index is built beside it.
3. **Retrieval:** hybrid search, then rerank with `BAAI/bge-reranker-large`.
4. **Agent:** `Qwen/Qwen2.5-7B-Instruct` (4-bit) writes a plan, then loops through Thought, Action, Observation.
5. **Answer:** a separate step writes the final answer from the evidence only and cites it.

## Dataset

The `data/` folder holds 11 synthetic files for a fictional company, NovaTech Solutions (made-up data, not real company records).

| Type | Files |
|---|---|
| Documents | Employee_handbook.docx, HR_policy.pdf, IT_security_policy.txt, Product_guide.pdf, Support_FAQ.txt, Customer_contract.pdf, Incident_report.txt |
| Tables | Pricing.csv, Offices_directory.csv, Sales_data.csv, Employees.csv |

## Project structure

```
.
├── data/              # the 11 dataset files
├── notebook/          # Agentic_RAG.ipynb
├── screenshots/       # screenshots of the app
├── requirements.txt
└── README.md
```

## Run on Kaggle

1. Upload the files in `data/` as a Kaggle Dataset.
2. Create a notebook, add the dataset, and import `notebook/Agentic_RAG.ipynb`.
3. In the notebook settings, turn on **Internet** and choose **GPU T4 x2**.
4. Run the cells in order. The last cell launches the Gradio app.

## Example questions

- Which customers are entitled to a service credit for the October incident, and how much is each?
- What is the yearly cost of 10 agents on the Business plan with annual billing?
- Which employees are entitled to 30 days of annual leave, and how many days does each have left?
- By what date must Delta Logistics cancel to avoid renewal?
- What is the CEO's name? (not in the data, the assistant should say it cannot find it)

## Limitations

- The 7B model can still make mistakes on long multi-step questions, so check the Agent process panel when an answer looks wrong.
- The search settings (for example `min_rerank_score`) were tuned on this dataset only.
- The data is English only.

## Tech stack

Python, Transformers, Sentence-Transformers, ChromaDB, rank-bm25, pandas, pdfplumber, python-docx, Gradio.
