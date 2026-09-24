# AI Resume Matcher

**Client production MVP for semantic resume search, candidate retrieval and ranking using vector embeddings, FAISS and a Telegram interface.**

AI Resume Matcher is an applied HRTech system developed to automate the first stage of candidate screening.

Instead of manually opening a resume database and comparing candidates one by one, the recruiter can provide a vacancy description and receive a ranked shortlist of the most relevant profiles.

The high-level workflow is:

```text
Resume Database
      ↓
Data Preparation
      ↓
Text Representation
      ↓
Embeddings
      ↓
FAISS Vector Index
      ↓
Semantic Search
      ↓
Multi-Factor Ranking
      ↓
TOP-N Candidates
      ↓
Telegram
      ↓
Human Review
```

> **Project status:** Working client production MVP / end-to-end tested candidate retrieval and decision-support system.

> **Role:** AI / Automation Developer.

> **Important boundary:** The system supports candidate discovery and prioritization. It does not make autonomous hiring decisions.

> **Public scope:** Candidate data, client datasets, production credentials, Telegram tokens, exact private ranking configuration and other sensitive operational details are not published.

---

# Product Purpose

Recruiters often spend substantial time on repetitive first-stage screening.

A typical manual process looks like:

```text
Vacancy
   ↓
Open Resume Database
   ↓
Read Many Profiles
   ↓
Compare Experience
   ↓
Compare Skills
   ↓
Check Other Relevant Requirements
   ↓
Create Shortlist
```

The purpose of AI Resume Matcher is to reduce the amount of resume material that must be inspected manually.

The automated flow becomes:

```text
Vacancy
   ↓
Semantic Search
   ↓
Candidate Ranking
   ↓
TOP-N
   ↓
Human Review
```

The final hiring decision remains with a human recruiter.

---

# Core Engineering Principle

The system is based on a simple idea:

> **Search for meaning, not only exact keywords.**

Traditional search depends heavily on lexical overlap.

For example:

```text
Vacancy:
"Systems administration specialist"

Candidate:
"Linux / Windows infrastructure administrator"
```

A strict keyword search may underestimate the relationship.

Semantic retrieval instead works conceptually as:

```text
Vacancy Meaning
      ↓
Embedding
      ↓
Vector Space
      ↓
Nearest Candidate Representations
```

This makes candidate discovery less dependent on identical wording.

---

# Business Problem

Manual first-stage recruitment frequently requires:

- reviewing many resumes;
- comparing job requirements with candidate experience;
- checking professional relevance;
- checking location or operational constraints;
- comparing several candidates;
- building a shortlist;
- repeating the same process for multiple vacancies.

This creates repetitive work before meaningful human evaluation even starts.

AI Resume Matcher automates the upper retrieval layer:

```text
Candidate Pool
→ Intelligent Retrieval
→ Ranking
→ Shortlist
→ Human Assessment
```

---

# What the System Automates

Before:

```text
Vacancy
→ Open Candidate Database
→ Read Large Number of Resumes
→ Compare Manually
→ Shortlist
```

After:

```text
Vacancy
→ Telegram
→ Semantic Retrieval
→ Candidate Ranking
→ TOP-N
→ Human Review
```

The system is therefore best understood as:

**AI-assisted candidate retrieval and decision support.**

---

# System Architecture

The application contains several connected layers.

```text
┌────────────────────────┐
│ Resume Data            │
│ Excel / Structured DB  │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Data Preparation       │
│ Pandas / Normalization │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Text Corpus            │
│ Candidate Documents    │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Embedding Layer        │
│ RuBERT                 │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ FAISS Vector Index     │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Search & Ranking       │
│ Semantic + Weighted    │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Telegram Application   │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Recruiter              │
└────────────────────────┘
```

---

# Data Layer

The initial candidate database is loaded from a structured Excel source into Pandas.

The original implementation works with a set of resume attributes relevant to candidate discovery.

For the public case, personally sensitive candidate fields are deliberately not reproduced.

The public architecture focuses on professional and operational information such as:

- target position;
- professional experience;
- technical skills;
- general skills;
- employment history;
- education;
- languages;
- employment format;
- relocation or location requirements where legitimately relevant.

---

# Responsible HR Data Handling

