# Development Milestones

This public changelog contains selected, sanitized milestones from the design, implementation, testing and client validation of **AI Resume Matcher**.

The project is classified as a **working client production MVP for semantic candidate retrieval, ranking and Telegram-based decision support**.

Real resumes, candidate identities, client datasets, production credentials, exact private ranking configuration and other sensitive HR information remain private.

---

# Foundation

## Milestone 1 — Recruitment Search Problem Defined

The project started from a repetitive first-stage recruitment process:

```text
Vacancy
→ Open Resume Database
→ Read Candidates
→ Compare
→ Build Shortlist
```

**Outcome:** candidate retrieval became the automation target.

---

## Milestone 2 — Decision-Support Boundary Defined

The system was designed to support recruitment rather than replace the recruiter.

```text
AI
→ Retrieve and Rank

Human
→ Evaluate and Decide
```

**Outcome:** final employment decisions remained outside the automated pipeline.

---

# Data Pipeline

## Milestone 3 — Candidate Database Imported

Structured candidate information was loaded from Excel into Pandas.

**Outcome:** the candidate pool became available for programmatic processing.

---

## Milestone 4 — Source Schema Inspected

The available fields and DataFrame structure were analyzed.

**Outcome:** the search pipeline could be built on explicit source-data knowledge instead of assumptions.

---

## Milestone 5 — Search-Relevant Data Selected

The project selected candidate information relevant to retrieval and matching.

**Outcome:** unnecessary source fields did not need to dominate the semantic corpus.

---

## Milestone 6 — Data Normalization Added

Source fields were normalized into a more consistent application representation.

**Outcome:** downstream embedding and search logic became less dependent on the raw Excel schema.

---

# Candidate Corpus

## Milestone 7 — Candidate Rows Converted to Documents

Each structured candidate record was transformed into an independent text representation.

Conceptually:

```text
Excel Row
→ Candidate Document
```

**Outcome:** tabular HR data became compatible with semantic retrieval.

---

## Milestone 8 — Stable Candidate Mapping Added

A unique internal identifier preserved the connection between each vector-search document and the original candidate row.

**Outcome:** search results could be resolved back to complete structured candidate records.

---

## Milestone 9 — Document Boundary Added

Candidate documents were explicitly separated in the generated corpus.

**Outcome:** record boundaries remained recoverable during processing.

---

# Embedding Layer

## Milestone 10 — Russian-Language Embedding Model Integrated

The project connected a Hugging Face embedding model suitable for Russian-language text.

The implementation used:

```text
cointegrated/rubert-tiny2
```

**Outcome:** candidate descriptions could be represented as semantic vectors.

---

## Milestone 11 — Embedding Normalization Enabled

Embedding normalization was configured.

**Outcome:** vector representations were prepared consistently for similarity search.

---

## Milestone 12 — LangChain Embedding Integration Added

The embedding model was connected through LangChain / HuggingFaceEmbeddings.

**Outcome:** the text-processing and vector-store layers could be integrated through a common application abstraction.

---

# FAISS

## Milestone 13 — Candidate Documents Converted to LangChain Documents

Prepared resume text was wrapped into vector-store compatible documents.

**Outcome:** the candidate corpus became ready for indexing.

---

## Milestone 14 — FAISS Candidate Index Created

The project created a FAISS vector index from candidate documents.

Conceptually:

```text
Candidate Documents
→ Embeddings
→ FAISS
```

**Outcome:** semantic nearest-neighbor search became available across the candidate pool.

---

# Semantic Search

## Milestone 15 — Vacancy Search Implemented

A vacancy or textual recruitment request became the query for vector retrieval.

```text
Vacancy
→ Embedding
→ FAISS
```

**Outcome:** recruiters no longer needed to express every search as exact keywords.

---

## Milestone 16 — TOP-N Retrieval Added

The search layer supported returning a configurable number of candidates.

**Outcome:** the recruiter could control shortlist size.

---

## Milestone 17 — Candidate IDs Recovered From Results

Internal document identifiers were extracted from FAISS results.

**Outcome:** vector hits could be mapped back into the source DataFrame.

