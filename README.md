# 🔍 RAG Evaluation Framework

> **A component-wise evaluation framework for building reliable, grounded, safe, and production-ready RAG applications using DeepEval.**

Retrieval-Augmented Generation (RAG) systems are not evaluated effectively by checking only whether the final answer "looks correct."

A RAG application can fail at multiple layers:

* The retriever may return irrelevant documents.
* The generator may hallucinate information.
* The complete pipeline may produce poorly grounded answers.
* The final application response may be incomplete or poorly structured.
* The application may generate unsafe content, leak sensitive information, or operate outside its intended scope.

This project focuses on **evaluating RAG systems layer by layer**, making it easier to identify *where* an AI system is failing and *what* needs to be improved.

---

## 🚀 Evaluation Journey

The project follows a progressive evaluation strategy:

```text
                                             RAG EVALUATION
                              │
             ┌────────────────┴────────────────┐
             │                                 │
      OFFLINE EVALUATION                PRODUCTION EVALUATION
             │                                 │
     ┌───────┴────────┐                 ┌──────┴───────┐
     │                │                 │              │
 COMPONENT-LEVEL  APPLICATION-LEVEL  OPERATIONAL   ONLINE EVALUATION
     │                │                 │              │
 ┌───┼────┐       ┌───┴────┐        ┌───┼────┐    LangSmith
 │   │    │       │        │        │   │    │       │
 ▼   ▼    ▼       ▼        ▼        ▼   ▼    ▼       ▼
Retr Gen Pipeline Quality Security Cost Token Latency  Real User
 │   │      │       │        │       │    │      │     Traces
 │   │      │       │        │       │    │      │       │
 ▼   ▼      ▼       ▼        ▼       └────┴──────┘       ▼
Prec Faith Context Correct Toxicity       │       ┌───────────────┐
Recall Rel. Relevancy Complete Leakage     │       │ Online Metrics│
             Faithfulness Style Scope     │       │ Quality +     │
             Answer Relevancy Adherence   │       │ Performance   │
                                          │       └───────┬───────┘
                                          │               ▼
                                          │       LangSmith Dashboard
                                          │               │
                                          │        ┌──────┴──────┐
                                          │        │             │
                                          ▼        ▼             ▼
                                   REGRESSION   Monitor       Improve
                                   TESTING        │             │
                                      │           └──────┬──────┘
                                      │                  │
                                      └──── Re-evaluate ─┘
```

### 📌 Five Evaluation Stages

| Stage | Component           | Evaluation                                           |
| ----- | ------------------- | ---------------------------------------------------- |
| 1️⃣   | Retriever           | Precision, Recall                                    |
| 2️⃣   | Generator           | Faithfulness, Answer Relevancy                       |
| 3️⃣   | End-to-End RAG      | Contextual Relevancy, Faithfulness, Answer Relevancy |
| 4️⃣   | Application Quality | Correctness, Completeness, Style                     |
| 5️⃣   | Security & Safety   | Toxicity, Leakage, Scope Adherence                   |

---

# 🏗️ System Architecture

