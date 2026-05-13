# Gemini 3 Flash - Text to SQL Evaluation 

## 1. Introduction 
- The rapid advancement of Large Language Models (LLMs) has significantly improved Natural Language Processing applications, particularly in Text-to-SQL generation. Text-to-SQL systems aim to convert natural language queries into executable SQL statements, enabling users to interact with databases without requiring extensive knowledge of SQL syntax.
- This research focuses on evaluating and improving Text-to-SQL performance using Supervised Fine-Tuning (SFT) and Direct Preference Optimization (DPO). Initially, the study investigates the baseline capabilities and accuracy of the Gemini 3 Flash model on SQL-related tasks through a series of benchmark query evaluations. The objective is to analyze the model’s reasoning ability, SQL generation accuracy, and overall effectiveness in handling database-oriented natural language queries.

## 2. Objective 
The primary objective of this experiment is to evaluate the Text-to-SQL generation capability of the **Gemini 3 Flash** model by measuring its accuracy in converting natural language queries into valid SQL statements. The experiment aims to analyze the model’s SQL reasoning ability, query generation performance, and effectiveness in handling different database-related tasks.

Additionally, this study serves as a baseline evaluation for future research focused on improving Text-to-SQL performance using Supervised Fine-Tuning (SFT) and Direct Preference Optimization (DPO). The results obtained from the experiment will help identify the strengths, limitations, and potential improvement areas of the model for advanced alignment and optimization techniques.


## 3. Dataset Information 
The dataset used in this experiment is derived from the BIRD Benchmark Development Set[1]. Five databases were randomly selected from the eleven databases in the development split to ensure a diverse evaluation setup.

| Database           | Total Queries |  Simple | Moderate | Challenging |
| ------------------ | ------------: | ------: | -------: | ----------: |
| Card Games         |           191 |     125 |       53 |          13 |
| Codebase Community |           186 |     151 |       30 |           5 |
| Formula            |           174 |     117 |       43 |          14 |
| Student Club       |           158 |     101 |       48 |           9 |
| Toxicology         |           145 |      76 |       38 |          31 |
| **Total**          |       **854** | **570** |  **212** |      **72** |


## 4. Experiment Setup 
The experimental setup involves **accessing the model through the Google Gemini web application using the Gemini 3 Flash model (fast mode)**. The evaluation is structured in sessions, where each session consists of 30 prompts.
In each session, the first prompt includes the full input format comprising the instruction, question, and evidence to establish context. The remaining 29 prompts include only the  question and evedence, with variations in reasoning strategies such as **no Chain-of-Thought (no-CoT), Chain-of-Thought (CoT), and Divide-and-Conquer CoT.**

These prompt strategies are designed to evaluate different reasoning behaviors of the model under structured and unstructured reasoning conditions. The overall prompting methodology is inspired by the research paper “ExCoT: Optimizing Reasoning for Text-to-SQL with Execution Feedback” [2], which emphasizes improving Text-to-SQL performance through structured reasoning and execution-guided feedback.

### 4.1 Prompts 
The prompts used in this experiment are inspired by the research paper “ExCoT: Optimizing Reasoning for Text-to-SQL with Execution Feedback”[2], particularly for implementing different reasoning strategies such as no Chain-of-Thought (no-CoT), Chain-of-Thought (CoT), and Divide-and-Conquer CoT. The prompt templates corresponding to each reasoning type are provided below.

