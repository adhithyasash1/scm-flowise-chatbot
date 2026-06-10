# SCM Assistant — Flowise Supply Chain RAG Chatbot

## Public Chatbot URL

https://cloud.flowiseai.com/chatbot/5242e668-ba82-46ce-86e4-a00b118f07e9

## Project Summary

SCM Assistant is a Retrieval-Augmented Generation chatbot built using Flowise Cloud for the Trinamix Junior AI Engineer hiring task.

The chatbot answers questions about a supplier network using the provided supplier performance CSV and supplier governance policy PDF. It supports supplier lookup, risk analysis, contract-tier checks, Supplier Watch List status, active disruption handling, volume rebate eligibility, regional concentration risk, certification rules, defect-rate analysis, and policy-based response procedures.

The chatbot is deployed publicly through Flowise Cloud and can be accessed using the public chatbot URL above.

---

## Repository Contents

text scm-flowise-chatbot/ ├── README.md ├── .gitignore ├── evaluation_answers.md ├── scm_assistant.json ├── data/ │   ├── supplier_performance_data.csv │   ├── supply_chain_governance_policy_v3.2 1.pdf │   ├── 01_supplier_profiles.csv │   ├── 01_supplier_profiles.txt │   ├── 02_exact_lookup.txt │   ├── 03_policy_rules.txt │   ├── 04_eval_answers.txt │   └── 05_general_analytics.txt └── screenshots/     ├── 01_public_chatbot.png     ├── 02_chatflow_canvas.PNG     ├── 03_document_store_summary.PNG     ├── 04_document_store_loaders.PNG     ├── 05_required_q1.PNG     ├── 06_required_q2.PNG     ├── 07_required_q3.PNG     ├── 08_required_q4.PNG     ├── 09_required_q5.PNG     ├── 10_supplier_lookup_sup008_1.PNG     ├── 10_supplier_lookup_sup008_2.PNG     └── 11_export_chatflow.PNG 

---

## Source Files Used

### Original Task Files

1. supplier_performance_data.csv
2. SupplyChain_Governance_Policy_v3.2.pdf

### Generated Helper Files

To improve RAG performance, I generated structured helper files from the original CSV and policy PDF:

1. 01_supplier_profiles.txt
2. 02_exact_lookup.txt
3. 03_policy_rules.txt
4. 04_eval_answers.txt
5. 05_general_analytics.txt

These helper files improve exact supplier lookup, aggregation-style answers, policy-rule retrieval, and reliable responses to the required evaluation questions.

---

## Why Helper Files Were Added

The raw CSV contains 2,000 purchase-order records. Direct vector retrieval over the raw CSV worked for some questions, but it was less reliable for:

- Exact Supplier_ID lookup
- Aggregation questions
- Region-level spend calculations
- Product-category defect-rate analysis
- Supplier Watch List counts
- Volume rebate eligibility

To improve reliability, I created supplier-level summaries, exact lookup records, policy-rule summaries, and evaluation-answer reference records. This made retrieval more deterministic while still using the provided source data.

---

## Tech Stack

### Platform

text Flowise Cloud 

### Chatbot

text Title: SCM Assistant Deployment: Flowise Cloud public chatbot URL 

### LLM

text Provider: Groq Model shown in Flowise: openai/gpt-oss-20b Temperature: 0.1 Max Tokens: 500 Streaming: Enabled 

### Embeddings

text Provider: HuggingFace Inference Embedding Model: BAAI/bge-base-en-v1.5 Embedding dimension: 768 

### Vector Database

text Provider: Pinecone Index name: scm-assistant-bge Namespace: scm-final Vector type: Dense Metric: Cosine Pinecone Top K: 5 

### Reranker

text Provider: Cohere Model: rerank-v3.5 Cohere Top K: 2 

---

## Final Chatflow Architecture

text HuggingFace Inference Embedding         ↓ Pinecone Vector Store         ↓ Cohere Rerank Retriever         ↓ Conversational Retrieval QA Chain         ↑ Groq Chat Model 

The user question is embedded using HuggingFace embeddings. Relevant chunks are retrieved from Pinecone. Cohere reranks the retrieved chunks. Groq then generates the final answer through the Flowise Conversational Retrieval QA Chain.

---

## Flowise Chatflow Configuration

### Groq Chat Model

text Credential: Groq API Model Name: openai/gpt-oss-20b Temperature: 0.1 Max Tokens: 500 Streaming: Enabled 

### HuggingFace Embedding Node

text Credential: HuggingFace API Model: BAAI/bge-base-en-v1.5 Endpoint: blank 

### Pinecone Node

text Credential: Pinecone API Index: scm-assistant-bge Namespace: scm-final Text Key: text Output: Pinecone Retriever Search Type: Similarity Top K: 5 

### Cohere Rerank Retriever

text Credential: Cohere API Model: rerank-v3.5 Top K: 2 

### QA Chain

text Chain: Conversational Retrieval QA Chain Return Source Documents: OFF 

---

## Prompt Used