```text
                                              ┌──────────────────────────┐
                         │        USER QUERY        │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │       RAG PIPELINE        │
                         │                          │
                         │  Query → Retriever       │
                         │          ↓               │
                         │   Retrieved Context      │
                         │          ↓               │
                         │      LLM / Generator     │
                         │          ↓               │
                         │     Final Response       │
                         └────────────┬─────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                 │
                    ▼                 ▼                 ▼
          ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
          │    RETRIEVER    │ │    GENERATOR    │ │    PIPELINE     │
          │    EVALUATION   │ │    EVALUATION   │ │    EVALUATION   │
          │                 │ │                 │ │                 │
          │ • Precision     │ │ • Faithfulness  │ │ • Contextual    │
          │ • Recall        │ │ • Answer        │ │   Relevancy     │
          │                 │ │   Relevancy     │ │ • Faithfulness  │
          └────────┬────────┘ └────────┬────────┘ │ • Answer        │
                   │                   │          │   Relevancy     │
                   │                   │          └────────┬────────┘
                   └───────────────────┼───────────────────┘
                                       │
                                       ▼
                         ┌──────────────────────────┐
                         │   APPLICATION EVALUATION │
                         │                          │
                         │ • Correctness            │
                         │ • Completeness           │
                         │ • Style                  │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │    SECURITY & SAFETY     │
                         │                          │
                         │ • Toxicity               │
                         │ • Information Leakage    │
                         │ • Scope Adherence        │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │    REGRESSION TESTING    │
                         │                          │
                         │ Compare with Previous   │
                         │ Evaluation Runs          │
                         │                          │
                         │ • Detect Quality Drops   │
                         │ • Detect New Failures    │
                         │ • Pass / Fail            │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │  OPERATIONAL EVALUATION  │
                         │                          │
                         │ • Latency                │
                         │ • Token Usage            │
                         │ • LLM / Inference Cost   │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                    ╔════════════════════════════════════╗
                    ║       PRODUCTION DEPLOYMENT        ║
                    ╚══════════════════╤═════════════════╝
                                       │
                                       ▼
                         ┌──────────────────────────┐
                         │    REAL USER QUERIES     │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │   LANGSMITH TRACING      │
                         │                          │
                         │ • Inputs / Outputs       │
                         │ • Retrieval Traces       │
                         │ • LLM Calls              │
                         │ • Tokens / Cost          │
                         │ • Latency                │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │    ONLINE EVALUATION     │
                         │                          │
                         │ Same Quality Metrics     │
                         │ + Operational Metrics    │
                         │ on Production Traces     │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │    LANGSMITH DASHBOARD   │
                         │                          │
                         │ Quality │ Cost │ Tokens  │
                         │ Latency │ Errors │ Trends│
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │   MONITOR & IMPROVE      │
                         │                          │
                         │ Detect → Analyze → Fix   │
                         │ → Re-evaluate → Repeat   │
                         └──────────────────────────┘
```

---

# 🎯 Why Component-Wise Evaluation?

A single end-to-end score does not tell you **where the problem exists**.

For example:

```text
Bad Final Answer
       │
       ├── ❌ Retrieval Problem?
       │
       ├── ❌ Generation Problem?
       │
       ├── ❌ Context Relevancy Problem?
       │
       ├── ❌ Application Logic Problem?
       │
       └── ❌ Safety / Scope Problem?
```

Instead of treating the RAG application as one black box, this project evaluates individual components independently.

### Engineering principle

```text
Measure
   ↓
Identify Failure
   ↓
Isolate Component
   ↓
Improve
   ↓
Re-Evaluate
```

This makes RAG optimization more systematic and reproducible.

---

# 1️⃣ Retriever Evaluation

The first stage evaluates whether the retrieval system is returning the **right information**.

### Evaluation Strategy

A golden evaluation dataset is created containing:

* User query
* Expected relevant documents
* Retrieved documents

```text
Golden Dataset
      │
      ▼
    Query
      │
      ▼
   Retriever
      │
      ▼
Retrieved Documents
      │
      ▼
Precision + Recall
      │
      ▼
Retrieval Failure Analysis
```

### Metrics

#### Precision

Measures how many retrieved documents are actually relevant.

```text
Precision =
Relevant Retrieved Documents
─────────────────────────────
Total Retrieved Documents
```

#### Recall

Measures how many of the relevant documents were successfully retrieved.

```text
Recall =
Relevant Retrieved Documents
─────────────────────────────
Total Relevant Documents
```

### Goal

Identify retrieval failures such as:

* Missing relevant documents
* Irrelevant retrievals
* Poor semantic matching
* Incorrect ranking
* Insufficient retrieval depth

---

# 2️⃣ Generator Evaluation

The second stage isolates the **LLM generator** from the retriever.

Instead of allowing retrieval errors to affect the evaluation, the generator receives known/ideal context.

```text
Golden Context
      │
      ▼
   Generator
      │
      ▼
Generated Answer
      │
      ├───────────────┐
      ▼               ▼
Faithfulness    Answer Relevancy
```

This helps answer:

> "If the model receives the correct context, can it generate a grounded and relevant answer?"

### Metrics

#### 🧠 Faithfulness

Checks whether claims in the generated answer are supported by the provided context.

High faithfulness → fewer unsupported claims.

#### 🎯 Answer Relevancy

Checks whether the generated answer actually addresses the user's question.

---

# 3️⃣ End-to-End RAG Pipeline Evaluation

After independently evaluating retrieval and generation, the complete RAG pipeline is evaluated.

```text
User Query
    │
    ▼
Retriever
    │
    ▼
Retrieved Context
    │
    ▼
Generator
    │
    ▼
Final Answer
    │
    ▼
DeepEval
    │
    ├── Contextual Relevancy
    ├── Faithfulness
    └── Answer Relevancy
```

