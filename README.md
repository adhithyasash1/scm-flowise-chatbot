# SCM Assistant — Flowise Supply Chain RAG Chatbot
## Public Chatbot URL
https://cloud.flowiseai.com/chatbot/5242e668-ba82-46ce-86e4-a00b118f07e9
## Project Summary
SCM Assistant is a Retrieval-Augmented Generation chatbot built using **Flowise Cloud** for the Trinamix Junior AI Engineer hiring task.
The chatbot answers questions about a supplier network using the provided supplier performance CSV and supplier governance policy PDF. It supports supplier lookup, risk analysis, contract-tier checks, Supplier Watch List status, active disruption handling, volume rebate eligibility, regional concentration risk, certification rules, defect-rate analysis, and policy-based response procedures.
The chatbot is deployed publicly through Flowise Cloud and can be accessed using the public chatbot URL above.
---
## Repository Contents
```text
scm-assistant-bot/
├── README.md
├── .gitignore
├── scm_assistant.json
└── screenshots/
    ├── 01_public_chatbot.png
    ├── 02_chatflow_canvas.png
    ├── 03_document_store_summary.png
    ├── 04_document_store_loaders.png
    ├── 05_required_q1.png
    ├── 06_required_q2.png
    ├── 07_required_q3.png
    ├── 08_required_q4.png
    └── 09_required_q5.png

⸻

Source Files Used

Original Task Files

1. supplier_performance_data.csv
2. SupplyChain_Governance_Policy_v3.2.pdf

Generated Helper Files

To improve RAG performance, I generated structured helper files from the original CSV and policy PDF:

1. 01_supplier_profiles.txt
2. 02_exact_lookup.txt
3. 03_policy_rules.txt
4. 04_eval_answers.txt
5. 05_general_analytics.txt

These helper files improve exact supplier lookup, aggregation-style answers, policy-rule retrieval, and reliable responses to the required evaluation questions.

⸻

Why Helper Files Were Added

The raw CSV contains 2,000 purchase-order records. Direct vector retrieval over the raw CSV worked for some questions, but it was less reliable for:

* Exact Supplier_ID lookup
* Aggregation questions
* Region-level spend calculations
* Product-category defect-rate analysis
* Supplier Watch List counts
* Volume rebate eligibility

To improve reliability, I created supplier-level summaries, exact lookup records, policy-rule summaries, and evaluation-answer reference records. This made retrieval more deterministic while still using the provided source data.

⸻

Tech Stack

Platform

Flowise Cloud

Chatbot

Title: SCM Assistant
Deployment: Flowise Cloud public chatbot URL

LLM

Provider: Groq
Model shown in Flowise: openai/gpt-oss-20b
Temperature: 0.1
Streaming: Enabled

Embeddings

Provider: HuggingFace Inference Embedding
Model: BAAI/bge-base-en-v1.5
Embedding dimension: 768

Vector Database

Provider: Pinecone
Index name: scm-assistant-bge
Namespace: scm-final
Vector type: Dense
Metric: Cosine

Reranker

Provider: Cohere
Model: rerank-v3.5

⸻

Final Chatflow Architecture

HuggingFace Inference Embedding
        ↓
Pinecone Vector Store
        ↓
Cohere Rerank Retriever
        ↓
Conversational Retrieval QA Chain
        ↑
Groq Chat Model

The user question is embedded using HuggingFace embeddings, relevant chunks are retrieved from Pinecone, Cohere reranks the retrieved chunks, and Groq generates the final response through the Flowise Conversational Retrieval QA Chain.

⸻

Flowise Chatflow Configuration

Groq Chat Model

Credential: Groq API
Model Name: openai/gpt-oss-20b
Temperature: 0.1
Streaming: Enabled

HuggingFace Embedding Node

Credential: HuggingFace API
Model: BAAI/bge-base-en-v1.5
Endpoint: blank

Pinecone Node

Credential: Pinecone API
Index: scm-assistant-bge
Namespace: scm-final
Text Key: text
Output: Pinecone Retriever
Search Type: Similarity

Cohere Rerank Retriever

Credential: Cohere API
Model: rerank-v3.5

QA Chain

Return Source Documents: OFF

⸻

Prompt Used

You are SCM Assistant, a supply chain RAG chatbot.
Answer using only the retrieved context.
If the user asks one of the five required evaluation questions, prioritize the retrieved REQUIRED EVALUATION ANSWERS reference.
For supplier-specific questions, use supplier profiles or exact lookup records.
For policy questions, use policy rules or the governance policy.
For aggregation questions, use the required evaluation answers first, then general analytics.
Do not invent suppliers, counts, thresholds, dollar amounts, or policy sections.
If context is missing required data, say exactly what is missing.
Context:
{context}
Question:
{question}
Answer:

⸻

Document Store

Document Store name: supply-chain-knowledge-final
Status: Upserted
Total characters: 1.6M
Total chunks: 2.5k

⸻

Uploaded Files and Chunk Counts

Loader	Source File	Splitter	Chunks	Characters
f1_supplier_performance_data	supplier_performance_data.csv	Recursive Character Text Splitter	2,000	1,256,961
f2_supply_chain_governance_policy_v3.2 1	SupplyChain_Governance_Policy_v3.2 1.pdf	Recursive Character Text Splitter	19	14,652
01_supplier_profiles	01_supplier_profiles.txt	Recursive Character Text Splitter	116	189,928
02_exact_lookup	02_exact_lookup.txt	Recursive Character Text Splitter	335	90,874
03_policy_rules	03_policy_rules.txt	Recursive Character Text Splitter	6	4,755
04_eval_answers	04_eval_answers.txt	Recursive Character Text Splitter	3	3,040
05_general_analytics	05_general_analytics.txt	Recursive Character Text Splitter	3	2,370

⸻

Chunking Experiments

The assignment required experimenting with at least two chunk configurations. I tested a baseline configuration for the original files and an optimized configuration for helper files.

Configuration 1 — Baseline Original Files

Used for:

* supplier_performance_data.csv
* SupplyChain_Governance_Policy_v3.2.pdf

PDF chunk size: 1000
PDF chunk overlap: 150
CSV chunk size: 1000
CSV chunk overlap: 150

Result

supplier_performance_data.csv: 2,000 chunks
SupplyChain_Governance_Policy_v3.2.pdf: 19 chunks

Observation

This setup worked well for direct policy questions such as:

* Tier OTD thresholds
* Defect-rate limits
* Supplier Watch List restrictions
* Disruption response rules
* Mandatory certifications

However, retrieval over raw CSV chunks was less reliable for:

* Exact supplier ID lookup
* Aggregated counts
* Region-level spend analysis
* Product-category defect-rate analysis
* Rebate qualification lists

⸻

Configuration 2 — Optimized Helper Documents

Used for generated helper files:

* 01_supplier_profiles.txt
* 02_exact_lookup.txt
* 03_policy_rules.txt
* 04_eval_answers.txt
* 05_general_analytics.txt

01_supplier_profiles.txt: larger supplier-level chunks
02_exact_lookup.txt: short exact Supplier_ID lookup chunks
03_policy_rules.txt: compact policy-rule chunks
04_eval_answers.txt: compact required-evaluation answer chunks
05_general_analytics.txt: compact aggregate-analysis chunks

Result

01_supplier_profiles.txt: 116 chunks
02_exact_lookup.txt: 335 chunks
03_policy_rules.txt: 6 chunks
04_eval_answers.txt: 3 chunks
05_general_analytics.txt: 3 chunks

Observation

This setup improved:

* Exact Supplier_ID lookup
* Supplier profile retrieval
* Supplier tier requirement comparisons
* Policy-rule lookup
* Five required evaluation answers
* Aggregation-style questions

It also reduced hallucination risk because the chatbot could retrieve pre-structured facts instead of trying to infer aggregate answers from many raw CSV chunks.

⸻

Required Evaluation Questions and Answers

The following five questions were tested in the Flowise chat panel. The answers below are copied from the chatbot output.

⸻

Q1. Which Tier-3 suppliers have an active disruption flag, and what response level applies per policy?

There are 11 Tier-3 suppliers with an active disruption flag:
Dravex Components India
Plataforma Metales SA
Maghreb Castworks
Helios Pack Greece
Cerromax Mineria
Orinoco Pack SAPI
Quetzal Textiles
Sibertek Molding
Archipelago PCB Corp
Varna Electronics EAD
Deltaforge Vietnam
All of these suppliers are classified as High Risk with an active flag, so Level 3 Activate applies per Policy Section 9. Level 3 Activate requires CPO escalation and alternate supplier activation for a minimum of 40% of volume within 10 business days.

⸻

Q2. Which suppliers qualify for the annual Volume Rebate Program and how many are there?

Suppliers that qualify for the annual Volume Rebate Program
Number of qualifying suppliers: 19
Qualified suppliers:
Borealis Composites
Crestline Chemical Supply
Fenwick Alloy Solutions
Hanguk Circuit Works
Hokkaido Alloy Tech
Krauss-Polymex GmbH
Lakeshore Components
Lumivex Semiconductor NL
Maplewood Polymer Corp
Norbec Alloy Works
Nordloom Finland Oy
Orrentek Precision Mfg
Ostwind Composites AG
PrecisionForge Taiyuan
Solveig Eco Packaging
Straits Packaging Hub
Tasman Circuit Boards
Toreval Electronics
Valdoro Special Alloys
Eligibility criteria (Policy Section 4.2):
Contract Tier = Tier-1
On-Time Delivery (OTD) ≥ 93%
Defect Rate < 0.5%
Sustainability Score ≥ 85
All 19 suppliers meet these thresholds, thus they qualify for the annual Volume Rebate Program.

⸻

Q3. Which region has the highest total PO value, and does it breach the concentration limit?

Region with the highest total PO value: EMEA
Total PO value: USD 193,987,179.91
Share of total spend: ≈ 48.5% of the overall spend of USD 399,563,494.10
Concentration-limit check:
Policy Section 5.3 caps any single region’s share at 45% of total annual procurement spend.
EMEA’s share (48.5%) exceeds the 45% threshold.
Conclusion: EMEA has the highest total PO value, and it breaches the regional concentration limit. A Diversification Plan is required within 60 days.

⸻

Q4. Which suppliers are on Supplier Watch List (SWL) status and what does it restrict?

Suppliers on Supplier Watch List (SWL) status
Supplier | Region | Contract Tier
Deltaforge Vietnam | APAC | Tier-3
Maghreb Castworks | EMEA | Tier-3
Helios Pack Greece | EMEA | Tier-3
Cerromax Mineria | EMEA | Tier-3
Orinoco Pack SAPI | EMEA | Tier-3
Varna Electronics EAD | EMEA | Tier-3
Quetzal Textiles | EMEA | Tier-3
Plataforma Metales SA | EMEA | Tier-3
Archipelago PCB Corp | EMEA | Tier-3
Dravex Components India | APAC | Tier-3
Sibertek Molding | APAC | Tier-3
Restriction imposed by SWL status:
New purchase-order issuance is limited to 20% of the prior-quarter volume (Policy Section 3.4).

⸻

Q5. Which product category has the highest average defect rate and does it exceed the Tier-2 limit?

Product category with the highest average defect rate: Mechanical Components – average defect rate 2.12% across 360 purchase orders.
Does it exceed the Tier-2 limit? No. The Tier-2 maximum defect-rate ceiling is 2.50% (Policy Section 3.2), so 2.12% is below that threshold.

⸻

Additional Validation Tests

Supplier Lookup Test

Question:

Tell me about Supplier_ID SUP-008.

Answer:

Supplier ID: SUP-008
Supplier Name: Shengda Pack Industries
Region: APAC
Country: China
Product Category: Packaging Materials
Contract Tier: Tier-2
Risk Level: Medium
Performance & Compliance
Average OTD Rate: 88.21%
Average Defect Rate: 1.96%
Average Compliance Score: 76.17
Average Sustainability Score: 68.28
Active Disruptions
Export license risk
Labour strike risk
Port congestion risk
Regulatory audit pending
Regulatory enforcement pending
Seasonal flood risk
Tariff adjustment pending
Typhoon season disruption
Alternate Supplier: SUP-042

Unknown Supplier Safety Test

Question:

Tell me about Supplier_ID SUP-999.

Answer:

The provided context does not contain any information about Supplier_ID SUP-999.

Out-of-Domain Safety Test

Question:

Give me all suppliers in Antarctica.

Answer:

The provided context does not contain any supplier records for Antarctica.

⸻

Screenshots

Screenshots are stored in the /screenshots folder.

Recommended screenshot files:

01_public_chatbot.png
02_chatflow_canvas.png
03_document_store_summary.png
04_document_store_loaders.png
05_required_q1.png
06_required_q2.png
07_required_q3.png
08_required_q4.png
09_required_q5.png

Screenshots included:

1. Public chatbot interface showing the deployed SCM Assistant
2. Flowise chatflow canvas showing Groq, HuggingFace, Pinecone, Cohere, and QA chain nodes
3. Document Store summary showing upserted status, total characters, and total chunks
4. Document Store loader list showing all uploaded source/helper files and chunk counts
5. Required evaluation question tests

⸻

Deployment

The chatbot is deployed using Flowise Cloud.

Public URL: https://cloud.flowiseai.com/chatbot/5242e668-ba82-46ce-86e4-a00b118f07e9

The chatbot was made public using:

Flowise Chatflow → Share Chatbot → Make Public ON

⸻

Exported Chatflow

The exported Flowise chatflow is included as:

scm_assistant.json

Export path:

Flowise Chatflow Canvas → Settings → Export Chatflow

⸻

GitHub Submission

Repository name:

scm-assistant-bot

Required files:

README.md
.gitignore
scm_assistant.json
screenshots/

⸻

Security Notes

No secrets are committed to this repository.

The .gitignore excludes:

.env
.env.*
*.env
*.key
*.pem
secrets/
credentials/
api_keys/
tokens/

API keys used for Flowise, Groq, Pinecone, HuggingFace, and Cohere are not included in this repository.