---

## Milestone 18 — Full Candidate Records Restored

The selected IDs were used to retrieve original candidate information.

**Outcome:** semantic retrieval became useful at the application layer.

---

# Multi-Factor Search

## Milestone 19 — Search Engine Extended Beyond One Query

A second version introduced multiple semantic search signals.

**Outcome:** matching evolved beyond one-dimensional similarity.

---

## Milestone 20 — Professional Relevance Signal Added

Job-role / professional relevance became one independent search component.

**Outcome:** candidate ranking could reflect semantic professional fit.

---

## Milestone 21 — Geographic / Operational Signal Added

A separate location-related search signal was introduced.

**Outcome:** ranking could incorporate operational requirements in addition to professional similarity.

---

# Score Processing

## Milestone 22 — Similarity Scores Retrieved

The search layer began using scored FAISS results rather than only document ordering.

**Outcome:** downstream ranking gained explicit numeric signals.

---

## Milestone 23 — Score Ranges Analyzed

Minimum and maximum score values were obtained for independent searches.

**Outcome:** differences between score distributions became visible.

---

## Milestone 24 — Score Normalization Implemented

Independent search scores were normalized before aggregation.

Conceptually:

```text
Raw Scores
→ Normalized Scores
```

**Outcome:** different retrieval signals became more comparable.

---

# Weighted Ranking

## Milestone 25 — Criterion Weights Introduced

Independent search factors could receive different ranking importance.

**Outcome:** candidate scoring became configurable.

---

## Milestone 26 — Combined Candidate Score Added

Normalized signals were aggregated into a combined relevance value.

Conceptually:

```text
Signal A × Weight A
+
Signal B × Weight B
+
...
=
Combined Score
```

**Outcome:** multiple search dimensions could contribute to one ranking.

---

## Milestone 27 — Candidate Sorting Added

Candidates were sorted using the combined ranking output.

**Outcome:** the system could return an ordered shortlist.

---

## Milestone 28 — TOP-N Candidate List Generated

The ranking layer produced the final requested shortlist.

**Outcome:** retrieval results became directly useful to a recruiter.

---

# Telegram Application

## Milestone 29 — Telegram Interface Added

A Telegram bot was created as the user-facing layer.

**Outcome:** users no longer needed to interact directly with Python or Colab code.

---

## Milestone 30 — python-telegram-bot Integrated

The application used:

```text
python-telegram-bot
```

with an event-driven handler architecture.

**Outcome:** the search engine became accessible through a conversational UI.

---

## Milestone 31 — ApplicationBuilder Added

Telegram application initialization was implemented.

**Outcome:** bot runtime and handlers could be managed through the framework.

---

## Milestone 32 — ConversationHandler Added

The user interaction was modeled as a multi-step conversation.

**Outcome:** vacancy input and result-count input could be collected in separate stages.

---

# Recruiter Flow

## Milestone 33 — `/start` Flow Added

The recruiter could initialize the application through:

```text
/start
```

**Outcome:** a clear entry point was available.

---

## Milestone 34 — Vacancy Input Added

The bot requested the vacancy text.

**Outcome:** semantic search could be driven by natural-language job descriptions.

---

## Milestone 35 — Vacancy State Stored

The current request was stored in Telegram conversation state.

**Outcome:** later interaction steps retained the search context.

---

## Milestone 36 — Result Count Requested

The recruiter was asked how many candidates should be returned.

**Outcome:** shortlist size became user-configurable.

---

## Milestone 37 — Numeric Input Validation Added

Invalid result-count input was checked before search execution.

**Outcome:** malformed input did not enter the ranking pipeline.

---

## Milestone 38 — Search Connected to Telegram

After collecting the vacancy and candidate count, the bot invoked the semantic search engine.

**Outcome:** the user interface and ML layer became one application.

---

## Milestone 39 — Results Cleaned for User Display

Internal technical identifiers were removed before presentation.

**Outcome:** recruiter output remained focused on candidate information rather than implementation metadata.

---

## Milestone 40 — Candidate Shortlist Returned