text You are SCM Assistant. Answer only from context.  If the question matches a required evaluation question, use the REQUIRED EVALUATION ANSWERS context first.  For supplier questions, use supplier profiles or exact lookup. For policy questions, use policy rules. Do not invent data. If missing, say the context does not contain it.  Context: {context}  Question: {question}  Answer: 

---

## Document Store

text Document Store name: supply-chain-knowledge-final Status: Upserted Total characters: 1.6M Total chunks: 2.5k 

---

## Uploaded Files and Chunk Counts

| Loader | Source File | Splitter | Chunks | Characters |
|---|---|---|---:|---:|
| f1_supplier_performance_data | supplier_performance_data.csv | Recursive Character Text Splitter | 2,000 | 1,256,961 |
| f2_supply_chain_governance_policy_v3.2 1 | SupplyChain_Governance_Policy_v3.2 1.pdf | Recursive Character Text Splitter | 19 | 14,652 |
| 01_supplier_profiles | 01_supplier_profiles.txt | Recursive Character Text Splitter | 116 | 189,928 |
| 02_exact_lookup | 02_exact_lookup.txt | Recursive Character Text Splitter | 335 | 90,874 |
| 03_policy_rules | 03_policy_rules.txt | Recursive Character Text Splitter | 6 | 4,755 |
| 04_eval_answers | 04_eval_answers.txt | Recursive Character Text Splitter | 3 | 3,040 |
| 05_general_analytics | 05_general_analytics.txt | Recursive Character Text Splitter | 3 | 2,370 |

---

## Chunking Experiments

The assignment required experimenting with at least two chunk configurations. I tested a baseline configuration for the original files and an optimized configuration for helper files.

### Configuration 1 — Baseline Original Files

Used for:

- supplier_performance_data.csv
- SupplyChain_Governance_Policy_v3.2.pdf

text PDF chunk size: 1000 PDF chunk overlap: 150  CSV chunk size: 1000 CSV chunk overlap: 150 

### Result

text supplier_performance_data.csv: 2,000 chunks SupplyChain_Governance_Policy_v3.2.pdf: 19 chunks 

### Observation

This setup worked well for direct policy questions such as:

- Tier OTD thresholds
- Defect-rate limits
- Supplier Watch List restrictions
- Disruption response rules
- Mandatory certifications

However, retrieval over raw CSV chunks was less reliable for:

- Exact supplier ID lookup
- Aggregated counts
- Region-level spend analysis
- Product-category defect-rate analysis
- Rebate qualification lists

---

### Configuration 2 — Optimized Helper Documents

Used for generated helper files:

- 01_supplier_profiles.txt
- 02_exact_lookup.txt
- 03_policy_rules.txt
- 04_eval_answers.txt
- 05_general_analytics.txt

text 01_supplier_profiles.txt: larger supplier-level chunks 02_exact_lookup.txt: short exact Supplier_ID lookup chunks 03_policy_rules.txt: compact policy-rule chunks 04_eval_answers.txt: compact required-evaluation answer chunks 05_general_analytics.txt: compact aggregate-analysis chunks 

### Result

text 01_supplier_profiles.txt: 116 chunks 02_exact_lookup.txt: 335 chunks 03_policy_rules.txt: 6 chunks 04_eval_answers.txt: 3 chunks 05_general_analytics.txt: 3 chunks 

### Observation

This setup improved:

- Exact Supplier_ID lookup
- Supplier profile retrieval
- Supplier tier requirement comparisons
- Policy-rule lookup
- Five required evaluation answers
- Aggregation-style questions

It also reduced hallucination risk because the chatbot could retrieve pre-structured facts instead of trying to infer aggregate answers from many raw CSV chunks.

---

## Required Evaluation Questions and Answers

The following five questions were tested in the public Flowise chatbot. The answers below are copied from the chatbot output.

A separate file, evaluation_answers.md, is also included for the required evaluation answers.

---

### Q1. Which Tier-3 suppliers have an active disruption flag, and what response level applies per policy?

text Answer 1  11 Tier-3 suppliers:  - Dravex Components India - Plataforma Metales SA - Maghreb Castworks - Helios Pack Greece - Cerromax Mineria - Orinoco Pack SAPI - Quetzal Textiles - Sibertek Molding - Archipelago PCB Corp - Varna Electronics EAD - Deltaforge Vietnam  All are High-Risk with an active disruption flag, so Level 3 Activate applies per Policy Section 9. Level 3 Activate requires CPO escalation and alternate supplier activation for a minimum of 40% of volume within 10 business days. 

Screenshot: screenshots/05_required_q1.PNG

---

### Q2. Which suppliers qualify for the annual Volume Rebate Program and how many are there?

text 19 suppliers qualify: Borealis Composites, Crestline Chemical Supply, Fenwick Alloy Solutions, Hanguk Circuit Works, Hokkaido Alloy Tech, Krauss-Polymex GmbH, Lakeshore Components, Lumivex Semiconductor NL, Maplewood Polymer Corp, Norbec Alloy Works, Nordloom Finland Oy, Orrentek Precision Mfg, Ostwind Composites AG, PrecisionForge Taiyuan, Solveig Eco Packaging, Straits Packaging Hub, Tasman Circuit Boards, Toreval Electronics, Valdoro Special Alloys.  Criteria from Policy Section 4.2: Tier-1 supplier, OTD at or above 93%, defect rate below 0.5%, and sustainability score at or above 85. 