Employment-related AI requires additional discipline.

The public portfolio therefore follows several principles:

```text
AI Retrieves
AI Ranks for Review
Human Decides
```

and:

```text
Professional Relevance
>
Irrelevant Personal Characteristics
```

Potentially sensitive or protected personal information should not be used as an automated proxy for candidate quality.

The original engineering analysis also identified the need to remove irrelevant personal information and increase the influence of genuinely job-relevant factors.

---

# Data Preparation

Raw structured records are not indexed directly without preparation.

The pipeline performs:

```text
Excel
   ↓
Pandas DataFrame
   ↓
Relevant Fields
   ↓
Normalization
   ↓
Candidate Text Representation
```

This creates a more consistent input for semantic retrieval.

---

# Structured Record to Search Document

Each candidate row is transformed into a text document.

Conceptually:

```text
Structured Resume Row
        ↓
Normalized Candidate Text
        ↓
Stable Internal Identifier
        ↓
Vector Document
```

The identifier preserves the connection between:

```text
Vector Search Result
```

and:

```text
Original Candidate Record
```

This is important because the vector index is only the retrieval layer.

The full candidate record remains in the structured data source.

---

# Candidate Identity Mapping

A vector index returns documents or document identifiers.

The application therefore maintains a mapping:

```text
Candidate Record
      ↕
Search Document
      ↕
Vector Index Result
```

After a relevant vector is found, the application can return to the original DataFrame and reconstruct the candidate information required for the recruiter.

---

# Data Normalization

Technical source column names were normalized into clearer application-level concepts.

The general pattern is:

```text
Source Schema
     ↓
Application Schema
     ↓
Search Corpus
```

This reduces unnecessary coupling between:

- source Excel structure;
- text representation;
- retrieval layer.

---

# Embedding Layer

Candidate text is transformed into numerical vector representations.

The original implementation uses:

```text
cointegrated/rubert-tiny2
```

through Hugging Face / LangChain integration.

Conceptually:

```text
Candidate Text
      ↓
RuBERT Embedding
      ↓
Numeric Vector
```

The embeddings are normalized before search.

---

# Why RuBERT

The candidate data and search scenarios are Russian-language oriented.

A Russian-language embedding model therefore provides a more appropriate semantic representation than a generic lexical search.

The architecture remains replaceable:

```text
Candidate Text
      ↓
Embedding Model
      ↓
Vector
```

The retrieval layer does not fundamentally depend on one permanent model family.

---

# Vector Search Layer

FAISS is used as the primary vector search engine.

The index is built from prepared candidate documents.

Conceptually:

```text
Candidate Documents
       ↓
Embeddings
       ↓
FAISS Index
```

A vacancy query follows the same embedding process:

```text
Vacancy
   ↓
Embedding
   ↓
FAISS Similarity Search
   ↓
Nearest Candidate Vectors
```

---

# Why FAISS

FAISS provides an efficient mechanism for similarity search in vector space.

A key benefit of this architecture is that the complete candidate database does not need to be sent to an LLM for every request.

Instead:

```text
Vacancy Query
      ↓
Embedding
      ↓
Vector Search
      ↓
Small Relevant Candidate Set
```

This is substantially different from repeatedly placing the entire resume corpus into an LLM context.

---

# Semantic Candidate Retrieval

The recruiter can provide a vacancy description containing information such as:

- job role;
- responsibilities;
- professional experience;
- required technologies;
- hard skills;
- other relevant requirements.

The search flow becomes:

```text
Vacancy Text
      ↓
Embedding
      ↓
FAISS
      ↓
Relevant Candidate IDs
      ↓
Original Resume Records
```

---

# Semantic Search vs Keyword Search

Keyword retrieval works well when both sides use the same terminology.

Semantic retrieval is useful when wording differs but meaning remains similar.

For example:

```text
"Backend Python Engineer"
```

may be semantically close to a profile describing:

```text
"Django, FastAPI, REST API and Python service development"
```

even when the exact vacancy title is absent.

---

# Multi-Factor Ranking

The project evolved beyond a single similarity query.

A second search/ranking layer allows multiple candidate-relevance dimensions to be considered.

Conceptually:

```text
Professional Relevance
+
Location / Operational Fit
+
Additional Job-Relevant Factors
      ↓
Combined Candidate Ranking
```