The bot delivered ranked candidate results directly into Telegram.

**Outcome:** the complete user scenario became usable without code.

---

# Async Application Layer

## Milestone 41 — Async Telegram Handlers Implemented

Bot handlers were implemented using asynchronous functions.

**Outcome:** the interface moved beyond a purely sequential notebook interaction pattern.

---

## Milestone 42 — Async Runtime Compatibility Added

Runtime compatibility for notebook execution was configured where required.

**Outcome:** the Telegram application could execute within the documented development environment.

---

# Polling

## Milestone 43 — Telegram Polling Started

The bot was launched through a polling runtime.

Conceptually:

```text
Telegram Update
→ Handler
→ Search
→ Result
```

**Outcome:** new Telegram messages could continuously enter the application while the runtime remained active.

---

# End-to-End Integration

## Milestone 44 — Data and Interface Layers Connected

The system combined:

```text
Excel
→ Pandas
→ Embeddings
→ FAISS
→ Ranking
→ Telegram
```

**Outcome:** ML retrieval became part of a complete application.

---

## Milestone 45 — Vacancy-to-Shortlist Scenario Tested

The full recruiter workflow was executed end to end.

```text
Vacancy
→ Search
→ Ranking
→ TOP-N
→ Telegram
```

**Outcome:** the application was validated as an integrated system.

---

# Client Use

## Milestone 46 — Working Client Solution Launched

The system was run and used as an applied client solution.

**Outcome:** the project moved beyond an isolated embedding experiment.

---

# Production MVP Boundary

## Milestone 47 — Three Functional Layers Confirmed

The system contained:

```text
Data Pipeline
+
Search Engine
+
Application Interface
```

**Outcome:** the project met the functional definition of an applied MVP.

---

## Milestone 48 — Runtime Boundary Kept Explicit

Available technical evidence showed Google Colab as the demonstrated execution environment.

**Outcome:** the public case does not invent permanent server infrastructure.

---

## Milestone 49 — 24/7 Hosting Not Claimed

The case deliberately does not claim:

```text
VPS
Docker
High Availability
Formal SLA
24/7 Runtime
Production Observability
```

without implementation evidence.

**Outcome:** maturity claims remain evidence-based.

---

# Search Quality Analysis

## Milestone 50 — Whole-Resume Embedding Limitation Identified

The project recognized that embedding a long resume into one vector may dilute highly important signals.

**Outcome:** section-level semantic representations became a logical future improvement.

---

## Milestone 51 — Mandatory Requirement Limitation Identified

Semantic similarity was recognized as insufficient for enforcing every hard requirement.

**Outcome:** future hybrid search can combine retrieval with deterministic filters.

---

## Milestone 52 — Personal-Data Influence Analyzed

The project considered the risk that irrelevant personal characteristics could influence semantic similarity.

**Outcome:** removing unnecessary personal information became an important production-hardening direction.

---

# Responsible AI

## Milestone 53 — Human Decision Boundary Maintained

The system was positioned as:

```text
Candidate Retrieval
+
Ranking
+
Shortlist
```

rather than:

```text
Automatic Hiring
```

**Outcome:** employment decisions remained with human professionals.

---

## Milestone 54 — Job-Relevant Ranking Principle Established

Professional relevance should be prioritized over unrelated personal characteristics.

**Outcome:** the design direction became more appropriate for responsible HR automation.

---

# Production Evolution

## Milestone 55 — Persistent Hosting Identified as Next Step

A permanently available commercial version would require migration from a temporary notebook runtime to persistent infrastructure.

**Outcome:** deployment requirements were separated from the completed MVP.

---

## Milestone 56 — Persistent Vector Index Identified

Future infrastructure can preserve or incrementally update the candidate vector index.

**Outcome:** application restarts would not necessarily require complete re-indexing.

---

## Milestone 57 — Incremental Data Update Path Identified

New and updated candidate records can be processed independently.

Conceptually:

```text
New Resume
→ Validate
→ Normalize
→ Embed
→ Update Index
```

**Outcome:** the architecture gained a clear path toward continuously updated candidate pools.

---

