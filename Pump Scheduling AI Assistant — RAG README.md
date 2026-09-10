# AI-Powered Pump Scheduling Assistant – RAG

## Overview

The **Pump Scheduling System** is an enterprise application used to monitor pumps and assist operators with pump scheduling based on operational constraints, pump capabilities, maintenance information and pipeline limitations.

As the system evolved, a large amount of operational information was maintained in documents such as:

- Pump operating manuals
- Pump specifications
- Scheduling guidelines
- Maintenance procedures
- Pipeline operating constraints
- Safety guidelines
- Operational rules

Operators previously had to manually search through these documents to find the required information.

To improve this experience, we implemented an **AI-Powered Pump Scheduling Assistant using Retrieval-Augmented Generation (RAG)**.

The assistant allows users to ask questions in natural language and receive answers based on the organization's pump-related documentation.

---

# Business Problem

Operators frequently needed answers to questions such as:

- What is the maximum operating flow of Pump P-102?
- What are the operating constraints for P-105?
- Can P-102 operate during the maintenance window?
- Why can't P-102 and P-105 operate simultaneously?
- What is the minimum operating flow for a particular pump?
- What are the scheduling restrictions for a specific pipeline?

The information required to answer these questions was distributed across multiple documents.

Using a traditional keyword-based search was not always effective because users could ask the same question in different ways.

For example:

```text
"What is the maximum flow of P-102?"

"How much flow can P-102 handle?"

"What is the upper flow limit for P-102?"
```

All three questions refer to the same concept.

This was the primary reason for introducing semantic search and RAG.

---

# Solution

We implemented an AI assistant using **Retrieval-Augmented Generation (RAG)**.

The high-level flow is:

```text
User Question
      |
      v
ASP.NET Core API
      |
      v
Generate Query Embedding
      |
      v
Vector Similarity Search
      |
      v
Retrieve Relevant Document Chunks
      |
      v
Build Prompt with Context
      |
      v
Gemini LLM
      |
      v
Grounded AI Response
```

The LLM does not directly access all enterprise documents.

Instead, the application retrieves the most relevant information and provides it to the LLM as context.

---

# Technology Stack

| Component | Technology |
|---|---|
| Backend | ASP.NET Core Web API |
| Language | C# |
| LLM | Google Gemini |
| Embeddings | Gemini Embedding Model |
| Vector Database | PostgreSQL |
| Vector Extension | pgvector |
| ORM | Entity Framework Core |
| API Documentation | Swagger |
| Authentication | Existing application authentication |
| Frontend | Angular / React |
| Existing Database | Application relational database |

---

# Why RAG?

The Gemini model has general knowledge, but it does not automatically know our organization's internal pump specifications, scheduling rules or operational documentation.

We therefore used RAG instead of relying only on the LLM.

RAG combines:

```text
Enterprise Knowledge
        +
Semantic Retrieval
        +
Large Language Model
        =
Grounded AI Response
```

This allows the application to use internal and application-specific information without fine-tuning the LLM.

---

# RAG Architecture

```text
                    Pump Scheduling System
                             |
                             |
                    AI Assistant API
                             |
                             v
                    RAG Service Layer
                             |
              +--------------+--------------+
              |                             |
              v                             v
       Embedding Service              Vector Store
              |                     PostgreSQL + pgvector
              |                             |
              +--------------+--------------+
                             |
                             v
                    Relevant Context
                             |
                             v
                       Prompt Builder
                             |
                             v
                        Gemini LLM
                             |
                             v
                     AI Generated Answer
```

---

# Document Ingestion Pipeline

Before users can ask questions, the application needs to process the pump-related documents.

The ingestion pipeline is:

```text
PDF / DOCX / Text Document
          |
          v
     Text Extraction
          |
          v
        Chunking
          |
          v
   Generate Embeddings
          |
          v
 PostgreSQL + pgvector
```

## Step 1 – Document Upload

Operational documents are collected from the approved application knowledge source.

Example:

```text
Pump-P102-Manual.pdf
Pump-P105-Manual.pdf
Pump-Scheduling-Rules.pdf
Pipeline-Constraints.pdf
Pump-Maintenance-Guidelines.pdf
Safety-Guidelines.pdf
```

---

# Step 2 – Text Extraction