#### 4.1.1 noCoT Prompt Template
```
System:
You are an AI assistant helping a data analyst write SQL queries to answer questions.
User:
Below I will provide a DB schema and a question that can be answered by querying the provided DB. You
will then write a SQL query enclosed in ‘‘‘sql ...‘‘‘ that answers the question (and nothing else).
Database Schema: {Schema }
Question: {Question }
```
#### 4.1.2 simpleCoT Prompt Template
```
System:
You are an AI assistant helping a data analyst write SQL queries to answer questions.
User:
Below I will provide a DB schema and a question that can be answered by querying the provided DB. You
will then write out your thought process in detail followed by a single SQL query enclosed in ‘‘‘sql
...‘‘‘ that answers the question.
Database Schema: {Schema }
Question: {Question }
```
#### 4.1.3 Divide & Conquer Template
```
System:
As a Text2SQL assistant, your main task is to formulate an SQL query in response to a given natural
language inquiry. This process involves a chain-of-thought (CoT) approach, which includes a ’divide and
conquer’ strategy.
In the ’divide’ phase of this CoT process, we break down the presented question into smaller, more
manageable sub-problems using pseudo-SQL queries. During the ’conquer’ phase, we aggregate the
solutions of these sub-problems to form the final response.
Lastly, we refine the constructed query in the optimization step, eliminating any unnecessary clauses and
conditions to ensure efficiency.
User:
Below I will provide a DB schema and a question that can be answered by querying the provided DB. You
will then write out your thought process in detail followed by a single SQL query enclosed in ‘‘‘sql
...‘‘‘ that answers the question.
Database Info: Database Schema: {Schema }
Question: Question: {Question }
Main Question: {Main Question } Analysis: {Analysis }
Pseudo SQL: “‘sql {Pseudo SQL }“‘
Sub-questions:
1. {Sub-question } Analysis: {Analysis } 2. {Sub-question } Analysis: {Analysis } Pseudo SQL: “‘sql {Pseudo SQL }“‘
Pseudo SQL: “‘sql {Pseudo SQL }“‘
Final SQL Assembly: “‘sql {SQL }“‘
Optimization: {Analysis } “‘sql {Optimized SQL }“‘
```

### 4.2 Evaluation 
The performance of the model is evaluated using two main metrics: validation accuracy and execution accuracy. Validation accuracy assesses whether the generated SQL queries are syntactically valid and logically consistent, while execution accuracy measures whether the queries are both syntactically correct and semantically accurate by verifying the correctness of the executed results.


## Result
| Database           | Reasoning Type         | Validation Accuracy | Execution Accuracy |
| ------------------ | ---------------------- | ------------------: | -----------------: |
| Card Games         | No-CoT                 |           164 / 191 |          140 / 191 |
| Card Games         | Simple CoT             |           168 / 191 |          138 / 191 |
| Card Games         | Divide-and-Conquer CoT |                  NC |                 NC |
| Codebase Community | No-CoT                 |                  NC |                 NC |
| Codebase Community | Simple CoT             |                  NC |                 NC |
| Codebase Community | Divide-and-Conquer CoT |                  NC |                 NC |
| Formula            | No-CoT                 |                  NC |                 NC |
| Formula            | Simple CoT             |                  NC |                 NC |
| Formula            | Divide-and-Conquer CoT |                  NC |                 NC |
| Student Club       | No-CoT                 |                  NC |                 NC |
| Student Club       | Simple CoT             |                  NC |                 NC |
| Student Club       | Divide-and-Conquer CoT |                  NC |                 NC |
| Toxicology         | No-CoT                 |                  NC |                 NC |
| Toxicology         | Simple CoT             |                  NC |                 NC |
| Toxicology         | Divide-and-Conquer CoT |                  NC |                 NC |

### References
- [1] https://bird-bench.github.io
- [2] https://arxiv.org/pdf/2503.19988

### Experiment Files 

#### Card Games 
- [1] 400 -460(no cot) https://gemini.google.com/share/48211d84d2c1
- [2] 460 - 500(no cot) https://gemini.google.com/share/5ca3e5eaf8d6
- [3] 491- 530(no cot) https://gemini.google.com/share/0e49ce63228d
- [4] 370 - 400(no cot) https://gemini.google.com/share/d8ac6780a413
- [5] 340 - 370(no cot) https://gemini.google.com/share/6871648e6519
- [1] 400 - 430(simple cot) https://gemini.google.com/share/d774ee8c6abd
- [2] 431 - 460(simple cot) https://gemini.google.com/share/622bfdc84911
- [3] 461 - 490(simple cot) https://gemini.google.com/share/92a957b85f75
- [4] 491 - 520(simple cot) https://gemini.google.com/share/deceb2eb6b86
- [5] 371 - 400(simple cot) https://gemini.google.com/share/9dd7524f3404
- [6] 340 - 370(simple cot) https://gemini.google.com/share/54db2581d011兒