### Metrics

**Contextual Relevancy**

Evaluates whether the retrieved context is relevant to the user's query.

**Faithfulness**

Evaluates whether the final answer is grounded in the retrieved context.

**Answer Relevancy**

Evaluates whether the final answer addresses the original question.

### Why this stage matters

Component-level evaluation tells us whether individual pieces work.

End-to-end evaluation tells us whether those pieces work **together**.

---

# 4️⃣ Application-Level Quality Evaluation

A technically correct RAG pipeline can still produce responses that are not ideal for real users.

This stage evaluates the **quality of the final application response** using DeepEval GEval.

```text
User Query
     │
     ▼
RAG Application
     │
     ▼
Final Response
     │
     ▼
DeepEval GEval
     │
     ├── Correctness
     ├── Completeness
     └── Style
```

### Evaluation Dimensions

### ✅ Correctness

Does the response provide an accurate answer based on the available information?

### 📚 Completeness

Does the response sufficiently address the user's question without missing important information?

### ✍️ Style

Is the response clear, concise, structured, and appropriate for the intended application?

---

# 5️⃣ 🔐 Security & Safety Evaluation

Accuracy alone is not enough.

AI applications also need to be evaluated for **safe and controlled behavior**.

This stage uses **DeepEval GEval** to evaluate application-level security and safety.

```text
User Query
     │
     ▼
RAG Application
     │
     ▼
Final Response
     │
     ▼
DeepEval GEval
     │
     ├── Toxicity
     ├── Leakage
     └── Scope Adherence
     │
     ▼
Security & Safety Evaluation
```

### 🔴 Toxicity

Evaluates whether the application generates harmful, abusive, offensive, or inappropriate responses.

### 🔒 Leakage

Evaluates whether the application reveals information that should not be disclosed, including potentially sensitive or internal information.

### 🎯 Scope Adherence

Evaluates whether the application stays within its intended purpose and appropriately handles requests outside its defined scope.

---

# 🧪 Evaluation Methodology

The overall methodology follows:

```text
                    ┌──────────────────┐
                    │ Evaluation Data  │
                    │ Golden Dataset   │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ RAG Application  │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Evaluation Layer│
                    └────────┬─────────┘
                             │
             ┌───────────────┼────────────────┐
             │               │                │
             ▼               ▼                ▼
         Retrieval       Generation      Application
         Metrics          Metrics          Metrics
             │               │                │
             └───────────────┼────────────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Failure Analysis │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ System Improvement│
                    └────────┬─────────┘
                             │
                             ▼
                       Re-Evaluation
```

---

# 📊 Evaluation Stack

### Core

* Python
* RAG
* Large Language Models
* Vector Search
* Semantic Retrieval

### Evaluation

* DeepEval
* GEval
* Golden Evaluation Datasets
* LLM-as-a-Judge
* Component-Level Evaluation

### RAG Concepts

* Retrieval Evaluation
* Generation Evaluation
* Contextual Relevancy
* Faithfulness
* Answer Relevancy
* Application Quality
* AI Safety Evaluation

### Development

* LangChain
* ChromaDB
* Pydantic
* dotenv
* uv

---

# 📁 Project Structure

```text
RAG_EVALUATION/
│
├── .deepeval/
│
├── chroma_store/
│
├── data/
│
├── goldens/
│   └── faithfulness_dataset.json
│
├── evals/
│   ├── eval_retriever.py
│   ├── eval_generator.py
│   ├── eval_pipeline.py
│   ├── eval_application.py
│   └── eval_security.py
│
├── src/
│   ├── generator.py
│   ├── retriever.py
│   └── ...
│
├── .env
├── .env.local
├── .gitignore
├── main.py
├── pyproject.toml
├── README.md
└── uv.lock
```

> Generated environments, local vector stores, API keys, caches, and evaluation artifacts should remain excluded from version control.

---

# ⚙️ Installation

## 1. Clone the repository

```bash
git clone <repository-url>
cd RAG_EVALUATION
```

## 2. Create the environment

Using `uv`:

```bash
uv venv
```

Activate the environment on Windows:

```bash
.venv\Scripts\activate
```

## 3. Install dependencies

```bash
uv sync
```

Or install the project dependencies using your preferred Python environment manager.

---

# 🔑 Environment Variables

Create a local environment file and configure the required API credentials.

Example:

```env
GROQ_API_KEY=your_api_key
```

Never commit API keys or other secrets to Git.