The text is extracted from the documents.

For example:

```text
Pump ID: P-102

Pump Type: Crude Oil Transfer Pump

Maximum Flow Rate: 500 m3/hr

Minimum Flow Rate: 150 m3/hr

Maximum Operating Temperature: 80°C

Maintenance Interval: 500 operating hours.

P-102 should not operate simultaneously with P-105
because both pumps share the same pipeline capacity.
```

---

# Step 3 – Document Chunking

Large documents are divided into smaller meaningful sections called **chunks**.

For example:

```text
Chunk 1:
Pump P-102 maximum flow rate is 500 m3/hr.

Chunk 2:
Pump P-102 minimum flow rate is 150 m3/hr.

Chunk 3:
P-102 and P-105 should not operate simultaneously
because they share the same pipeline capacity.

Chunk 4:
P-102 requires maintenance every 500 operating hours.
```

Chunking helps the retrieval system find the most relevant information instead of passing the entire document to the LLM.

---

# Step 4 – Generate Embeddings

Each document chunk is converted into an embedding.

An embedding is a numerical representation of the semantic meaning of the text.

For example:

```text
"P-102 maximum flow rate is 500 m3/hr"
```

is converted into a vector representation similar to:

```text
[0.021, -0.143, 0.287, 0.091, ...]
```

The actual vector contains many dimensions depending on the embedding model.

The important point is that semantically similar text produces vectors that are close to each other in vector space.

---

# Step 5 – Store Embeddings

We use:

**PostgreSQL + pgvector**

as the vector store.

Conceptually, the data looks like:

```text
DocumentChunks
------------------------------------------------
Id
DocumentName
ChunkText
Embedding
Metadata
CreatedDate
```

The `Embedding` column stores the vector representation of the chunk.

Example:

```text
DocumentName:
Pump-P102-Manual.pdf

ChunkText:
P-102 maximum flow rate is 500 m3/hr.

Embedding:
[0.021, -0.143, 0.287, ...]
```

---

# Query Processing

Once the documents are indexed, users can ask questions through the AI Assistant.

Example:

```text
Why can't P-102 and P-105 operate together?
```

The application processes the question through the following flow:

```text
User Question
      |
      v
Generate Question Embedding
      |
      v
Vector Similarity Search
      |
      v
Retrieve Top Relevant Chunks
      |
      v
Create Context
      |
      v
Send Context + Question to Gemini
      |
      v
Generate Answer
```

---

# Step 1 – Generate Query Embedding

The user's question is converted into an embedding using the same embedding approach used during document ingestion.

Example:

```text
Why can't P-102 and P-105 operate together?
```

becomes a vector representation.

---

# Step 2 – Vector Similarity Search

The generated query vector is compared with the vectors stored in PostgreSQL using pgvector.

The system retrieves the most semantically relevant chunks.

For example:

```text
Result 1:
P-102 should not operate simultaneously with P-105
because both pumps share the same pipeline capacity.

Result 2:
Pipeline PL-01 has a maximum capacity of 700 m3/hr.

Result 3:
P-102 is connected to pipeline PL-01.
```

The application selects the top relevant results.

---

# Step 3 – Build Context

The retrieved chunks are combined into a context section.

Example:

```text
Context:

P-102 should not operate simultaneously with P-105
because both pumps share the same pipeline capacity.

Pipeline PL-01 has a maximum capacity of 700 m3/hr.

P-102 is connected to pipeline PL-01.
```

---

# Step 4 – Prompt Construction

The application creates a controlled prompt for Gemini.

Example:

```text
You are an AI assistant for the Pump Scheduling System.

Answer the user's question using only the provided context.

If the information is not available in the context,
do not make assumptions.

Context:

P-102 should not operate simultaneously with P-105
because both pumps share the same pipeline capacity.

Pipeline PL-01 has a maximum capacity of 700 m3/hr.

P-102 is connected to pipeline PL-01.

Question:

Why can't P-102 and P-105 operate together?
```

---

# Step 5 – Gemini Response

Gemini generates a grounded response.

Example:

```text
P-102 and P-105 should not operate simultaneously because
both pumps share the same pipeline capacity.

Operating both pumps together could exceed the available
pipeline capacity and violate the defined operating constraint.
```

---

# API