This transforms the architecture from:

```text
Single Vector Search
```

into:

```text
Candidate Matching Engine
```

---

# Independent Search Signals

Different candidate attributes can produce separate retrieval signals.

For example:

```text
Signal A
→ Professional Relevance

Signal B
→ Geographic / Operational Requirement
```

Additional job-relevant signals may be incorporated where appropriate.

The results can then be normalized before aggregation.

---

# Score Normalization

Different search queries may return scores in different ranges.

To combine them, the project includes a normalization stage.

Conceptually:

```text
Raw Similarity Scores
        ↓
Normalization
        ↓
Comparable Search Signals
```

Without normalization, one signal may dominate simply because its score distribution uses a different range.

---

# Weighted Ranking

After normalization, individual relevance signals can be weighted.

Conceptually:

```text
Candidate Score
=
Weight A × Signal A
+
Weight B × Signal B
+
...
+
Weight N × Signal N
```

The exact production weighting configuration is intentionally not published.

The important engineering principle is:

> **Candidate ranking can combine multiple independent retrieval signals instead of relying on one similarity number.**

---

# Ranking Extensibility

The architecture can theoretically incorporate additional legitimate job-related criteria such as:

- professional skills;
- relevant experience;
- role relevance;
- required language capability;
- employment format;
- work location constraints;
- relocation availability;
- education where required by the role.

The correct set of criteria depends on the business and legal context.

---

# Protected Attributes

The system should not treat protected or irrelevant personal characteristics as proxies for professional suitability.

The production-oriented design principle is:

```text
Job-Relevant Evidence
→ Ranking

Protected / Irrelevant Personal Data
→ Exclude From Ranking
```

Human review remains mandatory.

---

# Candidate Ranking Result

After scores are calculated, candidates are ordered and the application returns the requested number of results.

```text
Candidate Pool
      ↓
Search
      ↓
Normalized Signals
      ↓
Combined Ranking
      ↓
Sort
      ↓
TOP-N
```

The recruiter chooses the desired result count.

---

# Telegram Interface

The vector search engine was wrapped in a Telegram application so the client does not need to interact with:

- Python;
- notebook code;
- FAISS;
- embeddings;
- Pandas;
- vector search functions.

The user-facing interface becomes:

```text
Telegram
```

---

# Telegram Technology

The application layer uses:

**python-telegram-bot**

with components such as:

```text
ApplicationBuilder
CommandHandler
MessageHandler
ConversationHandler
ContextTypes
async / await
```

This turns the search engine into an event-driven user-facing application.

---

# Recruiter Workflow

The main interaction is intentionally simple.

## Step 1 — Start

```text
/start
```

---

## Step 2 — Vacancy

The bot requests a vacancy description.

```text
Enter Vacancy
```

The recruiter sends the text.

---

## Step 3 — State Storage

The vacancy text is stored in the current Telegram conversation state.

Conceptually:

```text
context.user_data
```

---

## Step 4 — Result Count

The bot asks how many resumes should be returned.

For example:

```text
5
```

---

## Step 5 — Search

The application passes the vacancy into the semantic retrieval layer.

```text
Vacancy
→ Embedding
→ FAISS
```

---

## Step 6 — Ranking

Retrieved candidates are processed by the ranking logic.

```text
Retrieved Candidates
→ Ranking
→ TOP-N
```

---

## Step 7 — Result Cleanup

Internal technical identifiers are removed from the user-facing result.

---

## Step 8 — Telegram Delivery

The ranked candidate selection is sent directly to the recruiter.

```text
TOP-N
→ Telegram
→ Recruiter
```

---

# User Input Validation

The bot also handles basic invalid states.

Examples include:

```text
Missing Vacancy
Invalid Result Count
Conversation Cancellation
```

The interaction can be cancelled through a dedicated command.

This prevents malformed user input from being passed blindly into the search pipeline.

---

# Conversation State

The Telegram interface uses conversation state to separate the interaction into stages.

Conceptually:

```text
START
   ↓
WAIT_FOR_VACANCY
   ↓
WAIT_FOR_RESULT_COUNT
   ↓
SEARCH
   ↓
RETURN_RESULTS
   ↓
END
```

