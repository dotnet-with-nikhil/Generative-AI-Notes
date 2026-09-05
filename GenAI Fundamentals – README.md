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