The AI assistant is exposed through an ASP.NET Core Web API.

## Endpoint

```http
POST /api/ai/ask
```

## Request

```json
{
  "question": "Why can't P-102 and P-105 operate together?"
}
```

## Response

```json
{
  "answer": "P-102 and P-105 should not operate simultaneously because they share the same pipeline capacity.",
  "sources": [
    "Pump-P102-Manual.pdf",
    "Pipeline-Constraints.pdf"
  ]
}
```

---

# Source References

The response can optionally include the documents or chunks used to generate the answer.

Example:

```json
{
  "answer": "P-102 and P-105 should not operate simultaneously because they share the same pipeline capacity.",
  "sources": [
    {
      "document": "Pump-P102-Manual.pdf",
      "chunk": "P-102 should not operate simultaneously with P-105."
    },
    {
      "document": "Pipeline-Constraints.pdf",
      "chunk": "Pipeline PL-01 has a maximum capacity of 700 m3/hr."
    }
  ]
}
```

This improves transparency and allows users to verify the information.

---

# Hallucination Control

One of the important concerns with Generative AI is hallucination.

We therefore added instructions to the prompt such as:

```text
Use only the supplied context to answer the question.

Do not make assumptions.

If the required information is not available,
respond that the information is not available.
```

This does not completely eliminate hallucinations, but it significantly improves grounding when combined with good retrieval.

---

# RAG vs Traditional Search

Traditional search primarily depends on matching keywords.

For example:

```text
Maximum flow P-102
```

RAG uses semantic similarity.

Therefore, questions such as:

```text
What is the maximum flow of P-102?

How much flow can P-102 handle?

What is the upper operating limit of P-102?
```

can retrieve similar information even though the exact words are different.

---

# Why PostgreSQL + pgvector?

The existing application already uses a relational database.

Using PostgreSQL with pgvector provided several advantages:

- Reuse of existing database infrastructure
- Simple integration with the .NET application
- Support for vector similarity search
- Ability to store metadata alongside embeddings
- Reduced infrastructure complexity
- Easy integration with Entity Framework Core

For a larger enterprise deployment, a dedicated managed search platform such as Azure AI Search could also be considered depending on scale and search requirements.

---

# Security Considerations

The AI layer should not expose unrestricted enterprise information.

Important considerations include:

- Authentication and authorization
- Document-level access control
- Avoiding sensitive information in prompts
- API key protection
- Input validation
- Prompt injection protection
- Logging and monitoring
- Limiting retrieved content
- Preventing unauthorized document access

The application should ensure that a user only receives information they are authorized to access.

---

# Important Design Decision

The LLM is **not responsible for making the final pump scheduling decision**.

The existing scheduling engine continues to handle deterministic business rules.

For example:

```text
AI Assistant
     |
     v
Understand user question
     |
     v
Retrieve relevant information
     |
     v
Explain operational constraints
     |
     v
Existing Scheduling Engine
     |
     v
Validate actual scheduling rules
```

This separation is important because pump scheduling can involve operational and safety-related constraints.

The LLM is therefore used for:

- Natural-language interaction
- Knowledge retrieval
- Information summarization
- Explanation

while deterministic application code remains responsible for:

- Schedule validation
- Pump constraints
- Capacity validation
- Maintenance validation
- Business rules

---

# Example User Scenarios

## Scenario 1 – Pump Operating Limit

### User

```text
What is the maximum flow rate of P-102?
```

### RAG retrieves

```text
P-102 maximum flow rate is 500 m3/hr.
```

### AI

```text
The maximum operating flow rate of P-102 is 500 m3/hr.
```

---

## Scenario 2 – Pump Compatibility

### User

```text
Why can't P-102 and P-105 operate together?
```

### RAG retrieves

```text
P-102 and P-105 share the same pipeline capacity.
```

### AI

```text
P-102 and P-105 cannot operate simultaneously because
they share the same pipeline capacity.
```

---

## Scenario 3 – Maintenance

### User

```text
Can P-102 be scheduled during its maintenance window?
```

### RAG retrieves

```text
P-102 must not be included in the scheduling plan
during maintenance mode.
```

### AI

```text
No. P-102 should not be scheduled during its maintenance
window according to the operating guidelines.
```

---

# RAG Components