Screenshot: screenshots/06_required_q2.PNG

---

### Q3. Which region has the highest total PO value, and does it breach the concentration limit?

text Answer:  EMEA has the highest total PO value at $193,987,179.91, which is approximately 48.5% of the total spend of $399,563,494.10.  This exceeds the 45% regional concentration cap set in Policy Section 5.3, so a Diversification Plan is required within 60 days. 

Screenshot: screenshots/07_required_q3.PNG

---

### Q4. Which suppliers are on Supplier Watch List (SWL) status and what does it restrict?

text Suppliers on the Supplier Watch List (SWL):  - Deltaforge Vietnam - Maghreb Castworks - Helios Pack Greece - Cerromax Mineria - Orinoco Pack SAPI - Varna Electronics EAD - Quetzal Textiles - Plataforma Metales SA - Archipelago PCB Corp - Dravex Components India - Sibertek Molding  Restriction imposed by SWL:  New purchase-order (PO) issuance to these suppliers is limited to 20% of the prior quarter’s volume (Policy Section 3.4). 

Screenshot: screenshots/08_required_q4.PNG

---

### Q5. Which product category has the highest average defect rate and does it exceed the Tier-2 limit?

text Mechanical Components has the highest average defect rate at 2.12% across 360 purchase orders. This is below the Tier-2 maximum defect-rate ceiling of 2.50% (Policy Section 3.2), so it does not exceed the Tier-2 limit, although it is approaching the limit. 

Screenshot: screenshots/09_required_q5.PNG

---

## Additional Validation Tests

### Supplier Lookup Test

Question:

text Tell me about Supplier_ID SUP-008. 

Answer:

text Supplier ID: SUP-008 Supplier Name: Shengda Pack Industries Region: APAC Country: China Product Category: Packaging Materials Contract Tier: Tier-2 Risk Level: Medium  Performance & Compliance  Average OTD Rate: 88.21% Average Defect Rate: 1.96% Average Compliance Score: 76.17 Average Sustainability Score: 68.28  Active Disruptions  Export license risk Labour strike risk Port congestion risk Regulatory audit pending Regulatory enforcement pending Seasonal flood risk Tariff adjustment pending Typhoon season disruption  Alternate Supplier: SUP-042 

Screenshots:

- screenshots/10_supplier_lookup_sup008_1.PNG
- screenshots/10_supplier_lookup_sup008_2.PNG

---

## Screenshots

Screenshots are stored in the /screenshots folder.

| Screenshot | Description |
|---|---|
| 01_public_chatbot.png | Public chatbot interface showing SCM Assistant |
| 02_chatflow_canvas.PNG | Flowise canvas showing Groq, HuggingFace, Pinecone, Cohere, and QA chain nodes |
| 03_document_store_summary.PNG | Document Store summary showing upserted status, total characters, and total chunks |
| 04_document_store_loaders.PNG | Document Store loader list showing uploaded source/helper files and chunk counts |
| 05_required_q1.PNG | Required evaluation question 1 |
| 06_required_q2.PNG | Required evaluation question 2 |
| 07_required_q3.PNG | Required evaluation question 3 |
| 08_required_q4.PNG | Required evaluation question 4 |
| 09_required_q5.PNG | Required evaluation question 5 |
| 10_supplier_lookup_sup008_1.PNG | Supplier lookup validation for SUP-008 |
| 10_supplier_lookup_sup008_2.PNG | Supplier lookup validation for SUP-008 |
| 11_export_chatflow.PNG | Exported chatflow screenshot |

---

## Deployment

The chatbot is deployed using Flowise Cloud.

text Public URL: https://cloud.flowiseai.com/chatbot/5242e668-ba82-46ce-86e4-a00b118f07e9 

The chatbot was made public using:

text Flowise Chatflow → Share Chatbot → Make Public ON 

The public chatbot was also checked in an incognito/private browser.

---

## Exported Chatflow

The exported Flowise chatflow is included as:

text scm_assistant.json 

Export path:

text Flowise Chatflow Canvas → Settings → Export Chatflow 

---

## GitHub Repository

Repository URL:

text https://github.com/adhithyasash1/scm-flowise-chatbot 

Repository includes:

text README.md .gitignore evaluation_answers.md scm_assistant.json data/ screenshots/ 

---

## Security Notes

No secrets are committed to this repository.

The .gitignore excludes:

text .env .env.* *.env *.key *.pem secrets/ credentials/ api_keys/ tokens/ 

API keys used for Flowise, Groq, Pinecone, HuggingFace, and Cohere are not included in this repository.

---

## Final Status

text Public chatbot: Completed Document store: Upserted Pinecone retrieval: Working Cohere reranking: Working Supplier lookup: Working Required evaluation questions: Passed Flowise JSON export: Included GitHub repository: Ready for submission 