This makes the interface easier to use than a single complex command containing all parameters.

---

# Async Telegram Layer

Handlers are implemented using asynchronous functions.

Conceptually:

```text
Telegram Update
      ↓
Async Handler
      ↓
Search Logic
      ↓
Response
```

This creates a more application-like interaction model than a purely sequential notebook script.

---

# Telegram Polling

The bot receives Telegram updates through polling.

The operational flow is:

```text
Telegram
   ↓
Bot Handler
   ↓
Search Engine
   ↓
FAISS
   ↓
Ranking
   ↓
Telegram Response
```

---

# AI + Application Integration

The main value of the project is not an isolated vector-search experiment.

It combines several layers:

```text
Data Layer
Excel / Pandas
       ↓
NLP Layer
RuBERT Embeddings
       ↓
Vector Retrieval
FAISS
       ↓
Ranking Layer
Normalization + Weighted Signals
       ↓
Application Logic
Python
       ↓
User Interface
Telegram
```

This converts an AI retrieval technique into an operational tool.

---

# Technology Stack

Public technology categories:

**Python · Pandas · Excel · Hugging Face · RuBERT · Sentence Transformers · LangChain · FAISS · Vector Search · Semantic Search · Embeddings · Candidate Ranking · Async Python · python-telegram-bot · Telegram Bot API · Google Colab · HRTech · AI Decision Support**

---

# Implemented Components

The project implemented and tested the following pipeline:

```text
Resume Import
→ Data Inspection
→ Field Selection
→ Data Preparation
→ Resume-to-Text Conversion
→ Candidate Identifier Mapping
→ Embedding Generation
→ FAISS Index
→ Semantic Search
→ Candidate Recovery
→ Multi-Factor Retrieval
→ Score Normalization
→ Weighted Ranking
→ Candidate Sorting
→ TOP-N
→ Telegram Interface
→ Conversation State
→ Input Validation
→ Async Processing
→ Telegram Result Delivery
→ End-to-End Test
```

---

# End-to-End Validation

The complete customer scenario was tested.

```text
Recruiter
   ↓
Telegram
   ↓
Vacancy
   ↓
Requested Candidate Count
   ↓
Embedding
   ↓
FAISS Search
   ↓
Candidate Ranking
   ↓
TOP-N
   ↓
Telegram
```

This validates the interaction between:

- source data;
- NLP layer;
- vector retrieval;
- ranking;
- application state;
- user interface.

---

# Client Production MVP

The project was used as a working client solution.

At the same time, the available technical evidence shows Google Colab as the demonstrated execution environment.

Therefore this public case intentionally does not claim:

- permanent 24/7 hosting;
- dedicated VPS deployment;
- Docker production runtime;
- high-availability infrastructure;
- formal SLA;
- full observability stack;
- autoscaling infrastructure.

The correct maturity description is:

> **Working client production MVP with an end-to-end Telegram interface.**

---

# Runtime Environment

Google Colab was used during the documented implementation.

This was sufficient for the validated client MVP and development workflow.

However, notebook runtime introduces an operational limitation:

```text
Notebook Running
→ Bot Available

Notebook Stopped
→ Runtime Stops
```

A permanently available commercial version would require a persistent runtime environment.

---

# Production Evolution

A hardened deployment could move conceptually toward:

```text
Persistent Backend
      ↓
Versioned Data Pipeline
      ↓
Persistent Vector Index
      ↓
Telegram Webhook / Polling
      ↓
Monitoring
      ↓
Backup / Recovery
```

These are future hardening directions and are not presented as components already implemented in the documented MVP.

---

# Vector Index Persistence

A permanent deployment should avoid unnecessarily rebuilding the entire candidate index for every application restart.

A production evolution may introduce:

```text
Resume Database
      ↓
Controlled Index Build
      ↓
Persisted Vector Index
      ↓
Incremental Update
```

The exact persistence implementation is outside the documented MVP scope.

---

# Data Refresh Pipeline

Candidate databases change over time.

A mature system could support:

```text
New / Updated Resume
      ↓
Validation
      ↓
Normalization
      ↓
Embedding
      ↓
Index Update
```

This allows search results to reflect the current candidate pool.

---

# Search Quality Limitations

Semantic vector search has important limitations.