The implementation can be divided into the following services:

```text
AI Assistant API
        |
        +-- DocumentService
        |
        +-- TextExtractionService
        |
        +-- ChunkingService
        |
        +-- EmbeddingService
        |
        +-- VectorSearchService
        |
        +-- PromptBuilder
        |
        +-- GeminiService
        |
        +-- RAGService
```

The main orchestration happens inside the `RAGService`.

Conceptually:

```csharp
public async Task<string> AskAsync(string question)
{
    var queryEmbedding =
        await embeddingService.GenerateAsync(question);

    var chunks =
        await vectorSearchService.SearchAsync(queryEmbedding);

    var prompt =
        promptBuilder.Build(question, chunks);

    return await geminiService.GenerateAsync(prompt);
}
```

---

# End-to-End Flow

The complete flow is:

```text
                    DOCUMENT INGESTION

PDF / DOCX
    |
    v
Text Extraction
    |
    v
Chunking
    |
    v
Embedding Generation
    |
    v
PostgreSQL + pgvector
```

Then:

```text
                    USER QUERY

Operator
    |
    | "Why can't P-102 and P-105 run together?"
    v
ASP.NET Core API
    |
    v
Query Embedding
    |
    v
pgvector Similarity Search
    |
    v
Top Relevant Chunks
    |
    v
Prompt Builder
    |
    v
Gemini
    |
    v
Grounded Response
    |
    v
Operator
```

---

# Future Enhancement – Agentic AI

The next evolution of the solution is to introduce an AI Agent.

The current RAG solution primarily performs:

```text
Retrieve → Augment → Generate
```

An agent can additionally use application tools.

For example:

```text
                    AI Agent
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
 GetPumpDetails   GetSchedule   GetMaintenance
        |              |              |
        +--------------+--------------+
                       |
                       v
                Validate Rules
                       |
                       v
                     Gemini
                       |
                       v
                Natural Language
                   Response
```

Example:

```text
User:

Can I schedule P-102 tomorrow at 10 AM?
```

The agent could:

1. Retrieve pump details
2. Retrieve the current schedule
3. Check maintenance information
4. Retrieve relevant operational rules using RAG
5. Validate scheduling constraints
6. Provide an explanation to the operator

The actual scheduling operation would still be performed by the existing application business logic.

---

# RAG vs Agent

| Capability | RAG | Agent |
|---|---|---|
| Retrieve documents | Yes | Yes |
| Semantic search | Yes | Yes |
| Generate answers | Yes | Yes |
| Call application APIs | No / Limited | Yes |
| Execute multiple steps | Limited | Yes |
| Tool calling | No | Yes |
| Decision orchestration | No | Yes |

Therefore:

```text
GenAI
   ↓
RAG
   ↓
Tool Calling
   ↓
Agentic AI
```

---

# Key Benefits

The RAG implementation provides:

- Natural-language access to pump documentation
- Faster access to operational information
- Semantic search instead of keyword-only search
- Reduced manual document searching
- Grounded responses using enterprise information
- Source references for better transparency
- Integration with the existing .NET application
- No requirement to fine-tune the LLM
- Ability to update knowledge by updating documents and embeddings

---

# Key Interview Explanation

The implementation can be summarized as:

> "We introduced a RAG-based AI assistant into the Pump Scheduling System to help operators access pump-related operational knowledge using natural language. We processed pump documentation into chunks, generated embeddings and stored them in PostgreSQL using pgvector. When a user asks a question, we generate an embedding for the query, perform vector similarity search to retrieve the most relevant chunks, and provide those chunks as context to Gemini. Gemini then generates a grounded response. We kept the existing scheduling engine and business rules separate from the LLM, so the AI layer was responsible for retrieval and explanation while the deterministic .NET application remained responsible for actual scheduling validation."

---

# Conclusion

The RAG implementation provides an AI-powered knowledge layer on top of the existing Pump Scheduling System.

Instead of replacing the existing application logic, the AI capability works alongside it:

```text
Existing .NET Application
          +
Enterprise Knowledge
          +
RAG
          +
Gemini
          =
AI-Powered Pump Scheduling Assistant
```

The solution can subsequently be extended with tool calling and Agentic AI to allow the assistant to interact with existing application APIs and perform multi-step operational workflows.