# GenAI Fundamentals

## Beginner Notes for .NET Full Stack Developers

This document explains the basic Generative AI concepts required to start building a **GenAI-powered API Test Case Generator**.

The explanations are intentionally kept simple and practical for a .NET developer.

---

# Table of Contents

1. [What is Generative AI?](#1-what-is-generative-ai)
2. [What is an LLM?](#2-what-is-an-llm)
3. [What are Tokens?](#3-what-are-tokens)
4. [What is Context Window?](#4-what-is-context-window)
5. [What is Temperature?](#5-what-is-temperature)
6. [What is Hallucination?](#6-what-is-hallucination)
7. [What is Prompt Engineering?](#7-what-is-prompt-engineering)
8. [What is an Embedding?](#8-what-is-an-embedding)
9. [How These Concepts Work Together](#9-how-these-concepts-work-together)
10. [Interview Quick Revision](#10-interview-quick-revision)

---

# 1. What is Generative AI?

## Simple Definition

**Generative AI is a type of Artificial Intelligence that can create new content based on the instructions or input provided by the user.**

It can generate:

- Text
- Code
- Images
- Audio
- Video
- SQL queries
- Test cases
- Documentation
- Summaries
- Emails

## Example

Suppose we provide this prompt:

```text
Create unit test cases for this .NET Core API.
```

The AI can generate something like:

```csharp
[Fact]
public async Task GetUser_ShouldReturnUser_WhenUserExists()
{
    // Arrange

    // Act

    // Assert
}
```

This is called **Generative AI** because the AI is generating new content.

---

## Traditional Software vs Generative AI

### Traditional Software

```text
Input
  |
  v
Business Logic
  |
  v
Output
```

Example:

```text
GET /api/users/10
       |
       v
  UserService
       |
       v
   Database
       |
       v
   User JSON
```

The developer explicitly defines the business logic.

---

### Generative AI

```text
Prompt / Input
      |
      v
   AI Model
      |
      v
Generated Content
```

Example:

```text
"Create test cases for User API"
             |
             v
          AI Model
             |
             v
Test Cases + Edge Cases + Code
```

---

## Example for Our API Test Case Generator

Suppose we have:

```text
POST /api/users
```

Request:

```json
{
  "name": "John",
  "email": "john@test.com",
  "age": 30
}
```

We can ask GenAI:

```text
Generate test cases for this API.
```

The AI may generate:

```text
1. Valid user creation
2. Missing name
3. Missing email
4. Invalid email
5. Duplicate email
6. Negative age
7. Age below allowed limit
8. Very long name
9. Special characters in name
10. Missing request body
```

---

## Interview Definition

> Generative AI is a type of AI that can generate new content such as text, code, images, SQL queries, and test cases based on user instructions or input.

---

# 2. What is an LLM?

## Full Form

**LLM = Large Language Model**

Examples include:

- GPT
- Gemini
- Claude
- Llama

---

## Simple Definition

> **An LLM is a large AI model trained on huge amounts of data that can understand and generate human-like text and code.**

Think of an LLM as the **brain behind many Generative AI applications**.

---

## Example

We ask:

```text
What is the capital of India?
```

The LLM generates:

```text
New Delhi
```

Or:

```text
Create a C# method to add two numbers.
```

The LLM may generate:

```csharp
public int Add(int a, int b)
{
    return a + b;
}
```

---

## LLM from a .NET Developer's Perspective

As a .NET developer, think about an LLM as an external service that your application communicates with through an API.

```text
Your .NET Application
        |
        | Prompt
        v
     LLM API
        |
        | Response
        v
Your .NET Application
```

For our project:

```text
.NET Web API
      |
      v
Gemini / GPT / Claude
      |
      v
Generated Test Cases
```

This is similar to consuming other external APIs.

For example:

```text
Your Application
      |
      v
Payment API
      |
      v
Payment Response
```

Similarly:

```text
Your Application
      |
      v
LLM API
      |
      v
AI Response
```

---

## Why is it called "Large"?

It is called a **Large Language Model** because the model is trained using enormous amounts of data and contains a very large number of learned parameters.

For interviews, you don't initially need to understand the mathematical details.

---

## Interview Definition

> LLM stands for Large Language Model. It is an AI model trained on a huge amount of data that can understand and generate human-like text and code. Examples include GPT, Gemini, Claude, and Llama.

---

# 3. What are Tokens?

## Simple Definition

> **Tokens are the small pieces of text that an LLM processes.**

An LLM does not process text exactly the same way humans read it.

The text is broken into smaller pieces called **tokens**.

---

## Example

Suppose we provide:

```text
I love programming
```

The tokenizer may represent it approximately as:

```text
"I"
" love"
" programming"
```

These pieces are called tokens.

> The exact tokenization depends on the model and tokenizer.

---

## Token Does NOT Always Mean One Word

A token can be:

- A complete word
- Part of a word
- Punctuation
- A number
- A special text sequence

Therefore:

```text
1 word != necessarily 1 token
```

---

## Why are Tokens Important?

Tokens are important mainly for three reasons.

### 1. API Cost

Many LLM APIs calculate cost based on the number of tokens processed.

Conceptually:

```text
Input Tokens
+
Output Tokens
=
Total Tokens
```

More tokens can mean higher cost.

---

### 2. Context Window

An LLM can process only a certain maximum number of tokens at a time.

This maximum capacity is called the **Context Window**.

---

### 3. Performance

Sending unnecessary information increases the number of tokens.

For example:

```text
100-page document
+
50-page documentation
+
Large source code
+
Previous conversation
```

can consume a large number of tokens.

Therefore, GenAI applications try to provide only relevant information to the model.

This becomes very important when learning **RAG (Retrieval-Augmented Generation)**.

---

## Example for Our Test Case Generator

Suppose we send:

```text
Generate test cases for this API.

POST /api/users

Request:
{
  "name": "John",
  "email": "john@test.com"
}
```

All of this text is converted into tokens before being processed by the model.

The generated response is also represented as tokens.

---

## Interview Definition

> Tokens are the small pieces of text that an LLM processes. A token can represent a complete word, part of a word, punctuation, or another text element. Tokens are important because they affect processing limits, context window, and API cost.

---

# 4. What is Context Window?

## Simple Definition

> **Context Window is the maximum amount of information, measured in tokens, that an LLM can process and consider at one time.**

Think of it as the model's **working space**.

---

## Simple Real-Life Analogy

Imagine you have a notebook.

The notebook has limited space.

You can write only a certain amount of information in it.

Similarly, an LLM has a maximum amount of information it can process within one context.

---

## Context in a Conversation

Consider:

```text
User:
My name is Nikhil.

AI:
Nice to meet you Nikhil.

User:
What is my name?

AI:
Your name is Nikhil.
```

The AI needs the previous information:

```text
My name is Nikhil
```

to answer the question.

That previous information is part of the **context**.

---

## Context in Our API Test Case Generator

The context can contain:

```text
System Instructions
        +
User Prompt
        +
API Definition
        +
Validation Rules
        +
Conversation History
        +
Retrieved Documentation
```

All this information consumes tokens from the context window.

---

## Example

Suppose, just for understanding, a model has a context window of:

```text
100 tokens
```

If we send:

```text
80 tokens
```

then only limited space remains for the response.

If we attempt to send:

```text
500 tokens
```

we exceed the model's context capacity.

> Modern LLMs can support much larger context windows. The numbers above are only for understanding the concept.

---

## Context Window vs Memory

These are not the same thing.

### Context Window

Means:

> Information available to the model during the current processing context.

It can include:

```text
Prompt
+
Conversation
+
Instructions
+
Documents
+
Retrieved Information
```

### Memory

Memory refers to information that may be retained or provided across interactions, depending on the AI application's architecture and configuration.

Therefore:

```text
Context Window != Permanent Memory
```

---

## Interview Definition

> Context window is the maximum amount of information, measured in tokens, that an LLM can process and consider at one time. It can include the prompt, conversation history, instructions, documents, and other relevant information.

---

# 5. What is Temperature?

## Simple Definition

> **Temperature controls how predictable or creative the AI's response should be.**

Think of temperature as a **creativity/randomness setting**.

```text
Low Temperature
      |
      v
More predictable response
```

```text
High Temperature
      |
      v
More varied / creative response
```

---

## Example for Our Test Case Generator

Suppose we ask:

```text
Generate test cases for:

POST /api/users

Request:
{
  "name": "John",
  "email": "john@test.com",
  "age": 30
}
```

### Lower Temperature

The response may be more predictable:

```text
1. Valid user creation
2. Missing name
3. Invalid email
4. Negative age
5. Duplicate email
```

Running the same prompt may produce relatively consistent results.

---

### Higher Temperature

The model may produce more varied test cases:

```text
1. Valid user creation
2. Empty name
3. Special characters in name
4. Invalid email format
5. Duplicate email
6. Very large age value
7. Age = 0
8. Extremely long email
9. Missing Content-Type header
10. Invalid JSON
```

---

## Which is better for Our POC?

For an **API Test Case Generator**, we generally want controlled and predictable output.

Therefore, we would typically use a **low or moderate temperature**, depending on the model and desired behavior.

---

## Important Note

Temperature does **not** mean:

```text
Low = bad
High = good
```

It depends on the use case.

For example:

```text
Test Case Generation → More controlled
Creative Writing      → More creative
```

---

## Interview Definition

> Temperature is a parameter that controls the randomness or creativity of an LLM's output. Lower temperature generally produces more predictable responses, while higher temperature can produce more diverse responses.

---

# 6. What is Hallucination?

## Simple Definition

> **Hallucination happens when an AI generates an answer that sounds correct but is actually incorrect, unsupported, or invented.**

In simple words:

> **AI confidently makes something up.**

---

## Example for Our API Test Case Generator

Suppose our actual API is:

```text
POST /api/users
```

And it accepts:

```json
{
  "name": "John",
  "email": "john@test.com"
}
```

But the AI generates:

```text
Test Case:
Verify that the user is created with a unique phone number.
```

The problem is:

```text
phoneNumber
```

does not exist in our API.

The AI has invented information.

That is a **hallucination**.

---

## Another Example

Suppose our actual API returns:

```text
200 OK
```

But the AI says:

```text
Expected Response:
201 Created
```

If the AI was not given the actual API behavior and simply assumed `201`, this can be a hallucination.

---

# Why Does Hallucination Happen?

An LLM is not simply querying a traditional database of facts.

It generates responses based on patterns learned during training and the context provided to it.

Therefore:

```text
Plausible Answer
       !=
Always Correct Answer
```

---

# How Can We Reduce Hallucination?

For our Test Case Generator, we should provide accurate context.

Instead of:

```text
Generate test cases for User API.
```

provide:

```text
Endpoint:
POST /api/users

Request:
{
  "name": "string",
  "email": "string"
}

Validation Rules:
- name is required
- email is required
- email must be valid
- email must be unique

Success Response:
200 OK

Error Responses:
400 Bad Request
409 Conflict
```

Now the model has much better information.

Later, **RAG** can help retrieve relevant API documentation automatically.

---

## Additional Protection

We can reduce hallucinations using:

- Clear prompts
- Accurate context
- RAG
- Structured output
- Output validation
- Business-rule validation
- Schema validation
- Grounding the model with trusted data

---

## Interview Definition

> Hallucination is when an LLM generates information that appears correct but is factually incorrect, unsupported, or not present in the provided context. It can be reduced using accurate context, good prompting, RAG, structured output, and validation.

---

# 7. What is Prompt Engineering?

## Simple Definition

> **Prompt engineering means designing clear and effective instructions for an AI model so that it produces the desired output.**

In simple words:

> **It means learning how to ask the AI properly.**

---

# Bad Prompt

For our application:

```text
Generate test cases.
```

The AI does not know:

- Which API?
- What request?
- What validation rules?
- How many test cases?
- Positive or negative?
- Should edge cases be included?
- What output format should be used?

---

# Better Prompt

```text
Generate test cases for the following API.

Endpoint:
POST /api/users

Request:
{
  "name": "John",
  "email": "john@test.com",
  "age": 30
}

Validation Rules:
- Name is required
- Email is required
- Email must be valid
- Age must be greater than 18

Generate:

1. Positive test cases
2. Negative test cases
3. Boundary test cases
4. Edge cases

Return the result in JSON format.
```

This is a much better prompt.

---

# Even Better Prompt for Our POC

We can define the role:

```text
You are an expert API QA engineer.
```

Provide the API:

```text
API:
POST /api/users
```

Provide the rules:

```text
Validation:
- Name is required
- Email is required
- Age must be greater than 18
```

Specify the task:

```text
Generate positive, negative, boundary and security test cases.
```

Specify the output format:

```text
Return JSON with:

{
  "testCaseId": "",
  "title": "",
  "type": "",
  "input": "",
  "expectedResult": ""
}
```

This is **Prompt Engineering**.

---

# Prompt Engineering from a .NET Developer Perspective

You already know that a method works better when the input contract is clear.

For example:

```csharp
public TestCaseResult GenerateTestCases(
    ApiDefinition api,
    ValidationRules rules,
    TestCaseOptions options)
```

This is better than:

```csharp
GenerateTestCases();
```

because the method receives clear information.

Similarly:

```text
Poor Prompt
    |
    v
Poor / Unpredictable Output
```

Whereas:

```text
Clear Prompt
+
Relevant Context
+
Expected Output Format
    |
    v
Better Output
```

---

## Interview Definition

> Prompt engineering is the process of designing clear, structured, and specific instructions for an LLM to get more accurate, consistent, and useful responses.

---

# 8. What is an Embedding?

This is an important concept because embeddings are the foundation of **semantic search, Vector Databases, and RAG**.

---

## Simple Definition

> **An embedding is a numerical representation of text that captures its meaning.**

In very simple terms:

> **Embedding converts text into numbers so that a computer can compare the meaning of different pieces of text.**

---

## Example

Suppose we have:

```text
Text 1:
User email is required.
```

And:

```text
Text 2:
Email must be provided when creating a user.
```

Humans understand that these sentences have similar meaning.

An embedding model converts them into numerical vectors.

Conceptually:

```text
"User email is required."
          |
          v
    Embedding Model
          |
          v
[0.12, -0.45, 0.78, 0.21, ...]
```

And:

```text
"Email must be provided."
          |
          v
    Embedding Model
          |
          v
[0.14, -0.43, 0.76, 0.24, ...]
```

The vectors can be mathematically compared for similarity.

> The numbers above are only examples. Real embeddings typically contain many more dimensions.

---

# Why Do We Need Embeddings?

Computers are very good at comparing numbers.

Suppose our API documentation says:

```text
Email is mandatory for user registration.
```

The user asks:

```text
Do I need to provide an email?
```

The exact words are different, but the meaning is similar.

Embeddings help the system identify this semantic similarity.

---

# Embeddings in Our API Test Case Generator

Imagine our company has API documentation:

```text
User API Validation Rules

1. Name is mandatory.
2. Email is mandatory.
3. Email must be unique.
4. Age must be greater than 18.
```

We can convert the documentation into embeddings.

```text
API Documentation
       |
       v
Embedding Model
       |
       v
Vector Representation
       |
       v
Vector Database
```

Later, the user asks:

```text
Generate test cases for mandatory fields in User API.
```

The question can also be converted into an embedding.

```text
User Question
       |
       v
Embedding Model
       |
       v
Query Vector
```

The system searches the vector database for semantically similar information.

It may retrieve:

```text
Name is mandatory.
Email is mandatory.
```

Then that information is provided to the LLM.

```text
User Question
      +
Retrieved API Documentation
      |
      v
     LLM
      |
      v
Generated Test Cases
```

This is the basic idea behind **RAG**.

---

# Embedding vs Traditional Keyword Search

Suppose our documentation contains:

```text
Email is mandatory for user registration.
```

The user searches:

```text
Do I need to provide email?
```

A simple keyword search may not always perform well because the exact words don't match.

Embedding-based search focuses more on **meaning**.

Conceptually:

```text
"Email is mandatory"
        |
        | Similar Meaning
        v
"Do I need to provide email?"
```

This is called **semantic search**.

---

## Real-Life Analogy

Imagine you go to a library.

You don't remember the exact title of a book.

You tell the librarian:

> "I need a book about building web applications using Microsoft technologies."

The librarian understands the meaning of your request and finds relevant books.

Embedding-based search works somewhat similarly:

```text
Your Question
      |
      v
Meaning represented as numbers
      |
      v
Find similar meanings
      |
      v
Relevant Documents
```

---

## Interview Definition

> An embedding is a numerical representation of text that captures its semantic meaning. Embeddings are commonly used for semantic search, similarity matching, and RAG systems, where relevant information is retrieved from a vector database.

---

# 9. How These Concepts Work Together

Now let's connect everything to our **GenAI API Test Case Generator**.

---

## Overall Flow

```text
                         USER
                           |
                           |
             "Generate test cases"
                           |
                           v
                    .NET Core API
                           |
                           v
                  Prompt Engineering
                           |
                           v
                  Relevant API Context
                           |
                           v
                         LLM
                           |
                           v
                  Generated Test Cases
```

---

## Temperature

Controls how predictable or creative the response is.

```text
Temperature
     |
     v
Response Randomness
```

For test case generation, we generally want controlled output.

---

## Hallucination

The problem where AI can generate incorrect or invented information.

```text
Insufficient Context
       |
       v
      LLM
       |
       v
Potential Hallucination
```

---

## Prompt Engineering

Defines how we communicate with the AI.

```text
Clear Instructions
+
Relevant Context
+
Output Format
       |
       v
Better AI Response
```

---

## Embeddings

Convert text into numerical representations of meaning.

```text
API Documentation
       |
       v
Embedding Model
       |
       v
Vector
       |
       v
Vector Database
```

This later enables **RAG**.

---

# Complete Architecture

```text
                         USER
                           |
                           v
                  .NET Core Web API
                           |
                           v
                  Request / API Data
                           |
                           v
                  Prompt Engineering
                           |
                           +------------------+
                           |                  |
                           v                  v
                     User Context      API Documentation
                                              |
                                              v
                                      Embedding Model
                                              |
                                              v
                                       Vector Database
                                              |
                                              v
                                       Relevant Context
                                              |
                           +------------------+
                           |
                           v
                         LLM
                    (GPT / Gemini)
                           |
                           v
                  Generated Test Cases
                           |
                           v
                    Output Validation
                           |
                           v
                         USER
```

---

# 10. Interview Quick Revision

## Generative AI

**Remember:**

```text
AI that generates new content.
```

### Interview Answer

> Generative AI is a type of AI that can generate new content such as text, code, images, SQL queries, and test cases based on user instructions or input.

---

## LLM

**Remember:**

```text
The AI model / brain.
```

### Interview Answer

> LLM stands for Large Language Model. It is an AI model trained on a huge amount of data that can understand and generate human-like text and code.

---

## Token

**Remember:**

```text
Small piece of text processed by the model.
```

### Interview Answer

> Tokens are the small pieces of text that an LLM processes. They are important because they affect context limits, processing, and API cost.

---

## Context Window

**Remember:**

```text
The model's working space.
```

### Interview Answer

> Context window is the maximum amount of information, measured in tokens, that an LLM can process and consider at one time.

---

## Temperature

**Remember:**

```text
Creativity / randomness control.
```

### Interview Answer

> Temperature is a parameter that controls the randomness or creativity of an LLM's output. Lower values generally produce more predictable responses, while higher values can produce more diverse responses.

---

## Hallucination

**Remember:**

```text
AI making things up.
```

### Interview Answer

> Hallucination occurs when an LLM generates information that appears correct but is actually incorrect, unsupported, or invented.

---

## Prompt Engineering

**Remember:**

```text
Asking AI properly.
```

### Interview Answer

> Prompt engineering is the process of designing clear, structured, and specific instructions for an LLM to get more accurate and useful responses.

---

## Embedding

**Remember:**

```text
Meaning represented as numbers.
```

### Interview Answer

> An embedding is a numerical representation of text that captures its semantic meaning. It is commonly used for semantic search, similarity matching, and RAG.

---

# Final Memory Trick

Remember these eight concepts like this:

```text
GenAI
  ↓
Generate new content

LLM
  ↓
AI model / Brain

Token
  ↓
Piece of text

Context Window
  ↓
Working space

Temperature
  ↓
Creativity control

Hallucination
  ↓
AI making things up

Prompt Engineering
  ↓
Asking AI properly

Embedding
  ↓
Meaning converted into numbers
```

---

# What to Learn Next

After understanding these fundamentals, the recommended learning sequence for our **API Test Case Generator** is:

```text
1. GenAI Fundamentals
        |
        v
2. Prompt Engineering
        |
        v
3. Structured Output / JSON
        |
        v
4. Embeddings
        |
        v
5. Vector Database
        |
        v
6. RAG
        |
        v
7. Function Calling / Tool Calling
        |
        v
8. AI Agents
        |
        v
9. Build GenAI API Test Case Generator
```

The next major concept to understand is **RAG (Retrieval-Augmented Generation)** because it will allow our test case generator to use real API documentation, Swagger/OpenAPI definitions, validation rules, and project-specific information instead of relying only on the LLM's general knowledge.


# RAG Fundamentals

## Beginner Notes for .NET Full Stack Developers

This document explains the basic concepts of **RAG (Retrieval-Augmented Generation)** in simple language.

The examples are based on our planned project:

> **GenAI-powered API Test Case Generator for .NET Core APIs**

---

# Table of Contents

1. [What is RAG?](#1-what-is-rag)
2. [Why do we need RAG?](#2-why-do-we-need-rag)
3. [RAG vs Fine-Tuning](#3-rag-vs-fine-tuning)
4. [How does RAG work?](#4-how-does-rag-work)
5. [What is Chunking?](#5-what-is-chunking)
6. [Complete RAG Flow](#6-complete-rag-flow)
7. [RAG Example for API Test Case Generator](#7-rag-example-for-api-test-case-generator)
8. [Interview Questions and Answers](#8-interview-questions-and-answers)
9. [Quick Revision](#9-quick-revision)

---

# 1. What is RAG?

## Full Form

**RAG = Retrieval-Augmented Generation**

Don't worry about the complicated name.

In simple words:

> **RAG means: Before asking the LLM to generate an answer, first find the relevant information from your own data and give that information to the LLM.**

So instead of asking the AI:

```text
Generate test cases.
```

we do:

```text
Find relevant API information
        ↓
Give that information to the LLM
        ↓
Generate test cases
```

---

# Simple Real-Life Example

Imagine you have a company employee handbook.

You ask:

```text
What is our company's work-from-home policy?
```

Instead of expecting the AI to already know your company's policy, the system:

```text
Search Company Documents
        ↓
Find Work From Home Policy
        ↓
Give it to LLM
        ↓
Generate Answer
```

This is RAG.

---

# RAG in Our API Test Case Generator

Suppose our company has API documentation:

```text
User API Documentation

Endpoint:
POST /api/users

Validation Rules:
- Name is required
- Email is required
- Email must be unique
- Age must be greater than 18

Success Response:
200 OK

Error Responses:
400 Bad Request
409 Conflict
```

Now the user asks:

```text
Generate test cases for POST /api/users.
```

Instead of asking the LLM without context, our application first retrieves the relevant API documentation.

```text
User Question
      ↓
Search API Documentation
      ↓
Relevant Information
      ↓
LLM
      ↓
Test Cases
```

This is RAG.

---

# Simple Definition to Remember

```text
RAG = Retrieve relevant information + Generate answer
```

Or:

```text
Search First
     ↓
Give Context
     ↓
Ask AI
```

---

# 2. Why do we need RAG?

This is one of the most important questions.

## Problem with a normal LLM

An LLM has been trained on a huge amount of data.

But your company's private information may not be part of its training data.

For example:

```text
Your Company API Documentation
Your Internal Business Rules
Your Swagger Files
Your Database Documentation
Your Internal Processes
Your Product Rules
```

The LLM may not know these things.

---

# Example Without RAG

Suppose we ask:

```text
Generate test cases for our User API.
```

The LLM may generate generic test cases:

```text
1. Valid user
2. Invalid email
3. Missing name
4. Invalid age
```

These might be useful, but they may not match our actual API.

---

# Example With RAG

Suppose our actual API documentation says:

```text
POST /api/users

Rules:

- Name is required
- Email is required
- Email must be unique
- Age must be >= 18
- User status defaults to Active
- Duplicate email returns 409
```

RAG retrieves this information and provides it to the LLM.

Now the AI can generate:

```text
TC01:
Create user with valid name, email and age.

Expected:
200 OK

TC02:
Create user with duplicate email.

Expected:
409 Conflict

TC03:
Create user with age = 17.

Expected:
400 Bad Request

TC04:
Create user without name.

Expected:
400 Bad Request

TC05:
Verify default user status is Active.
```

These test cases are much more relevant to our actual application.

---

# Main Reasons We Need RAG

## 1. Use Private / Company Data

RAG allows the application to retrieve information from your own data sources.

Examples:

```text
Company Documentation
Swagger/OpenAPI
PDF
Word Documents
Database
Knowledge Base
Confluence
SharePoint
Internal APIs
```

---

## 2. Provide Up-to-Date Information

Suppose your API validation rule changes.

Old rule:

```text
Age >= 18
```

New rule:

```text
Age >= 21
```

If the latest documentation is stored in your knowledge base, RAG can retrieve the updated rule.

The LLM doesn't necessarily need to be retrained.

---

## 3. Reduce Hallucination

Without relevant context:

```text
LLM
 ↓
May Guess
 ↓
Potential Hallucination
```

With relevant context:

```text
Relevant Documentation
        ↓
      LLM
        ↓
Grounded Response
```

RAG doesn't guarantee zero hallucinations, but it can significantly improve grounding when retrieval and context are good.

---

## 4. Avoid Retraining the Model

If your company has:

```text
10,000 API documents
```

you don't necessarily need to train a new model every time the documentation changes.

Instead:

```text
Update Documents
      ↓
Update Knowledge Base
      ↓
RAG Retrieves Relevant Information
```

---

# Simple Analogy

Think about an exam.

### Without RAG

You ask a student:

> "Answer this question from memory."

The student may remember incorrectly.

### With RAG

You give the student the relevant textbook page:

```text
Question
   +
Relevant textbook page
   ↓
Answer
```

That's the basic idea of RAG.

---

# Interview Definition

> RAG allows an LLM application to retrieve relevant information from external or private data sources and provide that information as context to the LLM before generating the response. It helps applications provide more relevant and grounded answers without requiring model retraining.

---

# 3. RAG vs Fine-Tuning

This is a **very common interview question**.

At first, RAG and Fine-Tuning may look similar because both can improve an AI application's behavior.

But they solve different problems.

---

# What is Fine-Tuning?

## Simple Definition

> **Fine-tuning means taking an existing AI model and training it further using a specific dataset so that it behaves better for a particular task or style.**

Think:

```text
Existing Model
      ↓
Additional Training
      ↓
Specialized Model
```

---

# Simple Example

Suppose we have a general LLM.

We want it to consistently generate test cases in our company's specific format.

We could provide many examples:

```text
Input API → Expected Test Case Format

Input API → Expected Test Case Format

Input API → Expected Test Case Format

Input API → Expected Test Case Format
```

The model can be fine-tuned to better follow that pattern.

---

# RAG vs Fine-Tuning

The easiest way to remember:

> **RAG gives the model information.**

> **Fine-tuning changes how the model behaves.**

---

## Example

Suppose your company has:

```text
10,000 API documents
```

and you want the AI to know the latest API rules.

Use:

```text
RAG
```

because the information can change frequently.

---

Suppose you want the AI to always produce test cases in a very specific style:

```json
{
  "testCaseId": "TC001",
  "title": "Valid User",
  "category": "Positive",
  "request": {},
  "expectedStatusCode": 200
}
```

and you have many examples of this format.

Fine-tuning may be useful for teaching the model a particular behavior/style.

---

# Comparison Table

| Feature | RAG | Fine-Tuning |
|---|---|---|
| Main purpose | Provide relevant information | Change model behavior |
| Uses external data at runtime | Yes | Not necessarily |
| Good for private/company knowledge | Yes | Possible, but often not the first choice |
| Good for frequently changing information | Yes | Not ideal |
| Requires retraining | No | Yes |
| Can reduce hallucination | Yes, when retrieval is good | Not primarily designed for this |
| Updates knowledge | Update documents/vector store | Usually requires another training process |
| Implementation complexity | Moderate | Higher |
| Cost | Usually lower | Can be higher |
| Best use case | Knowledge / facts / documents | Behavior / style / specialized task |

---

# Simple Example

## RAG

```text
Question:
What is the validation rule for User API?

        ↓

Search Company Documentation

        ↓

"Age must be greater than 18"

        ↓

LLM

        ↓

Answer
```

---

## Fine-Tuning

```text
Training Examples
       ↓
Fine-Tune Model
       ↓
Specialized Model
       ↓
Generate output in desired style
```

---

# Which One Should We Use for Our POC?

For our **API Test Case Generator**, RAG is a very good choice.

Why?

Because we want to provide:

```text
Swagger/OpenAPI
API Documentation
Validation Rules
Business Rules
Existing Test Cases
API Examples
```

to the AI.

These are **knowledge/data problems**, so RAG fits very well.

---

# Interview Answer

If the interviewer asks:

> "RAG vs Fine-tuning?"

You can answer:

> **RAG is mainly used to provide external or private knowledge to an LLM at runtime, while fine-tuning is used to adapt the model's behavior, style, or performance for a specific task. For frequently changing company data such as API documentation, RAG is generally more suitable because we can update the knowledge base without retraining the model.**

---

# 4. How Does RAG Work?

This is probably the **most important RAG question**.

At a high level:

```text
Documents
    ↓
Chunking
    ↓
Embeddings
    ↓
Vector Database
    ↓
User Question
    ↓
Embedding
    ↓
Similarity Search
    ↓
Relevant Chunks
    ↓
Prompt + Context
    ↓
LLM
    ↓
Answer
```

Let's understand each step.

---

# Step 1: Collect Documents

First, we need data.

For our project, this could be:

```text
Swagger/OpenAPI files
API Documentation
Validation Rules
Business Rules
Existing Test Cases
README files
```

Example:

```text
User API Documentation
----------------------

POST /api/users

Rules:
- Name is required
- Email is required
- Email must be unique
- Age >= 18
```

---

# Step 2: Chunking

Large documents are divided into smaller pieces.

This process is called:

```text
Chunking
```

Example:

```text
Large API Documentation
          ↓
     Split into pieces
          ↓
Chunk 1
Chunk 2
Chunk 3
Chunk 4
```

We will explain chunking in detail in the next section.

---

# Step 3: Generate Embeddings

Each chunk is converted into a numerical representation called an **embedding**.

```text
Chunk
  ↓
Embedding Model
  ↓
Vector
```

Conceptually:

```text
"Email is required"
        ↓
[0.12, -0.45, 0.78, ...]
```

---

# Step 4: Store Embeddings

The embeddings are stored in a **Vector Database**.

Conceptually:

```text
Chunk
+
Embedding
+
Metadata
       ↓
Vector Database
```

Examples of vector databases include:

- Azure AI Search
- Pinecone
- Qdrant
- Weaviate
- Milvus
- PostgreSQL with pgvector
- Elasticsearch

For a .NET/Azure-oriented application, **Azure AI Search** can be a natural option to explore.

---

# Step 5: User Asks a Question

For example:

```text
Generate test cases for mandatory fields in User API.
```

---

# Step 6: Convert User Question into Embedding

The question is converted into an embedding too.

```text
User Question
      ↓
Embedding Model
      ↓
Query Vector
```

---

# Step 7: Similarity Search

The system searches the vector database for information that is semantically similar to the user's question.

For example:

```text
User Question:
"Generate test cases for mandatory fields."

        ↓

Vector Search

        ↓

Retrieved Information:

"Name is required."

"Email is required."
```

---

# Step 8: Build the Prompt

Now we combine:

```text
User Question
      +
Retrieved Context
      +
Instructions
```

Example:

```text
You are an expert API QA engineer.

User Request:
Generate test cases for mandatory fields.

Relevant API Documentation:
- Name is required.
- Email is required.
- Email must be unique.

Generate positive and negative test cases.

Return JSON.
```

---

# Step 9: Send to LLM

Now the LLM receives the prompt.

```text
Prompt
  +
Relevant Context
      ↓
     LLM
      ↓
Generated Test Cases
```

---

# Step 10: Return the Result

The LLM may return:

```json
[
  {
    "testCaseId": "TC001",
    "title": "Create user without name",
    "type": "Negative",
    "expectedStatusCode": 400
  },
  {
    "testCaseId": "TC002",
    "title": "Create user without email",
    "type": "Negative",
    "expectedStatusCode": 400
  }
]
```

---

# Complete RAG Flow

```text
                    DOCUMENTS
                        |
                        v
                    Chunking
                        |
                        v
                Embedding Model
                        |
                        v
                Vector Database
                        |
                        |
                   USER QUERY
                        |
                        v
                Query Embedding
                        |
                        v
                Similarity Search
                        |
                        v
                Relevant Chunks
                        |
                        v
              Prompt + Context
                        |
                        v
                       LLM
                        |
                        v
                Generated Response
```

---

# 5. What is Chunking?

## Simple Definition

> **Chunking means breaking a large document into smaller, meaningful pieces before creating embeddings and storing them for retrieval.**

In simple words:

> **Large document → Small pieces**

---

# Why Do We Need Chunking?

Imagine you have a 100-page API documentation document.

You don't want to send the entire document to the LLM every time the user asks a question.

Instead, we divide it into smaller pieces.

```text
100 Page Document
        ↓
     Chunking
        ↓
Chunk 1
Chunk 2
Chunk 3
Chunk 4
...
Chunk 100
```

Now the system can retrieve only the relevant chunks.

---

# Example

Suppose we have:

```text
User API Documentation

Section 1:
Authentication

Section 2:
POST /api/users

Section 3:
GET /api/users

Section 4:
PUT /api/users/{id}

Section 5:
DELETE /api/users/{id}

Section 6:
Error Handling
```

We can create chunks such as:

```text
Chunk 1:
Authentication information

Chunk 2:
POST /api/users information

Chunk 3:
GET /api/users information

Chunk 4:
PUT /api/users/{id} information

Chunk 5:
DELETE /api/users/{id} information

Chunk 6:
Error handling information
```

---

# Why Small Chunks Help

Suppose the user asks:

```text
Generate test cases for PUT /api/users/{id}.
```

We don't need:

```text
Authentication documentation
POST documentation
GET documentation
DELETE documentation
```

We mainly need:

```text
PUT /api/users/{id}
```

So the vector search can retrieve the relevant chunk.

```text
User Question
      ↓
Vector Search
      ↓
PUT API Chunk
      ↓
LLM
      ↓
Test Cases
```

This saves:

- Tokens
- Processing
- Cost
- Context space

And improves relevance.

---

# Chunking Example

Suppose we have:

```text
Chunk 1:
POST /api/users

Name is required.
Email is required.
Age must be >= 18.
```

```text
Chunk 2:
GET /api/users

Returns all active users.
```

```text
Chunk 3:
PUT /api/users/{id}

Email cannot be changed.
Name can be updated.
```

User asks:

```text
Can email be changed using PUT API?
```

The system retrieves:

```text
Chunk 3
```

because it is the most relevant information.

---

# Chunk Size

Chunking isn't simply:

```text
Every 100 characters
```

or:

```text
Every 500 words
```

The goal is to create **meaningful chunks**.

For example, this is a bad chunk:

```text
Chunk:
POST /api/users

Name is required.

Email is
```

The information is incomplete.

A better chunk would contain the complete logical section:

```text
POST /api/users

Name is required.
Email is required.
Age must be >= 18.

Success:
200 OK

Errors:
400 Bad Request
409 Conflict
```

---

# Overlapping Chunks

Sometimes chunks can overlap.

Example:

```text
Chunk 1:
Name is required.
Email is required.
Age must be >= 18.

Chunk 2:
Age must be >= 18.
Email must be unique.
Duplicate email returns 409.
```

Notice:

```text
Age must be >= 18.
```

appears in both chunks.

This is called **chunk overlap**.

Overlap can help preserve context that might otherwise be lost at chunk boundaries.

---

# Simple Chunking Analogy

Imagine a large book.

Instead of giving the entire book to someone and asking:

> "Find information about Chapter 5."

You create smaller sections:

```text
Chapter 1
Chapter 2
Chapter 3
Chapter 4
Chapter 5
...
```

Then you directly retrieve Chapter 5.

That's the basic idea behind chunking.

---

# Interview Definition

> Chunking is the process of splitting large documents into smaller meaningful pieces before generating embeddings. It helps the retrieval system find relevant information and reduces the amount of unnecessary context sent to the LLM.

---

# 6. Complete RAG Flow

Let's connect everything together.

```text
                  SOURCE DOCUMENTS
                         |
                         v
                    CHUNKING
                         |
                         v
                 EMBEDDING MODEL
                         |
                         v
                  VECTOR DATABASE
                         |
                         |
                  USER QUESTION
                         |
                         v
                 QUERY EMBEDDING
                         |
                         v
                 SIMILARITY SEARCH
                         |
                         v
                  RELEVANT CHUNKS
                         |
                         v
                PROMPT + CONTEXT
                         |
                         v
                        LLM
                         |
                         v
                 GENERATED RESPONSE
```

---

# 7. RAG Example for API Test Case Generator

Let's design our POC conceptually.

## Step 1: Input

Our application receives an API definition:

```json
{
  "method": "POST",
  "endpoint": "/api/users",
  "request": {
    "name": "string",
    "email": "string",
    "age": "integer"
  }
}
```

---

## Step 2: Documentation

We have additional API rules:

```text
User API Rules:

Name:
- Required
- Maximum length: 100

Email:
- Required
- Must be valid
- Must be unique

Age:
- Required
- Must be >= 18

Duplicate email:
- Returns 409 Conflict
```

---

## Step 3: Chunking

We divide the documentation into meaningful chunks.

```text
Chunk 1:
User API basic information.

Chunk 2:
Name validation.

Chunk 3:
Email validation.

Chunk 4:
Age validation.

Chunk 5:
Error handling.
```

---

## Step 4: Embeddings

Each chunk is converted into a vector.

```text
Chunk 1 → Vector 1
Chunk 2 → Vector 2
Chunk 3 → Vector 3
Chunk 4 → Vector 4
Chunk 5 → Vector 5
```

---

## Step 5: Store in Vector Database

```text
Vector Database

Vector 1 → User API
Vector 2 → Name validation
Vector 3 → Email validation
Vector 4 → Age validation
Vector 5 → Error handling
```

---

## Step 6: User Request

User asks:

```text
Generate negative test cases for email validation.
```

---

## Step 7: Query Embedding

The question is converted into a vector.

```text
"Generate negative test cases for email validation."
                    |
                    v
              Query Vector
```

---

## Step 8: Similarity Search

The system searches the vector database.

It may retrieve:

```text
Chunk 3:

Email:
- Required
- Must be valid
- Must be unique
```

and:

```text
Chunk 5:

Duplicate email:
- Returns 409 Conflict
```

---

## Step 9: Prompt

The application creates:

```text
You are an expert API QA engineer.

Generate negative test cases for email validation.

Relevant API Context:

Email:
- Required
- Must be valid
- Must be unique

Duplicate email:
- Returns 409 Conflict

Return JSON.
```

---

## Step 10: LLM

The prompt is sent to the LLM.

```text
Prompt
  +
Retrieved Context
       |
       v
      LLM
       |
       v
Test Cases
```

---

## Step 11: Output

The LLM could return:

```json
[
  {
    "testCaseId": "TC001",
    "title": "Email is missing",
    "type": "Negative",
    "expectedStatusCode": 400
  },
  {
    "testCaseId": "TC002",
    "title": "Invalid email format",
    "type": "Negative",
    "expectedStatusCode": 400
  },
  {
    "testCaseId": "TC003",
    "title": "Duplicate email",
    "type": "Negative",
    "expectedStatusCode": 409
  }
]
```

---

# 8. Interview Questions and Answers

## Q1. What is RAG?

### Answer

> RAG stands for Retrieval-Augmented Generation. It is a technique where an application retrieves relevant information from external or private data sources and provides that information as context to an LLM before generating the response.

---

## Q2. Why do we need RAG?

### Answer

> We use RAG when the LLM needs access to private, domain-specific, or frequently changing information. It allows us to provide relevant external knowledge at runtime without retraining the model and can help reduce hallucinations.

---

## Q3. RAG vs Fine-Tuning?

### Answer

> RAG is mainly used to provide external knowledge or context to an LLM, while fine-tuning is used to adapt the model's behavior, style, or performance for a specific task. For frequently changing company information such as API documentation, RAG is generally more suitable.

---

## Q4. How does RAG work?

### Answer

> In a typical RAG system, documents are first split into chunks, embeddings are generated for those chunks and stored in a vector database. When a user asks a question, the question is converted into an embedding and a similarity search retrieves relevant chunks. Those chunks are then added to the prompt and sent to the LLM to generate the final response.

---

## Q5. What is Chunking?

### Answer

> Chunking is the process of splitting large documents into smaller meaningful pieces before generating embeddings. It allows the retrieval system to find relevant information more accurately and prevents unnecessary information from being sent to the LLM.

---

# 9. Quick Revision

## RAG

```text
Retrieve relevant information
+
Generate answer
```

---

## Why RAG?

```text
Private Data
+
Latest Information
+
Domain Knowledge
+
Better Grounding
```

---

## RAG vs Fine-Tuning

```text
RAG
↓
Give the model information

Fine-Tuning
↓
Change/adapt model behavior
```

---

## How RAG Works

```text
Documents
   ↓
Chunking
   ↓
Embeddings
   ↓
Vector Database
   ↓
User Question
   ↓
Query Embedding
   ↓
Similarity Search
   ↓
Relevant Chunks
   ↓
Prompt + Context
   ↓
LLM
   ↓
Answer
```

---

## Chunking

```text
Large Document
      ↓
Small Meaningful Pieces
```

---

# Final Memory Trick

Remember RAG using these 5 words:

```text
RAG

1. Split
   ↓
2. Embed
   ↓
3. Store
   ↓
4. Retrieve
   ↓
5. Generate
```

Or:

```text
Documents
    ↓
Chunk
    ↓
Embedding
    ↓
Vector DB
    ↓
Retrieve
    ↓
LLM
    ↓
Answer
```

---

# RAG in One Sentence

> **RAG is a technique where we retrieve relevant information from our own data and give it to an LLM as context so that the LLM can generate a more relevant and grounded response.**

---

# Our API Test Case Generator

The complete idea is:

```text
              API DOCUMENTATION
                      |
                      v
                  CHUNKING
                      |
                      v
                 EMBEDDINGS
                      |
                      v
                VECTOR DB
                      |
                      |
                   USER
                      |
                      v
            "Generate test cases
             for User API"
                      |
                      v
             QUERY EMBEDDING
                      |
                      v
            SIMILARITY SEARCH
                      |
                      v
             RELEVANT API RULES
                      |
                      v
              PROMPT + CONTEXT
                      |
                      v
                     LLM
                      |
                      v
             GENERATED TEST CASES
                      |
                      v
                    USER
```

This architecture will be the foundation of our **GenAI-powered .NET Core API Test Case Generator POC**.

---

# Next Concepts to Learn

After understanding RAG, the next concepts should be:

```text
1. Vector Database
        ↓
2. Similarity Search
        ↓
3. Cosine Similarity
        ↓
4. Embedding Models
        ↓
5. Chunking Strategies
        ↓
6. RAG Pipeline
        ↓
7. RAG Evaluation
        ↓
8. Prompt + Retrieved Context
        ↓
9. Structured JSON Output
        ↓
10. Build RAG-based Test Case Generator
```

Once these are clear, you will be able to explain the complete RAG architecture confidently in a GenAI interview.