The project explicitly considered several of them.

---

# Large Resume Averaging

Representing an entire resume as one embedding can dilute highly important information.

Conceptually:

```text
Important Skill
+
Large Amount of Other Information
      ↓
Single Average Representation
```

Some critical signals may lose influence.

---

# Strict Requirement Problem

Semantic similarity does not guarantee that every mandatory requirement is satisfied.

For example:

```text
High Semantic Similarity
```

does not necessarily mean:

```text
Mandatory Certification Present
```

or:

```text
Required Experience Threshold Satisfied
```

This is why vector retrieval should be combined with explicit business constraints where necessary.

---

# Hybrid Search Evolution

A more advanced version can combine:

```text
Semantic Retrieval
+
Structured Filters
+
Explicit Requirement Checks
+
Ranking
```

For example:

```text
Vector Search
→ Candidate Pool
→ Required Skill Filter
→ Experience Check
→ Final Ranking
```

This can reduce false positives.

---

# Resume Sectioning

Instead of embedding the complete resume into one vector, a more advanced implementation can create separate semantic representations for:

```text
Role
Skills
Experience
Education
Summary
```

Then:

```text
Vacancy
      ↓
Independent Matching
      ↓
Weighted Combination
```

This can make the ranking more interpretable and reduce information dilution.

---

# Bias and Fairness

Employment-related ranking systems require additional scrutiny.

Potential bias can enter through:

- historical resume data;
- non-job-related personal information;
- imbalanced candidate pools;
- proxy variables;
- inappropriate ranking criteria;
- model embedding behavior.

The design principle should therefore remain:

```text
AI-Assisted Retrieval
+
Relevant Professional Evidence
+
Human Review
```

and never:

```text
Model Score
=
Automatic Hiring Decision
```

---

# Explainability

A useful future extension is to provide not only a candidate order but also a concise explanation.

For example:

```text
Candidate A
→ Matching Python experience
→ Relevant backend background
→ Required location available
```

This creates a more auditable decision-support interface.

Such explanation tooling is a natural evolution and is not claimed as part of the original validated MVP.

---

# Privacy

Candidate resumes contain personal information.

Production handling should therefore include controls around:

- access;
- storage;
- data minimization;
- retention;
- authorized users;
- logging;
- exported results;
- backups.

The public repository contains no real candidate records.

---

# Public Data Policy

This portfolio intentionally excludes:

```text
Real Resumes
Candidate Names
Contact Details
Private HR Data
Telegram Tokens
Client Files
Private Ranking Configuration
Production Credentials
```

Only sanitized architecture and engineering patterns are documented publicly.

---

# Why Human Review Matters

Recruiting contains contextual judgment that should not be delegated blindly to an embedding score.

The system's role is:

```text
Large Candidate Set
      ↓
AI Retrieval
      ↓
Smaller Candidate Set
      ↓
Human Review
```

not:

```text
AI Score
      ↓
Automatic Employment Decision
```

This keeps the system in a decision-support role.

---

# Engineering Competencies Demonstrated

## Data Engineering

Transforming structured Excel candidate data into a controlled search corpus.

## NLP Embeddings

Representing Russian-language resumes and vacancy descriptions as semantic vectors.

## Vector Retrieval

Building and querying a FAISS candidate index.

## Semantic Search

Moving beyond exact keyword matching toward meaning-based candidate discovery.

## Candidate Ranking

Combining multiple retrieval signals into an ordered candidate list.

## Score Normalization

Making independent search results suitable for aggregation.

## Application Engineering

Connecting ML retrieval to usable Python business logic.

## Telegram Product Development

Building a client-facing conversational interface for a technical search engine.

## Async Python

Using asynchronous Telegram handlers and event-driven interaction.

## State Management

Managing multi-step recruiter conversations.

## End-to-End Testing

Validating the complete vacancy-to-shortlist workflow.

## Responsible AI Awareness

Recognizing bias, privacy, sensitive-data and human-decision boundaries in employment-related AI.

---

# Main Engineering Result

The primary engineering result is not simply:

```text
FAISS
```

or:

```text
RuBERT
```

It is the complete application pattern:

```text
Structured Business Data
      ↓
Semantic Representation
      ↓
Vector Retrieval
      ↓
Multi-Factor Ranking
      ↓
Application Interface
      ↓
Human Decision Support
```