---

# ▶️ Running the Project

Run the main application:

```bash
uv run main.py
```

Run individual evaluation stages through the corresponding scripts inside:

```text
evals/
```

For example:

```bash
uv run evals/eval_generator.py
```

---

# 🧠 LLM-as-a-Judge with GEval

For application-level evaluation, this project uses **GEval-style criteria** to evaluate qualitative properties of AI-generated responses.

Instead of relying only on exact-match metrics, the evaluator judges dimensions such as:

```text
Correctness
Completeness
Style
Toxicity
Leakage
Scope Adherence
```

This is particularly useful for LLM applications where a response can be technically different from a reference answer while still being correct and useful.

---

# 🔍 Failure Analysis

Evaluation is not treated as the final step.

When an evaluation identifies a problem, the next step is to determine its source.

### Example

```text
Low Final Answer Quality
        │
        ▼
Check Retriever
        │
   ┌────┴────┐
   │         │
 Good       Bad
   │         │
   ▼         ▼
Check       Improve
Generator   Retrieval
   │
   ▼
Check Grounding
   │
   ▼
Check Application Behavior
   │
   ▼
Re-run Evaluation
```

This creates an iterative improvement loop:

```text
Evaluate → Analyze → Improve → Evaluate Again
```

---

# 📈 Results

Evaluation results are generated through the individual evaluation modules.

Results should be interpreted **per component and per metric**, rather than relying on a single overall score.

Recommended reporting format:

| Evaluation Stage | Metric               | Result |
| ---------------- | -------------------- | -----: |
| Retriever        | Precision            |      — |
| Retriever        | Recall               |      — |
| Generator        | Faithfulness         |      — |
| Generator        | Answer Relevancy     |      — |
| End-to-End       | Contextual Relevancy |      — |
| End-to-End       | Faithfulness         |      — |
| End-to-End       | Answer Relevancy     |      — |
| Application      | Correctness          |      — |
| Application      | Completeness         |      — |
| Application      | Style                |      — |
| Security         | Toxicity             |      — |
| Security         | Leakage              |      — |
| Security         | Scope Adherence      |      — |

> Replace the placeholders with the actual evaluation outputs from your runs. This README intentionally does not fabricate benchmark scores.

---

# 🎯 Key Learnings

Through this project, I explored how to move from:

```text
"Does my RAG application work?"
```

to:

```text
"How well does each component work?"
```

and ultimately:

```text
"Can I measure, diagnose, improve, and validate the entire AI system?"
```

### Main takeaways

* RAG evaluation should be **component-wise**.
* Retrieval quality directly impacts downstream generation.
* Generator evaluation should isolate generation from retrieval failures.
* End-to-end evaluation is necessary after component-level testing.
* Application quality requires more than factual correctness.
* LLM applications should also be evaluated for **security and safety**.
* Evaluation results should drive engineering decisions.
* The evaluation → improvement → re-evaluation loop is critical for building reliable AI systems.

---

# 🛠️ Future Improvements

Planned improvements include:

* [ ] Expand the golden evaluation dataset
* [ ] Add more retrieval failure categories
* [ ] Add automated regression testing
* [ ] Add evaluation result tracking
* [ ] Add experiment comparison
* [ ] Add visualization of evaluation trends
* [ ] Integrate LangSmith traces with evaluation results
* [ ] Add prompt/version tracking
* [ ] Add adversarial security test cases
* [ ] Add CI-based evaluation checks
* [ ] Add automated evaluation reports
* [ ] Explore additional RAG and agent evaluation metrics

---

# 📚 Project Philosophy

This project follows a simple principle:

> **Don't just build AI systems. Measure them.**

A production-quality RAG system should be:

**Grounded → Relevant → Correct → Complete → Safe → Reliable**

The goal is to make evaluation an integral part of the AI engineering lifecycle rather than something performed only after development.

---

# 👨‍💻 About

Built by **Rohit Yadav** as a hands-on exploration of **RAG evaluation, LLM evaluation, AI reliability, and production-oriented AI engineering**.

The project is inspired in part by the RAG evaluation methodology and learning resources shared by **Nitish Singh Sir**.

---

## ⭐ If you find this project useful

Consider exploring the evaluation workflow, experimenting with your own RAG application, and extending the framework with additional metrics and test cases.

**Build → Evaluate → Diagnose → Improve → Re-Evaluate**

🚀 **Build reliable AI. Evaluate continuously. Engineer with evidence.**