## Milestone 58 — Hybrid Retrieval Identified

A future version can combine:

```text
Vector Search
+
Structured Filters
+
Hard Requirements
+
Ranking
```

**Outcome:** semantic relevance and deterministic constraints can complement one another.

---

## Milestone 59 — Section-Level Embeddings Identified

Resume sections such as skills, role and experience can be embedded separately.

**Outcome:** ranking can become more granular and interpretable.

---

## Milestone 60 — Explainable Matching Identified

A future interface can explain why a candidate appeared in the shortlist.

Conceptually:

```text
Candidate
→ Relevant Skills
→ Relevant Experience
→ Matching Requirement
```

**Outcome:** AI-assisted recruitment can become easier to audit.

---

# Privacy

## Milestone 61 — Candidate Data Classified as Sensitive

Resume information was recognized as personal business data.

**Outcome:** candidate data should remain outside the public portfolio.

---

## Milestone 62 — Public Case Sanitized

Public documentation excludes:

- real resumes;
- candidate identities;
- contact details;
- private HR data;
- client datasets;
- Telegram tokens;
- exact private ranking configuration.

**Outcome:** technical competence can be demonstrated without exposing candidate information.

---

# Evidence Discipline

## Milestone 63 — Unsupported Infrastructure Claims Excluded

The project does not claim infrastructure that was not independently demonstrated.

**Outcome:** the public case remains aligned with actual implementation evidence.

---

## Milestone 64 — Unsupported Performance Metrics Excluded

The project does not invent:

- recruiter-hours saved;
- hiring conversion;
- matching accuracy percentage;
- ROI;
- production SLA;
- number of successful hires.

**Outcome:** the portfolio separates working functionality from unmeasured business impact.

---

# Final Result

## Milestone 65 — Complete Candidate Retrieval System Delivered

The system connected:

```text
Candidate Database
+
Data Preparation
+
RuBERT Embeddings
+
FAISS
+
Semantic Retrieval
+
Multi-Factor Ranking
+
Telegram
```

**Outcome:** an ordinary resume database became an AI-assisted retrieval tool.

---

# Current Status

## Milestone 66 — Working Client Production MVP

**Working client production MVP / end-to-end tested candidate retrieval and ranking system.**

Confirmed implementation includes:

```text
Excel
→ Pandas
→ Candidate Documents
→ Embeddings
→ FAISS
→ Semantic Search
→ Ranking
→ TOP-N
→ Telegram
→ Human Review
```

---

# Architectural Result

The reusable engineering pattern is:

```text
Structured Domain Data
+
Semantic Representation
+
Vector Search
+
Ranking
+
User Interface
+
Human Decision
=
Applied AI Retrieval System
```

---

# Key Engineering Lesson

The most important conclusion from the project is:

> **The useful product is not the vector database itself. It is the complete retrieval workflow around it.**

That workflow includes:

```text
Data Preparation
→ Representation
→ Retrieval
→ Ranking
→ Source Mapping
→ User Interface
→ Human Review
```

---

# Public Disclosure Policy

## Publicly Shared

- project purpose;
- sanitized architecture;
- embedding / vector-search pattern;
- ranking concept;
- Telegram workflow;
- production-MVP boundaries;
- development milestones;
- responsible-AI considerations;
- technology categories.

## Kept Private

- real candidate resumes;
- names;
- contact information;
- private personal attributes;
- client datasets;
- Telegram credentials;
- internal access details;
- exact private ranking weights;
- client-specific rules;
- production credentials;
- private runtime configuration.

---

# AIAQ Lab

**AI and business-process automation focused on practical, measurable operational improvements.**

**Website:** [https://aiaqlab.com/](https://aiaqlab.com/)

**Telegram channel:** [https://t.me/ai_b2b_automation](https://t.me/ai_b2b_automation)

**Projects & consulting:** [https://t.me/ai_arch_pro](https://t.me/ai_arch_pro)

**Email:** [ai@aiaqlab.com](mailto:ai@aiaqlab.com)

**GitHub:** [https://github.com/ai-b2b-automation](https://github.com/ai-b2b-automation)