---

# Business Value

The system reduces repetitive search work at the top of the recruitment funnel.

## Smaller Review Set

Instead of manually inspecting the entire database, the recruiter can start with a targeted shortlist.

## Natural-Language Search

The vacancy itself can act as the search query.

## Reduced Dependence on Exact Keywords

Semantic retrieval can identify candidates whose experience is described differently.

## Reusable Candidate Database

The same indexed pool can support multiple vacancy searches.

## Accessible Interface

The recruiter works through Telegram rather than Python or a notebook.

## Human Control

The system accelerates retrieval without replacing professional review.

---

# What Is Not Claimed

This public case deliberately does not claim:

- autonomous hiring decisions;
- autonomous candidate rejection;
- guaranteed recruitment quality;
- guaranteed absence of bias;
- a validated fairness benchmark;
- 24/7 production hosting;
- production VPS infrastructure;
- Docker deployment;
- Kubernetes;
- high availability;
- formal SLA;
- production monitoring stack;
- automated candidate outreach;
- automated interview decisions;
- guaranteed placement conversion;
- measured recruiter time savings;
- measured hiring ROI.

Those capabilities or metrics require separate implementation evidence.

---

# Current Status

**Working client production MVP / end-to-end tested AI candidate retrieval and ranking system.**

Validated architecture:

```text
Excel Resume Data
→ Pandas
→ Normalized Candidate Documents
→ RuBERT Embeddings
→ FAISS
→ Semantic Retrieval
→ Multi-Factor Ranking
→ TOP-N
→ Telegram
→ Human Recruiter
```

The project demonstrates how vector search can be transformed from an ML experiment into an applied HR workflow.

---

# Reusable Pattern

The engineering pattern behind AI Resume Matcher is broader than recruiting:

```text
Structured Domain Data
+
Semantic Embeddings
+
Vector Search
+
Ranking
+
Simple User Interface
=
Applied Retrieval Product
```

The same architecture can be adapted to:

- expert databases;
- supplier selection;
- knowledge retrieval;
- product matching;
- document search;
- service matching;
- internal talent search.

---

# Key Engineering Lesson

The central lesson from the project is:

> **A vector database becomes useful only when retrieval, ranking, source-data mapping and the user workflow are engineered as one system.**

The complete chain is:

```text
Data
→ Representation
→ Retrieval
→ Ranking
→ Interface
→ Human Decision
```

---

# Public Repository Scope

## Publicly Shared

- project purpose;
- sanitized architecture;
- candidate retrieval flow;
- embedding model category;
- FAISS search architecture;
- ranking approach;
- Telegram workflow;
- production-MVP boundaries;
- engineering lessons;
- responsible-AI considerations;
- development milestones;
- technology categories.

## Kept Private

- real candidate resumes;
- names and contact information;
- personal candidate attributes;
- client datasets;
- Telegram bot token;
- internal access details;
- exact private ranking weights;
- production credentials;
- client-specific business rules;
- private runtime configuration.

---

# AIAQ Lab

**AI and business-process automation focused on practical, measurable operational improvements.**

**Website:** [https://aiaqlab.com/](https://aiaqlab.com/)

**Telegram channel:** [https://t.me/ai_b2b_automation](https://t.me/ai_b2b_automation)

**Projects & consulting:** [https://t.me/ai_arch_pro](https://t.me/ai_arch_pro)

**Email:** [ai@aiaqlab.com](mailto:ai@aiaqlab.com)

**GitHub:** [https://github.com/ai-b2b-automation](https://github.com/ai-b2b-automation)

---

# Interested in AI Retrieval Systems?

Many business processes contain the same hidden problem:

```text
Large Information Pool
+
Human Search
+
Repeated Comparison
```

A semantic retrieval architecture can convert that into:

```text
Natural-Language Request
→ AI Retrieval
→ Ranked Results
→ Human Decision
```

AIAQ Lab develops applied AI systems that connect retrieval, automation and human workflows into usable business tools.

**Website:** [https://aiaqlab.com/](https://aiaqlab.com/)

**Projects & consulting:** [https://t.me/ai_arch_pro](https://t.me/ai_arch_pro)
